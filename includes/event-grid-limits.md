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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845972"
---
A következő korlátozások vonatkoznak Azure Event Grid rendszertémakörökre és az egyéni témakörökre, és *nem* az Event-tartományokra.

| Erőforrás | Korlát |
| --- | --- |
| Egyéni témakörök az Azure-előfizetések esetében | 100 |
| Esemény-előfizetések/témakör | 500 |
| Egyéni témakör közzétételi arányának (bejövő) | 5 000 esemény/másodperc/téma |
| Közzétételi kérelmek | 250 másodpercenként |
| Esemény mérete | 1 MB (több 64 KB-os eseményként kell fizetni) |

A következő korlátozások érvényesek az esemény-tartományokra.

| Erőforrás | Korlát |
| --- | --- |
| Témakörök/esemény tartománya | 100 000 |
| Esemény-előfizetések témakörben egy tartományon belül | 500 |
| Tartományi hatókörű esemény-előfizetések | 50 |
| Esemény-tartomány közzétételi sebessége (bejövő) | 5 000 esemény másodpercenként |
| Közzétételi kérelmek | 250 másodpercenként |
| Azure-előfizetéshez tartozó esemény-tartományok | 100 |