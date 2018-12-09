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
ms.openlocfilehash: 1562e311ef9253c16b97e88decae615ff0d365c4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111740"
---
1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson az **Erőforrás létrehozása** > **Adatbázisok** > **Azure Cosmos DB** elemre.
   
   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Az a **létrehozása az Azure Cosmos DB-fiók** lap, adja meg az új Azure Cosmos DB-fiók alapszintű beállításait. 
 
    Beállítás|Érték|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetése|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|Új létrehozása<br><br>A használt egyedi nevet majd adja meg a azonosítója|Válassza az **Új létrehozása** lehetőséget. Majd adja meg a fiók új erőforráscsoport nevét. Az egyszerűség kedvéért használja ugyanazt a nevet, az azonosítóját. 
    Fiók neve|Adjon meg egy egyedi nevet|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához. A rendszer a *documents.azure.com* utótaggal egészíti ki a megadott azonosítót az URI létrehozásához, ezért válasszon egyedi azonosítót.<br><br>Az azonosító csak kisbetűket, számokat és a kötőjel (-) karaktert használhat. 3 – 50 karakter hosszúságúnak kell lennie.
    API|Core(SQL)|A létrehozni kívánt fiók típusát az API határozza meg. Az Azure Cosmos DB öt API-kat biztosít: a dokumentum-adatbázis, a Gremlin Graph Core(SQL) adatbázist, a MongoDB, a dokumentum-adatbázis, az Azure Table és a Cassandra. Jelenleg minden API egy külön fiókot kell létrehoznia. <br><br>Válassza ki **Core(SQL)** mivel ebben a cikkben létrehozott egy dokumentum-adatbázis és a lekérdezés SQL-szintaxis használatával. <br><br>[További információ az SQL API](../articles/cosmos-db/documentdb-introduction.md).|
    Hely|Válassza ki a felhasználóihoz legközelebb eső régiót|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.

    Válassza ki **felülvizsgálat + létrehozás**. Folytassa a **hálózati** és **címkék** szakaszban. 

    ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

4. A fiók létrehozása eltarthat néhány percig. Várja meg, hogy a portálon megjelenjen a **Gratulálunk! Azure Cosmos DB-fiókja létrejött** lap.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

