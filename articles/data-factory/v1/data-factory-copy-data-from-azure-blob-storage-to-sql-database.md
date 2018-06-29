---
title: Adatok másolása SQL adatbázis - Azure Blob Storage |} Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan lehet egy Azure Data Factory-folyamathoz másolási tevékenység segítségével adatok másolása az Blob-tároló az SQL-adatbázis.
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
ms.openlocfilehash: 4538e5b49b161f22ba6d5979234786a58cae5783
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047726"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Oktatóanyag: Adatok másolása Blob-tároló az SQL Database Data Factory használatával
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
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [másolási tevékenység oktatóanyag](../quickstart-create-data-factory-dot-net.md). 

Ebben az oktatóanyagban létrehoz egy adat-előállító és a Blob-tároló az SQL database-adatok másolása.

A másolási tevékenység végzi az adatok továbbítását az Azure Data Factoryban. Egy olyan, globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és méretezhető módon másolja át az adatokat a különböző adattárak között. A Másolás tevékenységgel kapcsolatos részletekért tekintse meg a [Data Movement Activities](data-factory-data-movement-activities.md) (Adattovábbítási tevékenységek) című cikket.  

> [!NOTE]
> A részletes áttekintést nyújt a Data Factory szolgáltatásnak, tekintse meg a [Bevezetés az Azure Data Factory](data-factory-introduction.md) cikk.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Az oktatóanyag előfeltételei
Ez az oktatóanyag megkezdése előtt rendelkeznie kell a következő előfeltételek teljesülését:

* **Azure-előfizetés**.  Ha nem rendelkezik előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes próbafiókot. Tekintse meg a [ingyenes](http://azure.microsoft.com/pricing/free-trial/) cikkben alább.
* **Azure Storage-fiók**. A blob Storage tárolót használja a **forrás** ebben az oktatóanyagban az adattároló. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket.
* **Azure SQL Database** Azure SQL-adatbázis, használja a **cél** ebben az oktatóanyagban az adattároló. Ha még nem rendelkezik, amelyek az oktatóanyag, lásd: Azure SQL-adatbázis [létrehozása és konfigurálása az Azure SQL Database](../../sql-database/sql-database-get-started.md) kattintva létrehozhat egyet.
* **Az SQL Server 2012 vagy 2014 vagy a Visual Studio 2013**. Használja az SQL Server Management Studio vagy Visual Studio, és egy minta-adatbázis létrehozásához, és az eredmény adatok megtekintéséhez az adatbázisban.  

## <a name="collect-blob-storage-account-name-and-key"></a>A blob storage fióknevet és kulcsot gyűjtése
A fiók nevét és a fiók kulcsát az Azure storage-fiók, ez az oktatóanyag elvégzéséhez szükséges. Jegyezze fel **fióknév** és **fiókkulcs** az Azure-tárfiókot.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Kattintson a **minden szolgáltatás** a bal oldali menüre, majd válassza a **Tárfiókok**.

    ![Tallózás - Storage-fiókok](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. Az a **Tárfiókok** panelen válassza a **Azure storage-fiók** ebben az oktatóanyagban használni kívánt.
4. Válassza ki **hívóbetűk** hivatkozásra **beállítások**.
5. Kattintson a **másolási** (kép) gomb melletti **tárfióknév** szöveg mezőbe, a Mentés és illessze be azt valahol (például: a fájlt).
6. Másolja, és jegyezze fel az előző lépés megismétlésével a **key1**.

    ![Tárelérési kulcs](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Zárja be a paneleken kattintva **X**.

## <a name="collect-sql-server-database-user-names"></a>SQL server, az adatbázis, a felhasználói neveket gyűjtése
Azure SQL server, az adatbázis és a felhasználó az oktatóanyag elvégzéséhez nevei van szüksége. Jegyezze fel a neveket **server**, **adatbázis**, és **felhasználói** az Azure SQL adatbázishoz.

1. Az a **Azure-portálon**, kattintson a **minden szolgáltatás** a bal oldalon válassza ki **SQL-adatbázisok**.
2. Az a **SQL adatbázisok panel**, jelölje be a **adatbázis** ebben az oktatóanyagban használni kívánt. Jegyezze fel a **adatbázisnév**.  
3. Az a **SQL-adatbázis** panelen kattintson a **tulajdonságok** alatt **beállítások**.
4. Jegyezze fel a értékeinek **kiszolgálónév** és **kiszolgáló-rendszergazdai bejelentkezés**.
5. Zárja be a paneleken kattintva **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>SQL-kiszolgálóhoz való hozzáféréshez Azure-szolgáltatások engedélyezése
Győződjön meg arról, hogy **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás kikapcsolt **ON** az Azure SQL-kiszolgáló, hogy a Data Factory szolgáltatás hozzá tud férni az Azure SQL-kiszolgálót. A beállítás ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

1. Kattintson a **minden szolgáltatás** a bal oldalon, majd kattintson a központ **SQL Server-kiszolgálók**.
2. Válassza ki a kiszolgálót, és kattintson a **BEÁLLÍTÁSOK** területen a **Tűzfal** elemre.
3. A **Tűzfalbeállítások** panelen kattintson a **BE** kapcsolóra az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás mellett.
4. Zárja be a paneleken kattintva **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>A Blob Storage és az SQL-adatbázis előkészítése
Most, az Azure blob storage és előkészítése Azure SQL-adatbázis az oktatóanyag az alábbi lépések elvégzésével:  

1. Indítsa el a Jegyzettömböt. Másolja az alábbi szöveget, és mentse a fájt **emp.txt** való **C:\ADFGetStarted** mappa a merevlemezen.

    ```
    John, Doe
    Jane, Doe
    ```
2. Az [Azure Storage Explorer](http://storageexplorer.com/) vagy egy hasonló eszköz használatával hozza létre az **adftutorial** tárolót, és töltse fel az **emp.txt** fájlt a tárolóba.

    ![Az Azure Storage Explorer. A Blob storage az SQL database-adatok másolása](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
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

    **Ha az SQL Server 2012 vagy 2014 a számítógépen telepítve van:** kövesse az utasításokat [kezelése az Azure SQL Database SQL Server Management Studio használatával](../../sql-database/sql-database-manage-azure-ssms.md) csatlakozás az Azure SQL-kiszolgálóhoz, és az SQL-parancsfájl futtatása. 

    Ha az ügyfél számára nem engedélyezett az Azure SQL Server elérése, konfigurálnia kell az Azure SQL Server tűzfalát, hogy engedélyezze a hozzáférést a gép számára (IP-cím). Az Azure SQL Server-tűzfal konfigurálásának lépéseit lásd [ebben a cikkben](../../sql-database/sql-database-configure-firewall-settings.md).

## <a name="create-a-data-factory"></a>Data factory létrehozása
Az Előfeltételek befejeződött. Az alábbi módok egyikével adat-előállító hozhat létre. Kattintson a legördülő listából válassza ki a felső vagy az alábbi hivatkozások a beállítások az oktatóanyag elvégzéséhez.     

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
