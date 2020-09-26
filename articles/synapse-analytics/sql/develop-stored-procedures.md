---
title: Tárolt eljárások használata
description: Tippek a tárolt eljárások megvalósításához a szinapszis SQL-ben megoldások fejlesztéséhez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 09/23/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f2046614f3665a699d02c76210676fb32f99fc73
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288919"
---
# <a name="use-stored-procedures-in-synapse-sql"></a>Tárolt eljárások használata a szinapszis SQL-ben

Tippek a tárolt eljárások megvalósításához a szinapszis SQL-készletben (adattárházban) a megoldások fejlesztéséhez.

## <a name="what-to-expect"></a>Amire számíthat

A szinapszis SQL számos, a SQL Serverban használt T-SQL-funkciót támogat. Még ennél is fontosabb, hogy a megoldás teljesítményének maximalizálása érdekében Felskálázási funkciók érhetők el.

Az SQL-készlet méretezésének és teljesítményének fenntartása érdekében vannak olyan funkciók és funkciók is, amelyek viselkedési különbségekkel és másokkal rendelkeznek, amelyek nem támogatottak.

## <a name="stored-procedures-in-synapse-sql"></a>Tárolt eljárások a szinapszis SQL-ben

A tárolt eljárások nagyszerű lehetőséget biztosítanak az SQL-kód beágyazására és az adattárházban lévő adataihoz való közvetlen tárolásra. A tárolt eljárások segítik a fejlesztőket abban, hogy a kódot felügyelhető egységekre ágyazzák, így könnyebben felhasználhatóságot modularize. Az egyes tárolt eljárások is elfogadják a paramétereket, hogy azok még rugalmasabbak legyenek. A következő példában megtekintheti azokat az eljárásokat, amelyek eldobják a külső objektumokat, ha az adatbázisban vannak:

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

Ezek az eljárások olyan utasítás használatával hajthatók végre `EXEC` , amelynél megadhatja az eljárás nevét és paramétereit:

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

A szinapszis SQL egyszerűsített és gördülékenyen tárolt eljárások megvalósítását teszi lehetővé. SQL Server képest a legnagyobb különbség az, hogy a tárolt eljárás nem előre lefordított kód. Az adattárházak esetében a fordítási idő kisebb, mint a nagyméretű adatköteteken végzett lekérdezések futtatásához szükséges idő. Fontos, hogy a tárolt eljárás kódja megfelelően legyen optimalizálva a nagyméretű lekérdezésekhez. A cél az óra, perc és másodperc megtakarítása, nem ezredmásodperc. Ezért hasznos, ha a tárolt eljárásokat SQL Logic tárolóként szeretné meggondolni.

Amikor a szinapszis SQL végrehajtja a tárolt eljárást, az SQL-utasítások elemzése, lefordítása és futási ideje optimalizált. A folyamat során minden utasítás elosztott lekérdezésekre lesz konvertálva. Az adatbázison végrehajtott SQL-kód eltér az elküldött lekérdezéstől.

## <a name="encapsulate-validation-rules"></a>Érvényesítési szabályok beágyazása

A tárolt eljárások lehetővé teszik az ellenőrzési logikák megkeresését az SQL Database-ben tárolt egyetlen modulban. A következő példában láthatja, hogyan ellenőrizheti a paraméterek értékeit, és módosíthatja az alapértelmezett értékeket.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

Az SQL-eljárás logikája érvényesíti a bemeneti paramétereket az eljárás meghívásakor.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>Tárolt eljárások beágyazása

Ha a tárolt eljárás más tárolt eljárásokat hív meg, vagy dinamikus SQL-t hajt végre, akkor a belső tárolt eljárás vagy a kód meghívása beágyazottnak mondható.
A beágyazott eljárás példája az alábbi kódban látható:

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

Ez az eljárás olyan paramétert fogad el, amely valamilyen nevet jelöl, majd más eljárásokat hív meg, hogy eldobják az objektumokat ezen a néven.
A szinapszis SQL-készlet legfeljebb nyolc beágyazási szintet támogat. Ez a képesség némileg eltér a SQL Server. A SQL Server beágyazási szintje 32.

A legfelső szintű tárolt eljárás az 1. szintű beágyazásnak felel meg.

```sql
EXEC clean_up 'mytest'
```

Ha a tárolt eljárás egy másik EXEC hívást is végrehajt, a beágyazási szint két értékre nő.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

Ha a második eljárás elvégez néhány dinamikus SQL-műveletet, a beágyazási szint háromra nő.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> A szinapszis SQL jelenleg nem támogatja [a @NESTLEVEL @](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)-t. Követnie kell a beágyazási szintet. Nem valószínű, hogy túllépi a nyolcnál nagyobb korlátot, de ha igen, újra kell dolgoznia a kódot, hogy az illeszkedjen a korláton belüli beágyazási szintre.

## <a name="insertexecute"></a>INSERT..EXEARANYOS

A szinapszis SQL nem teszi lehetővé, hogy egy tárolt eljárás eredmény-készletét egy INSERT utasítással használja. Egy alternatív megközelítés is használható. Példaként tekintse meg az [ideiglenes táblákról](develop-tables-temporary.md)szóló cikket.

## <a name="limitations"></a>Korlátozások

Léteznek olyan Transact-SQL tárolt eljárások néhány aspektusa, amelyek nincsenek megvalósítva a szinapszis SQL-ben, például:

* ideiglenes tárolt eljárások
* számozott tárolt eljárások
* kiterjesztett tárolt eljárások
* CLR tárolt eljárásai
* titkosítási beállítás
* replikálási beállítás
* tábla értékű paraméterek
* csak olvasható paraméterek
* alapértelmezett paraméterek (kiépített készletben)
* végrehajtási környezetek
* visszatérési utasítás

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [fejlesztés áttekintése](develop-overview.md).
