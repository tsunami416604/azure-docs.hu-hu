---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75564816"
---
## <a name="register-extensions"></a>Bővítmények regisztrálása

A HTTP és időzítő eseményindítók kivételével a 2.x-es és újabb sorozatban futóidő-verzióban lévő függvénykötések bővítménycsomagokként vannak megvalósítva. Az Azure Functions futásidejű 2.x-es verziójában és azon túl explicit módon regisztrálnia kell a bővítményeket a függvényekben használt kötési típusokhoz. Ez alól kivételt képeznek a HTTP-kötések és az időzítő-eseményindítók, amelyek nem igényelnek bővítményeket.

Választhat, hogy a kötésbővítményeket egyenként telepíti, vagy hozzáadhat egy bővítményköteg-hivatkozást a host.json projektfájlhoz. A bővítménykötegek megszüntetik annak az esélyét, hogy több kötési típus használata esetén is problémamerüljön a csomagkompatibilitási problémák. Ez az ajánlott megközelítés a kötési kiterjesztések regisztrálásához. A bővítőcsomagok a .NET Core 2.x SDK telepítésének követelményét is megszüntetik. 

### <a name="extension-bundles"></a>Hosszabbító csomagok

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

További információ: [Az Azure Functions kötésbővítmények regisztrálása](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Mielőtt kötéseket ad hozzá a function.json fájlhoz, bővítménykötegeket kell hozzáadnia a host.json fájlhoz.

### <a name="register-individual-extensions"></a>Egyedi bővítmények regisztrálása

Ha olyan bővítményeket kell telepítenie, amelyek nem egy csomagban vannak, manuálisan regisztrálhatja az egyes bővítménycsomagokat az egyes kötésekhez. 

> [!NOTE]
> A bővítmények manuális regisztrálásához `func extensions install`a .

Miután frissítette a *function.json* fájlt, hogy tartalmazza a függvény összes kötését, futtassa a következő parancsot a projekt mappában.

```bash
func extensions install
```

A parancs beolvassa a *function.json* fájlt, hogy lássa, mely csomagokra van szüksége, telepíti őket, és újraépíti a bővítmények projektet. Hozzáadja az új kötéseket az aktuális verzióhoz, de nem frissíti a meglévő kötéseket. Ezzel `--force` a beállítással frissítheti a meglévő kötéseket a legújabb verzióra az újak telepítésekor.
