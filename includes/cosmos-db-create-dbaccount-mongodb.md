---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.custom: include file
ms.openlocfilehash: 0acc989f558c5de4e8f52ba2c77f2842f8d5d70c
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75751967"
---
1. Egy új ablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben válassza az **erőforrás létrehozása**, majd az **adatbázisok**lehetőséget, majd a **Azure Cosmos db**alatt válassza a **Létrehozás**lehetőséget.
   
   ![Képernyőkép a Azure Portalről, kiemelve a további szolgáltatásokat és Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Az **Azure Cosmos DB-fiók létrehozása** oldalon adja meg az új Azure Cosmos DB-fiók beállításait. 
 
    Beállítás|Value (Díj)|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetése|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|Új létrehozása<br><br>Ezután adja meg az AZONOSÍTÓban megadott egyedi nevet|Válassza az **Új létrehozása** lehetőséget. Ezután adjon meg egy új erőforráscsoport-nevet a fiókhoz. Az egyszerűség kedvéért használja az AZONOSÍTÓval megegyező nevet. 
    Fiók neve|Adjon meg egy egyedi nevet|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához. A rendszer a *documents.azure.com* utótaggal egészíti ki a megadott azonosítót az URI létrehozásához, ezért válasszon egyedi azonosítót.<br><br>Az azonosító csak kisbetűket, számokat és a kötőjel (-) karaktert használhatja. Hossza csak 3 és 31 karakter közötti lehet.
    API|MongoDB-hez készült Azure Cosmos DB API|A létrehozni kívánt fiók típusát az API határozza meg. A Azure Cosmos DB öt API-t biztosít a dokumentumok adatbázisaihoz, a Gremlin, a Azure Cosmos DB API-MongoDB, az Azure Tablehez és a Cassandrahoz. Jelenleg külön fiókot kell létrehoznia minden egyes API-hoz. <br><br>Válassza a **MongoDB** lehetőséget, mert ebben a rövid útmutatóban olyan táblát hoz létre, amely együttműködik a MongoDB.|
    Földrajzi egység|Válassza ki a felhasználóihoz legközelebb eső régiót|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.

    Válassza a **felülvizsgálat + létrehozás**lehetőséget. Kihagyhatja a **hálózat** és **címkék** szakaszt. 

    ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. A fiók létrehozása eltarthat néhány percig. Várja meg, amíg a portálon megjelenik a **Gratulálunk! A MongoDB-kompatibilitású Cosmos-fiókja kész** oldal.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
