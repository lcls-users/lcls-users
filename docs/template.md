---
title : Template
summary: Include a short feature list with example markdown.
authors:
    - GFD
date: 2023-02-10
---
# Features/Extensions template

## Meta-data
YAML metadata will override certain features of the generated page. E.g.

* title - Replace the title next to the top home link. Otherwise the title will be the first header

Other fields can be added which could be parsed with python later, if certain automated analysis/content creation is needed. This goes at the top of the markdown page and is fenced by three hyphens.

```
---
title : Template
summary: Include a short feature list with example markdown.
authors:
    - GFD
date: 2023-02-10
---
```

## Admonitions

Admonitions are used to highlight information. Two styles, danger and note, although I think you can use any word for the note style (other than danger).

```
!!! danger "Title"
	content
	
!!! note "Title"
	content
```

!!! danger "Be careful what you wish for!"
	content

!!! note "It is worth noting that..."
	content


## Anchor links

This [link](template.md#test) jumps to a header anchor link, automatically generated. Using the attr\_list extension, like this, `{: #test}` you can assign id's to make it easier to link long headers. This extension provides additional functionality. [Documentation](https://python-markdown.github.io/extensions/attr_list/)


Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Mauris rhoncus aenean vel elit scelerisque mauris pellentesque pulvinar pellentesque. Tristique senectus et netus et malesuada fames. Pharetra et ultrices neque ornare aenean euismod. Libero justo laoreet sit amet. Urna nec tincidunt praesent semper feugiat nibh sed. Amet justo donec enim diam. Scelerisque eleifend donec pretium vulputate sapien nec sagittis aliquam malesuada. Porttitor leo a diam sollicitudin tempor. Eget est lorem ipsum dolor sit amet consectetur adipiscing.

Sit amet consectetur adipiscing elit duis tristique sollicitudin nibh. Ultrices sagittis orci a scelerisque purus. Ac placerat vestibulum lectus mauris ultrices eros in cursus. Ultrices tincidunt arcu non sodales neque sodales ut etiam. Nunc non blandit massa enim nec dui nunc. Eleifend quam adipiscing vitae proin sagittis nisl rhoncus mattis rhoncus. Diam vulputate ut pharetra sit amet aliquam. Quis imperdiet massa tincidunt nunc pulvinar sapien. Tempus urna et pharetra pharetra massa. Eu nisl nunc mi ipsum.

Et tortor at risus viverra. Sed egestas egestas fringilla phasellus faucibus scelerisque eleifend. Urna id volutpat lacus laoreet non curabitur gravida. Purus sit amet volutpat consequat mauris nunc. A scelerisque purus semper eget. Convallis posuere morbi leo urna molestie. Nulla facilisi nullam vehicula ipsum a arcu. Velit scelerisque in dictum non consectetur a erat nam at. Viverra adipiscing at in tellus. Non arcu risus quis varius. Dapibus ultrices in iaculis nunc sed. Aenean pharetra magna ac placerat vestibulum. Interdum varius sit amet mattis vulputate enim nulla. Ante in nibh mauris cursus mattis molestie a. Suscipit tellus mauris a diam maecenas sed. Ultricies integer quis auctor elit sed vulputate mi sit amet. Nec ultrices dui sapien eget mi. Maecenas volutpat blandit aliquam etiam erat velit scelerisque.

Venenatis a condimentum vitae sapien pellentesque habitant morbi. Vel risus commodo viverra maecenas accumsan. Gravida dictum fusce ut placerat orci. Pellentesque adipiscing commodo elit at imperdiet dui accumsan. Diam quam nulla porttitor massa id. Vel eros donec ac odio tempor orci dapibus ultrices. Id neque aliquam vestibulum morbi blandit cursus risus at. Amet est placerat in egestas erat imperdiet sed. Massa sed elementum tempus egestas sed. Sagittis purus sit amet volutpat consequat mauris nunc congue. Eu tincidunt tortor aliquam nulla. Urna neque viverra justo nec ultrices dui sapien eget. Sed viverra tellus in hac habitasse platea dictumst. Lectus quam id leo in vitae turpis massa. Neque viverra justo nec ultrices dui sapien eget. Molestie a iaculis at erat pellentesque adipiscing commodo. Mauris vitae ultricies leo integer malesuada nunc vel. Commodo elit at imperdiet dui accumsan sit amet. Ligula ullamcorper malesuada proin libero. Donec ac odio tempor orci.

#### Anchor {: #test}

## Definition lists
Definition lists may be useful for glossary entries.


Item 1
:    This is a def list

Item 2
:    This is also in a def list

## Tables

| Left align | Center align | Right align |
|:-----------|:------------:|------------:|
| Left       | Center       |       right |
| Left       | Center       |       right |
