---
title: Bevezetés az Azure Cosmos DB API a mongodb-hez
description: Ismerje meg, hogyan használhatja az Azure Cosmos DB tárolja, és JSON-dokumentumok nagy mennyiségű lekérdezés a népszerű OSS MongoDB-vel alacsony késleltetéssel.
keywords: mi a MongoDB
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: overview
ms.date: 02/12/2018
ms.author: sclyon
ms.openlocfilehash: 52036dd0bdcdbc9d4ae71f388a7bb750fd781a5f
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547983"
---
# <a name="introduction-to-azure-cosmos-db-api-for-mongodb"></a>Bevezetés az Azure Cosmos DB API a mongodb-hez

Az [Azure Cosmos DB](../cosmos-db/introduction.md) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása az alapvető fontosságú alkalmazásokhoz. Az Azure Cosmos DB az [iparág legjobb szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/cosmos-db/) által biztosított [teljes körű, globális terjesztést](distribute-data-globally.md) kínál, valamint [a teljesítmény és a tárterület rugalmas méretezését](partition-data.md) világszerte, az esetek 99%-ában egyszámjegyű ezredmásodperces késéseket és garantált magas rendelkezésre állást. Az Azure Cosmos DB [automatikusan indexeli az adatokat](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) anélkül, hogy a felhasználónak sémákat és indexeket kellene kezelnie. Többmodelles szolgáltatás, amely támogatja a dokumentumokat, a kulcs-értékeket, a diagramokat és az oszlopos adatmodelleket. 

![Az Azure Cosmos DB API a mongodb-hez](./media/mongodb-introduction/cosmosdb-mongodb.png) 

Az Azure Cosmos DB-adatbázisok használhatók a [MongoDB](https://docs.mongodb.com/manual/introduction/)-hez írt alkalmazások adattáraként. Ez a funkció azt jelenti, hogy a meglévő [illesztők](https://docs.mongodb.org/ecosystem/drivers/) segítségével a MongoDB-hez írt alkalmazása mostantól képes kommunikálni az Azure Cosmos DB-vel, és használhat Azure Cosmos DB-adatbázisokat a MongoDB-adatbázisok helyett. Sok esetben egy kapcsolati sztring egyszerű módosításával válthat a MongoDB használatáról az Azure Cosmos DB-re. A funkció használatával könnyedén létrehozhat és futtathat globálisan elosztott MongoDB adatbázis-alkalmazásokat az Azure felhőben az Azure Cosmos DB-vel és az [átfogó, iparágvezető szolgáltatói szerződéseivel](https://azure.microsoft.com/support/legal/sla/cosmos-db), miközben továbbra is alkalmazhatja a MongoDB használata során megszerzett ismereteit és megszokott eszközeit.

**Kompatibilitás MongoDB**: Használhatja a meglévő MongoDB szakértelemmel, alkalmazáskód, és azokat az eszközöket, az Azure Cosmos DB megvalósítja a MongoDB átviteli protokoll. A MongoDB-vel alkalmazásokat fejleszthet, valamint üzembe helyezheti azokat az éles környezetben a teljesen felügyelt és globálisan elosztott Azure Cosmos DB szolgáltatással. További információ a támogatott verziókkal kapcsolatban: [A MongoDB protokoll támogatása](mongodb-feature-support.md#mongodb-protocol-support).

Az Azure Cosmos DB API a mongodb-hez nem használható közvetlen-végpontként szolgáltatásokhoz, például az Azure Stream Analytics, mert az informatikai ugyanazokkal a [ügyfélillesztők](https://docs.mongodb.org/ecosystem/drivers/) , a natív mongodb-hez. Integrálható az Azure Stream Analytics, érdemes lehet [Azure App Service](../app-service/app-service-web-overview.md) vagy [Azure Functions szolgáltatást](../azure-functions/functions-overview.md) közbenső szolgáltatás, amely tudjon adatokat írni az Azure Cosmos DB API a mongodb-hez.

## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>Milyen előnyökkel jár az Azure Cosmos DB MongoDB-alkalmazásokkal való használata?

**Rugalmasan méretezhető átviteli sebesség és tárterület:** Az alkalmazások igényeinek által könnyen méretezéssel a MongoDB-adatbázis. Az adatok tárolása SSD-meghajtón történik az alacsony, előre jelezhető késés érdekében. Az Azure Cosmos DB által támogatott MongoDB-gyűjtemények szinte korlátlanul méretezhető tárterületet és átviteli sebességet biztosítanak. Ahogy az alkalmazás növekszik, kiszámítható teljesítmény mellett, rugalmasan és zökkenőmentesen méretezheti az Azure Cosmos DB-t. 

**Többrégiós replikáció:** Az Azure Cosmos DB transzparensen replikálja az adatokat, a MongoDB-fiókjához társított, lehetővé téve a kompromisszumot kínál a konzisztencia, a rendelkezésre állás és a teljesítmény között a globális adatokhoz való hozzáférést igénylő alkalmazások fejlesztését, minden régióban , a megfelelő garanciákkal. Az Azure Cosmos DB transzparens regionális feladatátvételt biztosít a többkiszolgálós API-k segítségével, valamint világszerte rugalmasan méretezhető teljesítményt és tárolókapacitást nyújt. További információ: [Globális adatterjesztés](distribute-data-globally.md).

**Nem**: Nem kell kezelése és méretezése a MongoDB-adatbázisokat. Az Azure Cosmos DB egy teljesen felügyelt szolgáltatás, amely azt jelenti, hogy nem Önnek kell kezelnie az infrastruktúrákat és a virtuális gépeket. Az Azure Cosmos DB több mint 30 [Azure-régióban](https://azure.microsoft.com/regions/services/) érhető el.

**Aprólékosan beállítható konzisztenciaszintek:** Mivel az Azure Cosmos DB többmodelles API-k támogatja a konzisztencia beállítások a fiók szintjén és a konzisztencia kényszerítésének minden API vezérli. MongoDB 3.6, amíg hiba nem a munkamenet-konzisztencia fogalma nem, ha egy Azure Cosmos DB API-fiók használata a munkamenet-konzisztencia a konzisztencia MongoDB visszaminősíteni, végleges, mongodb-hez készült Azure Cosmos DB API használatakor. A mongodb-hez szüksége egy Azure Cosmos DB API-your-saját-olvasási garancia, ha a fiók alapértelmezett konzisztenciaszintjét erős vagy korlátozott frissesség, kell állítani. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a konzisztenciaszintek használatával](consistency-levels.md).

| Az Azure Cosmos DB alapértelmezett konzisztenciaszintje |   Mongo API (3.4) |
|---|---|
|Végleges| Végleges |
|Konzisztens előtag| Végleges, konzisztens sorrenddel |
|Munkamenet| Végleges, konzisztens sorrenddel |
|Kötött elavulás| Erős |
| Erős | Erős |

**Automatikus indexelés**: Alapértelmezés szerint az Azure Cosmos DB automatikusan indexeli az összes tulajdonság a MongoDB-adatbázis a dokumentumokon belül, és nem vár vagy igényel bármiféle séma vagy másodlagos indexek létrehozása sem. Emellett az egyedi indexelési képesség engedélyez egy egyediségi megkötést azokon a dokumentummezőkön, amelyeket a rendszer már automatikusan indexelt az Azure Cosmos DB-ben.

**Nagyvállalati szintű**: Az Azure Cosmos DB támogatja a több helyi replika biztosításához 99,99 %-os rendelkezésre állást és az adatvédelem érdekében a helyi és regionális meghibásodások esetén. Az Azure Cosmos DB vállalati szintű [megfelelőségi tanúsítványokkal](https://www.microsoft.com/trustcenter) és biztonsági funkciókkal rendelkezik. 

## <a name="how-to-get-started"></a>Első lépések

Kövesse a MongoDB rövid útmutatóinak lépéseit egy Azure Cosmos DB-fiók létrehozásához, és migrálja a meglévő MongoDB-alkalmazását az Azure Cosmos DB használatához, vagy hozzon létre egy újat:

* [Meglévő Node.js MongoDB-webalkalmazás migrálása](create-mongodb-nodejs.md).
* [.NET-tel és az Azure portal segítségével Azure Cosmos DB API a MongoDB-webalkalmazás létrehozása](create-mongodb-dotnet.md)
* [Konzolalkalmazás létrehozása Javával és az Azure portal segítségével Azure Cosmos DB API a mongodb-hez](create-mongodb-java.md)

## <a name="next-steps"></a>További lépések

Információ az Azure Cosmos DB API a mongodb-hez készült integrálva van a teljes Azure Cosmos DB-dokumentációt, de Íme néhány tanácsot a kezdéshez:

* Kövesse a [Csatlakozás egy MongoDB-fiókhoz](connect-mongodb-account.md) című oktatóanyag lépéseit, amelyből megtudhatja, hogyan kérheti le a fiókja kapcsolati sztringjének adatait.
* Kövesse [A Studio 3T (MongoChef) használata Azure Cosmos DB-vel](mongodb-mongochef.md) című oktatóanyag lépéseit, amelyből megtudhatja, hogyan hozhat létre kapcsolatot az Azure Cosmos DB-adatbázis és a MongoDB-alkalmazás között a Studio 3T-ben.
* Kövesse az [Adatok migrálása a MongoDB számára protokolltámogatást biztosító Azure Cosmos DB-be](mongodb-migrate.md) című oktatóanyag lépéseit az adatok a MongoDB-adatbázis egyik API-jába való importálásához.
* Csatlakozzon a MongoDB-fiók egyik API-jához a [Robomongo](mongodb-robomongo.md) használatával.
* Ismerje meg, hogyan [konfigurálhat olvasási beállításokat a globálisan elosztott alkalmazásokhoz](../cosmos-db/tutorial-global-distribution-mongodb.md).
