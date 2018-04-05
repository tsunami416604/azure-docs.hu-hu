---
title: Azure Cosmos DB MongoDB API-fiók létrehozása
description: Leírja, hogyan hozhat létre Azure Cosmos DB MongoDB API-fiókot az Azure Portalon
services: cosmos-db
author: mimig1
ms.service: cosmos-db
ms.topic: include
ms.date: 03/20/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 02ea0e011642313b885bc48ec48104fa2789da81
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
1. Egy új ablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben kattintson az **Erőforrás létrehozása**, az **Adatbázisok**, majd az **Azure Cosmos DB** területen a **Létrehozás** elemre.
   
   ![Képernyőfelvétel az Azure portálról, a További szolgáltatások és az Azure Cosmos DB menüpont kiemelve](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Az **Új fiók** panelen adja meg a **MongoDB** API-t, és töltse ki az Azure Cosmos DB-fiók kívánt konfigurációját.
 
    ![Képernyőfelvétel az Új Azure Cosmos DB panelről](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)

    * Az **Azonosítónak** az Azure Cosmos DB-fiók azonosításához használni kívánt egyedi névnek kell lennie. Az azonosító csak kisbetűket, számokat és a '-' karaktert tartalmazhatja, és 3–50 karakter hosszúságú lehet.
    * Az **Előfizetés** az Ön Azure-előfizetése. A rendszer ezt automatikusan kitölti.
    * Az **Erőforráscsoport** az Azure Cosmos DB-fiók erőforráscsoportjának neve.
    * A **Hely** az a földrajzi hely, ahol az Azure Cosmos DB-példány található. Válassza ki a felhasználókhoz legközelebb eső helyet.

4. A fiók létrehozásához kattintson a **Létrehozás** gombra.
5. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.

    ![Központi telepítés elindítva értesítés](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  Amikor befejeződött a telepítés, nyissa meg az új fiókot az Összes erőforrás csempén. 

    ![Azure Cosmos DB-fiók az Összes erőforrás csempén](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)
