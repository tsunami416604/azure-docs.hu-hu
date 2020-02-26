---
title: A hírcsatorna-feldolgozó processzor indítási idejének konfigurálása – Azure Cosmos DB
description: Megtudhatja, hogyan konfigurálhatja a módosítási hírcsatorna processzorát egy adott dátummal és időponttal való olvasás megkezdéséhez
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 8a5507d11c9545e4053dde832b7305f9bf35e39e
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586274"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>A hírcsatorna-processzor módosításának kezdési idejének konfigurálása

Ez a cikk azt ismerteti, hogyan konfigurálhatja a [változási hírcsatorna processzorát](./change-feed-processor.md) , hogy egy adott dátummal és időponttal kezdjen el olvasni.

## <a name="default-behavior"></a>Alapértelmezett viselkedés

Amikor az első alkalommal elindul a Change feed-processzor, a megkezdi a bérletek tárolójának inicializálását, és elindítja a [feldolgozási életciklusát](./change-feed-processor.md#processing-life-cycle). A rendszer nem észleli a tárolóban a változási csatorna processzorának első inicializálásakor történt módosításokat.

## <a name="reading-from-a-previous-date-and-time"></a>Olvasás egy korábbi dátumból és időpontból

A módosítási hírcsatorna processzora inicializálható úgy, hogy egy **adott dátummal és**időponttal kezdődő módosításokat olvasson be, ha a `DateTime` egy példányát átadja a `WithStartTime` Builder bővítménynek:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="TimeInitialization":::

A módosítási hírcsatorna processzora az adott dátumra és időpontra lesz inicializálva, és megkezdi az azt követő módosítások olvasását.

## <a name="reading-from-the-beginning"></a>Olvasás az elejétől

Más forgatókönyvekben, például az adatáttelepítés során vagy egy tároló teljes előzményeinek elemzéséhez a **tároló élettartamának elejéről**kell olvasni a változási csatornát. Ehhez használhatja a `WithStartTime`t a Builder bővítményben, de a `DateTime.MinValue.ToUniversalTime()`átadásával, amely a minimális `DateTime` érték UTC szerinti megjelenítését eredményezi, például:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="StartFromBeginningInitialization":::

A módosítási hírcsatorna processzora inicializálva lesz, és megkezdi a módosítások olvasását a tároló élettartamának elejétől kezdve.

> [!NOTE]
> Ezek a testreszabási beállítások csak akkor működnek, ha beállítja a változási csatorna processzorának kiindulási időpontját. Ha a bérletek tárolója első alkalommal lett inicializálva, a módosításnak nincs hatása.

> [!NOTE]
> Ha egy adott időpontot ad meg, csak a tárolóban aktuálisan létező elemek módosításai lesznek beolvasva. Ha egy elem törölve lett, a változási csatornán lévő előzmények is el lettek távolítva.

## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat olvashat a hírcsatorna-feldolgozó szolgáltatással kapcsolatos változásokról:

* [A hírcsatorna-feldolgozó változásának áttekintése](change-feed-processor.md)
* [A Change feed kalkulátor használata](how-to-use-change-feed-estimator.md)
