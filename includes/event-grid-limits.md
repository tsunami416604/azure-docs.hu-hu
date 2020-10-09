---
title: fájlbefoglalás
description: fájlbefoglalás
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b884bc72381c98af77f2f49336f3dd5762c68734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91859576"
---
A következő korlátozások vonatkoznak a Azure Event Grid **témakörökre** (a System, az Custom és a partner témakörökre). 

| Erőforrás | Korlát |
| --- | --- |
| Egyéni témakörök az Azure-előfizetések esetében | 100 |
| Esemény-előfizetések/témakör | 500 |
| Közzétételi arány egyéni vagy partneri témakörben (bejövő forgalom) | 5 000 esemény/mp vagy 1 MB/s (amelyik előbb teljesül) |
| Esemény mérete | 1 MB  |
| Privát végponti kapcsolatok/témakör  | 64 | 
| IP-tűzfalszabályok egy témakörben | 16 | 

A következő korlátozások vonatkoznak Azure Event Grid **tartományokra**. 

| Erőforrás | Korlát |
| --- | --- |
| Témakörök/esemény tartománya | 100.000 |
| Esemény-előfizetések témakörben egy tartományon belül | 500 |
| Tartományi hatókörű esemény-előfizetések | 50 |
| Esemény-tartomány közzétételi sebessége (bejövő) | 5 000 esemény/mp vagy 1 MB/s (amelyik előbb teljesül) |
| Azure-előfizetéshez tartozó esemény-tartományok | 100 |
| Privát végponti kapcsolatok tartomány szerint | 64 | 
| IP-tűzfalszabályok tartományon belüli száma | 16 | 


