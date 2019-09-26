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
ms.openlocfilehash: d21fd6c4848a4806039d5cbed5c7ed8ab4112405
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71307605"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Kattintson az **Erőforrás létrehozása** > **Adatbázisok** > **Azure Cosmos DB** elemre.

   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. A **Azure Cosmos db fiók létrehozása** lapon adja meg az új Azure Cosmos-fiók alapszintű beállításait. 

    |Beállítás|Érték|Leírás |
    |---|---|---|
    |Subscription|Előfizetés neve|Válassza ki az Azure Cosmos-fiókhoz használni kívánt Azure-előfizetést. |
    |Erőforráscsoport|Erőforráscsoport neve|Válasszon ki egy erőforráscsoportot, vagy válassza az **új létrehozása**lehetőséget, majd adjon meg egy egyedi nevet az új erőforráscsoport számára. |
    |Fiók neve|Egyedi név|Adjon meg egy nevet az Azure Cosmos-fiók azonosításához. A rendszer a *documents.azure.com* utótaggal egészíti ki a megadott azonosítót az URI létrehozásához, ezért válasszon egyedi azonosítót.<br><br>Az azonosító csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Hosszának 3-31 karakter közöttinek kell lennie.|
    |API|A létrehozandó fiók típusa|Válassza a **Core (SQL)** lehetőséget egy dokumentum-adatbázis és-lekérdezés létrehozásához az SQL-szintaxis használatával. <br><br>A létrehozni kívánt fiók típusát az API határozza meg. A Azure Cosmos DB öt API-t biztosít: Core (SQL) és MongoDB a Gremlin, az Azure Table és a Cassandra. Jelenleg minden API egy külön fiókot kell létrehoznia. <br><br>[További információ az SQL API](../articles/cosmos-db/documentdb-introduction.md).|
    |Location|A felhasználókhoz legközelebb eső régió|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adataihoz.|

   ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-account.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Kihagyhatja a **hálózat** és a **címkék** szakaszt.

1. Tekintse át a Fiókbeállítások beállítást, majd kattintson a **Létrehozás**gombra. A fiók létrehozása néhány percet vesz igénybe. Várjon, amíg befejeződik a portál oldalának megjelenítése a **központi telepítés befejezéséhez**. 

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy megnyissa a Azure Cosmos db fiók lapot. 

    ![A Azure Cosmos DB fiók lapja](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
