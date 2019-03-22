---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 4fec9be34e390498b85ecfcb3f3b61055a08fdd2
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305139"
---
1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali navigációs panelen válassza ki **erőforrás létrehozása**. Válassza ki **adatbázisok** majd **Azure Cosmos DB**.
   
   ![Képernyőkép az Azure Portalon, a további szolgáltatások és az Azure Cosmos DB menüpont kiemelve](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. Az a **létrehozása az Azure Cosmos DB-fiók** lap, adja meg az új Azure Cosmos DB-fiók beállításait:
 
    Beállítás|Érték|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetése|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|Új létrehozása<br><br>A használt egyedi nevet majd adja meg a azonosítója|Válassza az **Új létrehozása** lehetőséget. Majd adjon meg egy új erőforráscsoport neve a fiókjához. Az egyszerűség kedvéért használja ugyanazt a nevet, az azonosítóját. 
    Fiók neve|Adjon meg egy egyedi nevet|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához.<br><br>Az azonosító csak kisbetűket, számokat és a kötőjel (-) karaktert használhat. 3. és 31 karakter között kell lennie.
    API|Azure-tábla|A létrehozni kívánt fiók típusát az API határozza meg. Az Azure Cosmos DB öt API-kat biztosít: A dokumentum-adatbázis Core(SQL) a Gremlin gráf típusú adatbázisokat, a MongoDB, a dokumentum-adatbázis, az Azure Table és a Cassandra. Jelenleg minden API egy külön fiókot kell létrehoznia. <br><br>Válassza ki **Azure Table** mivel ebben a rövid útmutatóban hoz létre egy táblát, amely a Table API-val működik. <br><br>[További információ a Table API](../articles/cosmos-db/table-introduction.md).|
    Hely|Válassza ki a felhasználóihoz legközelebb eső régiót|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. A felhasználók számára tegye lehetővé számukra a lehető leggyorsabb hozzáférést biztosítsa az adatok legközelebb eső helyet használja.

    Hagyhatja az **Georedundancia** és **többrégiós ír** beállításokat alapértelmezett értékükön (**letiltása**) RU további díjak elkerülése érdekében. Folytassa a **hálózati** és **címkék** szakaszokat.

5. Válassza ki **felülvizsgálat + létrehozás**. Az érvényesítés befejezése után jelölje ki a **létrehozás** a fiók létrehozásához. 
 
   ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. A fiók létrehozása pár percet vesz igénybe. Ekkor megjelenik egy üzenet arról, hogy **folyamatban van a központi telepítés**. Várjon, amíg befejeződnek, és válassza ki a központi telepítés **erőforrás megnyitása**.

    ![Az Azure portál értesítési panelen](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)
