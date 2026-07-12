# s3-headers-lite

[![CI](https://github.com/softwarepatterns-com/s3-headers-lite/actions/workflows/ci.yml/badge.svg)](https://github.com/softwarepatterns-com/s3-headers-lite/actions/workflows/ci.yml)
[![crates.io](https://img.shields.io/crates/v/s3-headers-lite.svg)](https://crates.io/crates/s3-headers-lite)
[![docs.rs](https://docs.rs/s3-headers-lite/badge.svg)](https://docs.rs/s3-headers-lite)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](https://github.com/softwarepatterns-com/s3-headers-lite/blob/main/LICENSE)

A minimal, lighter-weight alternative to the official AWS crates. Zero `aws-*` dependencies. 

Only generates the headers necessary for communicating with S3-like services, so use any HTTP client you want. Examples use reqwest, but any HTTP client will work.

## Get

```rust
use s3_headers_lite::{S3HeadersBuilder, ValidS3Url};

let url = ValidS3Url::parse("https://example.s3.amazonaws.com/test/test.json").unwrap();

let headers = S3HeadersBuilder::new(&url)
  .set_access_key(&access_key)
  .set_secret_key(&secret_key)
  .set_region(&region)
  .set_method("GET")
  .build();

let response = reqwest::blocking::Client::new()
  .get(url.as_url().clone())
  .headers(reqwest::header::HeaderMap::from_iter(headers.into_iter().map(
    |(k, v)| {
      (
        reqwest::header::HeaderName::from_str(k).unwrap(),
        reqwest::header::HeaderValue::from_str(&v).unwrap(),
      )
    },
  )))
  .send()
  .unwrap();
```

## Put

```rust
use s3_headers_lite::{S3HeadersBuilder, ValidS3Url};

let url = ValidS3Url::parse("https://example.s3.amazonaws.com/test/test.json").unwrap();
let content = "{\"c\":\"d\"}\n".as_bytes().to_vec();

let headers = S3HeadersBuilder::new(&url)
  .set_access_key(&access_key)
  .set_secret_key(&secret_key)
  .set_region(&region)
  .set_method("PUT")
  .set_payload_hash_with_content(&content)
  .build();

let response = reqwest::blocking::Client::new()
  .put(url.as_url().clone())
  .headers(reqwest::header::HeaderMap::from_iter(headers.into_iter().map(
    |(k, v)| {
      (
        reqwest::header::HeaderName::from_str(k).unwrap(),
        reqwest::header::HeaderValue::from_str(&v).unwrap(),
      )
    },
  )))
  .send()
  .unwrap();
```

## List

```rust
use s3_headers_lite::{S3HeadersBuilder, ValidS3Url};

let url = ValidS3Url::parse("https://example.s3.amazonaws.com/").unwrap();

let headers = S3HeadersBuilder::new(&url)
  .set_access_key(&access_key)
  .set_secret_key(&secret_key)
  .set_region(&region)
  .set_method("GET")
  .build();

let response = reqwest::blocking::Client::new()
  .get(url.as_url().clone())
  .headers(reqwest::header::HeaderMap::from_iter(headers.into_iter().map(
    |(k, v)| {
      (
        reqwest::header::HeaderName::from_str(k).unwrap(),
        reqwest::header::HeaderValue::from_str(&v).unwrap(),
      )
    },
  )))
  .send()
  .unwrap();
```
