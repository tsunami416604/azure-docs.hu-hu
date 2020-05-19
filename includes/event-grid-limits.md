---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 05/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 220759e8ed31c091887bd55f8d12aa4cc03a065f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590281"
---
A következő korlátozások vonatkoznak Azure Event Grid rendszertémakörökre és az egyéni témakörökre, és *nem* az Event-tartományokra.

| Erőforrás | Korlát |
| --- | --- |
| Egyéni témakörök az Azure-előfizetések esetében | 100 |
| Esemény-előfizetések/témakör | 500 |
| Egyéni témakör közzétételi arányának (bejövő) | 5 000 esemény/másodperc/téma |
| Közzétételi kérelmek | 250 másodpercenként |
| Esemény mérete | 1 MB. A műveletekre azonban 64 KB-os növekmények vonatkoznak. Így a 64 KB-nál több esemény is felmerül, mintha több esemény lenne. Például az 130 KB-os esemény olyan műveleteket von maga után, mintha 3 különálló esemény lenne.  |
| Témakörök/esemény tartománya | 100 000 |
| Esemény-előfizetések témakörben egy tartományon belül | 500 |
| Tartományi hatókörű esemény-előfizetések | 50 |
| Esemény-tartomány közzétételi sebessége (bejövő) | 5 000 esemény másodpercenként |
| Egy esemény tartományának közzétételi kérelmei | 250 másodpercenként |
| Azure-előfizetéshez tartozó esemény-tartományok | 100 |
| Privát végponti kapcsolatok témakör vagy tartomány szerint | 64 | 
| IP-tűzfalszabályok témakör vagy tartomány szerint | 16 | 