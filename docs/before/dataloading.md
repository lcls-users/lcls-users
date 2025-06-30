# Data Loading

## Basics of psana

Psana design borrows ideas from multitude of other framworks such as pyana, myana, BaBar framework, etc. Its main principles are summarized here:

- support processing of both XTC and HDF5 data format
- user code should be independent of specific data format
- should be easy to use and extend for end users
- support re-use of the existing analysis code
- common simple configuration of user analysis code 


This manual is accompanied by the [Reference Manual](https://confluence.slac.stanford.edu/display/PSDMInternal/psana+-+Reference+Manual) which describes interfaces of the classes available in Psana. 
<p align="justify">Only the basics will be covered here. Two approaches will be tackled : the first one allows for a more complex and personalized use of Psana, the second one is more straight-forward and allows only the loading of images.
</p>

### Option 1 : Creating an adaptable Psana Interface 
<p align="justify">
Psana is at the heart of loading and processing the data. If various information about the physics of the experiments want to be gathered, it makes things easier if the class is modular. Start by importing Psana and creating a PsanaInterface class. The arguments will be the experiment name, run number, detector name. Optionally can set a directory to alternative calibration files.
</p>

```python
import numpy as np
import logging
import psana

logger = logging.getLogger(__name__)

class PsanaInterface:

    def __init__(self,exp,run,det_type,calibdir = None):
        self.exp = exp
        self.run = run
        self.det_type = det_type
        self.set_up(det_type,calibdir)
    
    def set_up(self,det_type,calibdir=None):
        """
        Instantiate DataSource and Detector objects; use the run 
        functionality to retrieve all psana.EventTimes.
        
        Parameters
        ----------
        det_type : str
            detector type, e.g. epix10k2M or jungfrau4M
        calibdir: str
            directory to alternative calibration files
        """

        ds_args=f'exp={self.exp}:run={self.run}:idx'
        self.ds = psana.DataSource(ds_args)   
        self.det = psana.Detector(det_type, self.ds.env())
        self.runner = next(self.ds.runs())
        self.times = self.runner.times()
        self.max_events = len(self.times)
        if calibdir is not None:
            setOption('psana.calib_dir',calibdir)
        self._calib_data_available()
    
    def _calib_data_available(self):
        """
        Check whether calibration data is available.
        """
        self.calibrate = True
        evt = self.runner.event(self.times[0])
        if (self.det.pedestals(evt) is None) or (self.det.gain(evt) is None):
            logger.warning("Warning: calibration data unavailable, returning uncalibrated data")
            self.calibrate = False
```
<p align="justify">
This allows to properly set up Psana. From here, one can implement other functions, such as the loading of images:
</p>

```python
def get_images(self, num_images, assemble=True):
    """
    Retrieve a fixed number of images from the run. If the pedestal or gain 
    information is unavailable and unassembled images are requested, return
    uncalibrated images. 

    Parameters
    ---------
    num_images : int
        number of images to retrieve (per rank)
    assemble : bool, default=True
        whether to assemble panels into image

    Returns
    -------
    images : numpy.ndarray, shape ((num_images,) + det_shape)
        images retrieved sequentially from run, optionally assembled
    """
    # set up storage array
    if 'opal' not in self.det_type.lower():
        if assemble:
            images = np.zeros((num_images, 
                                self.det.image_xaxis(self.run).shape[0], 
                                self.det.image_yaxis(self.run).shape[0]))
        else:
            images = np.zeros((num_images,) + self.det.shape())
    else:
        images = np.zeros((num_images, 230, 1024))
        assemble = False

    # retrieve next batch of images
    counter_batch = 0
    while counter_batch < num_images:

        if self.counter >= self.max_events:
            images = images[:counter_batch]
            print("No more events to retrieve")
            break
        else:
            evt = self.runner.event(self.times[self.counter])
            if assemble and self.det_type.lower()!='rayonix':
                if not self.calibrate:
                    raise IOError("Error: calibration data not found for this run.")
                else:
                    img = self.det.image(evt=evt)
            else:
                if self.calibrate:
                    img = self.det.calib(evt=evt)
                else:
                    img = self.det.raw(evt=evt)
                    if self.det_type == 'epix10k2M':
                        img = img & 0x3fff # exclude first two bits
            if img is not None:
                images[counter_batch] = img
                counter_batch += 1
                    
            if self.track_timestamps:
                self.get_timestamp(evt.get(EventId))
                
            self.counter += 1
            
    return images
```

### Option 2 : Creating a straight-forward loading interface
<p align="justify">
If one only needs the images, an easier and more straight-forward interface can be created.
</p>

```python
class PsanaImg:
    """
    It serves as an image accessing layer based on the data management system
    psana in LCLS.  
    """

    def __init__(self, exp, run, mode, detector_name):
        # Set up data source
        self.datasource_id = f"exp={exp}:run={run}:{mode}"
        self.datasource    = psana.DataSource( self.datasource_id )
        self.run_current   = next(self.datasource.runs())
        self.timestamps    = self.run_current.times()
        # Set up detector
        self.detector = psana.Detector(detector_name)
        self.detector_name = detector_name

        # Set image reading mode
        self.read = { "raw"   : self.detector.raw,
                      "calib" : self.detector.calib,
                      "image" : self.detector.image,
                      "mask"  : self.detector.mask, }

    def __len__(self):
        return len(self.timestamps)


    def get(self, event_num, id_panel = None, mode = "calib"): 

        if event_num >= self.__len__():
            raise ValueError(f"Event number {event_num} is out of range!!!")
        
        # Fetch the timestamp according to event number...
        timestamp = self.timestamps[event_num]

        # Access each event based on timestamp...
        event = self.run_current.event(timestamp)

        # Only two modes are supported...
        assert mode in ("raw", "calib", "image"), \
            f"Mode {mode} is not allowed!!!  Only 'raw', 'calib' and 'image' are supported."

        # Fetch image data based on timestamp from detector...
        data = self.read[mode](event)
        img  = data[int(id_panel)] if id_panel is not None else data

        return img


    def assemble(self, multipanel = None, mode = "image", fake_event_num = 0):
        # Set up a fake event_num...
        event_num = fake_event_num

        # Fetch the timestamp according to event number...
        timestamp = self.timestamps[int(event_num)]

        # Access each event based on timestamp...
        event = self.run_current.event(timestamp)

        # Fetch image data based on timestamp from detector...
        img = self.read[mode](event, multipanel)

        return img
```

One can then load an image with the 'get' attribute, and iterate if several images are needed.

## Server/Client Approach

### Motivation

<p align="justify">
A limiting issue on S3DF’s workflow is the conda environment. The dependencies are complex and one cannot
install any module or libraries on environments that contain psana. Similarly, one cannot update libraries to
their latest versions. Thus, it is a limiting factor when trying new methods. For instance, Dask Cuda was not
preinstalled on the available environments. A new environment managing method has thus been developed to solve this issue.
</p>

### Set up a server and a client
<p align="justify">
The idea is that a server using a Psana-compatible environment will load the images (using the PsanaImg class detailed previously for instance) at the request of a client running on another environment (any environment). The client will then perform the task given, but will have access to any module the user had installed. The following method details the process :
</p>

1. Select the environment used to gather the images (it has to contain psana, thus it has to be a S3DF-
maintained environment. Usually, the format is "ana-4.0.XX-py3")

2. Create your working environment and install all the dependencies you need to run your code. You can
modify it later.

3. In the [iclient.py](#client) file, import as run_client_task the main function you want your client to run.

4. Create a task that uses [JobScheduler](#jobscheduler). The command should be :
    - Call [iserver.py](#server)
    - Wait for 5sec (server initiating)
    - Deactivate the psana environment
    - Activate your own personalized environment
    - Call iclient.py with the correct arguments (number of images needed...)

5. Call the task : the server will gather the images requested by the client. Then the server will shutdown
and the client will run the imported run_client_task.

#### JobScheduler

The following JobScheduler is adapted to S3DF and allows to run tasks with personalized arguments and dependencies.

```python
import shutil
import logging
import os

""" Helper methods for job scheduling. """

logger = logging.getLogger(__name__)

class JobScheduler:

    def __init__(self, jobfile, logdir='./', jobname='btx',
                 account='lcls', queue='ffbh3q', ncores=1, time='01:00:00',
                 reservation=""):
        self.manager = 'SLURM'
        self.jobfile = jobfile
        self.logdir = logdir
        self.jobname = jobname
        self.account = account
        self.queue = queue
        self.ncores = ncores
        self.time = time
        self.reservation = reservation
        self._data_systems_management()

    def _data_systems_management(self):
        """ List the Data Systems group folder paths. """
        try:
            computing_facility = os.environ['FACILITY']
        except KeyError:
            print('FACILITY environment variable not defined.')

        if(computing_facility == 'SLAC'):
            self.ana_conda_dir = '/cds/sw/ds/ana/'
            self.ana_tools_dir = '/cds/sw/package/'
        elif(computing_facility == 'SRCF_FFB'):
            self.ana_conda_dir = '/cds/sw/ds/ana/'
            self.ana_tools_dir = '/cds/sw/package/'
        elif(computing_facility == 'S3DF'):
            self.ana_conda_dir = '/sdf/group/lcls/ds/ana/sw/'
            self.ana_tools_dir = '/sdf/group/lcls/ds/tools/'
        else:
            raise NotImplementedError('Unknown computing facility.')

        self.ana_conda_manage = f'{self.ana_conda_dir}conda1/manage/bin/'
        self.ana_conda_bin = f'{self.ana_conda_dir}conda1/inst/envs/ana-4.0.60-py3/bin/'

    def _find_python_path(self):
        """ Determine the relevant python path. """
        pythonpath=None
        possible_paths = [f"{self.ana_conda_bin}python"]
    
        try:
            pythonpath = os.environ['WHICHPYTHON']
        except KeyError:
            pass
    
        for ppath in possible_paths:
            if os.path.exists(ppath):
                pythonpath = ppath
                if self.ncores > 1:
                    pythonpath = f"{os.path.split(ppath)[0]}/mpirun -n {self.ncores} {ppath}"

        return pythonpath            

    def write_header(self):
        """ Write resource specification to submission script. """
        if(self.manager == 'SLURM'):
            template = ("#!/bin/bash\n"
                        "#SBATCH -p {queue}\n"
                        "#SBATCH --job-name={jobname}\n"
                        "#SBATCH --output={output}\n"
                        "#SBATCH --error={error}\n"
                        "#SBATCH --ntasks={ncores}\n"
                        "#SBATCH --time={time}\n"
                        "#SBATCH --mem=0\n"
                        "#SBATCH --exclusive\n\n")
        else:
            raise NotImplementedError('JobScheduler not implemented.')

        context = {
            "queue": self.queue,
            "jobname": self.jobname,
            "output": os.path.join(self.logdir, f"{self.jobname}.out"),
            "error": os.path.join(self.logdir, f"{self.jobname}.err"),
            "ncores": self.ncores,
            "time": self.time
        }

        with open(self.jobfile, 'w') as jfile:
            jfile.write(template.format(**context))

        facility = os.environ['FACILITY']
        if self.account is not None and facility == 'S3DF':
            with open(self.jobfile, 'a') as jfile:
                jfile.write(f"#SBATCH -A {self.account}\n\n")

        if self.reservation and facility == "S3DF":
            with open(self.jobfile, 'a') as jfile:
                jfile.write(f"#SBATCH --reservation {self.reservation}\n\n")

    def _write_dependencies(self, dependencies):
        """ Source dependencies."""
        dep_paths = ""
        if "psana" in dependencies:
            dep_paths += f"source {self.ana_conda_manage}psconda.sh \n"
        if "crystfel" in dependencies:
            if (os.environ['FACILITY'] == 'S3DF'):
                dep_paths += f"export PATH={self.ana_tools_dir}crystfel/0.10.2/bin:$PATH\n"
            else:
                dep_paths += f"export PATH={self.ana_tools_dir}crystfel/crystfel-dev/bin:$PATH\n"
        if "mosflm" in dependencies:
            if(os.environ['FACILITY'] == 'S3DF'):
                dep_paths += f"export PATH={self.ana_tools_dir}:$PATH\n"
            else:
                dep_paths += f"export PATH={self.ana_tools_dir}autosfx:$PATH\n"
        if "ccp4" in dependencies:
            if (os.environ['FACILITY'] == 'S3DF'):
                dep_paths += f"source {self.ana_tools_dir}ccp4-8.0/bin/ccp4.setup-sh\n"
            else:
                dep_paths += f"source {self.ana_tools_dir}ccp4/ccp4-8.0/bin/ccp4.setup-sh\n"
        if "phenix" in dependencies:
            dep_paths += f"source {self.ana_tools_dir}phenix-1.13-2998/phenix_env.sh\n"
        if "xds" in dependencies:
            dep_paths += f"export PATH={self.ana_tools_dir}XDS-INTEL64_Linux_x86_64:$PATH\n"
        if "xgandalf" in dependencies:
            dep_paths += "export PATH=/reg/g/cfel/crystfel/indexers/xgandalf/include/:$PATH\n"
            dep_paths += "export PATH=/reg/g/cfel/crystfel/indexers/xgandalf/include/eigen3/Eigen/:$PATH"
        dep_paths += "\n"
        
        with open(self.jobfile, 'a') as jfile:
            jfile.write(dep_paths)
            if 'SIT_PSDM_DATA' in os.environ:
                jfile.write(f"export SIT_PSDM_DATA={os.environ['SIT_PSDM_DATA']}\n")

    def write_main(self, application, dependencies=[],find_python_path=True):
        """ Write application and source requested dependencies. """
        if dependencies:
            self._write_dependencies(dependencies)

        if find_python_path:
            pythonpath = self._find_python_path()
        else:
            pythonpath = "python"
            
        with open(self.jobfile, 'a') as jfile:
            jfile.write(application.replace("python", pythonpath))

    def submit(self):
        """ Submit to queue. """
        os.system(f"sbatch -W {self.jobfile}")
        logger.info(f"sbatch -W {self.jobfile}")

    def clean_up(self):
        """ Add a line to delete submission file."""
        with open(self.jobfile, 'a') as jfile:
            jfile.write(f"if [ -f {self.jobfile} ]; then rm -f {self.jobfile}; fi")
```

Once the JobScheduler class has been created, the user needs to create a command that will be submitted. In the server client case, an example of command would be :

```python
command = "which python;"
command += f"python {server_path} & echo 'Server is running'"
command += "; sleep 10"
command += ";conda deactivate; echo 'Server environment deactivated'"
command += f"; conda activate {path/to/your/environment}; which python; echo 'Client environment activated'"
command += f"; python {client_path} --exp {exp} --run {run}" #Add all arguments needed
```

Once this is done, the user just needs to call the JobScheduler:
```python
js = JobScheduler('path/to/jobfile',queue = 'ampere', ncores=1, jobname='jobname',logdir='/path/to/log/dir',account='lcls')
js.write_header()
js.write_main(f"{command}\n", dependencies=['psana'],find_python_path=False)
js.clean_up()
js.submit()
```
#### Server

The server is the entity responsible for the gathering of the images and the sending to the client. The iserver.py file should be :

```python
import signal
import json
import socket
from multiprocessing import shared_memory, Process
import numpy as np
from ipsana import PsanaImg

# Initialize buffer for each process
psana_img_buffer = {}

def get_psana_img(exp, run, access_mode, detector_name):
    """
    Fetches a PsanaImg object for the given parameters, 
    caching the object to avoid redundant initializations.
    """
    key = (exp, run)
    if key not in psana_img_buffer:
        psana_img_buffer[key] = PsanaImg(exp, run, access_mode, detector_name)
    return psana_img_buffer[key]

def worker_process(server_socket):
    # Ignore CTRL+C in the worker process
    signal.signal(signal.SIGINT, signal.SIG_IGN)

    while True:
        try:
            # Accept a new connection
            connection, client_address = server_socket.accept()
            # Receive request data
            request_data = connection.recv(4096).decode('utf-8')

            if request_data == "DONE":
                print("Received shutdown signal. Shutting down server.")
                connection.close()
                break

            request_data = json.loads(request_data)
            exp           = request_data.get('exp')
            run           = request_data.get('run')
            access_mode   = request_data.get('access_mode')
            detector_name = request_data.get('detector_name')
            event         = request_data.get('event')
            mode          = request_data.get('mode')

            # Fetch psana image data
            psana_img = get_psana_img(exp, run, access_mode, detector_name)
            data = psana_img.get(event, None, mode)

            # Keep numpy array in a shared memory
            shm = shared_memory.SharedMemory(create=True, size=data.nbytes)
            shared_array = np.ndarray(data.shape, dtype=data.dtype, buffer=shm.buf)
            shared_array[:] = data

            response_data = json.dumps({'name': shm.name,
                'shape': data.shape,
                'dtype': str(data.dtype)})
            # Send response with shared memory details
            connection.sendall(response_data.encode('utf-8'))

            # Wait for the client's acknowledgment
            ack = connection.recv(1024).decode('utf-8')
            if ack == "ACK":
                unlink_shared_memory(shm.name)
            else:
                print("Did not receive proper acknowledgment from client.")

        except Exception as e:
            print(f"Unexpected error: {e}")
            continue

def unlink_shared_memory(shm_name):
    try:
        shm = shared_memory.SharedMemory(name=shm_name)
        shm.close()
        shm.unlink()
    except FileNotFoundError:
        pass

def start_server(address, num_workers):
    # Init TCP socket, set reuse, bind, and listen for connections
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server_socket.bind(address)
    server_socket.listen()

    # Create and start worker processes
    processes = []
    for _ in range(num_workers):
        p = Process(target=worker_process, args=(server_socket,))
        p.start()
        processes.append(p)

    print(f"Started {num_workers} worker processes.")
    return processes, server_socket

if __name__ == "__main__":
    server_address = ('localhost', 5000)
    num_workers = 5
    print("Starting server ...")
    processes, server_socket = start_server(server_address, num_workers)
    print("Server started")

    try:
        # Wait to complete, join is wait
        for p in processes:
            p.join()
    except KeyboardInterrupt:
        print("Shutdown signal received")
        for p in processes:
            # Trigger connection to unblock accept() in workers
            with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as trigger_socket:
                trigger_socket.connect(server_address)
                trigger_socket.sendall("DONE".encode('utf-8'))
        # Wait to complete, join is wait
        for p in processes:
            p.join()
        server_socket.close()
        print("Server shutdown gracefully.")
```

Make sure that the [PsanaImg](#option-2--creating-a-straight-forward-loading-interface) class is implemented.

#### Client

The client is the entity responsible for the request made to the server. It then is the one sending the gathered images to the processing task. Make sure to import your task properly. The iclient.py file should look like this :

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

import json
import socket
import time
import requests
import io
import numpy as np
import argparse
import time
import os
import sys

from multiprocessing import shared_memory

from torch.utils.data import Dataset
from torch.utils.data import DataLoader

from btx.processing.pipca_nopsana import main as run_client_task # This is the main function that runs the iPCA algorithm
from btx.processing.pipca_nopsana import remove_file_with_timeout

class IPCRemotePsanaDataset(Dataset):
    def __init__(self, server_address, requests_list):
        """
        server_address: The address of the server. For UNIX sockets, this is the path to the socket.
                        For TCP sockets, this could be a tuple of (host, port).
        requests_list: A list of tuples. Each tuple should contain:
                       (exp, run, access_mode, detector_name, event)
        """
        self.server_address = server_address
        self.requests_list = requests_list

    def __len__(self):
        return len(self.requests_list)

    def __getitem__(self, idx):
        request = self.requests_list[idx]
        return self.fetch_event(*request)

    def fetch_event(self, exp, run, access_mode, detector_name, event):
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as sock:
            sock.connect(self.server_address)
            # Send request
            request_data = json.dumps({
                'exp'          : exp,
                'run'          : run,
                'access_mode'  : access_mode,
                'detector_name': detector_name,
                'event'        : event,
                'mode'         : 'calib',
            })
            sock.sendall(request_data.encode('utf-8'))

            # Receive and process response
            response_data = sock.recv(4096).decode('utf-8')
            response_json = json.loads(response_data)

            # Use the JSON data to access the shared memory
            shm_name = response_json['name']
            shape    = response_json['shape']
            dtype    = np.dtype(response_json['dtype'])

            # Initialize shared memory outside of try block to ensure 
            # it's in scope for finally block
            shm = None
            try:
                # Access the shared memory
                shm = shared_memory.SharedMemory(name=shm_name)
                data_array = np.ndarray(shape, dtype=dtype, buffer=shm.buf)

                # Convert to numpy array (this creates a copy of the data)
                result = np.array(data_array)
            finally:
                # Ensure shared memory is closed even if an exception occurs
                if shm:
                    shm.close()
                    shm.unlink()

            # Send acknowledgment after successfully accessing shared memory
            sock.sendall("ACK".encode('utf-8'))

            return result


def parse_input():
    """
    Parse command line input.
    """

    parser = argparse.ArgumentParser()
    parser.add_argument("-e", "--exp", help="Experiment name.", required=True, type=str)
    parser.add_argument("-r", "--run", help="Run number.", required=True, type=int)
    #Don't forget to add all other needed arguments (det_type, start_offset, num_images...)
    
    return parser.parse_args()


if __name__ == "__main__":

    # Python executable location
    print("\nPython executable location from client:")
    print(sys.executable)
    

    start_time = time.time()
    params = parse_input()
    exp = params.exp
    run = params.run
    det_type = params.det_type
    start_offset = params.start_offset
    num_components = params.num_components
    batch_size = params.batch_size
    path = params.path
    tag = params.tag
    overwrite = True
    filename_with_tag = f"{path}ipca_model_nopsana_{tag}.h5"
    remove_file_with_timeout(filename_with_tag, overwrite, timeout=10)
    all_data = []

    if start_offset is None:
        start_offset = 0
    num_images = params.num_images
    loading_batch_size = params.loading_batch_size

    for event in range(start_offset, start_offset + num_images, loading_batch_size):
        requests_list = [ (exp, run, 'idx', det_type, img) for img in range(event,event+loading_batch_size) ]

        server_address = ('localhost', 5000)
        dataset = IPCRemotePsanaDataset(server_address = server_address, requests_list = requests_list)
        dataloader = DataLoader(dataset, batch_size=20, num_workers=10, prefetch_factor = None)
        dataloader_iter = iter(dataloader)

        for batch in dataloader_iter:
            all_data.append(batch)
        print(f"Loaded {event+loading_batch_size} images.")
    
    all_data = np.concatenate(all_data, axis=0) 

    #Then, run your client task ! 
```

Don't forget to add all the needed arguments for the task, and to properly call the task.

