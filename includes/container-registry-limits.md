---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 03/11/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0090f02382e024e5539383328b55d58798002d63
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117158"
---
| Erőforrás | Alapszintű | Standard | Prémium |
|---|---|---|---|
| Tár<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Rendszerképréteg maximális mérete | 200 GiB | 200 GiB | 200 GiB |
| Olvasási műveletek percenként<sup>2, 3</sup> | 1,000 | 3000 | 10,000 |
| Írási műveletek percenként<sup>2, 4</sup> | 100 | 500 | 2000 |
| Letöltési sávszélesség (MBps)<sup>2</sup> | 30 | 60 | 100 |
| Feltöltési sávszélesség (MBps)<sup>2</sup> | 10 | 20 | 50 |
| Webhookok | 2 | 10 | 500 |
| Georeplikáció | N/A | N/A | [Támogatott][geo-replication] |
| Tartalommegbízhatóság | N/A | N/A | [Támogatott][content-trust] |
| Virtuális hálózati hozzáférés | N/A | N/A | [Előzetes verzió][vnet] |
| A privát kapcsolat integrációja | N/A | N/A | [Előzetes verzió][plink] |
| Felhasználó által kezelt kulcsok | N/A | N/A | [Előzetes verzió][cmk] |
| Adattárhatókörrel rendelkező engedélyek | N/A | N/A | [Előzetes verzió][token]|
| &bull; Jogkivonatok | N/A | N/A | 20 000 |
| &bull; Hatókör-leképezések | N/A | N/A | 20 000 |
| &bull; Adattárak hatókör-leképezésenként | N/A | N/A | 500 |


<sup>1</sup>A megadott tárhelykorlátok a *csomagban foglalt* tárterületet jelentik az egyes rétegekre vonatkozóan. A korlátokat meghaladó képtárolásért további napidíjat számítunk fel GiB-onként. További információ a díjszabásról: [Az Azure Container Registry díjszabása][pricing].

<sup>2</sup>*Az olvasási műveletek*, *az írási műveletek* és *a sávszélesség* értékei minimális becsült érték. Az Azure Container Registry a teljesítmény növelésére törekszik a használatnak megfelelően.

<sup>3</sup>Egy [Docker-lekérés](https://docs.docker.com/registry/spec/api/#pulling-an-image) több olvasási műveletet jelent a rendszerkép rétegeinek számától, valamint a jegyzékfájl lekérésétől függően.

<sup>4</sup>Egy [Docker-leküldés](https://docs.docker.com/registry/spec/api/#pushing-an-image) több írási műveletet jelent a leküldendő rétegek számától függően. Egy `docker push` *olvasási műveletet* tartalmaz egy meglévő rendszerkép jegyzékfájljának lekéréséhez.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md