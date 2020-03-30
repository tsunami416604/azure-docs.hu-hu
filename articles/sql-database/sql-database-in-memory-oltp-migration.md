---
title: In-Memory OLTP javítja az SQL txn perf
description: Memóriabeli OLTP elfogadása a tranzakciós teljesítmény javítása érdekében egy meglévő SQL-adatbázisban.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: 653ed75341d5d56ecbe06cb59f0efafa1e68aa0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067269"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>A memórián belüli OLTP használata az alkalmazások teljesítményének növeléséhez az SQL Database-ben

[A memórián belüli OLTP](sql-database-in-memory.md) a prémium [és üzleti legkritikusabb szintű](sql-database-service-tiers-vcore.md) adatbázisokban a tranzakciófeldolgozás, az adatbetöltés és az átmeneti adatforgatókönyvek teljesítményének javítására használható anélkül, hogy növelné a tarifacsomagot. 

> [!NOTE] 
> Ismerje meg, hogyan [duplázza meg a Quorum a kulcsadatbázis munkaterhelését, miközben 70%-kal csökkenti a DTU-t az SQL Database segítségével](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Kövesse az alábbi lépéseket a memórián belüli OLTP alkalmazásával a meglévő adatbázisban.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>1. lépés: Győződjön meg arról, hogy prémium és üzleti legkritikusabb szintű adatbázist használ

A memórián belüli OLTP csak prémium szintű és üzleti legkritikusabb szintű adatbázisokban támogatott. A memóriában támogatott, ha a visszaadott eredmény 1 (nem 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*Az XTP* az *Extreme Transaction Processing rövidítése*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>2. lépés: Azonosítsa az okat, amelyeket át szeretne telepíteni a memórián belüli OLTP-ra
Az SSMS tartalmaz egy **tranzakcióteljesítmény-elemzési áttekintésjelentést,** amely et aktív munkaterheléssel rendelkező adatbázison futtathat. A jelentés azonosítja azokat a táblákat és tárolt eljárásokat, amelyek a memórián belüli OLTP-ra való áttelepítésre jelöltek.

Az SSMS-ben a jelentés létrehozása:

* Az **Objektumkezelőben**kattintson a jobb gombbal az adatbáziscsomópontra.
* Kattintson **a Jelentések** > **– szokásos jelentések** > **tranzakcióteljesítmény-elemzése – áttekintés parancsra.**

További [információ: Annak meghatározása, hogy egy táblát vagy tárolt eljárást át kell-e portolni a memórián belüli OLTP-re.](https://msdn.microsoft.com/library/dn205133.aspx)

## <a name="step-3-create-a-comparable-test-database"></a>3. lépés: Hasonló tesztadatbázis létrehozása
Tegyük fel, hogy a jelentés azt jelzi, hogy az adatbázis rendelkezik egy táblával, amely számára előnyös lenne, ha memóriaoptimalizált táblává konvertálná. Javasoljuk, hogy először tesztelje a javallatot teszteléssel.

Szüksége van egy tesztmásolatra az éles adatbázisból. A tesztadatbázisnak ugyanazon a szolgáltatási szintszinten kell lennie, mint az éles adatbázisnak.

A tesztelés megkönnyítése érdekében a tesztadatbázist az alábbiak szerint kell módosítani:

1. Csatlakozzon a tesztadatbázishoz az SSMS segítségével.
2. A LEKÉRDEZÉSEK WITH (SNAPSHOT) beállításának elkerülése érdekében állítsa be az adatbázis beállítást a következő T-SQL utasításban látható módon:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>4. lépés: Táblák áttelepítése
Létre kell hoznia és fel kell népesítenie a tesztelni kívánt tábla memóriaoptimalizált másolatát. A következő konkra dulaktissza:

* A praktikus memória optimalizálása varázsló SSMS.
* Kézi T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Memóriaoptimalizálás varázsló az SSMS-ben
Az áttelepítési beállítás használata:

1. Csatlakozzon a tesztadatbázishoz az SSMS segítségével.
2. Az **Objektumkezelőben**kattintson a jobb gombbal a asztalra, majd kattintson a **Memóriaoptimalizálási tanácsadó parancsra.**
   
   * Megjelenik **a Táblamemória-optimalizáló tanácsadó** varázsló.
3. A varázslóban kattintson az **Áttelepítés érvényesítése** (vagy a **Tovább** gomb) gombra annak megtekintéséhez, hogy a táblázat rendelkezik-e olyan nem támogatott szolgáltatásokkal, amelyek nem támogatottak a memóriaoptimalizált táblázatokban. További információkért lásd:
   
   * A *memóriaoptimalizálási ellenőrzőlista* a [Memóriaoptimalizálási Tanácsadóban.](https://msdn.microsoft.com/library/dn284308.aspx)
   * [A Transact-SQL konstrukciókat nem támogatja a memórián belüli OLTP.](https://msdn.microsoft.com/library/dn246937.aspx)
   * [Átáttelepítés a memórián belüli OLTP-re.](https://msdn.microsoft.com/library/dn247639.aspx)
4. Ha a tábla nem rendelkezik nem támogatott funkciókkal, a tanácsadó eltudja végezni a tényleges sémát és az adatok áttelepítését.

#### <a name="manual-t-sql"></a>Kézi T-SQL
Az áttelepítési beállítás használata:

1. Csatlakozzon a tesztadatbázishoz az SSMS (vagy egy hasonló segédprogram) segítségével.
2. Szerezze be a teljes T-SQL-parancsfájlt a táblához és az indexekhez.
   
   * Az SSMS-ben kattintson a jobb gombbal az asztalcsomópontra.
   * Kattintson **a Parancsfájl létrehozása az** > új**lekérdezésablakba** > **parancsra.**
3. A parancsfájlablakban adja hozzá a WITH (MEMORY_OPTIMIZED = ON) értéket a CREATE TABLE utasításhoz.
4. Ha van fürtözött index, módosítsa nonclustered.If there is an clustered index, change it to NONCLUSTERED.
5. Nevezze át a meglévő táblát SP_RENAME használatával.
6. A szerkesztett CREATE TABLE parancsfájl futtatásával hozza létre a tábla új, memóriare optimalizált példányát.
7. Másolja az adatokat a memóriaoptimalizált táblázatba az INSERT... VÁLASSZA A * INTO LEHETŐSÉGET:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>5. lépés (nem kötelező): Tárolt eljárások áttelepítése
A memórián belüli szolgáltatás a jobb teljesítmény érdekében módosíthatja a tárolt eljárást is.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>A natívan összeállított tárolt eljárásokkal kapcsolatos szempontok
A natívan összeállított tárolt eljárásnak a következő beállításokkal kell rendelkeznie a T-SQL WITH záradékon:

* NATIVE_COMPILATION
* SCHEMABINDING: olyan táblák, amelyekben a tárolt eljárás nem módosíthatja az oszlopdefiníciókat olyan módon, amely hatással lenne a tárolt eljárásra, hacsak el nem dobja a tárolt eljárást.

A natív modulnak egy nagy [ATOMIC-blokkot](https://msdn.microsoft.com/library/dn452281.aspx) kell használnia a tranzakciókezeléshez. Explicit BEGIN TRANSACTION vagy ROLLBACK TRANSACTION nem szerepkör. Ha a kód egy üzleti szabály megsértését észleli, az atomblokkot [egy THROW](https://msdn.microsoft.com/library/ee677615.aspx) utasítással szüntetheti meg.

### <a name="typical-create-procedure-for-natively-compiled"></a>Tipikus CREATE ELJÁRÁS Natív fordításhoz
Általában a T-SQL létrehozása natívan lefordított tárolt eljárás hasonló a következő sablon:

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

* A TRANSACTION_ISOLATION_LEVEL a SNAPSHOT a natívan lefordított tárolt eljárás leggyakoribb értéke. A többi érték egy részhalmaza azonban is támogatott:
  
  * ISMÉTELHETŐ OLVASÁS
  * Szerializálható
* A NYELV értéknek meg kell jelennie a sys.languages nézetben.

### <a name="how-to-migrate-a-stored-procedure"></a>Tárolt eljárás áttelepítése
Az áttelepítési lépések a következők:

1. Szerezze be a CREATE PROCEDURE parancsfájlt a normál értelmezett tárolt eljáráshoz.
2. Írja át a fejlécet, hogy megfeleljen az előző sablonnak.
3. Győződjön meg arról, hogy a tárolt eljárás T-SQL-kód olyan szolgáltatásokat használ-e, amelyek nem támogatottak a natívmódon lefordított tárolt eljárásokhoz. Szükség esetén alkalmazzon kerülő megoldásokat.
   
   * További információt a [Natívmódon összeállított tárolt eljárások áttelepítésével kapcsolatos problémák értendők meg.](https://msdn.microsoft.com/library/dn296678.aspx)
4. Nevezze át a régi tárolt eljárást SP_RENAME használatával. Vagy egyszerűen dobd el.
5. Futtassa a szerkesztett CREATE PROCEDURE T-SQL parancsfájlt.

## <a name="step-6-run-your-workload-in-test"></a>6. lépés: A számítási feladatok futtatása a tesztben
Futtasson egy számítási feladatot a tesztadatbázisban, amely hasonló az éles adatbázisban futó számítási feladathoz. Ez felfedi a táblák és a tárolt eljárások memóriabeli funkciójának használatával elért teljesítménynövekedést.

A munkaterhelés főbb jellemzői a következők:

* Egyidejű kapcsolatok száma.
* Olvasási/írási arány.

A tesztterhelés személyre szabásához és futtatásához érdemes megfontolni a praktikus ostress.exe eszköz használatát, amely [az itt](sql-database-in-memory.md)látható .

A hálózati késés minimalizálása érdekében futtassa a tesztet ugyanabban az Azure-földrajzi régióban, ahol az adatbázis létezik.

## <a name="step-7-post-implementation-monitoring"></a>7. lépés: A végrehajtás utáni ellenőrzés
Fontolja meg a memórián belüli implementációk teljesítményhatásainak figyelését éles környezetben:

* [Monitor a memóriában tárolt .](sql-database-in-memory-oltp-monitoring.md)
* [Az Azure SQL Database felügyelete dinamikus felügyeleti nézetek használatával](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Memórián belüli OLTP (memórián belüli optimalizálás)](https://msdn.microsoft.com/library/dn133186.aspx)
* [Bevezetés a natívan összeállított tárolt eljárásokba](https://msdn.microsoft.com/library/dn133184.aspx)
* [Memóriaoptimalizálási tanácsadó](https://msdn.microsoft.com/library/dn284308.aspx)

