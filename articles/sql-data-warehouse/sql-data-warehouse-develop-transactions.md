---
title: Az SQL Data Warehouse tranzakciók |} Microsoft Docs
description: Ötletek a tranzakciók az Azure SQL Data Warehouse adattárházzal történő, megoldások.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: ''
ms.assetid: ae621788-e575-41f5-8bfe-fa04dc4b0b53
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 29d53e18539f2c24dd64090b2ac6f9dd4c783961
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="transactions-in-sql-data-warehouse"></a>Az SQL Data Warehouse-tranzakciók
Módon teheti meg, az SQL Data Warehouse támogatja a tranzakciókat, az adatraktár-számítási feladat részeként. Azonban kell biztosítják az SQL Data Warehouse teljesítményét léptékű néhány funkció korlátozva az SQL Server képest. Ez a cikk kiemeli a különbségeket, és felsorolja a többi. 

## <a name="transaction-isolation-levels"></a>Tranzakció elkülönítési szinten
Az SQL Data Warehouse megvalósítja az ACID-tranzakciókat. Azonban a dokumentumos tranzakciótámogatást elkülönítését korlátozódik `READ UNCOMMITTED` és ez nem módosítható. Megvalósíthat számos kódolási módszerek a nem véglegesített adatokat az adatok elkerülésére, ha ez problémát jelent meg. A legnépszerűbb módszerek megakadályozhatja, hogy a felhasználók továbbra is előkészítés alatt álló adatok lekérdezése a CTAS és a tábla partíciós váltás (gyakran más néven mozgó ablak mintát) használja. A népszerű megközelítés nézeteket, amelyek előre szűrje az adatokat is.  

## <a name="transaction-size"></a>Tranzakció mérete
Egyetlen módosítása tranzakció mérete korlátozott. A korlát még ma alkalmazza ") eloszlása feladatonként (". Ezért az összes felosztás kerülhet sor, a korlát megszorozzuk a terjesztési száma. Hozzávetőleges tranzakcióban sorok maximális számát osztja a terjesztési kap minden egyes sorára teljes mérete. A változó hosszúságú oszloppal vegye figyelembe véve az átlagos oszlop hossza, nem pedig a maximális méret használatával.

A következő előfeltételek az alábbi táblázatban szereplő történtek:

* Az adatok még akkor is, terjesztési történt. 
* Az átlagos sor hossza 250 bájt

| [DWU][DWU] | Cap eloszlása (GB) | Azokat a Terjesztéseket száma | Tranzakció maximális (GB) | # Sorok eloszlása | Tranzakciónként sorok maximális száma |
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

Optimalizálása és a naplóba írt adatok csökkentése érdekében tekintse meg a [tranzakciók gyakorlati tanácsok] [ Transactions best practices] cikk.

> [!WARNING]
> A tranzakció maximális mérete csak érhető el, a kivonat, vagy akkor is igaz, ahol az adatok terjedésének az elosztott ROUND_ROBIN táblákat. Ha a tranzakció van írás a kihasználtságot módon a terjesztési majd korlát valószínű előtt a tranzakció maximális méret érhető el.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Tranzakció állapota
Az SQL Data Warehouse a XACT_STATE() függvény segítségével -2 értéke hibás tranzakció jelentést. Ez azt jelenti, hogy a tranzakció sikertelen volt, és csak visszaállítás meg van jelölve

> [!NOTE]
> -2, sikertelen tranzakció jelöléséhez a XACT_STATE függvény használatát az SQL Server különböző viselkedés képviseli. SQL Server vissza nem vonható véglegesítésű tranzakcióból képviselő -1 értéket használja. SQL Server egy tranzakción belül hibák tűri szerint vissza nem vonható véglegesítésű kell megjelölni, hogy nélkül. Például `SELECT 1/0` volna hibát jelez, de nem kényszerítik ki a tranzakció vissza nem vonható véglegesítésű állapotba kerülnek. SQL Server is lehetővé teszi az olvasási műveletek vissza nem vonható véglegesítésű a tranzakcióban. Azonban az SQL Data Warehouse nem teszik ezt. Ha a hiba akkor fordul elő, az SQL Data Warehouse tranzakción belül automatikusan módba lép,-2 állapotát, és nem tudnak elvégezni további választhatja ki utasításokat, amíg az utasítás vissza lett állítva. Ezért fontos ellenőrizni, hogy az alkalmazás kódjában, hogy XACT_STATE() használ, ha Ön esetleg kód módosításokat.
> 
> 

Például az SQL Serverben láthatja a tranzakciót, amelynek a következőképpen néz ki:

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

Ha nem adja meg a kódot, mert az újabb majd jelenik meg a következő hibaüzenet:

Üzenet 111233, szint 16, 1, 1 111233; sor állapota Az aktuális tranzakció megszakadt, és minden függő módosításának rendelkezik visszaállításra került. OK: A tranzakció csak visszaállítási állapotban nem kifejezetten vissza lett állítva egy DDL, DML vagy SELECT utasítás előtt.

Még nem fog ERROR_ * függvények kimenete.

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

Összes megváltozott, hogy a `ROLLBACK` a tranzakció kellett az olvasás a hibaadatok előtt megtörténjen a `CATCH` blokkot.

## <a name="errorline-function"></a>Error_Line() function
Akkor is érdemes megjegyezni, hogy az SQL Data Warehouse nem valósítja meg, és nem támogatja a ERROR_LINE() függvény. Ha ez a kódban, távolítsa el, hogy meg kell felelnie az SQL Data Warehouse kell. A kódban lekérdezés címkék inkább megfelelő funkciók végrehajtásához. Tekintse meg a [címke] [ LABEL] cikk további részleteket a szolgáltatást.

## <a name="using-throw-and-raiserror"></a>Segítségével THROW és RAISERROR
THROW az SQL Data Warehouse kivételt váltson ki több modern végrehajtására, de a RAISERROR is támogatott. Néhány dologban is érdemes figyelmet kell fordítani azonban fizet.

* Felhasználó által definiált hibaüzenetek számok nem lehet THROW 100 000-150 000 tartományán
* RAISERROR hibaüzenetek 50 000 van rögzítve.
* Ilyen hibaszámú használata nem támogatott.

## <a name="limitiations"></a>Limitiations
Az SQL Data Warehouse rendelkezik néhány egyéb korlátozások is tranzakciók.

Ezek a következők:

* Elosztott tranzakciók
* Nem engedélyezett beágyazott tranzakciók
* Nem engedélyezett pontokon menthet
* Elnevezett tranzakciók
* Nem jelölt tranzakciók
* Nem támogatott például DDL `CREATE TABLE` belül a felhasználó definiált tranzakció

## <a name="next-steps"></a>További lépések
Tranzakciók optimalizálása kapcsolatos további információkért lásd: [tranzakciók gyakorlati tanácsok][Transactions best practices].  Az SQL Data Warehouse gyakorlati tanácsokat kapcsolatban lásd: [gyakorlati tanácsok az SQL Data Warehouse][SQL Data Warehouse best practices].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Transactions best practices]: ./sql-data-warehouse-develop-best-practices-transactions.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->
