---
title: Adatbázis lekérdezése az R és Azure SQL Database Machine Learning Services (előzetes verzió) használatával
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ebből a cikkből megtudhatja, hogyan használható az R-szkriptek Azure SQL Database Machine Learning Services egy adatbázishoz való kapcsolódáshoz Azure SQL Database és a Transact-SQL-utasítások használatával történő lekérdezéséhez.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: sqldbrb=2 
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, sstein
manager: cgronlun
ms.date: 05/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 3a939c816cac44ed85802ecfa591564effc1ee73
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91328833"
---
# <a name="quickstart-use-r-with-azure-sql-database-machine-learning-services-preview-to-query-a-database"></a>Rövid útmutató: az R használata Azure SQL Database Machine Learning Services (előzetes verzió) használatával egy adatbázis lekérdezéséhez 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebben a rövid útmutatóban az R és Azure SQL Database Machine Learning Services segítségével csatlakozhat egy adatbázishoz a Azure SQL Database-ben, és T-SQL-utasítások használatával kérdez le adatokat.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy [Azure SQL Database](single-database-create-quickstart.md)
- Az R-t engedélyező [Machine learning Services](machine-learning-services-overview.md) .
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> A cikkben található parancsfájlok az **Adventure Works** -adatbázis használatára íródnak.

Az R-vel Machine Learning Services az adatbázisbeli R-parancsfájlok futtatásához használt Azure SQL Database funkció. További információ: [R-projekt](https://www.r-project.org/).

## <a name="get-the-sql-server-connection-information"></a>A SQL Server-kapcsolatok adatainak beolvasása

A Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok lekérése. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok**  vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett Azure SQL Database vagy a **gazdagép** melletti teljes kiszolgálónevet az Azure SQL felügyelt példányának felügyelt példánya számára. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

## <a name="create-code-to-query-your-database"></a>Kód létrehozása az adatbázis lekérdezéséhez

1. Nyissa meg **SQL Server Management Studio** és kapcsolódjon az adatbázishoz.

   Ha segítségre van szüksége a csatlakozáshoz, tekintse meg a rövid útmutató [: SQL Server Management Studio használata az adatbázisok Azure SQL Database-ban való csatlakoztatásához és lekérdezéséhez](connect-query-ssms.md).

1. Adja át a teljes R-szkriptet a [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárásnak.

   A szkript áthalad az `@script` argumentumon. Az argumentumon belül minden elemnek `@script` érvényes R-kódnak kell lennie.
   
   >[!IMPORTANT]
   >Az ebben a példában szereplő kód a minta AdventureWorksLT-adatait használja, amelyeket az adatbázis létrehozásakor választhat forrásként. Ha az adatbázis különböző adatokkal rendelkezik, a SELECT lekérdezésben használja a saját adatbázisában lévő táblákat. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Ha hibaüzenet jelenik meg, annak oka az lehet, hogy a (R) Machine Learning Services nyilvános előzetes verziója nincs engedélyezve az adatbázishoz. Lásd a fenti [előfeltételeket](#prerequisites) .

## <a name="run-the-code"></a>A kód futtatása

1. Hajtsa végre az [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárást.

1. Győződjön meg arról, hogy az **üzenetek** ablakban a 20 legfontosabb kategória/termék sorok lesznek visszaadva.

## <a name="next-steps"></a>További lépések

- [Az első adatbázis megtervezése Azure SQL Database](design-first-database-tutorial.md)
- [Azure SQL Database Machine Learning Services (R)](machine-learning-services-overview.md)
- [Egyszerű R-parancsfájlok létrehozása és futtatása Azure SQL Database Machine Learning Servicesban (előzetes verzió)](r-script-create-quickstart.md)
