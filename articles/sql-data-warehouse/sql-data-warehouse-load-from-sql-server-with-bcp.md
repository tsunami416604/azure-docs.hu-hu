<properties
   pageTitle="Adatok betöltése az SQL Serverből az Azure SQL Data Warehouse-ba (bcp) | Microsoft Azure"
   description="Kisebb adatméret esetében a bcp segítségével exportál adatokat az SQL Serverről egybesimított fájlokba, majd közvetlenül az SQL Data Warehouse-ba importálja őket."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# Adatok betöltése az SQL Serverből az Azure SQL Data Warehouse-ba (egybesimított fájlok)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Kisebb adatkészletek esetében a bcp parancssori segédprogrammal exportálhat adatokat az SQL Serverből, majd közvetlenül az SQL Data Warehouse-ba töltheti be őket.

Az oktatóanyag során a következőket hajtja végre a bcp segítségével:

- Exportál egy táblát az SQL Serverből a bcp out paranccsal (vagy létrehoz egy egyszerű mintafájlt)
- Importál egy táblát egy egybesimított fájlból az SQL Data Warehouse-ba.
- Statisztikákat készít a betöltött adatokról.

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## Előkészületek

### Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

- Egy SQL Data Warehouse-adatbázis
- Telepített bcp parancssori segédprogram
- Telepített sqlcmd parancssori segédprogram

A bcp és sqlcmd parancssori segédeszközöket letöltheti a [Microsoft letöltőközpontból][].

### Adatok ASCII vagy UTF-16 formátumban

Ha a saját adataival próbálja használni ezt az oktatóanyagot, az adatoknak ASCII vagy UTF-16 kódolást kell használniuk, mert a bcp nem támogatja az UTF-8 formátumot. 

A PolyBase támogatja az UTF-8 formátumot, de az UTF-16 formátumot még nem. Ügyeljen arra, hogy ha a bcp-t és a PolyBase-t együtt kívánja használni, az adatokat az SQL Serverből való exportálás után UTF-8 formátumra kell alakítania. 


## 1. Céltábla létrehozása

Adjon meg egy táblát az SQL Data Warehouse-ban a betöltés céltáblájául. A tábla oszlopainak meg kell felelnie az adatfájl egyes soraiban szereplő adatoknak.

Tábla létrehozásához nyisson meg egy parancssort, és az sqlcmd.exe segítségével futtassa a következő parancsot:


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
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

## 4. Statisztika létrehozása

Az SQL Data Warehouse még nem támogatja a statisztikák automatikus létrehozását vagy automatikus frissítését. A legjobb lekérdezési teljesítmény eléréséhez fontos létrehozni statisztikákat a táblák összes oszlopához az első betöltés után, illetve az adatok minden lényeges módosítását követően. A statisztika részletes ismertetése: [Statisztika][]. 

A statisztika létrehozásához futtassa az alábbi parancsot az újonnan betöltött táblákon.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## 5. Adatok exportálása az SQL Data Warehouse-ból
Ha szeretné, exportálhatja az SQL Data Warehouse-ból épp visszatöltött adatokat.  Az exportáláshoz használt parancs pontosan megegyezik az SQL Serverről való exportáláshoz használttal.

Az eredmények viszont eltérőek lesznek. Minthogy az adatokat a rendszer elosztott helyeken tárolja az SQL Data Warehouse-ban, az adatok exportálásakor minden számítási csomópont a kimeneti fájlba írja az adatait. A kimeneti fájl adatainak sorrendje valószínűleg eltér a bemeneti fájl adatainak sorrendjétől.

### Tábla exportálása és az exportált eredmények összehasonlítása

Az exportált adatok megtekintéséhez nyissa meg a parancssort, és futtassa ezt a parancsot a saját paramétereivel. A ServerName az Azure logikai SQL Server neve.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Az adatok helyes exportálását az új fájl megnyitásával ellenőrizheti. A fájl adatainak egyeznie kell az alábbi szöveggel, de a sorrendje valószínűleg eltérő lesz:

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

### Lekérdezés eredményeinek exportálása

A bcp **queryout** függvényével exportálhatja egy lekérdezés eredményeit az egész tábla exportálása helyett. 

## További lépések
A betöltés áttekintése: [Adatok betöltése az SQL Data Warehouse-ba][].
További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés][].
További információk a táblázatok létrehozásáról az SQL Data Warehouse-ban: [Táblatervezés][] vagy [CREATE TABLE szintaxis][].

<!--Image references-->

<!--Article references-->

[Adatok betöltése az SQL Data Warehouse-ba]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse fejlesztői áttekintés]: sql-data-warehouse-overview-develop.md
[Táblatervezés]: sql-data-warehouse-develop-table-design.md
[Statisztika]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE szintaxis]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft letöltőközpontból]: https://www.microsoft.com/download/details.aspx?id=36433



<!--HONumber=Jun16_HO2-->


