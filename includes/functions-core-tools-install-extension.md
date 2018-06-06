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
ms.openlocfilehash: 6fb497a5b6da00dece43c7f41ea3c411f385a2ba
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726887"
---
Amikor helyileg funkciók fejlesztése a bővítmények segítségével az Azure Functions Core eszközök a Terminálszolgáltatások vagy a parancssorból is telepítheti. 

Miután frissítette a *function.json* fájlt a a kötéseket a függvény van szüksége, futtassa a `func extensions install` projektmappában parancsot. A parancs beolvassa a *function.json* fájlra, és mely csomagokat kell, és telepíti azokat.

Ha egy adott csomag verzióját telepíti, vagy a telepítendő csomagok szerkesztése előtt a *function.json* fájl létrehozásához használja a `func extensions install` parancs a csomag nevével, az alábbi példában látható módon:

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Cserélje le `<target_version>` a csomag megadott verzióval. A következő egyes csomagot lapokon felsorolt érvényes verziók [NuGet.org](https://nuget.org).
