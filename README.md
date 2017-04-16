# Trust Graph JS Command Line Interface

Latest build: [![Build Status](https://travis-ci.org/trustgraph/js-trustgraph-cli.svg?branch=master)](https://travis-ci.org/trustgraph/js-trustgraph-cli)

## Usage

### Create Claims

```
trust claim --help

Usage: trust-claim [options]

  Options:

    -h, --help                   output usage information
    --creator <creator>          DID or URL of claim creator
    --target <target>            DID or URL of claim target
    --description <description>  Rating description
    --tags <tag1, tag2>          Rating tags
    --value <value>              Rating weight in the range 0..1
    --algorithm <algorithm>      Signing algorithm
    --private-key <key>          Private key
```

For example:

```
trust claim \
  --creator did:00a65b11-593c-4a46-bf64-8b83f3ef698f \
  --target did:59f269a0-0847-4f00-8c4c-26d84e6714c4 \
  --description 'Elixir programming' \
  --value 0.99 \
  --tags 'programming, Elixir' \
  --algorithm EcdsaKoblitzSignature2016 \
  --private-key L4mEi7eEdTNNFQEWaa7JhUKAbtHdVvByGAqvpJKC53mfiqunjBjw
```

Creates the following signed JSON, in the [JSON-LD Verifiable Claim](https://opencreds.github.io/vc-data-model/#expressing-entity-credentials-in-json) format.

```json
{
    "@context": "https://schema.trustgraph.io/TrustClaim.jsonld",
    "type": "TrustClaim",
    "issuer": "did:00a65b11-593c-4a46-bf64-8b83f3ef698f",
    "issued": "2017-03-04T02:05:07-08:00",
    "claim": {
        "@context": "https://schema.org/",
        "type": "Review",
        "itemReviewed": "did:59f269a0-0847-4f00-8c4c-26d84e6714c4",
        "author": "did:00a65b11-593c-4a46-bf64-8b83f3ef698f",
        "keywords": "programming, Elixir",
        "reviewRating": {
            "@context": "https://schema.org/",
            "type": "Rating",
            "bestRating": 1,
            "worstRating": 0,
            "ratingValue": "0.99",
            "description": "Elixir programming"
        }
    },
    "signature": {
        "type": "sec:EcdsaKoblitzSignature2016",
        "http://purl.org/dc/terms/created": {
            "type": "http://www.w3.org/2001/XMLSchema#dateTime",
            "@value": "2017-03-04T10:05:07Z"
        },
        "http://purl.org/dc/terms/creator": {
            "id": "EcdsaKoblitz-public-key:020d79074ef137d4f338c2e6bef2a49c618109eccf1cd01ccc3286634789baef4b"
        },
        "sec:domain": "example.com",
        "signature:Value": "IEd/NpCGX7cRe4wc1xh3o4X/y37pY4tOdt8WbYnaGw/Gbr2Oz7GqtkbYE8dxfxjFFYCrISPJGbBNFyaiVBAb6bs="
    }
}
```

We canonicalize the JSON, by minifying and sorting hashes by keys:

```json
{"@context":"https://schema.trustgraph.io/TrustClaim.jsonld","claim":{"@context":"https://schema.org/","author":"did:00a65b11-593c-4a46-bf64-8b83f3ef698f","itemReviewed":"did:59f269a0-0847-4f00-8c4c-26d84e6714c4","keywords":"programming, Elixir","reviewRating":{"@context":"https://schema.org/","bestRating":1,"description":"Elixir programming","ratingValue":"0.99","type":"Rating","worstRating":0},"type":"Review"},"issued":"2017-03-04T02:05:07-08:00","issuer":"did:00a65b11-593c-4a46-bf64-8b83f3ef698f","signature":{"http://purl.org/dc/terms/created":{"@value":"2017-03-04T10:05:07Z","type":"http://www.w3.org/2001/XMLSchema#dateTime"},"http://purl.org/dc/terms/creator":{"id":"EcdsaKoblitz-public-key:020d79074ef137d4f338c2e6bef2a49c618109eccf1cd01ccc3286634789baef4b"},"sec:domain":"example.com","signature:Value":"IEd/NpCGX7cRe4wc1xh3o4X/y37pY4tOdt8WbYnaGw/Gbr2Oz7GqtkbYE8dxfxjFFYCrISPJGbBNFyaiVBAb6bs=","type":"sec:EcdsaKoblitzSignature2016"},"type":"TrustClaim"}
```

Then hash the canonical JSON to get an ID for the claim:

```
QmbVYv7Zih44uJ8MAcpxQ3TZGKUscNoYyK6UKUaut6jK77  # sha2-256 multihash
```

### Retrieve Claims

Retrieve and analyze a collection of ratings.

***NOTE: not yet implemented!***
Below is an API sketch; please submit any
comments or requests as Github issues.

```
trust map --help

Usage: trust-map [options]

  Options:

    -h, --help           output usage information
    --perspective <DID>  Perspective (identity) through which trust network is seen
    --creator <creator>  DID or URL of claim creator
    --target <target>    DID or URI of claim target
    --tags <tag1, tag2>  Filter by tags
    --depth <levels>     Crawls trust ratings to specified depth
    --min-value <value>  Min trust rating 0..1
    --max-value <value>  Max trust rating 0..1
    --summarize          Summarize claims / build analysis
    --falloff            Trust level relative to depth, eg: [1, 0.5, 0.33]
```

For example:

```
trust map \
  --target did:59f269a0-0847-4f00-8c4c-26d84e6714c4 \
  --tags 'programming, Elixir' \
```
