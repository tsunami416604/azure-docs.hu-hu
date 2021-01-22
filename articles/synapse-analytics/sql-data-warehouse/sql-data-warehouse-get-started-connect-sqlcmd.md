---
title: Kapcsolat a Sqlcmd
description: A Sqlcmd parancssori segédprogrammal csatlakozhat, és lekérdezheti a szinapszis SQL-készletét.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3d1d8d3ce3afece5a979aadc27cd82dc7ddaf0d5
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676228"
---
# <a name="connect-to-sql-pool-in-azure-synapse-analytics-with-sqlcmd"></a>Kapcsolódás az SQL-készlethez az Azure szinapszis Analytics és a Sqlcmd használatával

> [!div class="op_single_selector"]
>
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Használja az [Sqlcmd] [Sqlcmd] parancssori segédprogramot egy SQL-készlethez való kapcsolódáshoz és lekérdezéshez.  

## <a name="1-connect"></a>1. kapcsolat

A [Sqlcmd] [Sqlcmd] első lépéseihez nyissa meg a parancssort, és írja be a **Sqlcmd** , majd az SQL-készlethez tartozó kapcsolódási karakterláncot. A kapcsolati sztringben a következő paraméterekre van szükség:

* **Server (-S):** A kiszolgáló neve `<`kiszolgálónév`>`.database.windows.net formátumban.
* **Adatbázis (-d):** SQL-készlet neve.
* **Idézett azonosítók engedélyezése (-I):** Az idézőjelek közé tartozó azonosítókat engedélyezni kell egy SQL Pool-példányhoz való kapcsolódáshoz.

Az SQL Server-hitelesítés használatához meg kell adnia a felhasználónév/jelszó paramétereit:

* **Felhasználó (-U):** A kiszolgáló felhasználója az űrlap `<` felhasználója`>`
* **Password (-P):** A felhasználóhoz tartozó jelszó.

A kapcsolati sztring például a következőképpen nézhet ki:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Az Azure Active Directory beépített hitelesítés használatához meg kell adnia az Azure Active Directory paramétereit:

* **Azure Active Directory Authentication (-G):** az Azure Active Directory használata a hitelesítéshez

A kapcsolati sztring például a következőképpen nézhet ki:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Az Active Directory használatával történő hitelesítéshez [engedélyeznie kell az Azure Active Directory-hitelesítést](sql-data-warehouse-authentication.md).

## <a name="2-query"></a>2. lekérdezés

A kapcsolódás után kiadhatók a példányon a támogatott Transact-SQL utasítások.  Ebben a példában a lekérdezések elküldése interaktív módban történik.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

A következő példák bemutatják, hogyan lehet a lekérdezéseket kötegelt módban futtatni a -Q kapcsoló használatával vagy az SQL átirányításával az sqlcmd-re.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Következő lépések

A Sqlcmd-ben elérhető beállítások részletes ismertetését lásd: [Sqlcmd-dokumentáció](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).