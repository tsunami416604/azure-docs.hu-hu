---
title: Kapcsolódás a szinapszis SQL-hez a Sqlcmd használatával
description: Az SQL on-demand (előzetes verzió) és az SQL-készlet lekérdezéséhez használja a Sqlcmd parancssori segédprogramot.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 7ccb30cdd77e511572147a0b0f7287f931a45df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82186838"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Kapcsolódás a szinapszis SQL-hez a Sqlcmd használatával

> [!div class="op_single_selector"]
>
> * [Azure Data Studio (előzetes verzió)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

A [Sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) parancssori segédprogrammal kapcsolódhat az SQL on-demand (előzetes verzió) és az SQL-készlet lekérdezéséhez a szinapszis SQL-en belül.  

## <a name="1-connect"></a>1. kapcsolat
A [Sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)megkezdéséhez nyissa meg a parancssort, és írja be a **Sqlcmd** , majd a szinapszis SQL Database-hez tartozó kapcsolódási karakterláncot. A kapcsolati sztringben a következő paraméterekre van szükség:

* **Server (-S):** A kiszolgáló neve `<`kiszolgálónév`>`.database.windows.net formátumban.
* **Adatbázis (-d):** Adatbázis neve
* **Idézett azonosítók engedélyezése (-I):** Az idézőjelek közé tartozó azonosítókat engedélyezni kell a szinapszis SQL-példányhoz való kapcsolódáshoz

SQL Server hitelesítés használatához hozzá kell adnia a felhasználónevet és a jelszó paramétereit:

* **Felhasználó (-U):** A kiszolgáló felhasználója az `<`űrlap felhasználója`>`
* **Jelszó (-P):** A felhasználóhoz tartozó jelszó

A kapcsolatok karakterlánca a következő példához hasonló lehet:

**Igény szerinti SQL**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**SQL-készlet**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Az Azure Active Directory beépített hitelesítés használatához meg kell adnia az Azure Active Directory paramétereit:

* **Azure Active Directory Authentication (-G):** az Azure Active Directory használata a hitelesítéshez

A kapcsolatok karakterlánca a következő példákhoz hasonlóan jelenhet meg:

**Igény szerinti SQL**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**SQL-készlet**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Az Active Directory használatával történő hitelesítéshez [engedélyeznie kell az Azure Active Directory-hitelesítést](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="2-query"></a>2. lekérdezés

### <a name="use-sql-pool"></a>SQL-készlet használata

A csatlakoztatást követően bármilyen támogatott [Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL) utasítást ki lehet állítani a példányon. Ebben a példában a lekérdezések interaktív módban lesznek elküldve:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Az SQL-készlet esetében az alábbi példák bemutatják, hogyan futtathat lekérdezéseket batch módban a-Q kapcsolóval vagy az SQL Sqlcmd:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-sql-on-demand"></a>Az igény szerinti SQL használata

A csatlakozás után bármely támogatott [Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL) utasítást kiállíthat a példányon.  A következő példában a lekérdezések interaktív módban lesznek elküldve:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

Az SQL igény szerinti használatához az alábbi példák bemutatják, hogyan futtathat lekérdezéseket batch módban a-Q kapcsolóval vagy az SQL Sqlcmd:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>További lépések

A Sqlcmd beállításaival kapcsolatos további információkért tekintse meg a [Sqlcmd dokumentációját](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
