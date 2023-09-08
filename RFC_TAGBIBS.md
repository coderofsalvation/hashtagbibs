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

HashTagBibs (or simply: **'bibs'**) are tiny tags, allowing mere mortals to connect words to other words using pencil, voice or keyboard.<br>
They expand into multiple [BibTags](https://en.wikipedia.org/wiki/BibTeX).<br>
For example, using OCR, scanned paper can 'connect' with online graphs.<br>
Think of it as the brother of hashtags: a command for tagging-this-with-that.<br>
The goal of this spec is three-fold:

* specify bibs: a terse tagdescription which expands into a reversed BibTag 
* specify bibs as URI fragment: a way to hint browsers to jump to (bib)tagged content
* specify bibrulers: dumb line-based separators for bibtags

{mainmatter}

# What are bibs

<img src="postit.jpg" style="max-width:400px"/>

bibs allow non-technical humans to write reversed BibTags in a short-form (on paper):

```
John please get out the laundry 

#john
#laundry@chores@todo
```

> This basically means: 

* **tag** `john` with tagname `john`
* **tag** `laundry` with tagname `chores` and `todo`, by expanding into the following BibTags:

```
John please get out the laundry 

@john{john,

}

@chores{laundry,
  
}
@todo{laundry,

}
```

> the word and `john` `laundry` can now be highlighted in the human text (or 3D object can be shown when their objectname matches). bibs are basically one step up from socialmedia hashtags, allowing mere mortals to connect words to other things using pencil, voice or keyboard. 

There's no precise predicates or properties (just 'this points to that', which empowers citizen annotation (an essential precursor of RDF).

## format

* `@<word>[@tag[@anothertag[...]]]` 

> syntactically, bibs are (concatenated) emaildomains without an extension 

| language         | example                                                                                    |
|------------------|--------------------------------------------------------------------------------------------|
| javascript regex | `/(@[a-zA-Z0-9_+]+@[a-zA-Z0-9_@]+)/g`                                                      |
| shell grep       | `cat textwithbibs.txt | grep -oE '(@[a-zA-Z0-9_+]+@[a-zA-Z0-9_@]+)'`                       | 
| shell awk        | `cat textwithbibs.txt | xargs -n1 | awk '/(@[a-zA-Z0-9_+]+@[a-zA-Z0-9_@]+)/ { print $0 }'` |

1. to qualify as a bib, a word should start with a hashtag, and contain at least 1 `@` character
1. last bib wins: overlapping bibs overwrite eachother (last tag(s) win)
1. spaces are not allowed, maximum by using `+` to represents spaces (`the+bill@todo` e.g.)

```
#great+gatsby@book@readinglist
#great+gatsby@book
```

would only expand to:

```
@book{great+gatsby

}
```

## Example: an textual kanban using tags

```
buy milk
finish paper
contact John
buy the great gatsby

#milk@todo
#contact@doing
#contact@done
#gatsby@done
#finish+paper@doing
```

If this text would be written on a paper, it could be scanned by a computer and represented spatially like so:

| todo      | doing        | done             |
|-----------|--------------|------------------|
| buy milk  | finish paper | contact John     |
|                          | buy great gatsby |

> One could argue that tagging a word like `buy` would create conflicts, but for most purposes this is really easy to spot / workaround. For serious, large bodies of text use (unique) expanded BibTags instead.

## Example javascript bibs expander

```
var bibs   = { regex: /(@[a-zA-Z0-9_+]+@[a-zA-Z0-9_@]+)/g, tags: {}}
text.replace( bibs.regex , (m,k,v) => {
   tok   = m.substr(1).split("@")
   match = tok.shift()            
   tok.map( (t) => bibs.tags[match] = `@${t}{${match},\n}\n` )
})
text = Object.values(bibs.tags).join('\n') + bibtex.replace( bibs.regex, '')
```

> Tiny but powerful

## Merging (BibTagged) overlaps

When a bib (`#great+gatsby@book` is copy-pasted into another document (a PDF or Textfile with a [visual-meta](https://visual-meta.info) appendix e.g.):

1. the editor should check for the existence of `@book{great+gatsby`
1. if exist: do nothing, leave target document as is
1. if not: create the expanded BibTag 
1. optionally, the editor can offer to add properties (as bibs are propertyless)

> propertyless bibs, are a great way as a 'process later'-medium ("I wrote down `great+gatsby@readinglist` on a papertowel/email, to scan/copy-paste it later)

# What are bibs in a URI fragment

Just like regular [URI Fragments](https://en.wikipedia.org/wiki/URI_fragment) they hint the browser to focus anything (Bib)Tagged:

* `https://website.com#@chores@todo`
* `https://mastodon.io/myprofile/#@chores@todo`
* `://xrworld.org/3dscene.gltf#@chores@todo`

> Format: `#@<bibtag>[ + @<bibtag> + [ ... ] ]`

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
  { ruler: `{visual-meta-start}` },
  { ruler: `{visual-meta-header-start}` },
  { k: "visual-meta", v: { version: "1.1", generator: "Author 7.6.2 1064)" } }
  { ruler: `{visual-meta-header-end}` },
  { ruler: `{visual-meta-bibgtex-self-citation-start}` },
  { k: "book{2021-12-08T10:56:03Z/TheFutureo", v: { author: "Frode ...", ... } }
  ...
]
```

Why not a nested tree-structure? This kneejerk reaction should always be considered 'a temporary option'.<br>
Bibrulers:

1. don't try to re-invent XML or JSON
1. don't promote traversing graphs (instead: just a list with tags)
1. they promote rather dumb, unnested lists
1. they  are much faster/simpler to lookup, implement, (de)serialize in low- and hihglevel languages.

# Contact

* leonvankammen|gmail.com

# IANA Considerations

This document has no IANA actions.

# Acknowledgments

TODO acknowledge.
