---
title: A hírcsatorna-feldolgozó processzor indítási idejének konfigurálása – Azure Cosmos DB
description: Megtudhatja, hogyan konfigurálhatja a módosítási hírcsatorna processzorát egy adott dátummal és időponttal való olvasás megkezdéséhez
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: d3d9af1b8cbda3f0fa2ff4650fef4602f7812f79
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85261715"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>A hírcsatorna-processzor módosításának kezdési idejének konfigurálása

Ez a cikk azt ismerteti, hogyan konfigurálhatja a [változási hírcsatorna processzorát](./change-feed-processor.md) , hogy egy adott dátummal és időponttal kezdjen el olvasni.

## <a name="default-behavior"></a>Alapértelmezett viselkedés

Amikor az első alkalommal elindul a Change feed-processzor, a megkezdi a bérletek tárolójának inicializálását, és elindítja a [feldolgozási életciklusát](./change-feed-processor.md#processing-life-cycle). A rendszer nem észleli a tárolóban a változási csatorna processzorának első inicializálásakor történt módosításokat.

## <a name="reading-from-a-previous-date-and-time"></a>Olvasás egy korábbi dátumból és időpontból

A módosítási hírcsatorna processzora inicializálható úgy, hogy egy **adott dátummal és**időponttal kezdődő módosításokat olvasson be, az a példányának átadásával `DateTime` a `WithStartTime` Builder bővítménnyel:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

A módosítási hírcsatorna processzora az adott dátumra és időpontra lesz inicializálva, és megkezdi az azt követő módosítások olvasását.

## <a name="reading-from-the-beginning"></a>Olvasás az elejétől

Más forgatókönyvekben, például az adatáttelepítés során vagy egy tároló teljes előzményeinek elemzéséhez a **tároló élettartamának elejéről**kell olvasni a változási csatornát. Ehhez használhatja a `WithStartTime` Builder bővítményt, de a továbbítást `DateTime.MinValue.ToUniversalTime()` , amely a minimális érték UTC szerinti megjelenítését eredményezi, például `DateTime` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

A módosítási hírcsatorna processzora inicializálva lesz, és megkezdi a módosítások olvasását a tároló élettartamának elejétől kezdve.

> [!NOTE]
> Ezek a testreszabási beállítások csak akkor működnek, ha beállítja a változási csatorna processzorának kiindulási időpontját. Ha a bérletek tárolója első alkalommal lett inicializálva, a módosításnak nincs hatása.

> [!NOTE]
> Ha egy adott időpontot ad meg, csak a tárolóban aktuálisan létező elemek módosításai lesznek beolvasva. Ha egy elem törölve lett, a változási csatornán lévő előzmények is el lettek távolítva.

## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>További lépések

A következő cikkekben további tudnivalókat olvashat a hírcsatorna-feldolgozó szolgáltatással kapcsolatos változásokról:

* [A hírcsatorna-feldolgozó változásának áttekintése](change-feed-processor.md)
* [A változáscsatorna-becslő használata](how-to-use-change-feed-estimator.md)
