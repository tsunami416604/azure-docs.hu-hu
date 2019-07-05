---
title: Az Azure Functions kötési bővítményeket regisztrálása
description: Ismerje meg, az adott környezet alapján az Azure Functions kötés bővítmények regisztrálásához.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 88ffd6ec24ed19dd3b1e57277884c8759cdac1f9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480329"
---
# <a name="register-azure-functions-binding-extensions"></a>Az Azure Functions kötési bővítményeket regisztrálása

Az Azure Functions verzió 2.x [kötések](./functions-triggers-bindings.md) a functions futtatókörnyezetének a különálló csomagként érhető el. .NET-es függvényeket kötések keresztül NuGet-csomagok eléréséhez, amíg bővítmény csomagok hozzáférést egyéb funkciók keresztül a konfigurációs beállítások összes kötését.

Vegye figyelembe a következő elemek bővítmények kötéséhez kapcsolódó:

- Kötési bővítményeket explicit módon aspektusnevek regisztrálva funkciók 1.x, kivéve, ha [létrehozása egy C# Visual studióval osztálytár](#local-csharp).

- HTTP- és időzítő eseményindító alapértelmezés szerint támogatott, és nem igényel a kiterjesztése.

Az alábbi táblázat azt jelzi, hogy mikor és hogyan regisztrálhatja a kötéseket.

| Fejlesztési környezet |Regisztráció<br/> a függvények 1.x  |Regisztráció<br/> a függvények 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatikus|Automatikus|
|Nem – .NET-nyelveket vagy a helyi Azure Core Tools-fejlesztés|Automatikus|[Azure Functions Core Tools és bővítmény csomagok használata](#extension-bundles)|
|C#használatával a Visual Studio 2019 osztálytár|[NuGet-eszközök](#c-class-library-with-visual-studio-2019)|[NuGet-eszközök](#c-class-library-with-visual-studio-2019)|
|A Visual Studio Code C# osztálytár|–|[A .NET Core parancssori felület használata](#c-class-library-with-visual-studio-code)|

## <a name="extension-bundles"></a>A helyi fejlesztési bővítmény kötegek

Bővítmény csomagjaiból a verzió 2.x verziójú futtatókörnyezet, amely kompatibilis adja meg a bővítmények kötése a függvényalkalmazás projektjét funkciók lehetővé teszi a helyi fejlesztési technológia. A bővítmény csomagok majd szerepelnek a központi telepítési csomag, az Azure-ba történő telepítésekor. Csomagok beállítása keresztül elérhető a Microsoft által kiadott összes kötések lehetővé teszi a *host.json* fájlt. A köteg megadott bővítménycsomagok kompatibilisek egymással, és segítségével csomagok közötti ütközések elkerülésével. Amikor fejlesztése helyileg, ellenőrizze, hogy a legújabb verzióját használja, [Azure Functions Core Tools](functions-run-local.md#v2).

Azure Functions Core Tools vagy a Visual Studio Code használatával az összes helyi fejlesztési bővítmény kötegeket használja.

Ha a bővítmény csomagok nem használja, telepítenie kell a .NET Core SDK 2.x a helyi számítógépen mielőtt bármilyen kötési bővítményeket telepít. Csomagok eltávolítja ezt a követelményt a helyi fejlesztési. 

Bővítmény csomagok használatához frissítse a *host.json* -fájl a következő bejegyzés `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

A következő tulajdonságok érhetők el `extensionBundle`:

| Tulajdonság | Leírás |
| -------- | ----------- |
| **`id`** | A Microsoft Azure Functions bővítmény csomagjaiból névtere. |
| **`version`** | A csomagot a telepítendő verzióját. A Functions futtatókörnyezete mindig választja, a verziótartományon vagy időköz által meghatározott maximális megengedhető verziójára. A fenti version értéke lehetővé teszi, hogy az akár 1.0.0-s, de nem többek között a 2.0.0-s minden csomag verzió. További információkért lásd: a [időköz jelölése verzió tartományok megadása](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Verziók növekmény csomagot a csomag módosítása a csomag. Főverzió módosítások fordulhat elő, ha a csomagban csomagokat növekmény egy fő verziót, amely általában egybeessen a Functions futtatókörnyezete főverziója változik.  

Az aktuális készletét a alapértelmezett csomagazonosító alapján telepített bővítmények felsorolása ez [extensions.json fájl](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio-2019"></a>C\# a Visual Studio 2019 osztálytár

A **Visual Studio 2019**, csomagokat a Package Manager konzol használatával lehet telepíteni a [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) parancsot, az alábbi példában látható módon:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Egy adott kötéshez használt csomag nevét a áttekintésével foglalkozó cikkben megtalálható a kötéshez. Egy vonatkozó példáért tekintse meg a [csomagok a Service Bus kötés áttekintésével foglalkozó cikkben szakaszában](functions-bindings-service-bus.md#packages---functions-1x).

Cserélje le `<TARGET_VERSION>` a példában a csomaghoz, egy adott verzióját a például `3.0.0-beta5`. Érvényes verzió szerepel az egyes csomagot oldalakon [NuGet.org](https://nuget.org). A főbb verziók, hogy a Functions futtatókörnyezete megfelelnek a referenciacikk a kötési 1.x vagy 2.x vannak megadva.

## <a name="c-class-library-with-visual-studio-code"></a>C# osztálytár Visual Studio Code használatával

> [!NOTE]
> Azt javasoljuk, [bővítmény csomagjaiból](#extension-bundles) szeretné, hogy automatikusan telepítse kompatibilis bővítménycsomagok kötelező funkciók.

A **Visual Studio Code**, a csomagok telepítése egy C# hordozhatóosztálytár-projektjének a parancssor használatával a [dotnet-csomag hozzáadása](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) parancsot a .NET Core parancssori felületen, a következő példában látható módon:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

A .NET Core CLI csak akkor használható, az Azure Functions 2.x fejlesztéséhez.

A csomag használatára egy adott kötéshez neve a áttekintésével foglalkozó cikkben megtalálható a kötéshez. Egy vonatkozó példáért tekintse meg a [csomagok a Service Bus kötés áttekintésével foglalkozó cikkben szakaszában](functions-bindings-service-bus.md#packages---functions-1x).

Cserélje le `<TARGET_VERSION>` a példában a csomaghoz, egy adott verzióját a például `3.0.0-beta5`. Érvényes verzió szerepel az egyes csomagot oldalakon [NuGet.org](https://nuget.org). A főbb verziók, hogy a Functions futtatókörnyezete megfelelnek a referenciacikk a kötési 1.x vagy 2.x vannak megadva.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Azure függvény eseményindítót és a kötési – példa](./functions-bindings-example.md)

