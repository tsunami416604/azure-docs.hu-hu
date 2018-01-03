---
title: "Hozzon létre, riasztások megtekintése és kezelése riasztások használata Azure figyelője – (előzetes verzió) |} Microsoft Docs"
description: "Használja az új Azure riasztások egységes felületet, megtekintése és kezelése a metrika és riasztási szabályok jelentkezzen egyetlen helyről."
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: vinagara
ms.openlocfilehash: e3902ec5fc27c829fa97042d15552c8c895c6408
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="create-view-and-manage-alerts-using-azure-monitor---alerts-preview"></a>Hozzon létre, riasztások megtekintése és kezelése figyelővel Azure - riasztások (előzetes verzió)

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan állíthat be riasztásokat a felületen új riasztások (előzetes verzió) Azure-portálon belül. Riasztási szabály definíciója van három részből áll:
- Cél: Adott Azure-erőforrás, amelyet figyelni
- Feltételek: Adott feltétel, vagy programot, amely jel látható, ha indíthat el művelet
- Művelet: A fogadó egy értesítés - adott hívás küldött e-mailben, SMS, webhook stb.

Riasztások (előzetes verzió) kifejezést használja **napló riasztások** megadásával írhatja le a riasztások alapján egyéni lekérdezés esetén a jel [Azure Naplóelemzés](../log-analytics/log-analytics-tutorial-viewdata.md). A metrika riasztási funkció hívása [közel valós idejű metrika riasztások](monitoring-near-real-time-metric-alerts.md) a meglévő riasztások a felhasználói élmény néven **metrika riasztások** riasztásokban (előzetes verzió). A *metrika riasztások*, egyes erőforrástípusok biztosítanak [többdimenziós metrikák](monitoring-metric-charts.md) az adott Azure-erőforrás és ilyen erőforrás pontosabb további szűrők használata a tehető ezért riasztásai Dimenziók; az ilyen riasztások nevezzük **Multi-Dimensional metrika riasztások**. Az Azure riasztások (előzetes verzió) is a riasztási szabályok és a segítségével felügyelheti azokat egy helyen; egyesített nézetét jeleníti meg beleértve az összes feloldatlan riasztások megtekintése. További tudnivalók funkciói [Azure Alerts(Preview) – áttekintés](monitoring-overview-unified-alerts.md).

> [!NOTE]
> Amíg Azure riasztások (előzetes verzió) biztosít egy új és továbbfejlesztett élmény riasztásokat hoznak létre az Azure-ban. A meglévő [Azure riasztások](monitoring-overview-alerts.md) élmény használható marad.
>

Részletes útmutató a Azure figyelmeztetések (előzetes verzió) használatával a következő részletes van.

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Riasztási szabályt létrehozni és az Azure portál
1. Az a [portal](https://portal.azure.com/), jelölje be **figyelő** és - FIGYELŐ szakaszban válasszon **riasztások (előzetes verzió)**.  
    ![Figyelés](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Válassza ki a **új riasztást szabály** gombra kattintva új riasztás létrehozása, az Azure-ban.
    ![Riasztások hozzáadása](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. A riasztás létrehozása a szakasz a három részből álló mellett látható: *határozza meg a riasztási feltétel*, *határozza meg a riasztás részleteinek*, és *Define művelet csoport*.
    ![Szabály létrehozása](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  A riasztást kiváltó körülmény első segítségével meghatározhatók a **válasszon erőforrás** hivatkozásra, és adja meg a célként megadott erőforrás kiválasztásával. Szűrő megfelelően kiválasztásával szükséges *előfizetés*, *erőforrástípus*, és végül kiválasztása szükséges *erőforrás*. Azure riasztások (előzetes verzió) lehetővé teszi a különböző típusú egyes felhasználói felületen keresztüli; riasztások létrehozásához a szükségeskonfiguráció-riasztás alapján válassza ki a következő lépés szerint:
    - A **metrika riasztások**: lépésekkel 5-7; majd folytassa a 11
    - A **napló riasztások**, Ugrás a 8. lépés-től

5. *Metrika riasztások*: Győződjön meg arról **erőforrástípus** kiválasztott platform vagy monitor szolgáltatás (eltérő *Naplóelemzési*), majd egyszer megfelelő **erőforrás** van Kattintson a kiválasztott *végzett* gombra kattintva visszatérhet a riasztás létrehozása. Ezután használja a **adja meg a feltételeket** jel beállítások, az állapotfigyelő szolgáltatás és a felsorolt - típus közül az adott jel kiválasztása gomb, ami a korábban kiválasztott erőforrás érhetők el.
    ![Válasszon ki egy erőforrástípust](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]
    > Csak gyors riasztások bevezetett új metrika képességekkel szereplő jel, metrikák platform szolgáltatásból
  

6. *Metrika riasztások*: a kijelölt jel riasztások logikát is meg kell adni. Referenciaként jel régebbi adatok végeznünk a az ablak használatával beállítás mellett látható **előzmények megjelenítése**, az elmúlt hat órán át az előző hét különböző. A helyen, a képi megjelenítés **riasztási logika** a megjelenített beállítások feltétel, a összesítési és végül küszöbérték lehet kiválasztani. A megadott logikai előnézete, mint a feltétel látható jel előzmények, valamint a képi megjelenítés lejátszásához, ha a figyelmeztetés akkor rendelkezik. Végül adja meg, milyen időtartamig, a riasztás kell keresnie a megadott feltétel kiválasztásával a **időszak** együtt, hogy a riasztás milyen gyakran fusson kiválasztásával beállítás **gyakoriság**.
    ![A metrika jel logikát konfigurálása](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Metrika riasztások*: Ha jel metrika, az értesítési ablakban szűrhetők adatpontok vagy a dimenziók használatával az említett az Azure erőforrás. Metrika riasztások hasonló, jel előzményeinek a képi megjelenítés választható ki időtartamot megállapításával **előzmények megjelenítése** legördülő menüből. Emellett a kiválasztott metrika dimenziók választható ki kiszűréséhez szükség idősor; a dimenziók kiválasztása nem kötelező, és felfelé történő öt dimenziók használhatók. **Riasztási logika** a megjelenített beállítások feltétel, a összesítési és végül küszöbérték lehet kiválasztani. A megadott logikai előnézete, mint a feltétel látható jel előzmények, valamint a képi megjelenítés lejátszásához, ha a riasztás az elmúlt elindítva lenne. Végül adja meg, milyen időtartamig, a riasztás kell keresnie a megadott feltétel kiválasztásával a **időszak** együtt, hogy a riasztás milyen gyakran fusson kiválasztásával beállítás **gyakoriság**.
    ![Többdimenziós metrika jel logikát konfigurálása](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Riasztások jelentkezzen*: Győződjön meg arról **erőforrástípus** egy elemzés forrása, például a *Naplóelemzési*, majd egyszer megfelelő **erőforrás** van a választott kattintson *Végzett* gombra. Ezután használja a **adja meg a feltételeket** elérhető, az erőforrás- és a jel listából jel beállítások listájának megtekintése gombra **egyéni napló keresés** beállítás.
   ![Válasszon ki egy erőforrástípust - egyéni napló keresése](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

9.  *Riasztások jelentkezzen*: a kijelölt lekérdezés, mert így is fel kell tüntetni **keresési lekérdezés** mezőben; Ha a lekérdezés szintaxisa nem megfelelő a mező piros hibaüzenet jelenik meg. Ha a lekérdezés szintaxisát,-hivatkozás jelenik meg a megadott lekérdezés régebbi adatok végeznünk a időszak utolsó hat órán át az előző hét kapcsolóval grafikon. 
   ![Riasztási szabály konfigurálása](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

    > [!NOTE]
    > Korábbi adatok vizuális Ha a lekérdezés eredményében van-e idő – részletek csak látható. Ha a lekérdezés eredménye az összegzett adatok vagy az adott oszlop értékének - azonos szinguláris rajzot útvonalként megjelenő 

10.  *Riasztások jelentkezzen*: A helyen, a képi megjelenítés **riasztási logika** a megjelenített beállítások feltétel, a összesítési és végül küszöbérték lehet kiválasztani. Végül adja meg a programot az időt a megadott feltétel értékeléséhez használatával **időszak** lehetőséget. Milyen gyakran riasztás kiválasztásával fusson együtt **gyakoriság**.
A **napló riasztások** riasztások alapulhat:
   - *A rekordok száma*: egy riasztás akkor jön létre, ha a lekérdezés által visszaadott rekordok száma nagyobb vagy kisebb, mint a megadott érték.
   - *Metrika mérési*: egy riasztást hoz létre, ha minden *értéket összesítő* az eredmények között meghaladja a megadott küszöbértéknél, és *szerint csoportosítva* választott érték. A riasztás száma a küszöbérték elérése esetén a választott időszakban hányszor. Megadhatja a teljes megszegése behatolások tetszőleges kombinációja eredmények beállított vagy egymást követő megszegése megkövetelése, hogy a behatolás kell-e magukat az egymást követő minták között.

   További információ [napló riasztások és azok típusát](monitor-alerts-unified-log.md)

    > [!TIP]
    > Jelenleg a riasztás (előzetes verzió) – napló keresési értesítések is igénybe vehet egyéni *időszak* és *gyakoriság* perc értéket. Érték 5 perc és 1440 perc 24 óra (vagyis) változhat. Ezért ha azt szeretné, hogy a riasztás időszak mondja ki a három óra, konvertálja a perc – 180 perc, a használat előtt

11. A második lépésben adja meg a nevét a riasztásból a **riasztásiszabály-névnek** mezőjét, valamint egy **leírás** és részletesen leírja a részletekről a riasztás és **súlyossági** értéket a a megadott beállításokat. Ezek az adatok összes értesítő e-mailek, értesítések, és az Azure-figyelő által végzett leküldéses használja fel újra. Emellett felhasználó azonnal aktiválni a létrehozásakor a riasztási szabály megfelelően való átváltással választhat **engedélyezése a szabály létrehozásakor** lehetőséget.

A **napló riasztások** csak, néhány további funkciók érhető el a riasztás részleteit:
- *Végfelhasználói értesítések letiltása*: Ha bekapcsolja a riasztási szabály letiltás, a szabály műveletek le vannak tiltva, egy meghatározott ideig új riasztás létrehozása után. A szabály mindig fut, és riasztási rekordokat hoz létre a megadott feltétel teljesülése. Így Ön idő duplikált műveletek futtatása nélkül a probléma elhárítása érdekében.
    ![Napló riasztásokat tiltja le a riasztások](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

12. Adja meg a harmadik és utolsó lépésként, ha van ilyen **művelet csoport** riasztási feltétel teljesülésekor a riasztási szabály bekövetkezhet kell. Válassza a meglévő művelet csoport riasztáshoz, vagy hozzon létre egy új művelet csoportot. A következők szerint kijelölt művelet csoport eseményindító Azure lesz riasztás esetén: email(s) küldése, SMS(s) küldeni, Webhook(s) hívja, szervizelése használata Azure Runbookok, leküldéses ITSM eszköz, stb. További információ [művelet csoportok](monitoring-action-groups.md).

A **napló riasztások** további funkciókkal felülbírálhatja az alapértelmezett műveletek érhető el:
- *E-mailes értesítés*: az e-mailben művelet csoport keresztül küldött felülbírálások tulajdonos. Az üzenet törzse nem módosítható.
- *Egyéni Json-adattartalmat tartalmaznak*: felülírja a webhook Json művelet csoportok által használt, és helyette az alapértelmezett adattartalom cserélje le egy egyéni adattartalom ![művelet felülbírálja a napló riasztások](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

13. Ha a mezők egyike sem érvényes, és a zöld osztásjelek a **riasztási szabályt létrehozni** gombbal rákattintva és a riasztás jön létre az Azure-figyelő - riasztások (előzetes verzió). Az összes riasztás a riasztások (előzetes verzió) irányítópultján tekintheti meg.
    ![A szabály létrehozása](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

Néhány percen belül a riasztás aktív, és elindítja a leírt módon.

## <a name="view-your-alerts-in-azure-portal"></a>A riasztás megtekintése az Azure-portálon
1. Az a [portal](https://portal.azure.com/), jelölje be **figyelő** és - FIGYELŐ szakaszban válasszon **riasztások (előzetes verzió)**.  

2. A **(előzetes verzió) irányítópult riasztások** megjelenik - az összes Azure-riasztás egyesített és megjelenő egyes board amelynek ![riasztási irányítópult](./media/monitoring-overview-unified/alerts-preview-overview.png)
1. Az irányítópult felső balról jobbra, egy pillanat alatt az alábbi - jelenítse meg a részletes rákattintva jeleníti meg:
    - *Riasztások indította*: A riasztások számát jelenleg logika teljesül, és a indította állapota
    - *A riasztási szabályok teljes*: A szám létre riasztási szabályok és az alatta szöveggel, a szám, amellyel jelenleg engedélyezve vannak
2. Égetett riasztások listája látható, amely a felhasználó a részletek megtekintéséhez kattintson
3. A megállapítás specifikus segítségnyújtás a riasztások; egy beállításokkal a legördülő lista felül adott szűréshez *előfizetés, erőforráscsoport és/vagy az erőforrás*. További, az összes feloldatlan riasztási, használatát a *szűrő riasztás* beállítás található a megadott kulcsszó - adott megfelelő riasztások *neve, riasztás feltételek, az erőforráscsoportot, és tároló-erőforrás*

## <a name="managing-your-alerts-in-azure-portal"></a>Azure-portálon a riasztások kezelése
1. Az a [portal](https://portal.azure.com/), jelölje be **figyelő** és - FIGYELŐ szakaszban válasszon **riasztások (előzetes verzió)**.  
2. Válassza ki a **szabályok kezelése** gombra kattint a felső sávon, navigáljon a szabály felügyeleti szakasz - létrehozott összes riasztási szabályok listázó; többek között a riasztásokat, amelyek le vannak tiltva.
3. Adott riasztási szabályok megkereséséhez egy használható a legördülő lista szűrők felül, amelyek lehetővé teszik a shortlist riasztási szabályok adott *előfizetés, csoportok és/vagy erőforrás*. Azt is megteheti a keresési ablaktáblán a riasztási szabály lista fölött jelölték meg *riasztások szűrése*, egy biztosíthat kulcsszóval, amely szerepel *riasztás nevét, a feltétel és a cél erőforráson*; csak megjelenítése a megfeleltetési szabályokról. 
   ![Riasztás szabályok kezelése](./media/monitoring-overview-unified/alerts-preview-rules.png)
4. Megtekintéséhez, vagy módosíthatja az adott riasztási szabályokat, kattintson a nevére, amelyek akkor jelennek meg egy kattintható hivatkozássá.
5. Riasztás definiált látható - három fázis szerkezete: 1.) a riasztás 3) művelet feltétel 2) a riasztás Részletcsoport. **Cél feltételek** rákattintva módosítsa a riasztás logika vagy egy új feltétel az ikon használata a korábbi logika törlése után lehet hozzáadni. Hasonlóképpen, a riasztás részletes adatait tartalmazó részben - **leírás** és **súlyossági** módosítható. És a művelet csoport módosítható, vagy egy új kialakított is létrehozhatja, ha a riasztás történő a **új művelet csoport** gomb ![riasztást szabály módosítása](./media/monitor-alerts-unified/AlertsPreviewEdit.png)
6. Az ablak tetején használ, a riasztás módosításai lehet tükröző többek között: *mentése* bármely riasztáshoz, végrehajtott változtatások véglegesítéséhez *elvetése* térhet vissza riasztás végrehajtott módosítások véglegesítése nélkül *letiltása*  inaktiválja a riasztást - elteltével azt már nem fut vagy bármilyen műveletet váltja ki. És végezetül *törlése* végleg eltávolítja a teljes riasztási szabály az Azure-ból.

## <a name="next-steps"></a>További lépések

* További információ az új [majdnem valós idejű metrika riasztások (előzetes verzió)](monitoring-near-real-time-metric-alerts.md)
* Első egy [metrikák gyűjtemény áttekintése](insights-how-to-customize-monitoring.md) ellenőrizze, hogy a szolgáltatás elérhető, és a gyors.
* További tudnivalók [napló riasztásait az Azure-riasztások (előzetes verzió)](monitor-alerts-unified-log.md)
