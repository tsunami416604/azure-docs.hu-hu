---
title: Rugalmas méretezés cassandra API-val az Azure Cosmos DB-ben
description: Ismerje meg az Azure Cosmos DB Cassandra API-fiók méretezésére rendelkezésre álló lehetőségeket és azok előnyeit/hátrányait
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 10d81de48c0d8f56c7c3fd26e3fd82a8c3df84c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474679"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Rugalmasan skálázegy Azure Cosmos DB Cassandra API-fiókot

Az Azure Cosmos DB API Cassandra rugalmas jellegének megismerésére számos lehetőség áll rendelkezésre. Az Azure Cosmos DB-ben a hatékony méretezéshez fontos megérteni, hogyan lehet a megfelelő mennyiségű kérelemegységet (RU/s) kiépíteni a rendszer teljesítményigényeinek figyelembevétele érdekében. Ha többet szeretne megtudni a kérelemegységekről, tekintse meg a [kérelemegységek](request-units.md) ről szóló cikket. 

A Cassandra API-hoz a .NET és a [Java SDK-k](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api)használatával lekérheti az egyes lekérdezések kérelemegység-díját. Ez hasznos a szolgáltatásban való rendelkezésre bocsátáshoz szükséges RU/ok mennyiségének meghatározásában.

![Az adatbázis-műveletek kérelemegységeket használnak fel](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Kezelési sebességkorlátozás (429 hiba)

Az Azure Cosmos DB a korlátozott (429) hibákat adja vissza, ha az ügyfelek több erőforrást (RU/s) használnak fel, mint a kiépített összeg. A Cassandra API az Azure Cosmos DB fordítja ezeket a kivételeket a túlterhelt hibák a Cassandra natív protokoll. 

Ha a rendszer nem érzékeny a késésre, elegendő lehet az átviteli sebesség korlátozásának kezelése újrapróbálkozások használatával. Tekintse meg a [Java-kód minta,](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) hogyan kezelhető sebességkorlátozás transzparens módon az [Azure Cosmos DB bővítmény](https://github.com/Azure/azure-cosmos-cassandra-extensions) Cassandra [újrapróbálkozási szabályzat](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) Java-ban használatával. A [Spark-bővítmény](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) t is használhatja a sebességkorlátozás kezeléséhez.

## <a name="manage-scaling"></a>Méretezés kezelése

Ha minimálisra kell csökkentenie a késést, a Cassandra API-ban a méretezési és kiépítési átviteli -rendszer (RUs) kezelésére számos lehetőség áll rendelkezésre:

* [Manuálisan az Azure Portal használatával](#use-azure-portal)
* [Programozott módon a vezérlősík funkcióinak használatával](#use-control-plane)
* [Programozott módon cql-parancsok használatával egy adott SDK-val](#use-cql-queries)
* [Dinamikusan az Autopilot használatával](#use-autopilot)

A következő szakaszok ismertetik az egyes megközelítések előnyeit és hátrányait. Ezután eldöntheti, hogy a legjobb stratégia egyensúlyt a skálázási igényeinek a rendszer, a teljes költség, és a hatékonyság igényeinek a megoldás.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Az Azure Portal használata

Az Azure Cosmos DB Cassandra API-fiók ban az erőforrások at az Azure Portal használatával méretezheti. További információ: A [tárolók és adatbázisok átviteli átviteli átbocsátásról szóló cikke.](set-throughput.md) Ez a cikk ismerteti az átviteli érték [adatbázis](set-throughput.md#set-throughput-on-a-database) vagy [tároló](set-throughput.md#set-throughput-on-a-container) szintű beállításának relatív előnyeit az Azure Portalon. Az ezekben a cikkekben említett "adatbázis" és "tároló" kifejezések a Cassandra API "keyspace" és "table" kifejezésére vannak leképezve.

Ennek a módszernek az az előnye, hogy ez egy egyszerű kulcsrakész módja az adatbázis átviteli kapacitásának kezelésére. A hátránya azonban az, hogy sok esetben a skálázási megközelítés bizonyos automatizálási szintek költséghatékonyak és nagy teljesítményűek lehetnek. A következő szakaszok ismertetik a vonatkozó forgatókönyveket és módszereket.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>A vezérlősík használata

Az Azure Cosmos DB Cassandra API-ja lehetővé teszi az átviteli kapacitás programozott módon történő beállítását a különböző vezérlősík-funkciók használatával. Útmutatást és mintákat az [Azure Resource Manager,](manage-cassandra-with-resource-manager.md)a [Powershell](powershell-samples-cassandra.md)és az [Azure CLI-cikkek](cli-samples-cassandra.md) című témakörben talál.

Ennek a módszernek az az előnye, hogy automatizálhatja az erőforrások felskálázását vagy lebontását egy időzítő alapján, hogy figyelembe vegye a csúcsaktivitást vagy az alacsony aktivitási időszakokat. Tekintse meg [itt](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) a mintát, hogyan valósíthatom meg ezt az Azure Functions és a Powershell használatával.

Ezzel a megközelítéssel hátránya lehet, hogy nem tud valós időben reagálni a kiszámíthatatlan változó méretezési igényekre. Ehelyett előfordulhat, hogy ki kell használnia az alkalmazáskörnyezetet a rendszerben, az ügyfél/SDK szinten vagy az [Autopilot](provision-throughput-autopilot.md)használatával.

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>CQL-lekérdezések használata egy adott SDK-val

A rendszert dinamikusan méretezheti a kóddal az adott adatbázis vagy tároló [CQL ALTER parancsainak](cassandra-support.md#keyspace-and-table-options) végrehajtásával.

Ennek a megközelítésnek az az előnye, hogy lehetővé teszi, hogy dinamikusan és az alkalmazásnak megfelelő egyéni módon reagáljon a méretezési igényekre. Ezzel a megközelítéssel továbbra is kihasználhatja a szokásos RU/s díjakat és díjakat. Ha a rendszer méretezési igényei többnyire kiszámíthatók (körülbelül 70% vagy több), az SDK CQL használatával az automatikus skálázás költséghatékonyabb módja lehet, mint az Autopilot használata. Ennek a megközelítésnek a hátránya, hogy meglehetősen összetett lehet az újrapróbálkozások végrehajtása, míg a sebességkorlátozás növelheti a késést.

## <a name="use-autopilot"></a><a id="use-autopilot"></a>Robotpilóta használata

Az átviteli műveletek manuális vagy programozott módja mellett az Azure cosmos-tárolókat is konfigurálhatja Autopilot módban. Az Autopilot mód automatikusan és azonnal a megadott RU-tartományokon belüli felhasználási igényekre méretezhető az SL-ek veszélyeztetése nélkül. További információkért tekintse meg az [Azure Cosmos-tárolók és adatbázisok létrehozása robotpilóta módban cikkben.](provision-throughput-autopilot.md)

Ennek a megközelítésnek az az előnye, hogy ez a legegyszerűbb módja a rendszer skálázási igényeinek kezelésére. Garantálja, hogy a beállított **RU-tartományokon belül**nem alkalmaznak sebességkorlátozást . A hátránya az, hogy ha a skálázási igények a rendszerben kiszámítható, Autopilot lehet egy kevésbé költséghatékony módja a skálázási igények, mint a testre szabott vezérlősík vagy SDK szint megközelítések a fent említett.

## <a name="next-steps"></a>További lépések

- Ismerkedés a [Cassandra API-fiókok, -adatbázisok és -táblák létrehozásával](create-cassandra-api-account-java.md) Java-alkalmazás használatával
