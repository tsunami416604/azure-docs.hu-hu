---
title: Létrehozása, megtekintése és kezelése az Azure Monitor használatával riasztások
description: Egységes Azure-riasztások új kezelőfelülete használatával hozhat létre, megtekintése és kezelése a metrika és naplóriasztási szabályok egyetlen helyről.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: a913075c051c6b784495917b7edbd7340254a212
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952630"
---
# <a name="create-view-and-manage-alerts-using-azure-monitor"></a>Hozzon létre, riasztások megtekintése és kezelése az Azure Monitor használatával  

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan állíthat be riasztásokat az új riasztások belül az Azure portal felhasználói felületének használatával. Riasztási szabály definíciója van három részből áll:
- Cél: Adott Azure-erőforrás, amely figyelni
- Feltételek: Adott feltétel vagy logika, amely jel látható, ha indíthat el a művelet
- Művelet: Adott hívás az értesítés - egyetlen külső eseményfogyasztó küldött e-mailben, SMS, webhook stb.

Azure-riasztások is biztosít egy egységesített nézetben a riasztási szabályok és kezelhetik azokat egyetlen helyen; beleértve az összes feloldatlan riasztások megtekintése. További tudnivalók a funkciói [Azure-riasztások – áttekintés](monitoring-overview-unified-alerts.md).

A riasztás kifejezést használja **Naplóriasztások** írja le a riasztásokhoz, ahol jel alapuló egyéni lekérdezés [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) vagy [Application Insights](../application-insights/app-insights-analytics.md). A [új metrikaalapú riasztási funkció](monitoring-near-real-time-metric-alerts.md) Ez a riasztás képes [többdimenziós metrikák](monitoring-metric-charts.md) az adott Azure-erőforrásokhoz. A riasztásokat az ilyen erőforrás további szűrők használhatók a dimenziók létrehozásának **Multi-Dimensional metrika riasztások**.


> [!NOTE]
> Azure-riasztások közben biztosít egy új, továbbfejlesztett élmény a riasztások létrehozása az Azure-ban. A meglévő [riasztások (klasszikus)](monitoring-overview-alerts.md) élmény továbbra is használható.
>

Részletes tovább lépésenkénti útmutató, amellyel Azure-riasztások segítségével.

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Riasztási szabály létrehozása az Azure Portallal
1. Az a [portál](https://portal.azure.com/), jelölje be **figyelő** , és válassza ki a MONITOR terület - **riasztások**.  
    ![Monitorozás](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Válassza ki a **Új riasztási szabály** gombra kattintva hozzon létre egy új értesítés az Azure-ban.
    ![Riasztás hozzáadása](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. A riasztás létrehozása szakaszt a három részből álló mellett látható: *riasztási feltétel megadása*, *riasztás részleteinek megadása*, és *definiálása műveletcsoport*.

    ![Szabály létrehozása](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  A riasztási feltétel megadása használatával a **erőforrás kiválasztása** hivatkozásra, és adja meg a cél erőforrás kijelölésével. Szűrő kiválasztásával a *-előfizetéssel, * szükséges erőforrás típusa, és végül kiválasztásával *erőforrás*.

    >[!NOTE]

    > Ellenőrizze a kiválasztott erőforrás elérhető a jeleket, folytatás előtt.

    ![Erőforrás kiválasztása](./media/monitor-alerts-unified/Alert-SelectResource.png)

 A felhasználói felület különféle típusú riasztásokhoz létre egyetlen helyen teszi lehetővé. A kívánt riasztás típusa alapján válassza ki a következő lépéssel, mint:

    - A **metrika riasztások**: lépésekkel 5 – 7; majd folytassa a 11
    - A **Naplóriasztások**, lépjen a 8. lépés.

    > [!NOTE]

    > A tevékenységnapló-riasztások is támogatottak, de az előzetes verzióban érhetők el. [További információk](monitoring-activity-log-alerts-new-experience.md).

5. *Metrikákhoz kapcsolódó riasztások*: Győződjön meg arról **erőforrástípus** jeltípus szerint a kiválasztott **metrika**, majd egyszer megfelelő **erőforrás** van kiválasztva, kattintson  *Kész* gombra kattintva térjen vissza a riasztás létrehozásához. Ezután használja a **adja meg a feltételeket** gombra az adott jel jel beállítások, az állapotfigyelő szolgáltatás és a felsorolt - típus közül választhat, amely a korábban kiválasztott erőforrás érhető el.

    ![Erőforrás kiválasztása](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  Az összes [közel valós idejű riasztások](monitoring-near-real-time-metric-alerts.md) figyelő szolgáltatással kompatibilis erőforrások felsorolt **Platform** és típusa, jelezze **metrika**

6. *Metrikákhoz kapcsolódó riasztások*: jel a kijelölt riasztási logika is meg kell adni. Referenciaként előzményadatok jel látható a kapcsolóval, hogy az idő ablak használatával a Teljesítménybeállítások **előzmények megjelenítése**, az elmúlt hét különböző az elmúlt hat óra. A helyen, a Vizualizáció **riasztási logika** kiválaszthatók a megjelenített beállítások a feltétel, az összesítés, végül a küszöbértéket. A megadott logikai előzetesként látható a feltétel jel előzményeit, valamint a Vizualizáció jelzi a riasztás akkor rendelkezik. Végül adja meg, milyen időtartam, a riasztás derítheti fel a megadott feltétel tárházából választva a **időszak** lehetőséget, és milyen gyakran fusson a riasztás a kiválasztásával **gyakorisága**.

    ![Metrika jellogika konfigurálása](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Metrikákhoz kapcsolódó riasztások*: Ha jel metrikát, akkor a riasztás ablak az említett Azure-beli erőforráshoz több adatpont vagy dimenziók használatával szűrhetők. 

    a. Válasszon egy időtartamot a **előzmények megjelenítése** különböző bizonyos idő megjelenítése a legördülő listából. Választhat a dimenziók esetében a támogatott mérőszámok szűrése egy time Series; a dimenziók kiválasztása nem kötelező, és akár az öt dimenziók használhatók. 

    b. **Riasztási logika** kiválaszthatók a megjelenített beállítások *feltétel*, *, összesítési és *küszöbérték*. A megadott logikai előzetesként látható a feltétel jel előzményeit, valamint a Vizualizáció lejátszásához, ha a riasztás az elmúlt elindítva lenne. 

    c. Adja meg az időtartamot, válassza a **időszak** milyen gyakran együtt a riasztás futásának kiválasztásával **gyakorisága**.

    ![A többdimenziós metrikával jellogika konfigurálása](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Naplóriasztások*: Győződjön meg arról **erőforrástípus** van egy analytics-forrásokhoz, például *Log Analytics* vagy *Application Insights* és típusa, jelezze **napló** , majd egyszer megfelelő **erőforrás** van kiválasztva, kattintson a *kész*. Következő az a **adja meg a feltételeket** érhető el, az erőforrás és a jel listából jel beállítások listájának megtekintése gombra **egyéni naplóbeli keresés** beállítást a kiválasztott log figyelő szolgáltatásokhoz, mint az *napló Analytics* vagy *az Application Insights*.

   ![Válasszon ki egy erőforrást – egyéni keresés](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Lista importálhat analytics-lekérdezéshez, mivel jel típusa – riasztások **Log (mentett lekérdezés)**, a fenti ábrán látható módon. Így a felhasználók tökéletes a lekérdezést az Analyticsben és mentheti későbbi használatra, a riasztások – olvashat részletesebben használatával érhető el lekérdezés mentése [a log analytics naplóbeli keresés használatával](../log-analytics/log-analytics-log-searches.md) vagy [megosztott lekérdezést az application insights szolgáltatásban Analytics](../log-analytics/log-analytics-overview.md). 

9.  *Naplóriasztások*: a kijelölt riasztási lekérdezés is fel kell tüntetni **keresési lekérdezés** mező; Ha a lekérdezés szintaxisa helytelen a mezőben az hiba vörös színnel jelenik meg. Ha a lekérdezés szintaxisa helyes - referenciaként előzményadatok a megadott lekérdezés látható gráfként kapcsolóval, hogy a Teljesítménybeállítások az időtartomány utolsó 6 óra múlt héten.

 ![Riasztási szabály konfigurálása](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > Előzményadatok vizualizációt a lekérdezés eredményében van-e idő – részletek csak látható. Ha a lekérdezés eredménye összegzett adatok vagy az adott oszlop értékének - azonos jelenik meg egyes számú rajzot.

    >  Az Application insights használatával Naplóriasztások Metrikamérés típusú, mely adott változó az adatok csoportosításához használatával megadhatja a **az összesített** lehetőséget; az alábbi képen szemléltetett módon:

    ![a beállítás az összesítés](./media/monitor-alerts-unified/aggregate-on.png)

10.  *Naplóriasztások*: A helyen, a Vizualizáció **riasztási logika** kiválaszthatók a megjelenített beállítások a feltétel, az összesítés, végül a küszöbértéket. Végül adja meg a logikai időt a megadott feltétel értékelése használatával **időszak** lehetőséget. Milyen gyakran fusson a riasztás a kiválasztásával együtt **gyakorisága**.
A **Naplóriasztások** riasztások alapjául is:
   - *Rekordok száma*: egy riasztást küld, ha a lekérdezés által visszaadott rekordok számát vagy nagyobb vagy kisebb, mint a megadott érték.
   - *Metrikus egység*: riasztás jön létre, ha egyes *összesített érték* az eredmények között meghaladja a megadott küszöbérték és *szerint csoportosítva* választott érték. Az aktivista álláspontokkal riasztás a küszöbérték elérése esetén a választott időszakban hányszor. Összes incidens bármely kombinációjával adatszivárgás is megadhat az eredménykészletet vagy egymás utáni incidensek megkövetelése, hogy az illetéktelen behatolásokat fel kell a rendszer egymást követő minták között. Tudjon meg többet [riasztások és azok típusát](monitor-alerts-unified-log.md).

    > [!TIP]
    > Jelenleg a riasztások - naplóbeli keresési értesítések is igénybe vehet egyéni *időszak* és *gyakorisága* perc értéket. Érték 5 perc és 1440 perc 24 óra (vagyis) változhat. Tehát ha azt szeretné, hogy a riasztási időszakot, például: három óra, átalakíthatja, perc – 180 perc, a használat előtt

11. A második lépésben adja meg a riasztás a nevét a **riasztási szabály neve** mezőt, valamint egy **leírása** -a riasztás adatait részletezve és **súlyossági** értéket a a megadott beállításokat. Ezeket az adatokat az összes értesítő e-mailek, értesítések és az Azure Monitor által végzett leküldéses újra felhasználhatók. Ezenkívül felhasználó dönthet azonnal aktiválni a riasztási szabály létrehozása a megfelelő átállításával **engedélyezése a szabály létrehozásakor** lehetőséget.

    A **Naplóriasztások** csak néhány további funkcióval érhető el a riasztás részleteit:

    - **Riasztások mellőzésének időtartama**: bekapcsolja a riasztási szabály letiltásra, amikor a szabály műveletek le vannak tiltva, egy meghatározott ideig új riasztás létrehozása után. A szabály továbbra is fut, és riasztási rekordokat hoz létre a megadott feltétel teljesülése. Ami lehetővé teszi, ideje a probléma javításához ismétlődő műveletek futtatása nélkül.

        ![Naplóriasztásokra vonatkozó figyelmeztetések mellőzése](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

        > [!TIP]
        > Adjon meg egy mellőzése riasztási érték nagyobb, mint a riasztásból értesítéseket le lesz állítva, átfedés nélkül gyakorisága

12. A harmadik és egyben utolsó lépésként adja meg az esetleges **műveletcsoport** aktiválódik a riasztási szabály a riasztási feltétel teljesülése esetén kell. Válassza ki alert bármely meglévő műveletcsoport, vagy hozzon létre egy új művelet. A következők szerint kiválasztva műveletcsoportot, eseményindító Azure rendszer riasztás esetén: email(s) küldése, SMS(s) küldeni, webhook meghívása, javíthatja a használatával az Azure-forgatókönyvek, leküldéses az ITSM-eszközhöz, stb. Tudjon meg többet [Műveletcsoportok](monitoring-action-groups.md).

    A **Naplóriasztások** néhány további funkcióval érhető el az alapértelmezett műveletek felülbírálása:

    - **E-mailes értesítés**: felülbírálások *e-mail tárgya* az e-mailben küldött műveletcsoport; Ha egy vagy több e-mail-művelet az említett műveleti csoport létezik. Az üzenet törzsét nem módosítható, és ez a mező **nem** e-mail-cím.
    - **Egyéni Json-adattartalom tartalmaznak**: felülbírálja a webhook Műveletcsoportok; által használt JSON-Beállításfájlt, ha az említett műveleti csoport szerepel egy vagy több webhook-műveletek. Felhasználó úgy adhat meg a JSON társított műveletcsoport; konfigurált összes webhookok használandó formátumát a webhook-formátumokról további információkért lásd: [Naplóriasztásokra vonatkozó webhook művelettel](monitor-alerts-unified-log-webhook.md). Teszt Webhook lehetőség a formátum és a JSON-mintának célra általi feldolgozáshoz, és ezt a beállítást, a címkével ellátott webszolgáltatásokban csak **tesztelés** céljából.

        ![Naplóriasztásokra vonatkozó művelet felülbírálások](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

        > [!NOTE]
        > A **Webhook tesztelése** működéséhez lehetőséget, a végpont támogatnia kell [idegen eredetű erőforrások megosztása (CORS)](https://www.w3.org/TR/cors/) és a felhasználó a CORS-proxy segítségével "Nincs Access-Control-Allow-Origin fejléc" problémák megkerülésében

13. Ha érvényes az összes mező kitöltése és a zöld osztásjelek a **riasztási szabály létrehozása** gombra, ha rájuk kattint, és a egy riasztás jön létre az Azure Monitor - riasztásokat. A riasztások irányítópult minden riasztás is megtekinthetők.

    ![A szabály létrehozása](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    Néhány percen belül a riasztás aktív, és elindítja a fent leírtaknak megfelelően.

## <a name="view-your-alerts-in-azure-portal"></a>A riasztások megtekintése az Azure Portalon

1. Az a [portál](https://portal.azure.com/), jelölje be **figyelő** , és válassza ki a MONITOR terület - **riasztások**.  

2. A **riasztások irányítópult** jelenik meg – az összes Azure-riasztások egységes és megjelenik egy egyetlen táblára viselkedésmintáit ![riasztási irányítópult](./media/monitoring-alerts-unified-usage/alerts-preview-overview.png)
3. Felső balról jobbra az irányítópult megjeleníti egy pillanat alatt a következő – amely kattintva részletes felsorolását lásd:
    - *Aktivált riasztások*: a riasztások számának jelenleg logikai teljesül, és az aktivált állapota
    - *Riasztási szabályok teljes*: és az alatta szöveggel, a szám, amely jelenleg engedélyezve van a létrehozott riasztási szabályok száma 
    

        > [!NOTE]
        > Annak biztosítása érdekében a konzisztens az application insights és a log analytics; a riasztások többek között az összes aktivált riasztások részletei az irányítópulton [Bővített egységes riasztás (előzetes verzió)](monitoring-overview-unified-alerts.md#enhanced-unified-alerts-public-preview) kell használni
  
  
4. Az összes aktivált riasztások listája látható, amely a felhasználó kattintson ide a részletekért
5. Keresés adott segítségnyújtás a riasztások; egyet a legördülő menü Beállítások felül használhatja a szűréshez megadott *előfizetés, erőforráscsoport és/vagy erőforrás*. További, a feloldatlan bármely riasztás, a használatát a *szűrő riasztás* beállítás található a megadott kulcsszó - a meghatározott megfelelő riasztások *nevét, a riasztás feltételek, erőforráscsoport, és a célként megadott erőforrás*

## <a name="managing-your-alerts-in-azure-portal"></a>Az Azure Portalon a riasztások kezelése
1. Az a [portál](https://portal.azure.com/), jelölje be **figyelő** , és válassza ki a MONITOR terület - **riasztások**.  
2. Válassza ki a **szabályok kezelése** gombot a felső sávon, nyissa meg a szabály felügyeleti szakaszra - létrehozott összes riasztási szabályt listázó; többek között a riasztásokat, amelyek le vannak tiltva.
3. A meghatározott riasztási szabályok megkereséséhez az egyik vagy a legördülő szűrők használatával felül, amelyek lehetővé teszik a riasztási szabályok shortlist konkrét *előfizetés, erőforráscsoport és/vagy erőforrás*. Azt is megteheti a keresési ablak a riasztási szabály lista fölött jelölték meg *riasztások szűrése*, egy biztosíthat kulcsszóval, amely szerepel *riasztás nevét, a feltétel és a célként megadott erőforrás*; csak megjelenítése megfeleltetési szabályoknak.
   ![A riasztás szabályok kezelése](./media/monitoring-alerts-unified-usage/alerts-preview-rules.png)
4. Megtekintéséhez, illetve konkrét riasztási szabály módosításához kattintson annak nevére, amelyek akkor jelennek meg az egy kattintható hivatkozás.
5. Meghatározott riasztás jelenik meg – található három fázisban szerkezete: 1) a riasztási feltétel 2) a riasztás részletei (3) műveleti csoport. **Cél feltételek** kattintva módosíthatja a riasztási logika és a egy új feltétel ikonra a használata a korábbi logika törlése után is hozzáadhatók. Hasonlóképpen, a riasztás részletes adatait tartalmazó részben - **leírás** és **súlyossági** módosíthatók. És a műveletcsoport is módosítható, vagy egy új kialakított is létrehozhatja, ha a riasztási használatával a **új műveletcsoport** gombra.

   ![Riasztási szabály módosítása](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. Használja az ablak tetején, a riasztás módosításai lehet reflektív többek között: **mentése** bármely változtatást a riasztást, véglegesíti **elveti** , lépjen vissza a riasztás végzett módosítások végrehajtása nélkül **letiltása**  inaktiválja a riasztást – amely után már nem fut vagy bármilyen műveletet aktivál. Végezetül pedig **törlése** végleg eltávolítja a teljes riasztási szabály az Azure-ból.

## <a name="next-steps"></a>További lépések

- További információ az új [közel valós idejű metrikákhoz kapcsolódó riasztások](monitoring-near-real-time-metric-alerts.md)
- Get- [metrikák gyűjtési áttekintése](insights-how-to-customize-monitoring.md) , győződjön meg arról, hogy szolgáltatása elérhető és válaszkész.
- Ismerje meg [Naplóriasztások az Azure-riasztások](monitor-alerts-unified-log.md)
- [További információ a riasztások (előnézet) élmény tevékenységnapló-riasztások](monitoring-activity-log-alerts-new-experience.md)
