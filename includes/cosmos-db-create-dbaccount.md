---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.custom: include file
ms.openlocfilehash: bfce10b0c9c55012c3ba2fb25121b28157f203b8
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672099"
---
1. Lépjen az [Azure Portalra](https://portal.azure.com/), és hozzon létre egy Azure Cosmos DB-fiókot. Keresse meg, majd válassza ki az **Azure Cosmos DB** lehetőséget.

   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Válassza a **Hozzáadás** lehetőséget.
1. Az **Azure Cosmos DB-fiók létrehozása** oldalon adja meg az új Azure Cosmos-fiók alapbeállításait. 

    |Beállítás|Érték|Leírás |
    |---|---|---|
    |Előfizetés|Előfizetés neve|Válassza ki az Azure Cosmos-fiókhoz használni kívánt Azure-előfizetést. |
    |Erőforráscsoport|Erőforráscsoport neve|Válasszon ki egy erőforráscsoportot, vagy válassza az **Új létrehozása** lehetőséget, majd adja meg az új erőforráscsoport egyedi nevét. |
    |Fiók neve|Egyedi név|Adjon meg egy nevet az Azure Cosmos-fiók azonosításához. A rendszer a *documents.azure.com* utótaggal egészíti ki a megadott nevet az URI létrehozásához, ezért válasszon egyedi nevet.<br><br>A név csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. A név 3–31 karakter hosszú lehet.|
    |API|A létrehozni kívánt fiók típusa|Válassza a **Core (SQL)** lehetőséget egy dokumentum-adatbázis létrehozásához és az SQL-szintaxissal való lekérdezéshez. <br><br>A létrehozni kívánt fiók típusát az API határozza meg. Az Azure Cosmos DB a következő öt API-t biztosítja: Core (SQL) és MongoDB a dokumentumok adataihoz, Gremlin a gráfadatokhoz, Azure Table és Cassandra. Jelenleg mindegyik API-hoz külön fiókot kell létrehozni. <br><br>[További információk az SQL API-ról](../articles/cosmos-db/documentdb-introduction.md).|
    |Ingyenes szintű kedvezmény alkalmazása|Alkalmazás vagy az alkalmazás mellőzése|Az ingyenes szintű Azure Cosmos DB használatával az első 400 kérelemegység és 5 GB tárhely ingyenesen érhető el a fiókban. További információk az [ingyenes szintről](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Hely|A felhasználókhoz legközelebb eső régió|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.|
    |Fiók típusa|Éles vagy nem éles környezet|Válassza az **Éles környezet** lehetőséget, ha a fiókot éles számítási feladatokhoz fogja használni. Válassza a **Nem éles környezet** lehetőséget, ha a fiókot nem éles környezetben fogja használni, például fejlesztéshez, teszteléshez, minőség-ellenőrzéshez vagy előkészítéshez. Ez az Azure-erőforráscímke egyik beállítása, amely finomhangolja a Portal-felületet, de nem érinti a mögöttes Azure Cosmos DB-fiókot. Ezt az értéket bármikor módosíthatja.|


> [!NOTE]
> Azure-előfizetésenként egy ingyenes szintű Azure Cosmos DB-fiókkal rendelkezhet, és a fiók létrehozásakor kell feliratkoznia. Ha nem látja az ingyenes szintű kedvezmény alkalmazására vonatkozó lehetőséget, az azt jelenti, hogy az előfizetés egy másik fiókján már engedélyezve van az ingyenes szint.
   
   ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **Hálózat** és a **Címkék** szakaszt átugorhatja.

1. Ellenőrizze a fiók beállításait, majd válassza a **Létrehozás** lehetőséget. A fiók létrehozása néhány percet vesz igénybe. Várja meg, hogy a portálon megjelenjen **Az üzembe helyezés befejeződött** üzenet. 

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Válassza az **Ugrás az erőforrásra** lehetőséget, és lépjen az Azure Cosmos DB-fiók oldalára. 

    ![Az Azure Cosmos DB-fiók oldala](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
