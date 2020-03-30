---
title: Tartós funkciók számlázása – Azure Functions
description: Ismerje meg a tartós függvények belső viselkedését, és hogyan befolyásolják az Azure Functions számlázását.
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 504ef93a0002895bc5662d95ad269c8593170ee2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74233015"
---
# <a name="durable-functions-billing"></a>Tartós funkciók számlázása

[A tartós függvények](durable-functions-overview.md) számlázása ugyanúgy történik, mint az Azure Functions. További információ: [Azure Functions pricing](https://azure.microsoft.com/pricing/details/functions/).

Az Azure [Functions-felhasználási tervben](../functions-scale.md#consumption-plan)az orchestrator-függvények végrehajtásakor tisztában kell lennie néhány számlázási viselkedéssel. A következő szakaszok részletesebben ismertetik ezeket a viselkedéseket és azok hatását.

## <a name="orchestrator-function-replay-billing"></a>Orchestrator függvény visszajátszása számlázás

[Az Orchestrator-függvények](durable-functions-orchestrations.md) többször is újrajátszhatók a vezénylés iményezési élettartama során. Minden visszajátszást az Azure Functions futásidejű külön függvénymeghívásként tekint meg. Ebből az okból az Azure Functions-felhasználási tervben egy orchestrator-függvény minden egyes visszajátszásáért számlázunk. Más csomagtípusok nem számítanak fel díjat az orchestrator függvény visszajátszásáért.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Várakozás és engedés az orchestrator függvényekben

Amikor egy orchestrator függvény megvárja, amíg egy aszinkron művelet befejeződik **a C#** vagy a JavaScript **hozamhasználatával,** a futásidejű úgy véli, hogy az adott végrehajtás befejeződött. Az orchestrator függvény számlázása ezen a ponton leáll. Nem folytatódik, amíg a következő orchestrator függvény visszajátszás. Nem kell fizetnie az orchestrator függvényre való várakozással vagy engedéssel töltött időért.

> [!NOTE]
> Funkciók hívása más funkciók tartják néhány an antipattern. Ennek oka a _kettős számlázásnak_nevezett probléma. Amikor egy függvény közvetlenül hív meg egy másik függvényt, mindkettő egyszerre fut. A hívott függvény aktívan futtatja a kódot, miközben a hívó függvény választ vár. Ebben az esetben meg kell fizetnie az időt a hívás függvény tölt várja a hívott függvény futtatásához.
>
> Az orchestrator-függvényekben nincs kettős számlázás. Az orchestrator függvény számlázási leáll, amíg megvárja egy tevékenységfüggvény vagy al-vezénylési eredménye.

## <a name="durable-http-polling"></a>Tartós HTTP-lekérdezés

Az Orchestrator függvények hosszú ideig futó HTTP-hívásokat kezdeményezhetnek a külső végpontokhoz a [HTTP-szolgáltatások ról szóló cikkben](durable-functions-http-features.md)leírtak szerint. A **C# híváshttpAsync** metódusa és a JavaScript-ben lévő **CallHttpHttp** metódus belsőleg lekérdezett egy HTTP-végpontot az [aszinkron 202 minta](durable-functions-http-features.md#http-202-handling)követése közben.

Jelenleg nincs közvetlen számlázás a belső HTTP-lekérdezési műveletek. A belső lekérdezés azonban az orchestrator függvény rendszeres visszajátszását okozhatja. Ezeknek a belső függvényvisszajátszásoknak a szokásos díjait számítjuk ki.

## <a name="azure-storage-transactions"></a>Azure Storage-tranzakciók

A Durable Functions alapértelmezés szerint az Azure Storage-t használja az állandó állapot megőrzéséhez, az üzenetek feldolgozásához és a partíciók blobbérleteken keresztüli kezeléséhez. Mivel ön a tárfiók a tulajdonában van, a tranzakciós költségeket az Azure-előfizetése számlája alapján számlázunk. A durable functions által használt Azure Storage-összetevőkről a Feladatközpontok ról olvashat [bővebben.](durable-functions-task-hubs.md)

Számos tényező járul hozzá a Durable Functions alkalmazás tényleges Azure Storage-költségeihez:

* Egyetlen függvényalkalmazás egyetlen feladatközponthoz van társítva, amely megosztja az Azure Storage-erőforrások készletét. Ezeket az erőforrásokat egy függvényalkalmazás összes tartós függvénye használja. A függvényalkalmazás funkcióinak tényleges száma nincs hatással az Azure Storage tranzakciós költségeire.
* Minden egyes függvényalkalmazás-példány belsőleg lekérdezi a tárfiók több várólistáját egy exponenciális-visszaegyszeri lekérdezési algoritmus használatával. Egy tétlen alkalmazáspéldány ritkábban kérdezi le a várólistákat, mint egy aktív alkalmazás, ami kevesebb tranzakciós költséget eredményez. A Tartós függvények várólista-lekérdezési viselkedéséről a [Teljesítmény és méretezés című cikk várólista-lekérdezési szakaszában olvashat bővebben.](durable-functions-perf-and-scale.md#queue-polling)
* Az Azure Functions Consumption vagy a Premium-csomagokban való futtatáskor az [Azure Functions méretezési vezérlő](../functions-scale.md#how-the-consumption-and-premium-plans-work) rendszeresen lekérdezi az összes feladatközpont-várólistát a háttérben. Ha egy függvényalkalmazás könnyű vagy közepes méretű, csak egy méretezési vezérlő példány a feladatsorok lekérdezése. Ha a függvényalkalmazás nagy számú példányra skálázódik, további méretezési vezérlőpéldányok adhatók hozzá. Ezek a további méretezési vezérlő példányok növelhetik a teljes várólista-tranzakció költségeit.
* Minden függvényalkalmazás-példány verseng a blobbérletek készletével. Ezek a példányok rendszeres időközönként hívásokat kezdeményeznek az Azure Blob szolgáltatáshoz a birtokolt bérletek megújítása vagy új bérletek beszerzése érdekében. A feladatközpont konfigurált partíciószáma határozza meg a blobbérletek számát. Horizontális felskálázás nagyobb számú függvény alkalmazáspéldányok valószínűleg növeli az Azure Storage tranzakciós költségek ezekkel a bérleti műveletekkel kapcsolatos.

Az Azure Storage díjszabásáról az [Azure Storage díjszabási](https://azure.microsoft.com/pricing/details/storage/) dokumentációjában talál további információt. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Azure Functions díjszabásáról](https://azure.microsoft.com/pricing/details/functions/)
