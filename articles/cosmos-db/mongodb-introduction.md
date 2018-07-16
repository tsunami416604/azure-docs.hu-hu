---
title: 'Bevezetés az Azure Cosmos DB használatába: MongoDB API | Microsoft Docs'
description: Ez a cikk azt ismerteti, hogy miként használható az Azure Cosmos DB közel valós idejű adateléréssel nagy mennyiségű JSON-dokumentum tárolására és lekérdezésére a népszerű OSS MongoDB API-k használatával.
keywords: mi a MongoDB
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: overview
ms.date: 02/12/2018
ms.author: sngun
ms.openlocfilehash: 214dfe3e676d3b07cf688fa0f7dcaf11462edfe8
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930885"
---
# <a name="introduction-to-azure-cosmos-db-mongodb-api"></a>Alapvető ismeretek az Azure Cosmos DB MongoDB API-ról

Az [Azure Cosmos DB](../cosmos-db/introduction.md) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása az alapvető fontosságú alkalmazásokhoz. Az Azure Cosmos DB az [iparág legjobb szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/cosmos-db/) által biztosított [teljes körű, globális terjesztést](distribute-data-globally.md) kínál, valamint [a teljesítmény és a tárterület rugalmas méretezését](partition-data.md) világszerte, az esetek 99%-ában egyszámjegyű ezredmásodperces késéseket és garantált magas rendelkezésre állást. Az Azure Cosmos DB [automatikusan indexeli az adatokat](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) anélkül, hogy a felhasználónak sémákat és indexeket kellene kezelnie. Többmodelles szolgáltatás, amely támogatja a dokumentumokat, a kulcs-értékeket, a diagramokat és az oszlopos adatmodelleket. 

![Azure Cosmos DB: MongoDB API](./media/mongodb-introduction/cosmosdb-mongodb.png) 

Az Azure Cosmos DB-adatbázisok használhatók a [MongoDB](https://docs.mongodb.com/manual/introduction/)-hez írt alkalmazások adattáraként. Ez a funkció azt jelenti, hogy a meglévő [illesztők](https://docs.mongodb.org/ecosystem/drivers/) segítségével a MongoDB-hez írt alkalmazása mostantól képes kommunikálni az Azure Cosmos DB-vel, és használhat Azure Cosmos DB-adatbázisokat a MongoDB-adatbázisok helyett. Sok esetben egy kapcsolati sztring egyszerű módosításával válthat a MongoDB használatáról az Azure Cosmos DB-re. A funkció használatával könnyedén létrehozhat és futtathat globálisan elosztott MongoDB adatbázis-alkalmazásokat az Azure felhőben az Azure Cosmos DB-vel és az [átfogó, iparágvezető szolgáltatói szerződéseivel](https://azure.microsoft.com/support/legal/sla/cosmos-db), miközben továbbra is alkalmazhatja a MongoDB használata során megszerzett ismereteit és megszokott eszközeit.

**MongoDB-kompatibilitás**: Használhatja a MongoDB-vel kapcsolatos meglévő szakértelmét, alkalmazáskódját és eszközeit, mivel az Azure Cosmos DB a MongoDB átviteli protokollját valósítja meg. A MongoDB-vel alkalmazásokat fejleszthet, valamint üzembe helyezheti azokat az éles környezetben a teljesen felügyelt és globálisan elosztott Azure Cosmos DB szolgáltatással. További információ a támogatott verziókkal kapcsolatban: [A MongoDB protokoll támogatása](mongodb-feature-support.md#mongodb-protocol-support).

## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>Milyen előnyökkel jár az Azure Cosmos DB MongoDB-alkalmazásokkal való használata?

**Rugalmasan méretezhető átviteli sebesség és tárterület:** Könnyedén növelheti vagy csökkentheti MongoDB-adatbázisának méretét az alkalmazás igényeinek megfelelően. Az adatok tárolása SSD-meghajtón történik az alacsony, előre jelezhető késés érdekében. Az Azure Cosmos DB által támogatott MongoDB-gyűjtemények szinte korlátlanul méretezhető tárterületet és átviteli sebességet biztosítanak. Ahogy az alkalmazás növekszik, kiszámítható teljesítmény mellett, rugalmasan és zökkenőmentesen méretezheti az Azure Cosmos DB-t. 

**Többrégiós replikáció:** Az Azure Cosmos DB transzparensen replikálja az adatait a MongoDB-fiókjához társított összes régióba, lehetővé téve a globális adathozzáférést igénylő alkalmazások fejlesztését, és kompromisszumot kínál a konzisztencia, a rendelkezésre állás és a teljesítmény között, a megfelelő garanciákkal. Az Azure Cosmos DB transzparens regionális feladatátvételt biztosít a többkiszolgálós API-k segítségével, valamint világszerte rugalmasan méretezhető teljesítményt és tárolókapacitást nyújt. További információ: [Globális adatterjesztés](distribute-data-globally.md).

**Nincs kiszolgálókezelés**: Nem szükséges kezelnie és méreteznie a MongoDB-adatbázisait. Az Azure Cosmos DB egy teljesen felügyelt szolgáltatás, amely azt jelenti, hogy nem Önnek kell kezelnie az infrastruktúrákat és a virtuális gépeket. Az Azure Cosmos DB több mint 30 [Azure-régióban](https://azure.microsoft.com/regions/services/) érhető el.

**Beállítható konzisztenciaszintek**: Mivel az Azure Cosmos DB támogatja a többmodelles API-kat, a konzisztenciabeállítások a fiók szintjén alkalmazhatók, és a konzisztencia kikényszerítését az egyes API-k vezérlik. A MongoDB 3.6-ig nem létezett a munkamenet-konzisztencia fogalma, ezért amikor beállít egy MongoDB API-fiókot a munkamenet-konzisztencia használatára, a MongoDB API-k használatakor a rendszer visszaállítja a konzisztenciát véglegesre. Ha „saját írások olvasása” garanciára van szüksége egy MongoDB API-fiókhoz, a fiók alapértelmezett konzisztenciaszintjét erősre vagy kötött elavulásra kell állítani. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a konzisztenciaszintek használatával](consistency-levels.md).

| Az Azure Cosmos DB alapértelmezett konzisztenciaszintje |   Mongo API (3.4) |
|---|---|
|Végleges| Végleges |
|Konzisztens előtag| Végleges, konzisztens sorrenddel |
|Munkamenet| Végleges, konzisztens sorrenddel |
|Kötött elavulás| Erős |
| Erős | Erős |

**Automatikus indexelés**: Alapértelmezés szerint az Azure Cosmos DB automatikusan indexeli a MongoDB-adatbázis dokumentumaiban található összes tulajdonságot, nem vár vagy igényel semmilyen sémát, és nem szükséges a másodlagos indexek létrehozása sem. Emellett az egyedi indexelési képesség engedélyez egy egyediségi megkötést azokon a dokumentummezőkön, amelyeket a rendszer már automatikusan indexelt az Azure Cosmos DB-ben.

**Vállalati szintű**: Az Azure Cosmos DB több helyi replikát támogat, hogy biztosítsa a 99,9%-os rendelkezésre állást és az adatvédelmet a helyi és regionális meghibásodások esetén. Az Azure Cosmos DB vállalati szintű [megfelelőségi tanúsítványokkal](https://www.microsoft.com/trustcenter) és biztonsági funkciókkal rendelkezik. 

További információért tekintse meg ezt a videót az Azure Cosmos DB vezető programmenedzserével, Aleksey Savateyevvel.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T136/player]
> 

## <a name="how-to-get-started"></a>Első lépések

Kövesse a MongoDB rövid útmutatóinak lépéseit egy Azure Cosmos DB-fiók létrehozásához, és migrálja a meglévő MongoDB-alkalmazását az Azure Cosmos DB használatához, vagy hozzon létre egy újat:

* [Meglévő Node.js MongoDB-webalkalmazás migrálása](create-mongodb-nodejs.md).
* [MongoDB API-webalkalmazás létrehozása a .NET és az Azure Portal használatával](create-mongodb-dotnet.md)
* [MongoDB API konzolalkalmazás létrehozása Java és az Azure Portal használatával](create-mongodb-java.md)

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB MongoDB API-val kapcsolatos információk az Azure Cosmos DB általános dokumentáció részét képezik, azonban íme néhány hivatkozás az első lépések megtételéhez:

* Kövesse a [Csatlakozás egy MongoDB-fiókhoz](connect-mongodb-account.md) című oktatóanyag lépéseit, amelyből megtudhatja, hogyan kérheti le a fiókja kapcsolati sztringjének adatait.
* Kövesse [A Studio 3T (MongoChef) használata Azure Cosmos DB-vel](mongodb-mongochef.md) című oktatóanyag lépéseit, amelyből megtudhatja, hogyan hozhat létre kapcsolatot az Azure Cosmos DB-adatbázis és a MongoDB-alkalmazás között a Studio 3T-ben.
* Kövesse az [Adatok migrálása a MongoDB számára protokolltámogatást biztosító Azure Cosmos DB-be](mongodb-migrate.md) című oktatóanyag lépéseit az adatok a MongoDB-adatbázis egyik API-jába való importálásához.
* Csatlakozzon a MongoDB-fiók egyik API-jához a [Robomongo](mongodb-robomongo.md) használatával.
* Ismerje meg, hogy mennyi kérelemegységet használnak a műveletei a [GetLastRequestStatistics paranccsal és az Azure Portal metrikáival](set-throughput.md#GetLastRequestStatistics).
* Ismerje meg, hogyan [konfigurálhat olvasási beállításokat a globálisan elosztott alkalmazásokhoz](../cosmos-db/tutorial-global-distribution-mongodb.md).
