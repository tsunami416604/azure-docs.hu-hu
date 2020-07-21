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
ms.openlocfilehash: 3f68ca0fc577e6cf3f896ede0418f11f59756701
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512613"
---
| Erőforrás | Alapszintű | Standard | Prémium |
|---|---|---|---|
| Tartalmazott tároló<sup>1</sup> (GIB) | 10 | 100 | 500 |
| Tárolási korlát (TiB) | 20| 20 | 20 |
| Képréteg maximális mérete (GiB) | 200 | 200 | 200 |
| ReadOps percenként<sup>2, 3</sup> | 1,000 | 3,000 | 10,000 |
| WriteOps percenként<sup>2, 4</sup> | 100 | 500 | 2000 |
| Sávszélesség letöltése MBps<sup>2</sup> | 30 | 60 | 100 |
| Feltöltési sávszélesség (MBps)<sup>2</sup> | 10 | 20 | 50 |
| Webhookok | 2 | 10 | 500 |
| Georeplikáció | n.a. | n.a. | [Támogatott][geo-replication] |
| Tartalommegbízhatóság | n.a. | n.a. | [Támogatott][content-trust] |
| Privát kapcsolat privát végpontokkal | n.a. | n.a. | [Támogatott][plink] |
| &bull;Privát végpontok | n.a. | n.a. | 10 |
| Szolgáltatási végpont VNet elérése | n.a. | n.a. | [Előnézet][vnet] |
| Felhasználó által kezelt kulcsok | n.a. | n.a. | [Támogatott][cmk] |
| Tárház – hatókörön belüli engedélyek | n.a. | n.a. | [Előnézet][token]|
| &bull;Tokenek | n.a. | n.a. | 20 000 |
| &bull;Hatóköri térképek | n.a. | n.a. | 20 000 |
| &bull;Adattárak/hatókör-leképezés | n.a. | n.a. | 500 |


<sup>1</sup> tárterület az egyes szintek napi díja. A további tárterületért a napi rendszerességgel, a maximális tárolási korlátig érvényes díjat számítunk fel. A díjszabással kapcsolatos információkért lásd: [Azure Container Registry díjszabása][pricing].

<sup>2</sup>a*ReadOps*, a *WriteOps*és a *sávszélesség* minimális becslés. Azure Container Registry a használathoz szükséges teljesítmény növelésére törekszik.

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