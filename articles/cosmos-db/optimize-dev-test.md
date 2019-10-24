---
title: Optimalizálás a Azure Cosmos DB fejlesztéséhez és teszteléséhez
description: Ez a cikk azt ismerteti, hogy Azure Cosmos DB több lehetőséget kínál a szolgáltatás ingyenes fejlesztésére és tesztelésére.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 992d165d323aab79bb7b5475aa396d4432691530
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754896"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>A fejlesztési és tesztelési díjak optimalizálása Azure Cosmos DB

Ez a cikk a Azure Cosmos DB fejlesztési és tesztelési célokra való használatának különböző lehetőségeit ismerteti díjmentesen.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB Emulator (helyileg letölthető verzió)

[Azure Cosmos db Emulator](local-emulator.md) egy helyi letölthető verzió, amely a Azure Cosmos db Cloud Service-t utánozza. Megírhatja és tesztelheti a Azure Cosmos DB API-kat használó kódokat, még akkor is, ha nincs hálózati kapcsolatban, és nem merül fel költségek. A Azure Cosmos DB Emulator helyi környezetet biztosít a felhőalapú szolgáltatás számára magas szintű megbízhatósággal rendelkező fejlesztési célokra. Az alkalmazást helyileg fejlesztheti és tesztelheti Azure-előfizetés létrehozása nélkül is. Ha készen áll az alkalmazás felhőben való üzembe helyezésére, frissítse a kapcsolati karakterláncot a felhőben lévő Azure Cosmos DB végponthoz való csatlakozáshoz, nincs szükség további módosításokra. [Egy CI/CD-folyamatot is beállíthat a Azure Cosmos db emulátor](tutorial-setup-ci-cd.md) felépítési feladattal az Azure DevOps a tesztek futtatásához. Első lépésként látogasson el a [Azure Cosmos db Emulator](local-emulator.md) cikkre.

## <a name="try-azure-cosmos-db-for-free"></a>Próbálja ki ingyen az Azure Cosmos DB-t

[Próbálja ki a Azure Cosmos db for Free](https://azure.microsoft.com/try/cosmosdb/) ingyenes élményét, amellyel adatbázisokat és gyűjteményeket hozhat létre, és kísérletezhet a felhőben Azure Cosmos db. Nem kell regisztrálnia az Azure-ba, és semmilyen díjat nem kell fizetnie. A kipróbálási Azure Cosmos DB fiókokat a rendszer jelenleg legfeljebb 30 napig elérhetővé teszi. Bármikor megújíthatja őket. Próbálja ki, Azure Cosmos DB fiókok megkönnyítik az alkalmazások kiAzure Cosmos DB értékelését, kiépítését és tesztelését a rövid útmutatók vagy oktatóanyagok segítségével. Létrehozhat egy bemutatót, vagy elvégezheti az egység tesztelését anélkül, hogy költségeket kellene kivonnia. Az ingyenes fiókok kipróbálása Azure Cosmos DB használatával kiértékelheti Azure Cosmos DB prémium szintű funkcióit ingyenesen, beleértve a kulcsrakész globális disztribúciót, a SLA-t és a konzisztencia-modelleket is. Létrehozhat egy legfeljebb 25 Azure Cosmos-tárolót tartalmazó adatbázist, és 10 000 RU/s teljesítményt. A minta alkalmazást futtathatja egy Azure-fiókra való feliratkozás vagy a bankkártya használata nélkül is. Az ingyenes kipróbálás Azure Cosmos DB használatával több régióból álló Azure Cosmos-fiókot hozhat létre, és néhány percen belül futtathat egy alkalmazást. Első lépésként tekintse [meg az ingyenes Azure Cosmos db kipróbálása](https://azure.microsoft.com/try/cosmosdb/) oldalt.

## <a name="azure-free-account"></a>Ingyenes Azure-fiók

A Azure Cosmos DBt az [ingyenes Azure-fiók](https://azure.microsoft.com/free)tartalmazza, amely egy adott időszakra ingyenesen kínál Azure-krediteket és-erőforrásokat. Kifejezetten a Azure Cosmos DB esetében ez az ingyenes fiók 5 GB tárhelyet és 400 RUs-t biztosít az egész évre kiépített átviteli sebességhez. Ez a megoldás lehetővé teszi, hogy a fejlesztők egyszerűen teszteljék Azure Cosmos DB funkcióit, vagy más Azure-szolgáltatásokkal integrálják azt nulla áron. Az ingyenes Azure-fiókkal $200 kreditet kap, amelyet az első 30 napban kell költeni. Nem számítunk fel díjat, még akkor sem, ha a szolgáltatást a frissítés megkezdése előtt elkezdi használni. Első lépésként látogasson el az [ingyenes Azure-fiók](https://azure.microsoft.com/free) oldalra.

## <a name="next-steps"></a>Következő lépések

Az emulátor vagy az ingyenes Azure Cosmos DB fiókok használatának első lépései a következő cikkekkel szerezhetők be:

* További információ a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ [a Azure Cosmos db-számla megismeréséről](understand-your-bill.md)
* További információ az [átviteli sebesség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási díjak optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási díjak optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések díjszabásának optimalizálásáról](optimize-cost-queries.md)
* További információ [a több régióból álló Azure Cosmos-fiókok díjainak optimalizálásáról](optimize-cost-regions.md)

