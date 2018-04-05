---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 03/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 575483192954f4e05db50e701e223829e041cffc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
| Erőforrás | Alapszintű | Standard | Prémium |
|---|---|---|---|---|
| Tárolás | 10 GiB | 100 GiB| 500 GiB |
| Percenként ReadOps<sup>1, 2</sup> | 1000 | 3000 | 10000 |
| Percenként WriteOps<sup>1, 3</sup> | 100 | 500 | 2000 |
| Töltse le a MB/s sávszélesség<sup>1</sup> | 30 | 60 | 100 |
| Töltse fel a MB/s sávszélesség<sup>1</sup> | 10 | 20 | 50 |
| Webhookok | 2 | 10 | 100 |
| Georeplikáció | – | – | [Támogatott *(előzetes verzió)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *WriteOps*, és *sávszélesség* minimális hozzávetőlegesek. ACR nagy hangsúlyt fektet használati van szüksége a teljesítmény javítása érdekében.

<sup>2</sup> [docker lekéréses](https://docs.docker.com/registry/spec/api/#pulling-an-image) az eszköz több olvasási műveletek a lemezkép, valamint a jegyzék beolvasása rétegek száma alapján.

<sup>3</sup> [docker leküldéses](https://docs.docker.com/registry/spec/api/#pushing-an-image) az eszköz több írási műveleteket, rétegeket, amelyeket a leküldött értesítést kell száma alapján. A `docker push` tartalmaz *ReadOps* egy meglévő lemezképet jegyzékfájljának beolvasása.