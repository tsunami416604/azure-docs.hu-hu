<properties
   pageTitle="Az Azure SQL Data Warehouse lekérdezése (sqlcmd) | Microsoft Azure"
   description="Az Azure SQL Data Warehouse lekérdezése az sqlcmd parancssori segédprogram használatával."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Az Azure SQL Data Warehouse lekérdezése (sqlcmd)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Ez az útmutató az [sqlcmd][] parancssori segédprogramot használja az Azure SQL Data Warehouse lekérdezéséhez.  

## 1. Kapcsolódás

Az [sqlcmd][] használatának megkezdéséhez nyissa meg a parancssort, és írja be az **sqlcmd** kifejezést, majd a saját SQL Data Warehouse-adatbázisának kapcsolati karakterláncát. A kapcsolati karakterláncban a következő paraméterekre van szükség:

+ **Server (-S):** A kiszolgáló neve `<`kiszolgálónév`>`.database.windows.net formátumban.
+ **Database (-d):** Az adatbázis neve.
+ **User (-U):** A kiszolgálói felhasználó neve `<`felhasználó formátumban.`>`
+ **Password (-P):** A felhasználóhoz tartozó jelszó.
+ **Enable Quoted Identifiers (-I):** A SQL Data Warehouse-példányokhoz való csatlakozáshoz engedélyezni kell a határolójeles azonosítókat.

A kapcsolati karakterlánc például a következőképpen nézhet ki:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

> [AZURE.NOTE] Az -I kapcsoló, amely a határolójeles azonosítókat engedélyezi, jelenleg szükséges az SQL Data Warehouse-hoz való kapcsolódáshoz.

## 2. Lekérdezés

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

## Következő lépések

Az sqlcmd-ben elérhető további lehetőségek részleteit az [sqlcmd dokumentációjában][sqlcmd] tekintheti meg.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure Portal]: https://portal.azure.com

<!--Other Web references-->



<!--HONumber=sep16_HO1-->


