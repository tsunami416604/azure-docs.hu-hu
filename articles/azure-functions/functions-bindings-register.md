---
title: Azure Functions kötési bővítmények regisztrálása
description: Megtudhatja, hogyan regisztrálhat egy Azure Functions-kötési bővítményt a környezete alapján.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 1688fe848beb62731391bf4399a0dabec5265320
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277517"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions kötési bővítmények regisztrálása

Azure Functions 2. x verziójában a [kötések](./functions-triggers-bindings.md) külön csomagokként érhetők el a functions futtatókörnyezetből. Míg a .NET függvények NuGet-csomagokon keresztül érik el a kötéseket, a bővítmények lehetővé teszik más függvények számára, hogy a konfigurációs beállításokon keresztül hozzáférjenek az összes kötéshez.

Vegye figyelembe a kötési bővítményekkel kapcsolatos következő elemeket:

- A kötési kiterjesztések nincsenek explicit módon regisztrálva az 1. x függvényekben, kivéve, ha [a Visual Studióval hoz létre egy C# osztály-függvénytárat](#local-csharp).

- A HTTP-és időzítő-eseményindítók alapértelmezés szerint támogatottak, és nincs szükség bővítményre.

A következő táblázat azt mutatja be, hogy mikor és hogyan regisztrálja a kötéseket.

| Fejlesztési környezet |Regisztráció<br/> a functions 1. x  |Regisztráció<br/> a functions 2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatikus|Automatikus|
|Non-.NET-nyelvek vagy helyi Azure Core-eszközök fejlesztése|Automatikus|[Azure Functions Core Tools-és bővítmény-csomagok használata](#extension-bundles)|
|C#osztály könyvtára a Visual Studióval|[NuGet-eszközök használata](#vs)|[NuGet-eszközök használata](#vs)|
|C#a Visual Studio Code-ot használó osztály könyvtára|N/A|[A .NET Core parancssori felülete használata](#vs-code)|

## <a name="extension-bundles"></a>Kiterjesztési csomagok helyi fejlesztéshez

A bővítmények egy olyan központi telepítési technológia, amely lehetővé teszi, hogy a Function alkalmazáshoz egy kompatibilis függvények kötését adja hozzá. Az alkalmazás létrehozásakor a bővítmények előre meghatározott készlete lesz hozzáadva. A csomagban definiált kiterjesztési csomagok kompatibilisek egymással, ami segít elkerülni a csomagok közötti ütközéseket. Az alkalmazás Host. JSON fájljában engedélyezheti a bővítmények kötegeit.  

A bővítmények a functions futtatókörnyezet 2. x vagy újabb verzióival is használhatók. Helyi fejlesztés esetén győződjön meg arról, hogy a [Azure functions Core Tools](functions-run-local.md#v2)legújabb verzióját használja.

A Azure Functions Core Tools, a Visual Studio Code és a távoli buildek használatával a bővítmények helyi fejlesztéshez használhatók.

Ha nem használ bővítmény-csomagokat, a kötési bővítmények telepítése előtt telepítenie kell a .NET Core 2. x SDK-t a helyi számítógépen. A kiterjesztési csomagok ezt a követelményt a helyi fejlesztéshez is megszüntetik. 

A bővítmények használatához frissítse a *Host. JSON* fájlt, hogy tartalmazza a `extensionBundle`következő bejegyzését:
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="vs"></a>C\# osztály könyvtára a Visual Studióval

A **Visual Studióban**a Package Manager konzolon telepítheti a csomagokat az [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) paranccsal, ahogy az alábbi példában is látható:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Az adott kötéshez használt csomag neve az adott kötéshez tartozó hivatkozási cikkben található. Példaként tekintse meg a [Service Bus kötési útmutató csomagok szakaszát](functions-bindings-service-bus.md#functions-1x).

Cserélje le a példában szereplő `<TARGET_VERSION>`t a csomag egy adott verziójára, például `3.0.0-beta5`. Az érvényes verziók a [NuGet.org](https://nuget.org)-on található egyedi csomag oldalain találhatók. Az 1. x vagy 2. x függvényeknek megfelelő főverziók a kötésre vonatkozó hivatkozási cikkben vannak megadva.

Ha `Install-Package`t használ a kötésre való hivatkozáshoz, nem kell használni a [bővítmények kötegeit](#extension-bundles). Ez a megközelítés kifejezetten a Visual Studióban létrehozott osztályok könyvtáraira vonatkozik.

## <a name="vs-code"></a>C# osztály könyvtára a Visual Studio Code-ban

A **Visual Studio Code**-ban a parancssorban C# telepítsen egy Class Library-projekthez tartozó [](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) csomagokat a a .net Core parancssori felülete. Az alábbi példa bemutatja, hogyan adhat hozzá kötéseket:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

A a .NET Core parancssori felülete csak Azure Functions 2. x fejlesztéshez használható.

A `<BINDING_TYPE_NAME>` helyére írja be annak a csomagnak a nevét, amely a szükséges kötést tartalmazza. A kívánt kötési hivatkozásról szóló cikket a [támogatott kötések listájában](./functions-triggers-bindings.md#supported-bindings)találja.

Cserélje le a példában szereplő `<TARGET_VERSION>`t a csomag egy adott verziójára, például `3.0.0-beta5`. Az érvényes verziók a [NuGet.org](https://nuget.org)-on található egyedi csomag oldalain találhatók. Az 1. x vagy 2. x függvényeknek megfelelő főverziók a kötésre vonatkozó hivatkozási cikkben vannak megadva.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Az Azure Function trigger és a kötési példa](./functions-bindings-example.md)
