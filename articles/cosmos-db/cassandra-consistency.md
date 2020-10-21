---
title: Apache Cassandra és Azure Cosmos DB konzisztencia-szintek
description: Apache Cassandra és Azure Cosmos DB konzisztencia-szintek.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 155e1ed5b089c16887c845c94f36c9c6d9f0af6e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284103"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Apache Cassandra és Azure Cosmos DB konzisztencia-szintek

A Azure Cosmos DBtól eltérően az Apache Cassandra nem biztosít natív módon pontosan meghatározott konzisztencia-garanciákat. Az Apache Cassandra Ehelyett írási konzisztencia-szintet és olvasási konzisztencia-szintet biztosít a magas rendelkezésre állás, a konzisztencia és a késési kompromisszumok engedélyezéséhez. Azure Cosmos DB Cassandra API használatakor:

* Az Apache Cassandra írási konzisztencia-szintje az Azure Cosmos-fiókban konfigurált alapértelmezett konzisztencia-szintre van leképezve. Az írási művelet (CL) konzisztenciája nem módosítható kérelmek alapján.

* Azure Cosmos DB dinamikusan leképezi a Cassandra-ügyfél illesztőprogramjában megadott olvasási konzisztencia-szintet az olvasási kérelemben dinamikusan konfigurált Azure Cosmos DB konzisztencia-szintek egyikére.

## <a name="mapping-consistency-levels"></a>Konzisztencia-szintek megfeleltetése

Az alábbi táblázat azt mutatja be, hogy a natív Cassandra konzisztencia-szintjei hogyan vannak leképezve a Azure Cosmos DB konzisztencia-szintjére Cassandra API használatakor:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Cassandra konzisztencia-modell leképezése" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

Ha az Azure Cosmos-fiókja nem az erős konzisztencia mellett van konfigurálva, akkor megtudhatja, hogy az ügyfelek erős és konzisztens olvasási feladatokat szereznek a számítási feladatokhoz a *Probabilistically határos* elavulás (PBS) metrikájának megtekintésével. Ez a mérőszám a Azure Portalban érhető el. További információért lásd: a [Probabilistically kötött elévülés (PBS) mérőszámának figyelése](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

A valószínűséggel határos elavulás azt mutatja, hogy a végleges konzisztencia milyen módon lehetséges. Ez a mérőszám betekintést nyújt abba, hogy milyen gyakran lehet erősebb konzisztencia, mint az Azure Cosmos-fiókjában jelenleg konfigurált konzisztencia-szint. Más szóval az írási és olvasási régiók kombinációjának nagy mértékben konzisztens olvasásának valószínűsége (ezredmásodpercben mérve) látható.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Cosmos DB globális eloszlásáról és konzisztenciáji szintjeiről:

* [A globális terjesztés áttekintése](distribute-data-globally.md)
* [Konzisztencia-szint áttekintése](consistency-levels.md)
* [Magas rendelkezésre állás](high-availability.md)
