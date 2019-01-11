---
title: Hozzon létre egy Azure SQL database, a portál használatával |} A Microsoft Docs
description: Az Azure Portalon egy Azure SQL Database logikai kiszolgáló és adatbázis létrehozása és lekérdezése.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 1/9/2019
ms.openlocfilehash: b11eb08a960e81ab938a9b15a1153c44706231c5
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198287"
---
# <a name="quickstart-create-an-azure-sql-database-in-the-azure-portal"></a>Gyors útmutató: Azure SQL Database létrehozása az Azure Portalon

Az Azure SQL Database egy *adatbázis--szolgáltatásként* , amely lehetővé teszi a felhőbeli magas rendelkezésre állású SQL Server-adatbázisokat futtathat és méretezhet. Ez a rövid útmutató bemutatja, hogyan hoz létre, és a egy Azure SQL database, az Azure portal használatával majd lekérdezi a kezdéshez. 

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

Ebben a rövid útmutatóban minden lépéseiért jelentkezzen be a [az Azure portal](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>SQL-adatbázis létrehozása

Az Azure SQL database egy meghatározott készletével rendelkezik [számítási és tárolási erőforrások](sql-database-service-tiers-dtu.md). Az adatbázis létrehozása egy [Azure SQL Database logikai kiszolgáló](sql-database-features.md) belül egy [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md).

Az AdventureWorksLT mintaadatokat tartalmazó SQL-adatbázis létrehozása:

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
   
1. Válassza ki **adatbázisok** majd **SQL Database**.
   
1. Az a **SQL-adatbázis létrehozása** űrlapon adja meg a következő értékeket: 
   
   - **Adatbázis neve**: Adja meg *mySampleDatabase*.
   - **Előfizetés**: Legördülő lista, és válassza ki a megfelelő előfizetés, ha nem jelenik meg.
   - **Erőforráscsoport**: Válassza ki **új létrehozása**, típus *myResourceGroup*, és válassza ki **OK**. 
   - **Forrás kiválasztása**: Legördülő listára, és válassza ki **minta (AdventureWorksLT)**.
    
    >[!IMPORTANT]
    >Ügyeljen arra, hogy válassza ki a **minta (AdventureWorksLT)** adatait ezzel és más, amely a tárolt adatok Azure SQL Database gyors útmutatók követheti.
  
   ![Az Azure SQL Database adatbázis létrehozása](./media/sql-database-get-started-portal/create-database-1.png)
   
1. A **kiszolgáló**válassza **új létrehozása**. 
   
1. Az a **új kiszolgáló** űrlapon adja meg a következő értékeket: 
   
   - **Kiszolgálónév**: Adja meg *mysqlserver*.
   - **Kiszolgáló-rendszergazdai bejelentkezés**: Típus *azureuser*. 
   - **Jelszó**: Adja meg *Azure1234567*. 
   - **Jelszó megerősítése**: Írja be újra a jelszót.
   - **Hely**: Legördülő lista, és válassza ki az egyik érvényes helyen sem.  
   
   >[!IMPORTANT]
   >Jegyezze fel a kiszolgáló rendszergazdai bejelentkezési nevét és jelszavát, így bejelentkezhet a kiszolgáló és az adatbázisok ennél és a többi rövid útmutató. Ha elfelejti a bejelentkezéshez vagy a jelszavát, kérje le a bejelentkezési nevét, vagy alaphelyzetbe állíthatja a jelszót a a **az SQL server** lapot. Megnyitásához a **az SQL server** lapra, jelölje be a kiszolgáló nevét, az adatbázis **áttekintése** lap az adatbázis létrehozása után.
   
    ![Kiszolgáló létrehozása](./media/sql-database-get-started-portal/create-database-server.png)

1. Válassza a **Kiválasztás** lehetőséget
   
1. Az a **SQL Database** kattintson képernyő **tarifacsomag**. Fedezze fel a dtu-k és az egyes szolgáltatásszinteken elérhető tárhely mennyiségét.
   
   >[!NOTE]
   >Ebben a rövid útmutatóban használja a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md), de a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) is rendelkezésre áll.
   
   >[!NOTE]
   >Jelenleg több mint 1 TB tárterület egységára prémium szinten érhető el minden régióban, kivéve: Egyesült Királyság északi régiója, USA nyugati középső RÉGIÓJA, Egyesült Királyság South2, kelet-Kína, közép-USDoDCentral, Németország, USDoDEast, USA-beli államigazgatás – délnyugati, Északkelet-US Gov Dél-India középső régiója, Németország, Észak-Kína és USA-beli államigazgatás – kelet. Ezekben a régiókban a prémium szinthez tartozó tárterület maximuma 1 TB. További információkért lásd: [P11 – P15 – aktuális korlátozások](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   
1. Ez a rövid útmutatóhoz válassza a **Standard** szolgáltatásszintet, és a csúszka segítségével válassza ki **10 Dtu (S0)** és **1** GB tárhelyet.
   
1. Kattintson az **Alkalmaz** gombra.  
   
   ![Díjszabás kiválasztása](./media/sql-database-get-started-portal/create-database-s1.png)
   
1. Az a **SQL Database** képernyő, válassza ki **létrehozás** üzembe helyezéséhez és az erőforráscsoport, kiszolgáló és adatbázis üzembe helyezése. 
   
   Üzembe helyezés eltarthat néhány percig. Választhat **értesítések** üzembe helyezési folyamat állapotának monitorozásához az eszköztáron.

   ![Értesítés](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>Az SQL-adatbázis lekérdezése

Most, hogy létrehozott egy Azure SQL database, használja a beépített lekérdezési eszközzel az Azure Portalon kapcsolódni az adatbázishoz, és lekérdezheti az adatokat.

1. Az a **SQL Database** az adatbázisra, válassza a lap **Lekérdezésszerkesztő (előzetes verzió)** a bal oldali menüben. 
   
   ![Jelentkezzen be a szerkesztő lekérdezése](./media/sql-database-get-started-portal/query-editor-login.png)
   
1. Adja meg a bejelentkezési adatait, és válassza ki **OK**.
   
1. Adja meg a következő lekérdezést a **Lekérdezésszerkesztő** ablaktáblán.
   
   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```
   
1. Válassza ki **futtatása**, majd tekintse át a lekérdezési eredményeket az **eredmények** ablaktáblán.

   ![Lekérdezésszerkesztő: eredmények](./media/sql-database-get-started-portal/query-editor-results.png)
   
1. Zárja be a **Lekérdezésszerkesztő** lapon, és válassza ki **OK** amikor a rendszer kéri, hogy a nem mentett szerkesztések elvetéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg az erőforráscsoport, az SQL server és SQL database, ha szeretné nyissa meg a [további lépések](#next-steps). A következő lépések bemutatják, hogyan történő csatlakozásról és lekérdezésről különböző módszereket használ az adatbázis. 

Ha elkészült, használja ezeket az erőforrásokat, törölheti őket a következő:

1. Az Azure Portal bal oldali menüből válassza ki a **erőforráscsoportok**, majd válassza ki **myResourceGroup**.
1. Az erőforráscsoport lapján, válassza ki a **erőforráscsoport törlése**. 
1. Adja meg *myResourceGroup* a mezőt, és válassza ki a **törlése**.

## <a name="next-steps"></a>További lépések

- Csatlakozás az Azure SQL database a helyszíni vagy távoli eszközök kiszolgálószintű tűzfalszabály létrehozásához. További információkért lásd: [hozzon létre egy kiszolgálószintű tűzfalszabályt](sql-database-get-started-portal-firewall.md).
- Miután létrehozott egy kiszolgálószintű tűzfalszabályt [csatlakozásról és lekérdezésről](sql-database-connect-query.md) számos különböző eszközöket és nyelveket használ az adatbázis. 
  - [Kapcsolódás és lekérdezés az SQL Server Management Studióval](sql-database-connect-query-ssms.md)
  - [Kapcsolódás és lekérdezés az Azure Data Studióval](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Azure CLI használatával Azure SQL-adatbázisok létrehozásához lásd: [Azure CLI-minták](sql-database-cli-samples.md).
- Azure PowerShell-lel az Azure SQL-adatbázisok létrehozásához lásd: [Azure PowerShell-minták](sql-database-powershell-samples.md).
