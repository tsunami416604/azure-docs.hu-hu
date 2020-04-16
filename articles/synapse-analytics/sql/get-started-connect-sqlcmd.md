---
title: Csatlakozás a Synapse SQL-hez az sqlcmd használatával
description: Az sqlcmd parancssori segédprogrammal igény szerint csatlakozhat az SQL-hez és az SQL-készlethez, és lekérdezheti azsql-készletet.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 8ff9034e6c31c8d95e862570e3962990dfec8442
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423753"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Csatlakozás a Synapse SQL-hez az sqlcmd segítségével

> [!div class="op_single_selector"]
>
> * [Azure Data Studio (előzetes verzió)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Az [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) parancssori segédprogrammal igény szerinti SQL-hez (előzetes verzió) és SQL-készlethez csatlakozhat és lekérdezhet a Synapse SQL-en belül.  

## <a name="1-connect"></a>1. Csatlakozás
Az [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)ismerkedéséhez nyissa meg a parancssort, és írja be az **sqlcmd** parancsot, majd a Synapse SQL-adatbázis kapcsolati karakterláncát. A kapcsolati sztringben a következő paraméterekre van szükség:

* **Server (-S):** A kiszolgáló neve `<`kiszolgálónév`>`.database.windows.net formátumban.
* **Adatbázis (-d):** Adatbázis neve
* **Idézett azonosítók engedélyezése (-I):** A szinapszis SQL-példányhoz való csatlakozáshoz engedélyezni kell a idézett azonosítókat.

Az SQL Server-hitelesítés használatához hozzá kell adnia a felhasználónevet és a jelszót:

* **Felhasználó (-U):** Kiszolgáló felhasználója `<`a Felhasználó űrlapon`>`
* **Jelszó (-P):** A felhasználóhoz társított jelszó

A kapcsolati karakterlánc a következő példához hasonló lehet:

**SQL igény szerint**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**SQL-készlet**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Az Azure Active Directory beépített hitelesítés használatához meg kell adnia az Azure Active Directory paramétereit:

* **Azure Active Directory Authentication (-G):** az Azure Active Directory használata a hitelesítéshez

A kapcsolati karakterlánc a következő példákhoz hasonlóan nézhet ki:

**SQL igény szerint**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**SQL-készlet**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Az Active Directory használatával történő hitelesítéshez [engedélyeznie kell az Azure Active Directory-hitelesítést](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="2-query"></a>2. Lekérdezés

### <a name="use-sql-pool"></a>SQL-készlet használata

A kapcsolódás után kiadhatók a példányon a támogatott Transact-SQL utasítások.  Ebben a példában a lekérdezések interaktív módban kerülnek elküldésre:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Az SQL-készlet esetében az alábbi példák bemutatják, hogyan futtathatja a lekérdezéseket kötegelt módban a -Q kapcsolóval, vagy az SQL sqlcmd-re történő csővezetékezésével:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-sql-on-demand"></a>Sql igény szerinti használata

A csatlakozás után bármely támogatott [Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL) utasítást kiadhat a példányellen.  A következő példában a lekérdezések interaktív módban kerülnek elküldésre:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

Az igény szerinti SQL-ben az alábbi példák bemutatják, hogyan futtathatja a lekérdezéseket kötegelt módban a -Q kapcsolóval, vagy az SQL sqlcmd-re történő csővezetékével:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>További lépések

Az sqlcmd-beállításokról további információt az [sqlcmd dokumentációjában](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)talál.
