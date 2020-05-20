---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 19d0be6a032868c6683cd5b6bbfa7f07306171fb
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683439"
---
| Erőforrás | Basic | Standard | Prémium |
|---|---|---|---|
| <sup>1</sup> . tároló | 10 GiB | 100 GiB| 500 GiB |
| Képréteg maximális mérete | 200 GiB | 200 GiB | 200 GiB |
| ReadOps percenként<sup>2, 3</sup> | 1,000 | 3,000 | 10,000 |
| WriteOps percenként<sup>2, 4</sup> | 100 | 500 | 2000 |
| Sávszélesség letöltése MBps<sup>2</sup> | 30 | 60 | 100 |
| Feltöltési sávszélesség (MBps)<sup>2</sup> | 10 | 20 | 50 |
| Webhookok | 2 | 10 | 500 |
| Georeplikáció | N/A | N/A | [Támogatott][geo-replication] |
| Tartalommegbízhatóság | N/A | N/A | [Támogatott][content-trust] |
| Privát kapcsolat privát végpontokkal | N/A | N/A | [Támogatott][plink] |
| Szolgáltatási végpont VNet elérése | N/A | N/A | [Előnézet][vnet] |
| Felhasználó által kezelt kulcsok | N/A | N/A | [Támogatott][cmk] |
| Tárház – hatókörön belüli engedélyek | N/A | N/A | [Előnézet][token]|
| &bull;Tokenek | N/A | N/A | 20000 |
| &bull;Hatóköri térképek | N/A | N/A | 20000 |
| &bull;Adattárak/hatókör-leképezés | N/A | N/A | 500 |


<sup>1</sup> A megadott tárolási korlát az egyes rétegek *belefoglalt* tárterületének mennyisége. A fenti korlátokat meghaladó képtárolásért napi további díjat számítunk fel. A díjszabással kapcsolatos információkért lásd: [Azure Container Registry díjszabása][pricing].

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