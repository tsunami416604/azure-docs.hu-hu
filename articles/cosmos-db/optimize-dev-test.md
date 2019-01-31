---
title: Optimalizálása fejlesztéshez és teszteléshez az Azure Cosmos DB
description: Ez a cikk bemutatja, hogyan Azure Cosmos DB több lehetőséget kínál a fejlesztési és tesztelési a szolgáltatás ingyenes.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: rimman
ms.openlocfilehash: 3bb39fe99e612fb19f12fd049ebb8f600e48373e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457870"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Fejlesztési és tesztelési költségek az Azure Cosmos DB optimalizálása

Ez a cikk az Azure Cosmos DB használata a fejlesztési és tesztelési ingyenes költség különböző lehetőségeket ismerteti.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Az Azure Cosmos DB emulator (helyileg letölthető verzió)

[Az Azure Cosmos DB emulator](local-emulator.md) helyi letölthető verziót, amely az Azure Cosmos DB felhőszolgáltatásról utánozza van. Írhat, és tesztelje a kódot, amely az Azure Cosmos DB API-kat használja, akkor is, ha nincs hálózati kapcsolat van, és bármely költségek nélkül. Az Azure Cosmos DB emulatort fejlesztési célra szolgáló helyi környezetet biztosít kiváló minőségben a felhőszolgáltatáshoz. Fejlesztheti és tesztelheti alkalmazását helyileg, egy Azure-előfizetés létrehozása nélkül. Ha készen áll a felhőben üzembe helyezni, frissítse a kapcsolati karakterláncot a felhőben az Azure Cosmos DB végponthoz csatlakozik, nem módosított előfizetésekben van szükség. Emellett [állítsa be az Azure Cosmos DB emulator rendelkező CI/CD folyamatot](tutorial-setup-ci-cd.md) készítése feladatot az Azure DevOps-tesztek futtatásához. Funkcionáló megkezdheti a [Azure Cosmos DB emulator](local-emulator.md) cikk.

## <a name="try-azure-cosmos-db-for-free"></a>Az Azure Cosmos DB ingyenes kipróbálása

[Próbálja ki ingyenesen az Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) díj környezetet biztosít, amely lehetővé teszi, hogy az adatbázis és gyűjtemény létrehozása, és kísérletezzen az Azure Cosmos DB a felhőben egy ingyenes. Nem Azure-hoz való regisztráció van, vagy fizetnie utánuk. A próbálja meg az Azure Cosmos DB-fiókok csak korlátozott ideig, jelenleg 30 napig érhetők el. Is megújítani őket, bármikor. Próbálja ki az Azure Cosmos DB fiókok segítségével egyszerűen kiértékelheti az Azure Cosmos DB, létrehozását és a egy alkalmazás tesztelése a gyors útmutatókat vagy oktatóanyagokat használatával. Hozzon létre egy bemutatót, vagy hajtsa végre a egységtesztelés szolgáltatásért nélkül. Az Azure Cosmos DB kipróbálása ingyenes fiókok, kiértékelheti az Azure Cosmos DB prémium szintű lehetőségek ingyenesen, többek között a kulcsrakész globális terjesztés, SLA-k és a konzisztencia modellek. Legfeljebb 25 Azure Cosmos-tárolók és 10 000 RU/s átviteli hozhat létre egy adatbázist. Az Azure-fiók előfizetés vagy a hitelkártya használata nélkül futtathatja a mintaalkalmazást. Az Azure Cosmos DB kipróbálása ingyenes, hozhat létre egy többrégiós Azure Cosmos-fiók és futtathat egy alkalmazást, mindössze néhány perc múlva. Első lépésként lásd [próbálja ki ingyenesen az Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) lapot.

## <a name="azure-free-account"></a>Ingyenes Azure-fiók

Az Azure Cosmos DB része a [ingyenes Azure-fiók](https://azure.microsoft.com/free), amely Azure-kredit és erőforrások ingyenes az biztosít egy adott időszakra. Kifejezetten az Azure Cosmos DB az ingyenes fiók az egész évre 5 GB-os tárolási és a létesített átviteli sebesség 400 kérelemegység kínál. Ez lehetővé teszi, hogy minden fejlesztő számára könnyen tesztelése az Azure Cosmos DB funkcióit, vagy díjmentesen más Azure-szolgáltatásokkal integrálva. Az ingyenes Azure-fiók az első 30 napban fordítania 200 USD értékű kreditet kap. Nem kell fizetnie, még akkor is, ha megkezdi a szolgáltatások használatát, amíg nem frissít. Első lépésként látogasson el a [ingyenes Azure-fiók](https://azure.microsoft.com/free) lapot.

## <a name="next-steps"></a>További lépések

Ön az emulátorban, vagy az ingyenes Azure Cosmos DB-fiók használata a következő cikkek az első lépések:

* Tudjon meg többet [optimalizálása fejlesztéshez és teszteléshez](optimize-dev-test.md)
* Tudjon meg többet [az Azure Cosmos DB-elszámolások ismertetése](understand-your-bill.md)
* Tudjon meg többet [átviteli költségek optimalizálása](optimize-cost-throughput.md)
* Tudjon meg többet [tárolási költségek optimalizálása](optimize-cost-storage.md)
* Tudjon meg többet [olvasási és írási a költségek optimalizálása](optimize-cost-reads-writes.md)
* Tudjon meg többet [lekérdezések költségeinek optimalizálása](optimize-cost-queries.md)
* Tudjon meg többet [többrégiós Azure Cosmos-fiókok költségeinek optimalizálása](optimize-cost-regions.md)

