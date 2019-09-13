---
title: Tartós függvények számlázása – Azure Functions
description: Ismerje meg a tartós függvények belső viselkedését, valamint azt, hogy a Azure Functions milyen hatással vannak a számlázásra.
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: b79d50adf932bd5c316fbda9d0964eea7c0484ca
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935886"
---
# <a name="durable-functions-billing"></a>Durable Functions számlázás

[Durable functions](durable-functions-overview.md) számlázása ugyanaz, mint Azure functions. További információkért lásd: [Azure Functions árképzése](https://azure.microsoft.com/pricing/details/functions/). A Azure Functions használati [tervben](../functions-scale.md#consumption-plan)szereplő Orchestrator-függvények végrehajtásakor bizonyos számlázási viselkedések ismerete szükséges. A következő szakaszok részletesen ismertetik ezeket a viselkedéseket és azok hatását.

## <a name="orchestrator-function-replay-billing"></a>Orchestrator függvény újrajátszása számlázás

A [Orchestrator függvények](durable-functions-orchestrations.md) többször is visszajátszhatók a folyamat teljes élettartama során. A rendszer minden egyes visszajátszás esetében az Azure Functions futtatókörnyezetet tekinti meg külön függvény meghívásakor. Emiatt a Azure Functions használati tervben a Orchestrator függvény minden újrajátszása után díjat számítunk fel. A Orchestrator függvény újrajátszása nem számítja fel a többi csomag típusát.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Várakozás és hozam a Orchestrator functions szolgáltatásban

Ha egy Orchestrator-függvény várakozik egy aszinkron művelet befejezésére a ( `await` C#) vagy `yield` a (JavaScript) használatával, a futtatókörnyezet úgy véli, hogy az adott végrehajtás befejeződött. A Orchestrator függvény számlázása ezen a ponton leáll, és addig nem folytatódik, amíg a következő Orchestrator-függvény újrajátszása be nem fejeződik. Nem számítunk fel díjat a Orchestrator-függvény várakozási ideje vagy hozamának elköltése során.

> [!NOTE]
> A más függvényeket meghívó függvények egy-egy ellenes mintanak tekintendők. Ennek oka a _kettős számlázási_megoldás. Ha egy függvény közvetlenül hív meg egy másik függvényt, akkor mindkettő egyszerre hajt végre. A hívott fél aktívan hajtja végre a kódot, miközben a hívó választ vár a válaszra. Ebben az esetben akkor kell fizetnie, amikor a hívó elvárta, hogy végrehajtsa a hívott fél számára. A Orchestrator függvények nem értek el ezt a kettős számlázást, mert a Orchestrator függvény számlázása leáll, miközben egy tevékenységi függvény (vagy az alfolyamatok) eredményére vár.

## <a name="durable-http-polling"></a>Tartós HTTP-lekérdezés

A Orchestrator függvények a [http-szolgáltatások](durable-functions-http-features.md) című cikkben leírtak szerint hosszan futó http-hívásokat végezhetnek külső végpontokra. A `CallHttpAsync` metódus (C#) és a `callHttp` metódus (JavaScript) belsőleg lekérdezheti a http-végpontot az [aszinkron 202 minta](durable-functions-http-features.md#http-202-handling)követése után. Jelenleg nincs közvetlen számlázás a belső HTTP-lekérdezési műveletekhez. A belső lekérdezés azonban azt eredményezheti, hogy a Orchestrator függvény rendszeres időközönként újrajátszható, és a belső függvény által visszajátszott standard díjakat számítunk fel.

## <a name="azure-storage-transactions"></a>Azure Storage-tranzakciók

Az Durable Functions alapértelmezés szerint az Azure Storage-t használja az állapot megőrzéséhez, az üzenetek feldolgozásához és a partíciók blob bérleteken keresztüli kezeléséhez. Ezt a Storage-fiókot Ön birtokolja, így az Azure-előfizetése minden tranzakciós költséggel jár. Az Durable Functions által használt Azure Storage-összetevőkkel kapcsolatos további információkért tekintse meg a [feladatok hubok](durable-functions-task-hubs.md) című cikket.

Számos tényező járul hozzá a Durable Functions alkalmazása során felmerülő tényleges Azure Storage-költségekhez.

* Egyetlen Function-alkalmazás van társítva egyetlen feladati hubhoz, amely az Azure Storage-erőforrások készletét osztja meg. Ezeket az erőforrásokat a Function app összes tartós funkciója használja. A Function alkalmazásban a függvények tényleges száma nem befolyásolja az Azure Storage tranzakciós költségeit.
* Minden Function App-példány belsőleg több várólistát kérdez le a Storage-fiókban egy exponenciális leállítási lekérdezési algoritmus használatával. Egy tétlen alkalmazás-példány lekérdezi a várólistákat ritkábban, mint egy aktív alkalmazást, így kevesebb tranzakciós költséget fog eredményezni. A Durable Functions üzenetsor – lekérdezési viselkedéssel kapcsolatos további információkért tekintse meg a [teljesítmény-és méretezési cikk üzenetsor-lekérdezési szakaszát](durable-functions-perf-and-scale.md#queue-polling) .
* A Azure Functions fogyasztási vagy prémium csomagokban való futtatásakor a [Azure functions skálázási vezérlő](../functions-scale.md#how-the-consumption-and-premium-plans-work) rendszeresen lekérdezi a háttérben lévő összes Task hub-várólistát. A kis-és nagyméretű méretezés alatt csak egyetlen méretezési vezérlő példány fogja lekérdezni ezeket a várólistákat. Ha a Function alkalmazás nagy mennyiségű példányra méretezhető, több méretezési vezérlő példány is felvehető. Ezek a további méretezési vezérlő példányok növelhetik a várólista-tranzakciók teljes költségét.
* Minden Function App-példány blob-bérletek egy készletére verseng. Ezek a példányok rendszeresen kezdeményezik az Azure Blob servicet, hogy a megújított bérletek új bérleteket szerezzenek. A blob-bérletek számát a feladat hub konfigurált partícióinak száma határozza meg. A nagyobb számú Function app-példányra való horizontális felskálázás valószínűleg növeli az Azure Storage-tranzakcióhoz kapcsolódó költségeket a címbérleti műveletekhez.

Az Azure Storage díjszabásával kapcsolatos további információkért tekintse meg az [Azure Storage díjszabási](https://azure.microsoft.com/pricing/details/storage/) dokumentációját.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a Azure Functions díjszabásáról](https://azure.microsoft.com/pricing/details/functions/)
