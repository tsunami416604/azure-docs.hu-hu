---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 11/05/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6707e844948ac76d4cec29faf69d80b3c9cb3c0f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392419"
---
| Erőforrás | Basic | Standard | Prémium |
|---|---|---|---|
| <sup>1</sup> . tároló | 10 GiB | 100 GiB| 500 GiB |
| Képréteg maximális mérete | 200 GiB | 200 GiB | 200 GiB |
| ReadOps percenként<sup>2, 3</sup> | 1,000 | 3,000 | 10,000 |
| WriteOps percenként<sup>2, 4</sup> | 100 | 500 | 2,000 |
| Sávszélesség letöltése MBps<sup>2</sup> | 30 | 60 | 100 |
| Feltöltési sávszélesség (MBps)<sup>2</sup> | 10 | 20 | 50 |
| Webhookok | 2 | 10 | 500 |
| Georeplikáció | – | – | [Támogatott][geo-replication] |
| Tartalommegbízhatóság | – | – | [Támogatott][content-trust] |
| Virtuális hálózati hozzáférés | – | – | [Előzetes verzió][vnet] |
| Tárház – hatókörön belüli engedélyek | – | – | [Előzetes verzió][token]|
| &bull; tokenek | – | – | 20 000 |
| &bull; hatókörének leképezése | – | – | 20 000 |
| &bull; adattárak/hatókör-leképezés | – | – | 500 |


<sup>1</sup> A megadott tárolási korlát az egyes rétegek *belefoglalt* tárterületének mennyisége. A fenti korlátokat meghaladó képtárolásért napi további díjat számítunk fel. A díjszabással kapcsolatos információkért lásd: [Azure Container Registry díjszabása][pricing].

<sup>2</sup>a*ReadOps*, a *WriteOps*és a *sávszélesség* minimális becslés. Azure Container Registry a használathoz szükséges teljesítmény növelésére törekszik.

<sup>3</sup> A [Docker lekérése](https://docs.docker.com/registry/spec/api/#pulling-an-image) több olvasási műveletre is vonatkozik a rendszerkép rétegeinek száma és a jegyzékfájl beolvasása alapján.

<sup>4</sup> A [Docker leküldése](https://docs.docker.com/registry/spec/api/#pushing-an-image) több írási műveletre is vonatkozik a leküldhető rétegek száma alapján. A `docker push` tartalmazza a *ReadOps* egy meglévő rendszerkép jegyzékfájljának lekéréséhez.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md