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
ms.openlocfilehash: e084256d9c2043d4382ca180ef3178175b301367
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745652"
---
A következő korlátozások vonatkoznak a Azure Event Grid **témakörökre** (a System, az Custom és a partner témakörökre). 

| Erőforrás | Korlát |
| --- | --- |
| Egyéni témakörök az Azure-előfizetések esetében | 100 |
| Esemény-előfizetések/témakör | 500 |
| Közzétételi arány egyéni vagy partneri témakörben (bejövő forgalom) | 5 000 esemény/mp vagy 1 MB/s (amelyik előbb teljesül)<br/>A rendszertémakörökre nem vonatkozik. |
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


