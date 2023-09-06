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

Tagbibs are tiny paperfriendly tags, which expand into multiple [BibTags](https://en.wikipedia.org/wiki/BibTeX).
Using OCR, they're great for connecting scanned paper with online graphs.
The goal of this document is twofold:

* specify tagbibs: a terse tagdescription which expands into a reversed BibTag 
* specify tagbibs URI fragments: a way to hint browsers to jump to (bib)tagged content
* specify bibrulers: dumb line-based separators for bibtags

{mainmatter}

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


# What are tagbibs fragments

`#@chores@todo

# Contact

* leonvankammen|gmail.com

# IANA Considerations

This document has no IANA actions.

# Acknowledgments

TODO acknowledge.
