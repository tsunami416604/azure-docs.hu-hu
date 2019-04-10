---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.author: rimman
ms.custom: include file
ms.openlocfilehash: 5d57d7e18befba175a5a8a825494ce512644b5a2
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59291738"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Kattintson az **Erőforrás létrehozása** > **Adatbázisok** > **Azure Cosmos DB** elemre.
   
   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Az a **létrehozása az Azure Cosmos DB-fiók** lap, adja meg az alapszintű beállításokat az új Azure Cosmos-fiókhoz. 
 
    |Beállítás|Érték|Leírás |
    |---|---|---|
    |Előfizetés|Előfizetés neve|Válassza ki, hogy az Azure Cosmos-fiókhoz használni kívánt Azure-előfizetést. |
    |Erőforráscsoport|Erőforráscsoport neve|Válasszon ki egy erőforráscsoportot, vagy válasszon **új létrehozása**, majd adja meg egy egyedi nevet az új erőforráscsoportnak. |
    | Fiók neve|Adjon meg egy egyedi nevet|Adjon meg egy nevet az Azure Cosmos-fiók azonosításához. A rendszer a *documents.azure.com* utótaggal egészíti ki a megadott azonosítót az URI létrehozásához, ezért válasszon egyedi azonosítót.<br><br>Az azonosító csak kisbetűket, számokat és kötőjelet (-) tartalmazhat. 3-31 karakter hosszúságúnak kell lennie.|
    | API|Mag (SQL)|A létrehozni kívánt fiók típusát az API határozza meg. Az Azure Cosmos DB öt API-kat biztosít: Core (SQL) és a dokumentum adatok esetében a Gremlin graph adatváltozásainak, az Azure Table és a Cassandra a mongodb-hez. Jelenleg minden API egy külön fiókot kell létrehoznia. <br><br>Válassza ki **Core (SQL)** egy dokumentum-adatbázis és a lekérdezés létrehozása SQL-szintaxis használatával. <br><br>[További információ az SQL API](../articles/cosmos-db/documentdb-introduction.md).|
    | Hely|Válassza ki a felhasználóihoz legközelebb eső régiót|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Tegye lehetővé számukra a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz a felhasználók legközelebb eső helyet használja.|
   
   ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Folytassa a **hálózati** és **címkék** szakaszokat. 

1. Tekintse át a fiók beállításait, és válassza ki **létrehozás**. A fiók létrehozása pár percet vesz igénybe. Várjon, amíg a portál oldala a megjelenítési **az üzembe helyezés befejeződése**. 

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Válassza ki **erőforrás megnyitása** , nyissa meg az Azure Cosmos DB-fiók lapját. 

    ![Az Azure Cosmos DB-fiók lap](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
