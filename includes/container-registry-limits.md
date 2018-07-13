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
ms.openlocfilehash: 92a5d162e7a0b2c752a2f8e9c5941edf43e539e3
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991089"
---
| Erőforrás | Alapszintű | Standard | Prémium |
|---|---|---|---|---|
| Storage | 10 GiB | 100 GiB| 500 GiB |
| Maximális képméret réteg | 20 giB | 20 giB | 50 GB |
| Percenkénti ReadOps<sup>1, 2</sup> | 1,000 | 3,000 | 10,000 |
| Írási műveletek száma percenként<sup>1, 3</sup> | 100 | 500 | 2,000 |
| Töltse le a sávszélesség MB/s<sup>1</sup> | 30 | 60 | 100 |
| Töltse fel a sávszélesség MB/s<sup>1</sup> | 10 | 20 | 50 |
| Webhookok | 2 | 10 | 100 |
| Georeplikáció | – | – | [Támogatott](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *írási műveletek*, és *sávszélesség* minimális becslések. ACR nagy hangsúlyt fektet a teljesítmény javítása, a használatához.

<sup>2</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) a rendszer lefordítja arra több olvasási műveletek a lemezképet, valamint a manifest lekérés a rétegek száma alapján.

<sup>3</sup> [docker leküldéses](https://docs.docker.com/registry/spec/api/#pushing-an-image) a rendszer lefordítja arra, hogy kell lehet leküldeni a rétegek száma alapján, több írási műveleteket. A `docker push` tartalmaz *ReadOps* beolvasni a meglévő rendszerképet jegyzékfájl.
