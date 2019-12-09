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
ms.openlocfilehash: a050ce62f745591608249b41ba56992d8fd35204
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935900"
---
## <a name="register-extensions"></a>Bővítmények regisztrálása

A HTTP-és időzítő-eseményindítók kivételével a függvények kötései a 2. x vagy újabb verziókban bővítmény-csomagokként vannak implementálva. A 2. x verzióban és a Azure Functions futtatókörnyezeten túl explicit módon regisztrálnia kell a függvényekben használt kötési típusok kiterjesztéseit. Ez alól kivételt képeznek a HTTP-kötések és az időzítő eseményindítók, amelyek nem igényelnek bővítményeket.

Dönthet úgy is, hogy külön telepíti a kötési bővítményeket, vagy hozzáadhat egy bővítményi csomagot a Host. JSON projektfájl-fájlhoz. A bővítményi csomagok több kötési típus használata esetén is megszüntetik a csomagok kompatibilitási problémáinak esélyét. A kötési bővítmények regisztrálásának ajánlott módja. A bővítményi csomagok szintén megszüntetik a .NET Core 2. x SDK telepítésének követelményét. 

### <a name="extension-bundles"></a>Kiterjesztési csomagok

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

További információ: [Azure functions kötési bővítmények regisztrálása](../articles/azure-functions/functions-bindings-register.md#extension-bundles). A functions. JSON fájlhoz kötések hozzáadása előtt adja hozzá a kiterjesztési csomagokat a Host. JSON fájlhoz.

### <a name="register-individual-extensions"></a>Egyéni bővítmények regisztrálása

Ha olyan bővítményeket kell telepítenie, amelyek nem egy csomagban vannak, akkor manuálisan is regisztrálhat egyes bővítményeket a kötésekhez. 

> [!NOTE]
> A bővítmények `func extensions install`használatával történő manuális regisztrálásához telepíteni kell a .NET Core 2. x SDK-t.

Miután frissítette a *function. JSON* fájlt, hogy tartalmazza a függvény által igényelt összes kötést, futtassa a következő parancsot a Project mappában.

```bash
func extensions install
```

A parancs beolvassa a *function. JSON* fájlt, amelyből megtudhatja, hogy mely csomagok szükségesek, telepíti őket, és újraépíti a bővítmények projektjét. Az új kötéseket a jelenlegi verzióban adja hozzá, de nem frissíti a meglévő kötéseket. Az `--force` lehetőséggel frissítheti a meglévő kötéseket a legújabb verzióra új telepítések telepítésekor.