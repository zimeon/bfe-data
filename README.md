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

Same procedure, Load Work from <https://zimeon.github.io/bfe-data/02_work_tidied.jsonld> works fine, shows new title and date.

## 3. Add extra data on the Work

```
> diff 02_work_tidied.jsonld 03_work_extra_triple.json
14a15
>    "http://example.org/predicate": "An Object",
31c32
<    "bf:mainTitle": "The Thing - Tidied"
---
>    "bf:mainTitle": "The Thing - Extra Data"
```

--> <https://zimeon.github.io/bfe-data/03_work_extra_triple.json>

Same procedure, Load Work from <https://zimeon.github.io/bfe-data/03_work_extra_triple.json> works fine, shows new title and date. As expected, does not show new data on form because this isn't set up in a profile. But, does show the extra triple in the RDF when Preview is selected. So, the extra triple is retained.

## 4. Other JSON-LD forms... compacted

Go to [JSON-LD playground](https://json-ld.org/playground/), load previous example as input, select "Compacted" form, copy output and change title to end with "- Compacted".

--> <https://zimeon.github.io/bfe-data/04_work_compacted.jsonld>

## 5. Other JSON-LD forms... expanded

Go to [JSON-LD playground](https://json-ld.org/playground/), load example 3 again as input, select "Expanded" formr, copy output and change title to end with "- Expanded".

--> <https://zimeon.github.io/bfe-data/05_work_expanded.jsonld>