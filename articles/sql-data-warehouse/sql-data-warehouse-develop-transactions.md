---
title: Az Azure SQL Data Warehouse olyan tranzakciók használatával |} A Microsoft Docs
description: Tippek a tranzakciók végrehajtása az Azure SQL Data Warehouse-megoldások fejlesztése.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 02/10/2019
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: ac2bc9fc05a7014cd575ec531ef0a6ba08b2609e
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100142"
---
# <a name="using-transactions-in-sql-data-warehouse"></a>Az SQL Data Warehouse olyan tranzakciók használatával
Tippek a tranzakciók végrehajtása az Azure SQL Data Warehouse-megoldások fejlesztése.

## <a name="what-to-expect"></a>Mi várható?
Hiányol, mert az SQL Data Warehouse támogatja a tranzakciókat az adatraktár-számítási feladat részeként. Azonban kell biztosítják az SQL Data Warehouse teljesítményét nagy mennyiségű egyes funkciók korlátozva az SQL Server képest. Ez a cikk kiemeli a különbségeket, és felsorolja a többi. 

## <a name="transaction-isolation-levels"></a>Tranzakció elkülönítési szinten
Az SQL Data Warehouse valósítja meg az ACID-tranzakciókat. Azonban a tranzakciós támogatás elkülönítési szintjét korlátozódik a READ UNCOMMITTED; Ez a szint nem lehet módosítani. Ha a READ UNCOMMITTED nagyon fontos, a kódolási módszereket, hogy az adatok szabálytalan olvasások száma is alkalmazható. A legnépszerűbb módszerek megakadályozza, hogy a felhasználók továbbra is előkészítés alatt álló adatok lekérdezése a CTAS és a tábla partíciós váltás (gyakran más néven mozgó ablak szerkezet) használja. A népszerű megközelítés nézeteket, amelyek előre szűrje az adatokat is.  

## <a name="transaction-size"></a>Tranzakciók mérete
Egyetlen módosítását tranzakciók mérete korlátozott. A korlát / terjesztési alkalmazza. Ezért az összes felosztás is lehet szorzata korlátot terjesztési száma szerint. A hozzávetőleges a tranzakcióban szereplő sorok maximális száma nullával való osztás a terjesztési kap minden egyes sor teljes méretét. Változó hosszúságú oszloppal fontolja meg egy átlagos oszlop hossza véve a maximális méret használata helyett.

A következő előfeltételek alatti táblázatban történtek:

* Az adatok egyenletes eloszlás történt 
* Átlagos sor hossza 250 bájt

| [DWU](sql-data-warehouse-overview-what-is.md) | Cap kiszolgálónként terjesztési (GiB) | Disztribúciók száma | Max. tranzakció mérete (GiB) | Száma terjesztési sorok | Tranzakciónként sorok maximális száma |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

A tranzakció-méretkorlát óránkénti tranzakciós vagy műveletet alkalmazza. Összes párhuzamos tranzakció között nem érvényes. Ezért minden tranzakció mennyiségű adat írni a napló számára engedélyezett. 

Optimalizálása, és minimalizálja a naplófájlba írt adatok mennyisége, tekintse meg a [tranzakciók ajánlott eljárások](sql-data-warehouse-develop-best-practices-transactions.md) cikk.

> [!WARNING]
> A tranzakció maximális méretét csak a rendszer ezen kivonat, vagy akkor is igaz, ahol az adatok terjedésének az elosztott ROUND_ROBIN táblákat. Ha a tranzakció írja az adatokat magokon feldolgozza a disztribúciók majd a korlát valószínű előtt a tranzakció maximális méret érhető el.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Tranzakció állapota
Az SQL Data Warehouse a XACT_STATE() függvényt használja, -2 értékének használatával sikertelen tranzakciók jelenti. Ez az érték azt jelenti, hogy a tranzakció sikertelen volt, és csak visszaállítás meg van jelölve.

> [!NOTE]
> -2, sikertelen tranzakciók jelölésére XACT_STATE függvény használatát az SQL Server eltérő viselkedéssel jelöli. Az SQL Server-véglegesíthető tranzakció képviselő -1 értéket használja. Az SQL Server tűri tranzakción belül hibák nélkül lesz megjelölve, véglegesíthető kellene. Például `SELECT 1/0` volna hibát okoz, de nem kényszeríti a tranzakció véglegesíthető állapotba kerülnek. Az SQL Server is lehetővé teszi az olvasások a véglegesíthető tranzakció. Azonban az SQL Data Warehouse nem teszi lehetővé az ehhez. Ha hiba történik az SQL Data Warehouse tranzakción belül a program automatikusan ad meg a-2 állapota, és nem lesz az összes ki utasításokat addig, amíg az utasítás vissza lett állítva. Ezért fontos ellenőrizni, hogy az alkalmazás kódjában XACT_STATE() használ, hogy kell elvégeznie kódjuk módosulna.
> 
> 

Ha például az SQL Server jelenhetnek meg tranzakciót, amelynek a következőhöz hasonlóan néz ki:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

A fenti kód lehetővé teszi a következő hibaüzenetet kapja:

Msg 111233, szint 16, 1, 1 111233; sor állapota Az aktuális tranzakció meg lett szakítva, és a függőben lévő módosítások vissza kell lett állítva. OK: A csak visszaállítási állapotban lévő tranzakció explicit módon visszagörgetése nem egy DDL, DML vagy SELECT utasítás előtt.

A kimenet a következő funkciók ERROR_ * nem jelenik meg.

Az SQL Data Warehouse a kód módosítani kell, kis mértékben:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Most meg az elvárt viselkedés. A hiba a tranzakció kezeli, és a functions ERROR_ * értékeket ad meg, a várt módon.

Módosított csak, hogy a VISSZAÁLLÍTÁS a tranzakció volt a hiba adatait a CATCH blokk, az olvasási előtt megtörténjen.

## <a name="errorline-function"></a>Error_Line() function
Emellett akkor is érdemes megjegyezni, hogy az SQL Data Warehouse nem megvalósításához, és nem támogatja a ERROR_LINE() függvény. Ha ez a kódban, távolítsa el, hogy meg kell felelnie az SQL Data Warehouse szeretne. A kódban szereplő lekérdezés címkék használata megfelelő funkciók megvalósításához. További részletekért tekintse meg a [címke](sql-data-warehouse-develop-label.md) cikk.

## <a name="using-throw-and-raiserror"></a>THROW és RAISERROR használatával
THROW a korszerűbb megvalósítása az SQL Data Warehouse láncolata, de a RAISERROR is támogatott. És fordítson különös figyelmet azonban érdemes néhány különbségek vannak.

* Felhasználó által definiált hiba üzenetek számok nem lehet a THROW 100 000 – 150 000 tartományban
* RAISERROR hibaüzenetek 50 000 vannak rögzítve.
* Ilyen hibaszámú használata nem támogatott.

## <a name="limitations"></a>Korlátozások
Az SQL Data Warehouse rendelkezik néhány egyéb korlátozások vonatkoznak a tranzakciók.

Ezek a következők:

* Elosztott tranzakciók
* Nem engedélyezett beágyazott tranzakciók
* A program nem menti a pontok engedélyezett
* Elnevezett tranzakciók
* Nem jelölt tranzakciók
* Például a CREATE TABLE DDL felhasználói tranzakción belül nem támogatott

## <a name="next-steps"></a>További lépések
Tranzakciók optimalizálása kapcsolatos további információkért lásd: [tranzakciók ajánlott eljárások](sql-data-warehouse-develop-best-practices-transactions.md). Egyéb eljárások az SQL Data Warehouse kapcsolatos további információkért lásd: [SQL Data Warehouse – gyakorlati tanácsok](sql-data-warehouse-best-practices.md).

