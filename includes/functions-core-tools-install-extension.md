---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d166a77a0636efea3b63660fde2187e3f2ec15c0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38943947"
---
A függvények helyi fejlesztésekor a bővítmények segítségével az Azure Functions Core Tools, a terminálról, vagy a parancssorból is telepítheti. 

Miután frissítette a *function.json* -fájl a kötéseket, hogy a függvény van szüksége, futtassa a `func extensions install` parancsot a projektmappában. A parancs beolvassa a *function.json* fájlra kattintva látható, mely csomagokat kell, és telepíti őket.

Ha egy adott verzióját a csomag telepítéséhez, vagy szerkesztése előtt csomagokat telepíteni szeretné a *function.json* fájlt, használja a `func extensions install` parancsot a csomag nevével, az alábbi példában látható módon:

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Cserélje le `<target_version>` a csomaghoz, egy adott verziójával például `3.0.0-beta5`. Érvényes verzió szerepel az egyes csomagot oldalakon [NuGet.org](https://nuget.org).
