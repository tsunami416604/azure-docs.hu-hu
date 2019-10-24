---
title: Globálisan elosztott elemzéseket használó megoldások Azure Cosmos DBban.
description: Ismerkedjen meg a Azure Cosmos DB globálisan elosztott elemzési funkciójával felépíthető megoldásokkal.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d50b946e67bbcc171850b71021165356011f58e3
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755204"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Globálisan elosztott elemzést használó megoldások Azure Cosmos DB

Ez a cikk azokat a megoldásokat ismerteti, amelyek a Azure Cosmos DB globálisan elosztott elemzési funkciójával hozhatók felépítésre.

## <a name="retail-and-consumer-goods"></a>Kiskereskedelmi és fogyasztói termékek

A Azure Cosmos DB Spark-támogatásával valós idejű ajánlásokat és ajánlatokat biztosíthat. Segítheti az ügyfeleket abban, hogy a valós idejű személyre szabással és a termékkel kapcsolatos javaslatok alapján felfedezzék a szükséges elemeket.

* A Apache Spark Runtime beépített Machine Learning támogatásával valós idejű ajánlásokat készíthet a termékkatalógusokban.

* A stream-adatforrások, az adatvásárlások és az ügyféladatok lehetőségre kattintva megadhatja az élettartamot megcélzó célként megadott javaslatokat.

* A Azure Cosmos DB globális terjesztési funkciójának használatával a különböző régiókba tartozó termékek nagy mennyisége ezredmásodpercben elemezhető.

* Gyorsan lekérheti a földrajzilag elosztott felhasználók és az adatelemzést. Az előléptetés átváltási arányának növeléséhez a megfelelő felhasználót a megfelelő időben kell kiszolgálni a megfelelő felhasználó számára.

* Kihasználhatja a beépített Spark streaming-képességet az élő adattartalom dúsításához, ha a statikus ügyféladatok használatával egyesíti azt. Így valós időben és az ügyfelek által megvalósított kontextusban teheti elérhetővé a személyre szabott és a célcsoportot mutató hirdetéseket.

Az alábbi képen látható, hogyan használhatók a Azure Cosmos DB Spark-támogatás a díjszabás és a promóciók optimalizálásához:

![Azure Cosmos DB Spark-támogatás a díjszabás és a promóció optimalizálásához](./media/spark-api-introduction/optimize-pricing-and-promotions.png)


Az alábbi képen látható, hogyan használhatók a Azure Cosmos DB Spark-támogatás a valós idejű javaslatok motorjában:

![Azure Cosmos DB Spark-támogatás a valós idejű ajánlási motorban](./media/spark-api-introduction/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Gyártási és IoT

Azure Cosmos DB beépített elemzési platformja lehetővé teszi, hogy a IoT-adatok valós idejű elemzését globális szinten több millió eszközről is engedélyezzék. Olyan modern innovációkat végezhet, mint például az időjárási minták, a prediktív elemzések és az energia-optimalizálások előrejelzése.

* Azure Cosmos DB használatával olyan adatokat is megadhat, mint például a valós idejű eszközök mérőszámai és az időjárási tényezők, majd az intelligens Grid Analytics alkalmazásával optimalizálhatja a kapcsolódó eszközök teljesítményét a mezőben. Az intelligens Grid Analytics a működési költségek szabályozásának kulcsa, a rács megbízhatóságának javítása és személyre szabott energiaellátási szolgáltatások biztosítása a fogyasztóknak.

Az alábbi képen látható, hogyan használhatók a Azure Cosmos DB Spark-támogatás a IoT-eszközök metrikáinak olvasásához és az intelligens Grid Analytics alkalmazásához:

![Azure Cosmos DB Spark-támogatás a IoT-eszközök metrikáinak olvasásához](./media/spark-api-introduction/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Prediktív karbantartás

* Az olyan adategységek, mint például a kis fúrási tornyokban használt kompresszorok és a mélytengeri platformok fenntartása összetett törekvés. Ezek az eszközök a világ különböző részein találhatók, és petabájt hoznak. A Azure Cosmos DB használatával olyan teljes körű prediktív adatfolyamatot építhet ki, amely a Spark streaming használatával nagy mennyiségű érzékelő telemetria, tároló-alkatrészt és érzékelő-hozzárendelési adatfeldolgozást végez.

* Gépi tanulási modelleket hozhat létre és helyezhet üzembe, amelyekkel előre jelezheti az eszközök meghibásodását, és a hiba bekövetkezése előtt kiállíthatja a karbantartási munkavégzési rendeléseket.

Az alábbi képen látható, hogyan használható a Azure Cosmos DB Spark-támogatás a prediktív karbantartási rendszer létrehozásához:

![Azure Cosmos DB Spark-támogatás prediktív karbantartási rendszer létrehozásához](./media/spark-api-introduction/predictive-maintenance-system.png)

Az alábbi képen látható, hogyan használható a Azure Cosmos DB Spark-támogatás a valós idejű járművek diagnosztikai rendszerének létrehozásához:

![Azure Cosmos DB Spark-támogatás a valós idejű járművek diagnosztikai rendszerének létrehozásához](./media/spark-api-introduction/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Játékok

* A beépített Spark-támogatással a Azure Cosmos DB percek alatt könnyedén hozhat létre, méretezheti és helyezhet üzembe fejlett elemzési és gépi tanulási modelleket, hogy a lehető legjobb játékélményt hozza létre.

* A játékosok, a vásárlások és a viselkedési adatokat elemezheti a megfelelő személyre szabott ajánlatok létrehozásához, hogy magas konverziós arányt lehessen elérni.

* A Spark Machine learning segítségével elemzéseket készíthet a játék telemetria adatairól, és betekintést nyerhet. A lassú betöltési idő és a játékon belüli problémák diagnosztizálására és megelőzésére is lehetőség van.

Az alábbi képen látható, hogyan használják a Azure Cosmos DB Spark-támogatását a Gaming Analyticsben:

![Azure Cosmos DB Spark-támogatás a Gaming Analyticsben](./media/spark-api-introduction/gaming-analytics.png)

## <a name="next-steps"></a>Következő lépések

* Az Azure Cosmos DB előnyeinek megismeréséhez tekintse meg az [áttekintést](introduction.md) ismertető cikket.
* [Ismerkedés a MongoDB Azure Cosmos DB API-val](mongodb-introduction.md)
* [Ismerkedjen meg a Azure Cosmos DB Cassandra API](cassandra-introduction.md)
* [Ismerkedés a Azure Cosmos DB Gremlin API-val](graph-introduction.md)
* [Ismerkedjen meg a Azure Cosmos DB Table API](table-introduction.md)
