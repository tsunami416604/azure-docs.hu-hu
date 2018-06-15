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
ms.openlocfilehash: 690c5f07a2b0f7a88e16f0b0bbbaa9ca78e37317
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31429243"
---
1. Egy új ablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben kattintson az **Erőforrás létrehozása**, az **Adatbázisok**, majd az **Azure Cosmos DB** területen a **Létrehozás** elemre.
   
   ![Képernyőfelvétel az Azure portálról, a További szolgáltatások és az Azure Cosmos DB menüpont kiemelve](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Az **Új fiók** panelen adja meg a **MongoDB** API-t, és töltse ki az Azure Cosmos DB-fiók kívánt konfigurációját.
 
    * Az **Azonosítónak** az Azure Cosmos DB-fiók azonosításához használni kívánt egyedi névnek kell lennie. Az azonosító csak kisbetűket, számokat és a '-' karaktert tartalmazhatja, és 3–50 karakter hosszúságú lehet.
    * Az **Előfizetés** az Ön Azure-előfizetése. A rendszer ezt automatikusan kitölti.
    * Az **Erőforráscsoport** az Azure Cosmos DB-fiók erőforráscsoportjának neve. Válassza ki az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét. Az egyszerűség kedvéért használhat az azonosítójával megegyező nevet.
    * A **Hely** az a földrajzi hely, ahol az Azure Cosmos DB-példány található. Válassza ki a felhasználókhoz legközelebb eső helyet.

    Ezt követően kattintson a **Create** (Létrehozás) gombra.

    ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. A fiók létrehozása eltarthat néhány percig. Várja meg, hogy a portálon megjelenjen a **Gratulálunk! MongoDB API-val rendelkező Azure Cosmos DB-fiókja elkészült** lap.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
