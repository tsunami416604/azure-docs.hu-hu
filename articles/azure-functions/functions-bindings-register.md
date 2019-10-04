---
title: Azure Functions kötési bővítmények regisztrálása
description: Megtudhatja, hogyan regisztrálhat egy Azure Functions-kötési bővítményt a környezete alapján.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 93ced443a73d5499d8b305770c3c866c26d540f0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086469"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions kötési bővítmények regisztrálása

Azure Functions 2. x verziójában a [kötések](./functions-triggers-bindings.md) külön csomagokként érhetők el a functions futtatókörnyezetből. Míg a .NET függvények NuGet-csomagokon keresztül érik el a kötéseket, a bővítmények lehetővé teszik más függvények számára, hogy a konfigurációs beállításokon keresztül hozzáférjenek az összes kötéshez.

Vegye figyelembe a kötési bővítményekkel kapcsolatos következő elemeket:

- A kötési kiterjesztések nincsenek explicit módon regisztrálva az 1. x függvényekben, kivéve, ha [a Visual Studióval C# hoz létre egy osztály-függvénytárat](#local-csharp).

- A HTTP-és időzítő-eseményindítók alapértelmezés szerint támogatottak, és nincs szükség bővítményre.

A következő táblázat azt mutatja be, hogy mikor és hogyan regisztrálja a kötéseket.

| Fejlesztési környezet |Regisztráció<br/> a functions 1. x  |Regisztráció<br/> a functions 2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatikus|Automatikus|
|Non-.NET-nyelvek vagy helyi Azure Core-eszközök fejlesztése|Automatikus|[Azure Functions Core Tools-és bővítmény-csomagok használata](#extension-bundles)|
|C#osztály könyvtára a Visual Studióval|[NuGet-eszközök használata](#vs)|[NuGet-eszközök használata](#vs)|
|C#a Visual Studio Code-ot használó osztály könyvtára|–|[A .NET Core parancssori felülete használata](#vs-code)|

## <a name="extension-bundles"></a>Kiterjesztési csomagok helyi fejlesztéshez

A bővítmények a 2. x verzió helyi fejlesztési technológiája, amely lehetővé teszi, hogy a Function app-projekthez hozzáadjon egy kompatibilis függvények kötési bővítményeket. Ezeket a kiterjesztési csomagokat a rendszer az Azure-ba történő üzembe helyezéskor felveszi a központi telepítési csomagba. A csomagok a Microsoft által közzétett összes kötést a *Host. JSON* fájl egy beállításával teszik elérhetővé. A csomagban definiált kiterjesztési csomagok kompatibilisek egymással, ami segít elkerülni a csomagok közötti ütközéseket. Helyi fejlesztés esetén győződjön meg arról, hogy a [Azure functions Core Tools](functions-run-local.md#v2)legújabb verzióját használja.

Az Azure Functions Core Tools vagy a Visual Studio Code használatával minden helyi fejlesztéshez használhat bővítmény-csomagokat.

Ha nem használ bővítmény-csomagokat, a kötési bővítmények telepítése előtt telepítenie kell a .NET Core 2. x SDK-t a helyi számítógépen. A csomagok ezt a követelményt a helyi fejlesztéshez is megszüntetik. 

A bővítmények használatához frissítse a *Host. JSON* fájlt, hogy tartalmazza a következő bejegyzést `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

A következő tulajdonságok érhetők el a `extensionBundle`ben:

| Tulajdonság | Leírás |
| -------- | ----------- |
| **`id`** | Microsoft Azure functions bővítmények névterei. |
| **`version`** | A telepítendő csomag verziószáma. A functions Runtime mindig kiválasztja a verziószám vagy az intervallum által meghatározott maximálisan megengedett verziót. A fenti verzió értéke lehetővé teszi, hogy az összes köteg verziója a 1.0.0-ból, de nem tartalmazza a 2.0.0. További információ: a [verziók tartományának megadására szolgáló intervallum jelölése](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). |

A csomag verziójának növekménye csomagokként a köteg változása. A főverzió módosításai akkor fordulnak elő, ha a csomagban lévő csomagok nagyobb verziójúak, ami általában egybeesik a functions futtatókörnyezet főverziójának változásával.  

Az alapértelmezett csomag által telepített bővítmények jelenleg a [Extensions. JSON fájlban](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)vannak felsorolva.

<a name="local-csharp"></a>

## <a name="vs"></a>C\# osztályú könyvtár a Visual Studióval

A **Visual Studióban**a Package Manager konzolon telepítheti a csomagokat az [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) paranccsal, ahogy az alábbi példában is látható:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Az adott kötéshez használt csomag neve az adott kötéshez tartozó hivatkozási cikkben található. Példaként tekintse meg a [Service Bus kötési útmutató csomagok szakaszát](functions-bindings-service-bus.md#packages---functions-1x).

Cserélje `<TARGET_VERSION>` le a példát a csomag egy adott verziójára, `3.0.0-beta5`például:. Az érvényes verziók a [NuGet.org](https://nuget.org)-on található egyedi csomag oldalain találhatók. Az 1. x vagy 2. x függvényeknek megfelelő főverziók a kötésre vonatkozó hivatkozási cikkben vannak megadva.

Ha egy kötésre mutató hivatkozást használ `Install-Package` , nem szükséges a [bővítmények](#extension-bundles)használata. Ez a megközelítés kifejezetten a Visual Studióban létrehozott osztályok könyvtáraira vonatkozik.

## <a name="vs-code"></a>C# osztály könyvtára a Visual Studio Code-ban

> [!NOTE]
> Javasoljuk, hogy [](#extension-bundles) a bővítmények használatával a függvények automatikusan telepítsék a kötési bővítmények kompatibilis készletét.

A **Visual Studio Code**-ban a parancssorban C# telepítsen egy Class Library-projekthez tartozó [](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) csomagokat a a .net Core parancssori felülete. Az alábbi példa bemutatja, hogyan adhat hozzá kötéseket:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

A a .NET Core parancssori felülete csak Azure Functions 2. x fejlesztéshez használható.

Cserélje `<BINDING_TYPE_NAME>` le a (z) helyére a kívánt kötésre vonatkozó hivatkozási cikkben megadott csomag nevét. A kívánt kötési hivatkozásról szóló cikket a [támogatott kötések listájában](./functions-triggers-bindings.md#supported-bindings)találja.

Cserélje `<TARGET_VERSION>` le a példát a csomag egy adott verziójára, `3.0.0-beta5`például:. Az érvényes verziók a [NuGet.org](https://nuget.org)-on található egyedi csomag oldalain találhatók. Az 1. x vagy 2. x függvényeknek megfelelő főverziók a kötésre vonatkozó hivatkozási cikkben vannak megadva.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Az Azure Function trigger és a kötési példa](./functions-bindings-example.md)
