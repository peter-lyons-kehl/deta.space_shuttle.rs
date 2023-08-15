<!-- markdownlint-disable MD025 -->
<!-- markdownlint-disable MD013 -->
<!-- The following comment hides this section from being shown by
     https://peter-kehl.github.io/deta.space_shuttle.rs.
-->
<!-- .slide: data-visibility="hidden" -->
# Slides & alternative navigation

If you are seeing this, consider viewing [presentation slides
(online)](https://peter-kehl.github.io/deta.space_shuttle.rs) instead. Or see
[README-NAVIGATE-SLIDES.md
(online)](https://github.com/peter-kehl/present_markdown_reveal.js/blob/main/README-NAVIGATE-SLIDES.md)
for alternatives.

---

# Rust applications on Deta.Space & Shuttle.rs

Shuttle.rs and Deta.Space are platforms for development and deployment of cloud applications. They
both support [Rust](todo). Let's look at which one you may want to choose.

SpeakerNote: You can see the slides, along with a transcript, on GitHub. If you clone or download it
and follow a few steps in its README, you can also render the slides and transcript locally and
offline.

---

# Disclaimers

- This is __not__ an introduction to either platform. Their existing documentation is already
  awesome. <todo>
- Both are agile/work in progress. Especially, functionality can be ahead of the documentation. Join
  and shape it!
- Not fully comparable. They share or differ in some features, but other features or limitations are
  unique to one or the other.
- Most features considered here are __not__ Rust-specific. However, they affect which platform to
  choose based on/for
  - an existing application or system design, or
  - a new system design, or
  - integration with non-Rust components, or
  - portable or platform-specific API's/database/storage, or
  - sharing of applications
  - developer base & instance owner base
  - data isolation, or
  - limits/quotas and affected use cases, or
  - options for cloud providers, or private cloud, or
  - an (optional) commercial use.
- Updated in mid August 2023.

---

# Spoiler Alert

I love each. So exciting.

SpeakerNote: Welcome to a brief introduction & comparison of development & deploying Rust
applications on Shuttle.rs & Deta.Space platforms. You can also read these slides with more details,
which we skip here for brevity.

---

# Shared Qualities

- local development on Linux, Mac OS & Windows
- easy deployment on the respective platform's cloud, in a Docker-like Linux container
- the deployed application can be private (for the developer only), or public
- the developer can deploy an unpublished (test) version of a public application. Such a version is
  visible only to her/him.
- no way to run `su`/`sudo` (or not documented), nor to customize at Docker level (for example: to
  access remote storage by mounting FUSE file systems)
- support & community on their Discord servers

---

# Deta.Space Qualities

- mesh design
  - an application can consist of up to five "micros" (computes). Each can be developed in any of
    the supported languages.
  - suitable if we want to add access control on top of/in front of an existing/3rd party codebase
    (installed as a part of your application). We can create a "proxy" that performs access control.
    It then forwards the request to the other (existing/3rd party) application. That is deployed on
    another micro that is not public.
- Rust applications don't get special handling. Instead, Rust micros have "custom" type.
- no restrictions on Rust version
- No special Rust crates/macros or code, other than getting the basic configuration. That can make
  the code a little bit more portable. But then you couldn't store data with Deta.Base/Deta.Store
  (see below).
- [Rust bindings for Deta API](https://github.com/jnsougata/deta-rust-sdk) are only unofficial.
- Rust support is new. There are only a few examples of Rust applications so far. But they are growing!
- not with PostgreSQL/MySQL... unless you use a pool manager
- not for background/long tasks (specifically: not for Discord bots)
- database & storage provided by the platform is only through their own NoSQL (Deta.Base) and their
  own storage (Deta.Store) API. If you use those, the source code is not portable. (Unless you
  create traits or wrappers. Such abstractions are a part of good design. But they add complexity
  when creating them, and even more so when maintaining.)
- data isolation: if using Deta.Base or Deta.Store, this data is separate per instance owner
- data provisioning: automatic
- AWS billing? Or other/private clouds?
- subdomain anonymization promotes/suggests using each instance only by its owner. If the
  application is for public, the users can "fork" their own instances.
- `/tmp` (and seemingly `/dev/shm`, too)
- app marketplace & commercial model: We can share free applications. Deta.Space is also planning an
  option of applications to be paid and to generate revenue for the developer.

SpeakerNote: Before we compare these platforms feature by feature, let's look at features unique to
one and then the other. Hopefully then the comparison will make more sense.

---

## Shuttle.rs Qualities

- developer base is the same as the instance owner base
- not promoting/targeting sharing (clones) of applications. Of course, developers are free to
  publish their code (on GIT or similar) so that others could deploy it on Shuttle.rs, too.
- suitable for background/long tasks (for example: for Discord bots). "No cold-start and can even
  have long-running threads" - see [FAQ](https://docs.shuttle.rs/support/faq) > "How does this
  differ from using serverless framework with Rust Lambda and provided runtime?"
- A fixed Rust version (currently `1.70`). See [FAQ](https://docs.shuttle.rs/support/faq) > "Which
  version of Rust..."
- PostgreSQL and steps for its provisioning, migrations/updates
- any NoSQL, and if so, is it provisioned automatically?
- no `/tmp`; only `/dev/shm`
- commercial model: For users with more than 5? applications. But, if you become a Shuttle.rs hero,
  it's free for life!

---

## Quantitative differences

This also includes other technical differences, if they seem to be a part of the platform's
design/architecture.

|   | Shuttle.rs | Deta.Space |
| --- | --- | --- |
| application/"micro" size | unspecified | [250MB](https://deta.space/docs/en/build/quick-starts/custom) |
| execution timeout | unspecified | [20s](https://deta.space/docs/en/build/reference/runtime#technical-specifications-for-micros) |
| RAM per execution | unclear | [250MB](https://deta.space/docs/en/build/reference/runtime#technical-specifications-for-micros) |
| RAM per container | [6GB](https://docs.shuttle.rs/introduction/how-shuttle-works) (4GB during high contention - very rare, see [FAQ](https://docs.shuttle.rs/support/faq) > "What happens when I create a project?") | unclear |
| `/dev/shm` and/or `/tmp` | unspecified (64MB as per `df -m`) | [512MB](https://deta.space/docs/en/build/reference/runtime#technical-specifications-for-micros) |
| processes/threads | [4 threads per project](https://docs.shuttle.rs/introduction/how-shuttle-works) | [1024](https://deta.space/docs/en/build/reference/runtime#technical-specifications-for-micros) |
| HTTP payload | unspecified | [5.5MB](https://deta.space/docs/en/build/reference/runtime#technical-specifications-for-micros) |
| database and/or object storage | 10GB | unspecified |
| regions | [eu-west](https://github.com/shuttle-hq/shuttle-docs/issues/162) and more planned | unspecified |

SpeakerNote:

---
