---
title: Az Azure monitorban egységes riasztások
description: Leírás egységes Azure-ban, hogy milyen riasztások kezelését teszi lehetővé riasztást, és a szabályok riasztásokat az Azure-szolgáltatás.
author: manishsm-msft
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: mamit
ms.component: alerts
ms.openlocfilehash: 30b2d60868702c6113612668b8e4cf9975aa2c40
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962197"
---
# <a name="unified-alerts-in-azure-monitor"></a>Az Azure monitorban egységes riasztások

## <a name="overview"></a>Áttekintés

> [!NOTE]
>  Egy új egyesített riasztási megoldást, amellyel kezelheti a több előfizetést riasztást, és bemutatja a riasztás Államokban és intelligens csoportok jelenleg nyilvános előzetes verzióban érhető el. Tekintse meg a leírását ez a cikk utolsó szakaszában fokozott élményt, és engedélyezheti azt.


Ez a cikk az Azure Monitor riasztási egységes élményt ismerteti. A [korábbi riasztási élmény](monitoring-overview-alerts.md) érhető el a **riasztások (klasszikus)** lehetőség az Azure figyelő menü. 

## <a name="features-of-the-unified-alert-experience"></a>A riasztási egységes felhasználói élményt funkciói

Az egységes felhasználói élményt a klasszikus élmény felett az alábbi előnyökkel jár:

-   **Értesítési rendszer jobb**: [Műveletcsoportok](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) nevesített csoportok vagy értesítési és műveleteket, amelyeket több riasztás felhasználható. 
- **Egyesített jelentéskészítési funkciók**: riasztásokat és metrikákat, naplókat és tevékenységeket tartalmazó naplók az Azure Monitor, a Log Analytics és az Application Insights riasztási szabályokat egy helyen kezelhető. 
- **Megtekintése az Azure Portal Log Analytics-riasztások aktivált**: Log Analytics-riasztásokkal mostantól a riasztások más forrásokból, az Azure Portalon lehet megtekinteni. Korábban a riasztások más forrásokból is külön portált.
- **Aktivált riasztások és a riasztási szabályok szétválasztása**: riasztási szabályok most megkülönböztetett riasztásokból. Riasztási szabályok egy feltételt, amely a riasztás definíciója. Riasztást egy riasztási szabályt elsőre példánya.
- **Jobb munkafolyamat**: A jelentéskészítési funkciók egységes riasztás végigvezeti Önt a riasztási szabályok konfigurálásának folyamatán.
 
Metrikákhoz kapcsolódó riasztások a következő fejlesztéseket szabályozhatják a klasszikus metrikariasztásokat:

-   **Továbbfejlesztett késés**: metrikákhoz kapcsolódó riasztások gyakorisággal percenként egyszer futtatható. Klasszikus metrikariasztásokat mindig gyakoriságot, 5 percenként egyszer futtatni. Riasztások továbbra is fennáll, hogy a naplók szükséges idő miatt egy percnél hosszabb késleltetést. 
-   **Többdimenziós metrikák támogatása**:, riasztja Önt a többdimenziós metrikák, ami azt jelenti, hogy egy adott példányán, a metrika figyelheti.
-   **Metrikai feltétel több felügyeleti**: gazdagabb riasztási szabályok, amelyek támogatják a mérőszámok maximális, minimális, átlagos és teljes értékek figyelési definiálhat.
-   **Több metrika figyelése kombinált**: egyetlen szabállyal legfeljebb két mérőszám figyelheti. Egy riasztás akkor aktiválódik, ha mindkét metrikák megsértik a megfelelő mértékben a küszöbértékeket a megadott időszakra vonatkozó.
-   **Naplók, metrikák** (korlátozott nyilvános előzetes verzió): bizonyos adatok kerülnek a Log Analytics mostantól log ki kell olvasni, és az Azure Monitor-metrikák alakítani, és ezután kapni a hasonlóan más metrikákkal. 


## <a name="alert-rules"></a>Riasztási szabályok
Egyesített riasztásainak használja a következő fogalmak riasztási szabályok elkülönítése riasztások közben a szerkesztőfelületen egységes különböző riasztástípusok között.

| Elem | Meghatározás |
|:---|:---|
| Riasztási szabály | Riasztás létrehozása a feltétel meghatározása. Riasztási szabály áll, amelyek egy _célerőforrásnál_, _jel_, _feltételek_, és _logikai_. Riasztási szabály csak akkor aktív, ha van egy _engedélyezve_ állapota.
| Célerőforrás | Határozza meg az adott erőforrásokat és azt jelzi, hogy a riasztás érhető el. A cél lehet Azure-erőforrásokkal.<br><br>Példák: virtuális gép, tárfiók, virtuálisgép-méretezési csoportot, Log Analytics-munkaterületet, Application Insights-erőforrás |
| Jel | A célerőforrás által kibocsátott adatok forrását. Jel támogatott típusok a következők *metrika*, *tevékenységnapló*, *Application Insights*, és *Log*. |
| Feltételek | Kombinációja _jel_ és _logikai_ egy cél-erőforrásra a alkalmazni.<br><br>Példák: Százalékos Processzorhasználat > 70 %-os, a kiszolgáló válaszideje > 4 ms, a napló eredményszámot lekérdezés > 100, és így tovább |
| Logika | Felhasználó által definiált logikai annak ellenőrzéséhez, hogy a jel belül a tartományértékeknek várható. |
| Műveletek | A riasztás akkor aktiválódik, amikor végrehajtandó művelet. Több művelet akkor fordulhat elő, riasztások aktiválódásakor. Ezek a riasztások Műveletcsoportok támogatja.<br><br>Példák: e-mailben való e-mail-cím, egy webhook URL-Címének meghívása |
| Figyelőre érvényes feltétel | Azt jelzi, hogy megoldódott-e a feltétellel, hogy metrikariasztás létrehozása. Metrikaalapú riasztási szabályok egy adott mérőszám minta rendszeres időközönként. Ha a riasztási szabályban megadott feltétele teljesül, akkor egy új riasztás jön létre olyan feltétellel, az "aktiválás".  A metrika feladata újra, ha a feltétel teljesülése továbbra is majd nem történik semmi.  Ha a feltételek nem teljesülnek, majd a riasztás módosul, amelyikben "megoldódott". A megadott feltétele teljesül, a következő futtatáskor egy másik riasztást hoz létre olyan feltétellel, az "aktiválás". |


## <a name="alert-pages"></a>Riasztási oldalak
Egyesített riasztások adja meg az összes Azure-riasztások megtekintése és kezelése egy helyen. A következő szakaszok ismertetik a Funkciók, az egyesített felület minden egyes laphoz.

### <a name="alerts-overview-page"></a>Riasztások – áttekintés oldalra
A **riasztások** Áttekintés lapján az összes aktivált riasztások összesített összegzését jeleníti meg, és a teljes száma a riasztási szabályok engedélyezve van. Az összesítések módosítása az előfizetés vagy szűrési paraméterekhez frissíti, és a riasztások aktivált listája.

 ![riasztások – áttekintés](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Riasztási szabályok kezelése
**Szabályok** egyetlen lapon az összes riasztási szabályok kezelése az Azure-előfizetések között van. Ez felsorolja az összes riasztási szabályt, és a céloldali erőforrások, erőforráscsoportok, szabálynév vagy állapot alapján lehet rendezni. Riasztási szabályok is kell szerkeszteni, engedélyezve van, vagy le van tiltva ezen a lapon.

 ![riasztások – szabályok](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Riasztási szabály létrehozása
Riasztásokat hozhat létre egységes módon végrehajtott, függetlenül a figyelési szolgáltatás, vagy jel típusa. Az összes aktivált riasztások, és a kapcsolódó részletes adatokat egyetlen lapon érhető el.
 
Akkor hozzon létre egy új riasztási szabály az alábbi három lépést:
1. Válassza ki a _cél_ a riasztás.
1. Válassza ki a _jel_ az a cél a rendelkezésre álló jelek.
1. Adja meg a _logikai_ kinyert adatok alkalmazandó.
 
Ez a szerzői műveletekhez részben egyszerűsített folyamat többé nem kell, hogy tudja, hogy a monitorozási forrás vagy azt jelzi, hogy egy Azure-erőforrás kiválasztása előtt támogatottak. A rendelkezésre álló jelek automatikusan a szűrt lista a célként megadott erőforrás kiválasztott alapján, és ez végigvezeti Önt a riasztási szabály a logikai meghatározása.

A riasztási szabályok létrehozásával kapcsolatos többet is megtudhat [, létrehozás, riasztások megtekintése és kezelése az Azure Monitor használatával](alert-metric.md).

Riasztások számos Azure-szolgáltatások figyelésének érhetők el. Információ és mikor érdemes használni, az ilyen szolgáltatásokkal: [figyelése az Azure-alkalmazások és erőforrások](../azure-monitor/overview.md). A következő táblázat a riasztási szabályok Azure-ban rendelkezésre álló típusok listája. Azt is megjeleníti, mi a riasztási egységes felhasználói élményt az jelenleg támogatja.

| **Forrás figyelése** | **Jel típusa**  | **Leírás** | 
|-------------|----------------|-------------|
| Azure Monitor | Metrika  | Más néven [közel valós idejű metrikákhoz kapcsolódó riasztások](monitoring-near-real-time-metric-alerts.md), azok metrika feltételek kiértékelése, percenként egyszer gyakran támogatja, és lehetővé teszik a több metrika és többdimenziós metrika szabályokat. A támogatott erőforrástípusok listája megtalálható [újabb metrikákhoz kapcsolódó riasztások az Azure-szolgáltatásokhoz az Azure Portalon](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).<br>[Klasszikus metrikariasztásokat](monitoring-overview-alerts.md) a riasztások új kezelőfelülete nem támogatottak. Ezeket megtalálhatja riasztások (klasszikus) alatt az Azure Portalon. A klasszikus riasztások bizonyos metrika van még nem lett áthelyezve a újabb riasztások típusait támogatja. A teljes listát lásd: [támogatott mérőszámok](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics). |
| Log Analytics | Logs  | Értesítések fogadása, vagy automatikus műveleteket futtatni, ha egy naplóbeli keresési lekérdezés megfelel bizonyos feltételeknek. A riasztások a Log Analytics [az új felhasználói felületre való másolásakor](monitoring-alerts-extend.md). A [előzetes verziója *Log Analytics-naplók, metrikák* ](monitoring-alerts-extend-tool.md) érhető el. Az előzetes verzió lehetővé teszi bizonyos típusú, naplók és alakíthatja át őket a metrikák, ahol, majd riasztja Önt őket az Új riasztási funkció használatával. Az előzetes verzió akkor hasznos, ha szeretne kapni, valamint a natív Azure Monitor-metrikák az Azure-naplók. |
| Tevékenységnaplók | Tevékenységnapló | Tartalmazza a rekordokat az összes létrehozása, frissítése és törlése a kijelölt tároló által létrehozott műveleteket. |
| Szolgáltatások állapota | Tevékenységnapló  | Egyesített riasztások nem támogatott. Lásd: [tevékenységnapló-riasztások létrehozása a szolgáltatási értesítések](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Application Insights | Logs  | Naplók az alkalmazás teljesítményének részleteit tartalmazza. Az elemzési lekérdezés segítségével meghatározhatja a műveletek végrehajtását alapján az alkalmazásadatok feltételeit. |
| Application Insights | Metrika | Egyesített riasztások nem támogatott. Lásd: [metrikákhoz kapcsolódó riasztások](../application-insights/app-insights-alerts.md). |
| Application Insights | Webalkalmazás rendelkezésre állási tesztek | Egyesített riasztások nem támogatott.  Lásd: [webtesztriasztást](../application-insights/app-insights-monitor-web-app-availability.md). Rendelkezésre álló webhelyre, hogy az Application insights szolgáltatásnak van kialakítva. Értesítés küldése, amikor a rendelkezésre állás vagy a webhely válaszképesség nem éri el elvárásainak. |

## <a name="enhanced-unified-alerts-public-preview"></a>Továbbfejlesztett egységes riasztások (nyilvános előzetes verzió)

Egy egységes riasztások továbbfejlesztett élmény az Azure Monitor nyilvános előzetes verziója 2018. június 1. jelent meg. Ez a tapasztalat épül, amely előnyeinek [riasztások egyesített](#overview), amely 2018 március nem régebbi, és amely lehetővé teszi, hogy kezelheti és az egyes riasztások összesítése, valamint a riasztás állapotának módosítása. Ez a szakasz ismerteti az új funkciók és az új riasztás lapok az Azure Portalon.

### <a name="enhanced-unified-alerts"></a>Továbbfejlesztett egységes riasztások

Az új felhasználói felület nem érhetők el a klasszikus egységes felhasználói élményt a következő szolgáltatásokat biztosítja:

- **Megtekintheti a riasztásokat több előfizetés**: most már megtekintheti és kezelheti a riasztásokat az egyes példányai több előfizetést egyetlen nézetben.
- **Az állapot a riasztások kezelése**: riasztások most már olyan állapotban, amely azt jelzi, hogy azok már nyugtázták zárva.
- **Riasztások rendszerezése az intelligens csoportok**: intelligens csoportok automatikusan csoportosíthatja kapcsolatos riasztásokat, így külön-külön helyett csoportként kezelheti azokat.

### <a name="enable-enhanced-unified-alerts"></a>Továbbfejlesztett egységes riasztások engedélyezése
Egyesített riasztások új kezelőfelülete engedélyezze a riasztások lap felső szalagja kiválasztásával. Ez a folyamat létrehoz egy riasztási tároló, amely tartalmazza az aktivált riasztások az elmúlt 30 nap között támogatott szolgáltatások. Az új funkció engedélyezését követően válthat oda-vissza a régi és új felhasználói élményt a szalagcím kiválasztásával között.

> [!NOTE]
>  Eltarthat néhány percig, először engedélyezni kell az új felhasználói felületre.

![Szalagcím](media/monitoring-overview-unified-alerts/opt-in-banner.png)

Az összes előfizetés, hogy regisztrált az új funkció engedélyezése esetén a hozzáférést. Bár a teljes előfizetés engedélyezve van, csak azok a felhasználók, válassza ki az új funkció is megtekinthetik azokat. Az előfizetés-hozzáféréssel rendelkező más felhasználók külön-külön engedélyeznie kell a felhasználói élményt.

Az új riasztás használatát teszi lehetővé a Műveletcsoportok vagy -értesítések a riasztási szabályok konfigurációja nem érinti. Csak a módon, hogy megtekinthető és kezelhető a riasztások az Azure Portalon aktivált példányok változik.

### <a name="smart-groups"></a>Intelligens csoportok
Az intelligens csoportok zaj csökkentésére azáltal, hogy a kapcsolódó riasztások egyetlen egységként helyett, a riasztásokat egyenként kezelheti. Intelligens csoportok adatait tekintheti meg, és az állapot beállítása hasonlóan történik, hogyan zajlik a riasztások. Minden riasztás pontosan egy intelligens csoport tagja.

Intelligens csoportok használatával a gépi tanulási úgy, hogy egyetlen problémára képviselő kapcsolódó riasztások automatikusan jönnek létre. Ha riasztás jön létre, az algoritmus hozzáad egy új intelligens csoportot vagy egy meglévő, például a korábbi minták, hasonló tulajdonságok és hasonló szerkezet információi alapján intelligens csoporthoz. 

Az algoritmus jelenleg csak egy előfizetésen belül ugyanaz a figyelő a szolgáltatás riasztásai figyelembe veszi. Intelligens csoportok akár 99 %-a-összevonás keresztül riasztási zaj csökkentéséhez. Az oka, hogy a riasztások Rendszeríró egy csoporthoz az intelligens csoport részletek lapon tekintheti meg.

Egy intelligens csoport nevét az első riasztás neve. Nem hozható létre, vagy intelligens csoport átnevezése.


### <a name="alert-states"></a>Riasztási állapotok
Továbbfejlesztett egységes riasztások a riasztás állapotának fogalmát vezetnek be. Beállíthatja, hogy riasztást adjon meg, hol helyezkedik el a feloldási folyamat állapotát. Ha riasztás jön létre, állapotba került *új*. Miután nyugtázta a riasztást, és ha bezárja az állapot módosíthatja. Az összes állapotváltozások előzményeit, a riasztás vannak tárolva.

A következő riasztási állapotok támogatottak.

| Állapot | Leírás |
|:---|:---|
| Új | A probléma csak észlelt, és még nem lett felülvizsgálva. |
| Visszaigazolva | A rendszergazda tekintse át a figyelmeztető és a munka megkezdése. |
| Lezárva | A probléma megoldódott. Riasztás lezárása után nyissa meg újra egy másik állapotának módosításával. |

A riasztás állapotát a figyelési feltétel eltér. Metrikaalapú riasztási szabályok riasztást értékre a feltételben _megoldott_ Ha a hibajelzést kiváltó körülmény már nem teljesül. Riasztás állapota a felhasználó állítja be, és független a figyelési feltétel. Bár a rendszer a figyelési feltétel, a "megoldódott" beállíthatók, a riasztás állapota nem változik, amíg a felhasználó megváltoztatná.

#### <a name="change-the-state-of-an-alert-or-smart-group"></a>Egy riasztás vagy intelligens csoport állapotának módosítása
Egy adott riasztás állapotának módosítása vagy az intelligens csoport állapotának beállításával együtt kezelheti a több riasztás is.

Egy riasztás állapotának módosítása kiválasztásával **riasztás állapotának módosítása** a riasztás a Részletek nézetben. Módosítsa az intelligens csoport kiválasztásával vagy **intelligens állapot módosítása** a részletes nézetben. Egyszerre több elemek állapotának módosításához először válassza ki azokat a listanézet, és válassza **állapot módosítása** az oldal tetején. 

Mindkét esetben válassza ki az új állapot a legördülő menüből. Adja meg a megjegyzést. Ha módosítja egy elem, lehetősége is van a alkalmazni a módosításokat az intelligens csoportban található összes riasztást.

![Állapot módosítása](media/monitoring-overview-unified-alerts/change-tate.png)

### <a name="alerts-page"></a>Riasztások lap
Az alapértelmezett riasztások oldal, amely egy adott időtartományon belül jönnek létre riasztások összegzését tartalmazza. Megjeleníti a riasztások esetében minden egyes oszlopokat, amelyek azonosítják az egyes állapotokban esetében minden egyes riasztások száma összesen. Válassza ki valamelyik megnyitásához a súlyossági szinten pedig a [minden riasztás](#all-alerts-page) oldalon a súlyosság szerint szűrve.

![Riasztások lap](media/monitoring-overview-unified-alerts/alerts-page.png)

Ez a nézet legördülő listák az oldal tetején lévő értékek kijelölésével szűrheti.

| Oszlop | Leírás |
|:---|:---|
| Előfizetés | Legfeljebb öt Azure-előfizetések kiválasztása. Csak a kijelölt előfizetésekben riasztások nézetében megtalálhatók. |
| Erőforráscsoport | Válasszon egy erőforráscsoportot. Csak azon riasztások t a kiválasztott erőforráscsoportban megtalálhatók a nézetet. |
| Időtartomány | Csak az adott időtartamon belül aktivált riasztások nézetében megtalálhatók. Támogatott értékei a következők: az elmúlt egy órában, az elmúlt 24 órában, az elmúlt 7 napban és az elmúlt 30 napban. |

Válassza ki a következő értékeket felső részén egy másik nyissa meg a riasztások oldaláról.

| Érték | Leírás |
|:---|:---|
| Riasztások összesen | A kiválasztott feltételeknek megfelelő riasztások teljes száma. Válassza ki ezt az értéket az összes riasztás megtekintése nyissa meg a szűrő sincs. |
| Intelligens csoportok | A kiválasztott feltételeknek a riasztások alapján létrehozott intelligens csoportok teljes száma. Válassza ki ezt az értéket az összes riasztás nézetben az intelligens csoportok listájának megnyitásához.
| Riasztási szabályok összesen | A kijelölt előfizetésben és erőforráscsoportban csoport riasztási szabályok teljes száma. Válassza ki ezt az értéket a szűrés alapjául a kijelölt előfizetésben és erőforráscsoportban szabályok nézet megnyitásához.


### <a name="all-alerts-page"></a>Az összes riasztások oldal 
Az összes riasztás oldal használatával megtekintheti a kiválasztott időtartományon belül létrehozott riasztások listáját. Megtekintheti az egyes riasztások listája vagy az intelligens csoportok, amelyek tartalmazzák a riasztások listája. Válassza ki a szalagcím Váltás a nézetek között az oldal tetején.

![Az összes riasztások oldal](media/monitoring-overview-unified-alerts/all-alerts-page.png)

Válassza ki a következő értékeket a lap tetején lévő legördülő listák szűrheti a nézetet.

| Oszlop | Leírás |
|:---|:---|
| Előfizetés | Legfeljebb öt Azure-előfizetések kiválasztása. Csak a kijelölt előfizetésekben riasztások nézetében megtalálhatók. |
| Erőforráscsoport | Válasszon egy erőforráscsoportot. Csak azon riasztások t a kiválasztott erőforráscsoportban megtalálhatók a nézetet. |
| Erőforrás típusa | Válassza ki egy vagy több erőforrás-típus. Csak azon riasztások t a kiválasztott típusú megtalálhatók a nézetet. Ez az oszlop csak akkor használható, miután lett megadva egy erőforráscsoportot. |
| Erőforrás | Válasszon ki egy erőforrást. Csak azon riasztások t célként adott erőforrásra a nézet szerepelnek. Ez az oszlop csak akkor használható, miután egy erőforrás-típus lett megadva. |
| Severity | Egy riasztás súlyosságának kiválasztása, vagy válasszon *összes* minden súlyossági szint esetében riasztásokat tartalmazza. |
| Figyelőre érvényes feltétel | Válasszon figyelőre érvényes feltételt, vagy válasszon *összes* feltételek riasztásokat tartalmazza. |
| Riasztás állapota | Válasszon egy riasztási állapotot, vagy válasszon *összes* állapotok riasztásokat tartalmazza. |
| Szolgáltatás monitorozása | Válasszon ki egy szolgáltatást, vagy válasszon *összes* tartalmazza az összes szolgáltatás. Csak a szolgáltatási cél használó szabályok által létrehozott riasztásokat is. |
| Időtartomány | Csak az adott időtartamon belül aktivált riasztások nézetében megtalálhatók. Támogatott értékei a következők: az elmúlt egy órában, az elmúlt 24 órában, az elmúlt 7 napban és az elmúlt 30 napban. |

Válassza ki **oszlopok** jelölje be a megjelenítendő oszlopokat az oldal tetején. 

### <a name="alert-detail-page"></a>Riasztás részletei lap
A riasztás részletei lap jelenik meg, válasszon ki egy riasztást. A riasztás részleteinek biztosít, és lehetővé teszi az állapot módosítását.

![Riasztás részletei](media/monitoring-overview-unified-alerts/alert-detail.png)

A riasztás részletei lap az alábbi szakaszokat tartalmazza.

| Section | Leírás |
|:---|:---|
| Alapvető erőforrások | A tulajdonságok és más jelentős a riasztásra vonatkozó információk megjelenítése. |
| Előzmények | Minden egyes a riasztás által végrehajtott műveletek és a riasztás végzett módosítások sorolja fel. Ez a jelenleg csak az állapotváltozásokat. |
| Az intelligens csoport | Információk az intelligens csoport tartalmazza a riasztás. A *riasztások száma* hivatkozik, amely az intelligens csoportban levő értesítések száma. Ez magában foglalja a más riasztások az elmúlt 30 napban létrehozott ugyanabba az intelligens csoportba.  Ettől függetlenül az Időszűrő a riasztások lista lapon. Válasszon ki egy riasztást, a részletek megtekintéséhez. |
| További részletek | A további környezeti információkat a riasztás, a riasztást létrehozó adatforrás típusa általában jellemző megjelenítése. |


### <a name="smart-group-detail-page"></a>Az intelligens csoport részletei lap
Az intelligens Részletek lapján jelenik meg, válasszon ki egy intelligens csoportot. Az intelligens csoport, beleértve az indoklást, amely a csoport létrehozásához használt, és lehetővé teszi, hogy az állapotváltozáshoz. részleteket biztosít.
 
![Az intelligens csoport részletei](media/monitoring-overview-unified-alerts/smart-group-detail.png)


Az intelligens Részletek lapján az alábbi szakaszokat tartalmazza.

| Section | Leírás |
|:---|:---|
| Riasztások | Az intelligens csoportban levő egyes riasztások listája. Válasszon ki egy riasztást a riasztás részletei lap megnyitásához. |
| Előzmények | Felsorolja az egyes műveletet, amelyet az intelligens csoport és a hozzá végrehajtott módosításokat. Ez az állapot és riasztási tagsági változás jelenleg korlátozott. |

## <a name="next-steps"></a>További lépések
- [Ismerje meg, hogyan használható a riasztások új kezelőfelülete létrehozása, megtekintése és riasztások kezelése](alert-metric.md)
- [További információ a riasztások felületen riasztások](monitor-alerts-unified-log.md)
- [További információ a riasztások felületen metrikákhoz kapcsolódó riasztások](monitoring-near-real-time-metric-alerts.md)
- [További információ a riasztások élményt a tevékenységnapló-riasztások](alert-activity-log.md)
