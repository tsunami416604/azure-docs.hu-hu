---
title: Az Azure SQL Database-Teljesítményelemzés lekérdezése |} A Microsoft Docs
description: Az Azure SQL Database lekérdezési alkalmazásteljesítmény-figyelő azonosítja a legtöbb CPU-igényes lekérdezéseket.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 5069b4e69c53ed93e9018cef2517f6125b838d12
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524165"
---
# <a name="azure-sql-database-query-performance-insight"></a>Az Azure SQL Database lekérdezési Terheléselemző
Egyszerűsíthetik relációs adatbázisok teljesítményének kezelését és a jelentős szakértelem és az időt, befektetés igénylő kihívást feladat. Lekérdezési Terheléselemző lehetővé teszi, hogy kevesebb időt azáltal, hogy a következő adatbázis-teljesítmény hibaelhárítása:

* Mélyebb betekintést az adatbázisok (DTU) erőforrás-felhasználásáról. 
* A leggyakoribb lekérdezések CPU/időtartama/végrehajtások száma, amely potenciálisan hangolásával jobb teljesítmény szerint.
* Lehetővé teszi a lekérdezés, a részletek feltárásához szövege és erőforrás-használat előzményeinek megtekintése. 
* Teljesítmény-finomhangolási által végrehajtott műveletek megjelenítése jegyzetekkel [SQL Azure Database Advisor](sql-database-advisor.md)  



## <a name="prerequisites"></a>Előfeltételek
* Lekérdezési Terheléselemző megköveteli, hogy [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) aktív az adatbázisban. Ha a Query Store nem fut, a portálon kéri kapcsolja be.

## <a name="permissions"></a>Engedélyek
A következő [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) engedélyekre van szükség a lekérdezési Terheléselemző használatával: 

* **Olvasó**, **tulajdonosa**, **közreműködői**, **SQL DB Contributor**, vagy **SQL Server Közreműködője** engedélyekre szükség a felső erőforrásigényes, lekérdezések és diagramok megtekintése. 
* **Tulajdonos**, **közreműködői**, **SQL DB Contributor**, vagy **SQL Server Közreműködője** lekérdezés szövegének megtekintéséhez szükséges engedélyekkel.

## <a name="using-query-performance-insight"></a>Lekérdezési Terheléselemző használata
Lekérdezési Terheléselemző használata Gyerekjáték:

* Nyissa meg [az Azure portal](https://portal.azure.com/) és a keresési adatbázis, amely meg szeretné vizsgálni. 
  * A bal oldali menüben, a támogatási és hibaelhárítási válassza ki a "Lekérdezési Terheléselemző".
* Az első lapon tekintse át a felső erőforrás-igényes lekérdezéseket.
* Válassza ki az egyes lekérdezések a részletek megtekintéséhez.
* Nyissa meg [SQL Azure Database Advisor](sql-database-advisor.md) , és ellenőrizze, hogy ha a javaslatok érhetők el.
* Csúszkákkal vagy nagyítás ikonok a megfigyelt időköz módosítása.
  
    ![teljesítmény-irányítópult](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Az adatok néhány órát kell rögzíti a Query Store az SQL Database-lekérdezés elemzéseket biztosít. Ha az adatbázis nincs tevékenységgel rendelkezik, vagy a Query Store nem volt aktív egy bizonyos időszakban, a diagram üres lesz időszak megjelenítésekor. Bármikor előfordulhat, hogy Query Store engedélyezése, ha még nem fut.   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>Legnagyobb Processzorigényű lekérdezések áttekintése
Az a [portál](http://portal.azure.com) tegye a következőket:

1. Keresse meg az SQL-adatbázishoz, és kattintson a **minden beállítás** > **támogatás + hibaelhárítás** > **lekérdezési terheléselemzőhöz**. 
   
    ![Lekérdezési terheléselemző][1]
   
    A leggyakoribb lekérdezések nézetben nyílik meg, és a leggyakoribb CPU fogyasztó lekérdezések vannak felsorolva.
2. Kattintson a diagram részletekért körül.<br>A felső sort az adatbázis teljes dtu-k % jeleníti meg, amíg a sávok megjelenítése a kiválasztott időszakban a kiválasztott lekérdezések által felhasznált processzorhasználatról (például ha **elmúlt hét** kiválasztott sávok egy napot jelöli).
   
    ![Leggyakoribb lekérdezések][2]
   
    Az alsó rács látható lekérdezések összesített adatait jelöli.
   
   * Lekérdezés azonosítója - lekérdezés belül az adatbázis egyedi azonosítója.
   * Processzor-lekérdezésenként megfigyelhető időköze alatt kerülne sor (aggregátumfüggvény függ).
   * Lekérdezésenként időtartama (aggregátumfüggvény függ).
   * Egy adott lekérdezés végrehajtásainak száma összesen.
     
     Válassza ki, vagy törölje vagy kizárja őket a diagramról jelölőnégyzetek segítségével egyéni lekérdezéseket.
3. Ha az adatok elavulttá válik, kattintson a **frissítése** gombra.
4. Nagyítás gomb megfigyelési időköz módosítása, és vizsgálja meg az adatforgalmi csúcsokhoz és használhatja a csúszkák: ![beállításai](./media/sql-database-query-performance/zoom.png)
5. Ha szeretné, ha azt szeretné, hogy egy másik nézetre, kiválaszthatja **egyéni** lapra, és állítsa be:
   
   * A metrika (CPU, időtartamának, végrehajtások száma)
   * Időtartam (elmúlt hét, a múlt hónapban a legutóbbi 24 óra). 
   * Lekérdezések száma.
   * Aggregátumfüggvény.
     
     ![beállítások](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Egyéni lekérdezések részleteinek megtekintése
Lekérdezés részleteinek megtekintéséhez:

1. Kattintson bármelyik lekérdezés a leggyakoribb lekérdezések listájában.
   
    ![részletek](./media/sql-database-query-performance/details.png)
2. A Részletek nézetben nyílik meg, és a lekérdezések Processzor fogyasztási/időtartama/végrehajtások száma idővel lebontva.
3. Kattintson a diagram részletekért körül.
   
   * Felső diagram átfogó adatbázis dtu-k % sorban látható, és a sávok a kijelölt lekérdezés által felhasznált processzorhasználatról.
   * Második diagram mutatja a teljes időtartam a kijelölt lekérdezés által.
   * Alsó diagram mutatja a végrehajtások száma a kijelölt lekérdezés által.
     
     ![Lekérdezés részletei][3]
4. Szükség esetén csúszkákkal, gombok nagyítás, vagy kattintson a **beállítások** lekérdezési adatok megjelenítésének testreszabásához, vagy válasszon másik időtartamot.

## <a name="review-top-queries-per-duration"></a>Tekintse át a leggyakoribb lekérdezések száma időtartama
A lekérdezési Terheléselemző legutóbbi frissítés, amelyek segítségével azonosíthatja a potenciális szűk keresztmetszeteket két új metrikát vezettünk: időtartam és a végrehajtás száma.<br>

Hosszú ideig futó lekérdezéseket a lehető legnagyobb lehetséges hosszabb erőforrások zárolását, más felhasználók számára és a méretezhetőség korlátozása rendelkezik. Szerepelnek a legjobb jelöltek optimalizálás.<br>

Hosszú ideig futó lekérdezések azonosításához:

1. Nyissa meg **egyéni** kiválasztott adatbázis a lekérdezési Terheléselemző lap
2. Lehet, a mérőszámok módosításához a **időtartama**
3. Válassza ki a lekérdezések és megfigyelési időköz száma
4. Összesítő függvény kiválasztása
   
   * **Sum** értékesítések[értékesítésekösszege]) összes lekérdezés végrehajtási idő, teljes megfigyelési időköze alatt kerülne sor.
   * **Maximális** lekérdezések megkeresi, mely végrehajtási idő volt legnagyobb egész megfigyelési időközönként.
   * **Átlagos** megkeresi átlagos végrehajtási idő, az összes lekérdezés végrehajtásának, és bemutatják, ezeket átlagokat kívül felső. 
     
     ![Lekérdezések időtartama][4]

## <a name="review-top-queries-per-execution-count"></a>Végrehajtások száma szerint a leggyakoribb lekérdezések áttekintéséhez
Nagy mennyiségű végrehajtás előfordulhat, hogy nem kell érintő-adatbázis és erőforrás-használat alacsony is lehet, de juthat, átfogó lassú.

Bizonyos esetekben nagyon magas végrehajtások száma növekszik vezethet túlzott hálózati. Adatváltások jelentős mértékben befolyásolhatja a teljesítményt. Érvényes a hálózati késés és alsóbb rétegbeli kiszolgáló késésre vannak. 

Például sok adatvezérelt Web sites erősen minden felhasználói kérelem adatbázisának elérésére. Amíg a kapcsolat készletezési segít, a megnövekedett hálózati forgalmat, és a feldolgozás során jelentkező terhelés, az adatbázis-kiszolgáló hátrányosan befolyásolhatja a teljesítményt.  Általános tanácsokat az adatváltások tartsa a lehető legkisebb.

Azonosíthatja a leggyakrabban végrehajtott lekérdezések ("forgalmas") lekérdezéseket:

1. Nyissa meg **egyéni** kiválasztott adatbázis a lekérdezési Terheléselemző lap
2. Lehet, a mérőszámok módosításához a **végrehajtások száma**
3. Válassza ki a lekérdezések és megfigyelési időköz száma
   
    ![lekérdezés végrehajtásainak száma][5]

## <a name="understanding-performance-tuning-annotations"></a>Teljesítmény-finomhangolási jegyzetek ismertetése
Felfedezése a lekérdezési Terheléselemző számítási feladatot, miközben Észreveheti a diagram felett függőleges vonal-ikonokat.<br>

Ezek az ikonok jsou poznámky; teljesítménybefolyásoló által végrehajtott műveletek képviselnek [SQL Azure Database Advisor](sql-database-advisor.md). Rámutató jegyzet elemzéseket, a művelet alapvető adatait:

![lekérdezés jegyzet][6]

Ha szeretne további információért vagy a alkalmazni az advisor-ajánlást, kattintson az ikonra. Megnyílik a művelet részleteit. Ha egy aktív javaslat alkalmazhat azonnal paranccsal.

![lekérdezés jegyzet részletei][7]

### <a name="multiple-annotations"></a>Több megjegyzést is.
Akkor lehet, hogy miatt nagyítási szintjét, egymás közelében lévő jegyzetek fog első összecsukva egy. Ez speciális ikon fog képviseli, rákattintva nyissa meg új panel, ahol csoportosított listája a jegyzetek fog megjelenik.
Lekérdezések és a teljesítmény-finomhangolási műveletek naplókezelője segít jobban megérteni az alkalmazások és szolgáltatások segítségével. 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>A Query Store konfigurációját, a lekérdezési Terheléselemző optimalizálása
A lekérdezési Terheléselemző használata, során merülhetnek fel az alábbi Query Store üzenetek:

* "Query Store nem megfelelően van konfigurálva ezen az adatbázison. Kattintson ide további."
* "Query Store nem megfelelően van konfigurálva ezen az adatbázison. Kattintson ide a beállítások módosításához." 

Ezeket az üzeneteket általában amikor Query Store nem tudja majd gyűjteni az új adatok jelennek meg. 

Első esetben történik, ha a Query Store csak olvasható állapotban van, és az optimális paraméterek beállítása. Növelje a Query Store méretét vagy a jelölés törlésével a Query Store kijavíthassuk.

![qds gomb][8]

Második eset történik, ha a Query Store ki van kapcsolva, vagy a paraméterek nincsenek beállítva optimális. <br>A rögzítése és adatmegőrzési szabályzatok, és engedélyezze a Query Store, az alábbi parancsok végrehajtásával, vagy közvetlenül a portálon:

![qds gomb][9]

### <a name="recommended-retention-and-capture-policy"></a>Ajánlott rögzítése és adatmegőrzési szabályzat
Adatmegőrzési házirendek két típusa van:

* Méretalapú – Ha automatikus értékre van beállítva, megtisztítja az adatok automatikusan elérésekor közel maximális méretét.
* Időalapú - állítjuk be, és 30 nap, ami azt jelenti, ha Query Store fog elfogyni a helye, akkor törli a lekérdezési adatok 30 napnál régebbi alapértelmezés szerint

Rögzítheti a házirend beállítható:

* **Az összes** – összes lekérdezés rögzíti.
* **Automatikus** – alkalmi lekérdezések és a lekérdezések jelentéktelen fordítási és végrehajtási időtartamát a rendszer figyelmen kívül hagyja. Végrehajtás száma, a fordítási és a futtatókörnyezet időtartama küszöbértékek belsőleg határozza meg. Ez a beállítás az alapértelmezett.
* **Nincs** -Query Store leállítja az új lekérdezések rögzítése, viszont már rögzített lekérdezések futásidejű statisztikái továbbra is gyűjtik.

Javasoljuk, hogy az összes szabályzatok beállítása az automatikus és 30 nap tiszta házirend:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Növelje a Query Store méretét. Ez elvégezhető csatlakozik egy adatbázishoz, és a kiadó a következő lekérdezést:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Ezek a beállítások alkalmazásának végül ellenőrizze a Query Store új lekérdezések összegyűjtése, azonban ha nem szeretné megvárni a Query Store törölheti. 

> [!NOTE]
> A következő lekérdezés végrehajtásakor a Query Store az összes aktuális adatokat törli. 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Összegzés
Lekérdezési Terheléselemző segítségével megismerheti a lekérdezési számítási feladatok hatását, és hogyan kapcsolódik az adatbázis erőforrás-használat. Ezzel a funkcióval akkor fog információ igényes lekérdezéseket, és könnyen azonosíthatja azokat, mielőtt azok a probléma megoldásához.

## <a name="next-steps"></a>További lépések
Az SQL database teljesítményének növelésével kapcsolatos további javaslatok kattintson [javaslatok](sql-database-advisor.md) a a **lekérdezési Terheléselemző** panelen.

![Performance Advisor](./media/sql-database-query-performance/ia.png)

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

