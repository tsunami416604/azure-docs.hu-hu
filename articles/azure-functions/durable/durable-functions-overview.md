---
title: Durable Functions áttekintése – Azure
description: A Azure Functions Durable Functions bővítményének bemutatása.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 12/22/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: 323ab530d8199dd154e5d3568c09f86f6f52d702
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087198"
---
# <a name="what-are-durable-functions"></a>Mi a Durable Functions?

*Durable functions* a [Azure functions](../functions-overview.md) kiterjesztése, amely lehetővé teszi, hogy állapot-nyilvántartó függvények írhatók legyenek kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat.

## <a name="benefits"></a>Előnyök

A bővítmény lehetővé teszi az állapot-nyilvántartó munkafolyamatok definiálását egy [*Orchestrator függvénnyel*](durable-functions-types-features-overview.md#orchestrator-functions), amely a következő előnyöket nyújtja:

* Megadhatja a munkafolyamatokat a kódban. Nem szükségesek JSON-sémák vagy-tervezők.
* Más függvények szinkron és aszinkron módon is hívhatók. Az úgynevezett függvényekből származó kimenet menthető a helyi változókba.
* Az előrehaladás automatikusan ellenőrzőpontra kerül, amikor a függvény megvárja. A folyamat újraindításakor vagy a virtuális gép újraindításakor a helyi állapot soha nem vész el.

## <a name="application-patterns"></a>Alkalmazásminták

Durable Functions elsődleges használati esete az összetett, állapot-nyilvántartó koordinációs követelmények leegyszerűsítése a kiszolgáló nélküli alkalmazásokban. Az alábbiakban néhány olyan jellemző alkalmazási mintát láthat, amelyek hasznosak lehetnek a Durable Functions:

* [Láncolására](durable-functions-concepts.md#chaining)
* [Ventilátor kijelentkezése/ventilátor](durable-functions-concepts.md#fan-in-out)
* [Aszinkron HTTP API-k](durable-functions-concepts.md#async-http)
* [Monitorozás](durable-functions-concepts.md#monitoring)
* [Emberi interakció](durable-functions-concepts.md#human)

## <a name="language-support"></a>Támogatott nyelvek

A Durable Functions jelenleg a következő nyelveket támogatja:

* **C#** : mindkét előre [lefordított osztály kódtára](../functions-dotnet-class-library.md) és [ C# parancsfájl](../functions-reference-csharp.md).
* **F#** : előre lefordított osztály kódtárak és F# szkriptek. F#a parancsfájl csak az Azure Functions futtatókörnyezet 1. x verziója esetén támogatott.
* **JavaScript**: csak az Azure functions futtatókörnyezet 2. x verziójára támogatott. A Durable Functions-bővítmény vagy újabb verzió 1.7.0 szükséges. 

Durable Functions célja az összes [Azure functions nyelv](../supported-languages.md)támogatása. További nyelvek támogatásához tekintse meg a [Durable functions problémák listáját](https://github.com/Azure/azure-functions-durable-extension/issues) a legújabb munkaállapotról.

A Azure Functionshoz hasonlóan a [Visual studio 2019](durable-functions-create-first-csharp.md), a [Visual Studio Code](quickstart-js-vscode.md)és a [Azure Portal](durable-functions-create-portal.md)használatával is készíthet durable Functionseket.

## <a name="billing"></a>Számlázás

Durable Functions számlázása ugyanaz, mint Azure Functions. További információkért lásd: [Azure Functions árképzése](https://azure.microsoft.com/pricing/details/functions/).

## <a name="jump-right-in"></a>Ugrás közvetlenül a

A következő, a nyelvfüggő gyors útmutatók egyikének elvégzésével megkezdheti a Durable Functions használatának megkezdését 10 perc alatt:

* [C#a Visual Studio 2019 használata](durable-functions-create-first-csharp.md)
* [JavaScript a Visual Studio Code használatával](quickstart-js-vscode.md)

Mindkét rövid útmutatóban helyileg létrehozhatja és tesztelheti a "Hello World" tartós funkciót. Ezután közzéteheti a függvénykódot az Azure-ban. Az Ön által létrehozott függvény összehangolja és láncokba rendezi a más függvények hívásait.

## <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról

Az alábbi videó a Durable Functions előnyeit mutatja be:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Mivel a Durable Functions a [Azure functions](../functions-overview.md)speciális bővítménye, az összes alkalmazás esetében nem megfelelő. További információ a Durable Functionsről: [Durable functions minták és műszaki fogalmak](durable-functions-concepts.md). Az egyéb Azure-előkészítési technológiákkal való összehasonlításért lásd: [Azure functions és Azure Logic apps összehasonlítása](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Durable Functions minták és technikai fogalmak](durable-functions-concepts.md)
