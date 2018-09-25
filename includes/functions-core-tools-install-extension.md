---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 09/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f1b53c53b1e5fb089eb9b8a9b816b11a1eea126d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044509"
---
A függvények helyi fejlesztésekor a bővítmények segítségével az Azure Functions Core Tools, a terminálról, vagy a parancssorból is telepítheti.

Miután frissítette a *function.json* -fájl, amely a függvénynek szüksége van, futtassa a következő parancsot a projektmappában összes kötését.

```bash
func extensions install
```

A parancs beolvassa a *function.json* fájlra kattintva látható, mely csomagokat kell, telepíti őket, és ismét létrehozza a bővítmények projekt. Az új kötések felveszi a jelenlegi verzió, hanem nem frissíti a meglévő kötéseit. Használja a `--force` lehetőséget, ha meglévő kötések a legújabb verzióra újakat telepítésekor.

Ha egy adott verzióját a csomag telepítéséhez, vagy szerkesztése előtt csomagokat telepíteni szeretné a *function.json* fájlt, használja a `func extensions install` parancsot a csomag nevével, az alábbi példában látható módon:

```bash
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Cserélje le `<target_version>` a csomaghoz, egy adott verziójával például `3.0.0-beta5`. Érvényes verzió szerepel az egyes csomagot oldalakon [NuGet.org](https://nuget.org).
