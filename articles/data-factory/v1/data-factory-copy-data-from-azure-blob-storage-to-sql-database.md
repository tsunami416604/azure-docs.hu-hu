---
title: Adatok másolása Blob Storage-ból az SQL Database – Azure |} A Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan lehet Azure Data Factory-folyamatot másolási tevékenység használatával adatok másolása Blob storage-ból az SQL database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 596a9e4e3e1d24bdcab561a7238548d418ac0581
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243573"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Oktatóanyag: Adatok másolása Blob Storage-ból az SQL Database Data Factory használatával
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [másolási tevékenység oktatóanyagát](../quickstart-create-data-factory-dot-net.md). 

Ebben az oktatóanyagban egy adat-előállítót egy folyamat az adatok másolása Blob storage-ból az SQL database és létrehozása.

A másolási tevékenység végzi az adatok továbbítását az Azure Data Factoryban. Egy olyan, globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és méretezhető módon másolja át az adatokat a különböző adattárak között. A Másolás tevékenységgel kapcsolatos részletekért tekintse meg a [Data Movement Activities](data-factory-data-movement-activities.md) (Adattovábbítási tevékenységek) című cikket.  

> [!NOTE]
> A Data Factory szolgáltatás részletes bemutatásáért lásd: a [az Azure Data Factory bemutatását](data-factory-introduction.md) cikk.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Az oktatóanyag előfeltételei
Ez az oktatóanyag elkezdéséhez az alábbi előfeltételekkel kell rendelkeznie:

* **Azure-előfizetés**.  Ha nem rendelkezik előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes próbafiókot. Tekintse meg a [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/) részleteivel.
* **Az Azure Storage-fiók**. A blob storage-ot használ egy **forrás** ebben az oktatóanyagban. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../../storage/common/storage-quickstart-create-account.md) ismertető cikket.
* **Azure SQL Database** Egy Azure SQL database-t használja egy **cél** ebben az oktatóanyagban. Ha nem rendelkezik Azure SQL-adatbázis, amelyet használhat az oktatóanyagban, lásd: [létrehozása és konfigurálása az Azure SQL Database](../../sql-database/sql-database-get-started.md) hozhat létre egyet.
* **Az SQL Server 2012 vagy 2014 vagy a Visual Studio 2013**. Használja az SQL Server Management Studio vagy a Visual Studio, és hozzon létre egy mintaadatbázist, és az eredmény adatok megtekintése az adatbázisban.  

## <a name="collect-blob-storage-account-name-and-key"></a>A blob storage-fiók neve és kulcsa gyűjtése
A fiók nevét és az Azure storage-fiók, ez az oktatóanyag elvégzéséhez fiókkulcs van szüksége. Jegyezze fel **fióknév** és **fiókkulcs** az Azure storage-fiók.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **minden szolgáltatás** a bal oldali menüben, és válassza **Tárfiókok**.

    ![Tallózás – Storage-fiókok](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. Az a **Tárfiókok** panelen válassza ki a **Azure storage-fiók** ebben az oktatóanyagban használni kívánt.
4. Válassza ki **hozzáférési kulcsok** mellett kapcsolni **beállítások**.
5. Kattintson a **másolási** (kép) gomb melletti **tárfióknevet** szöveg mezőbe, majd a Mentés és illessze be azt valahová (például: egy szövegfájlba).
6. Ismételje meg az előző lépésben másolja ki és jegyezze fel a **key1**.

    ![Tárelérési kulcs](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Kattintva zárja be az egyik panelen sem aktív **X**.

## <a name="collect-sql-server-database-user-names"></a>Az SQL server, a database, a felhasználói neveket gyűjtése
Szüksége lesz az Azure SQL server, az adatbázis és a felhasználó ebben az oktatóanyagban annak nevét. Jegyezze fel a neveket **kiszolgáló**, **adatbázis**, és **felhasználói** az Azure SQL Database.

1. Az a **az Azure portal**, kattintson a **minden szolgáltatás** a bal oldalon, majd válassza a **SQL-adatbázisok**.
2. Az a **SQL-adatbázisok panelen**, jelölje be a **adatbázis** , amelyet ebben az oktatóanyagban használni szeretne. Jegyezze fel a **adatbázisnév**.  
3. Az a **SQL-adatbázis** panelen kattintson a **tulajdonságok** alatt **beállítások**.
4. Jegyezze fel a tartozó értékeket **kiszolgálónév** és **KISZOLGÁLÓI rendszergazdai BEJELENTKEZÉSSEL**.
5. Kattintva zárja be az egyik panelen sem aktív **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>SQL server elérését az Azure-szolgáltatások engedélyezése
Ügyeljen arra, hogy **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** bekapcsolva beállítás **ON** az Azure SQL Serverhez, hogy a Data Factory szolgáltatás tudjon férni az Azure SQL Serverhez. A beállítás ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

1. Kattintson a **minden szolgáltatás** hubra a bal oldalon, majd a **SQL Server-kiszolgálók**.
2. Válassza ki a kiszolgálót, és kattintson a **BEÁLLÍTÁSOK** területen a **Tűzfal** elemre.
3. A **Tűzfalbeállítások** panelen kattintson a **BE** kapcsolóra az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás mellett.
4. Kattintva zárja be az egyik panelen sem aktív **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>A Blob Storage és SQL Database előkészítése
Készítse elő az Azure blob storage és Azure SQL database-t az oktatóanyaghoz a következő lépések végrehajtásával:  

1. Indítsa el a Jegyzettömböt. Másolja az alábbi szöveget, és mentse **emp.txt** való **C:\ADFGetStarted** mappába a merevlemezen.

    ```
    John, Doe
    Jane, Doe
    ```
2. Az [Azure Storage Explorer](http://storageexplorer.com/) vagy egy hasonló eszköz használatával hozza létre az **adftutorial** tárolót, és töltse fel az **emp.txt** fájlt a tárolóba.

    ![Az Azure Storage Explorerben. Adatok másolása Blob storage-ból SQL-adatbázis](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
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

    **Ha rendelkezik az SQL Server 2012 vagy 2014 van telepítve a számítógépen:** kövesse az utasításokat [Azure SQL Database szolgáltatás felügyelete az SQL Server Management Studióval](../../sql-database/sql-database-manage-azure-ssms.md) az Azure SQL-kiszolgálóhoz csatlakozhat, és futtassa az SQL-parancsfájlt. 

    Ha az ügyfél számára nem engedélyezett az Azure SQL Server elérése, konfigurálnia kell az Azure SQL Server tűzfalát, hogy engedélyezze a hozzáférést a gép számára (IP-cím). Az Azure SQL Server-tűzfal konfigurálásának lépéseit lásd [ebben a cikkben](../../sql-database/sql-database-configure-firewall-settings.md).

## <a name="create-a-data-factory"></a>Data factory létrehozása
Teljesítette az előfeltételeket. Létrehozhat egy adat-előállítót a következő módszerek egyikével. Kattintson a beállítások a legördülő lista tetején, vagy az alábbi hivatkozások az oktatóanyag elvégzéséhez.     

* [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
* [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Az oktatóanyagban található adatfeldolgozási folyamat adatokat másol egy forrásadattárból egy céladattárba. A bemeneti adatokat nem alakítja át kimeneti adatok létrehozásához. Az adatok Azure Data Factoryval történő átalakításának útmutatásáért olvassa el [az adatok Hadoop-fürt segítségével történő átalakítására szolgáló első folyamat létrehozását ismertető oktatóanyagot](data-factory-build-your-first-pipeline.md).
> 
> Összefűzhet két tevékenységet (vagyis egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. Lásd [a Data Factorybeli ütemezést és végrehajtást](data-factory-scheduling-and-execution.md) ismertető cikket. 
