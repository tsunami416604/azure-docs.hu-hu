---
title: Megtudhatja, hogyan helyezhet át egy Azure Cosmos DB-fiókot egy másik régióba
description: Megtudhatja, hogyan helyezhet át egy Azure Cosmos DB-fiókot egy másik régióba
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 60c28a96008355491c058cd08dbbb3a1cbffad98
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059369"
---
# <a name="how-to-move-an-azure-cosmos-db-account-to-another-region"></a>Azure Cosmos DB fiók áthelyezése másik régióba

Ez a cikk azt ismerteti, hogyan helyezhet át egy olyan régiót, ahol az adatok replikálódnak Azure Cosmos DB, illetve hogyan telepítheti át a fiók (Azure Resource Manager) meta-adatait, valamint az egyik régióból a másikba történő adatátvitelt.

## <a name="move-data-from-one-region-to-another"></a>Adatok áthelyezése egyik régióból a másikba

Azure Cosmos DB támogatja az adatreplikálást natív módon, így az adatok az egyik régióból a másikba való áthelyezése egyszerű, és a Azure Portal, a Azure PowerShell vagy az Azure parancssori felület használatával valósítható meg, és a következő lépések végrehajtásával jár:

1. Új régió hozzáadása a fiókhoz

    Új régió Azure Cosmos DB-fiókhoz való hozzáadásához tekintse meg a [régiók hozzáadása/eltávolítása egy Azure Cosmos db-fiókhoz](how-to-manage-database-account.md#addremove-regions-from-your-database-account) című témakört.

1. Manuális feladatátvétel végrehajtása az új régióban

    Abban az esetben, ha az eltávolítandó régió jelenleg a fiók írási régiója, akkor a fent hozzáadott új régióban feladatátvételt kell kezdeményezni. Ez egy nulla állásidőt okozó művelet. Ha egy több régióból álló fiókban helyez át egy olvasási régiót, kihagyhatja ezt a lépést. Feladatátvétel elindításához lásd: [manuális feladatátvétel végrehajtása Azure Cosmos-fiókon](how-to-manage-database-account.md#manual-failover)

1. Az eredeti régió eltávolítása

    Régió eltávolítása egy Azure Cosmos DB fiókból: [régiók hozzáadása/eltávolítása egy Azure Cosmos db-fiókhoz](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

## <a name="migrate-azure-cosmos-db-account-meta-data"></a>Azure Cosmos DB-fiók meta-adatfájljainak migrálása

A Azure Cosmos DB nem támogatja natív módon a fiók metaadatainak áttelepítését az egyik régióból a másikba. Ha a fiók metaadatait és az ügyféladatokat is át szeretné telepíteni az egyik régióból a másikba, létre kell hoznia egy új fiókot a kívánt régióban, majd manuálisan át kell másolnia azokat. Az SQL API-hoz közel nulla állásidő-áttelepítéshez a [ChangeFeed](change-feed.md) vagy egy olyan eszközt kell használnia, amely kihasználja azt. Ha MongoDB API-t, Cassandra-t vagy más API-t telepít át, vagy ha többet szeretne megtudni az adatfiókok közötti áttelepítési lehetőségekről, tekintse [meg a helyszíni vagy a Felhőbeli adatAzure Cosmos DBra való Migrálás lehetőségeit](cosmosdb-migrationchoices.md) Az alábbi lépések bemutatják, hogyan telepíthet át egy Azure Cosmos DB-fiókot az SQL API-hoz és annak adatait az egyik régióból a másikba:

1. Új Azure Cosmos DB fiók létrehozása a kívánt régióban

    Ha Azure Portal, PowerShell vagy CLI használatával szeretne új fiókot létrehozni, [hozzon létre egy Azure Cosmos db fiókot](how-to-manage-database-account.md#create-an-account).

1. Új adatbázis és tároló létrehozása

    Új adatbázis és tároló létrehozásához tekintse meg [Az Azure Cosmos-tároló létrehozása](how-to-create-container.md) című témakört.

1. Az adatáttelepítés a Azure Cosmos DB élő adatáttelepítési eszközzel

    Az adatgyűjtés közel nulla állásidővel való áttelepítéséhez lásd: [Azure Cosmos db élő adatáttelepítési eszköz](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)

1. Alkalmazás-összekapcsolási sztring frissítése

    Ha az élő áttelepítési eszköz továbbra is fut, frissítse a kapcsolódási adatokat az alkalmazások új központi telepítésében. Az alkalmazáshoz tartozó végpontokat és kulcsokat a Azure Portal kérheti le.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Hozzáférés-vezérlés (IAM) a Azure Portal – NoSQL adatbázis biztonságának bemutatása":::

1. Kérelmek átirányítása új alkalmazásba

    Ha az új alkalmazás csatlakoztatva van Azure Cosmos DB az ügyfelek kérelmeit átirányíthatja az új központi telepítésre.

1. A már nem szükséges erőforrások törlése

    Ha a kérések mostantól teljesen átirányítva lettek az új példányra, törölheti a régi Azure Cosmos DB fiókot és az élő adatmigráló eszközt.

## <a name="next-steps"></a>Következő lépések

Az Azure Cosmos-fiók, valamint az adatbázis és a tárolók kezelésével kapcsolatos további információkért és példákért olvassa el a következő cikkeket:

* [Azure Cosmos-fiók kezelése](how-to-manage-database-account.md)
* [Adatcsatorna módosítása Azure Cosmos DB](change-feed.md)
