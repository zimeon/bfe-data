# Exploring BFE data import

## 1. Create test Work

Went to <http://bibframe.org/bibliomata/bfe/development.html> created a new Monograph / Work. Entered work title "The Thing", date of work "2018-10-19". Click preview at bottom of page, copy JSON-LD from panel.

--> <https://zimeon.github.io/bfe-data/01_work_as_exported.jsonld>

Starting [BFE](http://bibframe.org/bibliomata/bfe/development.html) again, Load Work, enter URL `https://zimeon.github.io/bfe-data/01_work_as_exported.jsonld` then click Submit URL. Goes to editor page with title and date shown.

## 2. Tidy the data

Make some arbitrary bnode URIs instead of the ones output, and remove the duplicate type definition, change title to add "- Titied":

```
> diff 01_work_as_exported.jsonld 02_work_tidied.jsonld
13c13
<    "@id": "http://id.loc.gov/resources/works/e289146822784588077004627201841415121964",
---
>    "@id": "_:b0",
20c20
<     "@id": "_:bnode5c8go15KEHTz99pytb6gca"
---
>     "@id": "_:b1"
29,34c29,31
<    "@id": "_:bnode5c8go15KEHTz99pytb6gca",
<    "@type": [
<     "bf:Title",
<     "bf:Title"
<    ],
<    "bf:mainTitle": "The Thing"
---
>    "@id": "_:b1",
>    "@type": "bf:Title",
>    "bf:mainTitle": "The Thing - Tidied"
```

--> <https://zimeon.github.io/bfe-data/02_work_tidied.jsonld>

## 3. Add extra data on the Work

```
> diff 02_work_tidied.jsonld 03_work_extra_triple.json
14a15
>    "http://example.org/predicate": "An Object",
```

--> <https://zimeon.github.io/bfe-data/03_work_extra_triple.json>