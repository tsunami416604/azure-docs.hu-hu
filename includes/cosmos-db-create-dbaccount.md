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
ms.openlocfilehash: 8f8b8384b0c570e2b4925b0e84480b19a632187e
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75752284"
---
1. Azure Cosmos DB-fiók létrehozásához nyissa meg a [Azure Portal](https://portal.azure.com/) . Keresse meg és válassza ki a **Azure Cosmos db**.

   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Válassza a **Hozzáadás** lehetőséget.
1. A **Azure Cosmos db fiók létrehozása** lapon adja meg az új Azure Cosmos-fiók alapszintű beállításait. 

    |Beállítás|Value (Díj)|Leírás |
    |---|---|---|
    |Előfizetés|Előfizetés neve|Válassza ki az Azure Cosmos-fiókhoz használni kívánt Azure-előfizetést. |
    |Erőforráscsoport|Erőforráscsoport neve|Válasszon ki egy erőforráscsoportot, vagy válassza az **új létrehozása**lehetőséget, majd adjon meg egy egyedi nevet az új erőforráscsoport számára. |
    |Fiók neve|Egyedi név|Adjon meg egy nevet az Azure Cosmos-fiók azonosításához. Mivel a rendszer hozzáfűzi a *Documents.Azure.com* az URI létrehozásához megadott névvel, adjon meg egy egyedi nevet.<br><br>A név csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Hosszának 3-31 karakter közöttinek kell lennie.|
    |API|A létrehozandó fiók típusa|Válassza a **Core (SQL)** lehetőséget egy dokumentum-adatbázis és-lekérdezés létrehozásához az SQL-szintaxis használatával. <br><br>A létrehozni kívánt fiók típusát az API határozza meg. A Azure Cosmos DB öt API-t biztosít: a Core (SQL) és a MongoDB, a Gremlin, az Azure Table és a Cassandra típusú dokumentumokat. Jelenleg külön fiókot kell létrehoznia minden egyes API-hoz. <br><br>[További információ az SQL API-ról](../articles/cosmos-db/documentdb-introduction.md).|
    |Földrajzi egység|A felhasználókhoz legközelebb eső régió|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adataihoz.|

   ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-account.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Kihagyhatja a **hálózat** és a **címkék** szakaszt.

1. Tekintse át a Fiókbeállítások beállítást, majd kattintson a **Létrehozás**gombra. A fiók létrehozása néhány percet vesz igénybe. Várjon, amíg befejeződik a portál oldalának megjelenítése a **központi telepítés befejezéséhez**. 

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy megnyissa a Azure Cosmos db fiók lapot. 

    ![A Azure Cosmos DB fiók lapja](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
