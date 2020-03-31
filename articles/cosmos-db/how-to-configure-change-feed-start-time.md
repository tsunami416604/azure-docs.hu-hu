---
title: A változáscsatorna-processzor kezdési idejének konfigurálása - Azure Cosmos DB
description: További információ arról, hogyan állíthatja be a módosítási hírcsatorna-processzort úgy, hogy egy adott dátumtól és időponttól kezdje el az olvasást
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 600556a06d3f58c4d2ec79a49fdee5e8e04d4036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586274"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>A változáscsatorna-processzor kezdési idejének beállítása

Ez a cikk azt ismerteti, hogyan konfigurálhatja úgy a [módosítási hírcsatorna-processzort,](./change-feed-processor.md) hogy egy adott dátumtól és időponttól kezdje az olvasást.

## <a name="default-behavior"></a>Alapértelmezett viselkedés

Amikor egy változáscsatorna-feldolgozó először indul el, inicializálja a lízingtárolót, és elindítja a [feldolgozási életciklusát.](./change-feed-processor.md#processing-life-cycle) A módosítási hírcsatorna-feldolgozó első inicializálása előtt a tárolóban történt módosítások nem lesznek észlelve.

## <a name="reading-from-a-previous-date-and-time"></a>Olvasás egy előző dátumból és időpontból

A módosítási hírcsatorna-feldolgozó inicializálása egy adott dátumtól és időponttól kezdve egy **adott dátumtól és időponttól**kezdve lehetséges a módosítási folyamat leolvasásához, ha átadja a `DateTime` `WithStartTime` szerkesztő bővítményének egy példányát:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

A módosítási hírcsatorna-processzor inicializálása az adott dátumra és időpontra történik, és elkezdi olvasni az azt követő módosításokat.

## <a name="reading-from-the-beginning"></a>Olvasás az elejétől

Más esetekben, például az adatáttelepítések vagy egy tároló teljes előzményeinek elemzése esetén a módosítási hírcsatornát **a tároló élettartamának kezdetétől**kell olvasnunk. Ehhez használhatjuk `WithStartTime` az építő bővítményt, de `DateTime.MinValue.ToUniversalTime()`a átadást, amely a `DateTime` minimális érték UTC-ábrázolását eredményezné, így:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

A módosítási hírcsatorna-processzor inicializálódik, és a tároló élettartamának kezdetétől kezdve elkezdi olvasni a módosításokat.

> [!NOTE]
> Ezek a testreszabási beállítások csak a változáscsatorna-processzor kezdőpontjának beállítására szolgálnak. Miután a bérletek tároló inicializálása az első alkalommal, módosítása nincs hatása.

> [!NOTE]
> Időpont megadásakor a rendszer csak a tárolóban lévő elemek módosításait olvassa be. Ha egy elemet töröltek, a rendszer eltávolítja annak előzményeit a hírfolyamban.

## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>További lépések

Most a következő cikkekben olvashat bővebben a hírcsatorna-feldolgozó módosításáról:

* [A módosítási hírcsatorna-processzor áttekintése](change-feed-processor.md)
* [A változáscsatorna-becslő használata](how-to-use-change-feed-estimator.md)
