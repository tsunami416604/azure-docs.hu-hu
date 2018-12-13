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
ms.openlocfilehash: 8b5b7126a264d6a15010fc18288f4a4ba1263344
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53286000"
---
1. Egy új ablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben kattintson az **Erőforrás létrehozása**, az **Adatbázisok**, majd az **Azure Cosmos DB** területen a **Létrehozás** elemre.
   
   ![Képernyőfelvétel az Azure portálról, a További szolgáltatások és az Azure Cosmos DB menüpont kiemelve](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Az a **létrehozása az Azure Cosmos DB-fiók** lap, adja meg az új Azure Cosmos DB-fiók beállításait. 
 
    Beállítás|Érték|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetése|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|Új létrehozása<br><br>A használt egyedi nevet majd adja meg a azonosítója|Válassza az **Új létrehozása** lehetőséget. Majd adja meg a fiók új erőforráscsoport nevét. Az egyszerűség kedvéért használja ugyanazt a nevet, az azonosítóját. 
    Fiók neve|Adjon meg egy egyedi nevet|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához. A rendszer a *documents.azure.com* utótaggal egészíti ki a megadott azonosítót az URI létrehozásához, ezért válasszon egyedi azonosítót.<br><br>Az azonosító csak kisbetűket, számokat és a kötőjel (-) karaktert használhat. 3. és 31 karakter hosszúságúnak kell lennie.
    API|MongoDB|A létrehozni kívánt fiók típusát az API határozza meg. Az Azure Cosmos DB öt API-kat biztosít: A dokumentum-adatbázis Core(SQL) a Gremlin gráf típusú adatbázisokat, a MongoDB, a dokumentum-adatbázis, az Azure Table és a Cassandra. Jelenleg minden API egy külön fiókot kell létrehoznia. <br><br>Válassza ki **MongoDB** mivel ebben a rövid útmutatóban egy táblát, amely együttműködik a MongoDB API-t hoz létre.|
    Hely|Válassza ki a felhasználóihoz legközelebb eső régiót|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.

    Válassza ki **felülvizsgálat + létrehozás**. Folytassa a **hálózati** és **címkék** szakaszban. 

    ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. A fiók létrehozása eltarthat néhány percig. Várja meg, hogy a portálon megjelenjen a **Gratulálunk! MongoDB API-val rendelkező Azure Cosmos DB-fiókja elkészült** lap.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
