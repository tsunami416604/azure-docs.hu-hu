---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 05/29/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 942b9bdf0201acaefe3333bcf928772899b9bdc2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34665073"
---
| Erőforrás | Alapszintű | Standard | Prémium |
|---|---|---|---|---|
| Storage | 10 GiB | 100 GiB| 500 GiB |
| Maximális réteg képméret | 20 giB | 20 giB | 50 giB |
| Percenként ReadOps<sup>1, 2</sup> | 1000 | 3000 | 10000 |
| Percenként WriteOps<sup>1, 3</sup> | 100 | 500 | 2000 |
| Töltse le a MB/s sávszélesség<sup>1</sup> | 30 | 60 | 100 |
| Töltse fel a MB/s sávszélesség<sup>1</sup> | 10 | 20 | 50 |
| Webhookok | 2 | 10 | 100 |
| Georeplikáció | – | – | [Támogatott](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *WriteOps*, és *sávszélesség* minimális hozzávetőlegesek. ACR nagy hangsúlyt fektet használati van szüksége a teljesítmény javítása érdekében.

<sup>2</sup> [docker lekéréses](https://docs.docker.com/registry/spec/api/#pulling-an-image) az eszköz több olvasási műveletek a lemezkép, valamint a jegyzék beolvasása rétegek száma alapján.

<sup>3</sup> [docker leküldéses](https://docs.docker.com/registry/spec/api/#pushing-an-image) az eszköz több írási műveleteket, rétegeket, amelyeket a leküldött értesítést kell száma alapján. A `docker push` tartalmaz *ReadOps* egy meglévő lemezképet jegyzékfájljának beolvasása.