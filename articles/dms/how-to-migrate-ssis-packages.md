---
title: SSIS-csomagok újratelepítése sql egyetlen adatbázisba
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan telepítheti át vagy helyezheti át újra az SQL Server Integration Services-csomagokat és -projekteket az Azure SQL Database egyetlen adatbázisába az Azure Database Migration Service és az Adatáttelepítési segéd használatával.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 90a39b8fe3604a05f1d35a875ae4e34491b47d72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648529"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>SSIS-csomagok újratelepítése az Azure Database Migration Service szolgáltatással az Azure SQL Database szolgáltatásra

Ha SQL Server Integration Services (SSIS) szolgáltatást használ, és át szeretné telepíteni az SSIS-projekteket/-csomagokat az SQL Server által üzemeltetett ForrásSISDB-ből az Azure SQL Database által üzemeltetett cél SSISDB-be, újratelepítheti őket az Integrációs szolgáltatások központi telepítése varázslóval. A varázslót az SQL Server Management Studio (SSMS) épületéből indíthatja el.

Ha az SSIS által használt verzió korábbi, mint 2012, mielőtt az SSIS-projekteket/csomagokat áthelyezi a projekt telepítési modelljébe, először konvertálnia kell őket az Integrációs szolgáltatások projektkonverzióvarázslójával, amely az SSMS-ből is indítható. További információt a [Projektek konvertálása a projekt telepítési modelljéhez](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)című témakörben talál.

> [!NOTE]
> Az Azure Database Migration Service (DMS) jelenleg nem támogatja a forrás SSISDB egy Azure SQL Database-kiszolgálóra való áttelepítését, de az SSIS-projekteket/csomagokat a következő folyamat használatával újratelepítheti.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
>
> * Forrás SSIS-projektek/csomagok felmérése.
> * Telepítse át az SSIS-projekteket/csomagokat az Azure-ba.

## <a name="prerequisites"></a>Előfeltételek

A lépések végrehajtásához a következőkre van szükség:

* SSMS 17.2-es vagy újabb verziója.
* Az SSISDB üzemeltetésére a céladatbázis-kiszolgáló példánya. Ha még nem rendelkezik ilyen, hozzon létre egy Azure SQL Database-kiszolgálót (adatbázis nélkül) az Azure Portalon az SQL Server (csak logikai kiszolgáló) [űrlapra](https://ms.portal.azure.com/#create/Microsoft.SQLServer)navigálva.
* Az SSIS-t az Azure-SSIS-integrációs futásidejű (IR) tartalmazó Azure Data Factoryban (ADF) kell kiépíteni az Azure-SSIS-integrációs runtime-ot (IR) az Azure SQL Database-kiszolgáló példánya által üzemeltetett cél SSISDB-vel (az [Azure-SSIS-integrációs futásidő kiépítése az Azure Data Factoryban](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)című cikkben leírtak szerint).

## <a name="assess-source-ssis-projectspackages"></a>Forrás SSIS-projektek/csomagok értékelése

Bár a forrás SSISDB értékelése még nincs integrálva az adatbázis-áttelepítési segédbe (DMA) vagy az Azure Database Migration Service (DMS) szolgáltatásba, az SSIS-projektek/-csomagok értékelése/érvényesítése az Azure SQL Database-kiszolgálón üzemeltetett cél SSISDB-re való újratelepítése kor lesz.

## <a name="migrate-ssis-projectspackages"></a>SSIS-projektek/csomagok áttelepítése

SSIS-projektek/csomagok Azure SQL Database-kiszolgálóra való áttelepítéséhez hajtsa végre a következő lépéseket.

1. Nyissa meg a SSMS-t, majd a **Beállítások** lehetőséget a **Csatlakozás kiszolgálóhoz** párbeszédpanel megjelenítéséhez.

2. A **Bejelentkezés** lapon adja meg az Azure SQL Database-kiszolgálóhoz való csatlakozáshoz szükséges információkat, amely a cél SSISDB-t fogja üzemeltetni.

    ![SSIS bejelentkezés lap](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. A **Kapcsolat tulajdonságai** lap **Csatlakozás adatbázishoz** mezőjében jelölje be vagy írja be az **SSISDB**elemet, majd kattintson a **Csatlakozás gombra.**

    ![Az SSIS-kapcsolat tulajdonságai lap](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. Az SSMS Object Explorerben bontsa ki az **Integrációs szolgáltatások katalógusai csomópontot, bontsa** ki az **SSISDB**csomópontot, és ha nincsenek meglévő mappák, kattintson a jobb gombbal az **SSISDB** elemre, és hozzon létre egy új mappát.

5. Az **SSISDB**csoportban bontsa ki bármelyik mappát, kattintson a jobb gombbal **a Projektek**elemre, és válassza a Project telepítése **parancsot.**

    ![SSIS SSISDB csomópont kibontva](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. Az Integrációs szolgáltatások telepítése varázsló **Bevezetés** lapján tekintse át az információkat, majd kattintson a **Tovább**gombra.

    ![Központi telepítési varázsló – Bevezetés lap](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. A **Forrás kiválasztása** lapon adja meg a telepíteni kívánt meglévő SSIS-projektet.

    Ha az SSMS a forrás SSISDB-t üzemeltető SQL Server kiszolgálóhoz is csatlakozik, válassza az **Integrációs szolgáltatások katalógusát,** majd írja be a kiszolgáló nevét és a projekt elérési útját a katalógusba a projekt közvetlen telepítéséhez.

    Másik lehetőségként válassza a **Project telepítési fájlját,** majd adja meg egy meglévő projekttelepítési fájl (.ispac) elérési útját a projekt telepítéséhez.

    ![Központi telepítési varázsló Forrás kiválasztása lapja](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Válassza a **Tovább lehetőséget.**
9. A **Cél kiválasztása** lapon adja meg a projekt célját.

    a. A Kiszolgáló neve mezőbe írja be a teljesen minősített Azure SQL Database-kiszolgáló nevét (<server_name>.database.windows.net).

    b. Adja meg a hitelesítési adatokat, majd válassza a **Csatlakozás lehetőséget.**

    ![Központi telepítési varázsló Cél kiválasztása lapja](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. A **Tallózás gombra** a célmappa SSISDB-ben való megadásához válassza a **Tovább**gombot.

    > [!NOTE]
    > A **Tovább** gomb csak a **Csatlakozás**lehetőséget követően engedélyezett.

10. Az **Érvényesítés** lapon tekintse meg a hibákat/figyelmeztetéseket, majd szükség esetén módosítsa a csomagokat ennek megfelelően.

    ![A Központi telepítési varázsló ellenőrzése lapja](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Válassza a **Tovább lehetőséget.**

12. A **Véleményezés** lapon tekintse át a telepítési beállításokat.

    > [!NOTE]
    > A beállításokat az **Előző** lehetőséget választva módosíthatja, vagy a bal oldali ablaktáblában lévő bármelyik lépéshivatkozást kijelölheti.

13. A **központi telepítési** folyamat elindításához válassza a Telepítés lehetőséget.

14. A központi telepítési folyamat befejezése után megtekintheti az Eredmények lapot, amely az egyes telepítési műveletek sikeresvagy sikertelenségét jeleníti meg.
    a. Ha valamelyik művelet sikertelen volt, az **Eredmény** oszlopban válassza a **Sikertelen lehetőséget** a hiba magyarázatának megjelenítéséhez.
    b. Ha az eredményeket XML-fájlba szeretné menteni, válassza a **Jelentés mentése** lehetőséget.

15. Válassza a **Bezárás** lehetőséget az Integrációs szolgáltatások központi telepítését segítő varázslóból való kilépéshez.

Ha a projekt telepítése sikertelen, kiválaszthatja az Azure-SSIS IR-en futtatandó csomagokat.

## <a name="next-steps"></a>További lépések

* Tekintse át az áttelepítési útmutatót a Microsoft [adatbázis-áttelepítési útmutatójában.](https://datamigration.microsoft.com/)
