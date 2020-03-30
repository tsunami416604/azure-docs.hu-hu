---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ecf45a692f83689ed56c03bec13f291781508474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77061706"
---
1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. A bal oldali menüben válassza **az Erőforrás létrehozása parancsot.**
   
   ![Erőforrás létrehozása az Azure Portalon](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. Az **Új** lapon válassza az Adatbázisok Azure **Cosmos** > **DB**lehetőséget.
   
   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. Az **Azure Cosmos DB-fiók létrehozása** lapon adja meg az új Azure Cosmos DB-fiók beállításait. 
 
    Beállítás|Érték|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetése|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|Új létrehozása<br><br>Ezután adja meg ugyanazt a nevet, mint a fiókneve|Válassza az **Új létrehozása** lehetőséget. Ezután adjon meg egy új erőforráscsoport nevet a fiókjához. Az egyszerűség kedvéért használja ugyanazt a nevet, mint az Azure Cosmos DB-fiók neve. 
    Fiók neve|Adjon meg egy egyedi nevet|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához. A fiók URI-mongo.cosmos.azure.com *hozzá* fűzik az egyedi fióknevéhez.<br><br>A fióknév csak kisbetűket, számokat és kötőjeleket (-) használhat, és 3 és 31 karakter közötti nek kell lennie.
    API|Azure Cosmos DB a Mongo DB API-hoz|A létrehozni kívánt fiók típusát az API határozza meg. Az Azure Cosmos DB öt API-t biztosít: Core (SQL) a dokumentum-adatbázisok, Gremlin a gráf adatbázisok, Az Azure Cosmos DB Mongo DB API-dokumentum adatbázisok, Az Azure Table és a Cassandra. Jelenleg létre kell hoznia egy külön fiókot minden API-hoz. <br><br>Válassza az **Azure Cosmos DB-t a Mongo DB API-hoz,** mert ebben a rövid útmutatóban a MongoDB-val működő gyűjteményt hoz létre.<br><br>[További információ a MongoDB API-hoz való Azure Cosmos DB-ről.](../articles/cosmos-db/mongodb-introduction.md)|
    Hely|Válassza ki a felhasználóihoz legközelebb eső régiót|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.|

    Válassza **a Véleményezés+Létrehozás lehetőséget.** Kihagyhatja a **Hálózat** és **címkék szakaszt.** 

    ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. A fiók létrehozása eltarthat néhány percig. Várja meg, amíg a portál megjeleníti a **Gratulálunk! Az Azure Cosmos DB a Mongo DB API-fiók készen áll az** oldalon.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
