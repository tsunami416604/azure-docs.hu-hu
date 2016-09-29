<properties
   pageTitle="Adatok betöltése CSV-fájlból az Azure SQL Database-be (bcp) | Microsoft Azure"
   description="Kisebb adatméret esetén a bcp segítségével importálhatja az adatokat az Azure SQL Database-be."
   services="sql-database"
   documentationCenter="NA"
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/13/2016"
   ms.author="carlrab"/>



# Adatok betöltése CSV-fájlból az Azure SQL Data Warehouse-ba (egybesimított fájlok)

A bcp parancssori segédprogram használatával adatokat importálhat egy CSV-fájlból az Azure SQL Database-be.

## Előkészületek

### Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

- Egy Azure SQL Database logikai kiszolgáló és adatbázis
- Telepített bcp parancssori segédprogram
- Telepített sqlcmd parancssori segédprogram

A bcp és sqlcmd parancssori segédeszközöket letöltheti a [Microsoft letöltőközpontból][].

### Adatok ASCII vagy UTF-16 formátumban

Ha a saját adataival próbálja használni ezt az oktatóanyagot, az adatoknak ASCII vagy UTF-16 kódolást kell használniuk, mert a bcp nem támogatja az UTF-8 formátumot. 

## 1. Céltábla létrehozása

Adjon meg egy táblát az SQL Database-ben céltáblaként. A tábla oszlopainak meg kell felelnie az adatfájl egyes soraiban szereplő adatoknak.

Tábla létrehozásához nyisson meg egy parancssort, és az sqlcmd.exe segítségével futtassa a következő parancsot:


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```


## 2. Forrásadatfájlok létrehozása

Nyissa meg a Jegyzettömböt, és másolja az alábbi adatsorokat egy új szöveges fájlba, majd mentse ezt a fájlt a helyi ideiglenes könyvtárba (C:\Temp\DimDate2.txt). Ezek az adatok ASCII formátumban vannak.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(Opcionális) A saját adatok SQL Server-adatbázisból való exportálásához nyisson meg egy parancssort, és futtassa az alábbi parancsot. Cserélje le a TableName, ServerName, DatabaseName, Username és Password adatokat a saját adataira.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```

## 3. Az adatok betöltése
Az adatok betöltéséhez nyisson meg egy parancssort, és futtassa az alábbi, a kiszolgáló nevét, az adatbázis nevét, a felhasználónevet és a jelszót a saját értékeire lecserélő parancsot.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

A parancs segítségével ellenőrizze, hogy megfelelő volt-e az adatok betöltése

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Az eredménynek így kell kinéznie:

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2


## Következő lépések

Az SQL Server-adatbázisok áttelepítésével kapcsolatban tekintse meg az [SQL Server-adatbázisok áttelepítésével](sql-database-cloud-migrate.md) foglalkozó cikket.

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE szintaxis]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft letöltőközpontból]: https://www.microsoft.com/download/details.aspx?id=36433



<!--HONumber=Sep16_HO4-->


