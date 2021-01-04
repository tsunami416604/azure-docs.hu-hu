---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 06/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3fe1b966b56142fd312850ac5d77839b7d9db434
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2020
ms.locfileid: "97706224"
---
| Erőforrás | Alapszintű | Standard | Prémium |
|---|---|---|---|
| Tartalmazott tároló<sup>1</sup> (GIB) | 10 | 100 | 500 |
| Tárolási korlát (TiB) | 20| 20 | 20 |
| Képréteg maximális mérete (GiB) | 200 | 200 | 200 |
| ReadOps percenként<sup>2, 3</sup> | 1,000 | 3,000 | 10,000 |
| WriteOps percenként<sup>2, 4</sup> | 100 | 500 | 2000 |
| Sávszélesség letöltése Mbps<sup>2</sup> | 30 | 60 | 100 |
| Feltöltési sávszélesség (Mbps)<sup>2</sup> | 10 | 20 | 50 |
| Webhookok | 2 | 10 | 500 |
| Georeplikáció | N.A. | N.A. | [Támogatott][geo-replication] |
| Rendelkezésreállási zónák | N.A. | N.A. | [Előnézet][zones] |
| Tartalommegbízhatóság | N.A. | N.A. | [Támogatott][content-trust] |
| Privát kapcsolat privát végpontokkal | N.A. | N.A. | [Támogatott][plink] |
| &bull; Privát végpontok | N.A. | N.A. | 10 |
| Szolgáltatási végpont VNet elérése | N.A. | N.A. | [Előnézet][vnet] |
| Felhasználó által kezelt kulcsok | N.A. | N.A. | [Támogatott][cmk] |
| Tárház – hatókörön belüli engedélyek | N.A. | N.A. | [Előnézet][token]|
| &bull; Tokenek | N.A. | N.A. | 20 000 |
| &bull; Hatóköri térképek | N.A. | N.A. | 20 000 |
| &bull; Adattárak/hatókör-leképezés | N.A. | N.A. | 500 |


<sup>1</sup> tárterület az egyes szintek napi díja. A további tárterületért a napi rendszerességgel, a maximális tárolási korlátig érvényes díjat számítunk fel. A díjszabással kapcsolatos információkért lásd: [Azure Container Registry díjszabása][pricing].

<sup>2</sup>a *ReadOps*, a *WriteOps* és a *sávszélesség* minimális becslés. Azure Container Registry a használathoz szükséges teljesítmény növelésére törekszik.

<sup>3</sup> A [Docker lekérése](https://docs.docker.com/registry/spec/api/#pulling-an-image) több olvasási műveletre is vonatkozik a rendszerkép rétegeinek száma és a jegyzékfájl beolvasása alapján.

<sup>4</sup> A [Docker leküldése](https://docs.docker.com/registry/spec/api/#pushing-an-image) több írási műveletre is vonatkozik a leküldhető rétegek száma alapján. A `docker push` tartalmaz egy meglévő rendszerkép jegyzékfájljának beolvasására szolgáló *ReadOps* .

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md
[zones]: ../articles/container-registry/zone-redundancy.md
