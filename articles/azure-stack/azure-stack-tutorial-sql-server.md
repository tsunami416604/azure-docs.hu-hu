---
title: "SQL-adatbázisok Azure verem felhasználók számára történő elérhetővé |} Microsoft Docs"
description: "Az oktatóanyag segítséget nyújt az SQL Server erőforrás-szolgáltató telepítéséhez, és hozzon létre kínál, amelyekkel Azure verem felhasználók SQL adatbázisok létrehozására."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 7/03/2017
ms.author: brenduns
ms.reviewer: 
ms.custom: mvc
ms.openlocfilehash: e9fd74fa44bb9482ee2285f4305085ee6ff2fb73
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="make-sql-databases-available-to-your-azure-stack-users"></a>SQL-adatbázisok Azure verem felhasználók számára történő elérhetővé

Rendszergazdaként Azure verem felhő ajánlatokat, amelyek segítségével a felhasználók hozhat létre (bérlőkkel), amely a felhő-natív alkalmazások, webhelyek és a munkaterhelések használhatják SQL-adatbázisok létrehozására. Egyéni, igény szerinti, felhőalapú adatbázist biztosít a felhasználók számára, mentheti azokat időt és erőforrásokat. Ennek beállításához a fogja végrehajtani:

> [!div class="checklist"]
> * Az erőforrás-szolgáltató SQL Server telepítése
> * Ajánlat létrehozása
> * Az ajánlat tesztelése

## <a name="deploy-the-sql-server-resource-provider"></a>Az erőforrás-szolgáltató SQL Server telepítése

A telepítési folyamat részletes leírása a [Azure verem cikken használja az SQL adatbázisok](azure-stack-sql-resource-provider-deploy.md), és a következő fő lépésből áll:

1. [Az SQL erőforrás-szolgáltató telepítéséhez]( azure-stack-sql-resource-provider-deploy.md#deploy-the-resource-provider).
2. [A telepítés ellenőrzése]( azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Adja meg a kapacitás üzemeltetési SQL-kiszolgálójához.

## <a name="create-an-offer"></a>Ajánlat létrehozása

1.  [Állítsa be a kvóta](azure-stack-setting-quotas.md) és adjon neki nevet *SQLServerQuota*. Válassza ki **Microsoft.SQLAdapter** a a **Namespace** mező.
2.  [Hozzon létre egy csomagot](azure-stack-create-plan.md). Nevezze el *TestSQLServerPlan*, jelölje be a **Microsoft.SQLAdapter** szolgáltatás, és **SQLServerQuota** kvótát.

    > [!NOTE]
    > Ahhoz, hogy a felhasználók más-alkalmazásai létrehozására, a terv más szolgáltatások akkor lehet szükség. Például az Azure Functions az szükséges, hogy a tervben a **Microsoft.Storage** szolgáltatás, amíg a Wordpress igényel **Microsoft.MySQLAdapter**.
    > 
    >

3.  [Hozzon létre egy ajánlatot](azure-stack-create-offer.md), adjon neki nevet **TestSQLServerOffer** válassza ki a **TestSQLServerPlan** terv.

## <a name="test-the-offer"></a>Az ajánlat tesztelése

Most, hogy az SQL Server erőforrás-szolgáltató telepítése után, és létrehozott egy ajánlatot, bármikor beléphet egy olyan felhasználó nevében, az ajánlat előfizetni, és hozzon létre egy adatbázist.

### <a name="subscribe-to-the-offer"></a>Az ajánlat előfizetés
1. Jelentkezzen be a verem Azure portal (https://portal.local.azurestack.external) a bérlőt.
2. Kattintson a **egy előfizetés** és írja be **TestSQLServerSubscription** alatt **megjelenített név**.
3. Kattintson a **válasszon egy ajánlatot** > **TestSQLServerOffer** > **létrehozása**.
4. Kattintson a **további szolgáltatások** > **előfizetések** > **TestSQLServerSubscription** > **erőforrás szolgáltatók**.
5. Kattintson a **regisztrálása** mellett a **Microsoft.SQLAdapter** szolgáltató.

### <a name="create-a-sql-database"></a>SQL-adatbázis létrehozása

1. Kattintson a  **+**   >  **adatok + tárolás** > **SQL-adatbázis**.
2. Meghagyhatja az alapértelmezett beállításokat, a mezők, vagy a példákat is használhatja:
    - **Adatbázis neve**: SQLdb
    - **Maximális méretét megabájtban**: 100
    - **Előfizetés**: TestSQLOffer
    - **Erőforráscsoport**: SQL-rg-n
3. Kattintson a **bejelentkezési beállítások**, adja meg az adatbázis hitelesítő adatait, és kattintson a **OK**.
4. Kattintson a **SKU** > Válassza ki a létrehozott üzemeltető SQL Server SQL SKU > **OK**.
5. Kattintson a **Create** (Létrehozás) gombra.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az erőforrás-szolgáltató SQL Server telepítése
> * Ajánlat létrehozása
> * Az ajánlat tesztelése

Előzetes tovább az oktatóanyaghoz, megtudhatja, hogyan:

> [!div class="nextstepaction"]
> [Webes, mobil és API-alkalmazások számára elérhetővé a felhasználóknak]( azure-stack-tutorial-app-service.md)

