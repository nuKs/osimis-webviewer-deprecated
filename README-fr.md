# Thibault Piront (nuKs) — contributions à l'Osimis Web Viewer

*🇬🇧 An English version of this document is available: [README.md](README.md).*

Cette branche est basée sur `c0b1678` (9 janvier 2024, Sébastien Jodogne), l'état final du
dépôt — le viewer a depuis été abandonné au profit du Stone Web Viewer. La branche
`thibault-piront` pointe, elle, sur le dernier commit signé par Thibault Piront
(`6450846`, 29 août 2017).

Le README original du projet est conservé à la suite du document anglais, dans
[`README.md`](README.md).

## Synthèse

L'Osimis Web Viewer était un visualiseur d'imagerie médicale fonctionnant dans le navigateur,
distribué comme plugin d'[Orthanc](https://www.orthanc-server.com/), construit autour d'un
backend C++ et d'un frontend JavaScript/AngularJS. Son fichier `AUTHORS` crédite Thibault
Piront ainsi :

```
* Thibault Piront <thibault.piront@osimis.io>
  OSIMIS S.A.
  Belgium

  Overall design and lead developer.
```

Entre le 1er décembre 2015 et le 29 août 2017, il a signé **1 384 commits** dans ce dépôt —
de très loin la contribution la plus importante — couvrant l'application initiale,
l'architecture frontend, le backend C++ de traitement d'images, la chaîne de build et de CI,
ainsi que les procédures formelles de développement du projet.

## Volume de contribution

Les commits se répartissent sur quatre identités Git utilisées au cours de la période :

| Identité Git | Commits |
|---|---|
| `Thibault Piront <gse.nuks@gmail.com>` | 1 157 |
| `Thibault PIRONT <gse.nuks@gmail.com>` | 123 |
| `Thibault Piront <nuks-home@nuKs-home.local>` | 63 |
| `Thibault Piront nuKs <gse.nuks@gmail.com>` | 41 |
| **Total** | **1 384** (1 218 hors merges) |

À titre de comparaison, le dépôt compte 2 576 commits accessibles depuis l'ensemble des refs.
Les contributeurs suivants sont Alain Mazy (~722 toutes identités confondues) et Sébastien
Jodogne (~240).

Le dépôt lui-même est antérieur à ce travail : son commit racine est `8fddb97`
(25 février 2015, Sébastien Jodogne, *« initial commit »*), le plugin Orthanc Web Viewer
d'origine. L'application Osimis proprement dite commence avec :

- **`fade111`** — 1er décembre 2015 — *« chore: init app »* — premier commit de la nouvelle
  application ;
- **`6450846`** — 29 août 2017 — *« Merged in TP-WVB-295-export-jpeg-image (pull request #62) »* —
  dernier commit, toujours accessible depuis `dev` comme depuis `master`.

Trente-trois tags de release ont survécu (de `v0.1.0` à `1.4.3`, plus des instantanés datés
comme `april2016`) : le produit peut donc être restitué dans les états exacts qu'il avait
pendant cette période.

GitHub mesure la base de code à 2 278 357 octets de JavaScript, 337 304 de C++, 201 064 de C,
138 341 de HTML, 114 182 de SCSS et 60 528 de CMake — et aucun TypeScript.

## Empreinte technique

### Conception par composants, dès le prototype

La structure modulaire précède le besoin. Dès février 2016, le dépôt contient déjà une
**documentation par composant** (`wv-overlay`, `wv-serielist`, `wv-splitpane`, `wv-toolbar`,
`wv-viewport`) et des **démos de composants autonomes** — `demo-overlay.html`,
`demo-serielist.html`, `demo-splitpane.html`, `demo-toolbar.html` — qui exécutent chaque
élément en dehors de l'application.

Cette intention a tenu. Le frontend final expose toujours des points d'extension explicites
sous `frontend/src/app/viewport/` :

```
image-plugins/
series-plugins/
progressive-image-loader/
quality-policies/
```

### La refonte de l'architecture image / viewport

Le commit **`8172e97`** (29 novembre 2016, 38 fichiers, +3 054 / −1 413) est le changement
architectural central. Son message en énumère la portée :

```
refactor(viewport): wrap cornerstone - refactor/test image & viewport

Apply to some extent to both models & managers:
- annotations, viewport, image, preloader, cache

refactor(annotation): move annotations out of angular
refactor(viewport): quality policies
refactor(viewport): progressive image loader
refactor(image): mock ImageBinaryManager & ImageModel
```

Il sort le modèle métier d'AngularJS, isole la dépendance Cornerstone derrière un wrapper, et
sépare le chargement d'images, le cache, la sélection de qualité et les annotations en
responsabilités distinctes, avec mocks et tests.

L'adaptateur Cornerstone qu'il introduit se trouve dans
`frontend/src/app/viewport/progressive-image-loader/cornerstone-viewport.wrapper.js`, mergé via
**`034dbef`** (28 février 2017,
*« Merged in TP-WVB-183-abstract-cornerstone-viewport-data-object »*). Il encapsule l'objet
viewport de Cornerstone tout en conservant son interface, de sorte que le chargement progressif
à différentes résolutions reste invisible pour les appelants.

### Backend C++ — programmation générique et spécialisation de templates

Le backend route les endpoints HTTP vers les contrôleurs via une factory templatée, dans
`backend/WebViewerLibrary/AbstractWebViewer.cpp` :

```cpp
RegisterRoute<ImageController>("/osimis-viewer/images/");
RegisterRoute<SeriesController>("/osimis-viewer/series/");
RegisterRoute<ConfigController>("/osimis-viewer/config.js");
```

Les pipelines de traitement d'images sont ensuite assemblés par spécialisations explicites de
templates dans `Image/ImageController.cpp` — `_Instantiate<ResizePolicy>`,
`_Instantiate<JpegConversionPolicy>` et `_Instantiate<CompositePolicy>`, cette dernière
construisant un parser qui compose plusieurs politiques typées à partir d'une seule route.

### Modernisation d'Adobe/Boost.GIL

Un cas bien documenté de maintenance C++ fortement templatée :

| Commit | Date | Modification |
|---|---|---|
| `58be33a` | 28 avr. 2016 | *chore(gil): install Adobe GIL with numeric extensions* |
| `66dfdd3` | 28 avr. 2016 | *fix(gil): correct GIL compilation issues* |
| `44e004f` | 25 mai 2016 | *fix(gil): fix GIL library errors (again)* |
| `5fc48d3` | 28 sept. 2016 | *chore(GIL): adapt GIL to cxx11* |
| `32ab2b8` | 11 oct. 2016 | *style(gil): warn to not use standard GIL packages* |

Le portage C++11 est une véritable modification de métaprogrammation Boost.MPL, dans
`boost/gil/channel_algorithm.hpp` :

```diff
-struct unsigned_integral_max_value : public mpl::integral_c<UnsignedIntegralChannel,-1> {};
+struct unsigned_integral_max_value : public mpl::integral_c<UnsignedIntegralChannel, boost::integer_traits<UnsignedIntegralChannel>::const_max> {};
```

Le travail est documenté dans le dépôt lui-même, dans `backend/Dependencies/WARNING.txt` :

```
The boost GIL library available in this folder has been patched by Osimis.

The provided modifications are:
- Adobe extensions' inclusion
- Compatibility with recent compilers (C++03)
- Compatibility with C++11
```

La GIL embarquée a été retirée plus tard par un autre développeur (`99e245e`,
5 novembre 2018, *« removed gil; now using our own resize algo »*), bien après cette période.

### Processus de développement réglementé — ISO 13485

Le commit **`4f456dd`** (12 décembre 2016, *« chore(procedures): update README.md and write
procedures »*) ajoute 483 lignes de procédures formelles :

```
procedures/archive-test-reports.md
procedures/bootstrap-dev-environment.md
procedures/develop-auth-proxy.md
procedures/merge-orthancwebviewer.md
procedures/release-version.md
procedures/report-bug-or-feedback.md
procedures/run-tests.md
```

`archive-test-reports.md` en énonce directement la finalité réglementaire :

> In order to be able to fulfill ISO13485 Web Viewer Pro's Test Procedure without relaunching
> the Web Viewer Basic tests manually, WVB test reports have to be logged at each WVB version
> release.

`release-version.md` définit la frontière du produit et la barrière de release :

> The Web Viewer Basic lies at the boundaries between a Library and an Application.

Le document spécifie ensuite l'*API publique* du viewer, et fait dépendre la checklist de
release des artefacts du système qualité du Web Viewer Pro — *Design Input & Traceability
Matrix*, *Risk Assessment Report*, *Test Plan*, *System Architecture Description* — ainsi que
d'une revue des release notes des SOUP.

**Une précision importante sur l'endroit où se trouve cette preuve :** deux jours plus tard, le
commit `cdbf876` (14 décembre 2016, *« chore(procedures): remove references to WVP »*) a retiré
du dépôt public les références au Web Viewer Pro et à l'ISO. Le matériel ci-dessus n'est donc
visible que dans l'historique Git, pas dans l'arbre actuel. Sur `dev`,
`procedures/release-version.md` conserve encore la vérification des release notes des SOUP.

Les identifiants d'exigences et de tests issus de ce cycle de vie sont toujours présents dans
les tests sur `dev` : `USR-0502`, `USR-0509`, `USR-0510`, ainsi que la série `UT01xx`.

### Points de variation produit : LiveShare

LiveShare — la fonctionnalité de collaboration du Web Viewer Pro, closed source — a été
construite sur le modèle commun plutôt qu'à côté. L'`AnnotationManager` public documente
lui-même la couture :

```js
/**
 * @name osimis.AnnotationManager#getAll
 *
 * The `getAll` only intent is to provide backup of annotations for
 * storage. For instance LiveShare.
 */
```

Les commits associés dans ce dépôt comprennent `c591ff1` (8 mai 2017,
*« Merged in TP-WVP-98-integrate-latest-update-with-liveshare »*) et `7129555`
(15 juillet 2017, *« fix(splitpane): fix bug in WVP liveshare »*).

### Intégration et revue

Six commits de merge portent explicitement un trailer `Approved-by: Thibault PIRONT`, aux côtés
d'un grand nombre de merges de branches d'autres membres de l'équipe (`am/…`, `tn/…`) et de
branches de release — un rôle d'intégrateur et de relecteur, et pas seulement de contributeur
individuel.

## Ce que ce dépôt n'établit *pas*

Une recherche antérieure avait fait ressortir plusieurs réutilisations du viewer en aval, ainsi
que le Web Viewer Pro closed source. **Rien de tout cela n'est vérifiable depuis ce dépôt, et
les liens sources n'ont pas survécu à l'export de cette recherche.** Ces éléments sont
consignés ici comme pistes à revérifier, pas comme faits établis :

- **Web Viewer Pro / dispositif médical marqué CE** — closed source ; le dépôt public prouve
  l'*interface* avec lui (procédures, préfixes de tickets `WVP`, coutures LiveShare), pas son
  contenu.
- **OpenApp / CPMS** — le Clinical Patient Management System de la Commission européenne pour
  les European Reference Networks, dont les release notes de 2018 mentionneraient la mise à
  jour des « Osimis Viewer plug-ins ».
- **ISB Cancer Genomics Cloud** — proposerait une action « Open in Osimis Web Viewer ».
- **GNU Health, bitServer, SIMGOS, mLITE, « L2 », PAN Enterprise, BMC2 (consortium de
  cardiologie de l'université du Michigan), Biotron S.p.A. (vétérinaire), eSanjeevani (Inde,
  présélectionné)** — intégrations tierces rapportées, la plupart suivant le même point
  d'entrée `osimis-viewer/app/index.html?study=<id>`.
- **Un client vétérinaire spécialisé chevaux de course (fin 2016) et un client sud-africain** —
  évoqués de mémoire mais jamais nommés ; aucune identification publique n'a été trouvée.

Quiconque poursuivra ce travail devrait traiter cette liste comme un programme de recherche.
Pour la piste équine, le vocabulaire utile est *racehorse / thoroughbred / bloodstock /
yearling / pre-purchase X-rays*, nettement plus discriminant que « veterinary ».

## Sources

La source principale de tout ce qui précède est **ce dépôt lui-même** : son historique Git, ses
en-têtes de fichiers et sa documentation. Chaque empreinte de commit, citation, chemin de
fichier et décompte ci-dessus a été vérifié directement dans le dépôt.

La section non vérifiée provient d'une conversation de recherche antérieure exportée en PDF ;
cet export n'a pas conservé les URL de ses sources.

## Note méthodologique

Ce document a été compilé par une IA. Tous les chiffres et citations des sections vérifiées ont
été redérivés du dépôt le 15 août 2026 plutôt que repris de la recherche antérieure — plusieurs
dates de celle-ci ont d'ailleurs été corrigées au passage. Les affirmations qui n'ont pas pu
être vérifiées dans le dépôt sont confinées à la section qui l'indique.
