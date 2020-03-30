---
title: Adatok másolása a Blob Storage szolgáltatásból az SQL-adatbázisba – Azure
description: Ez az oktatóanyag bemutatja, hogyan használhatja a másolási tevékenység et egy Azure Data Factory-folyamat adatok másolása a Blob storage-ból SQL-adatbázisba.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cc2f0a513219a671dd8a75ee00af4fc9d4c6a68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979729"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Oktatóanyag: Adatok másolása a Blob Storage-ból az SQL Database-be a Data Factory használatával
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
> * [Vizuális stúdió](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [Powershell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [másolási tevékenység oktatóanyagát](../quickstart-create-data-factory-dot-net.md).

Ebben az oktatóanyagban hozzon létre egy adat-előállítót egy folyamattal, amely adatokat másol a Blob storage-ból az SQL-adatbázisba.

A másolási tevékenység végzi az adattovábbítást az Azure Data Factoryban. Egy olyan, globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és méretezhető módon másolja át az adatokat a különböző adattárak között. A Másolás tevékenységgel kapcsolatos részletekért tekintse meg a [Data Movement Activities](data-factory-data-movement-activities.md) (Adattovábbítási tevékenységek) című cikket.  

> [!NOTE]
> A Data Factory szolgáltatás részletes áttekintését az [Azure Data Factory bevezetés](data-factory-introduction.md) című cikkben találja.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Az oktatóanyag előfeltételei
Az oktatóanyag megkezdése előtt a következő előfeltételekkel kell rendelkeznie:

* **Azure-előfizetés**.  Ha nem rendelkezik előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes próbafiókot. A részleteket lásd az [Ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/) című cikkben.
* **Az Azure Storage-fiók.** Ebben az oktatóanyagban a **blobstorage-t** forrásadattárként használja. ha nem rendelkezik Azure-tárfiókkal, tekintse meg a [tárfiók létrehozása](../../storage/common/storage-account-create.md) cikket a létrehozási lépéseket.
* **Az Azure SQL Database .** Ebben az oktatóanyagban **destination** egy Azure SQL-adatbázist használ céladattárként. Ha nem rendelkezik egy Azure SQL-adatbázis, amely az oktatóanyagban használható, olvassa el az [Azure SQL-adatbázis létrehozása és konfigurálása](../../sql-database/sql-database-get-started.md) létrehozása.
* **SQL Server 2012/2014 vagy Visual Studio 2013**. Az SQL Server Management Studio vagy a Visual Studio segítségével mintaadatbázist hozhat létre, és megtekintheti az adatbázis eredményadatait.  

## <a name="collect-blob-storage-account-name-and-key"></a>Blob tárfiók nevének és kulcsának gyűjtése
Ehhez az oktatóanyaghoz szüksége van az Azure storage-fiók fióknevére és fiókkulcsára. Jegyezze fel **a fiók nevét** és a **fiókkulcsot** az Azure storage-fiókjához.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Kattintson a bal oldali menü **Összes szolgáltatás parancsára,** és válassza **a Tárfiókok lehetőséget.**

    ![Tallózás – Tárolási fiókok](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. A **Storage-fiókok** panelen válassza ki az ebben az oktatóanyagban használni kívánt **Azure-tárfiókot.**
4. A **BEÁLLÍTÁSOK**csoportban válassza **az Access-billentyűk** hivatkozását.
5. Kattintson **a Másolás** (kép) gombra **a Tárfiók neve** mező mellett, és mentse / illessze be valahol (például egy szöveges fájlban).
6. Ismételje meg az előző lépést a billentyű másolásához vagy **megjegyzéséhez1**.

    ![Tárolási hozzáférési kulcs](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Zárja be az összes pengék kattintva **X**.

## <a name="collect-sql-server-database-user-names"></a>SQL-kiszolgáló, adatbázis, felhasználónevek gyűjtése
Ehhez az oktatóanyaghoz az Azure SQL-kiszolgáló, az adatbázis és a felhasználó nevére van szükség. Jegyezze fel a **kiszolgáló**, **adatbázis**és az Azure SQL-adatbázis **felhasználójának** nevét.

1. Az **Azure Portalon**kattintson a bal oldali **Összes szolgáltatás** elemre, és válassza ki az **SQL-adatbázisokat.**
2. Az **SQL-adatbázisok panelen**jelölje ki az oktatóanyagban használni kívánt **adatbázist.** Jegyezze fel az **adatbázis nevét**.  
3. Az **SQL adatbázis** panelen kattintson a **Beállítások csoport** **Tulajdonságok** gombjára.
4. Jegyezze fel a **KISZOLGÁLÓ NÉV** ÉS A KISZOLGÁLÓ **FELÜGYELETI BEJELENTKEZÉS**értékeit.
5. Zárja be az összes pengék kattintva **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Az Azure-szolgáltatások sql-kiszolgálóelérésének engedélyezése
Győződjön meg arról, **hogy az Azure-szolgáltatások** hoz való hozzáférés engedélyezése beállítás **be van kapcsolva** az Azure SQL-kiszolgálón, hogy a Data Factory szolgáltatás hozzáférhessen az Azure SQL-kiszolgálóhoz. A beállítás ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

1. Kattintson a bal oldali **Összes szolgáltatásközpont** elemre, majd kattintson az **SQL-kiszolgálók**elemre.
2. Válassza ki a kiszolgálót, és kattintson a **BEÁLLÍTÁSOK** területen a **Tűzfal** elemre.
3. A **Tűzfalbeállítások** panelen kattintson a **BE** kapcsolóra az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás mellett.
4. Zárja be az összes pengék kattintva **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Blob Storage és SQL-adatbázis előkészítése
Most készítse elő az Azure blob storage és az Azure SQL-adatbázis az oktatóanyag az alábbi lépések végrehajtásával:  

1. Indítsa el a Jegyzettömböt. Másolja a következő szöveget **emp.txt** fájlba a merevlemez **C:\ADFGetStarted** mappájába.

    ```
    John, Doe
    Jane, Doe
    ```
2. Az [Azure Storage Explorer](https://storageexplorer.com/) vagy egy hasonló eszköz használatával hozza létre az **adftutorial** tárolót, és töltse fel az **emp.txt** fájlt a tárolóba.

3. A következő SQL-szkript használatával hozza létre az **emp** táblát az Azure SQL Database-ben.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Ha az SQL Server 2012/2014 telepítve van a számítógépen:** kövesse az [Azure SQL Database kezelése az SQL Server Management Studio használatával](../../sql-database/sql-database-manage-azure-ssms.md) az Azure SQL-kiszolgálóhoz való csatlakozáshoz és az SQL-parancsfájl futtatásához.

    Ha az ügyfél számára nem engedélyezett az Azure SQL Server elérése, konfigurálnia kell az Azure SQL Server tűzfalát, hogy engedélyezze a hozzáférést a gép számára (IP-cím). Az Azure SQL Server-tűzfal konfigurálásának lépéseit lásd [ebben a cikkben](../../sql-database/sql-database-configure-firewall-settings.md).

## <a name="create-a-data-factory"></a>Data factory létrehozása
Teljesítette az előfeltételeket. Adateltelepezést az alábbi módszerek egyikével hozhat létre. Kattintson az oktatóanyag végrehajtásához kattintson a lista tetején található legördülő lista egyik beállítására, vagy az alábbi hivatkozásokra.     

* [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
* [Vizuális stúdió](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [Powershell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Az oktatóanyagban található adatfeldolgozási folyamat adatokat másol egy forrásadattárból egy céladattárba. A bemeneti adatokat nem alakítja át kimeneti adatok létrehozásához. Az adatok Azure Data Factoryval történő átalakításának útmutatásáért olvassa el [az adatok Hadoop-fürt segítségével történő átalakítására szolgáló első folyamat létrehozását ismertető oktatóanyagot](data-factory-build-your-first-pipeline.md).
>
> Összefűzhet két tevékenységet (vagyis egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. Lásd [a Data Factorybeli ütemezést és végrehajtást](data-factory-scheduling-and-execution.md) ismertető cikket.
