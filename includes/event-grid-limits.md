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
ms.openlocfilehash: 3f94481e6a8550479788d92c744327e1dc3b58c4
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "66376919"
---
A következő korlátozások vonatkoznak Azure Event Grid rendszertémakörökre és az egyéni témakörökre, és *nem* az Event-tartományokra.

| Resource | Korlát |
| --- | --- |
| Egyéni témakörök az Azure-előfizetések esetében | 100 |
| Esemény-előfizetések/témakör | 500 |
| Egyéni témakör közzétételi arányának (bejövő) | 5 000 esemény/másodperc/téma |
| Közzétételi kérelmek | 250 másodpercenként |
| Esemény mérete | A 64 KB-os verziójának támogatása általánosan elérhető (GA). Az 1 MB-os támogatás jelenleg előzetes verzióban érhető el. |

A következő korlátozások érvényesek az esemény-tartományokra.

| Resource | Korlát |
| --- | --- |
| Témakörök/esemény tartománya | 1 000 a nyilvános előzetes verzióban |
| Esemény-előfizetések témakörben egy tartományon belül | 50 a nyilvános előzetes verzióban |
| Tartományi hatókörű esemény-előfizetések | 50 a nyilvános előzetes verzióban |
| Esemény-tartomány közzétételi sebessége (bejövő) | 5 000 esemény másodpercenként a nyilvános előzetes verzióban |
| Közzétételi kérelmek | 250 másodpercenként |