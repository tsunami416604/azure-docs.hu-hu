---
title: A migrálás utáni optimalizálási lépések az Azure Cosmos DB MongoDB-hoz való API-jával
description: Ez a dokumentum biztosítja a migrálás utáni optimalizálási technikákat a MongoDB-tól az Azure Cosmos DB APi-jéhez a Mongo DB-hez.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: ce33651aae64d0a90264dde6da64b4044c6ce132
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063616"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>A migrálás utáni optimalizálási lépések az Azure Cosmos DB MongoDB-hoz készült API-jának használatakor

Miután áttelepítette a MongoDB-adatbázisban tárolt adatokat az Azure Cosmos DB MongoDB-hoz kapcsolódó API-jába, csatlakozhat az Azure Cosmos DB-hez, és kezelheti az adatokat. Ez az útmutató az áttelepítés után figyelembe veje a szükséges lépéseket. Tekintse meg a [MongoDB áttelepítése az Azure Cosmos DB API-mongoDB-oktatóanyag](../dms/tutorial-mongodb-cosmos-db.md) a migrálási lépéseket.

Ebből az útmutatóból a következőket tudhatja meg:

- [Az alkalmazás csatlakoztatása](#connect-your-application)
- [Az indexelési házirend optimalizálása](#optimize-the-indexing-policy)
- [Globális disztribúció konfigurálása az Azure Cosmos DB MongoDB-hoz](#globally-distribute-your-data)
- [Konzisztenciaszint beállítása](#set-consistency-level)

> [!NOTE]
> Az alkalmazás szintjén az egyetlen kötelező áttelepítés utáni lépés az alkalmazás kapcsolati karakterláncának módosítása az új Azure Cosmos DB-fiókra való ponthoz. Minden más áttelepítési lépés ajánlott optimalizálás.
>

## <a name="connect-your-application"></a>Az alkalmazás csatlakoztatása

1. Egy új ablakban jelentkezzen be az [Azure Portalra](https://www.portal.azure.com/)
2. Az [Azure Portalon](https://www.portal.azure.com/)a bal oldali ablaktáblában nyissa meg a **Minden erőforrás** menüt, és keresse meg az Azure Cosmos DB-fiókot, amelyre áttelepítette az adatokat.
3. Nyissa meg a **Kapcsolati karakterlánc** panelt. A jobb oldali panel tartalmazza a fiókhoz való kapcsolódáshoz szükséges összes információt.
4. Használja az alkalmazás konfigurációjában (vagy más releváns helyeken) lévő kapcsolati információkat az Azure Cosmos DB MongoDB-kapcsolathoz való API-jának tükrözéséhez az alkalmazásban.
![Kapcsolat-karakterlánc](./media/mongodb-post-migration/connection-string.png)

További részletekért tekintse meg a [MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB-hoz](connect-mongodb-account.md) című lapon.

## <a name="optimize-the-indexing-policy"></a>Az indexelési házirend optimalizálása

Az összes adatmező alapértelmezés szerint automatikusan indexelésre kerül az Azure Cosmos DB-re való áttelepítés során. Sok esetben ez az alapértelmezett indexelési házirend elfogadható. Az indexek eltávolítása általában optimalizálja az írási kérelmeket, és az alapértelmezett indexelési házirend (azaz az automatikus indexelés) optimalizálja az olvasási kérelmeket.

Az indexelésről további információt az [Azure Cosmos DB MongoDB-hoz készült API-jában,](mongodb-indexing.md) valamint az [Azure Cosmos DB-cikkeiben található adatindexelés](index-overview.md) című témakörben talál.

## <a name="globally-distribute-your-data"></a>Az adatok globális terjesztése

Az Azure Cosmos DB világszerte az Azure összes [régiójában](https://azure.microsoft.com/regions/#services) elérhető. Miután kiválasztotta az Azure Cosmos DB-fiókjához az alapértelmezett konzisztenciaszintet, társíthat egy vagy több Azure-régiót (a globális terjesztési igényektől függően). A magas rendelkezésre állás és az üzletmenet folytonossága érdekében mindig javasoljuk, hogy legalább 2 régióban fusson. Tekintse át a tippeket [a több régiós telepítések költségeinek optimalizálásához az Azure Cosmos DB-ben.](optimize-cost-regions.md)

Az adatok globális terjesztéséhez olvassa el az [Adatok globális terjesztése az Azure Cosmos DB MongoDB API-ján.](tutorial-global-distribution-mongodb.md)

## <a name="set-consistency-level"></a>Konzisztenciaszint beállítása

Az Azure Cosmos DB 5 jól meghatározott [konzisztenciaszintet](consistency-levels.md)kínál. A MongoDB és az Azure Cosmos DB konzisztenciaszintek közötti leképezésről a [Konzisztenciaszintek és az Azure Cosmos-adatbázis-házirendek című olvasni való leképezése.](consistency-levels-across-apis.md) Az alapértelmezett konzisztenciaszint a munkamenet konzisztenciaszintje. A konzisztenciaszint módosítása nem kötelező, és optimalizálhatja azt az alkalmazáshoz. A konzisztenciaszint módosítása az Azure Portal használatával:

1. Lépjen az **Alapértelmezett konzisztencia** panelre a Beállítások csoportban.
2. Válassza ki a [konzisztenciaszintet](consistency-levels.md)

A legtöbb felhasználó a konzisztenciaszintet az alapértelmezett munkamenet-konzisztencia-beállítással hagyja. Vannak azonban [rendelkezésre állási és teljesítmény kompromisszumok a különböző konzisztenciaszintekhez.](consistency-levels-tradeoffs.md)

## <a name="next-steps"></a>További lépések

* [MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB-hez](connect-mongodb-account.md)
* [Csatlakozás Azure Cosmos DB-fiókhoz a Studio 3T használatával](mongodb-mongochef.md)
* [Olvasások globális terjesztése az Azure Cosmos DB MongoDB-hoz való API-jával](mongodb-readpreference.md)
* [Adatok lejárttá tétele a MongoDB-hez készült Azure Cosmos DB API-val](mongodb-time-to-live.md)
* [Konzisztenciaszintek az Azure Cosmos DB-ben](consistency-levels.md)
* [Indexelés az Azure Cosmos DB-ben](index-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)