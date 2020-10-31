---
title: Apache Cassandra és Azure Cosmos DB konzisztencia-szintek
description: Apache Cassandra és Azure Cosmos DB konzisztencia-szintek.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: f640ad85fd34dd5a4803e7dd96f1c0283f0c859a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100428"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Apache Cassandra és Azure Cosmos DB konzisztencia-szintek
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A Azure Cosmos DBtól eltérően az Apache Cassandra nem biztosít natív módon pontosan meghatározott konzisztencia-garanciákat. Az Apache Cassandra Ehelyett írási konzisztencia-szintet és olvasási konzisztencia-szintet biztosít a magas rendelkezésre állás, a konzisztencia és a késési kompromisszumok engedélyezéséhez. Azure Cosmos DB Cassandra API használatakor:

* Az Apache Cassandra írási konzisztencia-szintje az Azure Cosmos-fiókban konfigurált alapértelmezett konzisztencia-szintre van leképezve. Az írási művelet (CL) konzisztenciája nem módosítható kérelmek alapján.

* Azure Cosmos DB dinamikusan leképezi a Cassandra-ügyfél illesztőprogramjában megadott olvasási konzisztencia-szintet az olvasási kérelemben dinamikusan konfigurált Azure Cosmos DB konzisztencia-szintek egyikére.

## <a name="mapping-consistency-levels"></a>Konzisztenciaszintek leképezése

Az alábbi táblázat azt mutatja be, hogy a natív Cassandra konzisztencia-szintjei hogyan vannak leképezve a Azure Cosmos DB konzisztencia-szintjére Cassandra API használatakor:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Cassandra konzisztencia-modell leképezése" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

Ha az Azure Cosmos-fiókja nem az erős konzisztencia mellett van konfigurálva, akkor megtudhatja, hogy az ügyfelek erős és konzisztens olvasási feladatokat szereznek a számítási feladatokhoz a *Probabilistically határos* elavulás (PBS) metrikájának megtekintésével. Ez a mérőszám a Azure Portalban érhető el. További információért lásd: a [Probabilistically kötött elévülés (PBS) mérőszámának figyelése](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

A valószínűséggel határos elavulás azt mutatja, hogy a végleges konzisztencia milyen módon lehetséges. Ez a mérőszám betekintést nyújt abba, hogy milyen gyakran lehet erősebb konzisztencia, mint az Azure Cosmos-fiókjában jelenleg konfigurált konzisztencia-szint. Más szóval az írási és olvasási régiók kombinációjának nagy mértékben konzisztens olvasásának valószínűsége (ezredmásodpercben mérve) látható.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Cosmos DB globális eloszlásáról és konzisztenciáji szintjeiről:

* [A globális terjesztés áttekintése](distribute-data-globally.md)
* [Konzisztencia-szint áttekintése](consistency-levels.md)
* [Magas rendelkezésre állás](high-availability.md)
