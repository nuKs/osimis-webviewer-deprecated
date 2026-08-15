# Thibault Piront (nuKs) — contributions à l'Osimis Web Viewer

*🇬🇧 An English version of this document is available: [README.md](README.md).*

Cette branche pointe sur `6450846` (29 août 2017), le dernier commit signé par Thibault Piront
dans ce dépôt : le code présent ici est donc le viewer tel qu'il l'a laissé. La branche
`thibault-piront-2024` porte le même document au-dessus de l'état final du dépôt (`dev`).

Le README original du projet est conservé tel quel dans
[`README.original.md`](README.original.md).

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

Ce fichier n'a jamais été tenu à jour. Il ne nomme que l'auteur du plugin Orthanc d'origine et
le lead developer, et omet le reste de l'équipe — en particulier le designer du projet,
Jérémy Evrard ([voir plus bas](#un-crédit-manquant-dans-authors)).

## Volume de contribution

Thibault Piront a été actif sur le projet du **1er décembre 2015 au 29 août 2017**, et y a
signé **1 384 commits**, répartis sur quatre identités Git utilisées au cours de la période :

| Identité Git | Commits |
|---|---|
| `Thibault Piront <gse.nuks@gmail.com>` | 1 157 |
| `Thibault PIRONT <gse.nuks@gmail.com>` | 123 |
| `Thibault Piront <nuks-home@nuKs-home.local>` | 63 |
| `Thibault Piront nuKs <gse.nuks@gmail.com>` | 41 |
| **Total** | **1 384** (1 218 hors merges) |

### Part du travail

Le dépôt s'étend de février 2015 à janvier 2024 et compte 2 576 commits. Rapportée à cette
durée totale, puis à sa propre période d'activité :

| Périmètre | Ses commits | Total | Part |
|---|---|---|---|
| Durée totale du dépôt (2015–2024) | 1 384 | 2 576 | **53,7 %** |
| Sa période d'activité (déc. 2015 – août 2017) | 1 382 | 1 834 | **75,4 %** |
| Même période, hors bot de CI Jenkins | 1 382 | 1 721 | **80,3 %** |
| Même période, commits touchant `frontend/` | 413 | 457 | **90,4 %** |
| Même période, commits touchant `backend/` | 121 | 153 | **79,1 %** |

Le chiffre sur la durée totale est dilué par six ans et demi de maintenance postérieure à son
départ. Ce sont les chiffres de la période qui comptent, et il faut les lire en sachant de quoi
le reste est fait : sur cette fenêtre, le seul autre contributeur substantiel est Alain Mazy
(248 commits), dont le travail se concentre sur l'infrastructure de build et de release —
`Jenkinsfile/`, les scripts de build Windows et macOS, Docker —, sur les dépendances Boost
embarquées et sur des parties de la bibliothèque backend. Sébastien Jodogne (39), Thibault
Nélis (18) et Jérémy Evrard (33) constituent le solde.

Le résumé honnête est donc : environ quatre commits sur cinq pendant sa présence, et près de
neuf sur dix de l'application frontend elle-même. Les chiffres `frontend/` et `backend/` ne
partent que du commit de juillet 2016 qui a séparé le JavaScript et le C++ en deux répertoires.

Sur la durée totale du dépôt, les contributeurs suivants sont Alain Mazy (~722 toutes identités
confondues), Sébastien Jodogne (~240) et Jérémy Evrard (89).

Le dépôt lui-même est antérieur à ce travail — son commit racine est `8fddb97`
(25 février 2015, Sébastien Jodogne, *« initial commit »*), le plugin Orthanc Web Viewer
d'origine — mais le viewer Osimis a été réécrit et réarchitecturé de zéro, dès le premier jour.
La continuité est dans l'historique Git, pas dans le code.

Les chiffres le montrent. Au dernier commit pré-Osimis (`42ca8fc`, 27 novembre 2015), l'arbre
contenait 108 fichiers ; l'arbre final en contient 988. Exactement **deux** chemins d'origine
subsistent inchangés : `AUTHORS` et `COPYING`.

Ce qui subsiste réellement est un petit résidu C++, déplacé dans le nouveau backend :

- `backend/WebViewerLibrary/ShortTermCache/` — le sous-système de cache d'origine
  (`CacheManager`, `CacheScheduler`, `CacheIndex`, `ICacheFactory`, `IPrefetchPolicy`,
  `ViewerPrefetchPolicy`) ;
- `backend/WebViewerLibrary/ViewerToolbox.{h,cpp}` et `SeriesInformationAdapter.{h,cpp}` ;
- `backend/WebViewerPlugin/Plugin.cpp` — le point d'entrée du plugin Orthanc ;
- `backend/Resources/OrthancExplorer.js` et quelques fichiers de toolchain CMake.

Ces fichiers portent toujours le double en-tête `Copyright (C) 2012-2016 Sebastien Jodogne` /
`Copyright (C) 2017 Osimis`. Tout le reste — l'intégralité du frontend, le pipeline image et
séries, les politiques de traitement, les couches de configuration et de routage, le build et
la CI — est du code neuf.

La procédure du projet elle-même, `procedures/merge-orthancwebviewer.md`, énonce clairement la
situation lorsqu'elle explique comment récupérer les correctifs upstream :

> This repository is a fork of orthanc-webviewer-plugin. […] As the directory structure and
> most of the files have changed, professional merging tool such as sublimerge may reveal
> helpful.

L'application Osimis proprement dite commence avec :

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

## Un crédit manquant dans AUTHORS

**Jérémy Evrard était le designer du projet, et a mené une refonte complète du viewer.** Le
fichier `AUTHORS` n'a simplement jamais été mis à jour pour en rendre compte — il s'est arrêté
à l'auteur du plugin d'origine et au lead developer. Lire ce seul fichier donne donc une image
trompeuse de qui a construit ce produit.

Son empreinte Git ici est de 89 commits entre le 12 février 2016 et le 18 juillet 2018, sous
quatre identités (`Jérémy Evrard`, `Evrard Jérémy`, `jeremyOsimis`, toutes sur `je@osimis.io`),
concentrés sur l'interface :

- **Refonte de la toolbar** — septembre 2017 : `87058ec` et `0c49b01`, regroupement des outils
  et introduction de grands boutons avec modes d'affichage à plat ou en arborescence, suivis de
  nouvelles icônes de groupes (`e9ed6fb`).
- **Layout et affichage des études** — le mode d'affichage `oneCol` (`2b6e3a3`), le bouton de
  layout du splitpane (`5377783`), les couleurs de bordure de pane par étude (`42bc190`), les
  options de breadcrumb et de disclaimer (`71d48f7`), la hauteur de la timeline (`c4c13e3`).
- **Internationalisation** — novembre 2017 : `d17a8ad` (26 fichiers), plus le chargement des
  locales côté serveur et la configuration de la langue par défaut.
- **Impression** — juillet 2018 : `054ea03`, `1800f40` et le correctif Edge `62810f0`.
- **Détails d'interaction** — raccourcis clavier (`e748986`), comportement des tooltips sur
  appareils tactiles (`a4f7b54`), affichage de l'état des séries dans la liste (`9066481`).

Une réserve mérite d'être posée : le nombre de commits sous-estime la contribution d'un
designer. Le travail de design — maquettes, itérations, spécifications visuelles — n'atterrit
en grande partie pas dans un dépôt Git, et une bonne part du CSS qui en résulte a été commitée
par celui qui l'implémentait. Ces 89 commits sont un plancher de l'implication de Jérémy
Evrard, pas une mesure de celle-ci.

## Ce que ce dépôt n'établit *pas*

Au-delà du code, le viewer a eu une vie de composant à l'intérieur des produits d'autres
équipes, et le Web Viewer Pro closed source reposait directement dessus. Rien de tout cela
n'est vérifiable depuis ce dépôt. Les sources ci-dessous proviennent d'une recherche
antérieure ; chaque lien a été récupéré et est donné ici pour que l'affirmation puisse être
vérifiée plutôt que crue sur parole.

Deux d'entre elles ont été revérifiées à la rédaction de ce document et sont marquées comme
telles. Les autres sont citées en l'état et restent à contrôler.

### La version Pro et le contexte dispositif médical

| Affirmation | Source |
|---|---|
| Osimis Pro Web Viewer — *Instructions for Use* officielles, hébergées par ERKNet | [erknet.org — Image_viewer.pdf](https://www.erknet.org/fileadmin/files/user_upload/Image_viewer.pdf) |
| L'Osimis Web Viewer lui-même n'est **pas** un dispositif médical, ni marqué CE, ni destiné au diagnostic ✅ | [Orthanc Book — Osimis Web viewer plugin (deprecated)](https://orthanc.uclouvain.be/book/plugins/osimis-webviewer.html) |
| Sébastien Jodogne sur la version Pro partageant presque tout son code avec la version libre (2023) | [Orthanc Users — Stone Web Viewer, open source projects in the clinical environment](https://discourse.orthanc-server.org/t/stone-web-viewer-open-source-projects-in-the-clinical-environment/3676) |
| Différence Basic / Pro décrite publiquement en 2017 (marquage CE + LiveShare) | [orthanc-users — BQBGGbzVMfY](https://groups.google.com/g/orthanc-users/c/BQBGGbzVMfY) |
| Le Stone Web Viewer comme remplaçant ultérieur, réellement différent | [Orthanc Book — Stone Web viewer](https://orthanc.uclouvain.be/book/plugins/stone-webviewer.html) |

### Intégrations tierces

| Intégrateur | Source |
|---|---|
| **OpenApp / CPMS** — Commission européenne, maladies rares. Release note, CPMS Version 2, 18 janvier 2018 : *« Update Osimis Viewer plug-ins in CPMS in line with the latest release of Osimis viewer »* ✅ | [EpiCARE — release notes CPMS](https://epi-care.eu/clinical-patient-management-system-cpms-live-12pm-cet-20th-november-2017/) · [Étude de cas OpenApp](https://www.openapp.ie/clinical-patient-management-system-for-european-reference-networks-a-case-study/) · [Commission européenne — Work of the ERNs](https://health.ec.europa.eu/rare-diseases-and-european-reference-networks/european-reference-networks/work-erns_en) |
| **ISB Cancer Genomics Cloud** — action « Open in Osimis Web Viewer » depuis une cohorte cancérologique | [Documentation ISB-CGC — Saved Cohorts](https://isb-cgc-readthedocs.readthedocs.io/en/latest/sections/webapp/Saved-Cohorts.html) |
| **BMC2** — Blue Cross Blue Shield of Michigan Cardiovascular Consortium | [bmc2.org — registre PCI](https://bmc2.org/about/what-we-do/pci) |
| **PAN Enterprise** — portail patient affichant encore une colonne « Osimis Image » | [pultratt.panenterprise.com](https://pultratt.panenterprise.com/patient_portal_examination) |
| **Biotron S.p.A.** — usage vétérinaire, angle de Norberg sur radiographies de chiens (2019) | [Orthanc Users — Osimis viewer tool for vet](https://discourse.orthanc-server.org/t/osimis-viewer-tool-for-vet/1260) |
| **eSanjeevani** — service national indien de télémédecine ; viewer présélectionné dans une évaluation | [Journal of the ISfTeH (UKZN)](https://journals.ukzn.ac.za/index.php/JISfTeH/article/download/162/html?inline=1) |
| **Workflow IA → PACS** — le viewer comme couche d'inspection d'un pipeline expérimental | [Radiology: Artificial Intelligence 10.1148/ryai.2021200105](https://pubs.rsna.org/doi/10.1148/ryai.2021200105) |
| **Imagerie de recherche** — relectures effectuées avec le viewer | [PMC7728946](https://pmc.ncbi.nlm.nih.gov/articles/PMC7728946/) · [PMC12557960](https://pmc.ncbi.nlm.nih.gov/articles/PMC12557960/) |
| **Distribué comme brique Orthanc** pour les intégrateurs logiciels | [orthanc-setup-samples](https://github.com/orthanc-server/orthanc-setup-samples/) · [Adeo Clouds](https://www.adeoclouds.eu/product.php?id=8) |
| **EMR non nommé** — export d'images clés annotées vers l'application EMR principale | [Orthanc Users — save annotations to a new DICOM file](https://discourse.orthanc-server.org/t/save-annotations-to-a-new-dicom-file-webviewer-keyimagecapture/2881) |
| **Embarquement commercial en iframe, société non nommée** | [orthanc-users — n45sVSORXqM](https://groups.google.com/g/orthanc-users/c/n45sVSORXqM) |

La recherche antérieure citait également **GNU Health, bitServer, SIMGOS, mLITE et le système
russe « L2 »** comme intégrations au niveau du code source, la plupart construisant le même
point d'entrée `osimis-viewer/app/index.html?study=<id>`. Ces éléments ont été rapportés sans
citation et aucun lien n'a survécu pour eux : ce sont les plus fragiles de cette liste, et ils
sont à retrouver de zéro.

### Traces publiques du rôle

| Affirmation | Source |
|---|---|
| Se présente comme le développeur principal de l'Osimis Viewer, mai 2016 | [Orthanc Users — Web Viewer](https://discourse.orthanc-server.org/t/web-viewer/397) · [orthanc-users — KPoIBLcc3Ng](https://groups.google.com/g/orthanc-users/c/KPoIBLcc3Ng) |
| Oriente les utilisateurs vers les points d'extension `toolbar/`, `viewport/image-plugins/` et `viewport/series-plugins/` | [orthanc-users — TWh4gLVAqrc](https://groups.google.com/g/orthanc-users/c/TWh4gLVAqrc) · [orthanc-users — _i2AwYkT2kw](https://groups.google.com/g/orthanc-users/c/_i2AwYkT2kw) |
| Pitch Osimis nommant Jérémy Evrard et Thibault Piront comme les développeurs | [SlideShare — Osimis pitch](https://www.slideshare.net/slideshow/osimis-pitch/58265421) |
| Tags DICOM spécifiquement vétérinaires travaillés chez Osimis, 5 décembre 2016 | [orthanc-users — IQNy0VEq0wY](https://groups.google.com/g/orthanc-users/c/IQNy0VEq0wY) |
| « Fifty Shades of Orthanc » (Frédéric Lambrechts, 2016) — annonçait un tour des implémentations Osimis | [fredlambrechts.bitbucket.io](https://fredlambrechts.bitbucket.io/) |

### Toujours non identifiés

- **Un client vétérinaire spécialisé chevaux de course, actif fin 2016.** La trace la plus
  solide est le fil Orthanc ci-dessus sur les tags DICOM vétérinaires. Candidats examinés puis
  écartés : [ARQANA](https://www.thoroughbreddailynews.com/arqana-to-establish-repository/)
  (utilisait Asteris Keystone),
  [Eclipse Veterinary Software](https://www.eclipsesoftware.info/veterinary/) (webPACS
  propriétaire), [Equine MediRecord](https://www.rte.ie/news/business/2022/0804/1313813-equine-medirecord-secures-10m-investment-from-us-firm/)
  (dossiers de traitement, pas d'imagerie). Vocabulaire de recherche utile : *racehorse /
  thoroughbred / bloodstock / yearling / pre-purchase X-rays*, nettement plus discriminant que
  « veterinary ». Contexte métier : [EVA Blue Book](https://www.imags.com.au/EVA_Final_Blue_Book/116/).
- **Un client sud-africain, dont le CEO serait avocat.** Aucune correspondance solide.
  [CloudSound](https://www.cloudsound.ai/) a été envisagé puis écarté — aucune trace publique
  Orthanc ou Osimis.

## Sources

La source principale de tout ce qui précède la section ci-dessus est **ce dépôt lui-même** :
son historique Git, ses en-têtes de fichiers et sa documentation. Chaque empreinte de commit,
citation, chemin de fichier et décompte a été vérifié directement dans le dépôt.

Les affirmations externes proviennent d'une conversation de recherche antérieure, exportée en
PDF. Cet export n'affiche à l'écran que des libellés de citation, mais les URL sous-jacentes
subsistent sous forme d'annotations de lien PDF ; elles en ont été extraites et sont reproduites
ci-dessus.

Dépôt amont : [orthanc-team/osimis-webviewer-deprecated](https://github.com/orthanc-team/osimis-webviewer-deprecated).

## Note méthodologique

Ce document a été compilé par une IA. Tous les chiffres et citations des sections vérifiées ont
été redérivés du dépôt le 15 août 2026 plutôt que repris de la recherche antérieure — plusieurs
dates de celle-ci ont d'ailleurs été corrigées au passage. Les affirmations qui n'ont pas pu
être vérifiées dans le dépôt sont confinées à la section qui l'indique, chacune avec sa source ;
les deux marquées ✅ ont en outre été refetchées et confirmées mot pour mot.
