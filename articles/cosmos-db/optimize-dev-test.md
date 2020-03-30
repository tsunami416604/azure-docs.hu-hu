---
title: Optimalizálás az Azure Cosmos DB fejlesztéséhez és teszteléséhez
description: Ez a cikk bemutatja, hogy az Azure Cosmos DB hogyan kínál több lehetőséget a szolgáltatás ingyenes fejlesztésére és tesztelésére.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: d2ca7b7e4b637802df6a78c2493e3cc088f09881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246681"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Fejlesztési és tesztelési költségek optimalizálása az Azure Cosmos DB-ben

Ez a cikk ismerteti a különböző lehetőségeket az Azure Cosmos DB a fejlesztési és tesztelési ingyenes, valamint a fejlesztési költségek optimalizálására szolgáló technikákat.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB emulátor (helyileg letölthető verzió)

[Az Azure Cosmos DB-emulátor](local-emulator.md) egy helyi letölthető verzió, amely az Azure Cosmos DB felhőszolgáltatást utánozza. Írhat és tesztelhet kódot, amely az Azure Cosmos DB API-kat használja, még akkor is, ha nincs hálózati kapcsolat, és anélkül, hogy bármilyen költség. Az Azure Cosmos DB emulátor helyi környezetet biztosít fejlesztési célokra, a felhőszolgáltatáshoz való hűséggel. Az alkalmazást helyileg fejlesztheti és tesztelheti anélkül, hogy Azure-előfizetést hozna létre. Ha készen áll az alkalmazás felhőbe való üzembe helyezésére, frissítse a kapcsolati karakterláncot az Azure Cosmos DB-végponthoz való csatlakozáshoz a felhőben, nincs szükség további módosításokra. [Ci/CD-folyamatot is beállíthat az Azure Cosmos DB emulátor-alapú](tutorial-setup-ci-cd.md) buildelési feladattal az Azure DevOps-ban a tesztek futtatásához. Első lépések az [Azure Cosmos DB emulátor](local-emulator.md) cikk.

## <a name="azure-cosmos-db-free-tier"></a>Az Azure Cosmos DB ingyenes szintje 
Az Azure Cosmos DB ingyenes csomagja megkönnyíti az alkalmazások elindítását, fejlesztését és tesztelését, vagy akár kis éles számítási feladatok ingyenes futtatását. Ha az ingyenes szint engedélyezve van egy fiókban, az első 400 RU/s és 5 GB tárterület ingyenes lesz a fiókban. Létrehozhat egy megosztott átviteli adatbázist 25 tárolóval, amelyek 400 RU/s-ot osztanak meg az adatbázis szintjén, amelyek mindegyike ingyenes szint (5 megosztott átviteli adatbázis korlátozása egy ingyenes rétegfiókban). Az ingyenes szint a fiók élettartama alatt korlátlan ideig tart, és a rendszeres Azure Cosmos DB-fiók minden [előnyét és funkcióját](introduction.md#key-benefits) tartalmazza, beleértve a korlátlan tárhelyet és átviteli kapacitást (RU/s), az SLA-kat, a magas rendelkezésre állást, a kulcsrakész globális disztribúciót az összes Azure-régióban, és így tovább. Azure-előfizetésenként legfeljebb egy ingyenes rétegfiókkal rendelkezhet, és a fiók létrehozásakor engedélyeznie kell. A kezdéshez [hozzon létre egy új fiókot, amelyen engedélyezve van az ingyenes szint.](create-cosmosdb-resources-portal.md) További részleteket a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/) talál. 

## <a name="try-azure-cosmos-db-for-free"></a>Az Azure Cosmos DB ingyenes kipróbálása

[Próbálja ki az Azure Cosmos DB-t ingyenesen,](https://azure.microsoft.com/try/cosmosdb/) amely lehetővé teszi, hogy az Azure Cosmos DB-vel kísérletezzen a felhőben anélkül, hogy feliratkozna egy Azure-fiókra vagy hitelkártyával. Az Azure Cosmos DB-fiókok kipróbálása korlátozott ideig, jelenleg 30 napig érhető el. Ezeket bármikor megújíthatja. Próbálja ki az Azure Cosmos DB-fiókjait, amely megkönnyíti az Azure Cosmos DB kiértékelését, egy alkalmazás összeállítását és tesztelését, illetve a rövid útmutatók vagy oktatóanyagok használatát. Demót is létrehozhat, egységtesztelést végezhet, vagy akár több régióra kiterjedő fiókot is létrehozhat, és költség nélkül futtathat rajta egy alkalmazást. Egy Próbálja ki az Azure Cosmos DB-fiókjában egy megosztott átviteli adatbázis, legfeljebb 25 tárolók és 20 000 RU/s átviteli, vagy egy tároló legfeljebb 5000 RU/s. Első lépésekhez tekintse [meg az Azure Cosmos DB kipróbálása ingyenes](https://azure.microsoft.com/try/cosmosdb/) lapot.

## <a name="azure-free-account"></a>Ingyenes Azure-fiók

Az Azure Cosmos DB az [ingyenes Azure-fiók](https://azure.microsoft.com/free)része, amely egy bizonyos ideig ingyenesen kínál Azure-krediteket és erőforrásokat. Ez az ingyenes fiók 5 GB-os tárhelyet és 400 kiépített átviteli kapacitást kínál az egész évre. Ez a felület lehetővé teszi, hogy bármely fejlesztő egyszerűen tesztelje az Azure Cosmos DB funkcióit, vagy integrálja azt más Azure-szolgáltatásokkal nulla költség gel. Az ingyenes Azure-fiókkal az első 30 napban $200-os kreditet kap. Nem kell fizetnie, még akkor sem, ha elkezdi használni a szolgáltatásokat, amíg úgy nem dönt, hogy frissít. Első lépésekhez látogasson el [az Azure ingyenes fiókoldalára.](https://azure.microsoft.com/free)

## <a name="use-shared-throughput-databases"></a>Megosztott átviteliadat-adatbázisok használata

Egy [megosztott átviteli adatbázisban](set-throughput.md#set-throughput-on-a-database)az adatbázison belüli összes tároló megosztja az adatbázis kiosztott átviteli fáteresztő(RU)-ját. Ha például egy 400 RU/s-os adatbázist hoz üzembe, és négy tárolóval rendelkezik, mind a négy tároló megosztja a 400 RU/s-ot. Olyan fejlesztési vagy tesztelési környezetben, ahol minden tároló ritkábban érhető el, és így a legalább 400 RU/s-nál alacsonyabbra van szükség, a tárolók megosztott átviteli-adatbázisba helyezése segíthet a költségek optimalizálásában. 

Tegyük fel például, hogy a fejlesztési vagy tesztelési fiók négy tárolóval rendelkezik. Ha négy tárolót hoz létre dedikált átviteli fokkal (legalább 400 RU/s), a teljes RU/s 1600 RU/s lesz. Ezzel szemben, ha létrehoz egy megosztott átviteli adatbázist (legalább 400 RU/s), és a tárolókat ott helyezi el, a teljes RU/s csak 400 RU/s lesz. Általában a megosztott átviteli adatbázisok nagyszerű ek olyan esetekben, ahol nem kell garantált átviteli hang egyetlen egyes tárolókban.  További információ a [megosztott átviteli adatáteri adatbázisokról.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>További lépések

Az emulátor vagy az ingyenes Azure Cosmos DB-fiókok használatának első lépései a következő cikkekkel:

* További információ [az optimalizálásról fejlesztési és tesztelési célokra](optimize-dev-test.md)
* További információ [az Azure Cosmos DB-számlájának ismertetéséről](understand-your-bill.md)
* További információ az [átviteli költség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási költség optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási költségek optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések költségének optimalizálásáról](optimize-cost-queries.md)
* További információ a több régióra kiterjedő [Azure Cosmos-fiókok költségeinek optimalizálásáról](optimize-cost-regions.md)

