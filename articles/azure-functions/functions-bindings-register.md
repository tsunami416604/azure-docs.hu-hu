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
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 5534086d5754691f650370e465fa2c63210e0dc7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739610"
---
# <a name="register-azure-functions-binding-extensions"></a>Az Azure Functions kötési bővítményeket regisztrálása

Az Azure Functions támogatja a HTTP és a beépített időzítő. Más szolgáltatások használatához telepítenie kell, vagy *regisztrálása* egy [kötés](./functions-triggers-bindings.md) bővítmény. Kötési bővítményeket Azure Core Tools vagy a NuGet-csomagot keresztül biztosított. 

Az alábbi táblázat azt jelzi, hogy mikor és hogyan regisztrálhatja a kötéseket.

| Fejlesztési környezet |Regisztráció<br/> a függvények 1.x  |Regisztráció<br/> a függvények 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatikus|[A kérés automatikus](#azure-portal-development)|
|Nem – .NET-nyelveket vagy a helyi Azure Core Tools-fejlesztés|Automatikus|[Core Tools CLI-parancsok használata](#local-development-azure-functions-core-tools)|
|Visual Studio 2017 használatával C# osztálytár|[NuGet-eszközök](#c-class-library-with-visual-studio-2017)|[NuGet-eszközök](#c-class-library-with-visual-studio-2017)|
|A Visual Studio Code C# osztálytár|–|[A .NET Core parancssori felület használata](#c-class-library-with-visual-studio-code)|

A következő kötéstípusok kivételek, amelyek nem igényelnek explicit regisztrálása, mert automatikusan regisztrálva vannak az összes verziója és környezetek: A HTTP és időzítőhöz.

> [!IMPORTANT]
> Ez a tartalom esetében ez a cikk további részének csak érvényes függvények 2.x. Kötési bővítményeket explicit módon aspektusnevek regisztrálva funkciók 1.x, kivéve, ha [létrehozása egy C# Visual Studio 2017 használatával osztálytár](#local-csharp).

## <a name="azure-portal-development"></a>Azure portal-fejlesztés

Hozzon létre egy függvényt, vagy a felvenni egy kötést, ha kéri a bővítmény számára az eseményindítóval vagy kötéssel van szükség a regisztráció során. A gombra kattintva megismert **telepítése** regisztrálni a bővítményt. Telepítés akár 10 percet is igénybe a használatalapú csomag is.

Csak telepítenie kell minden egyes bővítmény megadott függvényalkalmazás egy alkalommal. Támogatott kötések, amelyek nem állnak rendelkezésre a portálon, vagy frissíteni a telepített kiterjesztést is [manuálisan telepíteni vagy frissíteni a kötési bővítményeket a portálról az Azure Functions](install-update-binding-extensions-manual.md).  

## <a name="local-development-azure-functions-core-tools"></a>Helyi fejlesztés az Azure Functions Core Tools

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>C# osztálytár a Visual Studio 2017

A **Visual Studio 2017**, csomagokat a Package Manager konzol használatával lehet telepíteni a [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) parancsot, az alábbi példában látható módon:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

A csomag használatára egy adott kötéshez neve a áttekintésével foglalkozó cikkben megtalálható a kötéshez. Egy vonatkozó példáért tekintse meg a [csomagok a Service Bus kötés áttekintésével foglalkozó cikkben szakaszában](functions-bindings-service-bus.md#packages---functions-1x).

Cserélje le `<target_version>` a példában a csomaghoz, egy adott verzióját a például `3.0.0-beta5`. Érvényes verzió szerepel az egyes csomagot oldalakon [NuGet.org](https://nuget.org). A főbb verziók, hogy a Functions futtatókörnyezete megfelelnek a referenciacikk a kötési 1.x vagy 2.x vannak megadva.

## <a name="c-class-library-with-visual-studio-code"></a>C# osztálytár Visual Studio Code használatával

A **Visual Studio Code**, a parancssor használatával csomagokat telepíthet a [dotnet-csomag hozzáadása](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) a .NET Core-CLI-parancs az alábbi példában látható módon:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

A .NET Core CLI csak akkor használható, az Azure Functions 2.x fejlesztéséhez.

A csomag használatára egy adott kötéshez neve a áttekintésével foglalkozó cikkben megtalálható a kötéshez. Egy vonatkozó példáért tekintse meg a [csomagok a Service Bus kötés áttekintésével foglalkozó cikkben szakaszában](functions-bindings-service-bus.md#packages---functions-1x).

Cserélje le `<target_version>` a példában a csomaghoz, egy adott verzióját a például `3.0.0-beta5`. Érvényes verzió szerepel az egyes csomagot oldalakon [NuGet.org](https://nuget.org). A főbb verziók, hogy a Functions futtatókörnyezete megfelelnek a referenciacikk a kötési 1.x vagy 2.x vannak megadva.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Azure függvény eseményindítót és a kötési – példa](./functions-bindings-example.md)

