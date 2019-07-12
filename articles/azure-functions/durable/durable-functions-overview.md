---
title: Durable Functions áttekintése – Azure
description: Az Azure Functions szolgáltatáshoz a Durable Functions bővítmény bemutatása.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 12/22/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: 6e1acf5f605d7f6fb42d24e6e7ec624a317b0e31
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612860"
---
# <a name="what-are-durable-functions"></a>Mik a Durable Functions?

*Durable Functions* bővítménye, [Azure Functions](../functions-overview.md) , amellyel írási állapot-nyilvántartó functions egy kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat.

## <a name="benefits"></a>Előnyök

A bővítmény lehetővé teszi, hogy állapot-nyilvántartó munkafolyamatok használatával meghatározhatja egy [ *vezérlőfüggvény*](durable-functions-types-features-overview.md#orchestrator-functions), lehet, amely a következő előnyöket nyújtják:

* A munkafolyamatok meghatározhatja a kódban. JSON-sémáinak vagy tervezők nem szükségesek.
* Más funkciók nem hívható meg mindkét szinkron és aszinkron módon történik. Meghívott függvényeken kimenete melyekbe menthetők a helyi változókhoz.
* Folyamat automatikusan alkulcsaihoz, ha a függvény várja. Helyi állapot soha nem elvész, amikor a folyamat újrahasznosítási eljárásának végrehajtásával vagy a virtuális gép újraindul.

## <a name="application-patterns"></a>Alkalmazásminták

Durable Functions elsődleges használati eset van egyszerűsítse összetett, állapot-nyilvántartó koordinációs követelményeket, a kiszolgáló nélküli alkalmazásokat. Az alábbiakban néhány tipikus alkalmazásminták, amelyek Durable Functions is:

* [-Láncolás](durable-functions-concepts.md#chaining)
* [Fan-out/fan-in](durable-functions-concepts.md#fan-in-out)
* [Az aszinkron HTTP API-k](durable-functions-concepts.md#async-http)
* [Monitorozás](durable-functions-concepts.md#monitoring)
* [Emberi beavatkozást igényel](durable-functions-concepts.md#human)

## <a name="language-support"></a>Támogatott nyelvek

Durable Functions jelenleg a következő nyelveket támogatja:

* **C#** : mindkét [előre lefordított osztálykódtárakat](../functions-dotnet-class-library.md) és [ C# parancsfájl](../functions-reference-csharp.md).
* **F#** : előre lefordított osztálykódtárakat és F# parancsfájlt. F#parancsfájl csak a támogatott verziója az Azure Functions futtatókörnyezet 1.x.
* **A JavaScript**: csak az verzió támogatott 2.x verzióját az Azure Functions futtatókörnyezettel. Durable Functions bővítmény 1.7.0-ás verzió vagy újabb verzió szükséges. 

Durable Functions rendelkezik az összes igazoló cél [Azure Functions nyelvek](../supported-languages.md). Tekintse meg a [Durable Functions problémák listája](https://github.com/Azure/azure-functions-durable-extension/issues) munkák további nyelveket támogatja a legújabb állapot.

Például az Azure Functions, vannak olyan sablonok segítenek tartós függvények használatával [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md), és a [az Azure portal](durable-functions-create-portal.md).

## <a name="billing"></a>Számlázás

Durable Functions számlázása ugyanaz, mint az Azure Functions. További információkért lásd: [Azure Functions árképzése](https://azure.microsoft.com/pricing/details/functions/).

## <a name="jump-right-in"></a>Ugrani

Akkor is Functions használatának első lépései tartós 10 percen belül ezeket a nyelvspecifikus rövid útmutatókat egyikének végrehajtásával:

* [C#a Visual Studio 2019 használatával](durable-functions-create-first-csharp.md)
* [JavaScript a Visual Studio Code használatával](quickstart-js-vscode.md)

Mindkét gyors útmutatók és helyileg létrehozhat, és egy "hello world" tartós függvény teszteléséhez. Ezután közzéteheti a függvénykódot az Azure-ban. A függvény létrehozása hangolja össze, és kapcsolódik egymáshoz hívások egyéb funkciók.

## <a name="learn-more"></a>Részletek

Az alábbi videó kiemeli Durable Functions előnyei:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Mivel Durable Functions-speciális bővítmény [Azure Functions](../functions-overview.md), nem megfelelő összes alkalmazáshoz. Durable Functions kapcsolatos további információkért lásd: [Durable Functions-minták és technikai kulcsfogalmak](durable-functions-concepts.md). És a többi Azure vezénylési technológia összehasonlításáért lásd: [hasonlítsa össze az Azure Functions és az Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Durable Functions-minták és technikai kulcsfogalmak](durable-functions-concepts.md)
