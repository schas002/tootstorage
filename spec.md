# TootStorage

A proof of concept for a Mastodon-powered file system

Specification

Version: 0.0.0

Authored by Andrew Zyabin

* * *

## Disclaimer

TootStorage is a **PROOF OF CONCEPT** provisioned to you **AS IS**, **WITHOUT ANY WARRANTY**, without even the implied warranty of **MERCHANTABILITY** or **FITNESS FOR A PARTICULAR PURPOSE**.

I, Andrew Zyabin, take **NO RESPONSIBILITY** if TootStorage gets you suspended or silenced or killed or your children die or whatever. This is a **PROOF OF CONCEPT** you use **AT YOUR OWN RISK**, much like I make something at my own risk.

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** in this document are to be interpreted as described in [RFC 2119](https://tools.ietf.org/rfc/rfc2119.txt).

## Files and sections

A **file** is an entity encodeable in a file system, in this case in one or more Mastodon Notes (henceforth called by their Mastodon term **toots**).

A file, by default, can only fit in 500 characters, the length of one toot. (Instances may set longer limits as to toot length.) Files longer than that **MUST** be divided into **sections**, and transmitted in a **thread** of toots, each later section replying to the earlier one.

## File formats

All TootStorage files are posted as one or more toots. All of them **MUST** be hidden behind a content warning (henceforth a **CW**) that is then recognized by a TootStorage client which manages sending and receiving the files.

All file toots are divided into one or more **parts**, each divided by a colon. There **MUST NOT** be a leading or trailing colon, unless the part is existent *and* empty.

### Singular section file

**CW**: TootStorage file v1

*privacy data*:*sha256sum*:*mode*:*data*

- If the toot is directed, *privacy data* is an existent part which **MAY** contain one or more pings (mentions) of users you want to be able to access the file. Otherwise, *privacy data* is nonexistent.
- *sha256sum* is always existent, and **MUST** contain (you guessed it) a hexadecimal [SHA-256 cryptographic hash](https://tools.ietf.org/rfc/rfc6234.txt) of the file encoded.
- *mode* is one of:
  - `0`. In this case, *data* is the raw file. This is **NOT RECOMMENDED** to be used, for various reasons including encodeability (only printable characters other than the colon are allowed).
  - `1`. In this case, *data* is the Base64 encoding of the file.

### Partial file, first section

**CW**: TootStorage partial file v1

*privacy data*:*sha256sum*:*mode*:*data*

- *privacy data*, *sha256sum*, *mode* and *data* act in the same way as they do in a singular section file. In this case, however, *sha256sum* **MUST** be the checksum of the *full* file.

### Partial file, continuation

**CW**: TootStorage partial file v1

*privacy data*:*mode*:*data*

- Again, *privacy data*, *mode* and *data* act in the same way as they do in a singular section file or in its first section.
- A toot which contains a continuation section **MUST** be a reply to either a first section or another continuation.
- A new *mode* is defined:
  - `e`. In this case, *data* is ignored, and this section is the final section of a file.
