# OLCF Access Guide

## Step 0 - Two account types at ORNL

ORNL can create an organizational account for external
collaborators (called XCAMS account).  Logins
with XCAMS use passwords, not RSA tokens.
It allows you to access services such as:

* code.ornl.gov
* latex.ornl.gov

Perhaps confusingly, it also allows you access
to "open" NCCS systems like Defiant, Olivine, OpenDTN,
and Odo.

This is different from your NCCS account.
Your NCCS account usually has a different username,
and uses an RSA SecurID token or a mobile phone app,
rather than password based.  The NCCS account allows you access to "moderate"
NCCS systems, like Frontier, Andes, DTN and Marble.

The reason for the difference is that OLCF maintains
"open" systems completely isolated from "moderate"
systems - separate networks, filesystems, login nodes,
etc.  All this makes NCCS much more secure.

Getting an XCAMS account is easy, but still won't
allow you access to any OLCF systems until you complete
the process in Step 1.

## Step 1 - Get a computer account

Only user groups (also known as project-IDs) can gain
access to OLCF systems.  Thus, to access OLCF machines,
you must either:

1. Apply for membership (and access) to a project using the [my.olcf.ornl.gov][1]
   user portal.
   - Fill out the form to request access to the specific
     project(s) you are collaborating on.
   - Additional help and information is available in the [OLCF User Guide][2]

2. Apply to create a new project allocation:
   * [DD Project Application][3]
     - small (~20,000 node-hours max)
     - focused on demonstrating emerging science and software at 1000+ node scale
     - rolling reviews
   * [INCITE][4]
     - large (~100,000 - 2M node-hours)
     - high impact science open to the world
     - deadlines around June of each year.
   * [ALCC][5]
     - large (~100,000 - 2M node-hours)
     - focused on research in DOE science priority areas
     - deadlines around Feb of each year.

In practice, you still need to do step (1) after receiving
an allocation.  After completing the new user form,
you will be given instructions on how to proceed.

The following general steps happen:

1. You agree to the [system use policies][6].
   * Link above includes the necessary acknowledgement statement for publications related to work done on OLCF resources.

2. The project PI approves your request via the my.olcf portal.

3. *If Accessing Moderate Systems*: You receive an RSA token via mail,
   and follow directions from OLCF staff on activating your token.

4. You should now be able to login to OLCF systems
   (e.g. `ssh <XCAMS>@flux.op.ccs.ornl.gov` or `ssh <NCCSID>@frontier.olcf.ornl.gov`)

Further information and more detailed instructions are
available from the [OLCF Accounts and Projects Documentation][10].


## Step 2 - Understand OLCF Systems

Now that you have access to OLCF, here
are a few things to know:

OLCF system [Best Practices][7] explain that
data in `$HOME` is served by NFS, continuously
snap-shotted, limited in size, and read-only
during jobs.

The major directories where work should occur
are on the high-performance filesystem.
Different systems have different filesystems mounted:

* `/lustre/wolf2/<project-id>` (ascent,odo)
* `/lustre/polis/<project-id>` (defiant)
* `/gpfs/alpine2/<project-id>` (summit)
* `/lustre/orion/<project-id>` (frontier, andes)

This is an NFS directory (`/ccs/proj/<project-id>`)
where software should be installed, and where results
should be copied after jobs.  This reduces clutter on the
high-performance filesystems.  Note that the high-performance
filesystems are automatically purged (contents deleted)
after a variable number of days (usually 90, but see
the [OLCF Policy Guide][6] for exact details).

There are various sub-directories under the
high-performance filesystem (e.g. `/lustre/polis/<project-id>`)
like `world-shared`, `proj-shared`, and `scratch`.
These only differ in directory-level UNIX permissions.


!!! danger "System Upgrades"
    OLCF is constantly improving and upgrading its systems
    in 2-3 year cycles.  You should pay careful attention
    to news in the "OLCF Weekly Update" (sent from olcf@olcf-communications.org)
    to be sure you are aware of these plans, move data,
    and update your workflows accordingly!

## Step 3 - Access your data

There are special-purpose data transfer nodes for each
enclave.  Open has `opendtn.ccs.ornl.gov`, which mounts `/gpfs/wolf2`.
Moderate has `dtn.olcf.ornl.gov`, which mounts `/lustre/orion`
and `/gpfs/alpine2`.  These nodes have fast connections
to the filesystem and external networks -- hence are
used both for globus-initiated transfers, and for direct
scp/rsync copies.

For more information, see the [Data Transfer][8]
portion of the OLCF user documentation.

## Step 4 - Installing Software and Running Jobs

OLCF maintains extensive [User Documentation][9]
on all the major HPC systems it operates.  The documentation
includes not only machine specs and HOWTO-s on using
the batch job queues, but also practical guides on
specific software and services like conda and jupyter
notebooks.


[1]: https://my.olcf.ornl.gov
[2]: https://docs.olcf.ornl.gov/services_and_applications/myolcf/index.html
[3]: https://my.olcf.ornl.gov/project-application-new
[4]: https://doeleadershipcomputing.org/call-for-proposals/
[5]: https://science.osti.gov/ascr/Facilities/Accessing-ASCR-Facilities/ALCC/Proposal-Information
[6]: https://docs.olcf.ornl.gov/accounts/olcf_policy_guide.html
[7]: https://olcf.ornl.gov/wp-content/uploads/2022/02/Best-Practices-2022.pdf
[8]: https://docs.olcf.ornl.gov/data/index.html
[9]: https://docs.olcf.ornl.gov/
[10]: https://docs.olcf.ornl.gov/accounts/accounts_and_projects.html#applying-for-a-user-account
