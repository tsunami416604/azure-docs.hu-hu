---
title: Az Azure Functions kötési bővítményeket regisztrálása
description: Ismerje meg, az adott környezet alapján az Azure Functions kötés bővítmények regisztrálásához.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 53eb5fc9389d913ecacec3729a06e47a1c2bf56b
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864553"
---
# <a name="register-azure-functions-binding-extensions"></a>Az Azure Functions kötési bővítményeket regisztrálása

Az Azure Functions verzió 2.x [kötések](./functions-triggers-bindings.md) a functions futtatókörnyezetének a különálló csomagként érhető el. .NET-es függvényeket kötések keresztül NuGet-csomagok eléréséhez, amíg bővítmény csomagok hozzáférést egyéb funkciók keresztül a konfigurációs beállítások összes kötését.

Vegye figyelembe a következő elemek bővítmények kötéséhez kapcsolódó:

- Kötési bővítményeket explicit módon aspektusnevek regisztrálva funkciók 1.x, kivéve, ha [létrehozása egy C# osztálytár használatával a Visual Studio 2019](#local-csharp).

- HTTP- és időzítő eseményindító alapértelmezés szerint támogatott, és nem igényel a kiterjesztése.

Az alábbi táblázat azt jelzi, hogy mikor és hogyan regisztrálhatja a kötéseket.

| Fejlesztési környezet |Regisztráció<br/> a függvények 1.x  |Regisztráció<br/> a függvények 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatikus|Automatikus|
|Nem – .NET-nyelveket vagy a helyi Azure Core Tools-fejlesztés|Automatikus|[Azure Functions Core Tools és bővítmény csomagok használata](#local-development-with-azure-functions-core-tools-and-extension-bundles)|
|C#használatával a Visual Studio 2019 osztálytár|[NuGet-eszközök](#c-class-library-with-visual-studio-2019)|[NuGet-eszközök](#c-class-library-with-visual-studio-2019)|
|A Visual Studio Code C# osztálytár|–|[A .NET Core parancssori felület használata](#c-class-library-with-visual-studio-code)|

## <a name="local-development-with-azure-functions-core-tools-and-extension-bundles"></a>Helyi fejlesztés és az Azure Functions Core Tools bővítmény csomagok

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2019"></a>C#a Visual Studio 2019 osztálytár

A **Visual Studio 2019**, csomagokat a Package Manager konzol használatával lehet telepíteni a [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) parancsot, az alábbi példában látható módon:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Egy adott kötéshez használt csomag nevét a áttekintésével foglalkozó cikkben megtalálható a kötéshez. Egy vonatkozó példáért tekintse meg a [csomagok a Service Bus kötés áttekintésével foglalkozó cikkben szakaszában](functions-bindings-service-bus.md#packages---functions-1x).

Cserélje le `<TARGET_VERSION>` a példában a csomaghoz, egy adott verzióját a például `3.0.0-beta5`. Érvényes verzió szerepel az egyes csomagot oldalakon [NuGet.org](https://nuget.org). A főbb verziók, hogy a Functions futtatókörnyezete megfelelnek a referenciacikk a kötési 1.x vagy 2.x vannak megadva.

## <a name="c-class-library-with-visual-studio-code"></a>C# osztálytár Visual Studio Code használatával

A **Visual Studio Code**, a parancssor használatával csomagokat telepíthet a [dotnet-csomag hozzáadása](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) a .NET Core-CLI-parancs az alábbi példában látható módon:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

A .NET Core CLI csak akkor használható, az Azure Functions 2.x fejlesztéséhez.

A csomag használatára egy adott kötéshez neve a áttekintésével foglalkozó cikkben megtalálható a kötéshez. Egy vonatkozó példáért tekintse meg a [csomagok a Service Bus kötés áttekintésével foglalkozó cikkben szakaszában](functions-bindings-service-bus.md#packages---functions-1x).

Cserélje le `<TARGET_VERSION>` a példában a csomaghoz, egy adott verzióját a például `3.0.0-beta5`. Érvényes verzió szerepel az egyes csomagot oldalakon [NuGet.org](https://nuget.org). A főbb verziók, hogy a Functions futtatókörnyezete megfelelnek a referenciacikk a kötési 1.x vagy 2.x vannak megadva.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Azure függvény eseményindítót és a kötési – példa](./functions-bindings-example.md)

