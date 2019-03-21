---
title: Az SQL Server Integration Services-csomagok áttelepítése az Azure-bA |} A Microsoft Docs
description: Ismerje meg, hogyan telepítheti át SQL Server Integration Services-csomagok az Azure-bA.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 884af4624c1e92ee765353c90fd189220664381d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183424"
---
# <a name="migrate-sql-server-integration-services-packages-to-azure"></a>Az SQL Server Integration Services-csomagok áttelepítése az Azure-bA
Ha az SQL Server Integration Services (SSIS) használ, és az SSIS-projektek/csomagok áttelepítése a forráskiszolgálóról a cél Azure SQL Database-kiszolgáló vagy az Azure SQL Database felügyelt példánya által üzemeltetett SSISDB SQL-kiszolgáló által üzemeltetett SSISDB szeretne, akkor is újból üzembe helyezheti őket az integrációs szolgáltatások telepítése varázsló használatával. A varázsló az SQL Server Management Studio (SSMS) belül el is indíthatja.

Ha az SSIS használata verziója korábbi, mint 2012, mielőtt újbóli üzembe helyezés az SSIS-projektek/csomagok a projekt üzembe helyezési modellbe, először konvertálhatja azokat az integrációs szolgáltatások projekt átalakítás varázslót, amely is elindítható az SSMS használatával. További információkért tekintse meg a cikket [projektek konvertálása a project-alapú üzemi modellbe](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Az Azure Database Migration Service (DMS) jelenleg nem támogatott az SSISDB forrás, de, ugyanakkor az SSIS-projektek/csomagok a következő eljárással. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Forrás SSIS-projektek/csomagok felmérése
> * SSIS-projektek/csomagok áttelepítése az Azure-bA.

## <a name="prerequisites"></a>Előfeltételek
A lépések elvégzéséhez szüksége:

- SSMS 17,2 vagy újabb verziója.
- A cél gazdagépre SSISDB adatbázis-kiszolgáló egy példányát.
 
  Ha még nem rendelkezik:
    - Az Azure SQL Database, egy Azure SQL Database-kiszolgáló létrehozása (nélkül egy adatbázis) az Azure Portalon lépjen az SQL Server (csak logikai kiszolgáló) [űrlap](https://ms.portal.azure.com/#create/Microsoft.SQLServer).
    - Az Azure SQL Database felügyelt példányába, kövesse a cikk részletesen [hozzon létre egy Azure SQL Database felügyelt példányába](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).

- SSIS kell létrehozni az Azure Data Factory (ADF) tartalmazó Azure-SSIS integrációs modul (IR) a cél Azure SQL Database-kiszolgáló példánya által üzemeltetett SSISDB (a cikkben leírtak szerint [üzembe helyezése az Azure-SSIS integrációs Modul az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)) vagy az Azure SQL Database felügyelt példány (a cikkben leírtak szerint [az Azure-SSIS integrációs modul létrehozása az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)). 

## <a name="assess-source-ssis-projectspackages"></a>Forrás SSIS-projektek/csomagok felmérése
Értékelési forrás SSISDB még nem integrált a Database Migration Assistant (DMA) vagy az Azure Database Migration Service (DMS), míg az SSIS-projektek/csomagok értékelt/érvényesíti, ezek a célhelyen lévő üzemeltetett SSISDB kérésének vannak Az Azure SQL Database-kiszolgáló vagy Azure SQL Database felügyelt példányain.

## <a name="migrate-ssis-projectspackages"></a>SSIS-projektek/csomagok áttelepítése
SSIS-projektek/csomagok áttelepítése az Azure SQL Database-kiszolgáló vagy az Azure SQL Database felügyelt példányába, hajtsa végre az alábbi lépéseket.

1.  Nyissa meg az ssms-ben, és válassza ki **beállítások** megjelenítéséhez a **kapcsolódás a kiszolgálóhoz** párbeszédpanel bezárásához.

2.  Az a **bejelentkezési** lapra, adja meg az Azure SQL Database-kiszolgáló vagy az Azure SQL Database felügyelt példánya a cél SSISDB üzemeltetési való kapcsolódáshoz szükséges adatokat.

    ![SSIS-bejelentkezési lap](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)
 
3.  Az a **kapcsolat tulajdonságai** lap a **csatlakozhat az adatbázishoz** szövegbeviteli mezőben válasszon ki vagy adja meg **SSISDB**, majd válassza ki **Connect**.

    ![SSIS-kapcsolat tulajdonságai lap](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4.  Az SSMS Object Explorerben bontsa ki a **Integration Services-katalógusok** csomópontot, bontsa ki a **SSISDB**, és ha nem találhatók meglévő mappák, kattintson a jobb gombbal **SSISDB** és Hozzon létre egy új mappát.

5.  A **SSISDB**, bontsa ki minden olyan mappát, kattintson a jobb gombbal **projektek**, majd válassza ki **projekt üzembe helyezése**.

    ![SSIS SSISDB csomópont kibontva](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6.  Az integrációs szolgáltatások telepítése varázsló a a **bemutatása** lapon tekintse át az adatokat, és válassza ki **tovább**.

    ![Központi telepítési varázsló bemutató lapja](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7.  Az a **forrás kiválasztása** adja meg azokat a meglévő SSIS-projekt, amely számára telepíteni kívánja.

    Ha az ssms-ben is össze van kapcsolva az SQL Server, a forrás SSISDB üzemeltetési, válassza ki a **Integration Services katalógus**, majd adja meg a kiszolgáló nevét és a projekt elérési útját a katalógusban, közvetlenül a projekt telepítésére.

    Azt is megteheti, válassza ki **projekt üzembe helyezési fájl**, és adja meg az elérési útját egy meglévő projekt üzembe helyezési fájl (.ispac) a projekt telepítésére.

    ![Üzembe helyezés varázsló forrás kiválasztása lap](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8.  Kattintson a **Tovább** gombra.
9.  Az a **cél kiválasztása** adja meg azokat a cél a projekthez.

       a. A kiszolgáló neve szövegbeviteli mezőben adja meg az Azure SQL Database teljes kiszolgálónév (< kiszolgáló_neve >. database.windows.net) vagy Azure SQL Database felügyelt példány nevét (< server_name.dns_prefix >. database.windows.net).
 
       b. Adja meg a hitelesítési adatokat, és válassza ki **Connect**.
    
       ![Üzembe helyezés varázsló cél kiválasztása lap](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Válassza a Tallózás gombra a célmappa az SSISDB, és kattintson a Tovább gombra.

    > [!NOTE]
    > A **tovább** kiválasztása után csak gomb **Connect**. 

10. Az a **ellenőrzése** lapon, majd ha szükséges, módosítsa a csomagok megfelelően és minden olyan hibák és figyelmeztetések megtekintése.

    ![Üzembe helyezés ellenőrzése varázsló lap](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Kattintson a **Tovább** gombra.

12. Az a **tekintse át** lapon, tekintse át a telepítési beállításokat.

    > [!NOTE]
    > Előző kiválasztásával, vagy a bal oldali ablaktáblán a lépés hivatkozások bármelyikére kattintva módosíthatja a beállításokat.

13. Válassza ki **telepítés** az üzembe helyezés elindításához.

14. Az üzembe helyezési folyamat befejezése után megtekintheti a eredmények oldal, amely megjeleníti a sikeres vagy minden egyes központi telepítési művelete sikertelen.
    a. Ha bármilyen művelet sikertelen volt a a **eredmény** oszlopában válassza **sikertelen** magyarázatát tartalmazza a hiba megjelenítéséhez.
    b. Bejelölheti **jelentés mentése** az eredményeket XML-fájlba menti.

15. Válassza ki **Bezárás** az integrációs szolgáltatások telepítési varázslóból való kilépéshez.

Ha az üzembe helyezés a projekt sikeres hiba nélkül, válassza a csomagokat tartalmaz futtathatók az Azure-SSIS integrációs modult.

## <a name="next-steps"></a>További lépések
- Tekintse át a migrálási útmutató segítséget nyújt a Microsoft [adatbázis-Migrálási útmutató](https://datamigration.microsoft.com/).