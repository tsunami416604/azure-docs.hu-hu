---
title: A riasztások és az értesítések figyelésének áttekintése az Azure-ban
description: Az Azure-beli riasztások áttekintése. Riasztások, klasszikus riasztások és a riasztások felület.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: e02c23623062f5cb3e4c597b0bb257b30aa9f44a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769770"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>A Microsoft Azure riasztásainak áttekintése 

Ez a cikk ismerteti, hogy mik a riasztások, azok előnyei, és hogyan lehet elkezdeni használni őket.  

## <a name="what-are-alerts-in-microsoft-azure"></a>Mik azok a riasztások a Microsoft Azure-ban?
Riasztások proaktív módon értesíti Önt, ha fontos feltételek találhatók a figyelési adatokat. Lehetővé teszik a problémák azonosítását és kezelését, mielőtt a rendszer felhasználói észrevennék őket. 

Ez a cikk ismerteti az egyesített riasztási élményt az Azure Monitorban, amely tartalmazza a log analytics és az Application Insights által korábban felügyelt riasztásokat. A [korábbi riasztási élményt](alerts-classic.overview.md) és riasztástípusokat klasszikus *riasztásoknak nevezzük.* Ezt a régebbi élményt és a régebbi riasztástípust a **Klasszikus riasztások megtekintése** a riasztási oldal tetején lehetőséget választva tekintheti meg. 

## <a name="overview"></a>Áttekintés

Az alábbi ábra a riasztások áramlását mutatja. 

![Riasztási folyamat diagramja](media/alerts-overview/Azure-Monitor-Alerts.svg)

A riasztási szabályok elkülönülnek a riasztásoktól és a riasztási műveletek től. A riasztási szabály rögzíti a cél és a riasztási feltételek. A riasztási szabály lehet engedélyezett vagy letiltott állapotban. A riasztások csak akkor jelennek meg, ha engedélyezve vannak. 

A riasztási szabály fő attribútumai a következők:

**Célerőforrás:** Meghatározza a riasztáshoz rendelkezésre álló hatókört és jeleket. A cél bármilyen Azure-erőforrás lehet. Példa: egy virtuális gép, egy tárfiók, egy virtuálisgép-méretezési csoport, egy Log Analytics-munkaterület vagy egy Application Insights-erőforrás. Bizonyos erőforrások (például a virtuális gépek) esetén több erőforrást is megadhat a riasztási szabály céljaként.

**Jel**: A célerőforrás által kibocsátott érték. A jelek a következő típusúak lehetnek: metrika, tevékenységnapló, Application Insights és napló.

**Feltételek**: A célerőforrásra alkalmazott jel és logika kombinációja. Példák: 

- A processzor százalékos aránya > 70%
- Szerverválasz > 4 ms 
- A naplólekérdezés 100 > eredményszáma

**Riasztás neve**: A felhasználó által konfigurált riasztási szabály konkrét neve.

**Riasztás leírása**: A felhasználó által konfigurált riasztási szabály leírása.

**Súlyosság**: A riasztás súlyossága a riasztási szabályban megadott feltételek teljesülése után. A súlyosság 0 és 4 között lehet.

- Sev 0 = Kritikus
- Sev 1 = Hiba
- Sev 2 = Figyelmeztetés
- Sev 3 = Tájékoztató
- Sev 4 = Részletes 

**Művelet**: A riasztás ellépésekekén végrehajtott konkrét művelet. További információt a [Műveletcsoportok című témakörben talál.](../../azure-monitor/platform/action-groups.md)

## <a name="what-you-can-alert-on"></a>Mit lehet figyelmeztetni a

Az [adatforrások figyelése](../../azure-monitor/platform/data-sources.md)című részben leírtak szerint riasztást adhat meg a metrikákról és a naplókról. Ezeket a következőkben ismertetjük a teljesség igénye nélkül:

- Metrika értékei
- Naplóbeli keresési lekérdezések
- Tevékenységnapló eseményei
- A mögöttes Azure-platform állapota
- Webhely rendelkezésre állására vonatkozó tesztek

Korábban az Azure Monitor metrikák, Application Insights, Log Analytics és a Service Health külön riasztási képességekkel rendelkezett. Idővel az Azure továbbfejlesztette és kombinálta mind a felhasználói felületet, mind a különböző riasztási módszereket. Ez a konszolidáció még folyamatban van. Ennek eredményeképpen még mindig vannak olyan riasztási képességek, amelyek még nem szerepelnek az új riasztási rendszerben.  

| **Figyelő forrás** | **Jel típusa**  | **Leírás** |
|-------------|----------------|-------------|
| Szolgáltatás állapota | Tevékenységnapló  | Nem támogatott. Lásd: [Tevékenységnapló-értesítések létrehozása a szolgáltatási értesítéseken.](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)  |
| Application Insights | Webes elérhetőségi tesztek | Nem támogatott. Lásd: [Webes tesztriasztások](../../azure-monitor/app/monitor-web-app-availability.md). Bármely olyan webhely számára elérhető, amely az Application Insights nak küld adatokat. Értesítést kap, ha egy webhely rendelkezésre állása vagy válaszképessége nem éri el az elvárásokat. |

## <a name="manage-alerts"></a>Riasztások kezelése
Beállíthatja, hogy a riasztás állapota megadja, hogy hol van a megoldási folyamatban. Ha a riasztási szabályban megadott feltételek teljesülnek, a rendszer riasztást hoz létre vagy küld el, és *állapota Új.* Módosíthatja az állapotot, amikor nyugtázza a riasztást, és amikor bezárja azt. Az összes állapotváltozás a riasztás előzményei között tárolódik.

A következő riasztási állapotok támogatottak.

| Állapot | Leírás |
|:---|:---|
| Új | A probléma csak most lett észlelve, és még nem lett felülvizsgálva. |
| Nyugtázva | Egy rendszergazda áttekintette a riasztást, és elkezdett dolgozni rajta. |
| Lezárt | A probléma megoldódott. A riasztás bezárása után újra megnyithatja azt egy másik állapotba módosításával. |

*A riasztás állapota* eltérő és független a *figyelő állapotától.* A riasztási állapotot a felhasználó állítja be. A figyelő feltételt a rendszer állítja be. Amikor egy riasztás aktiválódik, a riasztás figyelőállapota *ki van lőve.* Amikor a riasztást okozó alapfeltétel törlődik, a figyelő állapota *megoldódik.* A riasztás állapota nem változik, amíg a felhasználó nem módosítja azt. További információ [a riasztások és az intelligens csoportok állapotának módosításáról.](https://aka.ms/managing-alert-smart-group-states)

## <a name="smart-groups"></a>Intelligens csoportok 

Az intelligens csoportok gépi tanulási algoritmusokon alapuló riasztások összesítései, amelyek segíthetnek csökkenteni a riasztási zajt és a hibaelhárítást. [További információ az intelligens csoportokról](https://aka.ms/smart-groups) és [az intelligens csoportok kezeléséről.](https://aka.ms/managing-smart-groups)


## <a name="alerts-experience"></a>Riasztások élménye 
Az alapértelmezett Riasztások lap az adott időtartományon belül létrehozott riasztások összegzését tartalmazza. Megjeleníti az egyes súlyosságokra vonatkozó összes riasztást, és oszlopokat, amelyek azonosítják az egyes állapotok riasztásainak teljes számát az egyes súlyosságok esetében. Válassza ki a súlyosságok bármelyikét az adott súlyosság szerint szűrt [Minden riasztás](#all-alerts-page) lap megnyitásához.

Azt is megteheti, hogy [programozott módon számba veszi az előfizetéseken generált riasztási példányokat a REST API-k használatával.](#manage-your-alert-instances-programmatically)

> [!NOTE]
   >  Csak az elmúlt 30 napban létrehozott riasztásokhoz férhet hozzá.

Nem jelenít meg és nem követi nyomon a klasszikus riasztásokat. Módosíthatja az előfizetéseket vagy a szűrőparamétereket a lap frissítéséhez. 

![Képernyőkép a Riasztások lapról](media/alerts-overview/alerts-page.png)

Ezt a nézetet úgy szűrheti, hogy értékeket jelöl ki a lap tetején található legördülő menükben.

| Oszlop | Leírás |
|:---|:---|
| Előfizetés | Válassza ki azokat az Azure-előfizetéseket, amelyekhez meg szeretné tekinteni a riasztásokat. Választhatja ki az összes előfizetését. A nézetben csak azok a riasztások szerepelnek, amelyekhez hozzáférése van a kiválasztott előfizetésekben. |
| Erőforráscsoport | Egyetlen erőforráscsoport kijelölése. A nézetben csak a kijelölt erőforráscsoportban lévő célokkal rendelkező riasztások szerepelnek. |
| Időtartomány | A nézetben csak a kiválasztott időtartományon belül kilőtt riasztások szerepelnek. A támogatott értékek az elmúlt óra, az elmúlt 24 óra, az elmúlt 7 nap és az elmúlt 30 nap. |

Egy másik lap megnyitásához a következő értékeket válassza a Riasztások lap tetején:

| Érték | Leírás |
|:---|:---|
| Összes riasztás | A kiválasztott feltételeknek megfelelő riasztások teljes száma. Ezzel az értékkel szeretné megnyitni a Minden riasztás nézetet szűrő nélkül. |
| Intelligens csoportok | A kiválasztott feltételeknek megfelelő riasztásokból létrehozott intelligens csoportok teljes száma. Ezzel az értékkel nyissa meg az intelligens csoportok listáját a Minden riasztás nézetben.
| Összes riasztási szabály | A kiválasztott előfizetési és erőforráscsoportban lévő riasztási szabályok teljes száma. Ezzel az értékkel nyissa meg a kijelölt előfizetési és erőforráscsoportra szűrt Szabály nézetet.


## <a name="manage-alert-rules"></a>Riasztási szabályok kezelése
A **Szabályok** lap megjelenítéséhez válassza **a Figyelmeztetési szabályok kezelése**lehetőséget. A szabályok lap egyetlen hely az Azure-előfizetések összes riasztási szabályának kezelésére. Felsorolja az összes riasztási szabályt, és a célerőforrások, az erőforráscsoportok, a szabály neve vagy állapota alapján rendezhető. Ezen a lapon is szerkesztheti, engedélyezheti vagy letilthatja a riasztási szabályokat.  

 ![Képernyőkép a Szabályok lapról](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Riasztási szabály létrehozása
A figyelési szabályokat konzisztens módon hozhat meg, függetlenül a figyelési szolgáltatástól vagy a jel típusától.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
Új riasztási szabályt hozhat létre:
1. Válassza ki a _riasztás célját._
1. Válassza ki a _jelet_ a rendelkezésre álló jelek a cél.
1. Adja meg a jelből származó adatokra alkalmazandó _logikát._
 
Ez az egyszerűsített szerzői folyamat már nem szükséges ismernie a figyelési forrásvagy jelek, amelyek támogatják az Azure-erőforrás kiválasztása előtt. Az elérhető jelek listája automatikusan szűrve lesz a kiválasztott célerőforrás alapján. Szintén a cél alapján, a rendszer a riasztási szabály logikájának automatikus meghatározásán keresztül vezet.  

Az Azure Monitor használatával további információ arról, hogyan hozhat létre riasztási [szabályokat a Riasztások létrehozása, megtekintése és kezelése során.](../../azure-monitor/platform/alerts-metric.md)

Riasztások érhetők el számos Azure figyelési szolgáltatások. Az egyes szolgáltatások használatának módjáról és időpontjáról az [Azure-alkalmazások és -erőforrások figyelése című](../../azure-monitor/overview.md)témakörben talál további információt. 


## <a name="all-alerts-page"></a>Minden értesítés lap 
A **Minden értesítés** lap megtekintéséhez válassza az **Összes értesítés**lehetőséget. Itt tekintheti meg a kiválasztott időn belül létrehozott riasztások listáját. Megtekintheti az egyes riasztások listáját vagy a riasztásokat tartalmazó intelligens csoportok listáját. Jelölje ki a listatetején lévő szalagcímet a nézetek közötti váltáshoz.

![Képernyőkép az Összes értesítés lapról](media/alerts-overview/all-alerts-page.png)

A nézet szűréséhez jelölje ki a következő értékeket a lap tetején található legördülő menükben:

| Oszlop | Leírás |
|:---|:---|
| Előfizetés | Válassza ki azokat az Azure-előfizetéseket, amelyekhez meg szeretné tekinteni a riasztásokat. Választhatja ki az összes előfizetését. A nézetben csak azok a riasztások szerepelnek, amelyekhez hozzáférése van a kiválasztott előfizetésekben. |
| Erőforráscsoport | Egyetlen erőforráscsoport kijelölése. A nézetben csak a kijelölt erőforráscsoportban lévő célokkal rendelkező riasztások szerepelnek. |
| Erőforrás típusa | Jelöljön ki egy vagy több erőforrástípust. A nézetben csak a kiválasztott típusú célokkal rendelkező riasztások szerepelnek. Ez az oszlop csak egy erőforráscsoport megadása után érhető el. |
| Erőforrás | Jelöljön ki egy erőforrást. A nézetben csak az adott erőforrással rendelkező riasztások szerepelnek. Ez az oszlop csak erőforrástípus megadása után érhető el. |
| Severity | Válasszon egy riasztás súlyosságát, vagy válassza **az Összes** lehetőséget az összes súlyossági riasztásfelvételhez. |
| Figyelő állapota | Jelöljön ki egy figyelőfeltételt, vagy válassza **az Összes** lehetőséget az összes feltételre vonatkozó riasztások felvételéhez. |
| A riasztás állapota | Jelöljön ki egy riasztási állapotot, vagy válassza **az Összes** lehetőséget az összes állapot riasztásainak felvételéhez. |
| Figyelő szolgáltatás | Válasszon ki egy szolgáltatást, vagy válassza **az Összes** lehetőséget az összes szolgáltatás felvételéhez. Csak a szolgáltatás célként használó szabályok által létrehozott riasztások szerepelnek. |
| Időtartomány | A nézetben csak a kiválasztott időtartományon belül kilőtt riasztások szerepelnek. A támogatott értékek az elmúlt óra, az elmúlt 24 óra, az elmúlt 7 nap és az elmúlt 30 nap. |

Válassza az **Oszlopok** lehetőséget a lap tetején, és jelölje ki, hogy mely oszlopok jelenjenek meg. 

## <a name="alert-details-page"></a>Riasztás részletei lap
Amikor kiválaszt egy riasztást, ez a lap részletesen ismerteti a riasztást, és lehetővé teszi az állapotának módosítását.

![Képernyőkép a Riasztás részletei lapról](media/alerts-overview/alert-detail2.png)

A Riasztás részletei lap a következő szakaszokat tartalmazza:

| Section | Leírás |
|:---|:---|
| Összefoglalás | Megjeleníti a riasztások tulajdonságait és egyéb fontos információkat. |
| Előzmények | Felsorolja a riasztás által végrehajtott minden műveletet és a riasztás on-kban történt módosításait. Jelenleg csak az állapotváltozásokra korlátozódik. |
| Diagnosztika | Információ arról az intelligens csoportról, amelyben a riasztás szerepel. A *riasztások száma* az intelligens csoportban szereplő riasztások számát tartalmazza. Az elmúlt 30 napban létrehozott intelligens csoportban található egyéb riasztásokat is tartalmazza, függetlenül attól, hogy a riasztások listájában melyik időszűrő jelenik meg. Jelöljön ki egy riasztást a részletek megtekintéséhez. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) a riasztási példányokhoz

A riasztási példányok felhasználása és kezelése megköveteli, hogy a felhasználó beépített RBAC szerepkörrel rendelkezik a [figyelő közreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) vagy [a figyelési olvasó számára.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) Ezek a szerepkörök bármely Azure Resource Manager-hatókörben támogatottak, az előfizetési szinttől az erőforrásszintű részletes hozzárendelésekig. Ha például egy felhasználó csak a virtuális `ContosoVM1`gép közreműködői hozzáférésének figyelésével `ContosoVM1`rendelkezik, akkor a felhasználó csak a képernyőn létrehozott riasztásokat használhatja fel és kezelheti.

## <a name="manage-your-alert-instances-programmatically"></a>A riasztási példányok programozott kezelése

Előfordulhat, hogy programozott módon szeretné lekérdezni az előfizetéssel létrehozott riasztásokat. Ez lehet, hogy egyéni nézeteket hozzon létre az Azure Portalon kívül, vagy elemezze a riasztásokat a minták és trendek azonosításához.

Az előfizetések között létrehozott riasztások lekérdezése a [Riasztáskezelés REST API-jával,](https://aka.ms/alert-management-api) vagy az [Azure Resource Graph](../../governance/resource-graph/overview.md) és a REST API for [Resources](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)használatával.

A Resource Graph REST API erőforrások lehetővé teszi, hogy a riasztási példányok lekérdezése nagy méretekben. Ez akkor ajánlott, ha számos előfizetésben generált riasztásokat kell kezelnie. 

A következő mintakérelem a Resource Graph REST API-hoz egy előfizetésen belüli riasztások számát adja vissza:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

Az Erőforrásgráf-lekérdezés eredményét az Azure Resource Graph Explorer használatával is megtekintheti a portálon: [portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

Lekérdezheti a riasztásokat az [alapvető](alerts-common-schema-definitions.md#essentials) mezőket.

A [Riasztáskezelés REST API-val](https://aka.ms/alert-management-api) további információkat kaphat az adott riasztásokról, beleértve a [riasztási környezetmezőit](alerts-common-schema-definitions.md#alert-context) is.

## <a name="next-steps"></a>További lépések

- [További információ az intelligens csoportokról](https://aka.ms/smart-groups)
- [További információ a műveletcsoportokról](../../azure-monitor/platform/action-groups.md)
- [A riasztási példányok kezelése az Azure-ban](https://aka.ms/managing-alert-instances)
- [Intelligens csoportok kezelése](https://aka.ms/managing-smart-groups)
- [További információ az Azure-riasztások díjszabásáról](https://azure.microsoft.com/pricing/details/monitor/)






