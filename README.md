<!-- The following comment hides this section from being shown by
     https://peter-kehl.github.io/no_std_libs.
-->
<!-- .slide: data-visibility="hidden" -->
# Slides & alternative navigation

If you are seeing this, consider viewing [presentation slides
(online)](https://peter-kehl.github.io/no_std_libs) instead. Or see [README-NAVIGATE-SLIDES.md
(online)](https://github.com/peter-kehl/present_markdown_reveal.js/blob/main/README-NAVIGATE-SLIDES.md)
for alternatives.

<https://links>

---

## Rust applications on Deta.Space & Shuttle.rs

Shuttle.rs and Deta.Space are platforms for development and deployment of cloud applications. They
both support [Rust](todo). Let's look at which you may want to choose.

SpeakerNote: You can see the slides, along with a transcript, on GitHub. If you clone or download it and follow a few steps in its README, you can also render the slides and transcript locally and offline.

---

### Disclaimers

- This is __not__ an introduction to either platform. Their existing documentation is already awesome. <todo>
- Both are agile/work in progress. Especially, sometimes functionality is ahead of the documentation.
- Not fully comparable. They share some features, but other features are unique to one or the other.
- Most features considered here are __not__ Rust-specific. However, they affect which platform to choose based on/for
-  an existing application or system design, or
-  a new system design, or
-  integration with non-Rust components, or
-  portability or use of specific API's/database/storage, or
-  sharing of applications
- developer base & instance owner base
-  data isolation, or
-  limits/quotas and affected use cases, or
-  options for cloud providers, or private cloud, or
-  an (optional) commercial use.

### Spoiler Alert

I love each of them. So exciting.

SpeakerNote: Welcome to a brief introduction & comparison of development & deploying Rust
applications on Shuttle.rs & Deta.Space platforms. You can also read these slides with more
details, which we skip here for brevity.

---

## Shared features

- local development on Linux, Mac OS & Windows
- deployment on the platform cloud, in a Docker-like Linux container
- deployed application can be private (for the developer only), or public
- the developer can deploy an unpublished (test) version of a public application. Such a version is visible only to her/him.
- no (or no documented) way to run `su`/`sudo`, or to customize at Docker level (for example to access remote storage by mounting FUSE file systems).

---

## Unique to Deta.Space

- mesh design
-  an application can consist of up to five computes. Each can be developed in any of the supported languages.
-  suitable if we want to add access control on top of/in front of an existing/3rd party codebase
   (installed as a part of our application). We can create a "proxy" that performs access control.
   It then forwards the request to the other (existing/3rd party) application. That is deployed on another
   compute that is not public.
- Rust applications don't get special handling. Instead, Rust computes have "custom" type.
- No special Rust crates/macros or code, other than getting the basic configuration. That can make the code more portable outside of Deta.Space. (Unless it uses Deta.Base/Deta.Store and their automatic provisioning and isolation.)
- Rust support is new. There are only a few examples for Rust applications.
- not with PostgreSQL or similar.
- not for background/long tasks (specifically: not for Discord bots)
- no
- database & storage provided by the platform only through their own NoSQL (Deta.Base) and their own storage (Deta.Store). So, the source code is not portable.
- data isolation: If using Deta.Base or Deta.Store, this data is isolated per instance owner.
- AWS billing? Or other/private clouds?

SpeakerNote: Before we compare these platforms feature by feature, let's look at features unique to
one and then the other. Hopefully then the comparison will make more sense.

---

## Unique to Shuttle.rs

- developer base is the same as the instance owner base.

---

SpeakerNote:

---

