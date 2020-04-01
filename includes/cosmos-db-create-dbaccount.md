---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.custom: include file
ms.openlocfilehash: bfce10b0c9c55012c3ba2fb25121b28157f203b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672099"
---
1. Az [Azure Portalon](https://portal.azure.com/) hozzon létre egy Azure Cosmos DB-fiókot. Keresse meg és válassza az **Azure Cosmos DB** lehetőséget.

   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Válassza a **Hozzáadás** lehetőséget.
1. Az **Azure Cosmos DB-fiók létrehozása** lapon adja meg az új Azure Cosmos-fiók alapvető beállításait. 

    |Beállítás|Érték|Leírás |
    |---|---|---|
    |Előfizetés|Előfizetés neve|Válassza ki az Azure Cosmos-fiókhoz használni kívánt Azure-előfizetést. |
    |Erőforráscsoport|Erőforráscsoport neve|Jelöljön ki egy erőforráscsoportot, vagy válassza **az Új létrehozása**lehetőséget, majd adja meg az új erőforráscsoport egyedi nevét. |
    |Fiók neve|Egyedi név|Adjon meg egy nevet az Azure Cosmos-fiók azonosításához. Mivel *documents.azure.com* az URI létrehozásához megadott névhez van hozzáfűzve, használjon egyedi nevet.<br><br>A név csak kisbetűket, számokat és kötőjelet (-) tartalmazhat. 3-31 karakter közötti hosszúságúnak kell lennie.|
    |API|A létrehozandó fiók típusa|Válassza **a Core (SQL)** lehetőséget, ha sql szintaxissal szeretne dokumentum-adatbázist és lekérdezést létrehozni. <br><br>A létrehozni kívánt fiók típusát az API határozza meg. Az Azure Cosmos DB öt API-t biztosít: Core (SQL) és MongoDB a dokumentumadatokhoz, a Gremlin a gráfadatokhoz, az Azure Table és a Cassandra. Jelenleg létre kell hoznia egy külön fiókot minden API-hoz. <br><br>[További információ az SQL API-ról.](../articles/cosmos-db/documentdb-introduction.md)|
    |Ingyenes szintkedvezmény alkalmazása|Alkalmazás vagy Nem alkalmazás|Az Azure Cosmos DB ingyenes szinttel az első 400 RU/s és 5 GB tárterületet kapja ingyen egy fiókban. További információ az [ingyenes szintről.](https://azure.microsoft.com/pricing/details/cosmos-db/)|
    |Hely|A felhasználókhoz legközelebb eső régió|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb eső helyet, hogy a leggyorsabb hozzáférést biztosítsa számukra az adatokhoz.|
    |Fiók típusa|Termelés vagy nem gyártás|Válassza **a Termelés** lehetőséget, ha a fiókot éles számítási feladathoz fogja használni. Válassza a **Nem éles lehetőséget,** ha a fiókot nem éles, például fejlesztéshez, teszteléshez, minőségbiztosítási vagy átmeneti állapothoz fogja használni. Ez egy Azure-erőforrás-címke beállítás, amely beállítja a portál élményt, de nem befolyásolja az alapul szolgáló Azure Cosmos DB-fiók. Ezt az értéket bármikor módosíthatja.|


> [!NOTE]
> Azure-előfizetésenként legfeljebb egy ingyenes Azure Cosmos DB-fiókkal rendelkezhet, és a fiók létrehozásakor engedélyeznie kell. Ha nem látja az ingyenes szint kedvezményének alkalmazását, ez azt jelenti, hogy az előfizetésegy másik fiókja már engedélyezve van az ingyenes szinttel.
   
   ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Kihagyhatja a **Hálózat** és **címkék** szakaszt.

1. Tekintse át a fiókbeállításokat, és válassza a **Létrehozás gombot.** A fiók létrehozása néhány percet vesz igénybe. Várja meg, amíg a portállap megjelenik **A telepítés befejeződött.** 

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Válassza **az Ugrás az erőforráshoz** lehetőséget az Azure Cosmos DB-fiók lapra való ugráshoz. 

    ![Az Azure Cosmos DB-fiók lapja](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
