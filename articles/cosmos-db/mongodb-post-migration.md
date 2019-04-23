---
title: Optimalizálás az áttelepítést követő lépéseket használata az Azure Cosmos DB API a mongodb-hez
description: Ezt a dokumentumot tartalmaz az áttelepítés utáni optimalizálási technikákat MongoDB-ből az Azure Cosmos DB APi Mongo DB-hez készült.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c0c761fef481a1fdaa027f1329e9a4e72d62985a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013803"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Optimalizálás az áttelepítést követő lépéseket használata az Azure Cosmos DB API a mongodb-hez 

A mongodb-hez készült Azure Cosmos DB API a MongoDB-adatbázisban tárolt adatok, az áttelepítés után csatlakozhat az Azure Cosmos DB, és az adatok kezeléséhez. Ez az útmutató ismerteti a migrálás után érdemes lépéseit. Tekintse meg a [MongoDB Migrálása az Azure Cosmos DB API a MongoDB-oktatóanyag](../dms/tutorial-mongodb-cosmos-db.md) áttelepítési lépéseit.

Ebből az útmutatóból a következőket tudhatja meg:
- [Az alkalmazás csatlakoztatása](#connect-account)
- [Az indexelési házirendet optimalizálása](#indexing)
- [A MongoDB API-hoz az Azure Cosmos DB globális terjesztés konfigurálása](#distribute-data)
- [Set konzisztenciaszint](#consistency)

> [!NOTE]
> Az alkalmazás szintjén az csak a következő kötelező áttelepítés utáni lépésben módosul, hogy az új Azure Cosmos DB-fiók mutasson az alkalmazás a kapcsolati karakterláncot. Áttelepítés az összes többi optimalizálás használata ajánlott.
>

## <a id="connect-account"></a>Az alkalmazás csatlakoztatása 

1. Az egy új ablakban jelentkezzen be a [Azure Portalon](https://www.portal.azure.com/)
2. Az a [az Azure portal](https://www.portal.azure.com/), a bal oldali ablaktáblában nyissa meg a **összes erőforrás** menüben, és keresse meg az Azure Cosmos DB-fiókot, amelyhez az adatok átvitelét.
3. Nyissa meg a **kapcsolati karakterlánc** panelen. A jobb oldali panel tartalmazza a fiókhoz való kapcsolódáshoz szükséges összes információt.
4. Használja a kapcsolatadatokat az alkalmazás konfigurációs (vagy más releváns helyen), hogy az Azure Cosmos DB API a MongoDB kapcsolati az alkalmazásban. 
![Connection-String](./media/mongodb-post-migration/connection-string.png)

További részletekért tekintse meg a [egy Azure Cosmos DB MongoDB-alkalmazás csatlakoztatása](connect-mongodb-account.md) lapot.

## <a id="indexing"></a>Az indexelési házirendet optimalizálása

Az összes adat mezők automatikusan indexelt, alapértelmezés szerint az Azure Cosmos DB-adatok áttelepítése során. Sok esetben ez az alapértelmezett indexelési szabályzat elfogadható. Általában indexek eltávolítása optimalizálja az írási kérések, és alapértelmezett indexelési szabályzat (azaz a Automatikus indexelés) kellene optimalizálja az olvasási kéréseket.

Az indexelő további információkért lásd: [adatok indexelése az Azure Cosmos DB API a mongodb-hez](mongodb-indexing.md) , valamint a [indexelése az Azure Cosmos DB](index-overview.md) cikkeket.

## <a id="distribute-data"></a>Terjessze globálisan az adatait

Az Azure Cosmos DB érhető el az összes [Azure-régiók](https://azure.microsoft.com/regions/#services) világszerte. Miután az Azure Cosmos DB-fiókja alapértelmezett konzisztenciaszintjét, egy vagy több Azure-régiók (attól függően, a globális terjesztés szükségleteitől) is hozzárendelhetők. A magas rendelkezésre állás és üzleti folytonosság ajánlott legalább 2 régióban futó. Áttekintheti a tippek [optimalizálása az Azure Cosmos DB többrégiós üzemelő példányok költségei](optimize-cost-regions.md).

Terjessze globálisan az adatait, tekintse meg [terjesztheti az adatait globálisan az Azure Cosmos DB API a mongodb-hez](tutorial-global-distribution-mongodb.md). 

## <a id="consistency"></a>Set konzisztenciaszint
Az Azure Cosmos DB kínál jól definiált 5 [konzisztenciaszintek](consistency-levels.md). Olvassa el a mongodb-hez és az Azure Cosmos DB konzisztenciaszintjeinek közötti leképezéseket, olvassa el [konzisztenciaszintek és az Azure Cosmos DB API-k](consistency-levels-across-apis.md). Az alapértelmezett konzisztenciaszint a munkamenet-konzisztencia szintjét. A konzisztencia szintjének módosítása nem kötelező, és optimalizálhatja az alkalmazáshoz. Az Azure portal használatával konzisztenciaszint módosítása:

1. Nyissa meg a **alapértelmezett konzisztencia** a beállítások panelen.
2. Válassza ki a [konzisztenciaszint](consistency-levels.md)

A felhasználók többsége a konzisztencia szintjét az alapértelmezett munkamenet-konzisztencia beállításának, hagyja üresen. Vannak azonban [kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md). 

## <a name="next-steps"></a>További lépések

* [MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB-hez](connect-mongodb-account.md)
* [A Studio 3T használata az Azure Cosmos DB-fiók csatlakoztatása](mongodb-mongochef.md)
* [Hogyan terjessze globálisan olvassa be az Azure Cosmos DB API használatával a mongodb-hez](mongodb-readpreference.md)
* [Hamarosan lejár az adatokat az Azure Cosmos DB API a mongodb-hez](mongodb-time-to-live.md)
* [Az Azure Cosmos DB Konzisztenciaszint](consistency-levels.md)
* [Az Azure Cosmos DB indexelése](index-overview.md)
* [Az Azure Cosmos DB-kérésegységeiről](request-units.md)





