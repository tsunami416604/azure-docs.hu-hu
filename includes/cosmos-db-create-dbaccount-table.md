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
ms.openlocfilehash: 0b9abb80969209fa31f8db6236e57dc817a89f6f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908139"
---
1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben kattintson a **erőforrás létrehozása**, kattintson a **adatbázisok**, majd válassza ki **Azure Cosmos DB**. 

3. Az a **létrehozása az Azure Cosmos DB-fiók** lap, adja meg az új Azure Cosmos DB-fiók beállításait. 
 
    Beállítás|Érték|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetése|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|Új létrehozása<br><br>A használt egyedi nevet majd adja meg a azonosítója|Válassza az **Új létrehozása** lehetőséget. Majd adja meg a fiók új erőforráscsoport nevét. Az egyszerűség kedvéért használja ugyanazt a nevet, az azonosítóját. 
    Fiók neve|Adjon meg egy egyedi nevet|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához.<br><br>Az azonosító csak kisbetűket, számokat és a kötőjel (-) karaktert használhat. 3. és 31 karakter hosszúságúnak kell lennie.
    API|Azure-tábla|A létrehozni kívánt fiók típusát az API határozza meg. Az Azure Cosmos DB öt API-kat biztosít: A dokumentum-adatbázis Core(SQL) a Gremlin gráf típusú adatbázisokat, a MongoDB, a dokumentum-adatbázis, az Azure Table és a Cassandra. Jelenleg minden API egy külön fiókot kell létrehoznia. <br><br>Válassza az **Azure-tábla** lehetőséget, mivel ebben a rövid útmutatóban olyan táblát fog létrehozni, amely a Table API-val működik együtt. <br><br>[További információk a Table API-ról](../articles/cosmos-db/table-introduction.md)|
    Hely|Válassza ki a felhasználóihoz legközelebb eső régiót|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.

4. Hagyhatja **Georedundancia** és **többrégiós ír** beállításokat az alapértelmezett értékekre, amely **letiltása** további RU díjat úgy kerülheti el. Folytassa a **hálózati** és **címkék**.

5. Válassza ki **felülvizsgálat + létrehozás** az érvényesítés befejezése után jelölje ki a **létrehozás** a fiók létrehozásához. 
 
   ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. A fiók létrehozása eltarthat néhány percig, és látni fogja **folyamatban van a központi telepítés** üzenet. Várjon, amíg befejeződik, majd válassza ki **erőforrás megnyitása**.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)
