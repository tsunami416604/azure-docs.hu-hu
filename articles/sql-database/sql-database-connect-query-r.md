---
title: Az R használata Machine Learning Services lekérdezéséhez
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ebből a cikkből megtudhatja, hogyan használhatja az R-szkripteket Azure SQL Database Machine Learning Services egy Azure SQL Database-adatbázishoz való kapcsolódáshoz és a Transact-SQL-utasítások használatával történő lekérdezéséhez.
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
ms.openlocfilehash: a54b538247f81ea3bb0ea70a2af374158bd9e2ff
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826972"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Rövid útmutató: az R és a Machine Learning Services használata Azure SQL Database-adatbázisok lekérdezéséhez (előzetes verzió)

Ez a rövid útmutató bemutatja, hogyan használható az [R](https://www.r-project.org/) és a Machine learning Services egy Azure SQL Database-adatbázishoz való kapcsolódáshoz, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni. A Machine Learning Services az adatbázis-alapú R-parancsfájlok végrehajtásához használt Azure SQL Database szolgáltatása. További információ: [Azure SQL Database Machine learning Services az R (előzetes verzió)](sql-database-machine-learning-services-overview.md)szolgáltatással.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Azure SQL-adatbázis. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist Azure SQL Databaseban:

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
  | Létrehozás| [Portál](sql-database-single-database-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md) |
  | Adatok betöltése | Adventure Works betöltve |
  |||

  > [!NOTE]
  > Az R-vel Azure SQL Database Machine Learning Services előnézetében a felügyelt példányok központi telepítésének lehetősége nem támogatott.

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

- Machine Learning Services (R) engedélyezve. A nyilvános előzetes verzióban a Microsoft bevezeti Önt, és lehetővé teszi a gépi tanulást a meglévő vagy az új adatbázishoz. Kövesse a regisztráció az [előzetes](sql-database-machine-learning-services-overview.md#signup)verzióra című témakör lépéseit.

- A legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Az R-szkripteket más adatbázis-kezelő vagy lekérdezési eszközök használatával is futtathatja, de ebben a rövid útmutatóban a SSMS-t fogja használni.

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok** vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egyetlen adatbázishoz vagy a felügyelt példányhoz tartozó **gazdagép** melletti teljes kiszolgálónévhez. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

## <a name="create-code-to-query-your-sql-database"></a>Kód létrehozása az SQL-adatbázis lekérdezéséhez

1. Nyissa meg az **SQL Server Management Studiót**, és csatlakozzon az SQL-adatbázishoz.

   Ha segítségre van szüksége a csatlakozáshoz, tekintse meg [Az Azure SQL Database-adatbázisok csatlakoztatásához és lekérdezéséhez SQL Server Management Studio használata](sql-database-connect-query-ssms.md)című témakört.

1. Adja át a teljes R-szkriptet a [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárásnak.

   A parancsfájl a `@script` argumentummal halad át. A `@script` argumentumban szereplő összes értéknek érvényes R-kódnak kell lennie.
   
   >[!IMPORTANT]
   >Az ebben a példában szereplő kód a minta AdventureWorksLT-adatait használja, amelyeket az adatbázis létrehozásakor választhat forrásként. Ha az adatbázis különböző adatokkal rendelkezik, a SELECT lekérdezésben használja a saját adatbázisában lévő táblákat. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Ha hibába ütközik, előfordulhat, hogy a Machine Learning Services (with R) nyilvános előzetes verziója nincs engedélyezve az SQL-adatbázishoz. Lásd a fenti [előfeltételeket](#prerequisites) .

## <a name="run-the-code"></a>A kód futtatása

1. Hajtsa végre az [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárást.

1. Győződjön meg arról, hogy az **üzenetek** ablakban a 20 legfontosabb kategória/termék sorok lesznek visszaadva.

## <a name="next-steps"></a>További lépések

- [Az első Azure SQL-adatbázis megtervezése](sql-database-design-first-database.md)
- [Azure SQL Database Machine Learning Services (R)](sql-database-machine-learning-services-overview.md)
- [Egyszerű R-parancsfájlok létrehozása és futtatása Azure SQL Database Machine Learning Servicesban (előzetes verzió)](sql-database-quickstart-r-create-script.md)
- [Speciális R függvények írása a Azure SQL Database Machine Learning Services használatával (előzetes verzió)](sql-database-machine-learning-services-functions.md)
