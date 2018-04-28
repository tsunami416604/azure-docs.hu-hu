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
ms.openlocfilehash: 0a9f2aaa4bc6422d4e8a86373b5e578c5bd65b4c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben kattintson az **Erőforrás létrehozása**, az **Adatbázisok**, majd az **Azure Cosmos DB** területen a **Létrehozás** elemre. 
   
   ![Képernyőfelvétel az Azure portálról, a További szolgáltatások és az Azure Cosmos DB menüpont kiemelve](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. Az **Új fiók** oldalon adja meg az új Azure Cosmos DB-fiók beállításait. 
 
    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    ID (Azonosító)|*Adjon meg egy egyedi nevet*|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához. A rendszer a *documents.azure.com* utótaggal egészíti ki a megadott azonosítót az URI létrehozásához, ezért válasszon egyedi, de felismerhető azonosítót.<br><br>Az azonosító csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja, valamint 3–50 karakter hosszúságúnak kell lennie.
    API|Azure-tábla|A létrehozni kívánt fiók típusát az API határozza meg. Az adott alkalmazás igényei szerint az Azure Cosmos DB a következő öt API-t biztosítja: SQL (dokumentum-adatbázis), Gremlin (gráfadatbázis), MongoDB (dokumentum-adatbázis), Azure-tábla, Cassandra. Jelenleg mindegyikhez külön fiókra van szükség.<br><br>Válassza az **Azure-tábla** lehetőséget, mivel ebben a rövid útmutatóban olyan táblát fog létrehozni, amely a Table API-val működik együtt.<br><br>[További információk a Table API-ról](../articles/cosmos-db/table-introduction.md) |
    Előfizetés|*Adja meg a fenti azonosító esetében használt egyedi nevet*|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|Új létrehozása<br><br>*Ezután adja meg a fenti azonosító esetében használt egyedi nevet*|Válassza az **Új létrehozása** lehetőséget, majd adjon meg egy új erőforráscsoport-nevet a fióknak. Az egyszerűség kedvéért használhat az azonosítójával megegyező nevet.
    Hely|*Válassza ki a felhasználóihoz legközelebb eső régiót*|Válassza ki azt a földrajzi helyet, ahol az Azure Cosmos DB-fiókot üzemeltetni fogja. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.
    Georedundancia engedélyezése| Hagyja üresen | Ezzel egy második (párosított) régióban létrehozta az adatbázis replikált verzióját. Hagyja üresen.  
    Rögzítés az irányítópulton | Válassza ezt: | Jelölje be ezt a jelölőnégyzetet, így az új adatbázisfiók a könnyű hozzáférés érdekében megtalálható lesz a portál irányítópultján.

    Ezt követően kattintson a **Create** (Létrehozás) gombra.

    ![Az Azure Cosmos DB új fióklapja](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

4. A fiók létrehozása eltarthat néhány percig. Várjon, amíg a portál megjeleníti a **Gratulálunk! Azure Cosmos DB-fiókja létrejött** lapot.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)
