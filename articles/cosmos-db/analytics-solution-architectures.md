---
title: Globálisan elosztott elemzéseket használó megoldások az Azure Cosmos DB-ben.
description: Ismerje meg az Azure Cosmos DB globálisan elosztott elemzésekkel létrehozható megoldásokat.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d0b5042c1401a95cc75f4c01ace843659b6babfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73681754"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Globálisan elosztott elemzéseket használó megoldások az Azure Cosmos DB-ben

Ez a cikk ismerteti a megoldásokat, amelyek az Azure Cosmos DB globálisan elosztott elemzéshasználatával hozhathatók fel.

## <a name="retail-and-consumer-goods"></a>Kiskereskedelmi és fogyasztási cikkek

Az Azure Cosmos DB Spark-támogatásával valós idejű javaslatokat és ajánlatokat tehet. A valós idejű személyre szabással és termékajánlásokkal segíthet az ügyfeleknek felderíteni a szükséges elemeket.

* Az Apache Spark futásidejű beépített Machine Learning-támogatásával valós idejű javaslatokat hozhat létre a termékkatalógusokban.

* Az elemzési adatok, a beszerzési adatok és az ügyféladatok bányászhatva olyan célzott javaslatokat, amelyek a élettartam értékét meghajtók.

* Az Azure Cosmos DB globális terjesztési funkcióját használva a régiók között elosztott nagy mennyiségű termékadat ezredmásodpercben elemezhető.

* Gyorsan betekintést nyerhet a földrajzilag elosztott felhasználók és adatok. A promóciós konverziós arányt úgy javíthatja, hogy a megfelelő hirdetésnek a megfelelő felhasználónak a megfelelő időben kell megadnia.

* A beépített Spark-streamelési képesség kihasználva gazdagíthatja az élő adatokat statikus ügyféladatokkal kombinálva. Így személyre szabottabb és célzottabb hirdetéseket jeleníthet meg valós időben és az ügyfelek által végzett kontextusban.

Az alábbi képen bemutatjuk, hogyan használják az Azure Cosmos DB Spark támogatását az árak és promóciók optimalizálására:

![Az Azure Cosmos DB Spark-támogatása az árak és a promóciók optimalizálásához](./media/analytics-solution-architectures/optimize-pricing-and-promotions.png)


Az alábbi képen bemutatja, hogyan használja az Azure Cosmos DB Spark-támogatást a valós idejű ajánlási motorban:

![Az Azure Cosmos DB Spark támogatása a valós idejű ajánlási motorban](./media/analytics-solution-architectures/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Gyártás és IoT

Az Azure Cosmos DB beépített elemzési platformja lehetővé teszi az IoT-adatok valós idejű elemzését globális szinten több millió eszközről. Olyan modern újításokat hozhat, mint az időjárási minták előrejelzése, a prediktív elemzés és az energiaoptimalizálás.

* Az Azure Cosmos DB használatával kibányászhatja az adatokat, például a valós idejű eszköz-metrikákat és az időjárási tényezőket, majd intelligens hálózati elemzéseket alkalmazhat a terepen csatlakoztatott eszközök teljesítményének optimalizálásához. Az intelligens hálózati elemzés kulcsfontosságú a működési költségek szabályozásához, a hálózat megbízhatóságának javításához és a személyre szabott energiaszolgáltatások fogyasztóknak való biztosításához.

Az alábbi képen bemutatja, hogyan használható az Azure Cosmos DB Spark-támogatása az IoT-eszközök metrikáinak olvasására és az intelligens hálózatok elemzésének alkalmazására:

![Az Azure Cosmos DB Spark-támogatása az IoT-eszközök metrikáinak olvasásához](./media/analytics-solution-architectures/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Prediktív karbantartás

* Fenntartása eszközök, mint például a kompresszorok, hogy használják a kis fúrótornyok a mélytengeri platformok egy összetett törekvés. Ezek az eszközök világszerte találhatók, és petabájtnyi adatot generálnak. Az Azure Cosmos DB használatával létrehozhat egy teljes körű prediktív adatfolyamatot, amely a Spark streamelést használja nagy mennyiségű érzékelőtelemetria feldolgozásához, az eszközrészek és az érzékelő-leképezési adatok tárolásához.

* Gépi tanulási modelleket hozhat létre és helyezhet üzembe az eszközhibák előrejelzéséhez, mielőtt azok bekövetkeznének, és karbantartási munkarendeléseket adhat ki a hiba bekövetkezése előtt.

Az alábbi képen bemutatjuk, hogyan használja az Azure Cosmos DB Spark-támogatását egy prediktív karbantartási rendszer létrehozásához:

![Az Azure Cosmos DB Spark-támogatása egy prediktív karbantartási rendszer létrehozásához](./media/analytics-solution-architectures/predictive-maintenance-system.png)

Az alábbi képen bemutatjuk, hogyan használja az Azure Cosmos DB Spark-támogatását egy valós idejű járműdiagnosztikai rendszer létrehozásához:

![Az Azure Cosmos DB Spark-támogatása egy valós idejű járműdiagnosztikai rendszer létrehozásához](./media/analytics-solution-architectures/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Játékok

* A beépített Spark-támogatással az Azure Cosmos DB lehetővé teszi, hogy percek alatt egyszerűen elkészíthesse, méretezheti és üzembe helyezhesd a fejlett elemzési és gépi tanulási modelleket a lehető legjobb játékélmény érdekében.

* Elemezheti a játékos-, vásárlási és viselkedési adatokat, hogy releváns, személyre szabott ajánlatokat hozzon létre a magas konverziós arányok elérése érdekében.

* A Spark gépi tanulás használatával elemezheti és betekintést nyerhet a játék telemetriai adataiba. Diagnosztizálhatja és megelőzheti a lassú betöltési időket és a játékon belüli problémákat.

Az alábbi képen bemutatja, hogyan használja az Azure Cosmos DB Spark-támogatását a játékelemzésekben:

![Az Azure Cosmos DB Spark-támogatása a játékelemzésben](./media/analytics-solution-architectures/gaming-analytics.png)

## <a name="next-steps"></a>További lépések

* Az Azure Cosmos DB előnyeiről az [áttekintő](introduction.md) cikkben olvashat.
* [Ismerkedés a MongoDB Azure Cosmos DB API-jával](mongodb-introduction.md)
* [Ismerkedés az Azure Cosmos DB Cassandra API-val](cassandra-introduction.md)
* [Ismerkedjen meg az Azure Cosmos DB Gremlin API-val!](graph-introduction.md)
* [Ismerkedés az Azure Cosmos DB Table API-val](table-introduction.md)
