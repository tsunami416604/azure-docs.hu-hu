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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117158"
---
| Erőforrás | Basic | Standard | Prémium |
|---|---|---|---|
| <sup>Tárolás 1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maximális képréteg méret | 200 GiB | 200 GiB | 200 GiB |
| ReadOps percenként<sup>2, 3</sup> | 1,000 | 3,000 | 10,000 |
| WriteOps percenként<sup>2, 4</sup> | 100 | 500 | 2000 |
| Sávszélesség letöltése MBps<sup>2</sup> | 30 | 60 | 100 |
| Sávszélesség feltöltése MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhookok | 2 | 10 | 500 |
| Georeplikáció | N/A | N/A | [Támogatott][geo-replication] |
| Tartalommegbízhatóság | N/A | N/A | [Támogatott][content-trust] |
| Virtuális hálózati hozzáférés | N/A | N/A | [Előnézet][vnet] |
| Privát kapcsolat integrációja | N/A | N/A | [Előnézet][plink] |
| Felhasználó által kezelt kulcsok | N/A | N/A | [Előnézet][cmk] |
| Tárház hatókörrel rendelkező engedélyek | N/A | N/A | [Előnézet][token]|
| &bull;Tokenek | N/A | N/A | 20000 |
| &bull;Hatókör térképek | N/A | N/A | 20000 |
| &bull;Adattárak hatókörtérképenként | N/A | N/A | 500 |


<sup>1 1</sup> A megadott tárolási korlátok az egyes rétegek *hez tartozó* tárterület mennyisége. A fenti korlátok feletti képtárolásért GiB-nként további napi díjat számítunk fel. A díjszabásról az [Azure Container Registry díjszabása][pricing]című témakörben talál.

<sup>2</sup>*ReadOps*, *WriteOps*és *sávszélesség* minimális becslések. Az Azure Container Registry arra törekszik, hogy javítsa a teljesítményt, mint a használat megköveteli.

<sup>3. 20 0</sup> A [docker-lekérések](https://docs.docker.com/registry/spec/api/#pulling-an-image) a rendszerkép rétegeinek száma, valamint a jegyzékbe olvasási műveletek alapján több olvasási műveletre fordít.

<sup>4.</sup> A [docker-leküldéses](https://docs.docker.com/registry/spec/api/#pushing-an-image) fordítása több írási művelet, a lelökött rétegek száma alapján. A `docker push` tartalmazza *a ReadOps* egy meglévő lemezkép jegyzékfájljának lekéréséhez.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md