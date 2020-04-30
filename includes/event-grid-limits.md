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
ms.openlocfilehash: 5992726893b722b0aa46c976a0167793f5ee6bb4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82132294"
---
A következő korlátozások vonatkoznak Azure Event Grid rendszertémakörökre és az egyéni témakörökre, és *nem* az Event-tartományokra.

| Erőforrás | Korlát |
| --- | --- |
| Egyéni témakörök az Azure-előfizetések esetében | 100 |
| Esemény-előfizetések/témakör | 500 |
| Egyéni témakör közzétételi arányának (bejövő) | 5 000 esemény/másodperc/téma |
| Közzétételi kérelmek | 250 másodpercenként |
| Esemény mérete | 1 MB. A műveletekre azonban 64 KB-os növekmények vonatkoznak. Így a 64 KB-nál több esemény is felmerül, mintha több esemény lenne. Például az 130 KB-os esemény olyan műveleteket von maga után, mintha 3 különálló esemény lenne.  |

A következő korlátozások érvényesek az esemény-tartományokra.

| Erőforrás | Korlát |
| --- | --- |
| Témakörök/esemény tartománya | 100 000 |
| Esemény-előfizetések témakörben egy tartományon belül | 500 |
| Tartományi hatókörű esemény-előfizetések | 50 |
| Esemény-tartomány közzétételi sebessége (bejövő) | 5 000 esemény másodpercenként |
| Közzétételi kérelmek | 250 másodpercenként |
| Azure-előfizetéshez tartozó esemény-tartományok | 100 |