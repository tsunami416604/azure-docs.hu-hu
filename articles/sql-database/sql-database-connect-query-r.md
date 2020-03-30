---
title: Adatbázis lekérdezése az R és a Machine Learning Services használatával (előzetes verzió)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ez a cikk bemutatja, hogyan használhat R-parancsfájlt az Azure SQL Database Machine Learning Services használatával egy Azure SQL-adatbázishoz való csatlakozáshoz, és lekérdezheti azt transact-SQL utasítások használatával.
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
ms.openlocfilehash: 7103afc29e4021d950d9a3634b190f4439ecfe8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768518"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Rövid útmutató: Az R és a Machine Learning Services használata Azure SQL-adatbázis lekérdezéséhez (előzetes verzió)

Ebben a rövid útmutatóban az R és a Machine Learning Services használatával csatlakozhat egy Azure SQL-adatbázishoz, és t-SQL-utasítások használatával adatokat kérdezhetsz.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure SQL-adatbázis](sql-database-single-database-get-started.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) engedélyezve van az R szolgáltatással. [Regisztráljon az előzetes verzióra](sql-database-machine-learning-services-overview.md#signup).
- [SQL Server Felügyeleti stúdió](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> A cikkben szereplő parancsfájlok a **Kalandorbolt-adatbázis** használatára íródnak.

> [!NOTE]
> A nyilvános előzetes verzió során a Microsoft befogja önt, és engedélyezi a gépi tanulást a meglévő vagy az új adatbázishoz, azonban a felügyelt példány telepítési lehetősége jelenleg nem támogatott.

A Machine Learning Services in R az Azure SQL-adatbázis egyik szolgáltatása, amely az adatbázison belüli R-parancsfájlok végrehajtásához használatos. További információt az [R projekt című](https://www.r-project.org/)témakörben talál.

## <a name="get-sql-server-connection-information"></a>SQL-kiszolgálókapcsolati adatok beszerezése

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információk beszerezése. A közelgő eljárásokhoz szüksége lesz a teljesen minősített kiszolgáló- vagy állomásnévre, az adatbázis nevére és bejelentkezési adataira.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Keresse meg az **SQL-adatbázisok** vagy az **SQL felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a **kiszolgáló neve** melletti teljesen minősített kiszolgálónevet egyetlen adatbázishoz, vagy a teljesen minősített kiszolgálónevet a felügyelt példány **gazdagépe** mellett. A kiszolgáló vagy az állomásnév másolásához mutasson rá, és válassza a **Másolás ikont.**

## <a name="create-code-to-query-your-sql-database"></a>Kód létrehozása az SQL-adatbázis lekérdezéséhez

1. Nyissa meg **az SQL Server Management Studio alkalmazást,** és csatlakozzon az SQL-adatbázishoz.

   Ha segítségre van szüksége a csatlakozáshoz, [olvassa el a rövid útmutató: Az SQL Server Management Studio használata Azure SQL-adatbázis csatlakoztatásához és lekérdezéséhez című témakört.](sql-database-connect-query-ssms.md)

1. Adja át a teljes R-parancsfájlt a [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárásnak.

   A parancsfájl áthalad `@script` az argumentumon. Az argumentumon belül minden `@script` érvényes R-kódnak kell lennie.
   
   >[!IMPORTANT]
   >A példában szereplő kód az AdventureWorksLT-minta adatait használja, amelyeket az adatbázis létrehozásakor forrásként választhat. Ha az adatbázis különböző adatokat használ, használjon saját adatbázistábláit a SELECT lekérdezésben. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Ha hibába ütközik, előfordulhat, hogy a Machine Learning Services (with R) nyilvános előzetes verziója nincs engedélyezve az SQL-adatbázishoz. Lásd: Fenti [előfeltételek.](#prerequisites)

## <a name="run-the-code"></a>A kód futtatása

1. Hajtsa végre a [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárást.

1. Ellenőrizze, hogy a 20 legfontosabb kategória/termék sor visszakerül-e az **Üzenetek** ablakban.

## <a name="next-steps"></a>További lépések

- [Az első Azure SQL-adatbázis megtervezése](sql-database-design-first-database.md)
- [Azure SQL Database Machine Learning Services (R-rel)](sql-database-machine-learning-services-overview.md)
- [Egyszerű R-parancsfájlok létrehozása és futtatása az Azure SQL Database Machine Learning Services szolgáltatásban (előzetes verzió)](sql-database-quickstart-r-create-script.md)
- [Speciális R-függvények írása az Azure SQL Database-ben a Machine Learning Services használatával (előzetes verzió)](sql-database-machine-learning-services-functions.md)
