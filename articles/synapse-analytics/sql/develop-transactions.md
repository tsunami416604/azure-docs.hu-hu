---
title: Tranzakciók használata
description: Tippek a tranzakciók megvalósításához az SQL-készletben (adattárházban) a megoldások fejlesztéséhez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 9b9ce5110a03ec4d67b3e8af6d9b18e5ad6836af
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428718"
---
# <a name="using-transactions-in-sql-pool"></a>Tranzakciók használata az SQL-készletben

Tippek a tranzakciók megvalósításához az SQL-készletben (adattárházban) a megoldások fejlesztéséhez.

## <a name="what-to-expect"></a>Mire számíthat

Ahogy az várható volt, az SQL-készlet támogatja a tranzakciók at az adattárház számítási feladatok részeként. Az SQL-készlet teljesítményének nagy mértékű fenntartása érdekében azonban egyes szolgáltatások korlátozottak az SQL Server kiszolgálóhoz képest. Ez a cikk kiemeli a különbségeket, és felsorolja a többi.

## <a name="transaction-isolation-levels"></a>Tranzakcióelkülönítési szintek

Az SQL-készlet ACID-tranzakciókat valósít meg. A tranzakciós támogatás elkülönítési szintje alapértelmezés szerint READ UNCOMMITTED.  A véglegesített pillanatkép-elkülönítés olvasása a felhasználói adatbázis READ_COMMITTED_SNAPSHOT adatbázisbeállításának bekapcsolásával módosítható, amikor a fő adatbázishoz csatlakozik.  

Ha engedélyezve van, az adatbázis összes tranzakciója a READ COMMITTED SNAPSHOT ISOLATION alatt történik, és a READ UNCOMMITTED beállítás munkamenet szinten nem lesz tiszteletben tartva. A részletekért ellenőrizze [az ALTER ADATBÁZIS-BEÁLLÍTÁS beállításait (Transact-SQL).](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

## <a name="transaction-size"></a>Tranzakció mérete
Egyetlen adatmódosítási tranzakció mérete korlátozott. A korlát felosztásonként kerül alkalmazásra. Ezért a teljes felosztás úgy számítható ki, hogy a korlátot megszorozza az eloszlási számmal. 

A tranzakció sorainak maximális számának közelítéséhez ossza el az elosztási korlátot az egyes sorok teljes méretével. Változó hosszúságú oszlopok esetén a maximális méret helyett érdemes átlagos oszlophosszt venni.

Az alábbi táblázatban a következő feltételezések et tették:

* Az adatok egyenletes eloszlása történt
* Az átlagos sorhossz 250 bájt

## <a name="gen2"></a>Gen2 (1988–

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Felső korlát disztribúciónként (GB) | Disztribúciók száma | MAX tranzakció mérete (GB) | # Sorok elosztásonként | Sorok maximális százaléka tranzakciónként |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c lehetőséget |3,75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7,5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4.500 |300,000,000 |18,000,000,000 |
| DW15000c |112.5 |60 |6750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900,000,000 |54,000,000,000 |

## <a name="gen1"></a>1.

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Felső korlát disztribúciónként (GB) | Disztribúciók száma | MAX tranzakció mérete (GB) | # Sorok elosztásonként | Sorok maximális százaléka tranzakciónként |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3,75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7,5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

A tranzakció méretkorlátja tranzakciónként vagy műveletenként kerül alkalmazásra. Nem vonatkozik az összes egyidejű tranzakcióra. Ezért minden tranzakció számára megengedett, hogy ezt az adatmennyiséget a naplóba írja.

A naplóba írt adatok mennyiségének optimalizálásához és minimalizálásához olvassa el a Tranzakciók ajánlott eljárásokról szóló [cikket.](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

> [!WARNING]
> A maximális tranzakcióméret csak olyan megosztott vagy ROUND_ROBIN elosztott táblák esetében érhető el, ahol az adatok eloszlása egyenletes. Ha a tranzakció ferde módon írja az adatokat a disztribúciókhoz, akkor a korlát valószínűleg a maximális tranzakcióméret előtt érhető el.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Tranzakció állapota

Az SQL-készlet a XACT_STATE() függvénnyel jelenti a sikertelen tranzakciót a -2 érték használatával. Ez az érték azt jelenti, hogy a tranzakció sikertelen volt, és csak visszaállításra van megjelölve.

> [!NOTE]
> A -2 használata a XACT_STATE függvény által egy sikertelen tranzakció tanusította az SQL Server függvényének eltérő viselkedését. Az SQL Server a -1 érték segítségével ábrázol egy nem véglegesíthető tranzakciót. Az SQL Server elvisel bizonyos hibákat a tranzakción belül anélkül, hogy nem véglegesíthetőként kellene megjelölni. Például `SELECT 1/0` hibát okozna, de nem kényszeríthet idáig egy tranzakciót. Az SQL Server engedélyezi az olvasást a nem véglegesíthető tranzakcióban is. Az SQL-készlet azonban nem teszi lehetővé ezt. Ha hiba történik egy SQL készlettranzakción belül, az automatikusan a -2 állapotba kerül, és nem tud további kijelölését elkészíteni, amíg az utasítás t nem lett visszaállítva. Ezért fontos ellenőrizni, hogy az alkalmazás kódját, hogy ha használja XACT_STATE(), ahogy lehet, hogy a kód módosítása.

Az SQL Server ben például a következő rekreti jegében jelenhet meg egy tranzakció:

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

Az előző kód a következő hibaüzenetet adja:

Msg 111233, Level 16, State 1, 1 1 111233 vonal; Az aktuális tranzakció megszakadt, és a függőben lévő módosítások at visszavonták. Ok: A csak visszaállítási állapotban lévő tranzakció t nem lett kifejezetten visszaállítva DDL, DML vagy SELECT utasítás előtt.

A ERROR_* függvények kimenete nem lesz lecsökkentve.

Az SQL készletben a kódot kissé módosítani kell:

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
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
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

A várt viselkedés most megfigyelhető. A tranzakció hibáját a rendszer kezeli, és a ERROR_* függvények a várt módon adják meg az értékeket.

Minden, ami megváltozott, hogy a rollback a tranzakció kellett történnie, mielőtt az olvasás a hiba információkat a CATCH blokk.

## <a name="error_line-function"></a>Error_Line() függvény

Érdemes megjegyezni, hogy az SQL-készlet nem valósítja meg vagy támogatja a ERROR_LINE() függvényt. Ha ez a kódban, el kell távolítania, hogy megfeleljen az SQL-készlet. Az egyenértékű funkciók megvalósításához használjon lekérdezéscímkéket a kódban. További részletek a LABEL cikkben [olvashatók.](develop-label.md)

## <a name="using-throw-and-raiserror"></a>A THROW és a RAISERROR használata

A THROW a legmodernebb megvalósítás a kivételek emelésére az SQL készletben, de a RAISERROR is támogatott. Van néhány különbség, hogy érdemes figyelni azonban.

* A felhasználó által definiált hibaüzenetek száma nem lehet a 100 000-150 000 tartományban a THROW-hez
* A RAISERROR hibaüzenetek 50 000-re vannak javítva
* A sys.messages használata nem támogatott

## <a name="limitations"></a>Korlátozások

Sql-készlet nem rendelkezik néhány egyéb korlátozások, amelyek kapcsolódnak a tranzakciókhoz.

Ezek a következők:

* Nincs elosztott tranzakció
* Nincs szabad beágyazott tranzakció
* Nem engedélyezett a mentési pont
* Nincs elnevezett tranzakció
* Nincsmegjelölt tranzakció
* Nincs támogatás a DDL-hez, például a CREATE TABLE-hez egy felhasználó által definiált tranzakción belül

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni a tranzakciók optimalizálásáról, olvassa el a [Tranzakciók gyakorlati tanácsait.](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) További gyakorlati tanácsok útmutatók is rendelkezésre állnak az [SQL-készlet](best-practices-sql-pool.md) és [az SQL igény szerint (előzetes verzió)](on-demand-workspace-overview.md).
