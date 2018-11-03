---
title: Riasztások kezelése és értesítési figyelése az Azure-ban – áttekintés
description: Az Azure-ban riasztások áttekintése. Riasztások, a klasszikus riasztások, a riasztások felületet.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 9340c5a70ac652f1cf0a8b0cddce8488e0a62eee
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978869"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>A Microsoft Azure-ban riasztások áttekintése 

Ez a cikk azt ismerteti, milyen riasztásokat, azok előnyeit, és hogyan lehet használatba vételükhöz.  


## <a name="what-are-alerts-in-microsoft-azure"></a>Mik a figyelmeztetések a Microsoft Azure-ban?
Proaktív módon kap értesítést, ha fontos feltételek a figyelési adatok találhatók. Lehetővé teszik, hogy azonosítsa és problémákat, mielőtt azokat a felhasználókat a rendszer figyelje meg. 

Ez a cikk ismerteti az egyesített riasztási élmény az Azure monitorban, amely már tartalmazza a Log Analytics és az Application Insights. A [korábbi riasztási élmény](monitoring-overview-alerts.md) és riasztási típusok korábbi megnevezése **klasszikus riasztások**. Kattintva megtekintheti a régi felületet és a régebbi riasztástípus **klasszikus riasztások megtekintése** a riasztás oldalának tetején. 

## <a name="overview"></a>Áttekintés

Az alábbi ábrán a folyamat a riasztások jelöli. 

![A riasztási folyamat](media/monitoring-overview-alerts/Azure-Monitor-Alerts.svg)

Riasztási szabályok vannak elkülönítve, a riasztások és a riasztások aktiválódásakor elvégzett műveletet. 

**Riasztási szabály** – a riasztási szabályt kell-e a cél és a riasztási feltételeket. A riasztási szabály is lehet egy engedélyezett vagy letiltott állapotban. Riasztások csak akkor aktiválódjanak, ha engedélyezve van. 

Riasztási szabályok legfőbb attribútumai a következők:

**Célerőforrásnál** – a hatókör határozza meg, és jelzi a riasztás érhető el. A cél lehet Azure-erőforrásokkal. Példa célok: a virtuális gép, tárfiók, egy virtuálisgép-méretezési csoportot, Log Analytics-munkaterület vagy Application Insights-erőforrás. Az egyes erőforrások (például virtuális gépek), megadhat több olyan erőforrást a riasztási szabály céljaként.

**Jel** – jelzi, hogy a célként megadott erőforrás által kibocsátott vannak, és számos különböző lehet. A metrika, tevékenység napló, az Application Insights és napló.

**Feltételek** – feltételek kombinációja jel és logika egy cél-erőforrásra alkalmazni. Példák: 
   - Százalékos Processzorhasználat > 70 %-os
   - Kiszolgáló válaszideje > 4 ms 
   - A napló lekérdezés > 100 eredmény száma

**Riasztás neve** – egy egyedi nevet a riasztási szabály a felhasználó által beállított

**Riasztás leírása** – a felhasználó által beállított a riasztási szabály leírása

**Súlyosság** – Ha a riasztási szabályban megadott feltétel teljesülése a riasztás súlyosságát. Súlyosság 0-tól 4 terjedhet.

**A művelet** – a riasztás akkor aktiválódik, amikor egy bizonyos művelet. További információkért lásd: [Műveletcsoportok](monitoring-action-groups.md).

## <a name="what-you-can-alert-on"></a>A is riasztás

Metrikákat és naplókat is riasztás leírtak szerint [adatforrások figyelési](monitoring-data-sources.md). Ezek közé tartozik, de nem korlátozódnak:
- Metrikaértékek
- Naplóbeli keresési lekérdezések
- Tevékenységnapló-események
- A mögöttes Azure platform állapotát
- A webhely rendelkezésre állási tesztek

## <a name="manage-alerts"></a>Riasztások kezelése
Beállíthatja, hogy riasztást adjon meg, hol helyezkedik el a feloldási folyamat állapotát. A riasztási szabályban megadott megadott feltétele teljesül, ha riasztás jön létre, vagy aktivált, állapotba került, *új*. Miután nyugtázta a riasztást, és ha bezárja az állapot módosíthatja. Az összes állapotváltozások előzményeit, a riasztás vannak tárolva.

A következő riasztási állapotok támogatottak.

| Állapot | Leírás |
|:---|:---|
| Új | A probléma csak észlelt, és még nem lett felülvizsgálva. |
| Visszaigazolva | A rendszergazda tekintse át a figyelmeztető és a munka megkezdése. |
| Lezárva | A probléma megoldódott. Riasztás lezárása után nyissa meg újra egy másik állapotának módosításával. |

**Riasztás állapota** eltérő, és független a **feltétel figyelése**. Riasztás állapota a felhasználó állítja be. A figyelő az állapot a rendszer állítja be. Egy riasztás akkor aktiválódik, ha a riasztást figyelő feltétel értéke *aktivált*. Ha az alapul szolgáló a riasztást kiváltó törli üzenetszám, a figyelési feltétel értéke *megoldott*. A riasztás állapota nem változik meg, amíg a felhasználó megváltoztatná. Ismerje meg, [módosítása a riasztások és az intelligens csoportok állapotát](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Intelligens csoportok 
Az intelligens csoportok vannak előzetes verzióban érhető el. 

Az intelligens csoportjai összesítések alapján gépi tanulási algoritmusok, amely megkönnyíti a riasztásokat riasztási zaj csökkentésére, és a hibaelhárítással támogatási. [További információk az intelligens csoportokról](https://aka.ms/smart-groups) és [az intelligens csoportok kezelése](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Riasztások élmény 
Az alapértelmezett riasztások oldal, amely egy adott időtartományon belül jönnek létre riasztások összegzését tartalmazza. Megjeleníti a riasztások esetében minden egyes oszlopokat, amelyek azonosítják az egyes állapotokban esetében minden egyes riasztások száma összesen. Válassza ki valamelyik megnyitásához a súlyossági szinten pedig a [minden riasztás](#all-alerts-page) oldalon a súlyosság szerint szűrve.

Nem megjelenítése és nyomon követheti a régebbi [klasszikus riasztások](#classic-alerts). Módosíthatja az előfizetések vagy szűrőparaméterek frissíteni a lapot. 

![Riasztások lap](media/monitoring-overview-alerts/alerts-page.png)

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


## <a name="manage-alert-rules"></a>Riasztási szabályok kezelése
Kattintson a **riasztási szabályok kezelése** megjelenítéséhez a **szabályok** lapot. **Szabályok** egyetlen helyet minden riasztási szabályok kezelése az Azure-előfizetések között. Ez felsorolja az összes riasztási szabályt, és a céloldali erőforrások, erőforráscsoportok, szabálynév vagy állapot alapján lehet rendezni. Riasztási szabályok is kell szerkeszteni, engedélyezve van, vagy le van tiltva ezen a lapon.  

 ![riasztások – szabályok](./media/monitoring-overview-alerts/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Riasztási szabály létrehozása
Riasztásokat hozhat létre egységes módon végrehajtott, függetlenül a figyelési szolgáltatás, vagy jel típusa. Az összes aktivált riasztások, és a kapcsolódó részletes adatokat egyetlen lapon érhető el.
 
Akkor hozzon létre egy új riasztási szabály az alábbi három lépést:
1. Válassza ki a _cél_ a riasztás.
1. Válassza ki a _jel_ az a cél a rendelkezésre álló jelek.
1. Adja meg a _logikai_ kinyert adatok alkalmazandó.
 
Ez a szerzői műveletekhez részben egyszerűsített folyamat többé nem kell, hogy tudja, hogy a monitorozási forrás vagy azt jelzi, hogy egy Azure-erőforrás kiválasztása előtt támogatottak. Elérhető jelek automatikusan szűri a program a célként megadott erőforrás kiválasztott alapján. Is alapján, hogy a cél, végigvezeti a riasztási szabály a logikai automatikusan meghatározása.  

A riasztási szabályok létrehozásával kapcsolatos többet is megtudhat [, létrehozás, riasztások megtekintése és kezelése az Azure Monitor használatával](alert-metric.md).

Riasztások számos Azure-szolgáltatások figyelésének érhetők el. Információ és mikor érdemes használni, az ilyen szolgáltatásokkal: [figyelése az Azure-alkalmazások és erőforrások](../azure-monitor/overview.md). A következő táblázat a riasztási szabályok Azure-ban rendelkezésre álló típusok listája. Azt is megjeleníti, mi jelenleg támogatott a riasztási tapasztalatok alapján.

Korábban az Azure Monitor, az Application Insights, a Log Analytics és a Service Health korábban külön riasztási képességek. Az Azure túlóra, továbbfejlesztett, és a felhasználói felület és a különböző módszerek riasztások kombinált. Ez az összevonás még folyamatban van. Ennek eredményeképpen továbbra is vannak bizonyos riasztási funkciók nem, de az új riasztások rendszerben.  

| **Forrás figyelése** | **Jel típusa**  | **Leírás** | 
|-------------|----------------|-------------|
| Szolgáltatások állapota | Tevékenységnapló  | Nem támogatott. Lásd: [tevékenységnapló-riasztások létrehozása a szolgáltatási értesítések](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Application Insights | Webalkalmazás rendelkezésre állási tesztek | Nem támogatott. Lásd: [webtesztriasztást](../application-insights/app-insights-monitor-web-app-availability.md). Rendelkezésre álló webhelyre, hogy az Application insights szolgáltatásnak van kialakítva. Értesítés küldése, amikor a rendelkezésre állás vagy a webhely válaszképesség nem éri el elvárásainak. |


## <a name="all-alerts-page"></a>Az összes riasztások oldal 
Kattintson az értesítések száma az összes riasztások oldal megjelenítéséhez. Itt megtekintheti a kiválasztott időtartományon belül létrehozott riasztások listáját is. Megtekintheti az egyes riasztások listája vagy az intelligens csoportok, amelyek tartalmazzák a riasztások listája. Válassza ki a szalagcím Váltás a nézetek között az oldal tetején.

![Az összes riasztások oldal](media/monitoring-overview-alerts/all-alerts-page.png)

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

## <a name="alert-detail-page"></a>Riasztás részletei lap
A riasztás részletei lap jelenik meg, válasszon ki egy riasztást. A riasztás részleteinek biztosít, és lehetővé teszi az állapot módosítását.

![Riasztás részletei](media/monitoring-overview-alerts/alert-detail2.png)

A riasztás részletei lap az alábbi szakaszokat tartalmazza.

| Section | Leírás |
|:---|:---|
| Alapvető erőforrások | A tulajdonságok és más jelentős a riasztásra vonatkozó információk megjelenítése. |
| Előzmények | Minden egyes a riasztás által végrehajtott műveletek és a riasztás végzett módosítások sorolja fel. Egyelőre csak állapotváltozásokat. |
| Az intelligens csoport | Információk az intelligens csoport tartalmazza a riasztás. A *riasztások száma* hivatkozik, amely az intelligens csoportban levő értesítések száma. Az azonos intelligens csoport, függetlenül az Időszűrő a riasztások lista lap az elmúlt 30 napban létrehozott egyéb riasztásokat tartalmazza. Válasszon ki egy riasztást, a részletek megtekintéséhez. |
| További részletek | A további környezeti információkat a riasztás, a riasztást létrehozó adatforrás típusa általában jellemző megjelenítése. |


## <a name="classic-alerts"></a>Klasszikus riasztások 

A riasztási funkció 2018 június előtt az Azure Monitor mérőszámok és Tevékenységnaplók log "Riasztások (klasszikus)" nevezzük. 

További információkért lásd: [klasszikus riasztások](./monitoring-overview-alerts-classic.md)


## <a name="next-steps"></a>További lépések

- [További információk az intelligens csoportokról](https://aka.ms/smart-groups)
- [További információ a műveletcsoportokról](monitoring-action-groups.md)
- [Az Azure-ban a riasztási példányok kezelése](https://aka.ms/managing-alert-instances)
- [Az intelligens csoportok kezelése](https://aka.ms/managing-smart-groups)





