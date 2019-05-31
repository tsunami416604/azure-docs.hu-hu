---
title: Az R használata Azure SQL Database-adatbázis lekérdezéséhez
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ez a cikk bemutatja, hogyan az R-szkriptek használatával egy Azure SQL Database-adatbázishoz csatlakozhat, és végre lekérdezést Transact-SQL-utasításokkal.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: 1d4b17cf1e0349bf877c676cb4e591fc20ad4113
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416363"
---
# <a name="quickstart-use-r-to-query-an-azure-sql-database-preview"></a>Gyors útmutató: Az R használata Azure SQL Database-adatbázis (előzetes verzió) lekérdezéséhez

 Ez a rövid útmutató azt ismerteti, hogyan használhatja [R](https://www.r-project.org/) a Machine Learning-szolgáltatások csatlakoztatása egy Azure SQL Database-adatbázishoz, és Transact-SQL-utasítások használatával adatokat lekérdezni. Machine Learning-szolgáltatások az Azure SQL Database-adatbázis-R-parancsfájlok futtatásához használt egy funkciója. További információkért lásd: [Azure SQL Database Machine Learning Services (előzetes verzió) R-rel](sql-database-machine-learning-services-overview.md).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Azure SQL-adatbázis. Az alábbi rövid útmutatókban hozhat létre, és válassza az Azure SQL Database egy adatbázis is használja:

<!-- Managed instance is not supported during the preview
  || Single database | Managed instance |
  |:--- |:--- |:---|
  | Create| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configure | [Server-level IP firewall rule](sql-database-server-level-firewall-rule.md) | [Connectivity from a VM](sql-database-managed-instance-configure-vm.md) |
  ||| [Connectivity from on-site](sql-database-managed-instance-configure-p2s.md) |
  | Load data | Adventure Works loaded per quickstart | [Restore Wide World Importers](sql-database-managed-instance-get-started-restore.md) |
  ||| Restore or import Adventure Works from [BACPAC](sql-database-import.md) file from [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) |
  |||
-->

  || Önálló adatbázis |
  |:--- |:--- |
  | Hozzon létre| [Portál](sql-database-single-database-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Konfigurálás | [kiszolgálószintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md) |
  | Adatok betöltése | Az Adventure Works betöltött száma a rövid útmutató |
  |||

  > [!NOTE]
  > A felügyelt példány üzembe helyezési lehetőség az Azure SQL Database Machine Learning-szolgáltatások az r nyelv az előzetes verzióban nem támogatott.

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

- Machine Learning-szolgáltatások (az r nyelv) engedélyezve van. A nyilvános előzetes verzió ideje alatt a Microsoft is megjelenik majd, és engedélyezze a gépi tanulás a meglévő vagy új adatbázis számára. Kövesse a [regisztrálni az előzetes verzióra](sql-database-machine-learning-services-overview.md#signup).

- A legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Más adatbázis-kezelő vagy a lekérdezési eszközökkel R-szkriptek futtatása, de ez a rövid útmutató az ssms-t fogja használni.

## <a name="get-sql-server-connection-information"></a>Az SQL server-kapcsolati adatok lekéréséhez

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információkat kaphat. A következő eljárások szüksége a kiszolgáló teljes nevét vagy a gazdagépnév, az adatbázis neve és a bejelentkezési adatait.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Keresse meg a **SQL-adatbázisok** vagy **SQL felügyelt példányai** lapot.

3. A a **áttekintése** lapon, tekintse át a teljes kiszolgálónevet melletti **kiszolgálónév** egy önálló adatbázis vagy a kiszolgáló teljes neve melletti **gazdagép** számára egy felügyelt a példány. Másolja ki a kiszolgáló nevét vagy az állomásnevet, rámutatnak, és válassza a **másolási** ikonra.

## <a name="create-code-to-query-your-sql-database"></a>Az SQL-adatbázis lekérdezéséhez kód létrehozása

1. Nyissa meg az **SQL Server Management Studiót**, és csatlakozzon az SQL-adatbázishoz.

   Ha a kapcsolódás segítségre van szüksége, tekintse meg [a rövid útmutató: SQL Server Management Studio használatával csatlakozhat, és az Azure SQL Database-adatbázis lekérdezéséhez](sql-database-connect-query-ssms.md).

1. Adja át a teljes R-szkriptet a [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárást.

   A parancsfájl keresztülmegy a `@script` argumentum. Minden belül a `@script` argumentum érvényes R-kódot kell lennie.
   
   >[!IMPORTANT]
   >Ebben a példában a kódot az AdventureWorksLT mintaadatokat, amely forrásként is választja, az adatbázis létrehozásakor használ. Ha az adatbázis különböző adatokat, használja a saját adatbázis tábláinak a SELECT-lekérdezésben. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Ha hibába ütközik, előfordulhat, hogy a Machine Learning Services (with R) nyilvános előzetes verziója nincs engedélyezve az SQL-adatbázishoz. Lásd: [Előfeltételek](#prerequisites) felett.

## <a name="run-the-code"></a>A kód futtatása

1. Hajtsa végre a [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárást.

1. Győződjön meg arról, hogy az első 20 kategória/Product sort ad vissza a a **üzenetek** ablak.

## <a name="next-steps"></a>További lépések

- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)
- [Az Azure SQL Database Machine Learning-szolgáltatások (az R)](sql-database-machine-learning-services-overview.md)
- [Hozzon létre, és egyszerű R-szkriptek futtatása az Azure SQL Database, Machine Learning Services (előzetes verzió)](sql-database-quickstart-r-create-script.md)
- [Az Azure SQL Database, Machine Learning-Szolgáltatásokoz (előzetes verzió) használatával speciális R-függvények írása](sql-database-machine-learning-services-functions.md)
