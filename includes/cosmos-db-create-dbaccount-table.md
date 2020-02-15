---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 2001d0621a340cbdb04c0bb5eea1166ce8b88eab
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212758"
---
1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. A bal oldali menüben válassza az **erőforrás létrehozása**lehetőséget.
   
   ![Erőforrás létrehozása a Azure Portalban](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png)
   
3. Az **új** lapon válassza az **adatbázisok** > **Azure Cosmos db**lehetőséget.
   
   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)
   
3. A **Azure Cosmos db fiók létrehozása** lapon adja meg az új Azure Cosmos db-fiók beállításait. 
 
    Beállítás|Érték|Leírás
    ---|---|---
    Előfizetést|Az Ön előfizetése|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|**Új létrehozása**, majd a fiók neve|Válassza az **Új létrehozása** lehetőséget. Ezután adjon meg egy új erőforráscsoport-nevet a fiókhoz. Az egyszerűség kedvéért használja ugyanazt a nevet, mint a Azure Cosmos DB fiók nevét. 
    Fiók neve|Egyedi név|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához.<br><br>A fiók neve csak kisbetűket, számokat és kötőjeleket (-) tartalmazhat, és 3 – 31 karakter hosszúságú lehet.
    API|Tábla|A létrehozni kívánt fiók típusát az API határozza meg. A Azure Cosmos DB öt API-t biztosít: Core (SQL) dokumentum-adatbázisokhoz, Gremlin, MongoDB, Azure Table és Cassandra-hoz. Minden API-hoz létre kell hoznia egy külön fiókot. <br><br>Válassza az **Azure-tábla**lehetőséget, mert ebben a rövid útmutatóban olyan táblát hoz létre, amely együttműködik a Table API. <br><br>[További információ a Table APIról](../articles/cosmos-db/table-introduction.md).|
    Hely|A felhasználókhoz legközelebb eső régió|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.

    A **Letiltás** lehetőséggel elhagyhatja a **geo-redundancia** és a **többrégiós írási** beállításokat a további díjak elkerülése érdekében, és kihagyhatja a **hálózat** és a **címkék** szakaszt.

5. Válassza a **felülvizsgálat + létrehozás**lehetőséget. Az ellenőrzés befejezése után válassza a **Létrehozás** lehetőséget a fiók létrehozásához. 
 
   ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. A fiók létrehozása néhány percet vesz igénybe. Ekkor megjelenik egy üzenet, amely jelzi, hogy **a telepítés folyamatban van**. Várjon, amíg a telepítés befejeződik, majd válassza az **erőforráshoz való ugrás**lehetőséget.

    ![Az Azure Portal értesítések panel](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)

