---
title: Az Azure Functions kötésbővítményeinek regisztrálása
description: Ismerje meg az Azure Functions kötésbővítmény regisztrálását a környezet alapján.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 1688fe848beb62731391bf4399a0dabec5265320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277517"
---
# <a name="register-azure-functions-binding-extensions"></a>Az Azure Functions kötésbővítményeinek regisztrálása

Az Azure Functions 2.x-es [verziójában a kötések](./functions-triggers-bindings.md) a függvények futásidejű különálló csomagjaiként érhetők el. Míg a .NET függvények nuget csomagokon keresztül férnek hozzá a kötésekhez, a bővítménykötegek lehetővé teszik, hogy más funkciók egy konfigurációs beállításon keresztül hozzáférjenek az összes kötéshez.

Vegye figyelembe a kötési bővítményekhez kapcsolódó következő elemeket:

- A kötésbővítmények nincsenek kifejezetten regisztrálva az 1.x függvényekben, kivéve, ha [C# osztálytárat hoz létre a Visual Studio használatával.](#local-csharp)

- A HTTP- és időzítő-eseményindítók alapértelmezés szerint támogatottak, és nincs szükség bővítményre.

Az alábbi táblázat a kötések regisztrálásának módját mutatja be.

| Fejlesztési környezet |Regisztráció<br/> az 1.x függvényekben  |Regisztráció<br/> a 2.x függvényekben  |
|-------------------------|------------------------------------|------------------------------------|
|Azure portál|Automatikus|Automatikus|
|Non-.NET nyelvek vagy az Azure Core Tools helyi fejlesztése|Automatikus|[Az Azure Functions alapvető eszközeinek és bővítménycsomagjainak használata](#extension-bundles)|
|C# osztálykönyvtár a Visual Studio használatával|[NuGet eszközök használata](#vs)|[NuGet eszközök használata](#vs)|
|C# osztálykönyvtár a Visual Studio-kód használatával|N/A|[A .NET Core CLI használata](#vs-code)|

## <a name="extension-bundles-for-local-development"></a><a name="extension-bundles"></a>Mellékcsomagok helyi fejlesztéshez

A bővítménycsomagok egy olyan központi telepítési technológia, amely lehetővé teszi a Függvénykötési bővítmények kompatibilis készletének hozzáadását a függvényalkalmazáshoz. Az alkalmazás létrehozásakor a program egy előre definiált bővítménykészletet ad hozzá. A csomagban definiált bővítménycsomagok kompatibilisek egymással, ami segít elkerülni a csomagok közötti ütközéseket. Az alkalmazás host.json fájljában engedélyezheti a bővítménykötegeket.  

A Functions futásidejű 2.x-es és újabb verziójú bővítménykötegeket is használhat. Helyi fejlesztés esetén győződjön meg arról, hogy az [Azure Functions Core Tools](functions-run-local.md#v2)legújabb verzióját használja.

Az Azure Functions Core Tools, a Visual Studio Code és a távolról történő létrehozás során bővítménycsomagokat használhat a helyi fejlesztéshez.

Ha nem használ bővítménykötegeket, a kötési bővítmények telepítése előtt telepítenie kell a .NET Core 2.x SDK-t a helyi számítógépre. A bővítménycsomagok megszüntetik ezt a helyi fejlesztési követelményt. 

A bővítménykötegek használatához frissítse a *host.json* fájlt `extensionBundle`úgy, hogy az tartalmazza a következő bejegyzést:
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>C\# osztálykönyvtár a Visual Studio segítségével

A **Visual Studio alkalmazásban**a Csomagkezelő konzolról a [Csomagkezelő](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) ből telepíthet csomagokat, ahogy az a következő példában látható:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Az adott kötéshez használt csomag neve az adott kötésre vonatkozó referenciacikkben található. Például tekintse meg a [Service Bus kötési referenciacikk csomagok szakaszát.](functions-bindings-service-bus.md#functions-1x)

Cserélje `<TARGET_VERSION>` le a példában a csomag egy `3.0.0-beta5`adott verziójára, például a . Az érvényes verziók az egyes csomagok oldalain [NuGet.org.](https://nuget.org) A Functions runtime 1.x vagy 2.x-nek megfelelő főverziókat a kötés referenciacikkében határozzák meg.

Ha kötésre hivatkozik, `Install-Package` nem kell [bővítménykötegeket használnia.](#extension-bundles) Ez a megközelítés a Visual Studio beépített osztálytárakra vonatkozik.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>C# osztálykönyvtár Visual Studio-kóddal

A **Visual Studio Code alkalmazásban**telepítse a C# osztálykönyvtár-projekt csomagjait a parancssorból a [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) paranccsal a .NET Core CLI-ben. A következő példa bemutatja, hogyan adhat hozzá kötést:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

A .NET Core CLI csak az Azure Functions 2.x fejlesztéséhez használható.

Cserélje `<BINDING_TYPE_NAME>` le a szükséges kötést tartalmazó csomag nevére. A kívánt kötési referenciacikk a [támogatott kötések listájában](./functions-triggers-bindings.md#supported-bindings)található.

Cserélje `<TARGET_VERSION>` le a példában a csomag egy `3.0.0-beta5`adott verziójára, például a . Az érvényes verziók az egyes csomagok oldalain [NuGet.org.](https://nuget.org) A Functions runtime 1.x vagy 2.x-nek megfelelő főverziókat a kötés referenciacikkében határozzák meg.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Az Azure Függvény eseményindítója és kötési példa](./functions-bindings-example.md)
