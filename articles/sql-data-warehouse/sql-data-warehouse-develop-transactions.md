---
title: Az Azure SQL Data Warehouse tranzakciók használatával |} Microsoft Docs
description: Ötletek a tranzakciók az Azure SQL Data Warehouse adattárházzal történő, megoldások.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 7fa3d19cc0fca81616969773a40c3d3dbccc4a26
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="using-transactions-in-sql-data-warehouse"></a>Az SQL Data Warehouse tranzakciók használatával
Ötletek a tranzakciók az Azure SQL Data Warehouse adattárházzal történő, megoldások.

## <a name="what-to-expect"></a>Mi várható?
Módon teheti meg, az SQL Data Warehouse támogatja a tranzakciókat, az adatraktár-számítási feladat részeként. Azonban kell biztosítják az SQL Data Warehouse teljesítményét léptékű néhány funkció korlátozva az SQL Server képest. Ez a cikk kiemeli a különbségeket, és felsorolja a többi. 

## <a name="transaction-isolation-levels"></a>Tranzakció elkülönítési szinten
Az SQL Data Warehouse megvalósítja az ACID-tranzakciókat. Azonban a dokumentumos tranzakciótámogatást elkülönítési szintjét korlátozódik READ UNCOMMITTED; Ez a szint nem módosítható. Ha a READ UNCOMMITTED fontos, megakadályozza az adatok nem véglegesített adatokat módszerek kódolási számos valósíthat meg. A legnépszerűbb módszerek CTAS és a tábla partíciós váltás (gyakran más néven mozgó ablak minta) segítségével megakadályozhatja, hogy a felhasználók továbbra is előkészítés alatt álló adatok lekérdezése. Nézetek, amely előre szűrje az adatokat a népszerű megközelítés egyaránt.  

## <a name="transaction-size"></a>Tranzakció mérete
Egyetlen módosítása tranzakció mérete korlátozott. A korlát) eloszlása feladatonként (lesz alkalmazva. Ezért az összes felosztás kerülhet sor, a korlát megszorozzuk a terjesztési száma. Hozzávetőleges tranzakcióban sorok maximális számát osztja a terjesztési kap minden egyes sorára teljes mérete. A változó hosszúságú oszloppal vegye figyelembe véve az átlagos oszlop hossza, nem pedig a maximális méret használatával.

A következő előfeltételek az alábbi táblázatban szereplő történtek:

* Az adatok még akkor is, terjesztési történt. 
* Az átlagos sor hossza 250 bájt

| [DWU](sql-data-warehouse-overview-what-is.md) | Cap eloszlása (GB) | Azokat a Terjesztéseket száma | Tranzakció maximális (GB) | # Sorok eloszlása | Tranzakciónként sorok maximális száma |
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

A tranzakció méretkorlátot tranzakció vagy műveletet alkalmazza. Minden egyidejű tranzakciókat keresztül nem lesz alkalmazva. Ezért a mennyiségű adatot írni a napló minden tranzakció engedélyezett. 

Optimalizálás, és minimálisra csökkenthető a naplóba írt adatok mennyiségét, tekintse meg a [tranzakciók gyakorlati tanácsok](sql-data-warehouse-develop-best-practices-transactions.md) cikk.

> [!WARNING]
> A tranzakció maximális mérete csak érhető el, a kivonat, vagy akkor is igaz, ahol az adatok terjedésének az elosztott ROUND_ROBIN táblákat. Ha a tranzakció van írás a kihasználtságot módon a terjesztési majd korlát valószínű előtt a tranzakció maximális méret érhető el.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Tranzakció állapota
Az SQL Data Warehouse a XACT_STATE() függvény segítségével -2 értéke hibás tranzakció jelentést. Ez az érték azt jelenti, hogy a tranzakció sikertelen volt, és csak visszaállítás meg van jelölve.

> [!NOTE]
> -2, sikertelen tranzakció jelöléséhez a XACT_STATE függvény használatát az SQL Server különböző viselkedés képviseli. SQL Server -1 értéket használja egy nem véglegesíthető tranzakció képviseli. SQL Server tűri tranzakción belül hibák nélkül nem jelölhető meg, a rendszer nem véglegesíthető kell. Például `SELECT 1/0` volna hibát jelez, de nem kényszerítik ki a tranzakció nem véglegesíthető állapotba kerülnek. SQL Server is lehetővé teszi az olvasási műveletek a rendszer nem véglegesíthető tranzakció. Azonban az SQL Data Warehouse nem teszik ezt. Ha a hiba akkor fordul elő, az SQL Data Warehouse tranzakción belül automatikusan módba lép,-2 állapotát, és nem tudnak elvégezni további választhatja ki utasításokat, amíg az utasítás vissza lett állítva. Ezért fontos ellenőrizni, hogy az alkalmazás kódjában, hogy XACT_STATE() használ, ha Ön esetleg kód módosításokat.
> 
> 

Például az SQL Serverben láthatja a tranzakciót, amelynek a következőképpen néznek:

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
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Az előzőekben látható kód lehetőséget ad a következő hibaüzenet:

Üzenet 111233, szint 16, 1, 1 111233; sor állapota Az aktuális tranzakció megszakadt, és minden függő módosításának rendelkezik visszaállításra került. OK: A tranzakció csak visszaállítási állapotban nem kifejezetten vissza lett állítva egy DDL, DML vagy SELECT utasítás előtt.

A kimenet a ERROR_ * funkciók nem jelenik meg.

Az SQL Data Warehouse a kód némileg módosítani kell:

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

Most már a várt működést követi. A hiba a tranzakció kezeli, és a ERROR_ * funkciók elvárt adjon meg értékeket.

Módosított, hogy a tranzakció VISSZAÁLLÍTÁSA megtörténjen-e a CATCH blokkon belül hibaadatok olvasás előtt volt-e.

## <a name="errorline-function"></a>Error_Line() function
Akkor is érdemes megjegyezni, hogy az SQL Data Warehouse nem valósítja meg, és nem támogatja a ERROR_LINE() függvény. Ha ez a kódban, el kell távolítania azt meg kell felelnie az SQL Data Warehouse. A kódban lekérdezés címkék inkább megfelelő funkciók végrehajtásához. További részletekért lásd: a [címke](sql-data-warehouse-develop-label.md) cikk.

## <a name="using-throw-and-raiserror"></a>Segítségével THROW és RAISERROR
THROW az SQL Data Warehouse kivételt váltson ki több modern végrehajtására, de a RAISERROR is támogatott. Néhány dologban is érdemes figyelmet kell fordítani azonban fizet.

* Felhasználó által definiált hiba üzenetek számok nem lehet THROW 100 000-150 000 tartományán
* RAISERROR hibaüzenetek 50 000 van rögzítve.
* Ilyen hibaszámú használata nem támogatott.

## <a name="limitations"></a>Korlátozások
Az SQL Data Warehouse rendelkezik néhány egyéb korlátozások is tranzakciók.

Ezek a következők:

* Elosztott tranzakciók
* Nem engedélyezett beágyazott tranzakciók
* Nem engedélyezett pontokon menthet
* Elnevezett tranzakciók
* Nem jelölt tranzakciók
* Például a CREATE TABLE DDL felhasználói tranzakción belül nem támogatott

## <a name="next-steps"></a>További lépések
Tranzakciók optimalizálása kapcsolatos további információkért lásd: [tranzakciók ajánlott eljárások](sql-data-warehouse-develop-best-practices-transactions.md). Az SQL Data Warehouse gyakorlati tanácsokat kapcsolatban lásd: [gyakorlati tanácsok az SQL Data Warehouse](sql-data-warehouse-best-practices.md).

