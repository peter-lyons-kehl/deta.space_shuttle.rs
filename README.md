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

# Shared Features

- local development on Linux, Mac OS & Windows
- easy deployment on the respective platform's cloud, in a Docker-like Linux container
- no way to run `su`/`sudo` (or not documented), nor to customize at Docker level (for example: to
  access remote storage by mounting FUSE file systems)
- support & community on their Discord servers

---

# Deta.Space Features

- the deployed application can be private (for the developer only), or public ("published")
- even if the application is public, the developer can deploy an unpublished (test) version of that
  public application. Such a version is visible only to her/him
- a public (published) application can still have parts which are private
-  to access a private application, or private URLs of a public application, the appliction owner is
   authenticated by Deta.Space. (Deta authenticates the user through AWS, and it [doesn't have
   access to her/his password](https://deta.space/privacy).)
- mesh design
  - Mesh of micros: An application can consist of up to five
    ["micros"](https://deta.space/docs/en/build/fundamentals/the-space-runtime/micros) (computes).
    Each can be developed in any of the supported languages.
  - Suitable if we want to add access control on top of/in front of an existing/3rd party codebase
    (installed as a part of your application). We can create a "proxy" that performs access control.
    It then forwards the request to the other (existing/3rd party) application instance, deployed in
    another micro. (Such a micro would not be public.) We can proxy for example with
    [warp_reverse_proxy](https://docs.rs/warp-reverse-proxy).
  - Mesh of languages/frameworks: Each micro within the same application can use any of the
    supported languages/frameworks.
- Rust applications don't get special handling. Instead, Rust micros have "custom" type.
- No special Rust crates/macros or code, other than getting the basic configuration. That can make
  the code a little bit more portable/flexible. But then you couldn't store data with
  Deta.Base/Deta.Store (see below).
- [Rust bindings for Deta API](https://github.com/jnsougata/deta-rust-sdk) are only unofficial.
- [not with PostgreSQL/MySQL... unless you use a pool
  manager](https://deta.space/docs/en/build/reference/runtime#important-notes-for-micros)
- no restrictions on Rust version, nor on crate versions
- Rust support is new. There are only a few examples of Rust applications so far. But they are
  growing!
- [not for background/long
  tasks](https://deta.space/docs/en/build/reference/runtime#important-notes-for-micros)
  (specifically: not for Discord bots)
- database & storage provided by the platform is only through their own NoSQL (Deta.Base) and their
  own storage (Deta.Store) API. If you use those, the source code is not portable. (Unless you
  create traits or wrappers. Such abstractions are a part of good design. But they add complexity
  when creating them, and even more so when maintaining.)
- data isolation: if using Deta.Base or Deta.Store, this data is separate per instance owner, even
  if you clone someone else's published Deta application
- data provisioning: automatic
- `/tmp` (and seemingly `/dev/shm`, too)
- subdomain anonymization promotes/suggests using each instance only by its owner. If the
  application is for public, the users can "fork" their own instances.
- instance owner base is wider than "innovating"/hard core developer base. In other words, it's easy
  to have your own/separate deployment of an application that someone else published on Deta.Space,
  without developer skills.
- App Marketplace & commercial model: App Marketplace promotes sharing free applications. Deta.Space
  is also planning an option of applications to be paid so they would generate revenue for the
  developer.

SpeakerNote: Before we compare these platforms feature by feature, let's look at features unique to
one and then the other. Hopefully then the comparison will make more sense.

---

## Shuttle.rs Features

- suitable for background/long tasks (for example: for Discord bots). "No cold-start and can even
  have long-running threads" - see [FAQ](https://docs.shuttle.rs/support/faq) > "How does this
  differ from using serverless framework with Rust Lambda and provided runtime?"
- richer storage
  - wider variety
  - both public/free standards (portable) and proprietary (not portable)
  - RDS (SQL) and handling of migrations/updates
    - Postgres (either a [shared server](https://docs.shuttle.rs/resources/shuttle-shared-db), or a
      [dedicated instance](https://docs.shuttle.rs/resources/shuttle-aws-rds))
    - MySQL (a [dedicated instance](https://docs.shuttle.rs/resources/shuttle-aws-rds))
    - MariaDB (a [dedicated instance](https://docs.shuttle.rs/resources/shuttle-aws-rds))
    - [Turso](https://docs.shuttle.rs/resources/shuttle-turso) ([distributed
      SQLite](https://turso.tech) fork, [SQLite-compatible & with 1st class Rust
      support](https://turso.tech/pricing)). This is currently NOT hosted by Shuttle.rs, but it may
      be so in the future. Either way, it has a [dedicated crate from
      Shuttle](https://docs.shuttle.rs/resources/shuttle-turso).
  - noSQL: [MongoDB through a shared database](https://docs.shuttle.rs/resources/shuttle-shared-db)
  - key/value: proprietary [Shuttle Persist](https://docs.shuttle.rs/resources/shuttle-persist)
- data isolation
- A fixed Rust version (currently `1.70`). See [FAQ](https://docs.shuttle.rs/support/faq) > "Which
  version of Rust...". Similarly, [Turso is pinned to version
  `0.30.1`](https://docs.shuttle.rs/resources/shuttle-turso).
- no `/tmp`; only `/dev/shm`
- not promoting/targeting sharing (clones) of applications. Of course, developers are free to
  publish their code (on GIT or similar) so that others could deploy it on Shuttle.rs, too.
- instance owner base is the same as "innovating" developer base. In other words, if you want your
  own/separate deployment of an application that someone else published (on GIT...), you need more
  developer skills than with Deta.Space.
- commercial model: For users with more than 5? applications. But, the limits are not enforced yet.
  And, if you [become a Shuttle.rs hero](https://www.shuttle.rs/shuttle-heroes), it's free for life!
- use your [own AWS account](https://www.shuttle.rs/beta) 

---

## Quantitative differences

This also includes other technical differences, if they seem to be a part of the platform's
design/architecture.

| Property/Limit  | Shuttle.rs | Deta.Space |
| --- | --- | --- |
| application/"micro" size | unspecified | [250MB](https://deta.space/docs/en/build/quick-starts/custom) per micro |
| execution timeout | unspecified | [20s](https://deta.space/docs/en/build/reference/runtime#technical-specifications-for-micros) |
| RAM per execution | unclear | [250MB](https://deta.space/docs/en/build/reference/runtime#technical-specifications-for-micros) |
| RAM per container | [6GB](https://docs.shuttle.rs/introduction/how-shuttle-works) (4GB during high contention: very rare, see [FAQ](https://docs.shuttle.rs/support/faq) > "What happens when I create a project?") | unclear |
| `/dev/shm` and/or `/tmp` | unspecified ([64MB as per `df -m`](https://sys-info.shuttleapp.rs/)) | [512MB](https://deta.space/docs/en/build/reference/runtime#technical-specifications-for-micros) |
| processes/threads | [4 threads per project](https://docs.shuttle.rs/introduction/how-shuttle-works) | [1024](https://deta.space/docs/en/build/reference/runtime#technical-specifications-for-micros) |
| HTTP payload | unspecified | [5.5MB](https://deta.space/docs/en/build/reference/runtime#technical-specifications-for-micros) |
| database and/or object storage | [10GB on free tier](https://docs.shuttle.rs/introduction/how-shuttle-works#project-limitations), but not enforced yet. (Plus, free for life if you become a hero.) | unspecified (but personal use is free for life) |
| clouds/regions | AWS [eu-west](https://github.com/shuttle-hq/shuttle-docs/issues/162) and more planned. (See [FAQ](https://docs.shuttle.rs/support/faq) > Do we plan to support multiple regions?) | [AWS](https://deta.space/privacy) and potentially planning for [GCP and other clouds](https://jobs.deta.space/?ashby_jid=739b845d-17ff-475c-b05f-649801e919ad) |

SpeakerNote:

---

