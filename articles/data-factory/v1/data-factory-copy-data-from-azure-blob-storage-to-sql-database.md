---
title: Adatok másolása Blob Storageból SQL Databaseba – Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a másolási tevékenységet egy Azure Data Factory folyamatba az adatok blob Storage-ból az SQL Database-be való másolásához.
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
ms.openlocfilehash: 0255787ac90e63aff02ea65912ffa37c8ecc09fa
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929751"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Oktatóanyag: adatok másolása Blob Storageról SQL Databasera a Data Factory használatával
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [másolási tevékenység oktatóanyagát](../quickstart-create-data-factory-dot-net.md). 

Ebben az oktatóanyagban egy adatfeldolgozót hoz létre egy folyamattal, amely az adatok blob Storage-ból az SQL Database-be való másolásához szükséges.

A másolási tevékenység végzi az adatok továbbítását az Azure Data Factoryban. Egy olyan, globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és méretezhető módon másolja át az adatokat a különböző adattárak között. A Másolás tevékenységgel kapcsolatos részletekért tekintse meg a [Data Movement Activities](data-factory-data-movement-activities.md) (Adattovábbítási tevékenységek) című cikket.  

> [!NOTE]
> A Data Factory szolgáltatás részletes áttekintését lásd: [Bevezetés a Azure Data Factory](data-factory-introduction.md) cikkbe.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Az oktatóanyag előfeltételei
Az oktatóanyag megkezdése előtt a következő előfeltételeket kell megadnia:

* **Azure-előfizetés**.  Ha nem rendelkezik előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes próbafiókot. További részletekért tekintse meg az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) ismertető cikket.
* **Azure Storage-fiók**. Ebben az oktatóanyagban a blob Storage-t használja **forrásként** szolgáló adattárként. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../../storage/common/storage-quickstart-create-account.md) ismertető cikket.
* **Azure SQL Database** Ebben az oktatóanyagban az Azure SQL Database-t használja **célként** szolgáló adattárként. Ha nem rendelkezik Azure SQL-adatbázissal, amelyet az oktatóanyagban használhat, tekintse meg a következő témakört: [Azure SQL Database létrehozása és konfigurálása](../../sql-database/sql-database-get-started.md) egy létrehozásához.
* **SQL Server 2012/2014 vagy Visual Studio 2013**. A SQL Server Management Studio vagy a Visual Studio használatával hozzon létre egy mintaadatbázis-adatbázist, és tekintse meg az eredményeket az adatbázisban.  

## <a name="collect-blob-storage-account-name-and-key"></a>BLOB Storage-fiók nevének és kulcsának gyűjtése
Az oktatóanyag elvégzéséhez szüksége lesz az Azure Storage-fiókja fiókjának nevére és a fiók kulcsára. Jegyezze fel az Azure Storage-fiókhoz tartozó **fiók nevét** és a **fiók kulcsát** .

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben kattintson a **minden szolgáltatás** elemre, majd válassza a **Storage-fiókok**lehetőséget.

    ![Tallózás – Storage-fiókok](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. A **Storage-fiókok** panelen válassza ki azt az **Azure Storage-fiókot** , amelyet ebben az oktatóanyagban szeretne használni.
4. Válassza a **hozzáférési kulcsok** hivatkozását a **Beállítások**alatt.
5. Kattintson a **Másolás** (rendszerkép) gombra a **Storage-fiók neve** szövegmező mellett, és mentse/illessze be valahova (például szövegfájlba).
6. Az előző lépés megismétlésével másolja vagy jegyezze fel a **key1**.

    ![Tárelérési kulcs](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Az **X**gombra kattintva zárjuk le az összes pengét.

## <a name="collect-sql-server-database-user-names"></a>SQL Server, adatbázis, felhasználónevek gyűjtése
Ehhez az oktatóanyaghoz az Azure SQL Server, az adatbázis és a felhasználó nevét kell megadnia. Jegyezze fel a **kiszolgáló**, az **adatbázis**és a **felhasználó** nevét az Azure SQL Database-ben.

1. A **Azure Portal**kattintson a bal oldali **minden szolgáltatás** elemre, és válassza az **SQL-adatbázisok**lehetőséget.
2. Az **SQL-adatbázisok**panelen válassza ki az oktatóanyagban használni kívánt **adatbázist** . Jegyezze fel az **adatbázis nevét**.  
3. Az **SQL Database** panelen kattintson a **Tulajdonságok** elemre a **Beállítások**területen.
4. Jegyezze fel a **kiszolgálónév** és a **kiszolgálói rendszergazdai bejelentkezés**értékeit.
5. Az **X**gombra kattintva zárjuk le az összes pengét.

## <a name="allow-azure-services-to-access-sql-server"></a>Az SQL Server elérésének engedélyezése az Azure-szolgáltatások számára
Győződjön **meg** arról, hogy az Azure- **szolgáltatások hozzáférésének engedélyezése** beállítás be van kapcsolva az Azure SQL Serverhez, hogy a Data Factory szolgáltatás hozzáférhessen az Azure SQL Serverhez. A beállítás ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

1. Kattintson a bal oldali **összes szolgáltatás** központ elemre, majd az **SQL-kiszolgálók**lehetőségre.
2. Válassza ki a kiszolgálót, és kattintson a **BEÁLLÍTÁSOK** területen a **Tűzfal** elemre.
3. A **Tűzfalbeállítások** panelen kattintson a **BE** kapcsolóra az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás mellett.
4. Az **X**gombra kattintva zárjuk le az összes pengét.

## <a name="prepare-blob-storage-and-sql-database"></a>Blob Storage és SQL Database előkészítése
Készítse elő az Azure Blob Storage-t és az Azure SQL Database-t az oktatóanyaghoz az alábbi lépések végrehajtásával:  

1. Nyissa meg a Jegyzettömböt. Másolja az alábbi szöveget, és mentse **EMP. txt** néven a **C:\ADFGetStarted** mappába a merevlemezen.

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

    **Ha a számítógépen SQL Server 2012/2014 van telepítve:** kövesse az [Azure SQL Database SQL Server Management Studio használatával történő kezelésével](../../sql-database/sql-database-manage-azure-ssms.md) kapcsolatos utasításokat az Azure SQL Serverhez való kapcsolódáshoz és az SQL-szkript futtatásához. 

    Ha az ügyfél számára nem engedélyezett az Azure SQL Server elérése, konfigurálnia kell az Azure SQL Server tűzfalát, hogy engedélyezze a hozzáférést a gép számára (IP-cím). Az Azure SQL Server-tűzfal konfigurálásának lépéseit lásd [ebben a cikkben](../../sql-database/sql-database-configure-firewall-settings.md).

## <a name="create-a-data-factory"></a>Data factory létrehozása
Végrehajtotta az előfeltételeket. A következő módszerek egyikével hozhat létre egy adatelőállítót: Az oktatóanyag elvégzéséhez kattintson a felül lévő legördülő lista egyik lehetőségére, vagy az alábbi hivatkozásokra.     

* [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Az oktatóanyagban található adatfeldolgozási folyamat adatokat másol egy forrásadattárból egy céladattárba. A bemeneti adatokat nem alakítja át kimeneti adatok létrehozásához. Az adatok Azure Data Factoryval történő átalakításának útmutatásáért olvassa el [az adatok Hadoop-fürt segítségével történő átalakítására szolgáló első folyamat létrehozását ismertető oktatóanyagot](data-factory-build-your-first-pipeline.md).
> 
> Összefűzhet két tevékenységet (vagyis egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. Lásd [a Data Factorybeli ütemezést és végrehajtást](data-factory-scheduling-and-execution.md) ismertető cikket. 
