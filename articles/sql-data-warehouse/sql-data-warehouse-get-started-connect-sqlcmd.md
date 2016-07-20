<properties
   pageTitle="Első lépések: Csatlakozás az Azure SQL Data Warehouse-hoz | Microsoft Azure"
   description="Az SQL Data Warehouse-kapcsolat létrehozásának és a lekérdezések futtatásának első lépései."
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
   ms.date="05/16/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Csatlakozás és lekérdezés az SQLCMD használatával

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)


Ez a bemutató ismerteti, hogyan lehet néhány perc alatt csatlakozni az Azure SQL Data Warehouse-adatbázishoz, és adatokat lekérdezni onnan az sqlcmd.exe segédprogrammal. A bemutató keretében a következő lépéseket fogja végrehajtani:

+ Az előfeltételt jelentő szoftverek telepítése
+ Csatlakozás az AdventureWorksDW-mintaadatbázist tartalmazó adatbázishoz
+ Lekérdezés a mintaadatbázisból  

## Előfeltételek

+ Az [sqlcmd.exe][] letöltéséhez lásd: [Microsoft Command Line Utilities 11 for SQL Server][].

## A teljes Azure SQL-kiszolgálónév lekérdezése

Az adatbázishoz való csatlakozáshoz szükség van a kiszolgáló teljes nevére (***kiszolgálónév**.database.windows.net*), ami tartalmazza annak az adatbázisnak a nevét, amelyhez csatlakozni kíván.

1. Nyissa meg az [Azure portált][].
2. Keresse meg azt az adatbázist, amelyhez csatlakozni kíván.
3. Keresse meg a teljes kiszolgálónevet (a következő lépésekben szükség lesz rá):

![][1]


## Csatlakozás az SQL Data Warehouse-hoz az sqlcmd használatával

Amennyiben az SQL Data Warehouse egy adott példányához kíván csatlakozni az sqlcmd használatával, meg kell nyitnia a parancssort. Írja be az **sqlcmd** kifejezést, majd a saját SQL Data Warehouse adatbázisának kapcsolati karakterláncát. A kapcsolati karakterláncnak a következő paramétereket kell tartalmaznia:

+ **Server (-S):** A kiszolgáló neve `<`kiszolgálónév`>`.database.windows.net formátumban.
+ **Database (-d):** Az adatbázis neve.
+ **User (-U):** A kiszolgálói felhasználó neve `<`felhasználó formátumban.`>`
+ **Password (-P):** A felhasználóhoz tartozó jelszó.
+ **Enable Quoted Identifiers (-I):** A SQL Data Warehouse-példányokhoz való csatlakozáshoz engedélyezni kell a határolójeles azonosítókat.

Ezért ha csatlakozni kíván az SQL Data Warehouse-példányhoz, a következőt kell beírnia:

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## Mintalekérdezések futtatása

A kapcsolódás után kiadhatók a példányon a támogatott Transact-SQL utasítások.

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Az sqlcmd segédprogrammal kapcsolatban további információkat az [sqlcmd dokumentációjában olvashat][sqlcmd.exe].


## További lépések

Most, hogy képes csatlakozni és elvégezni a lekérdezéseket, próbáljon [csatlakozni a PowerBI-jal][].

A környezet Windows-hitelesítésre való konfigurálásához tekintse meg a [Csatlakozás az SQL Database vagy az SQL Data Warehouse szolgáltatáshoz Azure Active Directory-hitelesítéssel][] című cikket.

<!--Articles-->
[Csatlakozás az SQL Database vagy az SQL Data Warehouse szolgáltatáshoz Azure Active Directory-hitelesítéssel]: ../sql-data-warehouse/sql-data-warehouse-get-started-connect-aad-authentication.md
[csatlakozni a PowerBI-jal]: ./sql-data-warehouse-integrate-power-bi.md
[Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[SQLCMD]: ./sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other-->
[sqlcmd.exe]: https://msdn.microsoft.com/en-us/library/ms162773.aspx
[Microsoft Command Line Utilities 11 for SQL Server]: http://go.microsoft.com/fwlink/?LinkId=321501
[Azure portált]: https://portal.azure.com

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png



<!--HONumber=Jun16_HO2-->


