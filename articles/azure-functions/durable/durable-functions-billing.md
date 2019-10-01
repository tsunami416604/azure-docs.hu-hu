---
title: Tartós függvények számlázása – Azure Functions
description: Ismerje meg a Durable Functions belső viselkedését, valamint azt, hogy azok hogyan hatnak a Azure Functions számlázására.
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: f2de6bdf24aa1a0a11349c8f0ec9b3995b026a47
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694887"
---
# <a name="durable-functions-billing"></a>Durable Functions számlázás

[Durable functions](durable-functions-overview.md) számlázása ugyanúgy történik, mint Azure functions. További információkért lásd: [Azure Functions árképzése](https://azure.microsoft.com/pricing/details/functions/).

Azure Functions használati [tervben](../functions-scale.md#consumption-plan)szereplő Orchestrator-függvények végrehajtásakor figyelembe kell venni néhány számlázási viselkedést. A következő szakaszok részletesen ismertetik ezeket a viselkedéseket és azok hatását.

## <a name="orchestrator-function-replay-billing"></a>Orchestrator függvény újrajátszása számlázás

A [Orchestrator függvények](durable-functions-orchestrations.md) többször is visszaállhatnak egy előkészítési időszak alatt. A rendszer minden egyes visszajátszás esetében az Azure Functions futtatókörnyezetet tekinti meg külön függvény meghívásakor. Emiatt a Azure Functions fogyasztási tervben egy Orchestrator-függvény minden újrajátszása után számítunk fel díjat. Az egyéb Orchestrator nem számítanak fel díjat a funkció újrajátszása esetén.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Várakozás és hozam a Orchestrator functions szolgáltatásban

Ha egy Orchestrator-függvény megvárja, amíg egy aszinkron művelet befejeződik a JavaScriptben való **várakozás** C# vagy **hozam** használatával, a futtatókörnyezet úgy véli, hogy az adott végrehajtás befejeződött. A Orchestrator függvény számlázása ezen a ponton leáll. Ez a művelet csak a következő Orchestrator függvény újrajátszása után folytatódik. Nem számítunk fel díjat a Orchestrator-függvény várakozási ideje vagy hozamának elköltése során.

> [!NOTE]
> A más függvényeket meghívó függvényeket a rendszer úgy tekinti, hogy egy minta. Ennek oka a _kettős számlázási_megoldás. Ha egy függvény közvetlenül hív meg egy másik függvényt, akkor mindkettő egyszerre fut. A hívott függvény aktívan fut a kódban, miközben a hívó függvény választ vár. Ebben az esetben meg kell fizetnie, hogy a hívási függvény Mikor vár a meghívott függvény futtatására.
>
> A Orchestrator függvények nem rendelkeznek dupla számlázással. A Orchestrator függvény számlázása leáll, miközben egy tevékenységi függvény vagy alfolyamat eredményét várja.

## <a name="durable-http-polling"></a>Tartós HTTP-lekérdezés

A Orchestrator függvények a [http-szolgáltatások című cikkben](durable-functions-http-features.md)leírtak szerint hosszan futó http-hívásokat végezhetnek külső végpontokra. A **CallHttpAsync** metódusa C# és a JavaScript **callHttp** metódusa BELSŐLEG is lekérdezheti a HTTP-végpontot az [aszinkron 202-mintázatot](durable-functions-http-features.md#http-202-handling)követve.

Jelenleg nincs közvetlen számlázás a belső HTTP-lekérdezési műveletekhez. A belső lekérdezés azonban azt eredményezheti, hogy a Orchestrator függvény rendszeresen újrajátszható. Ezeknek a belső függvényeknek a díjait a standard díjszabás szerint számítjuk fel.

## <a name="azure-storage-transactions"></a>Azure Storage-tranzakciók

Az Durable Functions alapértelmezés szerint az Azure Storage-t használja az állapot állandó, az üzenetek feldolgozásához és a partíciók blob-bérleteken keresztüli kezeléséhez. Mivel Ön a Storage-fiók tulajdonosa, az Azure-előfizetésében minden tranzakciós költséget számlázunk. Az Durable Functions által használt Azure Storage-összetevőkkel kapcsolatos további információkért tekintse meg a [feladatok hubok című cikket](durable-functions-task-hubs.md).

Számos tényező járul hozzá a Durable Functions-alkalmazásban felmerülő tényleges Azure Storage-költségekhez:

* Egyetlen Function-alkalmazás van társítva egyetlen feladati hubhoz, amely az Azure Storage-erőforrások készletét osztja meg. Ezeket az erőforrásokat a Function app összes tartós funkciója használja. A Function alkalmazásban a függvények tényleges száma nem befolyásolja az Azure Storage tranzakciós költségeit.
* Minden Function App-példány belsőleg több várólistát kérdez le a Storage-fiókban egy exponenciális leállítási lekérdezési algoritmus használatával. Az inaktív alkalmazások példányai ritkábban kérdezik le a várólistákat, mint az aktív alkalmazások, ami kevesebb tranzakciós költséget eredményez. A Durable Functions üzenetsor – lekérdezési viselkedéssel kapcsolatos további információkért tekintse meg a [teljesítmény-és méretezési cikk üzenetsor-lekérdezési szakaszát](durable-functions-perf-and-scale.md#queue-polling).
* A Azure Functions-használat vagy a prémium csomagok futtatásakor a [Azure functions skálázási vezérlő](../functions-scale.md#how-the-consumption-and-premium-plans-work) rendszeresen lekérdezi a háttérben lévő összes Task hub-várólistát. Ha a Function alkalmazás enyhe és közepes méretű, akkor csak egyetlen méretezési vezérlő példány fogja lekérdezni ezeket a várólistákat. Ha a Function alkalmazás nagy mennyiségű példányra méretezhető, több méretezési vezérlő példány is felvehető. Ezek a további skálázási vezérlő-példányok növelhetik az összes üzenetsor-tranzakciós költségeket.
* Minden Function App-példány blob-bérletek egy készletére verseng. Ezek a példányok rendszeresen kezdeményezik az Azure Blob service a megújított bérletek megújítására vagy új bérletek megvásárlására. A Task hub konfigurált partícióinak száma határozza meg a blob bérletek számát. A nagyobb számú Function app-példányra való horizontális felskálázás valószínűleg megnöveli az Azure Storage-tranzakcióhoz kapcsolódó, a bérleti műveletekkel kapcsolatos költségeket.

Az Azure Storage díjszabásával kapcsolatos további információkért tekintse meg az [Azure Storage díjszabási](https://azure.microsoft.com/pricing/details/storage/) dokumentációját. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a Azure Functions díjszabásáról](https://azure.microsoft.com/pricing/details/functions/)
