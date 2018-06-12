---
title: Egyesített riasztásait az Azure-figyelő
description: Az Azure-ban egyesített olyan riasztásokat, amelyek lehetővé teszik kezelését leírása riasztást, és szabályok riasztást Azure-szolgáltatásokhoz.
author: manishsm-msft
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: mamit
ms.component: alerts
ms.openlocfilehash: ff2650ec7d4c2c1fffd57176327b56199335fa9d
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264816"
---
# <a name="unified-alerts-in-azure-monitor"></a>Egyesített riasztásait az Azure-figyelő

## <a name="overview"></a>Áttekintés

> [!NOTE]
>  Egy új, egységes riasztási kezelőfelülettel, amellyel felügyelheti a több előfizetéssel riasztást, és bemutatja a riasztás állapota és intelligens csoportok nyilvános előzetes verziójában jelenleg rendelkezésre áll. Tekintse meg a [utolsó szakaszban ismertetett](#enhanced-unified-alerts-experience-public-preview) leírását ez fokozott élményt, és a folyamat engedélyezve van.


Ez a cikk az Azure-figyelő riasztási egységes élményt ismerteti. A [előző riasztási élmény](monitoring-overview-alerts.md) érhető el a **riasztások (klasszikus)** beállítást az Azure-figyelő menüből. 

## <a name="features-of-the-unified-alert-experience"></a>A riasztási egységes élmény jellemzői

Az egységes felhasználói élmény a klasszikus felhasználói élmény van a következő előnyöket biztosítja:

-   **Értesítési rendszer jobb**: riasztások használata az egységes [művelet csoportok]( https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups), amely megnevezett értesítések és a műveletek a több riasztás is használhatnak. 
- **Az egységes jelentéskészítő környezetét** - kezelheti a riasztásokat, és a naplókat riasztás szabályainak metrikákat, és tevékenységet naplózni Azure figyelő, Naplóelemzés és az Application Insights egy helyen. 
- **Nézet indította Naplóelemzési riasztásait az Azure portálon** -Naplóelemzési riasztásai más, a más forrásokból származó riasztások megtekintéséhez az Azure portálon. Korábban ezek volt külön portált.
- **Fired riasztások és a riasztási szabályok** -riasztási szabályok most különböztetni a riasztásokat. Riasztási szabály olyan feltétel, amely riasztást meghatározását. Riasztást egy riasztási szabály indítási példánya.
- **Jobb munkafolyamat** -a jelentéskészítő környezetét egyesített riasztás végigvezeti Önt a riasztási szabály konfigurálásának lépésein.
 
Metrika riasztások klasszikus metrika riasztások rendelkezik a következő fejlesztéseket kínálja:

-   **Továbbfejlesztett késés**: metrika riasztások egy percenként gyakorisággal futtatható. Klasszikus metrika riasztások, 5 perces gyakorisággal végezni. Napló riasztásokat továbbra is hosszabb idő miatt egy percet vesz igénybe tölti be a naplók késleltetés van. 
-   **A többdimenziós metrikák támogatása**: figyelmeztetik a dimenzionális mérőszámokat, hogy lehetővé teszi a figyelheti a metrika egy adott példányához.
-   **Metrika feltételek teljesebb körű vezérlése**: gazdagabb riasztási szabályok, amelyek támogatják a maximális, minimális, átlagos, és az összes érték a mérőszámok figyelési adhat meg.
-   **Egyszerre több metrikák figyelését**: legfeljebb két metrikák kezelhető egyetlen szabállyal figyelheti. Riasztást vált ki, ha mindkét metrikák megsértik a megfelelő küszöbértéket a megadott időszakban.
-   **A naplók metrikák** (korlátozott nyilvános előzetes verzió): bizonyos adatokat, amelyek Log Analytics mostantól napló kicsomagolja és Azure figyelő metrikák alakítja át és más metrikákkal hasonlóan a majd riasztást. 


## <a name="alert-rules"></a>Riasztási szabályok
A riasztások egységes élmény használja a következő fogalmakat riasztási szabályok külön riasztásaiból közben egységes a szerzői műveletekhez élmény különböző riasztástípusok között.

| Elem | Meghatározás |
|:---|:---|
| Riasztási szabály | Riasztás létrehozása a feltétel meghatározása. Kártyából álló egy _célerőforrás_, _jel_, _feltételek_, és _logika_. Riasztási szabály csak akkor aktív, ha van egy _engedélyezett_ állapotát.
| Célzott erőforrás | Határozza meg a konkrét erőforrásokat, és jelzi a riasztások érhető el. A cél lehet az Azure-erőforrásokkal.<br>Példák: virtuális gép, tárfiókot, virtuálisgép-méretezési csoport, a Naplóelemzési munkaterület, Application Insights-erőforrás |
| Jel | A célerőforrás által kibocsátott adatainak forrását. Támogatott jel típusok *metrika*, *tevékenységnapló*, *Application Insights*, és *napló*. |
| Feltételek | Kombinációja _jel_ és _logika_ alkalmazza a cél erőforráson.<br>Példák: Százalékos CPU > 70 % kiszolgáló válaszideje > 4 ms, a napló eredményszám lekérdezése a > 100 stb. |
| Logika | Felhasználó által meghatározott logikát, ellenőrizze, hogy a jel belül-e a tartomány és-értékek várt. |
| Műveletek | A riasztás nézetnek indításakor végrehajtandó művelet. Több művelet akkor fordulhat elő, ha egy riasztás akkor következik be. Ezek a riasztások művelet csoportokat támogatja.<br>Példák: e egy e-mail címet, hívja a webhook URL-CÍMÉT. |
| A figyelőre érvényes feltétel | Azt jelzi, hogy a feltétellel, hogy létrehozott egy metrika riasztást később megoldódott. Metrika riasztási szabályok egy adott metrika rendszeres időközönként mintát. Ha a riasztási szabályban megadott feltétele teljesül, majd egy új riasztás hozza létre Fired feltétele.  Amikor a metrika van mintát újra, ha a megadott továbbra is feltétele teljesül, akkor semmi nem történik.  Ha azonban a feltétel nem teljesül, a riasztás Megoldva esetén megváltozik. A megadott feltétele teljesül, majd egy másik riasztás jön létre Fired feltétele a következő újraindításkor. |


## <a name="alert-pages"></a>Riasztások lap
Egyesített riasztások megtekintése és kezelése az Azure riasztások egy helyen adja meg. A következő szakaszok ismertetik az egységes élmény egyes lapjainak funkcióit.

### <a name="alerts-overview-page"></a>Riasztások – áttekintés oldalra
**Riasztások** – Áttekintés lapon látható az összes égetett riasztások összesített összegzését, és az összes engedélyezett riasztási szabályok. Az előfizetések, illetve a szűrő paramétereit frissíti az összesítések, és a riasztások listája következik be.

 ![riasztások – áttekintés](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>A riasztási szabályok kezelése
**Szabályok** egy egyetlen lap minden riasztási szabályok kezelése az Azure-előfizetések között. Minden riasztási szabályokat sorolja fel, és tároló erőforrásait, az erőforráscsoportok, a szabály neve vagy a állapota alapján rendezhető. A riasztási szabályok is szerkesztett nd engedélyezve van vagy le van tiltva ezen a lapon.

 ![riasztások-szabályok](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="creating-an-alert-rule"></a>Riasztási szabály létrehozása
Riasztások függetlenül a figyelőszolgáltatás egységes módon hozhat létre, vagy a típus azt. Összes következik be, riasztások, és a kapcsolódó részletes adatok találhatók egylapos.
 
Egy új riasztási szabályt hoz létre a következő három lépést:
1. Válassza ki a _cél_ a riasztás.
1. Válassza ki a _jel_ a rendelkezésre álló jeleket a cél a.
1. Adja meg a _logika_ a Signal adatok alkalmazandó.
 
Az egyszerűsített létrehozási folyamat már nem szükséges tudni, hogy a figyelési forrás- vagy egy Azure-erőforrás kiválasztása előtt támogatott jelek a felhasználó. A lista elérhető jelek automatikusan kijelölt tároló-erőforrás alapján szűri, és végigvezeti a riasztási szabály logikája meghatározása.

A riasztási szabályok létrehozásával kapcsolatos részletesebb [létrehozása, megtekintése és kezelése az Azure-figyelővel riasztások](monitor-alerts-unified-usage.md).

Riasztások több Azure-szolgáltatások figyelésének keresztül érhetők el. Információk és mikor érdemes használni ezeket a szolgáltatásokat mindegyikének: [figyelése Azure-alkalmazások és erőforrások](./monitoring-overview.md). A következő táblázat felsorolja a riasztási szabályok elérhető típusú Azure, és mi jelenleg a riasztás egységes élmény támogatja.

| **Forrás figyelése** | **Jel típusa**  | **Leírás** | 
|-------------|----------------|-------------|
| Az Azure-figyelő | Metrika  | Más néven [közel valós idejű metrika riasztások](monitoring-near-real-time-metric-alerts.md), 1 perces gyakorisággal metrika feltételek kiértékelése támogatja, és lehetővé teszik a több metrika és többdimenziós metrika szabályokat. A támogatott típusok listája érhető [újabb metrika értesítések az Azure-szolgáltatásokat az Azure portálon](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).<br>[Klasszikus metrika riasztások](monitoring-overview-alerts.md) nem támogatottak az új riasztások felületet. Megtalálja azokat a riasztásokat (klasszikus) Azure-portálon. A klasszikus riasztások néhány metrikák típusokat, amelyek még nem lettek áthelyezve az újabb riasztások támogatja. Teljes listájáért lásd: [metrikák támogatott](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics). |
| Log Analytics | Logs  | Értesítéseket, vagy futtassa az automatikus műveleteket, ha a napló keresési lekérdezés meghatározott feltételeknek eleget. A Naplóelemzési riasztások [az új felületre való másolásának](monitoring-alerts-extend.md). A [az előzetes *Naplóelemzési naplók, metrikák* ](monitoring-alerts-extend-tool.md) érhető el. Az előzetes lehetővé teszi bizonyos naplók típusait és alakíthatja át őket metrika, ahol meg tudja majd riasztás őket az Új riasztási felület használatával. Az előzetes akkor hasznos, ha le szeretné kérdezni natív Azure figyelő metrikák együtt az Azure-naplók. |
| Tevékenységnaplók | Tevékenységnapló | A kijelölt tároló által létrehozott összes létrehozási, frissítési és törlési műveletek rekordokat tartalmazza. |
| Service Health | Tevékenységnapló  | Egyesített riasztások nem támogatott. Lásd: [napló riasztások tevékenység létrehozása a szolgáltatás értesítések](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Application Insights | Logs  | Naplók és az alkalmazás teljesítményének részleteit tartalmazza. Analytics query használatával, az alkalmazásadatok alapján végrehajtandó műveletek feltételeinek megadása. |
| Application Insights | Metrika | Egyesített riasztások nem támogatott. [Metrika riasztások] megtekintéséhez. (.. /Application-insights/App-insights-alerts.MD) |
| Application Insights | Webalkalmazás-rendelkezésreállás figyelésére szolgáló tesztek | Egyesített riasztások nem támogatott.  Lásd: [webes teszt riasztások](../application-insights/app-insights-monitor-web-app-availability.md). Bármely szeretnék adatokat küldeni a Application Insights tagolva webhelyen érhető el. Értesítést kaphat, ha a rendelkezésre állás vagy a webhely válaszképesség nem éri el elvárásainak. |

## <a name="enhanced-unified-alerts-public-preview"></a>Továbbfejlesztett egyesített riasztások (nyilvános előzetes verzió)
> [!NOTE]
>  Ebben a szakaszban a funkció hamarosan elérhető. Azt nem szerepelhet a által használt még. 

Továbbfejlesztett egyesített riasztások élményt 2018. június 1 Azure figyelő nyilvános előzetes verziójában lett szabadítva. Ez a felület épít, előnyeit [riasztások egyesített](#overview) március 2018 kiadott, és lehetővé teszi a kezelése, és módosítja a riasztás állapota mellett az egyes riasztások összesítése. Ez a szakasz az új szolgáltatásokat és az Azure portálon Új riasztási lapok közötti váltáshoz ismerteti.

### <a name="features-enhanced-unified-alerts"></a>Továbbfejlesztett funkciók egyesített riasztások

Az új felület nem érhetők el a klasszikus egységes élmény a következő szolgáltatásokat biztosítja:

- **Megtekintheti a riasztásokat a különböző előfizetéseknél** -most már megtekintheti és kezeli a riasztások egyes példányai egyetlen nézetben több előfizetéssel.
- **Az állapot a riasztások kezelése** -riasztások most már rendelkezik olyan állapotban, amely azt jelzi, hogy a lett zárva a nyugtázott.
- **Intelligens csoportok riasztások rendezése** -intelligens csoportok automatikusan csoportba foglalásához kapcsolatos riasztásokat, így külön-külön helyett készletként kezelheti azokat.

### <a name="enable-enhanced-unified-alerts"></a>Továbbfejlesztett egyesített értesítések engedélyezése
A szalagcím a riasztások lap tetején kattintson az új, egységes riasztási kezelőfelület engedélyezése Ez a folyamat létrehoz egy riasztási tároló, amely tartalmazza az égetett riasztások az elmúlt 30 nap között támogatott szolgáltatások. Az új felület engedélyezése után válthat oda-vissza a régi és új felhasználói élmény a szalagcím kattintva.

> [!NOTE]
>  Először engedélyezni kell az új felület néhány percig is eltarthat.

![Transzparens](media/monitoring-overview-unified-alerts/opt-in-banner.png)

Ha engedélyezi az új felület fogja regisztrálni, amely rendelkezik hozzáféréssel előfizetéseken. Bár a teljes előfizetés engedélyezve van, csak az új felület kiválasztott felhasználók lesz tudja majd tekinteni azt. Az előfizetés hozzáféréssel rendelkező más felhasználók külön-külön engedélyeznie kell a felhasználói élmény.

Az Új riasztási élmény engedélyezése nem befolyásolja a művelet csoportok vagy értesítést küld a riasztási szabályok a konfigurációja. Ez csak módosítja, hogy megtekinthető és kezelhető az Azure portálon riasztásokat égetett példányai.

### <a name="smart-groups"></a>Intelligens csoportok
Intelligens csoportok zaj csökkentésére azáltal, hogy az egyes riasztások kezelése, hanem egyetlen kapcsolódó riasztások kezelése. Intelligens csoportok részleteinek megtekintése, és a beállítani egy riasztáshoz hasonló. Minden riasztás egy intelligens csoport tagja.

Intelligens csoportok automatikusan létrejönnek a gépi tanulási kapcsolódó riasztásokat, amelyek megfelelnek egy probléma egyesítése használatával. Ha riasztás jön létre, az algoritmus hozzáad egy új intelligens vagy egy meglévő intelligens csoportok olyan adatokat, mint a korábbi minták, a hasonlóság tulajdonságok és a hasonló struktúrával alapján. Az algoritmus jelenleg csak egy előfizetésen belül az azonos figyelő szolgáltatásból riasztások figyelembe veszi. Intelligens csoportok csökkentheti az összevonás keresztül riasztási zaj 99 %-kal. Az oka, hogy riasztások foglalt egy csoportot az intelligens csoport részletei lapon tekintheti meg.

Egy intelligens csoport nevét az első riasztás esetén. Nem hozható létre, vagy intelligens csoport átnevezése.


### <a name="alert-states"></a>Riasztás állapota
Továbbfejlesztett egyesített riasztások, akkor a riasztás állapota fogalmát. Beállíthatja, hogy riasztást adjon meg, hol helyezkedik el a feloldási folyamat állapotát.  Ha riasztás jön létre, állapotba került *új*. Ha korábban a nyugtázott egy riasztást, és lezárt már módosíthatja állapotát. A riasztás előzményeihez módosításokat állam tárolja.

A következő riasztási állapotok támogatottak.

| Állapot | Leírás |
|:---|:---|
| Új | A problémát most már észlelt, és még nem ellenőrzi. |
| Visszaigazolva | A rendszergazda tekintse át a figyelmeztető és megkezdte működését rajta. |
| Lezárva | A probléma megoldódott. Miután egy riasztás be lett zárva, akkor nyithatja meg a módosítás egy másik állapotba. |

Egy riasztás állapota eltér a figyelési feltétel. Metrika riasztási szabályok riasztást állíthat be feltétele _feloldva_ Ha a hibát kiváltó feltétel nem teljesül. A riasztás állapota a felhasználó állítja be, és a figyelési feltétel független. Annak ellenére, hogy a rendszer a feloldani a figyelési feltétel lehet beállítva, a riasztás állapota nem változik, amíg a felhasználó módosítja azt.

#### <a name="changing-the-state-of-an-alert-or-smart-group"></a>Egy riasztás vagy intelligens csoport állapotának módosítása
Az egyes riasztási állapot módosítására, vagy több riasztás együtt kezelheti úgy, hogy egy intelligens csoport állapotát.

A riasztás állapotának módosítása kattintva **módosítsa a riasztás állapota** a Részletek nézetben a riasztást, vagy állítsa az intelligens állapotának kattintva **intelligens csoport Állapotváltozáshoz** Részletek nézetben. Egyszerre több elem állapotának módosíthatja lista nézetben jelölje ki őket, kattintson **állapotváltoztatási** az oldal tetején. Mindkét esetben válassza ki az új állapot a legördülő listából, és megadhat egy megjegyzést. Ha csak egy elemet, akkor is rendelkezik egy lehetőség, hogy a módosítások alkalmazása az intelligens csoportban lévő összes riasztást.

![Állapot módosítása](media/monitoring-overview-unified-alerts/change-tate.png)

### <a name="alerts-page"></a>Riasztások lap
Az alapértelmezett riasztások lapon belül egy adott időkerete létrehozott riasztások összegzését tartalmazza. Minden súlyossági teljes riasztásait az oszlopokat azonosítja az egyes minden súlyossági a riasztások teljes számát jeleníti meg. Kattintson bármelyik elemre a nyomkövetés alapjául szolgáló a [minden riasztás](#all-alerts-page) oldalon a súlyosság szerint szűrve.

![Riasztások lap](media/monitoring-overview-unified-alerts/alerts-page.png)

Ez a nézet a legördülő lista az oldal tetején érték alapján szűrhetők.

| Oszlop | Leírás |
|:---|:---|
| Előfizetés | Válassza ki a legfeljebb 5 Azure-előfizetések. Csak a kijelölt előfizetésekben riasztások szerepelnek a nézetet. |
| Erőforráscsoport | Válasszon egy erőforráscsoportot. Csak azokat a riasztásokat, amelyek céljai a kijelölt erőforráscsoporthoz tartozik, szerepel a nézet. |
| Időtartomány | Csak a kijelölt időszak belül következik riasztásokat fognak szerepelni a nézetet. Támogatott értékei óránként túlra, elmúlt 24 órában, elmúlt 7 napban és 30 nap alatt. |

Kattintson a következő értékeket a riasztások lapon nyissa meg a másik oldal tetején.

| Érték | Leírás |
|:---|:---|
| Riasztások összesen | A kijelölt feltételeknek megfelelő riasztások teljes számát. Kattintson az értékre nincs szűrő nyissa meg az összes riasztás megtekintése. |
| Intelligens csoportok | A kijelölt feltételeknek megfelelő riasztások alapján létrehozott intelligens csoportok száma. Ezt az értéket az összes riasztás nézetben intelligens csoportok listájának megnyitásához kattintson.
| Riasztási szabályok száma | A kijelölt előfizetés és az erőforrás csoport riasztási szabályok száma. Kattintson a szabályok megtekintése a kiválasztott előfizetés és az erőforráscsoport szűrve nyissa meg ezt az értéket.


### <a name="all-alerts-page"></a>Minden riasztások lap 
Az összes értesítés lap lehetővé teszi a kijelölt időszak belül létrehozott riasztások listájának megtekintése. Vagy megtekintheti az egyes riasztások listája vagy az intelligens a figyelmeztetéseket tartalmazó csoportok listája. Kattintson a szalagcím nézetek közötti váltáshoz a lap tetején.

![Minden riasztások lap](media/monitoring-overview-unified-alerts/all-alerts-page.png)

A nézet a legördülő lista az oldal tetején a következő érték alapján szűrhetők.

| Oszlop | Leírás |
|:---|:---|
| Előfizetés | Válassza ki a legfeljebb 5 Azure-előfizetések. Csak a kijelölt előfizetésekben riasztások szerepelnek a nézetet. |
| Erőforráscsoport | Válasszon egy erőforráscsoportot. Csak azokat a riasztásokat, amelyek céljai a kijelölt erőforráscsoporthoz tartozik, szerepel a nézet. |
| Erőforrás típusa | Válasszon egy vagy több erőforrástípusok esetében. Csak azokat a riasztásokat, amelyek céljai a kiválasztott típus szerepel a nézetet. Ebben az oszlopban csak érhető el, miután egy erőforráscsoport van megadva. |
| Erőforrás | Válasszon ki egy erőforrástípust. Csak azon riasztások célként adott erőforrásaival szerepelnek a nézetet. Ez az oszlop csak érhető el, amennyiben az erőforrás-típus lett megadva. |
| Severity | Jelöljön ki egy riasztás súlyossága vagy *összes* összes súlyossági szintet használja riasztásokat tartalmazza. |
| A figyelőre érvényes feltétel | Jelöljön ki egy figyelési feltétel vagy *összes* feltételek riasztásokat tartalmazza. |
| Riasztás állapota | Válasszon ki egy riasztást állapotú vagy select *összes* állapotok riasztásokat tartalmazza. |
| Szolgáltatás figyelése | Jelöljön ki egy szolgáltatás vagy *összes* tartalmazza az összes szolgáltatás. Csak a cél, hogy a szolgáltatás használatával szabályok által létrehozott riasztásokat tartoznak. |
| Időtartomány | Csak a kijelölt időszak belül következik riasztásokat fognak szerepelni a nézetet. Támogatott értékei óránként túlra, elmúlt 24 órában, elmúlt 7 napban és 30 nap alatt. |

Kattintson a **oszlopok** megjelenítendő oszlopok kiválasztása a lap tetején. Kivételével az összes oszlop eltávolítása 

### <a name="alert-detail-page"></a>Riasztás részletei lap
A riasztás információs lapját kattintva egy riasztás jelenik meg. A riasztás részleteit, és módosíthatja az állapotát.

![Riasztás részletei](media/monitoring-overview-unified-alerts/alert-detail.png)

A riasztás részletei lapon az alábbi szakaszokat tartalmazza.

| Section | Leírás |
|:---|:---|
| Alapvető erőforrások | A tulajdonságok és a riasztás egyéb jelentős adatait jeleníti meg. |
| Előzmények | A riasztás által végrehajtott minden műveletet és a riasztás módosításai sorolja fel. Ez az állapot változásainak jelenleg csak. |
| Intelligens csoport | A riasztás intelligens-csoport információit tartalmazza. A **riasztások számát** hivatkozik a intelligens csoportban levő értesítések száma. Ez magában foglalja a más riasztások ugyanabban a azonos intelligens csoportban található, amelyek az elmúlt 30 napban lettek létrehozva.  Ez az az idő szűrő a riasztásokat a lista lapon függetlenül. Kattintson a riasztás a részletek megtekintéséhez. |
| További részletekért | További általában jellemző a riasztást létrehozó adatforrás típusú riasztás környezetfüggő információk megjelenítése. |


### <a name="smart-group-detail-page"></a>Csoport intelligens részletei lap
A csoport intelligens részletei lap jelenik meg, amikor rákattint az intelligens csoport. Részletesen bemutatja az intelligens csoport, beleértve a csoport létrehozásához használt indoklást, és módosíthatja az állapotát.
 
![Intelligens csoport részletei](media/monitoring-overview-unified-alerts/smart-group-detail.png)


A csoport intelligens részletei lap az alábbi szakaszokat tartalmazza.

| Section | Leírás |
|:---|:---|
| Riasztások | Az intelligens csoportban levő az egyes riasztások listája. Kattintson a riasztás részletei lap megnyitásához. |
| Előzmények | Minden intelligens csoport által végrehajtott műveletet és végzett változtatásokat sorolja fel. Ez az állapotváltozások és riasztási csoporttagsági változások jelenleg korlátozott. |

## <a name="next-steps"></a>További lépések
- [Az új riasztások felület használata létrehozása, megtekintése és riasztások kezelése](monitor-alerts-unified-usage.md)
- [További tudnivalók a napló riasztásokat a riasztások élmény](monitor-alerts-unified-log.md)
- [További tudnivalók a metrika riasztásokat a riasztások élmény](monitoring-near-real-time-metric-alerts.md)
- [További tudnivalók a tevékenység napló riasztásokat a riasztások élmény](monitoring-activity-log-alerts-new-experience.md)
