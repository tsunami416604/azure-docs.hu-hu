---
title: In-Memory OLTP javítja az SQL tranzakciós teljesítmény |} A Microsoft Docs
description: Fogadja el In-Memory OLTP egy meglévő SQL-adatbázis tranzakciós teljesítmény javítása érdekében.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: MightyPen
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: ad66253d33b2e99f0be79bfaddc86b3274f5cab0
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258921"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Az SQL Database az alkalmazás teljesítményének javításához használja In-Memory OLTP

[In-Memory OLTP](sql-database-in-memory.md) is használható a tranzakció-feldolgozás, adatbetöltés és átmeneti adatforgatókönyvek teljesítményének javítása érdekében [prémium és üzletileg kritikus szintű](sql-database-service-tiers-vcore.md) adatbázisok a tarifacsomag növelése nélkül. 

> [!NOTE] 
> Ismerje meg, hogyan [kvórum kulcsának adatbázis-munkaterhelés DTU takarítható meg és az SQL Database 70 %-kal kétszeresére nő.](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Kövesse az alábbi lépéseket a meglévő adatbázisban In-Memory OLTP elfogadására.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>1. lépés: Győződjön meg arról, a prémium és üzletileg kritikus szintű adatbázist használ

A memóriában tárolt OLTP-k csak a prémium és üzletileg kritikus szintű adatbázisok használata támogatott. A memóriában támogatott, ha a visszaadott eredmény: 1 (0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* rövidítése *szélsőséges tranzakció feldolgozása*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>2. lépés: Az In-Memory OLTP áttelepítendő objektumok azonosítása
SSMS tartalmaz egy **tranzakciós teljesítmény Hibaelemzés áttekintése** jelentést, amely egy adatbázis egy aktív számítási feladatok vonatkozóan futtathat le. A jelentés azonosítja, táblák és tárolt eljárások, amelyek a deduplikációra kijelölt In-Memory OLTP való migráláshoz.

Az ssms-ben, a jelentés létrehozásához:

* Az a **Object Explorer**, kattintson a jobb gombbal az adatbázis-csomópont.
* Kattintson a **jelentések** > **szabványos jelentések** > **tranzakciós teljesítmény Hibaelemzés áttekintése**.

További információkért lásd: [táblázat vagy tárolt eljárás kell használatát. A memóriában tárolt OLTP-k meghatározása](https://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>3. lépés: Egy hasonló teszt adatbázis létrehozása
Tegyük fel, hogy a jelentés azt jelzi, hogy az adatbázis rendelkezik, melyekhez hasznos lenne alakít át egy memóriaoptimalizált tábla. Azt javasoljuk, hogy először tesztelje a jelzés tesztelésével megerősítéséhez.

Az éles adatbázis egy tesztelési másolatot van szüksége. A test-adatbázis ugyanazon a szinten szolgáltatási szint, az éles adatbázis kell lennie.

Tesztelés megkönnyítése a Teljesítménybeállítások az adatbázis tesztelése a következő:

1. Csatlakozzon a teszt adatbázishoz SSMS használatával.
2. A lekérdezések WITH (SNAPSHOT) lehetőség újraregisztrálására, állítsa be, ahogyan a következő T-SQL utasítást az adatbázis-beállítás:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>4. lépés: Táblák áttelepítése
Kell létrehozni, és töltse ki a vizsgálni kívánt tábla memóriaoptimalizált másolatát. Létrehozhat használatával:

* A hasznos memória optimalizálási varázsló az ssms-ben.
* Manuális a T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Memória optimalizálási varázsló az ssms-ben
A migrálási beállítás használata:

1. Csatlakozzon a teszt adatbázishoz az ssms-ben.
2. Az a **Object Explorer**, kattintson a jobb gombbal a táblára, és kattintson **memória optimalizálási Advisor**.
   
   * A **tábla memória optimalizáló Advisor** varázsló jelenik meg.
3. A varázslóban kattintson **Migrálásának ellenőrzése** (vagy a **tovább** gomb) hogy a tábla rendelkezik-e bármilyen nem támogatott szolgáltatások által nem támogatott memóriaoptimalizált táblákkal. További információkért lásd:
   
   * A *memória optimalizálás ellenőrzőlista* a [memória optimalizálási Advisor](https://msdn.microsoft.com/library/dn284308.aspx).
   * [A Transact-SQL-szerkezetek nem támogatja a memóriabeli OLTP](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Memóriabeli OLTP-ba való migrálás](https://msdn.microsoft.com/library/dn247639.aspx).
4. Ha a tábla nem támogatott szolgáltatások, az advisor hajthat végre a tényleges séma és adatok áttelepítése az Ön számára.

#### <a name="manual-t-sql"></a>Manual T-SQL
A migrálási beállítás használata:

1. Csatlakozzon a teszt adatbázishoz ssms-ben (vagy egy hasonló eszköz) használatával.
2. Szerezze be a teljes T-SQL szkriptet a táblázat és az indexek.
   
   * Az ssms-ben kattintson a jobb gombbal a tábla csomópont.
   * Kattintson a **táblát, parancsfájl** > **LÉTESÍTÉSÉT** > **új lekérdezési ablak**.
3. A parancsfájl ablakában adja hozzá a WITH (MEMORY_OPTIMIZED = ON), a CREATE TABLE utasítás.
4. Ha egy FÜRTÖZÖTT index, módosítsa NONCLUSTERED.
5. Nevezze át a meglévő táblázat sp_rename szintaxis használatával.
6. A tábla új memóriaoptimalizált másolatának létrehozása a szerkesztett CREATE TABLE parancsfájl futtatásával.
7. Az adatok másolása a memóriaoptimalizált táblák INSERT használatával... VÁLASSZA KI * BE:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>(Nem kötelező) 5. lépés: Tárolt eljárások áttelepítése
A memórián belüli funkció jobb teljesítmény tárolt eljárás is módosíthatja.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Natív módon lefordított tárolt eljárásokkal kapcsolatos szempontok
Natív módon lefordított tárolt eljárásban kell rendelkeznie a T-SQL-záradék a következő beállításokat:

* NATIVE_COMPILATION
* SCHEMABINDING: azaz a táblákat, hogy a tárolt eljárás esetében nem módosított semmilyen módon, amely hatással lenne a tárolt eljárás csak akkor dobható el a tárolt eljárás oszlop definícióikat.

Egy natív modult kell használni a big Data típusú [ATOMI blokkokban](https://msdn.microsoft.com/library/dn452281.aspx) tranzakció kezelésére. Explicit BEGIN TRANSACTION vagy a ROLLBACK TRANSACTION Role szerepkör nincs. Ha a kódot egy üzleti szabály megsértése észlel, azt leállíthatja a atomic blokk-a [THROW](https://msdn.microsoft.com/library/ee677615.aspx) utasítást.

### <a name="typical-create-procedure-for-natively-compiled"></a>A tipikus CREATE PROCEDURE natív módon lefordított.
A T-SQL-natív módon lefordított tárolt eljárás létrehozása általában hasonlít az alábbi sablont:

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

* A TRANSACTION_ISOLATION_LEVEL a PILLANATKÉP érték a leggyakrabban használt natív módon lefordított tárolt eljárás. Azonban a többi érték egy részét is támogatottak:
  
  * REPEATABLE READ
  * A SZERIALIZÁLHATÓ
* A NYELVI érték sys.languages nézetében elérhetőnek kell lennie.

### <a name="how-to-migrate-a-stored-procedure"></a>Tárolt eljárás áttelepítése
A migrálási lépések a következők:

1. Szerezze be a CREATE PROCEDURE szkriptet a rendszeres értelmezett tárolt eljárást.
2. Írja át az előző sablon megfelelően a fejlécben.
3. Annak megállapítása, hogy a tárolt eljárás a T-SQL-kódot használja-e a szolgáltatásokat, amelyek natív módon lefordított tárolt eljárások nem támogatottak. Megkerülő megoldások megvalósításához, ha szükséges.
   
   * További részletekért lásd: [áttelepítési problémák tárolt eljárások natív módon lefordított](https://msdn.microsoft.com/library/dn296678.aspx).
4. Nevezze át a régi tárolt eljárás sp_rename szintaxis használatával. Vagy egyszerűen dobja el azt.
5. A szerkesztett hozzon létre ELJÁRÁST a T-SQL-szkript futtatásához.

## <a name="step-6-run-your-workload-in-test"></a>6. lépés: A számítási feladatok futtatásához tesztelés
A számítási feladatok futtatásához a teszt-adatbázis, amely hasonló az éles adatbázis a futó számítási feladatokra. Ez a teljesítmény nyereség érhető el, hogy a táblák és tárolt eljárások számára memóriabeli funkciójának használatát kiderül.

A számítási feladatok nagyobb attribútumai a következők:

* Egyidejű kapcsolatok száma.
* Olvasási/írási arány.

Testre szabni, és a tesztelési számítási feladatok futtatásához, fontolja meg a hasznos ostress.exe eszközt, amely szemlélteti az [Itt](sql-database-in-memory.md).

Hálózati késés minimalizálása érdekében futtassa a tesztet az azonos Azure földrajzi régióban, ahol az adatbázis található.

## <a name="step-7-post-implementation-monitoring"></a>7. lépés: Megvalósítás utáni figyelése
Vegye figyelembe, hogy az éles környezetben a memórián belüli megvalósításokban teljesítmény hatásainak figyelése:

* [Memóriabeli tárolás monitorozása](sql-database-in-memory-oltp-monitoring.md).
* [Az Azure SQL Database felügyelete dinamikus felügyeleti nézetek használatával](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Memóriában tárolt OLTP-k (memóriabeli optimalizálás)](https://msdn.microsoft.com/library/dn133186.aspx)
* [Bevezetés a natív módon lefordított tárolt eljárások](https://msdn.microsoft.com/library/dn133184.aspx)
* [Optimalizálás az Advisor memória](https://msdn.microsoft.com/library/dn284308.aspx)

