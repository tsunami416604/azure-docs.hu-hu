---
title: 'Oktatóanyag: Azure SQL Database rugalmas készlet hozzáadása feladatátvételi csoporthoz | Microsoft Docs'
description: A Azure Portal, a PowerShell vagy az Azure CLI használatával hozzáadhat egy Azure SQL Database rugalmas készletet egy feladatátvételi csoporthoz.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 0507b3cb64b4b12bac92cc6bc90120ab4ec56dee
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568738"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Oktatóanyag: Azure SQL Database rugalmas készlet hozzáadása feladatátvételi csoporthoz

Feladatátvevő csoport konfigurálása Azure SQL Database rugalmas készlethez és feladatátvételi teszt a Azure Portal használatával.  Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> - Hozzon létre egy Azure SQL Database önálló adatbázist.
> - Adja hozzá az önálló adatbázist egy rugalmas készlethez. 
> - Hozzon létre egy [feladatátvételi csoportot](sql-database-auto-failover-group.md) egy rugalmas készlethez két logikai SQL-kiszolgáló között.
> - Feladatátvételi teszt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel: 

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .


## <a name="1---create-a-single-database"></a>1 – önálló adatbázis létrehozása 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 – önálló adatbázis hozzáadása a rugalmas készlethez

1. Válassza az **erőforrás létrehozása** lehetőséget a [Azure Portal](https://portal.azure.com)bal felső sarkában.
1. Írja `elastic pool` be a keresőmezőbe az ENTER billentyűt, válassza ki az **SQL rugalmas adatbázis-készlet** ikont, majd válassza a **Létrehozás**lehetőséget. 

    ![Rugalmas készlet kiválasztása a piactéren](media/sql-database-elastic-pool-create-failover-group-tutorial/elastic-pool-market-place.png)

1. Állítsa be a rugalmas készletet a következő értékekkel:
   - **Név**: Adjon egyedi nevet a rugalmas készletnek, például `myElasticPool`:. 
   - **Előfizetés**: Válassza ki előfizetését a legördülő listából.
   - **ResourceGroup**: Válassza `myResourceGroup` ki a legördülő menüből az 1. szakaszban létrehozott erőforráscsoportot. 
   - **Kiszolgáló**: Válassza ki az 1. szakaszban létrehozott kiszolgálót a legördülő listából.  

       ![Új kiszolgáló létrehozása rugalmas készlethez](media/sql-database-elastic-pool-create-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Számítás + tárolás**: Válassza a **rugalmas készlet beállítása** lehetőséget a számítási és tárolási beállítások konfigurálásához, és adja hozzá az önálló adatbázist a rugalmas készlethez. A **készlet beállításai** lapon hagyja meg az alapértelmezett Gen5, 2 virtuális mag és 32gb értékkel. 

1. A **configure (Konfigurálás** ) lapon válassza az **adatbázisok** fület, majd válassza az **adatbázis hozzáadása**elemet. Válassza ki az 1. szakaszban létrehozott adatbázist, majd válassza az **alkalmaz** lehetőséget a rugalmas készlethez való hozzáadásához. A rugalmas készlet beállításainak alkalmazásához és a **configure (Konfigurálás** ) lap bezárásához kattintson ismét az **alkalmaz** gombra. 

    ![SQL-adatbázis hozzáadása rugalmas készlethez](media/sql-database-elastic-pool-create-failover-group-tutorial/add-database-to-elastic-pool.png)

1. A rugalmas készlet beállításainak áttekintéséhez válassza a **felülvizsgálat + létrehozás** lehetőséget, majd válassza a **Létrehozás** lehetőséget a rugalmas készlet létrehozásához. 


## <a name="3---create-the-failover-group"></a>3 – a feladatátvételi csoport létrehozása 
Ebben a lépésben létre fog hozni egy [feladatátvételi csoportot](sql-database-auto-failover-group.md) egy meglévő Azure SQL Server-kiszolgáló és egy másik régióban lévő új Azure SQL Server között. Ezután adja hozzá a rugalmas készletet a feladatátvételi csoporthoz. 


1. Válassza ki az **összes szolgáltatást** a [Azure Portal](https://portal.azure.com)bal felső sarkában. 
1. Írja `sql servers` be a keresőmezőbe a kifejezést. 
1. Választható Válassza ki a csillag ikont az SQL-kiszolgálók mellett kedvenc **SQL-kiszolgálókhoz** , és adja hozzá a bal oldali navigációs ablaktáblához. 
    
    ![SQL-kiszolgálók keresése](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. Válassza az **SQL-kiszolgálók** lehetőséget, és válassza ki az 1. szakaszban létrehozott kiszolgálót.
1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza a **Csoport hozzáadása** lehetőséget egy új feladatátvételi csoport létrehozásához. 

    ![Új feladatátvételi csoport hozzáadása](media/sql-database-elastic-pool-create-failover-group-tutorial/add-elastic-pool-to-failover-group.png)

1. A **feladatátvételi csoport** lapon adja meg vagy válassza ki a következő értékeket, majd válassza a **Létrehozás**lehetőséget:
    - **Feladatátvételi csoport neve**: Írjon be egy egyedi feladatátvételi csoport nevét, például `failovergrouptutorial`:. 
    - **Másodlagos kiszolgáló**: Válassza a *szükséges beállítások konfigurálását* , majd válassza az **új kiszolgáló létrehozása**lehetőséget. Másik lehetőségként már meglévő kiszolgálót is választhat másodlagos kiszolgálóként. A következő értékek beírása után válassza a **kiválasztás**lehetőséget. 
        - **Kiszolgáló neve**: Írjon be egy egyedi nevet a másodlagos kiszolgálónak, például `mysqlsecondary`:. 
        - **Kiszolgáló-rendszergazdai bejelentkezés**: Típusa`azureuser`
        - **Jelszó**: Írjon be egy olyan összetett jelszót, amely megfelel a jelszó követelményeinek.
        - **Hely**: Válasszon egy helyet a legördülő menüből, például az USA 2. keleti régiójában. Ez a hely nem lehet ugyanazon a helyen, mint az elsődleges kiszolgáló.

       > [!NOTE]
       > A kiszolgáló bejelentkezési és tűzfalbeállítások meg kell egyeznie az elsődleges kiszolgálóval. 
    
       ![Másodlagos kiszolgáló létrehozása a feladatátvételi csoport számára](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

1. Ha a másodlagos kiszolgáló van kiválasztva, a **csoporton belüli adatbázisok** feloldhatók. Jelölje ki a **hozzáadni** kívánt adatbázisokat, majd válassza ki a 2. szakaszban létrehozott rugalmas készletet. Egy figyelmeztetésnek kell megjelennie, amely arra kéri, hogy hozzon létre egy rugalmas készletet a másodlagos kiszolgálón. Válassza ki a figyelmeztetést, majd kattintson az **OK** gombra a rugalmas készlet létrehozásához a másodlagos kiszolgálón. 
        
    ![SQL-adatbázis hozzáadása a feladatátvételi csoporthoz](media/sql-database-single-database-create-failover-group-tutorial/add-sqldb-to-failover-group.png)
        
1. Válassza a **kiválasztás** elemet a rugalmas készlet beállításainak a feladatátvételi csoportra való alkalmazásához, majd válassza a **Létrehozás** lehetőséget a feladatátvételi csoport létrehozásához. A rugalmas készlet a feladatátvételi csoportba való felvétele automatikusan elindítja a Geo-replikálási folyamatot. 


## <a name="4---test-failover"></a>4 – feladatátvételi teszt 
Ebben a lépésben a feladatátvételi csoportot a másodlagos kiszolgálóra fogja felvenni, majd a Azure Portal használatával hajtja végre a feladatokat. 

1. Navigáljon az **SQL Server** -kiszolgálóhoz a [Azure Portalon](https://portal.azure.com)belül. 
1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza ki a 2. szakaszban létrehozott feladatátvételi csoportot. 
  
   ![Válassza ki a feladatátvételi csoportot a portálon](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. Ellenőrizze, hogy melyik kiszolgáló elsődleges, és melyik kiszolgáló a másodlagos. 
1. Válassza a **feladatátvétel** lehetőséget a feladat ablaktáblán a rugalmas készletet tartalmazó feladatátvételi csoport feladatátvételéhez. 
1. Válassza az **Igen** lehetőséget arra a figyelmeztetésre, amely értesíti, hogy a TDS-munkamenetek le lesznek választva. 

   ![Az SQL-adatbázist tartalmazó feladatátvételi csoport feladatátvétele](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. Tekintse át, hogy melyik kiszolgáló elsődleges, melyik kiszolgáló a másodlagos. Ha a feladatátvétel sikeres volt, a két kiszolgálónak felcserélt szerepkörrel kell rendelkeznie. 
1. Válassza újra a feladatátvételt, hogy a feladatátvételi csoportot vissza lehessen térni az eredeti beállításokhoz. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Törölje az erőforrásokat az erőforráscsoport törlésével. 

1. Navigáljon az erőforráscsoporthoz a [Azure Portal](https://portal.azure.com).
1. Válassza az **erőforráscsoport törlése** lehetőséget a csoport összes erőforrásának, valamint maga az erőforráscsoport törléséhez. 
1. Írja be az erőforráscsoport `myResourceGroup`nevét, a szövegmezőbe, majd kattintson a **Törlés** elemre az erőforráscsoport törléséhez.  


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure SQL Database önálló adatbázist adott hozzá egy feladatátvételi csoporthoz, és tesztelte a feladatátvételt. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Hozzon létre egy Azure SQL Database önálló adatbázist.
> - Adja hozzá az önálló adatbázist egy rugalmas készlethez. 
> - Hozzon létre egy [feladatátvételi csoportot](sql-database-auto-failover-group.md) egy rugalmas készlethez két logikai SQL-kiszolgáló között.
> - Feladatátvételi teszt.

Folytassa a következő oktatóanyaggal, amely bemutatja, hogyan migrálhat a DMS használatával.

> [!div class="nextstepaction"]
> [Oktatóanyag: SQL Server migrálása egy készletezett adatbázisba a DMS használatával](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
