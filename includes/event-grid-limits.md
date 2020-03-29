---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ee80c04a6365c2cf337c4033a90df8d2993c299d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76845972"
---
A következő korlátozások az Azure Event Grid rendszertémakörökre és egyéni témakörökre vonatkoznak, *nem* az eseménytartományokra.

| Erőforrás | Korlát |
| --- | --- |
| Egyéni témakörök Azure-előfizetésenként | 100 |
| Esemény-előfizetések témánként | 500 |
| Egyéni témakör közzétételi aránya (be- és visszafordítás) | 5000 esemény másodpercenként témánként |
| Kérelmek közzététele | 250 másodpercenként |
| Esemény mérete | 1 MB (több 64 KB-os eseményként számítva) |

A következő korlátozások csak az eseménytartományokra vonatkoznak.

| Erőforrás | Korlát |
| --- | --- |
| Témakörök eseménytartományonként | 100 000 |
| Esemény-előfizetések témakörenként egy tartományon belül | 500 |
| Tartományhatókör-esemény-előfizetések | 50 |
| Eseménytartomány közzétételi aránya (be- és visszakelődés) | 5000 esemény másodpercenként |
| Kérelmek közzététele | 250 másodpercenként |
| Eseménytartományok Azure-előfizetésenként | 100 |