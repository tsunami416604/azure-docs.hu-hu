---
title: Áttelepítés utáni optimalizálás lépései Azure Cosmos DB API-MongoDB
description: Ez a dokumentum áttelepítés utáni optimalizálási technikákat biztosít a MongoDB Azure Cosmos DB APi-hoz a Mongo DB-hez.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c521546bedf1ebfd42bce4c50aa79b199553fd5a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441568"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Áttelepítés utáni optimalizálási lépések Azure Cosmos DB API-MongoDB való használatakor 

Miután áttelepítette a MongoDB-adatbázisban tárolt adatAzure Cosmos DB API-ját a MongoDB-hez, csatlakozhat Azure Cosmos DBhoz, és kezelheti az adatkezelési lehetőséget. Ez az útmutató az áttelepítés után megfontolandó lépéseket ismerteti. Az áttelepítési lépésekhez tekintse [meg a MongoDB áttelepítése a Azure Cosmos db API-ját a MongoDB oktatóanyagban](../dms/tutorial-mongodb-cosmos-db.md) .

Ebből az útmutatóból a következőket tudhatja meg:
- [Az alkalmazás összekötése](#connect-account)
- [Az indexelési házirend optimalizálása](#indexing)
- [Globális terjesztés konfigurálása a MongoDB-hez Azure Cosmos DB API-hoz](#distribute-data)
- [Konzisztencia szintjének beállítása](#consistency)

> [!NOTE]
> Az alkalmazás szintjén az egyetlen kötelező áttelepítés utáni lépés módosítja az alkalmazásban lévő kapcsolódási karakterláncot, hogy az új Azure Cosmos DB fiókra mutasson. Az összes többi áttelepítési lépés javasolt optimalizálás.
>

## <a id="connect-account"></a>Az alkalmazás összekötése 

1. Új ablakban jelentkezzen be a [Azure Portal](https://www.portal.azure.com/)
2. A [Azure Portal](https://www.portal.azure.com/)a bal oldali ablaktáblán nyissa meg a **minden erőforrás** menüt, és keresse meg azt a Azure Cosmos db fiókot, amelyre áttelepítette az adatait.
3. Nyissa meg a **kapcsolatok karakterláncának** paneljét. A jobb oldali panel tartalmazza a fiókhoz való kapcsolódáshoz szükséges összes információt.
4. Használja a kapcsolódási adatokat az alkalmazás konfigurációjában (vagy más kapcsolódó helyeken), hogy tükrözze a Azure Cosmos DB API-ját az MongoDB való kapcsolódáshoz. 
![kapcsolatok – karakterlánc](./media/mongodb-post-migration/connection-string.png)

További részletekért tekintse meg a [MongoDB-alkalmazás Összekötése Azure Cosmos db](connect-mongodb-account.md) lapra.

## <a id="indexing"></a>Az indexelési házirend optimalizálása

Alapértelmezés szerint a rendszer az összes adatmezőt automatikusan indexeli a Azure Cosmos DBba történő adatáttelepítés során. Sok esetben ez az alapértelmezett indexelési házirend elfogadható. Általánosságban elmondható, hogy az indexek eltávolítása optimalizálja az írási kérelmeket, és az alapértelmezett indexelési házirend (azaz automatikus indexelés) optimalizálja az olvasási kérelmeket.

Az indexeléssel kapcsolatos további információkért lásd: [adatindexelés Azure Cosmos db API-MongoDB](mongodb-indexing.md) , valamint a Azure Cosmos db cikkek [indexelése](index-overview.md) .

## <a id="distribute-data"></a>Az adatai globális terjesztése

A Azure Cosmos DB világszerte minden [Azure-régióban](https://azure.microsoft.com/regions/#services) elérhető. Miután kiválasztotta az alapértelmezett konzisztencia-szintet a Azure Cosmos DB-fiókhoz, hozzárendelhet egy vagy több Azure-régiót (a globális terjesztési igényektől függően). A magas rendelkezésre állás és az üzletmenet folytonossága érdekében mindig javasoljuk, hogy legalább két régiót futtasson. Áttekintheti a [többrégiós központi telepítések költségeit Azure Cosmos DBban](optimize-cost-regions.md).

Az adattovábbítás globális elosztása érdekében tekintse [meg az Adatterjesztés globálisan Azure Cosmos db API-MongoDB](tutorial-global-distribution-mongodb.md)című témakört. 

## <a id="consistency"></a>Konzisztencia szintjének beállítása
Azure Cosmos DB 5 jól meghatározott konzisztencia- [szintet](consistency-levels.md)kínál. A MongoDB és a Azure Cosmos DB konzisztencia-szintek közötti leképezésről a [konzisztencia-szintek és a Azure Cosmos db API](consistency-levels-across-apis.md)-k című cikkből tájékozódhat. Az alapértelmezett konzisztencia-szint a munkamenet konzisztenciáji szintje. A konzisztencia szintjének módosítása nem kötelező, és optimalizálható az alkalmazáshoz. A konzisztencia szintjének módosítása Azure Portal használatával:

1. Lépjen az **alapértelmezett konzisztencia** panelre a beállítások területen.
2. A [konzisztencia szintjének](consistency-levels.md) kiválasztása

A legtöbb felhasználó a konzisztencia szintjét az alapértelmezett munkamenet-konzisztencia beállításnál hagyja. Vannak azonban [rendelkezésre állási és teljesítménybeli kompromisszumok a különböző konzisztencia-szintekhez](consistency-levels-tradeoffs.md). 

## <a name="next-steps"></a>Következő lépések

* [MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB-hez](connect-mongodb-account.md)
* [Kapcsolódás Azure Cosmos DB fiókhoz a Studio 3T használatával](mongodb-mongochef.md)
* [Olvasási műveletek globális elosztása a Azure Cosmos DB API-MongoDB](mongodb-readpreference.md)
* [Adatok lejárttá tétele a MongoDB-hez készült Azure Cosmos DB API-val](mongodb-time-to-live.md)
* [Azure Cosmos DB konzisztenciáji szintjei](consistency-levels.md)
* [Indexelés Azure Cosmos DB](index-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)





