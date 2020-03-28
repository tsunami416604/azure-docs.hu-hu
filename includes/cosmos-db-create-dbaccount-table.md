---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 2001d0621a340cbdb04c0bb5eea1166ce8b88eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212758"
---
1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. A bal oldali menüben válassza **az Erőforrás létrehozása parancsot.**
   
   ![Erőforrás létrehozása az Azure Portalon](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png)
   
3. Az **Új** lapon válassza az Adatbázisok Azure **Cosmos** > **DB**lehetőséget.
   
   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)
   
3. Az **Azure Cosmos DB-fiók létrehozása** lapon adja meg az új Azure Cosmos DB-fiók beállításait. 
 
    Beállítás|Érték|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetése|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|**Új létrehozása**, majd fióknév|Válassza az **Új létrehozása** lehetőséget. Ezután adjon meg egy új erőforráscsoport nevet a fiókjához. Az egyszerűség kedvéért használja ugyanazt a nevet, mint az Azure Cosmos DB-fiók neve. 
    Fiók neve|Egyedi név|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához.<br><br>A fióknév csak kisbetűket, számokat és kötőjeleket (-) használhat, és 3 és 31 karakter közötti nek kell lennie.
    API|Tábla|A létrehozni kívánt fiók típusát az API határozza meg. Az Azure Cosmos DB öt API-t biztosít: Core (SQL) a dokumentum-adatbázisok, Gremlin a gráf adatbázisok, MongoDB dokumentum adatbázisok, Azure Table és Cassandra. Minden API-hoz külön fiókot kell létrehoznia. <br><br>Válassza az **Azure Table**lehetőséget, mert ebben a rövid útmutatóban olyan táblát hoz létre, amely együttműködik a Table API-val. <br><br>[További információ a Table API-ról.](../articles/cosmos-db/table-introduction.md)|
    Hely|A felhasználókhoz legközelebb eső régió|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.

    A további költségek elkerülése érdekében a **Területi redundancia** és a **Többrégiós írások** beállítást **a Letiltás** című területen hagyhatja el, és kihagyhatja a **Hálózat** és **címkék** szakaszt.

5. Válassza **a Véleményezés+Létrehozás lehetőséget.** Az ellenőrzés befejezése után válassza a **Létrehozás** gombot a fiók létrehozásához. 
 
   ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. A fiók létrehozása néhány percet vesz igénybe. Megjelenik egy üzenet, amely szerint **a telepítés folyamatban van.** Várja meg, amíg befejeződik a központi telepítés, majd válassza **az Ugrás az erőforrásra**lehetőséget.

    ![Az Azure Portal értesítési ablaktáblája](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)

