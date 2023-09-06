%%%
Title = "tagbibs"
area = "Internet"
workgroup = "Internet"

[seriesInfo]
name = "tagbibs"
value = "draft-tagbibs-leonvankammen-00"
stream = "IETF"
status = "informational"

date = 2022-08-01 

[[author]]
initials="L.R."
surname="van Kammen"
fullname="L.R. van Kammen"

%%%

<!-- for annotated version see: https://raw.githubusercontent.com/ietf-tools/rfcxml-templates-and-schemas/main/draft-rfcxml-general-template-annotated-00.xml -->

.# Abstract

Tagbibs are tiny paperfriendly tags, which expand into multiple [BibTags](https://en.wikipedia.org/wiki/BibTeX).<br>
Using OCR, they're great for connecting scanned paper with online graphs.<br>
The goal of this spec is three-fold:

* specify tagbibs: a terse tagdescription which expands into a reversed BibTag 
* specify tagbibs URI fragments: a way to hint browsers to jump to (bib)tagged content
* specify bibrulers: dumb line-based separators for bibtags

{mainmatter}

# Definitions

| term              | explanation              |
|-------------------|--------------------------|
| linear LUT        | array lookup table       |

# What are tagbibs

<img src="postit.jpg" style="max-width:400px"/>

tagbibs allow non-technical humans to write reversed BibTags in a short-form (on paper):

```
Please get out the laundry 

@laundry@chores@todo
```

These tagbibs basically tag 'laundry' by expanding into the following BibTags:

```
Please get out the laundry 

@chores{laundry,
  
}
@todo{laundry,

}
```

> tagbibs are basically one step up from socialmedia hashtags, allowing mere mortals to tag things with pencil or keyboard. 

There's no precise predicates or properties, which allows citizen annotation (an essential precursor of RDF).

# What are bibrulers

Bibrulers are linebased separators, used by [visual-meta](https://visual-meta.info) as a means of organizing BibTags:

```
@{visual-meta-start}
@{visual-meta-header-start}
@visual-meta{
 version = {1.1},
 generator = {Author 7.6.2 (1064)},
}
@{visual-meta-header-end}
@{visual-meta-bibtex-self-citation-start}
@book{2021-12-08T10:57:03Z/TheFutureo,
author = {Frode Alexander Hegland},
editor = {Frode Alexander Hegland},
title = {The Future of Text ||},
...
```

This snippet would get decoded to this unnested array:

```
[
  { ruler: `@{visual-meta-start}` },
  { ruler: `@{visual-meta-header-start}` },
  { tag: "@visual-meta", props: { version: "1.1", generator: "Author 7.6.2 1064)" } }
  { ruler: `@{visual-meta-bibgtex-self-citation-start}` },
  { tag: "@book{2021-12-08T10:56:03Z/TheFutureo", ..... }
  ...
]
```

Why not a nested tree-structure? This kneejerk reaction should always be considered 'a last option'.

> Bibrulers don't try to re-invent XML, they are rather promoting dumb, unnested lists, which are much faster/simpler to traverse & implement in all languages.


# What are tagbibs fragments

Just like regular [URI Fragments](https://en.wikipedia.org/wiki/URI_fragment) they hint the browser to focus anything (Bib)Tagged:

* `https://website.com#@chores@todo`
* `https://mastodon.io/myprofile/#@chores@todo`
* `://3dworld.gltf#@chores@todo`

> Format: `#@<bibtag>[ + @<bibtag> + [ ... ] ]`

# Contact

* leonvankammen|gmail.com

# IANA Considerations

This document has no IANA actions.

# Acknowledgments

TODO acknowledge.
