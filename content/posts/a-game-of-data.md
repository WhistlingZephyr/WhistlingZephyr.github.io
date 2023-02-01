---
author: "WhistlingZephyr"
date: 2023-02-01T12:39:18+05:30
title: "Into the Rabbit Hole: A Game of Data"
tags:
  - programming
---

## Introduction

In this post, we'll find a way to list all countries, states, and capital cities
starting and ending with the same letter, where the letter can't be "a", using
[Wikidata](https://www.wikidata.org/wiki/Wikidata:Main_Page) and [SPARQL](https://en.wikipedia.org/wiki/SPARQL).

## Background

So one day <small>(2022-Dec-14 to be specific)</small> I was hanging out on Discord in the [Rust Programming Language Community Server](https://discord.gg/rust-lang-community), and [Dylan](https://github.com/Dylan-DPC) there gave me a fun challenge to solve: He said that he was trying to name all important places <small>(such as countries, states, and major cities)</small> that start and end with the same letter that isn't "A" <small>(because there are quite bunch that start and end with "A")</small>. Well, I raised the concern that differentiating between major and non-major cities is likely going to be difficult, so I ended up ended up considering only capital cities in its place. I determined that I needed a way to get a list of all the world's countries, states, and capital cities first; and right off the bat, Wikidata seemed like the best place to start. That day, I learnt a bit of SPARQL and jotted down a basic [query](https://w.wiki/678J) to find the solution. Although, that solution wasn't perfect; so let's try coming up with something better this time.

> Note: I'm aware there are alternative ways to approach this problem; however, I'll be using Wikidata's Query Service with SPARQL to solve this rather than approaching it in a different way, for example using the graphical query builder to get a list of places and then filtering through in with a different language.

## Before we start

- **Wikidata** is a knowledge database. It contains millions of statements, such as “the capital of Canada is Ottawa”, or “the Mona Lisa is painted in oil paint on poplar wood”, or “gold has a melting point of 1,064.18 degrees Celsius”.
- **SPARQL** is a language to formulate questions <small>(queries)</small> for knowledge databases. With the right database, a SPARQL query could answer questions like “what is the most popular tonality in music?” or “which character was portrayed by the most actors?” or “what’s the distribution of blood types?” or “which authors’ works entered the public domain this year?”. The acronym "SPARQL" stands for "SPARQL Protocol And RDF Query Language".
- **RDF** stands for "Resource Description Framework", it's used as a general method for describing and exchanging graph data, and it's what SPARQL queries.
- **WDQS**, the Wikidata Query Service, brings the two together: You enter a SPARQL query, it runs it against Wikidata’s dataset and shows you the result.

## Digging up

We can query Wikidata with SPARQL at <https://query.wikidata.org/>. A simple SPARQL query looks like this:

```SPARQL
SELECT ?a ?b ?c WHERE {
  x y ?a.
  m n ?b.
  ?b f ?c.
}
```

The `SELECT` clause is what you want to output and the `WHERE` clause contains constraints to filter said output with. Variables start with `?`, they're placeholders that you can tie constraints to. Inside the `WHERE` clause are "triples". Triples are how RDF stores relations. A triple is like a sentence: it has a _subject_, _predicate_, and _object_; terminated by a period. The predicate here is a property the subject has, and the subject and object themselves are considered items.

```SPARQL
SELECT ?fruit WHERE {
  ?fruit color yellow.
  ?fruit taste sour.
}
```

On Wikidata, items and properties aren't identified by human-readable names, instead they're assigned to an identifier. To find the identifier for an item or property, we [search](https://www.wikidata.org/wiki/Special:Search) for the item's Q-number with "search term" or the property's P-number with "P:search term".

We need to also include prefixes in our non-variable identifiers. In this case, we use the `wd:` prefix for items and `wdt:` prefix for properties. There are more prefixes listed ([here](https://www.wikidata.org/wiki/EntitySchema:E49)), but we won’t be needing those for this post.

Let's find a list of all countries. We can start by searching an example country to know what we're dealing with. I'll go with Germany. Searching "Germany" in WikiData tells us that its identifier is [Q183](https://www.wikidata.org/wiki/Q183), and there it's "instance of" a "country", in which "instance of" and "country" are [P31](https://www.wikidata.org/wiki/Property:P31) and [Q6256](https://www.wikidata.org/wiki/Q6256) respectfully. Let's also limit our query to the first 100 results, we can use a `LIMIT` clause for that with a number. Last but not least, we need a labeling service, because by default Wikidata only shows the identifiers of result items. For each variable `?foo`, we can add its label `?fooLabel` by including `SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }` in our `WHERE` clause. So at the end, our query for getting a list of countries ends up looking like this:

```SPARQL
SELECT ?countryLabel WHERE {
  ?country wdt:P31 wd:Q6256
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
LIMIT 100
```

The [result](https://w.wiki/6HC4) shows a list of 100 countries as we desired. Let's make it show the starting character of each country alongside. We can use `BIND(expression AS ?variable)` to define a variable from an expression and `SUBSTR(string, beginPosition, stringLength)` to get a substring of a string; the position index starts at 1.

```SPARQL
SELECT ?countryLabel ?countryStart WHERE {
  ?country wdt:P31 wd:Q6256.
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
  BIND(SUBSTR(?countryLabel, 1, 1) AS ?countryStart).
}
LIMIT 100
```

Running that query, we see that the [results](https://w.wiki/6HE4) don't actually show anything in the `countryStart` row. It's because even though we learned to use the `wikibase:label` service for fetching labels of our identifiers, we can't actually access the labels inside our `WHERE` clause yet. We can fetch labels manually using the `rdfs:label` property.

```SPARQL
SELECT ?countryLabel ?countryStart WHERE {
  ?country wdt:P31 wd:Q6256.
  ?country rdfs:label ?countryLabel
  BIND(SUBSTR(?countryLabel, 1, 1) AS ?countryStart).
}
LIMIT 100
```

However, in the [result](https://w.wiki/6HE5) we see that we get outputs from other languages as well. To fix that, we can use the `FILTER` function combined with the `LANG` function, and using `=` as the comparison operator.

```SPARQL
SELECT ?countryLabel ?countryStart WHERE {
  ?country wdt:P31 wd:Q6256.
  ?country rdfs:label ?countryLabel.
  FILTER(LANG(?countryLabel) = "en").
  BIND(SUBSTR(?countryLabel, 1, 1) AS ?countryStart).
}
LIMIT 100
```

The [result](https://w.wiki/6HE7) produces the expected output. We can make this query a bit more concise.

```SPARQL
SELECT ?countryLabel ?countryStart WHERE {
  ?country wdt:P31 wd:Q6256;
           rdfs:label ?countryLabel.
  FILTER(LANG(?countryLabel) = "en").
  BIND(SUBSTR(?countryLabel, 1, 1) AS ?countryStart).
}
LIMIT 100
```

in SPARQL, `a b c. a d e.` is the same as `a b c; d e.`. Now since we'll be comparing the starting character with the ending character, we can lowercase it using the `LCASE` function. And to get the last character of a string, we'll need the position of its last character, which is its length; we can get that with the `STRLEN` function. And we can remove our limit now, since there aren't many countries that match our requirement.

```SPARQL
SELECT ?countryLabel WHERE {
  ?country wdt:P31 wd:Q6256;
           rdfs:label ?countryLabel.
  FILTER(LANG(?countryLabel) = "en").
  BIND(LCASE(SUBSTR(?countryLabel, 1, 1)) AS ?countryStart).
  BIND(SUBSTR(?countryLabel, STRLEN(?countryLabel), 1) AS ?countryEnd).
  FILTER(?countryStart = ?countryEnd).
}
```

The [result](https://w.wiki/6HLF) gives a list of countries starting and ending with the same letter like Austria, Angola, etc. Let's filter out those that start and end with "a", because there are too many. We can do that by adding `STR(?countryStart) != "a" &&` in our `FILTER` function call. Why are we calling `STR`? because our variable `?countryLabel` and consequently `?countryStart` and `?countryEnd` are more than just strings and contain language metadata; so to compare with a plain string, we must convert it to one first.

```SPARQL
SELECT ?countryLabel WHERE {
  ?country wdt:P31 wd:Q6256;
           rdfs:label ?countryLabel.
  FILTER(LANG(?countryLabel) = "en").
  BIND(LCASE(SUBSTR(?countryLabel, 1, 1)) AS ?countryStart).
  BIND(SUBSTR(?countryLabel, STRLEN(?countryLabel), 1) AS ?countryEnd).
  FILTER(STR(?countryStart) != "a" && ?countryStart = ?countryEnd).
}
```

From the [results](https://w.wiki/6HLG), it appears that there aren't many countries that fit our requirement especially when we're limiting our search to their English names only. Now, we can do the same for states, which is [Q7275](https://www.wikidata.org/wiki/Q7275) and capital cities, which is [Q5119](https://www.wikidata.org/wiki/Q5119). We'll merge our selectors together with the `UNION` clause to avoid repetition. Let's also label which entry is what type.

```SPARQL
SELECT ?label ?type WHERE {
  {
    ?country wdt:P31    wd:Q6256;
             rdfs:label ?label.
    BIND("country" AS ?type).
  } UNION {
    ?state   wdt:P31    wd:Q7275;
             rdfs:label ?label.
    BIND("state" AS ?type).
  } UNION {
    ?capital wdt:P31    wd:Q5119;
             rdfs:label ?label.
    BIND("capital" AS ?type).
  }
  FILTER(LANG(?label) = "en").
  BIND(LCASE(SUBSTR(?label, 1, 1)) AS ?labelStart).
  BIND(SUBSTR(?label, STRLEN(?label), 1) AS ?labelEnd).
  FILTER(STR(?labelStart) != "a" && ?labelStart = ?labelEnd).
}
```

The [results](https://w.wiki/6HLK) match our requirements. However, there are entires missing, "Ohio" for example. We search it up and see that [Q1397](https://www.wikidata.org/wiki/Q1397) Ohio is an instance of [Q35657](https://www.wikidata.org/wiki/Q35657) U.S. state which is a subclass of [Q106458883](https://www.wikidata.org/wiki/Q106458883) state. We on the other hand, used [Q7275](https://www.wikidata.org/wiki/Q7275) state which appears to be different. Looking into a category that includes all our desired places such as states and provinces, we find that [Q107390](https://www.wikidata.org/wiki/Q107390) federated state is what we're looking for; it also states that it's equivalent to <https://schema.org/State>.

```SPARQL
SELECT ?label ?type WHERE {
  {
    ?country wdt:P31    wd:Q6256;
             rdfs:label ?label.
    BIND("country" AS ?type).
  } UNION {
    ?state   wdt:P31    wd:Q107390;
             rdfs:label ?label.
    BIND("state" AS ?type).
  } UNION {
    ?capital wdt:P31    wd:Q5119;
             rdfs:label ?label.
    BIND("capital" AS ?type).
  }
  FILTER(LANG(?label) = "en").
  BIND(LCASE(SUBSTR(?label, 1, 1)) AS ?labelStart).
  BIND(SUBSTR(?label, STRLEN(?label), 1) AS ?labelEnd).
  FILTER(STR(?labelStart) != "a" && ?labelStart = ?labelEnd).
}
```

The [result](https://w.wiki/6HLL) still doesn't have "Ohio", because [Q1397](https://www.wikidata.org/wiki/Q1397) Ohio isn't directly an instance of [Q107390](https://www.wikidata.org/wiki/Q107390) federated state but rather an instance of [Q35657](https://www.wikidata.org/wiki/Q35657) U.S. state which is a subclass of federated state. To solve it, we use `wdt:P31/wdt:P279*` as the property which says that there’s one “instance of” and then any number of “subclass of” statements between the item and the class. Also, we can `SELECT DISTINCT` instead of just `SELECT` here to avoid duplicate entires.

```SPARQL
SELECT DISTINCT ?label ?type WHERE {
  {
    ?country wdt:P31    wd:Q6256;
             rdfs:label ?label.
    BIND("country" AS ?type).
  } UNION {
    ?state   wdt:P31/wdt:P279* wd:Q107390;
             rdfs:label        ?label.
    BIND("state" AS ?type).
  } UNION {
    ?capital wdt:P31    wd:Q5119;
             rdfs:label ?label.
    BIND("capital" AS ?type).
  }
  FILTER(LANG(?label) = "en").
  BIND(LCASE(SUBSTR(?label, 1, 1)) AS ?labelStart).
  BIND(SUBSTR(?label, STRLEN(?label), 1) AS ?labelEnd).
  FILTER(STR(?labelStart) != "a" && ?labelStart = ?labelEnd).
}
```

And that's it! Our [result](https://w.wiki/6HLM) finally includes what seems to be all the matches. As of 2023-Jan-31 though, the result includes a place named just "Free City of" <small>([Q97940833](https://www.wikidata.org/wiki/Q97940833)</small>); it appears to be an error in Wikidata's data on that page, hopefully it'll get addressed soon.

## Outcome

<table><thead><tr><th>label</th><th>type</th></tr></thead><tbody><tr><td>St. George's</td><td>capital</td></tr><tr><td>Warsaw</td><td>capital</td></tr><tr><td>Oslo</td><td>capital</td></tr><tr><td>Tashkent</td><td>capital</td></tr><tr><td>Kralendijk</td><td>capital</td></tr><tr><td>Kirkuk</td><td>capital</td></tr><tr><td>Central African Republic</td><td>country</td></tr><tr><td>Sardis</td><td>capital</td></tr><tr><td>Czech Republic</td><td>country</td></tr><tr><td>Qashliq</td><td>capital</td></tr><tr><td>St. John's</td><td>capital</td></tr><tr><td>Seychelles</td><td>country</td></tr><tr><td>Kingdom of Denmark</td><td>country</td></tr><tr><td>Solomon Islands</td><td>country</td></tr><tr><td>Saint Vincent and the Grenadines</td><td>country</td></tr><tr><td>Saint Kitts and Nevis</td><td>country</td></tr><tr><td>Nordhausen</td><td>state</td></tr><tr><td>State of Damascus</td><td>state</td></tr><tr><td>Electoral Palatinate</td><td>state</td></tr><tr><td>Free City of</td><td>state</td></tr><tr><td>Yaracuy</td><td>state</td></tr><tr><td>Himachal Pradesh</td><td>state</td></tr><tr><td>Nuevo León</td><td>state</td></tr><tr><td>Ekiti State</td><td>state</td></tr><tr><td>Ebonyi State</td><td>state</td></tr><tr><td>Chinese Soviet Republic</td><td>state</td></tr><tr><td>Edo State</td><td>state</td></tr><tr><td>Enugu State</td><td>state</td></tr><tr><td>East Central State</td><td>state</td></tr><tr><td>Eastern Lakes State</td><td>state</td></tr><tr><td>Eastern Nile State</td><td>state</td></tr><tr><td>Landgraviate of Hesse-Kassel</td><td>state</td></tr><tr><td>Mizoram</td><td>state</td></tr><tr><td>Ohio</td><td>state</td></tr><tr><td>Ontario</td><td>state</td></tr><tr><td>Negeri Sembilan</td><td>state</td></tr><tr><td>Hohenzollern-Haigerloch</td><td>state</td></tr><tr><td>Grand Duchy of Würzburg</td><td>state</td></tr><tr><td>Spanish Netherlands</td><td>state</td></tr><tr><td>Nassau-Siegen</td><td>state</td></tr><tr><td>Saxe-Weissenfels</td><td>state</td></tr><tr><td>North Kordofan</td><td>state</td></tr><tr><td>Chechen Republic</td><td>state</td></tr><tr><td>Electorate of Cologne</td><td>state</td></tr><tr><td>Nidwalden</td><td>state</td></tr><tr><td>Electorate of Hesse</td><td>state</td></tr><tr><td>State of Honduras</td><td>state</td></tr><tr><td>Grand Duchy of Berg</td><td>state</td></tr><tr><td>Chuvash Republic</td><td>state</td></tr></tbody></table>

## Wrapping up

So that was it, we've completed our task. It's only the tip of the iceberg though, Wikidata and SPARQL are powerful tools that can be used together to query data related to just about anything. I hope this post gets you interested in tinkering around with it yourself. To me, programming is still just as exhilarating as the day I wrote my first hello world back in 2017.

## Going ahead

The query can still be improved in some ways, for example:

- Using short names instead of full titles <small>(e.g. Electorate of Cologne -> Cologne)</small> <small>(Note: this would be rather complex due to WikiData not providing a field for shorter names)</small>.
- Including names in other languages <small>(e.g. Deutschland)</small>.
- Removing places that existed in the past and no longer fit the criteria now or are renamed <small>(e.g. Hohenzollern-Haigerloch)</small>.

And if you want to learn more about SPARQL, you can use the following resources:

- <https://www.wikidata.org/wiki/Wikidata:SPARQL_tutorial>
- <https://en.wikibooks.org/wiki/SPARQL>
- <https://graphdb.ontotext.com/documentation/free/sparql-functions-reference.html>
- <https://github.com/andrecastro0o/SPARQL-cheatsheet>
- <https://www.wikidata.org/wiki/Wikidata:SPARQL_query_service>
- <https://mediawiki.org/wiki/Wikidata_Query_Service/User_Manual>

## Acknowledgements

Thanks to [@Dylan-DPC](https://github.com/Dylan-DPC) for coming up with the idea and reviewing the post, and to [@Monadic-Cat](https://github.com/Monadic-Cat) and [@Evrey](https://github.com/Evrey/) for also reviewing the post.
