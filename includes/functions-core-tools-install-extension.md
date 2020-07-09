---
title: fájlbefoglalás
description: fájlbefoglalás
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75564816"
---
## <a name="register-extensions"></a>Bővítmények regisztrálása

A HTTP-és időzítő-eseményindítók kivételével a függvények kötései a 2. x vagy újabb verziókban bővítmény-csomagokként vannak implementálva. A 2. x verzióban és a Azure Functions futtatókörnyezeten túl explicit módon regisztrálnia kell a függvényekben használt kötési típusok kiterjesztéseit. Ez alól kivételt képeznek a HTTP-kötések és az időzítő eseményindítók, amelyek nem igényelnek bővítményeket.

Beállíthatja, hogy a kötési bővítmények egyenként legyenek telepítve, vagy hozzáadhat egy bővítményi csomagot a Project-fájl host.js. A bővítményi csomagok több kötési típus használata esetén is megszüntetik a csomagok kompatibilitási problémáinak esélyét. A kötési bővítmények regisztrálásának ajánlott módja. A bővítményi csomagok szintén megszüntetik a .NET Core 2. x SDK telepítésének követelményét. 

### <a name="extension-bundles"></a>Kiterjesztési csomagok

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

További információ: [Azure functions kötési bővítmények regisztrálása](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Ahhoz, hogy a fájlhoz function.jskötéseket vegyen fel, hozzá kell adnia egy bővítményt a host.jshoz.

### <a name="register-individual-extensions"></a>Egyéni bővítmények regisztrálása

Ha olyan bővítményeket kell telepítenie, amelyek nem egy csomagban vannak, akkor manuálisan is regisztrálhat egyes bővítményeket a kötésekhez. 

> [!NOTE]
> A bővítmények a használatával történő manuális regisztrálásához `func extensions install` telepíteni kell a .net Core 2. x SDK-t.

Miután frissítette a *function.js* fájlt, hogy tartalmazza a függvény által igényelt összes kötést, futtassa a következő parancsot a Project mappában.

```bash
func extensions install
```

A parancs beolvassa a *function.jsa* fájlon, hogy megtekintse a szükséges csomagokat, telepíti őket, és újraépíti a bővítmények projektjét. Az új kötéseket a jelenlegi verzióban adja hozzá, de nem frissíti a meglévő kötéseket. `--force`Új telepítések esetén a meglévő kötések frissítése a legújabb verzióra lehetőség használatával.
