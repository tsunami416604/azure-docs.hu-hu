---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d62da82b4a4dd35532dd8776a9111689db469201
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448367"
---
## <a name="register-extensions"></a>Bővítmények regisztrálása

HTTP- és időzítő eseményindító kivételével Functions kötések verze modulu runtime a 2.x bővítménycsomagok vannak implementálva. A verzió 2.x verzióját az Azure Functions futtatókörnyezettel, explicit módon regisztrálnia kell a bővítmények a függvényekben használt kötési típusok. A kivételek, a HTTP-kötések és az időzítési eseményindítóktól bővítmények nem igénylő.

Választhatja a kötési bővítményeket külön-külön telepíti, vagy egy köteg kiterjesztéshivatkozásának souboru projektu host.json is hozzáadhat. Bővítmény csomagjaiból eltávolítja az esélye a csomag kompatibilitási problémák több kötési típus használatakor. Az ajánlott módszer regisztrálásához a kötési bővítményeket. Bővítmény csomagjaiból is eltávolítja a követelménynek, telepíti a .NET Core 2.x SDK. 

### <a name="extension-bundles"></a>bővítmény-csomagok

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

További tudnivalókért lásd: [regisztrálása az Azure Functions kötési bővítményeket](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Kötések a functions.json-fájl hozzáadása előtt hozzá kell adnia a host.json a bővítmény kötegeket.

### <a name="register-individual-extensions"></a>Az egyes bővítmények regisztrálása

Bővítmények, amelyek nem a csomag egyik gyermekszoftver telepítenie kell, ha manuálisan regisztrálhatja az egyes bővítménycsomagok adott kötések. 

> [!NOTE]
> Manuálisan kell regisztrálni extensions segítségével `func extensions install`, rendelkeznie kell a .NET Core SDK-t 2.x.

Miután frissítette a *function.json* -fájl, amely a függvénynek szüksége van, futtassa a következő parancsot a projektmappában összes kötését.

```bash
func extensions install
```

A parancs beolvassa a *function.json* fájlra kattintva látható, mely csomagokat kell, telepíti őket, és ismét létrehozza a bővítmények projekt. Az új kötések felveszi a jelenlegi verzió, hanem nem frissíti a meglévő kötéseit. Használja a `--force` lehetőséget, ha meglévő kötések a legújabb verzióra újakat telepítésekor.