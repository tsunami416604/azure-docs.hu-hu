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
ms.openlocfilehash: 890095718833c90a6764d0799d2081b393b1d0bd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887780"
---
A következő korlátozások vonatkoznak Azure Event Grid rendszertémakörökre és az egyéni témakörökre, és *nem* az Event-tartományokra.

| Erőforrás | Korlát |
| --- | --- |
| Egyéni témakörök az Azure-előfizetések esetében | 100 |
| Esemény-előfizetések/témakör | 500 |
| Egyéni témakör közzétételi arányának (bejövő) | 5 000 esemény/másodperc/téma |
| Közzétételi kérelmek | 250 másodpercenként |
| Esemény mérete | A 64 KB-os verziójának támogatása általánosan elérhető (GA). Az 1 MB-os támogatás jelenleg előzetes verzióban érhető el. |

A következő korlátozások érvényesek az esemény-tartományokra.

| Erőforrás | Korlát |
| --- | --- |
| Témakörök/esemény tartománya | 100 000 |
| Esemény-előfizetések témakörben egy tartományon belül | 500 |
| Tartományi hatókörű esemény-előfizetések | 50 |
| Esemény-tartomány közzétételi sebessége (bejövő) | 5 000 esemény másodpercenként |
| Közzétételi kérelmek | 250 másodpercenként |
| Azure-előfizetéshez tartozó esemény-tartományok | 100 |