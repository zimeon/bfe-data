# Authority Term Fetch

Exploring implications of the specification:

> GET => Fetch:
> * In (payload or param): URI
> * 200 Out (payload, preference JSON): All RDF statements with URI as subject in External Store, plus triples that provide an  `rdfs:label` property for the object values.

Start off with doing a current fetch from `lookup.ld4l.org` for the authority for Douglas Adams (this is based on the authority URI and not the RWO URI because the LC dump data doesn't yet include the RWO data...):

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
> wc -l adams_fetch.nt
103 adams_fetch.nt
```

So, this graph has 103 triples at present. We can extract the portion that conforms with the one-level-plus-labels specification using SPARQL:

```
> spahqler.py --graph adams_fetch.nt --query "CONSTRUCT { ?term ?p ?o . ?o rdfs:label ?o2 . } WHERE { ?term ?p ?o . OPTIONAL { ?o rdfs:label ?o2 . } }" --binding 'term=<http://id.loc.gov/authorities/names/n80076765>' > adams_one_level.nt

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