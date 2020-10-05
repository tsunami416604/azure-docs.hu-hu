---
title: fájlbefoglalás
description: fájlbefoglalás
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 08/19/2020
ms.custom: include file
ms.openlocfilehash: 4bad128e6f76b2d7dd87634a10b35ebbedd269ce
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88639051"
---
1. Az Azure Portal vagy a **Kezdőlap lapon**válassza az **erőforrás létrehozása**lehetőséget.

1. Az **új** lapon keresse meg és válassza ki **Azure Cosmos db**.

1. A **Azure Cosmos db** lapon válassza a **Létrehozás**lehetőséget.

1. A **Azure Cosmos db fiók létrehozása** lapon adja meg az új Azure Cosmos-fiók alapszintű beállításait. 

    |Beállítás|Érték|Leírás |
    |---|---|---|
    |Előfizetés|Előfizetés neve|Válassza ki az Azure Cosmos-fiókhoz használni kívánt Azure-előfizetést. |
    |Erőforráscsoport|Erőforráscsoport neve|Válasszon ki egy erőforráscsoportot, vagy válassza az **új létrehozása**lehetőséget, majd adjon meg egy egyedi nevet az új erőforráscsoport számára. |
    |Fiók neve|Egyedi név|Adjon meg egy nevet az Azure Cosmos-fiók azonosításához. Mivel a rendszer hozzáfűzi a *Documents.Azure.com* az URI létrehozásához megadott névvel, adjon meg egy egyedi nevet.<br><br>A név csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Hosszának 3-44 karakter közöttinek kell lennie.|
    |API|A létrehozandó fiók típusa|Válassza a **Core (SQL)** lehetőséget egy dokumentum-adatbázis és-lekérdezés létrehozásához az SQL-szintaxis használatával. <br><br>A létrehozni kívánt fiók típusát az API határozza meg. A Azure Cosmos DB öt API-t biztosít: a Core (SQL) és a MongoDB, a Gremlin, az Azure Table és a Cassandra típusú dokumentumokat. Jelenleg külön fiókot kell létrehoznia minden egyes API-hoz. |
    |Kapacitás mód|Kiosztott átviteli sebesség vagy kiszolgáló nélküli|Válassza a **kiépített átviteli sebesség** lehetőséget egy fiók [kiépített átviteli](../articles/cosmos-db/set-throughput.md) módban való létrehozásához. [Kiszolgáló nélküli módban lévő](../articles/cosmos-db/serverless.md) fiók létrehozásához válassza a **kiszolgáló** nélküli lehetőséget.<br><br>**Megjegyzés**: a kiszolgáló nélküli verzió jelenleg csak a Core (SQL) API-fiókok esetében érhető el.|
    |Az ingyenes szint árengedményének alkalmazása|Alkalmazás vagy Mellőzés|A Azure Cosmos DB ingyenes szinten az első 400 RU/s és 5 GB tárhely ingyenesen elérhető egy fiókban. További információ az [ingyenes szintjéről](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Hely|A felhasználókhoz legközelebb eső régió|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adataihoz.|
    |Fiók típusa|Éles üzem vagy nem éles üzem|Válassza a **gyártás** lehetőséget, ha a fiókot éles számítási feladatokhoz kívánja használni. Válassza a **nem üzemi** lehetőséget, ha a fiókot nem éles környezetben, például fejlesztési, tesztelési, QA vagy előkészítési célokra kívánja használni. Ez egy Azure-beli erőforrás-címkézési beállítás, amely a portál felületét, de nincs hatással az alapul szolgáló Azure Cosmos DB fiókra. Ezt az értéket bármikor megváltoztathatja.|

    > [!NOTE]
    > Az Azure-előfizetések esetében akár egy ingyenes szintű Azure Cosmos DB fiókot is beállíthat, és a fiók létrehozásakor be kell jelentkeznie. Ha nem látja az ingyenes csomagra vonatkozó kedvezményt, ez azt jelenti, hogy az előfizetés egy másik fiókja már engedélyezve van az ingyenes szinten.
   
    > [!NOTE]
    > A következő beállítások nem érhetők el, ha a **kiszolgáló** nélküli lehetőséget választja a **kapacitás módban**:
    > - Az ingyenes szint árengedményének alkalmazása
    > - Georedundancia
    > - Többrégiós írók
    
    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png" alt-text="Az Azure Cosmos DB Új fiók lapja":::

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Kihagyhatja a **hálózat** és a **címkék** szakaszt.

1. Tekintse át a Fiókbeállítások beállítást, majd kattintson a **Létrehozás**gombra. A fiók létrehozása néhány percet vesz igénybe. Várjon, amíg befejeződik a portál oldalának megjelenítése a **központi telepítés befejezéséhez**. 

    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png" alt-text="Az Azure Cosmos DB Új fiók lapja":::

1. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy megnyissa a Azure Cosmos db fiók lapot. 

    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png" alt-text="Az Azure Cosmos DB Új fiók lapja":::
