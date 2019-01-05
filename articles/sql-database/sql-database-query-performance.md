---
title: Lekérdezési Terheléselemző az Azure SQL Database-hez |} A Microsoft Docs
description: A legtöbb CPU-igényes lekérdezéseket egy Azure SQL database lekérdezési alkalmazásteljesítmény-figyelő azonosítja.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 5d892005881436dec89c0d0d010f7f02e7bdebf9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039197"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Az Azure SQL Database lekérdezési Terheléselemző

Kezelése és a relációs adatbázisok teljesítményének hangolása veszi szakértelemmel és ideje. Lekérdezési Terheléselemző az Azure SQL Database intelligens teljesítmény termékvonal egy részét képezi. Ez segít hibáinak elhárítása az adatbázis teljesítményét azáltal, hogy kevesebb időt:

* Mélyebb betekintést az adatbázisok (DTU) erőforrás-felhasználásáról.
* A leggyakoribb lekérdezések a Processzor, időtartama és végrehajtási száma (a potenciális finomhangolása javított teljesítménye a deduplikációra) szerint részletei.
* Lehetővé teszi a részletesebb le a lekérdezés a lekérdezés szövege és erőforrás-használat előzményeinek megtekintéséhez.
* A teljesítménnyel kapcsolatos javaslatok megjelenítése jegyzetekkel [az SQL Database Advisor](sql-database-advisor.md).

![Lekérdezési terheléselemző](./media/sql-database-query-performance/opening-title.png)

> [!TIP]
> Alapszintű alkalmazásteljesítmény-figyeléshez az Azure SQL Database lekérdezési Terheléselemző javasoljuk. Jegyezze fel a termék korlátozások közzétett cikkben. Speciális, az adatbázis teljesítményét, figyelés, javasoljuk, hogy [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md). Rendelkezik beépített intelligencia az automatizált teljesítménnyel kapcsolatos hibaelhárítás. Automatikus finomhangolása néhány adatbázist gyakori teljesítményproblémát, javasoljuk, hogy [az automatikus hangolás](sql-database-automatic-tuning.md).

## <a name="prerequisites"></a>Előfeltételek

Lekérdezési Terheléselemző megköveteli, hogy [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) aktív az adatbázisban. Ez automatikusan az összes Azure SQL-adatbázis alapértelmezés szerint engedélyezve van. Ha nem fut a Query Store, az Azure Portalon kérni fogja az engedélyezéshez.

> [!NOTE]
> Ha a "Query Store nem megfelelően van konfigurálva ezen az adatbázison" üzenet jelenik meg a portálon, [optimalizálása a Query Store konfigurációs](#optimize-the-query-store-configuration-for-query-performance-insight).
>

## <a name="permissions"></a>Engedélyek

A következőkre lesz szüksége [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) engedélyek lekérdezési Terheléselemző használatával:

* **Olvasó**, **tulajdonosa**, **közreműködői**, **SQL DB Contributor**, vagy **SQL Server Közreműködője** engedélyekre szükség a felső erőforrás-igényes lekérdezéseket és diagramok megtekintése.
* **Tulajdonos**, **közreműködői**, **SQL DB Contributor**, vagy **SQL Server Közreműködője** lekérdezés szövegének megtekintéséhez szükséges engedélyekkel.

## <a name="use-query-performance-insight"></a>A Lekérdezési terheléselemző használata

Lekérdezési Terheléselemző használata Gyerekjáték:

1. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és keresse meg egy adatbázis, amelyet meg szeretne vizsgálni.
2. A bal oldali menüben nyissa meg a **intelligens teljesítmény** > **lekérdezési Terheléselemző**.
  
   ![A menüben a lekérdezési Terheléselemző](./media/sql-database-query-performance/tile.png)

3. Az első lapon tekintse át a felső erőforrás-igényes lekérdezéseket.
4. Válassza ki az egyes lekérdezések a részletek megtekintéséhez.
5. Nyissa meg **intelligens teljesítmény** > **teljesítménnyel kapcsolatos javaslatok** , és ellenőrizze, hogy ha a teljesítménnyel kapcsolatos ajánlásokat érhetők el. A beépített teljesítménnyel kapcsolatos javaslatok további információkért lásd: [az SQL Database Advisor](sql-database-advisor.md).
6. Csúszkákkal vagy ikonjai a megfigyelt időköz módosításához nagyítás.

   ![teljesítmény-irányítópult](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Az SQL Database lekérdezési Terheléselemző található információk megjelenítéséhez Query Store kell néhány órán keresztül az adatok rögzítéséhez. Ha az adatbázis nem volt tevékenység, vagy ha a Query Store nem volt aktív egy bizonyos időszakban, a diagramok fog lehet üres, ha a lekérdezési Terheléselemző megjeleníti az adott időtartományban. Ha még nem fut. bármikor engedélyezheti a Query Store. További információkért lásd: [ajánlott eljárások a Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store).

## <a name="review-top-cpu-consuming-queries"></a>CPU-igényes leggyakoribb lekérdezések áttekintéséhez

Alapértelmezés szerint a lekérdezési Terheléselemző első öt CPU-igényes lekérdezés megjelenik, amikor először megnyitja.

1. Válassza ki, vagy törölje a jelet belefoglalása vagy kizárása őket a diagram jelölőnégyzetek segítségével egyéni lekérdezéseket.

    A felső sort jeleníti meg az adatbázis teljes DTU százalékos értéke. A sávok megjelenítése a kiválasztott lekérdezések során a kiválasztott időszakban felhasznált Processzorhasználat (%). Például ha **elmúlt hét** van kijelölve, minden egyes sáv jelöli egy nap.

    ![Leggyakoribb lekérdezések](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > A dtu-k sorban látható összesített értéket jelenít meg egy maximális használat értékre egy órás időszakban. Azt jelenti, csak a lekérdezés-végrehajtási statisztikák magas szintű összehasonlítását. Bizonyos esetekben DTU-használata előfordulhat, hogy úgy tűnik, túl magas képest végrehajtott lekérdezések, de nem feltétlenül Ez a helyzet.
   >
   > Ha a lekérdezés maximumot DTU 100 %-ra csak néhány percet, például a dtu-k sort, a lekérdezési Terheléselemző jeleníti meg a teljes órányi használat 100 %-ot (a következménye, a maximális összesített érték).
   >
   > Kifinomultabb összehasonlítását (akár egy percig) célszerű lehet egy egyéni DTU-kihasználtsági diagram:
   >
   > 1. Az Azure Portalon válassza ki a **Azure SQL Database** > **figyelés**.
   > 2. Válassza a **Metrika** lehetőséget.
   > 3. Válassza ki **+ Hozzáadás diagram**.
   > 4. Válassza ki a diagram a DTU százalékos értéke.
   > 5. Ezenkívül válassza **az elmúlt 24 órából** a bal felső menü, és módosítsa, egy perc alatt.
   >
   > A egyéni DTU diagram használata részletek kifinomultabb szintű összehasonlítani a lekérdezés-végrehajtási diagram.

   Az alsó rács látható lekérdezések összesített információit jeleníti meg:

   * Lekérdezés azonosítója, amelyet a lekérdezés az adatbázis egyedi azonosítója.
   * Processzor-lekérdezésenként során megfigyelhető időközt, amely aggregátumfüggvény függ.
   * Időtartam lekérdezéseként, amely aggregátumfüggvény attól is függ.
   * Egy adott lekérdezés végrehajtásainak száma összesen.

2. Ha az adatok elavulttá válik, válassza ki a **frissítése** gombra.

3. Csúszkák használja, és a Nagyítás gomb segítségével módosíthatja a megfigyelési gyakoriságát, és vizsgálja meg a használatalapú adatforgalmi csúcsokhoz:

   ![Csúszkák és a Nagyítás gomb az az időköz módosítása](./media/sql-database-query-performance/zoom.png)

4. Másik lehetőségként kiválaszthatja a **egyéni** lapon szabhatja testre a nézetet:

   * Metrika (CPU, időtartamának, végrehajtások száma).
   * Időtartam (elmúlt 24 órában, elmúlt hét, illetve az elmúlt hónapban).
   * Lekérdezések száma.
   * Aggregátumfüggvény.
  
   ![Az egyéni lap](./media/sql-database-query-performance/custom-tab.png)
  
5. Válassza ki a **Ugrás >** gombra a testre szabott nézet megtekintéséhez.

   > [!IMPORTANT]
   > Lekérdezési Terheléselemző megjelenítése az első 5-20 felhasználó lekérdezéseket, a választástól függően korlátozódik. Az adatbázis a legkiemelkedőbb téglalapokon látható kívül számos további lekérdezéseket futtathatja, és a rendszer a diagram nem vizsgálja ezeket a lekérdezéseket.
   >
   > Előfordulhat, hogy létezik egy adatbázis-munkaterhelés típusa, amelyben sok kisebb lekérdezésekre, a legkiemelkedőbb téglalapokon is látható, kívül gyakoribb futtatáshoz, és a legtöbb dtu-k használata. Ezek a lekérdezések nem jelennek meg, a teljesítmény diagramra.
   >
   > Például egy lekérdezést lehet, hogy használt fel, jelentős mennyiségű dtu-k egy ideig, bár a megfigyelt időszakban a teljes használat nem éri el a top-igényes lekérdezéseket. Ebben az esetben a lekérdezés erőforrás-használat nem jelent a diagramot.
   >
   > Ha a felső lekérdezés-végrehajtás túli lekérdezési Terheléselemző vonatkozó korlátozások tisztában van szüksége, fontolja meg [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) speciális adatbázis-teljesítmény figyelése és hibaelhárítása.
   >

## <a name="view-individual-query-details"></a>Egyéni lekérdezések részleteinek megtekintése

Lekérdezés részleteinek megtekintéséhez:

1. Válassza ki a lekérdezés a leggyakoribb lekérdezések listájában.

    ![Leggyakoribb lekérdezések listája](./media/sql-database-query-performance/details.png)

   A részletes nézetben nyílik meg. Azt jeleníti meg a Processzor fogyasztási, időtartama és a végrehajtások száma idővel.

2. Válassza ki a diagram szolgáltatásokat részleteiről.

   * A felső diagramon látható egy sort a az általános adatbázis DTU százalékos értéke. A csak a felhasznált a kijelölt lekérdezés Processzorhasználat (%).
   * A második diagram megjeleníti a kijelölt lekérdezés teljes időtartamát.
   * Az alsó diagramon látható a végrehajtások száma a kijelölt lekérdezés által.

   ![Lekérdezés részletei](./media/sql-database-query-performance/query-details.png)

3. Szükség esetén használhat csúszkák, nagyítás gombokkal, vagy választhatja **beállítások** lekérdezési adatok megjelenítésének testreszabásához, vagy válasszon egy másik időtartományt.

   > [!IMPORTANT]
   > Lekérdezési Terheléselemző nem rögzíti a DDL-lekérdezésekre. Bizonyos esetekben a rendszer nem rögzíti az összes ad hoc lekérdezéseket.
   >

## <a name="review-top-queries-per-duration"></a>Tekintse át a leggyakoribb lekérdezések száma időtartama

A lekérdezési Terheléselemző két mérőszám segítségével megkeresheti a potenciális szűk keresztmetszeteket: időtartam és a végrehajtás száma.

Hosszú ideig futó lekérdezéseket a lehető legnagyobb lehetséges hosszabb erőforrások zárolását, más felhasználók számára és a méretezhetőség korlátozása rendelkezik. Azok is a legjobb jelöltek optimalizálás.

A hosszú ideig futó lekérdezések azonosítása:

1. Nyissa meg a **egyéni** lekérdezési Terheléselemző lapján a kiválasztott adatbázishoz.
2. A mérőszámok módosításához **időtartama**.
3. Válassza ki a lekérdezések és a megfigyelés időköz számát.
4. Az összesítő függvény kiválasztása:

   * **Sum** mindezek együttesen komoly összeget az összes lekérdezés végrehajtási ideje a teljes megfigyelés alatt.
   * **Maximális** talál lekérdezi, hogy melyik végrehajtási idő volt maximális a teljes megfigyelés alatt.
   * **Átlagos** megkeresi az összes lekérdezés végrehajtásának átlagos végrehajtási idő, és megjeleníti a legkiemelkedőbb téglalapokon ezek átlagokat.

   ![Lekérdezés időtartama](./media/sql-database-query-performance/top-duration.png)

5. Válassza ki a **Ugrás >** gombra a testre szabott nézet megtekintéséhez.

   > [!IMPORTANT]
   > A lekérdezés nézetben módosításával nem frissíti a dtu-k sor. A dtu-k sor maximális használat értéke mindig látható az az időtartam alatt.
   >
   > Tudni, hogy az adatbázis DTU-fogyasztásának (akár egy percig). további részleteket tartalmazó, fontolja meg egy egyéni diagram létrehozása az Azure Portalon:
   >
   > 1. Válassza ki **az Azure SQL Database** > **figyelési**.
   > 2. Válassza a **Metrika** lehetőséget.
   > 3. Válassza ki **+ Hozzáadás diagram**.
   > 4. Válassza ki a diagram a DTU százalékos értéke.
   > 5. Ezenkívül válassza **az elmúlt 24 órából** a bal felső menü, és módosítsa, egy perc alatt.
   >
   > Azt javasoljuk, hogy az egyéni DTU-diagram használatával vesse össze a lekérdezési teljesítmény diagram.
   >

## <a name="review-top-queries-per-execution-count"></a>Végrehajtások száma szerint a leggyakoribb lekérdezések áttekintéséhez

Egy felhasználói adatbázist használja, előfordulhat, hogy get lassú, annak ellenére, hogy nagy mennyiségű végrehajtás előfordulhat, hogy nem lehet maga az adatbázis és érintő erőforrás-használat alacsony az alkalmazás.

Bizonyos esetekben egy nagy végrehajtások száma vezethet a több hálózati kerek lelassítja. Adatváltások befolyásolhatja a teljesítményt. Még érvényes a hálózati késés és alárendelt kiszolgálói kérések késése.

Például sok adatvezérelt webhelyek erősen minden felhasználói kérelem adatbázisának elérésére. Bár a kapcsolatkészletezést segít, a megnövekedett hálózati forgalmat és az adatbázis-kiszolgáló a feldolgozási terhelés lelassíthatja a teljesítmény. Általánosságban elmondható folyamatosan adatváltások minimális.

Azonosíthatja a leggyakrabban végrehajtott lekérdezések ("forgalmas"):

1. Nyissa meg a **egyéni** lekérdezési Terheléselemző lapján a kiválasztott adatbázishoz.
2. A mérőszámok módosításához **végrehajtások száma**.
3. Válassza ki a lekérdezések és a megfigyelés időköz számát.
4. Válassza ki a **Ugrás >** gombra a testre szabott nézet megtekintéséhez.

   ![lekérdezés végrehajtásainak száma](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Teljesítmény-finomhangolási jegyzetek ismertetése

Felfedezése a lekérdezési Terheléselemző számítási feladatot, miközben Észreveheti a diagramot fölött a függőleges vonallal ikonok.

Ezek az ikonok jsou poznámky. Ezek a teljesítménnyel kapcsolatos javaslatok megjelenítése [az SQL Database Advisor](sql-database-advisor.md). Az egérmutatót a jegyzet, a teljesítménnyel kapcsolatos javaslatok összefoglaló információkat szerezhet.

   ![lekérdezés jegyzet](./media/sql-database-query-performance/annotation.png)

Ha szeretné jobban átláthatja, vagy a alkalmazni az advisor-ajánlást, válassza ki a ikonra kattintva nyissa meg a javasolt művelet részleteit. Ha egy aktív javaslat, akkor azonnal alkalmazhatja a portálról.

   ![lekérdezés jegyzet részletei](./media/sql-database-query-performance/annotation-details.png)

Egyes esetekben a nagyítási szintjét, akkor lehet, hogy egymáshoz közel jegyzetek össze egyetlen jegyzet. Lekérdezési Terheléselemző egy csoport jegyzet ikon jelöli ezt. A csoport jegyzet ikon kiválasztásával megnyílik egy új panel, amely felsorolja a jegyzetek.

Naplókezelője lekérdezések és műveletek teljesítményének hangolása segíthetnek jobban megértheti az alkalmazások és szolgáltatások.

## <a name="optimize-the-query-store-configuration-for-query-performance-insight"></a>A Query Store konfigurációját, a lekérdezési Terheléselemző optimalizálása

Lekérdezési Terheléselemző használata esetén a Query Store következő hibaüzenetek jelenhetnek meg:

* "Query Store nem megfelelően van konfigurálva ezen az adatbázison. Kattintson ide további."
* "Query Store nem megfelelően van konfigurálva ezen az adatbázison. Kattintson ide a beállítások módosításához."

Ezeket az üzeneteket általában akkor jelenik meg, ha a Query Store nem lehet új adatokat gyűjteni.

Az első esetben történik, ha a Query Store a csak olvasható állapotban van, és az optimális paraméterek beállítása. A hibát az adattár méretének növelését, vagy a Query Store törlése. (Ha törli a Query Store, az összes korábban összegyűjtött telemetriai adatok elvesznek.)

   ![Query Store részletei](./media/sql-database-query-performance/qds-off.png)

A második esetben az történik, ha nincs engedélyezve a Query Store vagy paraméterei nem optimálisan vannak beállítva. Módosítsa a rögzítése és adatmegőrzési házirendet, és is engedélyezheti a Query Store, az adta meg a következő parancsok futtatásával [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) vagy az Azure Portalon.

### <a name="recommended-retention-and-capture-policy"></a>Ajánlott rögzítése és adatmegőrzési szabályzat

Adatmegőrzési házirendek két típusa van:

* **Méretalapú**: Ha ez a szabályzat úgy van beállítva **automatikus**, azt tisztítja adatokat automatikusan elérésekor közel maximális méretét.
* **Időalapú**: Ez a házirend alapértelmezés szerint 30 nap értéke. Ha a Query Store megtelik, a művelet törli lekérdezési adatok 30 napnál régebbi.

A rögzítési házirendjében is megadhatja:

* **Az összes**: Query Store az összes lekérdezés rögzíti.
* **Automatikus**: Query Store figyelmen kívül hagyja, alkalmi lekérdezések és a lekérdezések jelentéktelen fordítási és végrehajtási időtartamát. Küszöbértékek for végrehajtások száma, lefordítják időtartama, és a futásidő időtartama belsőleg határozza meg. Ez a beállítás az alapértelmezett.
* **Nincs**: Query Store leállítja az új lekérdezések rögzítése, de továbbra is összegyűjtött már rögzített lekérdezések futásidejének statisztikai adatait.

Javasoljuk, hogy az összes szabályzatok beállítása **automatikus** és a tisztítási szabályzatot úgy, hogy végrehajtja a következő parancsokat a 30 nap [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) vagy az Azure Portalon. (Cserélje le `YourDB` az adatbázis nevével.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

A Query Store méretének növelése csatlakozik egy adatbázishoz keresztül [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) vagy az Azure Portalon, és futtassa az alábbi lekérdezést. (Cserélje le `YourDB` az adatbázis nevével.)

```T-SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Ezek a beállítások alkalmazásának végül ellenőrizze a Query Store az új lekérdezések telemetriai adatok gyűjtésére. Ha azonnal működőképes legyen a Query Store, igény szerint választhat Query Store törölje a következő lekérdezés futtatásával ssms-ben vagy az Azure Portalon keresztül. (Cserélje le `YourDB` az adatbázis nevével.)

> [!NOTE]
> Futtassa az alábbi lekérdezést a művelet törli az összes korábban összegyűjtött figyelt telemetria a Query Store.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="summary"></a>Összegzés

Lekérdezési Terheléselemző segítségével megismerheti a számítási feladatok hatását, és hogy miként kapcsolódik az adatbázis-erőforrások felhasználását. Ezzel a funkcióval megismerheti a top-igényes lekérdezéseket az adatbázisban, és látni fogja a lekérdezések optimalizálásához, mielőtt azok a problémát.

## <a name="next-steps"></a>További lépések

* Válassza ki az adatbázis teljesítményére vonatkozó javaslatok, [javaslatok](sql-database-advisor.md) a lekérdezési Terheléselemző navigációs panelen.

    ![A Recommendations lapon](./media/sql-database-query-performance/ia.png)

* Érdemes lehet engedélyezni az [az automatikus hangolás](sql-database-automatic-tuning.md) a gyakori adatbázis-teljesítmény problémákat.
* Ismerje meg, hogyan [Intelligent Insights](sql-database-intelligent-insights.md) segítségével automatikusan az adatbázis teljesítményét kapcsolatos problémák elhárítása.
* Fontolja meg [Azure SQL Analytics]( ../azure-monitor/insights/azure-sql.md) speciális teljesítményfigyelési SQL adatbázisok, rugalmas készletek és a felügyelt példányok a nagy flotta beépített intelligenciával.
