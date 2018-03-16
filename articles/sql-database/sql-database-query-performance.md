---
title: "Lekérdezési teljesítménybe az Azure SQL Database |} Microsoft Docs"
description: "A legtöbb CPU-felhasználása lekérdezések lekérdezési teljesítmény figyeléséhez azonosítja az Azure SQL-adatbázis."
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 07/05/2017
ms.author: sstein
ms.openlocfilehash: 18d03ffcd586a809b37cbc1ca9a7843c25c7758d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-query-performance-insight"></a>Az Azure SQL adatbázis-lekérdezési Terheléselemző
Kezelése és a relációs adatbázisok teljesítményének hangolása jelentős szakértelmét és az idő befektetési igénylő nehéz feladat. Lekérdezési Terheléselemző kevesebb időt azáltal, hogy a következő adatbázis teljesítményének hibaelhárítási teszi lehetővé:

* Az adatbázisok (DTU) erőforrás-felhasználás mélyebb betekintést. 
* A leggyakoribb lekérdezések szerinti CPU/időtartama/végrehajtás, amely potenciálisan a jobb teljesítmény kell beállítani.
* Részletekbe menően tárhatják fel a részletek a lekérdezés olyan szöveg- és erőforrás-használat előzményeinek megtekintése. 
* Teljesítményhangolás által végrehajtott műveleteket megjelenítő jegyzetek [SQL Azure Database Advisor](sql-database-advisor.md)  



## <a name="prerequisites"></a>Előfeltételek
* A lekérdezési Terheléselemző megköveteli, hogy [Lekérdezéstár](https://msdn.microsoft.com/library/dn817826.aspx) aktív az adatbázishoz. Ha a Lekérdezéstár nem fut, a portál kéri kapcsolja be.

## <a name="permissions"></a>Engedélyek
A következő [szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-what-is.md) lekérdezési Terheléselemző használandó engedélyek szükségesek: 

* **Olvasó**, **tulajdonos**, **közreműködő**, **SQL DB Contributor**, vagy **SQL Server közreműködői** engedélyekre szükség a lekérdezések és diagramokat fel felső erőforrás megtekintéséhez. 
* **Tulajdonos**, **közreműködő**, **SQL DB Contributor**, vagy **SQL Server közreműködői** engedélyekre van szükség a lekérdezés szövegének megjelenítéséhez.

## <a name="using-query-performance-insight"></a>Lekérdezési Terheléselemző használatával
Lekérdezési Terheléselemző könnyen használható:

* Nyissa meg [Azure-portálon](https://portal.azure.com/) és a keresési adatbázis, amelyet meg szeretne vizsgálni. 
  * A bal oldali menüben, a támogatási és hibaelhárítási válassza a "Lekérdezési Terheléselemző".
* Az első lapon tekintse át a legfelső szintű erőforrás-igényes lekérdezések listáját.
* Válassza ki az egyes lekérdezések a részletek megtekintéséhez.
* Nyissa meg [SQL Azure Database Advisor](sql-database-advisor.md) és ellenőrizze, hogy e javaslatokkal érhető el.
* Csúszkákkal vagy nagyítás ikonok megfigyelt időköz módosításához.
  
    ![teljesítmény irányítópult](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Néhány órányi adatot kell az SQL Database, a lekérdezési teljesítmény áttekintést adnak a Lekérdezéstár lekérdezésével rögzíthetők. Ha az adatbázis nincs tevékenység vagy a Lekérdezéstár nem volt aktív egy bizonyos időn belül, a diagramok üres lesz az adott időszak megjelenítésekor. A Lekérdezéstár engedélyezheti bármikor, ha az nem futna.   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>Tekintse át a erőforrásigényes lekérdezések felső Processzor
Az a [portal](http://portal.azure.com) tegye a következőket:

1. Keresse meg az SQL-adatbázis, és kattintson a **összes beállítás** > **támogatási + hibaelhárítás** > **lekérdezési terheléselemzőhöz**. 
   
    ![Lekérdezési terheléselemző][1]
   
    A leggyakoribb lekérdezések nézet megnyílik, és a leggyakoribb CPU fogyasztó lekérdezések vannak felsorolva.
2. Kattintson a részletek a diagram körül.<br>Az első sor az adatbázis teljes DTU % jeleníti meg, amíg a sáv megjelenítése a kijelölt időszak során a kijelölt lekérdezés által használt CPU % (például ha **elmúlt hét** kijelölt minden sáv jelöli egy nap).
   
    ![Leggyakoribb lekérdezések][2]
   
    Az alsó rács látható lekérdezések összesített adatait jelöli.
   
   * Lekérdezésazonosítóval - lekérdezés adatbázis belül egyedi azonosítója.
   * CPU-t (aggregátumfüggvény függ) lekérdezés megfigyelhető időköze alatt kerülne sor.
   * Lekérdezésenként időtartama (aggregátumfüggvény függ).
   * Egy adott lekérdezés végrehajtások teljes száma.
     
     Válassza ki, vagy törölje a belefoglalása / kizárása azokat a diagram jelölőnégyzetek segítségével egyéni lekérdezéseket.
3. Ha az adatok elavulttá válik, kattintson a **frissítése** gombra.
4. Nagyítás gomb megfigyelési időköz módosításához, és vizsgálja meg a teljesítményt és használhatja a csúszkák: ![beállítások](./media/sql-database-query-performance/zoom.png)
5. Ha szükséges, ha azt szeretné, hogy egy másik nézetet, válassza **egyéni** lapra, és állítsa be:
   
   * A metrika (CPU, időtartama, végrehajtási száma)
   * Időtartam (utolsó 24 óra, elmúlt hét elmúlt hónap). 
   * Lekérdezések száma.
   * Aggregátumfüggvény.
     
     ![beállítások](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Egyes lekérdezések részleteinek megtekintése
Lekérdezés a részletek megtekintéséhez:

1. Kattintson a lista leggyakoribb lekérdezések egyetlen lekérdezés.
   
    ![részletek](./media/sql-database-query-performance/details.png)
2. A részleteket megjelenítő nézetet megnyílik, és a lekérdezések fogyasztás/időtartama/végrehajtás processzorszám időbeli bontásban.
3. Kattintson a részletek a diagram körül.
   
   * Sor általános adatbázis DTU % a felső diagram ábrázolja, és a görgetősávokat a kijelölt lekérdezés által használt CPU %.
   * Második diagram teljes időtartam látható a kijelölt lekérdezés által.
   * Alsó diagram végrehajtások teljes száma a kijelölt lekérdezés jeleníti meg.
     
     ![Lekérdezés részletei][3]
4. Másik lehetőségként csúszkákkal, Nagyítás gomb, vagy kattintson a **beállítások** testreszabásához lekérdezési adatok megjelenítésére, vagy válasszon másik időtartamot.

## <a name="review-top-queries-per-duration"></a>Tekintse át a felső lekérdezések száma időtartama
A lekérdezési Terheléselemző legutóbbi frissítését, hogy vezette be két új mérőszámok, amelyik segíthet a potenciális szűk keresztmetszetek azonosítása: duration és végrehajtási számát.<br>

Hosszan futó lekérdezések lennie a legnagyobb hosszabb erőforrások zárolása, más felhasználók számára és méretezhetőség korlátozza. Azok a-zel is a legjobb optimalizálás.<br>

Hosszú ideig futó lekérdezések megadása:

1. Nyissa meg **egyéni** lapon lekérdezési Terheléselemző a kiválasztott adatbázishoz
2. Módosítsa a mérni kívánt kell **időtartama**
3. Válassza ki a lekérdezések és megfigyelési időközben
4. Összesítő függvény kiválasztása
   
   * **Sum** hozzáadja az összes lekérdezés végrehajtási idő teljes megfigyelési időközben során.
   * **Maximális** megkeresi a lekérdezések teljes megfigyelési időközben maximális korábban mely végrehajtási idő.
   * **Átlagos** talál átlagos végrehajtási idő az összes lekérdezés végrehajtások, és ezek átlagok kívül felső is láthat. 
     
     ![lekérdezés időtartama][4]

## <a name="review-top-queries-per-execution-count"></a>Tekintse át a felső lekérdezések száma végrehajtási száma
Végrehajtások nagy száma lehet, hogy nem kell érintő maga adatbázis és erőforrás-használat alacsony lehet, de alkalmazás általános juthat, lassú.

Bizonyos esetekben nagyon magas végrehajtási száma is előfordulhat, hogy növelje a hálózati kiszolgálókkal való adatváltások számát. Adatváltások jelentős mértékben befolyásolhatja a teljesítményt. Azok a hálózati késés és alsóbb rétegbeli kiszolgáló késleltetésű. 

Például számos adatvezérelt webhely fokozottan érik el az adatbázist minden felhasználói kérelem esetén. Amíg a kapcsolat készletezését segítségével, a megnövekedett hálózati forgalmat, és az adatbázis-kiszolgáló terhelése feldolgozása hátrányosan befolyásolhatja a teljesítményt.  Általános útmutatásként adatváltások tartsa a lehető legkisebb értéke.

Gyakran azonosításához végrehajtott lekérdezések ("chatty") lekérdezéseket:

1. Nyissa meg **egyéni** lapon lekérdezési Terheléselemző a kiválasztott adatbázishoz
2. Módosítsa a mérni kívánt kell **végrehajtási száma**
3. Válassza ki a lekérdezések és megfigyelési időközben
   
    ![lekérdezés-végrehajtási száma][5]

## <a name="understanding-performance-tuning-annotations"></a>Teljesítmény hangolási jegyzetek ismertetése
Tervezi a terhelést a lekérdezési teljesítmény elemzését, miközben bizonyára észrevette, hogy fölött a diagram függőleges vonallal ikonok.<br>

Ezekkel az ikonokkal jegyzetek; érintő által végrehajtott műveletek teljesítményének képviselnek [SQL Azure Database Advisor](sql-database-advisor.md). Rámutató jegyzet által alapvető tudnivalók az beszerzése:

![lekérdezés Megjegyzés][6]

Ha további vagy advisor javaslat alkalmazni kívánja, kattintson az ikonra. Az a művelet részleteit nyílik meg. Ha egy aktív adott alkalmazhatja azonnal paranccsal.

![lekérdezés jegyzet részletei][7]

### <a name="multiple-annotations"></a>Több megjegyzés.
Azonban lehetséges, hogy miatt nagyítási szintjét, egymás közelében lévő fogja lekérni összecsukott valamelyikébe. Ez különleges ikon fog megjelenni, kattintson rá fog megnyitása új panel, ahol csoportosított listája a jegyzetek megjelenik.
Adatok, lekérdezések és teljesítményének hangolása műveletek jobb megértése érdekében az alkalmazások és szolgáltatások segítségével. 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>A Lekérdezéstár konfigurációs betekintés a lekérdezési teljesítmény optimalizálása
A lekérdezési Terheléselemző felhasználása során merülhetnek fel az alábbi Lekérdezéstár üzenetek:

* "A Lekérdezéstár nincs megfelelően konfigurálva ezen az adatbázison. Kattintson ide további."
* "A Lekérdezéstár nincs megfelelően konfigurálva ezen az adatbázison. Kattintson ide a beállítások módosításához." 

Ezek az üzenetek általában jelennek meg, amikor a Lekérdezéstár nem képes új adatok gyűjtéséért felelős ügyfélfeladatot. 

Első esetben történik, ha a Lekérdezéstár csak olvasható állapotban van, és paraméterei optimálisan vannak beállítva. A hibát a Lekérdezéstár méretének növelését, vagy a jelölés törlésével a Lekérdezéstár.

![qds gomb][8]

Második esetben történik, ha a Lekérdezéstár le van tiltva, vagy a paraméterek nincsenek beállítva optimális. <br>Módosítsa a rögzítése és megőrzési házirendet, és engedélyezze a Lekérdezéstárat, a következő az alábbi parancsok futtatásával, vagy közvetlenül a portálon:

![qds gomb][9]

### <a name="recommended-retention-and-capture-policy"></a>Ajánlott rögzítése és az adatmegőrzési házirend
Az adatmegőrzési két típusa van:

* Méret - alapú Ha automatikus értékre van beállítva, megtisztítja az adatok automatikusan elérésekor közelében maximális méretét.
* Idő alapján - alapértelmezett helyezünk 30 nap során, ami azt jelenti, a Lekérdezéstár nincs elég lemezterület fog futni, ha törli az információ lekérdezése 30 napnál régebbi

Rögzítése házirend beállítható:

* **Minden** – összes lekérdezés rögzíti.
* **Automatikus** -alkalomszerű lekérdezések és lekérdezések jelentéktelen fordítási és végrehajtási időtartamú figyelmen kívül lesznek hagyva. Végrehajtási szám, a fordítás és a futási ideje küszöbértékek belső határozza meg. Ez a beállítás az alapértelmezett.
* **Nincs** -Lekérdezéstár leállítja az új lekérdezések rögzítését, azonban már rögzített lekérdezések futásidejű statisztikák még gyűjtik.

Azt javasoljuk, hogy minden szabályzatok beállítása automatikus és 30 nap tiszta házirend:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

A Lekérdezéstár méretének növeléséhez. Ez végre tudja hajtani-adatbázishoz szeretne csatlakozni, és a következő lekérdezés kiállító:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Ezek a beállítások alkalmazásának végül ellenőrizze a Lekérdezéstár új lekérdezések gyűjtése, azonban ha nem akarja megvárni a Lekérdezéstár törlése. 

> [!NOTE]
> A következő lekérdezés végrehajtásakor a lekérdezéstárban az összes aktuális adatokat törli. 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Összegzés
Lekérdezési Terheléselemző segítségével megismerheti, hogy a lekérdezés-munkaterhelési a hatását, és hogyan vonatkozik adatbázis hálózatierőforrás-fogyasztás. Ezzel a szolgáltatással akkor fog információ a leginkább erőforrásigényes lekérdezések, és könnyen azonosíthatja a meglévők közül, így elkerülhetők a probléma megoldásához.

## <a name="next-steps"></a>További lépések
Az SQL-adatbázis teljesítményének javítása kapcsolatos további javaslatok kattintson [javaslatok](sql-database-advisor.md) a a **lekérdezési Terheléselemző** panelen.

![Teljesítmény Advisor](./media/sql-database-query-performance/ia.png)

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

