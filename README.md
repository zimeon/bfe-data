# Exploring BFE data import

These notes from <https://github.com/zimeon/bfe-data>, nice form at <https://zimeon.github.io/bfe-data/>.

Resources:

  * the LC BFE instance is at <http://bibframe.org/bfe/index.html>
  * the `jsonld` command-line tool is part of <https://github.com/ruby-rdf/json-ld>.

## Minimal Work in Monograph profile

### 1. Create test Work

Went to [BFE](http://bibframe.org/bfe/index.html) created a new Monograph / Work. Entered work title "The Thing", date of work "2018-10-19". Click preview at bottom of page, copy JSON-LD from panel.

--> <https://zimeon.github.io/bfe-data/01_work_as_exported.jsonld>

Starting [BFE](http://bibframe.org/bfe/index.html) again, Load Work, enter URL `https://zimeon.github.io/bfe-data/01_work_as_exported.jsonld` then click Submit URL. Goes to editor page with title and date shown.

### 2. Tidy the data

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

### 3. Add extra data on the Work

```
> diff 02_work_tidied.jsonld 03_work_extra_triple.jsonld
14a15
>    "http://example.org/predicate": "An Object",
31c32
<    "bf:mainTitle": "The Thing - Tidied"
---
>    "bf:mainTitle": "The Thing - Extra Data"
```

--> <https://zimeon.github.io/bfe-data/03_work_extra_triple.jsonld>

Same procedure, Load Work from <https://zimeon.github.io/bfe-data/03_work_extra_triple.jsonld> works fine, shows new title and date. As expected, does not show new data on form because this isn't set up in a profile. But, does show the extra triple in the RDF when Preview is selected. So, the extra triple is retained.

### 4. Other JSON-LD forms... compacted

Go to [JSON-LD playground](https://json-ld.org/playground/), load previous example as input, select "Compacted" form, copy output and change title to end with "- Compacted".

--> <https://zimeon.github.io/bfe-data/04_work_compacted.jsonld>

Again, is loaded fine, show expected data. Preview has normalized form with extra triple.

### 5. Other JSON-LD forms... expanded

Go to [JSON-LD playground](https://json-ld.org/playground/), load example 3 again as input, select "Expanded" formr, copy output and change title to end with "- Expanded".

--> <https://zimeon.github.io/bfe-data/05_work_expanded.jsonld>

Again, is loaded fine, show expected data. Preview has normalized form with extra triple.

### 6. Simplification and then flattening

After manual simplification to denormalize (while retaining same bnode names and JSON-LD `@graph` structure) gives:
`
--> <https://zimeon.github.io/bfe-data/06_work_simplified.jsonld>

We don't get the title or the label for `text` type, but do see that date. In the preview turtle we see that data has been lost:

```
:b0_b0 <http://example.org/predicate> "An Object";
    bf:content <http://id.loc.gov/vocabulary/contentTypes/txt>;
    bf:originDate "2018-10-19";
    bf:title _:b0_b1;
    a bf:Work.
```

even though from a JSON-LD point of view it codes for the same triples as `03_work_extra_triple.json`:

```
> jsonld --format ntriples 03_work_extra_triple.jsonld | sort > 03.nt

Parsed 9 statements in 0.020052 seconds @ 448.8330341113106 statements/second.
(py3)simeon@RottenApple bfe-data (master %)> jsonld --format ntriples 06_work_simplified.jsonld | sort > 06.nt

Parsed 9 statements in 0.018349 seconds @ 490.4899449561284 statements/second.
(py3)simeon@RottenApple bfe-data (master %)> diff 03.nt 06.nt 
9c9
< _:b1 <http://id.loc.gov/ontologies/bibframe/mainTitle> "The Thing - Extra Data" .
---
> _:b1 <http://id.loc.gov/ontologies/bibframe/mainTitle> "The Thing - Simplified" .
```

If the data is flattened:

```
> jsonld --flatten 06_work_simplified.jsonld > 06_work_simplified_flattened.jsonld
Flattened in 0.018733 seconds.
```

--> <https://zimeon.github.io/bfe-data/06_work_simplified_flattened.jsonld>>

then this form loads correctly, with the same 9 triples showing in tutle preview as `03_work_extra_triple.json`.

### Conclusions for minimal Work tests in Monograph profile

From this experiment it seems that BFE does not care about some minor changes in the form of JSON-LD it gets, but will not accept various other simplifications. Use of the JSON-LD flatten algorithm appears to make the example in simplified form load again (though not sure how general that is).

BFE is also happy to accept and persist extra triples (I tested with just one but assume that carries over for many), but obviously only shows things that a given profile knows about. However, if you go to Preview the extra data is shown in Turtle, JSON-LD and RDF/XML displays.

During these tests I found the upload to be unreliable. Sometimes I’d try to enter a URL and load, and then nothing would happen (was using Chrome on Mac). Other times it worked fine, so I just retried when it appeared to fail.

## Minimal Instance with Monograph profile

### 11. Create test minimal Instance

Went to [BFE](http://bibframe.org/bfe/index.html) created a new Monograph / Instance. Entered instance title "An Instance". Click preview at bottom of page, copy JSON-LD from panel.

--> <https://zimeon.github.io/bfe-data/11_instance.jsonld>

2018-10-19 -- Attempting to load this in the "Load IBC" did not work. In both Chrome and Firefox the browser seems to hang for some time after clicking "Submit URL", and eventually becomes response agiain on the same load page. No data gets loaded. @kirkhess resolved an issue with expectations of `instanceOf` to fix this.

Now this loads fine and shows the Instance.

### 12. Tidy without changing structure

Tidying to remove some unused cruft and use generic bnode names as follows:

```
> diff 11_instance.jsonld 12_instance_tidied.jsonld
5,9c5
<   "xsd": "http://www.w3.org/2001/XMLSchema#",
<   "bf": "http://id.loc.gov/ontologies/bibframe/",
<   "bflc": "http://id.loc.gov/ontologies/bflc/",
<   "madsrdf": "http://www.loc.gov/mads/rdf/v1#",
<   "pmo": "http://performedmusicontology.org/ontology/"
---
>   "bf": "http://id.loc.gov/ontologies/bibframe/"
13c9
<    "@id": "http://id.loc.gov/resources/instances/e93211693577620536533546183858602884391",
---
>    "@id": "_:b0",
25c21
<     "@id": "_:bnode7ZhNN2V8jv1XjBGERKYYGb"
---
>     "@id": "_:b1"
44,49c40,42
<    "@id": "_:bnode7ZhNN2V8jv1XjBGERKYYGb",
<    "@type": [
<     "bf:Title",
<     "bf:Title"
<    ],
<    "bf:mainTitle": "An Instance"
---
>    "@id": "_:b1",
>    "@type": "bf:Title",
>    "bf:mainTitle": "An Instance - Tidied"
```

--> <https://zimeon.github.io/bfe-data/12_instance_tidied.jsonld>

Works fine and shows the same data as 11 (modulo title change). The title, the carrier/media/issuance labels all show fine. There are 13 triples in the turtle preview.

### 13. Simplifying

If this JSON-LD is simplified (while still retaining the explicit `_:b1` `bf:Title` bnode which shouldn't be necessary) as follows:

--> <https://zimeon.github.io/bfe-data/13_instance_simplified.jsonld>

This loads but without title or labels. In the preview output there are only 5 triples (whereas the input has 13):

```
...
_:b0_b0 bf:carrier <http://id.loc.gov/vocabulary/carriers/nc>;
    bf:issuance <http://id.loc.gov/vocabulary/issuance/mono>;
    bf:media <http://id.loc.gov/vocabulary/mediaTypes/n>;
    bf:title _:b0_b1;
    a bf:Instance.
```

There is no change if the JSON-LD in expanded or compacted:

```
> jsonld --expand 13_instance_simplified.jsonld > 13_instance_simplified_expanded.jsonld
Expanded in 0.017915 seconds.
```

--> <https://zimeon.github.io/bfe-data/13_instance_simplified_expanded.jsonld>

```
> jsonld --compact 13_instance_simplified.jsonld > 13_instance_simplified_compacted.jsonld
Compacted in 0.017331 seconds.
```

--> <https://zimeon.github.io/bfe-data/13_instance_simplified_compacted.jsonld>

although all of these forms differ from `12_instance_tidied.jsonld` in the title, and generate identical ntriples output:

```
> jsonld --format ntriples 12_instance_tidied.jsonld | sort > 12.nt

Parsed 13 statements in 0.019021 seconds @ 683.4551285421377 statements/second.
> jsonld --format ntriples 13_instance_simplified.jsonld | sort > 13.nt

Parsed 13 statements in 0.018777 seconds @ 692.336368962028 statements/second.
> diff 12.nt 13.nt 
13c13
< _:b1 <http://id.loc.gov/ontologies/bibframe/mainTitle> "An Instance - Tidied" .
---
> _:b1 <http://id.loc.gov/ontologies/bibframe/mainTitle> "An Instance - Simplified" .
> jsonld --format ntriples 13_instance_simplified_expanded.jsonld | sort > 13ex.nt

Parsed 13 statements in 0.02384 seconds @ 545.3020134228188 statements/second.
> diff 13.nt 13ex.nt 
> jsonld --format ntriples 13_instance_simplified_compacted.jsonld | sort > 13cp.nt

Parsed 13 statements in 0.020405 seconds @ 637.0987503062975 statements/second.
> diff 13.nt 13cp.nt 
```

### 14. Over Simplifying

Taking this one step further, we can removed the `@graph` to get simpler JSON-LD:

--> <https://zimeon.github.io/bfe-data/14_instance_oversimplified.jsonld>

This loads but without title or labels. In the preview output there are only 5 triples, the same as with `13_instance_simplified.jsonld`:

```
... 
_:b0_b0 bf:carrier <http://id.loc.gov/vocabulary/carriers/nc>;
    bf:issuance <http://id.loc.gov/vocabulary/issuance/mono>;
    bf:media <http://id.loc.gov/vocabulary/mediaTypes/n>;
    bf:title _:b0_b1;
    a bf:Instance.
```

Whereas, again, the input converts to the same 13 triples as `12_instance_tidied.jsonld` (modulo title):

```
> jsonld --format ntriples 14_instance_oversimplified.jsonld | sort > 14.nt

Parsed 13 statements in 0.019161 seconds @ 678.4614581702416 statements/second.
> diff 12.nt 14.nt 
13c13
< _:b1 <http://id.loc.gov/ontologies/bibframe/mainTitle> "An Instance - Tidied" .
---
> _:b1 <http://id.loc.gov/ontologies/bibframe/mainTitle> "An Instance - Over-Simplified" .
```

--> <https://zimeon.github.io/bfe-data/14_instance_flattened.jsonld>

When the flattened form is loaded the carrier/media/issuance labels all show fine, but the title is lost and also does not show in the preview output.

If the flattend form is then compacted (to get rid of all the `@value` baggage and extra arrays):

--> <https://zimeon.github.io/bfe-data/14_instance_flattened_compacted.jsonld>

then the title _and_ the carrier/media/issuance labels all show fine. The preview output shows:

```
@prefix bf: <http://id.loc.gov/ontologies/bibframe/>.
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>.
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#>.

<http://id.loc.gov/vocabulary/carriers/nc> a bf:Carrier;
    rdfs:label "volume".
<http://id.loc.gov/vocabulary/issuance/mono> a bf:Issuance;
    rdfs:label "single unit".
<http://id.loc.gov/vocabulary/mediaTypes/n> a bf:Media;
    rdfs:label "unmediated".
_:b0_b0 a bf:Instance;
    bf:carrier <http://id.loc.gov/vocabulary/carriers/nc>;
    bf:issuance <http://id.loc.gov/vocabulary/issuance/mono>;
    bf:media <http://id.loc.gov/vocabulary/mediaTypes/n>;
    bf:title _:b0_b1.
_:b0_b1 a bf:Title;
    bf:mainTitle "An Instance - Over-Simplified".
```

which is correct.

### Conclusions for minimal Instance tests in Monograph profile

**Based on the results of tests 12, 13 and 14 -- BFE is critically sensitive to the form of the JSON-LD supplied. Straightforward expansion, compaction or flattening do not solve the issue. However, it appears that flatten and then compact may be a solution!**

## More complex instance data

### 20. Load instance data from MARC

In [BFE](http://bibframe.org/bfe/index.html), select tab `Load MARC`, entry type `LCCN` and then enter [22010771](https://lccn.loc.gov/22010771). After loading the data a BIBFRAME Instance page is displayed with for "Two-gun Sue". Select Preview from the bottom of the page, and copy JSON-LD output to:

--> <https://zimeon.github.io/bfe-data/20_two_gun_sue.jsonld>

Restart BFE and then select tab `Load IBC`, enter `https://zimeon.github.io/bfe-data/20_two_gun_sue.jsonld`. Result in an error message **Please choose an instance to load**

Attempting to load as a work does complete but seems to just pick out 4 of the 161 triples:

```
...
<http://id.loc.gov/resources/works/e201798077861968054014173633604452640887> bf:content <http://id.loc.gov/vocabulary/contentTypes/txt>;
    a bf:Work.
<http://id.loc.gov/vocabulary/contentTypes/txt> a bf:Content;
    rdfs:label "text".
```

## 21, 22, 23. Manipulation of this instance

... have played with the below but all pointless as BFE won't load <https://zimeon.github.io/bfe-data/20_two_gun_sue.jsonld>

```
> rapper --input turtle --output ntriples 20_two_gun_sue.ttl | sort > 21_two_gun_sue.nt 
rapper: Parsing URI file:///Users/simeon/src/bfe-data/20_two_gun_sue.ttl with parser turtle
rapper: Serializing with serializer ntriples
rapper: Parsing returned 161 triples
> jsonld --input-format turtle --compact 20_two_gun_sue.ttl > /tmp/a
Compacted in 0.011164 seconds.
> jsonld --flatten /tmp/a > /tmp/b
Flattened in 0.023556 seconds.
> jsonld --compact /tmp/b > 22_two_gun_sue_flat_compact.jsonld
Compacted in 0.030836 seconds.
> jsonld --format ntriples 22_two_gun_sue_flat_compact.jsonld | sort > 23_two_gun_sue_flat_compact.nt
Parsed 161 statements in 0.057687 seconds @ 2790.923431622376 statements/second.
> rdfdiffb.py -s 21_two_gun_sue.nt 23_two_gun_sue_flat_compact.nt 
Graphs 21_two_gun_sue.nt and 23_two_gun_sue_flat_compact.nt are isomorphic
```
