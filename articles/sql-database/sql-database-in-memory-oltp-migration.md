---
title: A memóriában tárolt OLTP javítja az SQL tranzakció Teljesítményfigyelőt
description: A memóriában tárolt OLTP használatával javíthatja a tranzakciós teljesítményt egy meglévő SQL-adatbázisban.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: 0f10936867c101c07cc8d29d68dc45021bfad1b3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810291"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>A memóriában tárolt OLTP használatával javíthatja az alkalmazások teljesítményét SQL Database

A [memóriában tárolt OLTP](sql-database-in-memory.md) felhasználható a tranzakciók feldolgozásának, az adatok betöltésének és az átmeneti adatkörnyezetek teljesítményének javítására, [prémium és üzletileg kritikus rétegbeli](sql-database-service-tiers-vcore.md) adatbázisokban, az árképzési szint növelése nélkül. 

> [!NOTE] 
> Ismerje meg [, hogy a kvórum megduplázza a kulcsfontosságú adatbázis számítási feladatait, miközben 70%-kal csökkenti a DTU a SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Kövesse az alábbi lépéseket a memóriában lévő OLTP a meglévő adatbázisban való elfogadásához.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>1\. lépés: Győződjön meg arról, hogy prémium és üzletileg kritikus szintű adatbázist használ

A memóriában tárolt OLTP csak prémium és üzletileg kritikus rétegbeli adatbázisokban támogatott. A memóriában tárolt érték akkor támogatott, ha a visszaadott eredmény 1 (nem 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*Xtp* a *szélsőséges tranzakció-feldolgozáshoz*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>2\. lépés: az áttelepíteni kívánt objektumok azonosítása a memóriában lévő OLTP
A SSMS tartalmaz egy **tranzakciós teljesítmény-elemzési áttekintő** jelentést, amelyet egy aktív számítási feladattal rendelkező adatbázison lehet futtatni. A jelentés azokat a táblákat és tárolt eljárásokat azonosítja, amelyeket a rendszer a memóriában lévő OLTP való áttelepítésre jelöltek.

A SSMS-ben a jelentés létrehozásához:

* A **Object Explorer**kattintson a jobb gombbal az adatbázis-csomópontra.
* Kattintson a **jelentések** > **szabványos jelentések** > a **tranzakciók teljesítményének elemzése áttekintés**lehetőségre.

További információ: annak [meghatározása, hogy egy tábla vagy tárolt eljárás a memóriában lévő OLTP legyen-e portolva](https://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>3\. lépés: összehasonlítható tesztelési adatbázis létrehozása
Tegyük fel, hogy a jelentés azt jelzi, hogy az adatbázis egy olyan táblát tartalmaz, amely kihasználhatja a memóriára optimalizált táblára való átalakítás előnyeit. Javasoljuk, hogy az első teszteléssel erősítse meg a jelzést teszteléssel.

Szüksége lesz az éles adatbázis tesztelési példányára. A tesztelési adatbázisnak ugyanazon a szolgáltatási szint szintjén kell lennie, mint az éles adatbázisnak.

A teszteléshez a következő módon csípést tehet a teszt-adatbázison:

1. Kapcsolódjon a tesztelési adatbázishoz a SSMS használatával.
2. Ha nem szeretné, hogy a WITH (SNAPSHOT) lehetőség megjelenjen a lekérdezésekben, állítsa az adatbázis lehetőséget az alábbi T-SQL-utasításban látható módon:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>4\. lépés: táblák átmigrálása
Létre kell hoznia és fel kell töltenie a tesztelni kívánt tábla memóriára optimalizált másolatát. A következőkkel hozható létre:

* A SSMS-ben a hasznos memória optimalizálása varázsló.
* Kézi T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Memória-optimalizálási varázsló a SSMS-ben
Az áttelepítési lehetőség használata:

1. Kapcsolódjon a test adatbázishoz a SSMS használatával.
2. A **Object Explorer**kattintson a jobb gombbal a táblára, majd kattintson a **memória-optimalizálási tanácsadó**elemre.
   
   * Megjelenik a **tábla memória-optimalizáló tanácsadó** varázslója.
3. A varázslóban kattintson az áttelepítés ellenőrzése elemre (vagy a **tovább** gombra) annak **ellenőrzéséhez** , hogy a tábla rendelkezik-e olyan nem támogatott funkciókkal, amelyek nem támogatottak a memóriára optimalizált táblákban. További információkért lásd:
   
   * Memória- *optimalizálási ellenőrzőlista* a [memória-optimalizálási tanácsadóban](https://msdn.microsoft.com/library/dn284308.aspx).
   * [A Transact-SQL-szerkezetek a memóriában tárolt OLTP nem támogatottak](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrálás a memóriában tárolt OLTP](https://msdn.microsoft.com/library/dn247639.aspx).
4. Ha a tábla nem rendelkezik nem támogatott funkciókkal, az Advisor a tényleges sémát és az adatáttelepítést is elvégezheti Önnek.

#### <a name="manual-t-sql"></a>Kézi T-SQL
Az áttelepítési lehetőség használata:

1. Kapcsolódjon a test-adatbázishoz a SSMS (vagy egy hasonló segédprogram) használatával.
2. Szerezze be a teljes T-SQL-szkriptet a táblához és az indexekhez.
   
   * A SSMS kattintson a jobb gombbal a tábla csomópontjára.
   * Kattintson a **parancsfájl-táblázat** > Létrehozás elemre **az** **új lekérdezési ablak** > éhez.
3. A parancsfájl ablakban adja hozzá a (MEMORY_OPTIMIZED = ON) parancsot a CREATE TABLE utasításhoz.
4. FÜRTÖZÖTT index esetén módosítsa azt nem FÜRTÖZÖTT értékre.
5. Nevezze át a meglévő táblát SP_RENAME használatával.
6. Hozza létre a tábla új, memóriára optimalizált példányát a szerkesztett CREATE TABLE szkript futtatásával.
7. Másolja az adatait a memóriára optimalizált táblába INSERT... VÁLASSZA KI A * ELEMET:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>5\. lépés (nem kötelező): tárolt eljárások migrálása
A memórián belüli szolgáltatás a jobb teljesítmény érdekében a tárolt eljárások módosítására is képes.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>A natív módon lefordított tárolt eljárásokkal kapcsolatos megfontolások
A natív módon lefordított tárolt eljárásoknak az alábbi beállításokkal kell rendelkezniük a T-SQL WITH záradékban:

* NATIVE_COMPILATION
* SCHEMABINDING: azokat a táblákat, amelyekben a tárolt eljárás nem módosítható az oszlop definíciói szerint, ami hatással lehet a tárolt eljárásra, hacsak nem dobja el a tárolt eljárást.

A natív modulnak egy Big [Atomic blokkot](https://msdn.microsoft.com/library/dn452281.aspx) kell használnia a tranzakciók kezeléséhez. Explicit BEGIN TRANSACTION vagy VISSZAÁLLÍTÁSi tranzakció esetében nincs szerepkör. Ha a kód egy üzleti szabály megsértését észleli, az atomi blokkot egy [THROW](https://msdn.microsoft.com/library/ee677615.aspx) utasítással vonhatja le.

### <a name="typical-create-procedure-for-natively-compiled"></a>Tipikus LÉTREHOZÁSi eljárás natív módon lefordítva
A natív módon lefordított tárolt eljárások létrehozása általában a T-SQL használatával történik a következő sablonhoz hasonló módon:

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* A TRANSACTION_ISOLATION_LEVEL a PILLANATKÉP a natív módon lefordított tárolt eljárás leggyakoribb értéke. Azonban a többi érték egy részhalmaza is támogatott:
  
  * ISMÉTELHETŐ OLVASÁS
  * SZERIALIZÁLHATÓ
* A nyelvi értéknek jelen kell lennie a sys. languages nézetben.

### <a name="how-to-migrate-a-stored-procedure"></a>Tárolt eljárás áttelepítésének módja
Az áttelepítési lépések a következők:

1. Szerezze be az eljárás létrehozása parancsfájlt a szabályos értelmezésű tárolt eljáráshoz.
2. Írja át a fejlécét az előző sablonnak megfelelően.
3. Győződjön meg arról, hogy a T-SQL-kód tárolt eljárása olyan funkciókat használ, amelyek nem támogatottak a natív módon lefordított tárolt eljárásokhoz. Szükség esetén hajtsa végre a megkerülő megoldásokat.
   
   * Részletekért lásd: [a natív módon lefordított tárolt eljárások áttelepítési problémái](https://msdn.microsoft.com/library/dn296678.aspx).
4. Nevezze át a régi tárolt eljárást SP_RENAME használatával. Vagy egyszerűen dobja el.
5. Futtassa a szerkesztett CREATE PROCEDURE T-SQL-szkriptet.

## <a name="step-6-run-your-workload-in-test"></a>6\. lépés: a számítási feladatok futtatása a tesztben
Futtasson munkaterhelést a tesztelési adatbázisban, amely hasonló az éles adatbázisban futó munkaterheléshez. Ez azt mutatja be, hogy milyen teljesítményt érhet el a memóriában lévő szolgáltatás használata a táblák és a tárolt eljárások esetében.

A számítási feladatok fő attribútumai a következők:

* Az egyidejű kapcsolatok száma.
* Olvasási/írási arány.

A tesztelési feladatok testre szabásához és futtatásához érdemes lehet használni a Handy ostress. exe eszközt, amely [itt](sql-database-in-memory.md)látható.

A hálózati késés csökkentése érdekében futtassa a tesztet ugyanabban az Azure földrajzi régióban, ahol az adatbázis létezik.

## <a name="step-7-post-implementation-monitoring"></a>7\. lépés: a megvalósítás utáni figyelés
Gondolja át a memóriában lévő megvalósítások teljesítménybeli hatásait az éles környezetben:

* [Memóriabeli tárolás figyelése](sql-database-in-memory-oltp-monitoring.md).
* [Az Azure SQL Database felügyelete dinamikus felügyeleti nézetek használatával](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Memóriában tárolt OLTP (memórián belüli optimalizálás)](https://msdn.microsoft.com/library/dn133186.aspx)
* [A natív módon lefordított tárolt eljárások bemutatása](https://msdn.microsoft.com/library/dn133184.aspx)
* [Memória-optimalizálási tanácsadó](https://msdn.microsoft.com/library/dn284308.aspx)

