---
title: Lekérdezési terheléselemző
description: A lekérdezési teljesítményfigyelés azonosítja az egy- és készletalapú adatbázisok legtöbb processzor-fogyasztó és hosszú ideig futó lekérdezését az Azure SQL-adatbázisban.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: f5998fde6659715de4fcb533cb0f41a8939b1c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214061"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Lekérdezési teljesítmény insight az Azure SQL-adatbázishoz

A Query Performance Insight intelligens lekérdezéselemzést biztosít az egy- és készletezésű adatbázisokhoz. Segít azonosítani a legfontosabb erőforrás-igényes és hosszú ideig futó lekérdezések a számítási feladatokban. Ez segít megtalálni a lekérdezéseket, hogy optimalizálja az általános számítási feladatok teljesítményét, és hatékonyan használja az erőforrást, amely fizet. A Query Performance Insight segítségével kevesebb időt fordítaz adatbázis-teljesítmény hibaelhárítására a következők biztosításával:

* Mélyebb betekintést az adatbázisok erőforrás (DTU) felhasználás
* Részletek a legnépszerűbb adatbázis-lekérdezések processzor, időtartam és végrehajtási szám (potenciális tuning jelöltek teljesítmény javítása)
* A lekérdezés részleteinek részletezése, a lekérdezés szövegének és az erőforrás-kihasználtság előzményeinek megtekintése
* Az adatbázis-tanácsadók teljesítményre vonatkozó [ajánlásait](sql-database-advisor.md) bemutató jegyzetek

![Lekérdezési terheléselemző](./media/sql-database-query-performance/opening-title.png)

## <a name="prerequisites"></a>Előfeltételek

A Lekérdezési teljesítmény betekintésmegköveteli, hogy a [Lekérdezéstároló](https://msdn.microsoft.com/library/dn817826.aspx) aktív az adatbázisban. Alapértelmezés szerint az összes Azure SQL-adatbázishoz automatikusan engedélyezve van. Ha a Query Store nem fut, az Azure Portal on enable is.

> [!NOTE]
> Ha a "Lekérdezési tároló nincs megfelelően konfigurálva ezen az adatbázison" üzenet jelenik meg a portálon, olvassa el [a Lekérdezéstároló konfigurációjának optimalizálása](#optimize-the-query-store-configuration)című témakört.

## <a name="permissions"></a>Engedélyek

A Query Performance Insight használatához a következő [szerepköralapú hozzáférés-vezérlési](../role-based-access-control/overview.md) engedélyekre van szükség:

* **A**leggyakoribb erőforrás-igényes lekérdezések és diagramok megtekintéséhez olvasó , **tulajdonos,** **közreműködő**, **SQL DB közreműködő**vagy **SQL Server Közreműködő** engedélyek szükségesek.
* A lekérdezés szövegének megtekintéséhez **tulajdonosi**, **közreműködői**, **SQL DB közreműködői**vagy **SQL Server Közreműködő** igéi szükségesek.

## <a name="use-query-performance-insight"></a>A Lekérdezési terheléselemző használata

A Lekérdezési teljesítmény betekintés könnyen használható:

1. Nyissa meg az [Azure Portalon,](https://portal.azure.com/) és keresse meg a megvizsgálni kívánt adatbázist.
2. A bal oldali menüben nyissa meg az **Intelligens teljesítménylekérdezés** > **teljesítménybelátását**.
  
   ![Lekérdezési teljesítménybetekintés a menüben](./media/sql-database-query-performance/tile.png)

3. Az első lapon tekintse át a legfontosabb erőforrás-igényes lekérdezések listáját.
4. A részletek megtekintéséhez válasszon ki egy adott lekérdezést.
5. Nyissa **meg az intelligens teljesítményteljesítményre** > vonatkozó**javaslatokat,** és ellenőrizze, hogy rendelkezésre állnak-e teljesítményre vonatkozó javaslatok. A beépített teljesítményjavaslatokról az [SQL Database Advisor](sql-database-advisor.md)című témakörben talál további információt.
6. A megfigyelt időköz módosításához használjon csúszkákat vagy nagyítási ikonokat.

   ![Teljesítmény irányítópult](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Ahhoz, hogy az SQL Database megjelenítse az adatokat a Query Performance Insight, Query Store kell rögzíteni néhány órányi adatot. Ha az adatbázis nak nincs tevékenysége, vagy ha a Lekérdezéstároló nem volt aktív egy bizonyos időszakban, a diagramok üresek lesznek, amikor a Lekérdezési teljesítmény betekintés megjeleníti ezt az időtartományt. A Query Store-t bármikor engedélyezheti, ha nem fut. További információt a [Query Store gyakorlati tanácsok című témakörben talál.](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store)
>

Az adatbázis teljesítményére vonatkozó javaslatokért válassza a [Javaslatok lehetőséget](sql-database-advisor.md) a Lekérdezés teljesítménybetekintése navigációs panelen.

![A Javaslatok lap](./media/sql-database-query-performance/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>Tekintse át a legelső CPU-fogyasztó lekérdezéseket

Alapértelmezés szerint a Lekérdezés idomítása az első öt PROCESSZOR-fogyasztó lekérdezést jeleníti meg, amikor először megnyitja.

1. Jelölje be az egyes lekérdezéseket, vagy törölje a jelet a diagramból jelölőnégyzetekkel való felvételéhez vagy kizárásához.

   A felső sorban az adatbázis teljes DTU-százaléka látható. A sávok a kiválasztott lekérdezések által a kijelölt időköz alatt felhasznált processzorszázalékot mutatják. Ha például **az Elmúlt hét** van kiválasztva, minden sáv egyetlen napot jelöl.

   ![Leggyakoribb lekérdezések](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > A megjelenített DTU-sor egy órás időszakokban a maximális felhasználási értékre összesítve van. Ez azt jelentette, a magas szintű összehasonlítás csak a lekérdezés végrehajtási statisztikák. Bizonyos esetekben a DTU-kihasználtság túl magasnak tűnhet a végrehajtott lekérdezésekhez képest, de előfordulhat, hogy nem ez a helyzet.
   >
   > Ha például egy lekérdezés csak néhány percig 100%-ra maxolt a DTU-t, a Lekérdezési teljesítményinsight DTU-sora a teljes felhasználási órát 100%-ként jeleníti meg (a maximális összesített érték következménye).
   >
   > A finomabb összehasonlításhoz (akár egy percig) érdemes lehet egyéni DTU-kihasználtsági diagramot létrehozni:
   >
   > 1. Az Azure Portalon válassza az **Azure SQL Database** > **Monitoring**lehetőséget.
   > 2. Válassza a **Metrika** lehetőséget.
   > 3. Válassza **a +Diagram hozzáadása**lehetőséget.
   > 4. Válassza ki a DTU százalékos arányt a diagramon.
   > 5. Ezenkívül válassza a bal felső menü **Utolsó 24 órája** parancsát, és módosítsa egy percre.
   >
   > Használja az egyéni DTU-diagramot a részletek finomabb szintjével a lekérdezés-végrehajtási diagrammal való összehasonlításhoz.

   Az alsó rács a látható lekérdezések összesített adatait jeleníti meg:

   * Lekérdezésazonosító, amely az adatbázisban lévő lekérdezés egyedi azonosítója.
   * Lekérdezésenkénti processzor megfigyelhető időközben, amely az összesítési függvénytől függ.
   * Lekérdezésenkénti időtartam, amely az összesítési függvénytől is függ.
   * Egy adott lekérdezés hez tartozó végrehajtások teljes száma.

2. Ha az adatok elavulttá válnak, kattintson a **Frissítés** gombra.

3. A csúszkák és a nagyítási gombok segítségével módosíthatja a megfigyelési időközt, és megvizsgálhatja a fogyasztási csúcsokat:

   ![Csúszkák és nagyítási gombok az intervallum módosításához](./media/sql-database-query-performance/zoom.png)

4. Szükség esetén az **Egyéni** lapon testreszabhatja a nézetet:

   * Metrika (CPU, időtartam, végrehajtási szám).
   * Időintervallum (elmúlt 24 óra, elmúlt hét vagy az elmúlt hónap).
   * Lekérdezések száma.
   * Összesítési függvény.
  
   ![Egyéni lap](./media/sql-database-query-performance/custom-tab.png)
  
5. A testreszabott nézet megtekintéséhez kattintson **az Ugrás >** gombra.

   > [!IMPORTANT]
   > A lekérdezési teljesítménybetekintés a kijelöléstől függően az 5–20 legfontosabb felvevő lekérdezés megjelenítésére korlátozódik. Az adatbázis sokkal több lekérdezést is futtathat a megjelenített legnépszerűbbeken túl, és ezek a lekérdezések nem fognak szerepelni a diagramon.
   >
   > Létezhet egy adatbázis-számítási feladat típusa, amelyben sok kisebb lekérdezések, túl a felső látható, gyakran futnak, és használja a dtu nagy részét. Ezek a lekérdezések nem jelennek meg a teljesítménydiagramon.
   >
   > Előfordulhat például, hogy egy lekérdezés egy ideig jelentős mennyiségű DTU-t vett fel, bár a megfigyelt időszakban a teljes felhasználása kisebb, mint a többi csúcsfogyasztó lekérdezés. Ebben az esetben a lekérdezés erőforrás-kihasználtsága nem jelenik meg a diagramon.
   >
   > Ha a Query Performance Insight korlátain túl is meg kell értenie a legfontosabb lekérdezés-végrehajtásokat, fontolja meg az [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) használatát az adatbázisok teljesítményének speciális figyeléséhez és hibaelhárításához.
   >

## <a name="view-individual-query-details"></a>Az egyes lekérdezések részleteinek megtekintése

A lekérdezés részleteinek megtekintése:

1. Jelöljön ki bármilyen lekérdezést a leggyakoribb lekérdezések listájában.

    ![A leggyakoribb lekérdezések listája](./media/sql-database-query-performance/details.png)

   Megnyílik egy részletes nézet. A processzorfelhasználást, az időtartamot és a végrehajtási számot mutatja az idő múlásával.

2. A részletekhez jelölje ki a diagram jellemzőit.

   * A felső diagram egy sort jelenít meg a teljes adatbázis DTU százalékával. A sávok a kijelölt lekérdezés által felhasznált PROCESSZOR-százalék.
   * A második diagram a kijelölt lekérdezés teljes időtartamát mutatja.
   * Az alsó diagram a kijelölt lekérdezés által végzett végrehajtások teljes számát mutatja.

   ![Lekérdezés részletei](./media/sql-database-query-performance/query-details.png)

3. Szükség esetén csúszkákat használhat, nagyítási gombokat használhat, vagy a **Beállítások** lehetőséget választva testreszabhatja a lekérdezési adatok megjelenítését, vagy másik időtartományt választhat.

   > [!IMPORTANT]
   > A Lekérdezési teljesítmény betekintése nem rögzít DDL-lekérdezéseket. Bizonyos esetekben előfordulhat, hogy nem rögzíti az összes ad hoc lekérdezést.
   >

## <a name="review-top-queries-per-duration"></a>A leggyakoribb lekérdezések áttekintése időtartamonként

A Lekérdezésteljesítmény-betekintés két mutatója segíthet megtalálni a potenciális szűk keresztmetszeteket: az időtartamot és a végrehajtási számokat.

A hosszú ideig futó lekérdezések rendelkeznek a legnagyobb potenciállal az erőforrások hosszabb zárolására, más felhasználók letiltására és a méretezhetőség korlátozására. Ők a legjobb jelöltek az optimalizálásra.

A hosszú ideig futó lekérdezések azonosítása:

1. Nyissa **meg** az Egyéni lapot a Lekérdezési teljesítmény betekintésben a kijelölt adatbázishoz.
2. Módosítsa a mutatókat **időtartamra.**
3. Válassza ki a lekérdezések számát és a megfigyelési időközt.
4. Válassza ki az összesítési függvényt:

   * **Az Összeg** összeadja a lekérdezés végrehajtási idejét a teljes megfigyelési időközhöz.
   * **Max** megkeresi azokat a lekérdezéseket, amelyekben a végrehajtási idő a teljes megfigyelési időközmaximális volt.
   * **Az Átlagos** érték megkeresi az összes lekérdezés-végrehajtás átlagos végrehajtási idejét, és megmutatja az átlagok felső idejét.

   ![Lekérdezés időtartama](./media/sql-database-query-performance/top-duration.png)

5. A testreszabott nézet megtekintéséhez kattintson **az Ugrás >** gombra.

   > [!IMPORTANT]
   > A lekérdezési nézet módosítása nem frissíti a DTU-vonalat. A DTU-sor mindig az intervallum maximális felhasználási értékét mutatja.
   >
   > Az adatbázis DTU-felhasználásának részletesebb (legfeljebb egy perces) megértéséhez fontolja meg egy egyéni diagram létrehozását az Azure Portalon:
   >
   > 1. Válassza az **Azure SQL-adatbázis** > **figyelése**lehetőséget.
   > 2. Válassza a **Metrika** lehetőséget.
   > 3. Válassza **a +Diagram hozzáadása**lehetőséget.
   > 4. Válassza ki a DTU százalékos arányt a diagramon.
   > 5. Ezenkívül válassza a bal felső menü **Utolsó 24 órája** parancsát, és módosítsa egy percre.
   >
   > Azt javasoljuk, hogy az egyéni DTU-diagramot használja a lekérdezésteljesítmény-diagrammal való összehasonlításhoz.
   >

## <a name="review-top-queries-per-execution-count"></a>Tekintse át a leggyakoribb lekérdezéseket végrehajtási számonként

Az adatbázist használó felhasználói alkalmazások lelassulhatnak, még akkor is, ha a végrehajtások nagy száma nem érinti magát az adatbázist, és az erőforrások használata alacsony.

Bizonyos esetekben a magas végrehajtási szám több hálózati oda-vissza utazáshoz vezethet. Az oda-vissza utak befolyásolják a teljesítményt. Hálózati késés és a kiszolgáló alsóbb rétegbeli késése van kitéve.

Például számos adatvezérelt webhely minden felhasználói kérelem hez nagy mértékben hozzáfér az adatbázishoz. Bár a kapcsolatkészletezés segít, a megnövekedett hálózati forgalom és az adatbázis-kiszolgáló feldolgozási terhelése lassíthatja a teljesítményt. Általában, tartsa oda-vissza a minimumra.

A gyakran végrehajtott ("beszédes") lekérdezések azonosítása:

1. Nyissa **meg** az Egyéni lapot a Lekérdezési teljesítmény betekintésben a kijelölt adatbázishoz.
2. Módosítsa a mutatókat **a végrehajtási szám**.
3. Válassza ki a lekérdezések számát és a megfigyelési időközt.
4. A testreszabott nézet megtekintéséhez kattintson **az Ugrás >** gombra.

   ![Lekérdezés-végrehajtási szám](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>A teljesítményhangolási jegyzetek ismertetése

A Lekérdezési teljesítmény betekintésben a munkaterhelés feltárása során előfordulhat, hogy a diagram tetején függőleges vonallal rendelkező ikonok jelennek meg.

Ezek az ikonok jegyzetek. Ezek azt mutatják, teljesítmény ajánlásokat [AZ SQL Database Advisor](sql-database-advisor.md). Ha egy jegyzet fölé viszi az egérmutatót, összesített információkat kaphat a teljesítményre vonatkozó javaslatokról.

   ![Lekérdezés jegyzetelése](./media/sql-database-query-performance/annotation.png)

Ha többet szeretne megtudni, vagy alkalmazni szeretné a tanácsadó ajánlását, válassza az ikont az ajánlott művelet részleteinek megnyitásához. Ha ez egy aktív javaslat, akkor azt azonnal alkalmazhatja a portálról.

   ![Jegyzet részleteinek lekérdezése](./media/sql-database-query-performance/annotation-details.png)

Bizonyos esetekben a nagyítási szint miatt lehetséges, hogy az egymáshoz közeli jegyzetek egyetlen jegyzetbe vannak összecsukva. A Lekérdezési teljesítmény insight ezt csoportjegyzet-ikonként jelöli. A csoportjegyzet ikonjának kiválasztásakor megnyílik egy új panel, amely felsorolja a jegyzeteket.

A lekérdezések és a teljesítményhangolási műveletek korrelációja segíthet a számítási feladatok jobb megértésében.

## <a name="optimize-the-query-store-configuration"></a>A Lekérdezéstároló konfigurációjának optimalizálása

A Query Performance Insight használata közben a következő Query Store hibaüzenetek jelenhetnek meg:

* "A Lekérdezéstároló nincs megfelelően konfigurálva ezen az adatbázison. Kattintson ide, ha többet szeretne megtudni."
* "A Lekérdezéstároló nincs megfelelően konfigurálva ezen az adatbázison. Kattintson ide a beállítások módosításához."

Ezek az üzenetek általában akkor jelennek meg, ha a Query Store nem tud új adatokat gyűjteni.

Az első eset akkor történik, ha a Query Store írásvédett állapotban van, és a paraméterek optimálisan vannak beállítva. Ezt az adattár méretének növelésével vagy a Query Store törlésével javíthatja. (Ha törli a Query Store-t, az összes korábban összegyűjtött telemetriai adat elvész.)

   ![Lekérdezési tároló részletei](./media/sql-database-query-performance/qds-off.png)

A második eset akkor történik, ha a Lekérdezéstároló nincs engedélyezve, vagy a paraméterek nincsenek optimálisan beállítva. Módosíthatja az adatmegőrzési és rögzítési szabályzatot, és engedélyezheti a Query Store-t az [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) vagy az Azure Portal következő parancsainak futtatásával.

### <a name="recommended-retention-and-capture-policy"></a>Ajánlott megőrzési és rögzítési házirend

Az adatmegőrzési házirendeknek két típusa van:

* **Méretalapú**: Ha a házirend **auto**értékre van állítva, automatikusan törli az adatokat, amikor eléri a maximális méretet.
* **Időalapú**: Alapértelmezés szerint ez a házirend 30 napra van állítva. Ha a Query Store-ból elfogy a hely, 30 napnál régebbi lekérdezésadatokat töröl.

A rögzítési házirendet a következőkre állíthatja:

* **Mind**: A Lekérdezéstároló az összes lekérdezést rögzíti.
* **Automatikus**: A Lekérdezéstároló figyelmen kívül hagyja a ritkán előforduló lekérdezéseket és lekérdezéseket, amelyek fordítási és végrehajtási időtartamúak. A végrehajtási szám, a fordítási időtartam és a futásidejű időtartam küszöbértékei belsőleg meghatározottak. Ez az alapértelmezett beállítás.
* **Nincs**: A Query Store leállítja az új lekérdezések rögzítését, de a már rögzített lekérdezések futásidejű statisztikái továbbra is gyűjtésre kerülnek.

Azt javasoljuk, hogy az összes szabályzatot **auto** és a tisztítási szabályzat 30 napra állításával a következő parancsokat az [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) vagy az Azure Portalon. (Cserélje `YourDB` le az adatbázis nevét.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

A Query Store méretének növelése az [SSMS-en](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) vagy az Azure Portalon keresztül egy adatbázishoz való csatlakozással és a következő lekérdezés futtatásával. (Cserélje `YourDB` le az adatbázis nevét.)

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Ezeknek a beállításoknak a alkalmazása végül a Lekérdezési tárolót az új lekérdezések telemetriai adatait gyűjti. Ha azonnal működésbe kell hoznia a Query Store-t, tetszés szerint törölheti a Query Store-t a következő lekérdezés SSMS-en vagy az Azure Portalon keresztül történő futtatásával. (Cserélje `YourDB` le az adatbázis nevét.)

> [!NOTE]
> A következő lekérdezés futtatása törli az összes korábban összegyűjtött figyelt telemetriai adatokat a Query Store.Running the following query will delete all previously collected monitored telemetry in Query Store.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>További lépések

Fontolja meg [az Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) használatát az egy- és készletalapú adatbázisok, rugalmas készletek, felügyelt példányok és példányadatbázisok nagy flottájának speciális teljesítményfigyeléséhez.
