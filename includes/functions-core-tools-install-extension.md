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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75564816"
---
## <a name="register-extensions"></a>Bővítmények regisztrálása

A HTTP-és időzítő-eseményindítók kivételével a függvények kötései a 2. x vagy újabb verziókban bővítmény-csomagokként vannak implementálva. A 2. x verzióban és a Azure Functions futtatókörnyezeten túl explicit módon regisztrálnia kell a függvényekben használt kötési típusok kiterjesztéseit. Ez alól kivételt képeznek a HTTP-kötések és az időzítő eseményindítók, amelyek nem igényelnek bővítményeket.

Dönthet úgy is, hogy külön telepíti a kötési bővítményeket, vagy hozzáadhat egy bővítményi csomagot a Host. JSON projektfájl-fájlhoz. A bővítményi csomagok több kötési típus használata esetén is megszüntetik a csomagok kompatibilitási problémáinak esélyét. A kötési bővítmények regisztrálásának ajánlott módja. A bővítményi csomagok szintén megszüntetik a .NET Core 2. x SDK telepítésének követelményét. 

### <a name="extension-bundles"></a>Kiterjesztési csomagok

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

További információ: [Azure functions kötési bővítmények regisztrálása](../articles/azure-functions/functions-bindings-register.md#extension-bundles). A function. JSON fájlhoz tartozó kötések hozzáadása előtt adja hozzá a kiterjesztési csomagokat a Host. JSON fájlhoz.

### <a name="register-individual-extensions"></a>Egyéni bővítmények regisztrálása

Ha olyan bővítményeket kell telepítenie, amelyek nem egy csomagban vannak, akkor manuálisan is regisztrálhat egyes bővítményeket a kötésekhez. 

> [!NOTE]
> A bővítmények a használatával `func extensions install`történő manuális regisztrálásához telepíteni kell a .net Core 2. x SDK-t.

Miután frissítette a *function. JSON* fájlt, hogy tartalmazza a függvény által igényelt összes kötést, futtassa a következő parancsot a Project mappában.

```bash
func extensions install
```

A parancs beolvassa a *function. JSON* fájlt, amelyből megtudhatja, hogy mely csomagok szükségesek, telepíti őket, és újraépíti a bővítmények projektjét. Az új kötéseket a jelenlegi verzióban adja hozzá, de nem frissíti a meglévő kötéseket. Új telepítések esetén a meglévő kötések frissítése a legújabb verzióra `--force` lehetőség használatával.
