# Thibault Piront (nuKs) — contributions to the Osimis Web Viewer

*🇫🇷 Une version française de ce document est disponible : [README-fr.md](README-fr.md).*

This branch is based on `c0b1678` (9 January 2024, Sébastien Jodogne), the final state of the
repository — the viewer has since been deprecated in favour of the Stone Web Viewer. The
`thibault-piront` branch points instead at the last commit authored by Thibault Piront
(`6450846`, 29 August 2017).

The original project README is kept below this document.

## Summary

The Osimis Web Viewer was a browser-based medical imaging viewer distributed as a plugin for
[Orthanc](https://www.orthanc-server.com/), built around a C++ backend and a
JavaScript/AngularJS frontend. Its `AUTHORS` file credits Thibault Piront as:

```
* Thibault Piront <thibault.piront@osimis.io>
  OSIMIS S.A.
  Belgium

  Overall design and lead developer.
```

Between 1 December 2015 and 29 August 2017 he authored **1,384 commits** in this repository —
by a wide margin the largest contribution — covering the initial application, the frontend
architecture, the C++ image-processing backend, the build and CI chain, and the project's
formal development procedures.

## Contribution volume

Commits are spread across four Git identities used over the period:

| Git identity | Commits |
|---|---|
| `Thibault Piront <gse.nuks@gmail.com>` | 1,157 |
| `Thibault PIRONT <gse.nuks@gmail.com>` | 123 |
| `Thibault Piront <nuks-home@nuKs-home.local>` | 63 |
| `Thibault Piront nuKs <gse.nuks@gmail.com>` | 41 |
| **Total** | **1,384** (1,218 excluding merges) |

For scale, the repository holds 2,576 commits reachable from all refs. The next largest
contributors are Alain Mazy (~722 across his identities) and Sébastien Jodogne (~240).

The repository itself predates this work: its root commit is `8fddb97`
(25 February 2015, Sébastien Jodogne, *"initial commit"*), the original Orthanc Web Viewer
plugin. The Osimis application proper starts with:

- **`fade111`** — 1 December 2015 — *"chore: init app"* — first commit of the new application;
- **`6450846`** — 29 August 2017 — *"Merged in TP-WVB-295-export-jpeg-image (pull request #62)"* —
  last commit, still reachable from both `dev` and `master`.

Thirty-three release tags survive (`v0.1.0` through `1.4.3`, plus dated snapshots such as
`april2016`), so the product can be checked out at the exact states it had during that period.

GitHub measures the codebase at 2,278,357 bytes of JavaScript, 337,304 of C++, 201,064 of C,
138,341 of HTML, 114,182 of SCSS and 60,528 of CMake — and no TypeScript.

## Technical footprint

### Component-oriented design, from the prototype onward

The modular structure predates any need for it. As early as February 2016 the repository
already ships **per-component documentation** (`wv-overlay`, `wv-serielist`, `wv-splitpane`,
`wv-toolbar`, `wv-viewport`) and **standalone component demos** — `demo-overlay.html`,
`demo-serielist.html`, `demo-splitpane.html`, `demo-toolbar.html` — that run each piece
outside the application shell.

That intent held. The final frontend still exposes explicit extension points under
`frontend/src/app/viewport/`:

```
image-plugins/
series-plugins/
progressive-image-loader/
quality-policies/
```

### The image/viewport architecture rework

Commit **`8172e97`** (29 November 2016, 38 files, +3,054 / −1,413) is the central
architectural change. Its message enumerates the scope:

```
refactor(viewport): wrap cornerstone - refactor/test image & viewport

Apply to some extent to both models & managers:
- annotations, viewport, image, preloader, cache

refactor(annotation): move annotations out of angular
refactor(viewport): quality policies
refactor(viewport): progressive image loader
refactor(image): mock ImageBinaryManager & ImageModel
```

It pulls the domain model out of AngularJS, isolates the Cornerstone dependency behind a
wrapper, and splits image loading, caching, quality selection and annotations into separate
responsibilities with mocks and tests.

The Cornerstone adapter it introduces lives at
`frontend/src/app/viewport/progressive-image-loader/cornerstone-viewport.wrapper.js`, merged
through **`034dbef`** (28 February 2017,
*"Merged in TP-WVB-183-abstract-cornerstone-viewport-data-object"*). It wraps Cornerstone's
viewport object while preserving its interface, so that progressive loading across image
resolutions stays invisible to callers.

### C++ backend — generic programming and template specialization

The backend routes HTTP endpoints to controllers through a template factory in
`backend/WebViewerLibrary/AbstractWebViewer.cpp`:

```cpp
RegisterRoute<ImageController>("/osimis-viewer/images/");
RegisterRoute<SeriesController>("/osimis-viewer/series/");
RegisterRoute<ConfigController>("/osimis-viewer/config.js");
```

Image-processing pipelines are then assembled by explicit template specializations in
`Image/ImageController.cpp` — `_Instantiate<ResizePolicy>`, `_Instantiate<JpegConversionPolicy>`
and `_Instantiate<CompositePolicy>`, the last of which builds a parser that composes several
typed policies from a single route.

### Modernizing Adobe/Boost.GIL

A well-documented piece of template-heavy C++ maintenance:

| Commit | Date | Change |
|---|---|---|
| `58be33a` | 28 Apr. 2016 | *chore(gil): install Adobe GIL with numeric extensions* |
| `66dfdd3` | 28 Apr. 2016 | *fix(gil): correct GIL compilation issues* |
| `44e004f` | 25 May 2016 | *fix(gil): fix GIL library errors (again)* |
| `5fc48d3` | 28 Sep. 2016 | *chore(GIL): adapt GIL to cxx11* |
| `32ab2b8` | 11 Oct. 2016 | *style(gil): warn to not use standard GIL packages* |

The C++11 port is a genuine Boost.MPL change in `boost/gil/channel_algorithm.hpp`:

```diff
-struct unsigned_integral_max_value : public mpl::integral_c<UnsignedIntegralChannel,-1> {};
+struct unsigned_integral_max_value : public mpl::integral_c<UnsignedIntegralChannel, boost::integer_traits<UnsignedIntegralChannel>::const_max> {};
```

The work is documented in-tree, in `backend/Dependencies/WARNING.txt`:

```
The boost GIL library available in this folder has been patched by Osimis.

The provided modifications are:
- Adobe extensions' inclusion
- Compatibility with recent compilers (C++03)
- Compatibility with C++11
```

The vendored GIL was later dropped by another developer (`99e245e`, 5 November 2018,
*"removed gil; now using our own resize algo"*), well after this period.

### Regulated development process — ISO 13485

Commit **`4f456dd`** (12 December 2016, *"chore(procedures): update README.md and write
procedures"*) adds 483 lines of formal project procedures:

```
procedures/archive-test-reports.md
procedures/bootstrap-dev-environment.md
procedures/develop-auth-proxy.md
procedures/merge-orthancwebviewer.md
procedures/release-version.md
procedures/report-bug-or-feedback.md
procedures/run-tests.md
```

`archive-test-reports.md` states the regulatory purpose directly:

> In order to be able to fulfill ISO13485 Web Viewer Pro's Test Procedure without relaunching
> the Web Viewer Basic tests manually, WVB test reports have to be logged at each WVB version
> release.

`release-version.md` defines the product boundary and the release gate:

> The Web Viewer Basic lies at the boundaries between a Library and an Application.

It then specifies the viewer's *Public API*, and makes the release checklist depend on the
Web Viewer Pro's quality-management artefacts — *Design Input & Traceability Matrix*,
*Risk Assessment Report*, *Test Plan*, *System Architecture Description* — plus a review of
SOUP release notes.

**An important caveat about where this evidence lives:** two days later, commit `cdbf876`
(14 December 2016, *"chore(procedures): remove references to WVP"*) stripped the Web Viewer Pro
and ISO references back out of the public repository. The material above is therefore only
visible in Git history, not in the current tree. `procedures/release-version.md` on `dev` still
retains the SOUP release-notes check.

Requirement and test identifiers from that lifecycle are still present in the tests on `dev`:
`USR-0502`, `USR-0509`, `USR-0510`, and the `UT01xx` series.

### Product-line seams: LiveShare

LiveShare — the collaboration feature of the closed-source Web Viewer Pro — was built on the
shared model rather than beside it. The public `AnnotationManager` documents the seam itself:

```js
/**
 * @name osimis.AnnotationManager#getAll
 *
 * The `getAll` only intent is to provide backup of annotations for
 * storage. For instance LiveShare.
 */
```

Related commits in this repository include `c591ff1` (8 May 2017,
*"Merged in TP-WVP-98-integrate-latest-update-with-liveshare"*) and `7129555` (15 July 2017,
*"fix(splitpane): fix bug in WVP liveshare"*).

### Integration and review

Six merge commits carry an explicit `Approved-by: Thibault PIRONT` trailer, alongside a large
number of merges of other team members' branches (`am/…`, `tn/…`) and of release branches —
a role of integrator and reviewer rather than individual contributor alone.

## What this repository does *not* establish

A prior research pass surfaced a number of downstream uses of the viewer and of the
closed-source Web Viewer Pro. **None of it is verifiable from this repository, and the source
links did not survive the export of that research.** It is recorded here as leads to
re-verify, not as established fact:

- **Web Viewer Pro / CE-marked medical device** — closed source; the public repository proves
  the *interface* to it (procedures, `WVP` ticket prefixes, LiveShare seams), not its content.
- **OpenApp / CPMS** — the European Commission's Clinical Patient Management System for
  European Reference Networks, reportedly updating "Osimis Viewer plug-ins" in its 2018
  release notes.
- **ISB Cancer Genomics Cloud** — reportedly offering an "Open in Osimis Web Viewer" action.
- **GNU Health, bitServer, SIMGOS, mLITE, "L2", PAN Enterprise, BMC2 (University of Michigan
  cardiology consortium), Biotron S.p.A. (veterinary), eSanjeevani (India, shortlisted)** —
  reported third-party integrations, most of them following the same
  `osimis-viewer/app/index.html?study=<id>` entry point.
- **A racehorse/thoroughbred veterinary client (late 2016) and a South African client** —
  recalled but never named; no public identification was found.

Anyone continuing this work should treat the list above as a search agenda. The useful search
vocabulary for the equine lead is *racehorse / thoroughbred / bloodstock / yearling /
pre-purchase X-rays*, which is far more discriminating than "veterinary".

## Sources

The primary source for everything in the preceding sections is **this repository itself** —
its Git history, file headers and documentation. Every commit hash, quotation, file path and
count above was checked directly against it.

The unverified section derives from an earlier research conversation exported to PDF; that
export did not preserve its source URLs.

## Methodology

This document was compiled by an AI. All figures and quotations in the verified sections were
re-derived from the repository on 15 August 2026 rather than carried over from the earlier
research, and several dates in that research were corrected in the process. Claims that could
not be checked against the repository are confined to the section above that says so.

---

# Osimis Web Viewer

The [Osimis'](htpp://www.osimis.io/) Web Viewer provides medical image 
visualization straight from the browser.

It is distributed as a plugin to [Orthanc](http://www.orthanc-server.com/). In 
other words, the viewer can be connected to most modalities, but also leveraged
through Orthanc's strong architectural extensibility.

2D rendering is supported with the usual tools:

- Zooming
- Panning
- Windowing
- Length Measurement
- Angle Measurement
- Point/Circle/Rectangle of Interest
- Image Flipping/Rotation
- Multiframe support

Have a look at [our blog](http://www.osimis.io/en/blog.html).

## Demo

A demo of the viewer is available at those links:

- [IRM study](http://osimisviewer.osimis.io/osimis-viewer/app/index.html?study=1b4c72ad-5aba2557-9fc396b3-323e190c-07d36585).
- [Full demo](http://osimisviewer.osimis.io/), embedded within the Orthanc
  Explorer. Other studies are available from there.

## What's new

See the [release notes](https://bitbucket.org/osimis/osimis-webviewer-plugin/src/master/RELEASE_NOTES.txt).

## Installation & Usage

The latest stable version is available [here](http://www.osimis.io/en/download.html).

Nightly builds are available [here](http://orthanc.osimis.io/#/nightly).
They are still unstable at the moment.

We recommend to download the binaries for Windows and Mac OS X & the docker
image for Linux.

[This article](http://www.osimis.io/en/blog/2016/06/03/deploy-Orthanc-on-a-PC-in-38-seconds.html)
details the installation process on Windows.

[This procedure](https://osimis.atlassian.net/wiki/spaces/OKB/pages/26738689/How+to+use+osimis+orthanc+Docker+images#Howtouseosimis/orthancDockerimages?-Quickstart) explains how to use the
docker image on Linux.

For Mac OS X, the procedure is very similar to the windows' one. Unzip the
downloaded folder and double click on the `startOrthanc.command` file.

## Configuration

Orthanc is configurable via a [JSON file](https://orthanc.chu.ulg.ac.be/book/users/configuration.html).
This plugin provide a few optional options as well.  Check [this page](https://osimis.atlassian.net/wiki/spaces/OKB/pages/10321921/Osimis+Web+Viewer+-+Configuration+file) for a full list.


## Licensing

The Osimis' Web Viewer is licensed under the AGPL license. See the COPYING
file.
We also kindly ask scientific works and clinical studies that make use of
Orthanc to cite Orthanc in their associated publications. Similarly, we ask
open-source and closed-source products that make use of Orthanc to warn us
about this use. You can cite S. Jodogne's work using the following BibTeX
entry:

```
@inproceedings{Jodogne:ISBI2013,
author = {Jodogne, S. and Bernard, C. and Devillers, M. and Lenaerts, E. and Coucke, P.},
title = {Orthanc -- {A} Lightweight, {REST}ful {DICOM} Server for Healthcare and Medical Research},
booktitle={Biomedical Imaging ({ISBI}), {IEEE} 10th International Symposium on}, 
year={2013}, 
pages={190-193}, 
ISSN={1945-7928},
month=apr,
url={http://ieeexplore.ieee.org/xpl/articleDetails.jsp?tp=&arnumber=6556444},
address={San Francisco, {CA}, {USA}}
}
```

## Contact & Bug/Feedback Report

Any question/feedback/bug report are well appreciated. You may report them on the [Orthanc Users Group](https://groups.google.com/forum/#!forum/orthanc-users).

The full bug/feedback report procedure is available in the 
`procedures/report-bug-or-feedback.md` file.

## Authentification Proxy Development & Plugin's routes

See the `procedures/develop-auth-proxy.md` file.

## Development

### Folder structure

Six folders are available at the root:

- `backend/` contains the C++ plugin source code (& cmake build process).
- `frontend/` contains the HTML/JavaScript source code (& gulp build process).
- `reverse-proxy/` contains a reverse proxy suitable for development.
- `demo/` contains a standalone docker, proxied version of orthanc and the
  viewer with sample files.
- `scripts/` contains global demo building scripts, it is mostly used by the
  Continuous Integration System. It also contains scripts to build/run the web
  viewer demo in a docker environment.
- `tests/` contains the integration tests.

### Build

See the `procedures/bootstrap-dev-environment.md` file.

### Development

See the `procedures/bootstrap-dev-environment.md` file.

### Testing

See the `procedures/run-tests.md` file.

### Release procedure

See the `procedures/release-version.md` file. The
`procedures/archive-test-reports.md` file will be mentionned.

### Pulling changes back from orthanc-webviewer-plugin

See the `procedures/merge-orthancwebviewer.md` file.
