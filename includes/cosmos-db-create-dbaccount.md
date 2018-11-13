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
ms.openlocfilehash: d6eeb62ce990c24138f14e7f3b8a1ce4048e0174
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50746424"
---
1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson az **Erőforrás létrehozása** > **Adatbázisok** > **Azure Cosmos DB** lehetőségre.
   
   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Az **Új fiók** oldalon adja meg az új Azure Cosmos DB-fiók beállításait. 
 
    Beállítás|Érték|Leírás
    ---|---|---
    ID (Azonosító)|*Adjon meg egy egyedi nevet*|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához. A rendszer a *documents.azure.com* utótaggal egészíti ki a megadott azonosítót az URI létrehozásához, ezért válasszon egyedi azonosítót.<br><br>Az azonosító csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja, valamint 3–50 karakter hosszúságúnak kell lennie.
    API|SQL|A létrehozni kívánt fiók típusát az API határozza meg. Az Azure Cosmos DB a következő öt API-t biztosítja: SQL (dokumentum-adatbázis), Gremlin (gráfadatbázis), MongoDB (dokumentum-adatbázis), Table API és Cassandra API. Jelenleg mindegyik API-hoz külön fiókra van szükség. <br><br>Válassza az **SQL** lehetőséget, mivel ebben a cikkben egy dokumentum-adatbázist fog létrehozni, és SQL-szintaxissal fogja lekérdezni. <br><br>[További információk az SQL API-ról](../articles/cosmos-db/documentdb-introduction.md)|
    Előfizetés|*Az Ön előfizetése*|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|Új létrehozása<br><br>*Majd adja meg a fenti azonosító esetében használt egyedi nevet*|Válassza ki az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét. Az egyszerűség kedvéért használhat az azonosítójával megegyező nevet. 
    Hely|*Válassza ki a felhasználóihoz legközelebb eső régiót*|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.
    Georedundancia engedélyezése| Hagyja üresen | Ezzel egy második (párosított) régióban létrehozta az adatbázis replikált verzióját. Hagyja üresen.  

    Ezt követően kattintson a **Create** (Létrehozás) gombra.

    ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

4. A fiók létrehozása eltarthat néhány percig. Várja meg, hogy a portálon megjelenjen a **Gratulálunk! Azure Cosmos DB-fiókja létrejött** lap.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

