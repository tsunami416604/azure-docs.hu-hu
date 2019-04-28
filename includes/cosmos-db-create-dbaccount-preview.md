---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/24/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: 3915df46495e754843009a761e9a07aa2daddae2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686739"
---
1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson az **Erőforrás létrehozása** > **Adatbázisok** > **Azure Cosmos DB** lehetőségre.
   
   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Az a **létrehozása az Azure Cosmos DB-fiók** lap, adja meg az új Azure Cosmos DB-fiók beállításait. 
 
    Beállítás|Value (Díj)|Leírás
    ---|---|---
    Előfizetés|*Az Ön előfizetése*|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|Új létrehozása<br><br>*Adjon meg egy egyedi nevet*|Válassza ki az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét. Az egyszerűség kedvéért ugyanazzal a névvel is használhatja, mint a fiók neve. 
    Fiók neve|*Adjon meg egy egyedi nevet*|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához. A rendszer a *documents.azure.com* utótaggal egészíti ki a megadott azonosítót az URI létrehozásához, ezért válasszon egyedi azonosítót.<br><br>Az azonosító csak kisbetűket, számokat és kötőjelet (-) tartalmazhat, és 3. és 31 karakter hosszúságúnak kell lennie...
    API|Mag (SQL)|A létrehozni kívánt fiók típusát az API határozza meg. Az Azure Cosmos DB öt API-kat biztosít: SQL (document database), Gremlin (graph database), MongoDB (document database), Table API, and Cassandra API. Jelenleg mindegyik API-hoz külön fiókra van szükség. <br><br>Válassza ki **Core (SQL)** mivel ebben a cikkben létrehoz egy dokumentum-adatbázist és lekérdezés az SQL-szintaxis használatával. <br><br>[További információk az SQL API-ról](../articles/cosmos-db/documentdb-introduction.md)|
    Location egység|*Válassza ki a felhasználóihoz legközelebb eső régiót*|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.
    Georedundancia engedélyezése| Hagyja üresen | Ezzel egy második (párosított) régióban létrehozta az adatbázis replikált verzióját. Hagyja üresen.  
    Többrégiós írási műveletek| Hagyja üresen | Ez lehetővé teszi az adatbázis régió mindkét olvasási és írási régióba. Hagyja üresen.  

    Ezután kattintson a **Felülvizsgálat + létrehozás** elemre. Folytassa a **hálózati** és **címkék** szakaszban. 

    ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-preview.png)

    Tekintse át az összefoglaló adatokat, és kattintson a **létrehozás**. 

    ![A fiók ellenőrzése összefoglaló lap](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-summary-preview.png)

4. A fiók létrehozása eltarthat néhány percig. Várja meg a portálon megjelenjen a **az üzembe helyezés befejeződése** üzenet, és kattintson a **erőforrás megnyitása**.     

    ![A fiók sikeresen létrejött lap](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-complete-preview.png)

5. A portál megjeleníti a **Gratulálunk! Azure Cosmos DB-fiókja létrejött** lap.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

