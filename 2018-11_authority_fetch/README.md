# Authority Term Fetch

Exploring implications of the one-level-plus-labels [Sinopia external data specification](https://ld4p.github.io/sinopia/external-data):

> GET => Fetch:
> * In (payload or param): URI
> * 200 Out (payload, preference JSON): All RDF statements with URI as subject in External Store, plus triples that provide an  `rdfs:label` property for the object values.

## Using current `lookup.ld4l.org` with authority URIs

Start off with doing a current fetch from `lookup.ld4l.org` for the authority for Douglas Adams. This is based on the authority URI and not the RWO URI because the LC dump data doesn't yet include the RWO data:

```
> wget --output-document adams_fetch.jsonld  'https://lookup.ld4l.org/authorities/fetch/linked_data/locnames_ld4l_cache?uri=http://id.loc.gov/authorities/names/n80076765&format=jsonld'
--2018-11-21 17:16:07--  https://lookup.ld4l.org/authorities/fetch/linked_data/locnames_ld4l_cache?uri=http://id.loc.gov/authorities/names/n80076765&format=jsonld
Resolving lookup.ld4l.org... 34.225.103.176, 34.202.129.86
Connecting to lookup.ld4l.org|34.225.103.176|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [application/ld+json]
Saving to: 'adams_fetch.jsonld'

adams_fetch.jsonld                             [ <=>                                                                                   ]   8.71K  --.-KB/s    in 0s

2018-11-21 17:16:09 (32.4 MB/s) - 'adams_fetch.jsonld' saved [8922]
```

```
> jsonld --format ntriples adams_fetch.jsonld > adams_fetch.nt

Parsed 102 statements in 3.732466 seconds @ 27.327777399713753 statements/second.
```

So, this graph has 102 triples. We can extract the portion that conforms with the one-level-plus-labels specification using SPARQL:

```
> spahqler --graph adams_fetch.nt --query "CONSTRUCT { ?term ?p ?o . ?o rdfs:label ?o2 . } WHERE { ?term ?p ?o . OPTIONAL { ?o rdfs:label ?o2 . } }" --binding 'term=<http://id.loc.gov/authorities/names/n80076765>' > adams_one_level.nt

> wc -l adams_one_level.nt
43 adams_one_level.nt
(py3) simeon@Cider 2018-11_authority_fetch (master %)> more adams_one_level.nt
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/2000/01/rdf-schema#seeAlso> <http://id.loc.gov/authorities/names/nr93025732> .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/2004/02/skos/core#editorial> "[Machine-derived non-Latin script reference project.]" .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://www.loc.gov/mads/rdf/v1#Authority> .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#editorialNote> "[Machine-derived non-Latin script reference project.]" .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#see> <http://id.loc.gov/authorities/names/nr93025732> .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#hasSource> _:N7c26b4c370ee43eda7ef63e33ee508fb .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#isMemberOfMADSScheme> <http://id.loc.gov/authorities/names> .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/2004/02/skos/core#editorial> "[Non-Latin script references not evaluated.]" .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#hasExactExternalAuthority> <http://viaf.org/viaf/sourceID/LC%7Cn+80076765#skos:Concept> .
<http://id.loc.gov/authorities/names/n80076765> <http://id.loc.gov/vocabulary/identifiers/lccn> "n 80076765" .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#hasVariant> _:Naa9a5f367d654b28a5ce0bccb9f3195d .
<http://id.loc.gov/authorities/names/n80076765> <http://id.loc.gov/vocabulary/identifiers/local> "(OCoLC)oca00458128" .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#hasSource> _:N78b58f7026fb4081961b80b2bb6c07a5 .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#identifiesRWO> <http://id.loc.gov/rwo/agents/n80076765> .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#elementList> _:N9ad9829df2644b32a587d921b15bed6c .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#classification> "PR6051.D3352" .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/2004/02/skos/core#prefLabel> "Adams, Douglas, 1952-2001"@en .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#hasSource> _:N5c3009b2ddd84f9b849bc61313b7c82f .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/2004/02/skos/core#inScheme> <http://id.loc.gov/authorities/names> .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://www.w3.org/2004/02/skos/core#Concept> .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/2004/02/skos/core#changeNote> _:N97eb2183e3de49658fd0e45ec056b7ff .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#adminMetadata> _:N4311f4b6780f4d8ab3e315cf4cb9a872 .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/2004/02/skos/core#exactMatch> <http://viaf.org/viaf/sourceID/LC%7Cn+80076765#skos:Concept> .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/2008/05/skos-xl#altLabel> "\u05D0\u05D3\u05D0\u05DE\u05E1, \u05D3\u05D0\u05D2\u05DC\u05D0\u05E1, 1952-2001-"@en .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#hasVariant> _:N08a38ec3cdea409eb4eed77b7b462e56 .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/2008/05/skos-xl#altLabel> "\u4E9E\u7576\u65AF, 1952-2001"@en .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://www.loc.gov/mads/rdf/v1#PersonalName> .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#editorialNote> "[Non-Latin script references not evaluated.]" .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/2004/02/skos/core#altLabel> "\u4E9E\u7576\u65AF, 1952-2001" .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/2008/05/skos-xl#altLabel> _:N4dadf48969114b54bf21150a42e70f00 .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/2008/05/skos-xl#altLabel> _:Nf321fc5459c94109aecb3c1a59ddfeb1 .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/2004/02/skos/core#altLabel> "\u05D0\u05D3\u05D0\u05DE\u05E1, \u05D3\u05D0\u05D2\u05DC\u05D0\u05E1, 1952-2001-" .
<http://id.loc.gov/authorities/names/n80076765> <http://www.w3.org/2004/02/skos/core#changeNote> _:Nad0dda7ae4eb4ff1b89987a96fec2014 .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#hasSource> _:Nf4796bd45b6c45baad3ff536a5f85996 .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#authoritativeLabel> "Adams, Douglas, 1952-2001"@en .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#hasSource> _:N367ea32ddb7c4d6ea6ef6352411c97ce .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#hasSource> _:Ne1fd1c5afe8349aa9fd26200ad1b95c3 .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#isMemberOfMADSCollection> <http://id.loc.gov/authorities/names/collection_NamesAuthorizedHeadings> .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#hasSource> _:Nce802deb241b46bf910cc1e1d98cce5a .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#isMemberOfMADSCollection> <http://id.loc.gov/authorities/names/collection_LCNAF> .
<http://id.loc.gov/authorities/names/n80076765> <http://www.loc.gov/mads/rdf/v1#adminMetadata> _:N4f1f3ebc495a4c6aacd43faf08b01ac3 .
```

Observations:

  1. There are no `rdfs:label` properties in this graph. The labels that are present use `skos:prefLabel`, `mads:authoritativeLabel`, `skos:altLabel`, and `skosxl:altLabel`
  2. Some links to other entities are to entitites of a different type and so it is not obvious whether a particulaly scoped term fetch should return results for them (and in the general case we don't know whether the actual URI has proper linked-data support such that we could dereference the URI). For example, the RWO URI and a related name URI return nothing if put into a fetch of QA using the same authority base:

```
> curl 'https://lookup.ld4l.org/authorities/fetch/linked_data/locnames_ld4l_cache?uri=http://id.loc.gov/rwo/agents/n80076765?format=jsonld'
{}
> curl 'https://lookup.ld4l.org/authorities/fetch/linked_data/locnames_ld4l_cache?uri=http://id.loc.gov/authorities/names/nr93025732?format=jsonld'
{}
> 
```

  3. Some links to other entities are to bnodes (e.g. `_:N4f1f3ebc495a4c6aacd43faf08b01ac3` in above). In general one cannot expect bnode identifiers to have any meaning outside of a particular serialization document. One could have a rule that systems feeding data to Sinopia must support persistent (how persistent?) bnode identifiers but that may be hard to support depending upon the implementation approach. One could alternatively require the bnode identifiers to be skolemized but that has a problem of inserting unwanted identifiers into Sinopia unless there is a local convention that allows them to identified and removed again to recover bnodes.

## Comparison using LC RWO URIs

Although we would like to use the RWO data, the data dumps from LC (and hence Dave's cache), do not currently have RWO data except the link from the authority. We can, however, grab this data straigh from LC on a per-item basis and explore what might happen if it were available via `lookup.ld4l.org`. Taking the case of Douglas Adams again, <http://id.loc.gov/rwo/agents/n80076765>:

```
> curl -L -H "Accept: application/rdf+xml" http://id.loc.gov/rwo/agents/n80076765 > rwo_adams.xml
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100  4279  100  4279    0     0  27937      0 --:--:-- --:--:-- --:--:-- 27937

(py3)simeon@RottenApple 2018-11_authority_fetch (master *%)> spahqler --graph rwo_adams.xml --graph-format xml --query "CONSTRUCT { ?term ?p ?o . ?o rdfs:label ?o2 . } WHERE { ?term ?p ?o . OPTIONAL { ?o rdfs:label ?o2 . } }" --binding 'term=<http://id.loc.gov/rwo/agents/n80076765>' > rwo_adams_one_level.nt
```

we get the graph:

```
> more rwo_adams_one_level.nt 
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#occupation> <http://id.loc.gov/authorities/subjects/sh85009793> .
_:Nd193bc28d56b435fae207b86d7b408be <http://www.w3.org/2000/01/rdf-schema#label> "(lcgft) Science fiction" .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#fieldOfActivity> _:Nd193bc28d56b435fae207b86d7b408be .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#deathPlace> <http://id.loc.gov/authorities/names/n79081574> .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://www.loc.gov/mads/rdf/v1#RWO> .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.w3.org/2000/01/rdf-schema#label> "\n              Adams, Douglas,\n              1952-2001\n            " .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#associatedLocale> <http://id.loc.gov/authorities/names/n82068148> .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#occupation> <http://id.loc.gov/authorities/subjects/sh85110591> .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#hasAffiliation> _:N7879b19d7417418fade5eb82213415f7 .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://id.loc.gov/ontologies/bibframe/Person> .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#isIdentifiedByAuthority> <http://id.loc.gov/authorities/names/n80076765> .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#deathDate> _:N0acc5713c08d4c38aed277efff05a624 .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#hasAffiliation> _:N2d3935c4059f49c8be2bd8f4e65d2f29 .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#associatedLocale> <http://id.loc.gov/authorities/names/n79023147> .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#birthPlace> <http://id.loc.gov/authorities/names/n79018410> .
_:N4fbefa43c7234b0eb0ba80795990db7a <http://www.w3.org/2000/01/rdf-schema#label> "Computer game writer" .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#occupation> _:N4fbefa43c7234b0eb0ba80795990db7a .
_:N0acc5713c08d4c38aed277efff05a624 <http://www.w3.org/2000/01/rdf-schema#label> "(edtf) 2001-05-11" .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://xmlns.com/foaf/0.1/Person> .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#gender> <http://id.loc.gov/authorities/demographicTerms/dg2015060003> .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#occupation> <http://id.loc.gov/authorities/subjects/sh85118942> .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#occupation> <http://id.loc.gov/authorities/subjects/sh85092863> .
_:N7dd0d032d89d4dc58e623a5b21a353ef <http://www.w3.org/2000/01/rdf-schema#label> "(edtf) 1952-03-11" .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#associatedLanguage> <http://id.loc.gov/vocabulary/languages/eng> .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.loc.gov/mads/rdf/v1#birthDate> _:N7dd0d032d89d4dc58e623a5b21a353ef .

```

Observations:

  1. There are some `rdfs:label` labels on entities linked from the RWO:

```
> grep http://www.w3.org/2000/01/rdf-schema#label rwo_adams_one_level.nt 
_:Nd193bc28d56b435fae207b86d7b408be <http://www.w3.org/2000/01/rdf-schema#label> "(lcgft) Science fiction" .
<http://id.loc.gov/rwo/agents/n80076765> <http://www.w3.org/2000/01/rdf-schema#label> "\n              Adams, Douglas,\n              1952-2001\n            " .
_:N4fbefa43c7234b0eb0ba80795990db7a <http://www.w3.org/2000/01/rdf-schema#label> "Computer game writer" .
_:N0acc5713c08d4c38aed277efff05a624 <http://www.w3.org/2000/01/rdf-schema#label> "(edtf) 2001-05-11" .
_:N7dd0d032d89d4dc58e623a5b21a353ef <http://www.w3.org/2000/01/rdf-schema#label> "(edtf) 1952-03-11" .
```

  though there are other labels too:

```
> grep -i label rwo_adams.xml 
    <rdfs:label>
            </rdfs:label>
    <rdfs:label>(edtf) 1952-03-11</rdfs:label>
    <rdfs:label>(edtf) 2001-05-11</rdfs:label>
        <rdfs:label>Digital Village (Firm)</rdfs:label>
        <madsrdf:authoritativeLabel>St. John's College (University of Cambridge)</madsrdf:authoritativeLabel>
    <madsrdf:authoritativeLabel>Cambridge (England)</madsrdf:authoritativeLabel>
    <madsrdf:authoritativeLabel>Santa Barbara (Calif.)</madsrdf:authoritativeLabel>
    <madsrdf:authoritativeLabel>Great Britain</madsrdf:authoritativeLabel>
    <madsrdf:authoritativeLabel>England</madsrdf:authoritativeLabel>
    <rdfs:label>(lcgft) Science fiction</rdfs:label>
    <rdfs:label>Computer game writer</rdfs:label>
    <madsrdf:authoritativeLabel>Authors</madsrdf:authoritativeLabel>
    <madsrdf:authoritativeLabel>Novelists</madsrdf:authoritativeLabel>
    <madsrdf:authoritativeLabel>Screenwriters</madsrdf:authoritativeLabel>
    <madsrdf:authoritativeLabel>Radio writers</madsrdf:authoritativeLabel>
    <madsrdf:authoritativeLabel>Males</madsrdf:authoritativeLabel>
```

  2. as before
  3. as before
