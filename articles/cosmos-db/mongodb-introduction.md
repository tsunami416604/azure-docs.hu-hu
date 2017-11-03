---
title: "Bevezetés az Azure Cosmos DB: API-t a MongoDB |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure Cosmos DB tárolására és a lekérdezés nagy mennyiségű JSON-dokumentumok, kisebb késést a népszerű OSS MongoDB API-k használatával."
keywords: Mi az a MongoDB
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: anhoh
ms.openlocfilehash: 4dbf91a3c1d6a287d7337647f9e059566c7ddbe5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-cosmos-db-api-for-mongodb"></a>Bevezetés az Azure Cosmos DB: API-t a mongodb-Protokolltámogatással

Az [Azure Cosmos DB](../cosmos-db/introduction.md) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása az alapvető fontosságú alkalmazásokhoz. Az Azure Cosmos DB az [iparág legjobb szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/cosmos-db/) által biztosított [teljes körű, globális terjesztést](distribute-data-globally.md) kínál, valamint [a teljesítmény és a tárterület rugalmas méretezését](partition-data.md) világszerte, az esetek 99%-ában egyszámjegyű ezredmásodperces késéseket, [öt jól meghatározott konzisztenciaszintet](consistency-levels.md) és garantált magas rendelkezésre állást. Az Azure Cosmos DB [automatikusan indexeli az adatokat](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) anélkül, hogy a felhasználónak sémákat és indexeket kellene kezelnie. Többmodelles szolgáltatás, amely támogatja a dokumentumokat, a kulcs-értékeket, a diagramokat és az oszlopos adatmodelleket. 

![Az Azure Cosmos DB: MongoDB API](./media/mongodb-introduction/cosmosdb-mongodb.png) 

Írt cosmos DB adatbázisok használható adattárként [MongoDB](https://docs.mongodb.com/manual/introduction/). Ez azt jelenti, hogy a meglévő [illesztőprogramok](https://docs.mongodb.org/ecosystem/drivers/), az alkalmazás írt MongoDB mostantól Cosmos DB kommunikálni és Cosmos DB adatbázisok használata helyett a MongoDB-adatbázisokat. Sok esetben válthat a MongoDB Cosmos DB használatával egyszerűen módosítja a kapcsolati karakterláncot. Ezzel a funkcióval könnyedén építhet és futtatási MongoDB adatbázis-alkalmazások az Azure felhőalapú Azure Cosmos DB globális eloszláshoz és [átfogó iparágvezető SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db), miközben továbbra is használja a megszokott ismeretei és eszközei mongodb-protokolltámogatással.


## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>Mi az az előnye, hogy a MongoDB-alkalmazások Azure Cosmos DB használatával?

**Rugalmasan méretezhető átviteli sebesség és tárterület:** könnyedén méretezhető felfelé vagy lefelé a MongoDB-adatbázist az alkalmazás igényeinek. Az adatok tárolása tartós állapotú meghajtón (SSD) történik az alacsony, előre jelezhető késés érdekében. Cosmos DB támogatja a MongoDB-gyűjteményekre, amelyek méretezhető, gyakorlatilag korlátlan tárterület mérete és a létesített átviteli sebesség. Rugalmasan méretezhető Cosmos DB kiszámítható teljesítmény zökkenőmentesen pedig az alkalmazás forgalmához igazítható. 

**Több területi replikációs:** Cosmos DB transzparens módon replikálja az adatokat minden egyes tartozó a MongoDB-fiókkal, amely lehetővé teszi, ugyanakkor biztosítható a közötti konzisztencia, rendelkezésre állásáról és teljesítményéről, mindezt megfelelő garanciák mellékhatásokkal adatok globális elérését igénylő alkalmazások fejlesztéséhez. Cosmos DB transzparens regionális feladatátvétel többhelyű API-khoz és rugalmasan méretezhető átviteli sebesség és tárterület világszerte képességét biztosítja. További információ: [adatok globálisan terjesztése](distribute-data-globally.md).

**MongoDB-kompatibilitási**: a meglévő MongoDB szakértői alkalmazáskód és tooling is használhatja. MongoDB használata alkalmazások fejlesztéséhez és a termelési a teljes körűen felügyelt globálisan elosztott Cosmos DB szolgáltatás használatával történő központi telepítésére.

**Egyetlen kiszolgálóból álló felügyeleti**: nem kell kezelni, a méretezés a MongoDB-adatbázisok. A cosmos DB egy teljes körűen felügyelt szolgáltatás, amely azt jelenti, hogy nem kell kezelniük bármilyen infrastruktúra vagy a virtuális gépek saját kezűleg. A cosmos DB érhető el 30 + [Azure-régiókat](https://azure.microsoft.com/regions/services/).

**Aprólékosan beállítható konzisztenciaszintek:** Öt jól meghatározott konzisztenciaszint közül választhat a konzisztencia és a teljesítmény közötti optimális kompromisszum elérése érdekében. A lekérdezések és olvasási műveletek Cosmos DB öt különböző konzisztenciaszintet kínál: erős, kötött elavulás, munkamenet, egységes előtag, és végleges. Ezek a részletes, jól meghatározott konzisztenciaszintek lehetővé teszik, hogy ésszerű kompromisszumot alakítson ki a konzisztencia, a rendelkezésre állás és a késleltetés között. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a konzisztenciaszintek használatával](consistency-levels.md).

**Az automatikus indexeléshez**: alapértelmezés szerint Cosmos DB automatikusan indexeli az összes tulajdonságainak belül dokumentumok a MongoDB adatbázis- és nem várt vagy igényel semmilyen sémát, illetve másodlagos indexek létrehozását.

**Enterprise osztályú** -Azure Cosmos DB támogatja több helyi replika képes biztosítani a helyi és regionális hibák állásuk rendelkezésre állási és adatvédelem 99,99 %. Az Azure Cosmos DB rendelkezik enterprise osztályú [megfelelőségi minősítései közül](https://www.microsoft.com/trustcenter) és biztonsági szolgáltatásokat. 

További az Azure-ban a Scott Hanselman és Azure Cosmos DB egyszerű mérnöki Manager, Kirill Gavrylyuk videó péntekig.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Introducing-Azure-Cosmos-DB/player]
> 

## <a name="how-to-get-started"></a>Első lépések

Kövesse a MongoDB quickstarts egy Cosmos DB fiók létrehozásához, és telepítse át a meglévő Mongo DB alkalmazást Cosmos-Adatbázist kíván használni, vagy egy új létrehozása:

* [Telepítse át egy meglévő Node.js MongoDB webalkalmazás](create-mongodb-nodejs.md).
* [A .NET- és az Azure-portálon MongoDB API webalkalmazás létrehozása](create-mongodb-dotnet.md)
* [Hozza létre a MongoDB API konzol alkalmazását a Java és az Azure-portálon](create-mongodb-java.md)

## <a name="next-steps"></a>Következő lépések

Azure Cosmos DB MongoDB API információ integrálva van a teljes Azure Cosmos DB dokumentációt, de az alábbiakban néhány mutatók az első lépésekhez:

* Kövesse a [összekapcsolás MongoDB-fiókkal](connect-mongodb-account.md) az oktatóanyag segítséget nyújt a kapcsolati karakterlánc fiókadatok beszerzéséről.
* Kövesse a [használata MongoChef rendelkező Azure Cosmos DB](mongodb-mongochef.md) oktatóanyag áttekintésével megismerheti, hogyan MongoChef az Azure Cosmos DB adatbázis és a MongoDB alkalmazás közötti kapcsolat létrehozásához.
* Kövesse a [adatok áttelepítése az Azure Cosmos Adatbázishoz protokoll támogatja a mongodb-protokolltámogatással](mongodb-migrate.md) az oktatóanyag segítséget nyújt az adatok importálása egy API-t a MongoDB-adatbázist.
* Az API-t a MongoDB-fiók használatával csatlakozni [Robomongo](mongodb-robomongo.md).
* Ismerje meg, hány RUs az operatív-t használ a [GetLastRequestStatistics parancs és az Azure portál metrikák](request-units.md#GetLastRequestStatistics).
* Megtudhatja, hogyan [írásvédett globálisan elosztott alkalmazások beállításainak konfigurálása](../cosmos-db/tutorial-global-distribution-mongodb.md).
