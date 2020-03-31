---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/22/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 8f7a69b81430d964d1aade26ed179354171e4164
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77134629"
---
1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. A bal oldali menüben válassza **az Erőforrás létrehozása parancsot.**
   
   ![Erőforrás létrehozása az Azure Portalon](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-0.png)
   
3. Az **Új** lapon válassza az Adatbázisok Azure **Cosmos** > **DB**lehetőséget.
   
   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-1.png)
   
3. Az **Azure Cosmos DB-fiók létrehozása** lapon adja meg az új Azure Cosmos DB-fiók beállításait. 
 
    Beállítás|Érték|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetése|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|Új létrehozása<br><br>Ezután adja meg ugyanazt a nevet, mint a fiókneve|Válassza az **Új létrehozása** lehetőséget. Ezután adjon meg egy új erőforráscsoport nevet a fiókjához. Az egyszerűség kedvéért használja ugyanazt a nevet, mint az Azure Cosmos-fiók neve. 
    Fiók neve|Adjon meg egy egyedi nevet|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához. A fiók URI-cassandra.cosmos.azure.com *hozzá* fűzünk az egyedi fióknevéhez.<br><br>A fióknév csak kisbetűket, számokat és kötőjeleket (-) használhat, és 3 és 31 karakter közötti nek kell lennie.
    API|Cassandra|A létrehozni kívánt fiók típusát az API határozza meg. Az Azure Cosmos DB öt API-t biztosít: Core (SQL) a dokumentum-adatbázisok, Gremlin a gráf adatbázisok, MongoDB dokumentum adatbázisok, Azure Table és Cassandra. Minden API-hoz külön fiókot kell létrehoznia. <br><br>Válassza a **Cassandra**lehetőséget, mert ebben a rövid útmutatóban olyan táblát hoz létre, amely együttműködik a Cassandra API-val. <br><br>[További információ a Cassandra API-ról](../articles/cosmos-db/cassandra-introduction.md).|
    Hely|Válassza ki a felhasználóihoz legközelebb eső régiót|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.

    Válassza **a Véleményezés+Létrehozás lehetőséget.** Kihagyhatja a **Hálózat** és **címkék szakaszt.** 

    ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount-cassandra/azure-cosmos-db-create-new-account.png)

4. A fiók létrehozása eltarthat néhány percig. Várja meg, amíg a portál megjeleníti az oldalt mondván **Gratulálunk! Az Azure Cosmos DB-fiókja létrejött.**

