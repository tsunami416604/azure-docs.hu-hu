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
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063748"
---
Amikor helyileg funkciók fejlesztése a bővítmények segítségével az Azure Functions Core eszközök a Terminálszolgáltatások vagy a parancssorból is telepítheti. 

Miután frissítette a *function.json* fájlt a a kötéseket a függvény van szüksége, futtassa a `func extensions install` projektmappában parancsot. A parancs beolvassa a *function.json* fájlra, és mely csomagokat kell, és telepíti azokat.

Ha egy adott csomag verzióját telepíti, vagy a telepítendő csomagok szerkesztése előtt a *function.json* fájl létrehozásához használja a `func extensions install` parancs a csomag nevével, az alábbi példában látható módon:

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Cserélje le `<target_version>` a csomaghoz, az adott verzióval rendelkező például `3.0.0-beta5`. A következő egyes csomagot lapokon felsorolt érvényes verziók [NuGet.org](https://nuget.org).
