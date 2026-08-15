# Thibault Piront (nuKs) — contributions to the Osimis Web Viewer

*🇫🇷 Une version française de ce document est disponible : [README-fr.md](README-fr.md).*

This branch points at `6450846` (29 August 2017), the last commit authored by Thibault Piront
in this repository, so the code here is the viewer as he left it. The `thibault-piront-2024`
branch carries the same document on top of the repository's final state (`dev`).

The original project README is preserved unchanged as
[`README.original.md`](README.original.md).

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

That file was never kept up to date. It names only the author of the original Orthanc plugin
and the lead developer, and omits the rest of the team — in particular the project's designer,
Jérémy Evrard ([see below](#a-credit-missing-from-authors)).

## Contribution volume

Thibault Piront was active on the project from **1 December 2015 to 29 August 2017**, and
authored **1,384 commits** across four Git identities used over that period:

| Git identity | Commits |
|---|---|
| `Thibault Piront <gse.nuks@gmail.com>` | 1,157 |
| `Thibault PIRONT <gse.nuks@gmail.com>` | 123 |
| `Thibault Piront <nuks-home@nuKs-home.local>` | 63 |
| `Thibault Piront nuKs <gse.nuks@gmail.com>` | 41 |
| **Total** | **1,384** (1,218 excluding merges) |

### Share of the work

The repository spans February 2015 to January 2024 and holds 2,576 commits. Measured against
that whole lifetime, and against his own active period:

| Scope | His commits | Total | Share |
|---|---|---|---|
| Whole repository lifetime (2015–2024) | 1,384 | 2,576 | **53.7 %** |
| His active period (Dec. 2015 – Aug. 2017) | 1,382 | 1,834 | **75.4 %** |
| Same period, excluding the Jenkins CI bot | 1,382 | 1,721 | **80.3 %** |
| Same period, commits touching `frontend/` | 413 | 457 | **90.4 %** |
| Same period, commits touching `backend/` | 121 | 153 | **79.1 %** |

The whole-lifetime figure is diluted by six and a half years of maintenance after he left. The
period figures are the meaningful ones, and they should be read with what the remainder
consists of: in that window the only other substantial contributor is Alain Mazy (248 commits),
whose work concentrates on build and release infrastructure — `Jenkinsfile/`, the Windows and
macOS build scripts, Docker — on the vendored Boost dependencies, and on parts of the backend
library. Sébastien Jodogne (39), Thibault Nélis (18) and Jérémy Evrard (33) account for the
rest.

So the honest summary is: roughly four commits in five during his tenure, and around nine in
ten of the frontend application itself. The `frontend/` and `backend/` figures start from the
July 2016 commit that split JavaScript and C++ into separate directories.

The next largest contributors over the repository's whole lifetime are Alain Mazy (~722 across
his identities), Sébastien Jodogne (~240) and Jérémy Evrard (89).

The repository itself predates this work — its root commit is `8fddb97` (25 February 2015,
Sébastien Jodogne, *"initial commit"*), the original Orthanc Web Viewer plugin — but the Osimis
viewer was rewritten and rearchitected from scratch, starting on day one. The continuity is in
the Git history, not in the code.

The numbers make the point. At the last pre-Osimis commit (`42ca8fc`, 27 November 2015) the
tree held 108 files; the final tree holds 988. Exactly **two** of the original paths survive
unmoved: `AUTHORS` and `COPYING`.

What does survive, in substance, is a small C++ residue, relocated into the new backend:

- `backend/WebViewerLibrary/ShortTermCache/` — the original caching subsystem (`CacheManager`,
  `CacheScheduler`, `CacheIndex`, `ICacheFactory`, `IPrefetchPolicy`, `ViewerPrefetchPolicy`);
- `backend/WebViewerLibrary/ViewerToolbox.{h,cpp}` and `SeriesInformationAdapter.{h,cpp}`;
- `backend/WebViewerPlugin/Plugin.cpp` — the Orthanc plugin entry point;
- `backend/Resources/OrthancExplorer.js` and a few CMake toolchain files.

Those files still carry the dual header `Copyright (C) 2012-2016 Sebastien Jodogne` /
`Copyright (C) 2017 Osimis`. Everything else — the whole frontend, the image and series
pipeline, the processing policies, the configuration and routing layers, the build and CI — is
new code.

The project's own procedure, `procedures/merge-orthancwebviewer.md`, states the situation
plainly when explaining how to pull upstream bug fixes back in:

> This repository is a fork of orthanc-webviewer-plugin. […] As the directory structure and
> most of the files have changed, professional merging tool such as sublimerge may reveal
> helpful.

The Osimis application proper starts with:

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

## A credit missing from AUTHORS

**Jérémy Evrard was the project's designer, and carried out a full redesign of the viewer.**
The `AUTHORS` file simply was never updated to reflect it — it stopped at the original plugin
author and the lead developer — so reading that file alone gives a misleading picture of who
built this product.

His Git footprint here is 89 commits between 12 February 2016 and 18 July 2018, under four
identities (`Jérémy Evrard`, `Evrard Jérémy`, `jeremyOsimis`, all at `je@osimis.io`),
concentrated on the interface:

- **Toolbar overhaul** — September 2017: `87058ec` and `0c49b01`, grouping the tools and
  introducing large buttons with flat or tree display modes, followed by new group icons
  (`e9ed6fb`).
- **Layout and study display** — the `oneCol` display mode (`2b6e3a3`), the splitpane layout
  button (`5377783`), per-study pane border colours (`42bc190`), breadcrumb and disclaimer
  options (`71d48f7`), timeline height (`c4c13e3`).
- **Internationalization** — November 2017: `d17a8ad` (26 files) plus the server-side locale
  loading and default-language configuration.
- **Print support** — July 2018: `054ea03`, `1800f40` and the Edge fix `62810f0`.
- **Interaction details** — keyboard shortcuts (`e748986`), tooltip behaviour on touch
  devices (`a4f7b54`), series-state display in the series list (`9066481`).

A caveat worth stating: commit counts understate a designer's contribution. Design work —
mockups, iterations, visual specifications — largely does not land in a Git repository, and a
good deal of the resulting CSS was committed by whoever implemented it. The 89 commits are a
floor on Jérémy Evrard's involvement, not a measure of it.

## What this repository does *not* establish

Beyond the code, the viewer had a life as a component inside other people's products, and the
closed-source Web Viewer Pro sat directly on top of it. None of that is verifiable from this
repository. The sources below come from an earlier research pass; each link has been recovered
and is given here so the claim can be checked rather than taken on trust.

The ones marked ✅ were re-fetched and confirmed while writing this document. The rest are
cited as-found and still need checking.

### The Pro version and the medical-device context

| Claim | Source |
|---|---|
| Osimis Pro Web Viewer — official *Instructions for Use*, hosted by ERKNet | [erknet.org — Image_viewer.pdf](https://www.erknet.org/fileadmin/files/user_upload/Image_viewer.pdf) |
| The Osimis Web Viewer itself is **not** a medical device, not CE marked, not for diagnostic use ✅ | [Orthanc Book — Osimis Web viewer plugin (deprecated)](https://orthanc.uclouvain.be/book/plugins/osimis-webviewer.html) |
| Sébastien Jodogne on the Pro version sharing nearly all its code with the free one (2023) | [Orthanc Users — Stone Web Viewer, open source projects in the clinical environment](https://discourse.orthanc-server.org/t/stone-web-viewer-open-source-projects-in-the-clinical-environment/3676) |
| Basic vs Pro described publicly in 2017 (CE marking + LiveShare) | [orthanc-users — BQBGGbzVMfY](https://groups.google.com/g/orthanc-users/c/BQBGGbzVMfY) |
| Stone Web Viewer as the later, genuinely different replacement | [Orthanc Book — Stone Web viewer](https://orthanc.uclouvain.be/book/plugins/stone-webviewer.html) |

### Third-party integrations

| Integrator | Source |
|---|---|
| **OpenApp / CPMS** — European Commission, rare diseases. Release note, CPMS Version 2, 18 January 2018: *"Update Osimis Viewer plug-ins in CPMS in line with the latest release of Osimis viewer"* ✅ | [EpiCARE — CPMS release notes](https://epi-care.eu/clinical-patient-management-system-cpms-live-12pm-cet-20th-november-2017/) · [OpenApp case study](https://www.openapp.ie/clinical-patient-management-system-for-european-reference-networks-a-case-study/) · [European Commission — Work of the ERNs](https://health.ec.europa.eu/rare-diseases-and-european-reference-networks/european-reference-networks/work-erns_en) |
| **ISB Cancer Genomics Cloud** — "Open in Osimis Web Viewer" from a cancer cohort | [ISB-CGC docs — Saved Cohorts](https://isb-cgc-readthedocs.readthedocs.io/en/latest/sections/webapp/Saved-Cohorts.html) |
| **BMC2** — Blue Cross Blue Shield of Michigan Cardiovascular Consortium | [bmc2.org — PCI registry](https://bmc2.org/about/what-we-do/pci) |
| **PAN Enterprise** — patient portal still listing an "Osimis Image" column | [pultratt.panenterprise.com](https://pultratt.panenterprise.com/patient_portal_examination) |
| **Biotron S.p.A.** — veterinary use, Norberg angle on dog radiographs (2019) | [Orthanc Users — Osimis viewer tool for vet](https://discourse.orthanc-server.org/t/osimis-viewer-tool-for-vet/1260) |
| **eSanjeevani** — Indian national telemedicine service; the viewer shortlisted in an evaluation | [Journal of the ISfTeH (UKZN)](https://journals.ukzn.ac.za/index.php/JISfTeH/article/download/162/html?inline=1) |
| **AI → PACS workflow** — the viewer as the inspection layer of an experimental pipeline | [Radiology: Artificial Intelligence 10.1148/ryai.2021200105](https://pubs.rsna.org/doi/10.1148/ryai.2021200105) |
| **Research imaging** — reviews performed with the viewer | [PMC7728946](https://pmc.ncbi.nlm.nih.gov/articles/PMC7728946/) · [PMC12557960](https://pmc.ncbi.nlm.nih.gov/articles/PMC12557960/) |
| **Distributed as an Orthanc building block** for software integrators | [orthanc-setup-samples](https://github.com/orthanc-server/orthanc-setup-samples/) · [Adeo Clouds](https://www.adeoclouds.eu/product.php?id=8) |
| **Unnamed EMR** — exporting annotated key images to a main EMR application | [Orthanc Users — save annotations to a new DICOM file](https://discourse.orthanc-server.org/t/save-annotations-to-a-new-dicom-file-webviewer-keyimagecapture/2881) |
| **Unnamed commercial iframe embedding** | [orthanc-users — n45sVSORXqM](https://groups.google.com/g/orthanc-users/c/n45sVSORXqM) |
| **GNU Health** — DICOM integration for the GNU Health hospital information system ✅ | [Terabuck/GNUhealth-DICOM](https://github.com/Terabuck/GNUhealth-DICOM) · [README](https://github.com/Terabuck/GNUhealth-DICOM/blob/main/README.md) |
| **bitServer** — teleradiology and remote-radiologist workflow; resolves an accession number through Orthanc's `/tools/find`, then redirects to the viewer ✅ | [LinkServlet.java](https://github.com/id-05/bitServer/blob/master/src/main/java/LinkServlet.java) · [README](https://github.com/id-05/bitServer/blob/master/README.md) |
| **SIMGOS** — Indonesian Ministry of Health hospital ecosystem; the viewer is a single `PACService` config entry ✅ | [simgos-docker — php-fpm/local.php](https://github.com/argadhika/simgos-docker/blob/main/php-fpm/local.php) · [repository](https://github.com/argadhika/simgos-docker) |
| **mLITE / SIMRS** — a "Bridging PACS" screen linking studies straight into the viewer, next to a *Simpan ke SIMRS* (save to the hospital system) action ✅ | [Mlite — orthanc.html](https://github.com/riyanadityapradana/Mlite/blob/main/plugins/orthanc/view/admin/orthanc.html) |
| **"L2"** — Russian medical information system; builds the viewer URL across several Orthanc servers and peers ✅ | [api/dicom.py](https://github.com/mikhailprivalov/l2/blob/develop/api/dicom.py) · [repository](https://github.com/mikhailprivalov/l2) |

### The integration seam, in other people's words

Those last five converge on a single line — `osimis-viewer/app/index.html?study=<id>` — reached
independently by unrelated teams in different countries. bitServer resolves an accession number
through Orthanc's `/tools/find` and redirects:

```java
resp.sendRedirect("http://" + … + "/osimis-viewer/app/index.html?study=" + sid);
```

L2 does the equivalent across several Orthanc servers and peers; SIMGOS carries it as one
config value, `'viewer' => 'osimis-viewer/app/index.html'`; mLITE links to it directly from its
PACS bridging screen. None of them forked the viewer to do it.

The GNU Health case is the most useful of all, because it says *why* the seam held. Its README
states the choice outright:

> Osimis Dicom Viewer is used here because it handles the same UUID format as Orthanc, unlike
> the new Stone viewer that requires the 'StudyInstanceUID'.

That is an outside developer, years after the project was abandoned, choosing this viewer over
its official successor — because its entry point composed directly with Orthanc's own
identifiers and required no translation layer. It is external, unsolicited evidence for exactly
the architectural boundary described earlier in this document.

One caveat on SIMGOS: the repository above is a Docker wrapper presented as a development and
learning setup, not a production distribution. It shows Osimis support present in the SIMGOS
ecosystem, not that every SIMGOS hospital ran it.

### Public traces of the role

| Claim | Source |
|---|---|
| Presenting himself as the main developer of the Osimis Viewer, May 2016 | [Orthanc Users — Web Viewer](https://discourse.orthanc-server.org/t/web-viewer/397) · [orthanc-users — KPoIBLcc3Ng](https://groups.google.com/g/orthanc-users/c/KPoIBLcc3Ng) |
| Pointing users at the `toolbar/`, `viewport/image-plugins/` and `viewport/series-plugins/` extension points | [orthanc-users — TWh4gLVAqrc](https://groups.google.com/g/orthanc-users/c/TWh4gLVAqrc) · [orthanc-users — _i2AwYkT2kw](https://groups.google.com/g/orthanc-users/c/_i2AwYkT2kw) |
| Osimis pitch deck naming Jérémy Evrard and Thibault Piront as the developers | [SlideShare — Osimis pitch](https://www.slideshare.net/slideshow/osimis-pitch/58265421) |
| Veterinary-specific DICOM tags being worked on at Osimis, 5 December 2016 | [orthanc-users — IQNy0VEq0wY](https://groups.google.com/g/orthanc-users/c/IQNy0VEq0wY) |
| "Fifty Shades of Orthanc" (Frédéric Lambrechts, 2016) — announced a tour of Osimis implementations | [fredlambrechts.bitbucket.io](https://fredlambrechts.bitbucket.io/) |

### Still unidentified

- **A racehorse / thoroughbred veterinary client, active late 2016.** The strongest trace is
  the Orthanc thread above about veterinary DICOM tags. Candidates checked and set aside:
  [ARQANA](https://www.thoroughbreddailynews.com/arqana-to-establish-repository/) (used
  Asteris Keystone), [Eclipse Veterinary Software](https://www.eclipsesoftware.info/veterinary/)
  (own webPACS), [Equine MediRecord](https://www.rte.ie/news/business/2022/0804/1313813-equine-medirecord-secures-10m-investment-from-us-firm/)
  (medication records, not imaging). Useful search vocabulary: *racehorse / thoroughbred /
  bloodstock / yearling / pre-purchase X-rays* — far more discriminating than "veterinary".
  Background on the workflow: [EVA Blue Book](https://www.imags.com.au/EVA_Final_Blue_Book/116/).
- **A South African client, reportedly with a lawyer as CEO.** No solid match.
  [CloudSound](https://www.cloudsound.ai/) was considered and discarded — no public Orthanc or
  Osimis trace.

## Sources

The primary source for everything before the section above is **this repository itself** — its
Git history, file headers and documentation. Every commit hash, quotation, file path and count
was checked directly against it.

The external claims come from an earlier research conversation, exported to PDF. That export
shows only citation labels on screen, but the underlying URLs survive as PDF link annotations;
they were extracted from the file and are reproduced above.

Upstream repository: [orthanc-team/osimis-webviewer-deprecated](https://github.com/orthanc-team/osimis-webviewer-deprecated).

## Methodology

This document was compiled by an AI. All figures and quotations in the verified sections were
re-derived from the repository on 15 August 2026 rather than carried over from the earlier
research, and several dates in that research were corrected in the process. Claims that could
not be checked against the repository are confined to the section that says so, each with its
source; those marked ✅ were additionally re-fetched and confirmed verbatim.
