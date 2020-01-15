---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: b1dcf395c55e91a98b237f6e3866c97d74b7a97c
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942678"
---
1. Egy új ablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben válassza az **erőforrás létrehozása**, majd az **adatbázisok**lehetőséget, majd a **Azure Cosmos db**alatt válassza a **Létrehozás**lehetőséget.
   
   ![Képernyőkép a Azure Portalről, kiemelve a további szolgáltatásokat és Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Az **Azure Cosmos DB-fiók létrehozása** oldalon adja meg az új Azure Cosmos DB-fiók beállításait. 
 
    Beállítás|Value (Díj)|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetése|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|Új létrehozása<br><br>Ezután adja meg az AZONOSÍTÓban megadott egyedi nevet|Válassza az **Új létrehozása** lehetőséget. Ezután adjon meg egy új erőforráscsoport-nevet a fiókhoz. Az egyszerűség kedvéért használja az AZONOSÍTÓval megegyező nevet. 
    Fiók neve|Adjon meg egy egyedi nevet|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához. Mivel a rendszer hozzáfűzi a *Mongo.Cosmos.Azure.com* az URI létrehozásához megadott azonosítóhoz, egyedi azonosítót használjon.<br><br>Az azonosító csak kisbetűket, számokat és a kötőjel (-) karaktert használhatja. Hossza csak 3 és 31 karakter közötti lehet.
    API|MongoDB-hez készült Azure Cosmos DB API|A létrehozni kívánt fiók típusát az API határozza meg. A Azure Cosmos DB öt API-t biztosít a dokumentumok adatbázisaihoz, a Gremlin, a Azure Cosmos DB API-MongoDB, az Azure Tablehez és a Cassandrahoz. Jelenleg külön fiókot kell létrehoznia minden egyes API-hoz. <br><br>Válassza a **MongoDB** lehetőséget, mert ebben a rövid útmutatóban olyan gyűjteményt hoz létre, amely együttműködik a MongoDB.|
    Földrajzi egység|Válassza ki a felhasználóihoz legközelebb eső régiót|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.
    Verzió|3,6|Válassza ki a MongoDB 3,6-es vagy visszamenőleges kompatibilitást, és válassza a 3,2 elemet.

    Válassza a **felülvizsgálat + létrehozás**lehetőséget. Kihagyhatja a **hálózat** és **címkék** szakaszt. 

    ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. A fiók létrehozása eltarthat néhány percig. Várja meg, amíg a portálon megjelenik a **Gratulálunk! A MongoDB-kompatibilitású Cosmos-fiókja kész** oldal.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
