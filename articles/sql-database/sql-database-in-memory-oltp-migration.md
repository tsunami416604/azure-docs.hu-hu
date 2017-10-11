---
title: "A memórián belüli online Tranzakciófeldolgozási javítja az SQL túlhasználat telj |} Microsoft Docs"
description: "Fogadja el a memórián belüli online Tranzakciófeldolgozási egy meglévő SQL-adatbázis tranzakciós teljesítmény javítása érdekében."
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: MightyPen
ms.assetid: c2bf14a0-905b-47b4-afb6-efe9a61147d5
ms.service: sql-database
ms.custom: develop databases
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: jodebrui
ms.openlocfilehash: 50eed9aed417778bd497f55e20c8e732fdae9cf9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Használja a memórián belüli online Tranzakciófeldolgozási, az alkalmazás SQL-adatbázis teljesítményének növelése
[A memórián belüli online Tranzakciófeldolgozási](sql-database-in-memory.md) is használható a tranzakció-feldolgozást, adatfeldolgozást és átmeneti adatáttelepítések esetében teljesítményének javítása [prémium](sql-database-service-tiers.md) Azure SQL-adatbázisok növelése az árképzési szint nélkül. 

> [!NOTE] 
> Megtudhatja, hogyan [kvórum kulcsának adatbázis munkaterhelés megduplázódik, miközben csökkenti a DTU az SQL Database 70 %-kal](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Kövesse az alábbi lépéseket a meglévő adatbázis memórián belüli online Tranzakciófeldolgozási elfogadására.

## <a name="step-1-ensure-you-are-using-a-premium-database"></a>1. lépés: Győződjön meg arról, a prémium szintű adatbázist használ
A memórián belüli online Tranzakciófeldolgozási csak Premium adatbázisokat támogatja. A memóriában támogatott, ha a visszaadott eredmény 1 (0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* rövidítése *szélsőséges tranzakció feldolgozása*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>2. lépés: A memórián belüli online Tranzakciófeldolgozási át objektumokat azonosító
SSMS tartalmaz egy **tranzakció teljesítményét elemző áttekintése** jelentést, amely egy aktív munkaterhelés adatbázis is futtathatók. A jelentés azonosítja a táblák és tárolt eljárások, amelyek erre a memórián belüli online Tranzakciófeldolgozási való áttelepítésre.

Az SSMS jelentést:

* Az a **Object Explorer**, kattintson a jobb gombbal az adatbázis-csomópont.
* Kattintson a **jelentések** > **szabványos jelentések** > **tranzakció teljesítményét elemző áttekintése**.

További információkért lásd: [meghatározása, ha tábla vagy tárolt eljárás kell használatát. a memórián belüli online Tranzakciófeldolgozási](http://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>3. lépés: Összehasonlítható teszt-adatbázis létrehozása
Tegyük fel, hogy a jelentés azt jelzi, az adatbázis előnyös alakít át egy memóriaoptimalizált tábla tábla. Azt javasoljuk, hogy Ön először ellenőrizze, hogy erősítse meg az arra utal, hogy a tesztelés.

Az éles adatbázis test másolatának van szüksége. A teszt adatbázis ugyanazon a szinten szolgáltatási réteg az éles adatbázis kell lennie.

Megkönnyítése érdekében a teszteléshez, végeznünk a tesztadatbázis az alábbiak szerint:

1. A teszt adatbázis csatlakozni a szolgáltatáshoz az SSMS használatával.
2. A WITH (SNAPSHOT) lehetőséget a lekérdezésekben kellene elkerüléséhez állítsa az adatbázis-beállítás, ahogy az a következő T-SQL utasítást:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>4. lépés: A tábla áttelepítése
Kell létrehozni, és feltölti a vizsgálni kívánt tábla memóriaoptimalizált másolatát. Létrehozhat használatával:

* A hasznos memória optimalizálási varázsló szolgáltatáshoz az ssms.
* Kézi T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Memória optimalizálási varázsló szolgáltatáshoz az ssms
A áttelepítési beállítás használata:

1. Csatlakozás a teszt adatbázishoz ssms alkalmazásával.
2. Az a **Object Explorer**, kattintson a jobb gombbal a táblában, és kattintson a **memória optimalizálási Advisor**.
   
   * A **tábla memória optimalizáló Advisor** varázsló jelenik meg.
3. A varázslóban kattintson **áttelepítési érvényesítési** (vagy a **következő** gomb) a tábla tartalmaz-e a nem támogatott funkciókat, a memóriaoptimalizált táblákban nem támogatott. További információkért lásd:
   
   * A *memória optimalizálási ellenőrzőlista* a [memória optimalizálási Advisor](http://msdn.microsoft.com/library/dn284308.aspx).
   * [Nem támogatja a memórián belüli online Tranzakciófeldolgozási Transact-SQL szerkezetek](http://msdn.microsoft.com/library/dn246937.aspx).
   * [A memórián belüli online Tranzakciófeldolgozási történő](http://msdn.microsoft.com/library/dn247639.aspx).
4. Ha a tábla nem nem támogatott szolgáltatások, az advisor hajthat végre a tényleges séma- és adatáttelepítés meg.

#### <a name="manual-t-sql"></a>Kézi T-SQL
A áttelepítési beállítás használata:

1. A teszt adatbázis csatlakozni a szolgáltatáshoz az SSMS (vagy egy hasonló segédprogram) segítségével.
2. Szerezze be a teljes T-SQL-parancsfájlt a táblázat és az indexeket.
   
   * Az SSMS kattintson jobb gombbal a tábla csomópontra.
   * Kattintson a **a táblázatban parancsfájl** > **hozhat létre** > **új lekérdezési ablakba**.
3. A parancsfájl ablakban adja hozzá a WITH (MEMORY_OPTIMIZED = ON) a CREATE TABLE utasításban.
4. Ha egy FÜRTÖZÖTT index, módosítsa a NONCLUSTERED.
5. Nevezze át a meglévő táblázat SP_RENAME.
6. A szerkesztett CREATE TABLE parancsfájl futtatásával hozzon létre a tábla új memóriaoptimalizált példányát.
7. Másolja az adatokat a memóriaoptimalizált táblához INSERT használatával... VÁLASSZA KI * BE:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>(Választható) 5. lépés: telepítse át a tárolt eljárások
A memórián belüli szolgáltatás módosíthatja is javítja a teljesítményt a tárolt eljárást.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>A natív módon lefordított tárolt eljárásokkal kapcsolatos szempontok
Egy natív módon lefordított tárolt eljárásból kell rendelkeznie a T-SQL WITH záradék a következő beállításokat:

* WITH NATIVE_COMPILATION BEÁLLÍTÁST
* Sémához kötési: tábla, amelyen a tárolt eljárás nem módosul, hogy az hatással lenne a tárolt eljárás csak akkor dobható el, a tárolt eljárás oszlop definícióikat jelentését.

Egy natív modult kell használni a nagy [ATOMI blokkokban](http://msdn.microsoft.com/library/dn452281.aspx) tranzakció-kezelésre. Az explicit BEGIN TRANSACTION, vagy a ROLLBACK TRANSACTION Role szerepkör nincs. A kód egy üzleti szabály megsértését észleli, ha azt is leáll az atomic blokk egy [THROW](http://msdn.microsoft.com/library/ee677615.aspx) utasítást.

### <a name="typical-create-procedure-for-natively-compiled"></a>A tipikus CREATE PROCEDURE natív módon lefordított.
Általában a T-SQL natív módon lefordított tárolt eljárás létrehozása a következő sablon hasonlít:

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

* A TRANSACTION_ISOLATION_LEVEL a PILLANATKÉP érték a leggyakrabban használt a natív módon lefordított tárolt eljárásból. Azonban más értékek egy részhalmazát is támogatottak:
  
  * ISMÉTELHETŐ OLVASÁS
  * A SZERIALIZÁLHATÓ
* A NYELVI érték szerepelnie kell a sys.languages nézetben.

### <a name="how-to-migrate-a-stored-procedure"></a>Hogyan telepíthet át egy tárolt eljárás
Az áttelepítés lépései a következők:

1. Szerezze be a CREATE PROCEDURE parancsfájlt, amellyel a rendszeres értelmezett tárolt eljárást.
2. A fejlécben megfelelően az előző sablon újraírási.
3. Annak megállapítása, hogy a tárolt eljárás T-SQL-kódot használja-e ki a szolgáltatásokat, amelyeket nem támogat natív módon lefordított tárolt eljárásokhoz. Lehetséges megoldások megvalósításához, ha szükséges.
   
   * További információ: [áttelepítésekor fellépő hibák tárolt eljárások natív módon lefordított](http://msdn.microsoft.com/library/dn296678.aspx).
4. Nevezze át a régi tárolt eljárás SP_RENAME. Vagy egyszerűen ELDOBNI.
5. A szerkesztett létrehozása eljárás T-SQL-parancsfájl futtatása.

## <a name="step-6-run-your-workload-in-test"></a>6. lépés: A számítási feladatok futtatása teszt
A munkaterhelés futtassa a test adatbázisban hasonló az alkalmazások, az éles adatbázis futó. Ez a memórián belüli funkciójának használatát a táblák és tárolt eljárások megvalósítani jobb teljesítménye kódproblémájáról van.

Jelentős részét a munkaterhelésből attribútumok:

* Egyidejű kapcsolatok száma.
* Olvasási/írási arányt.

Testre szabni, és futtassa a test-munkaterhelés, fontolja meg a hasznos ostress.exe eszközt, amely a [Itt](sql-database-in-memory.md).

Hálózati késés csökkentése érdekében érdemes, a teszt futtatása a azonos Azure földrajzi régióban, ahol az adatbázis létezik.

## <a name="step-7-post-implementation-monitoring"></a>7. lépés: A megvalósítás utáni figyelése
Vegye figyelembe, hogy az éles környezetben a memórián belüli megvalósítások teljesítmény hatásainak figyelése:

* [Figyelje a memórián belüli tároló](sql-database-in-memory-oltp-monitoring.md).
* [Az Azure SQL Database felügyelete dinamikus felügyeleti nézetek használatával](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Memórián belüli online Tranzakciófeldolgozási (a memóriában optimalizálása)](http://msdn.microsoft.com/library/dn133186.aspx)
* [A natív módon lefordított tárolt eljárások bemutatása](http://msdn.microsoft.com/library/dn133184.aspx)
* [Memória optimalizálási Advisor](http://msdn.microsoft.com/library/dn284308.aspx)

