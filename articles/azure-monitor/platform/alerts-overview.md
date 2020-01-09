---
title: A riasztások és az értesítések monitorozásának áttekintése az Azure-ban
description: Az Azure-beli riasztások áttekintése. Riasztások, klasszikus riasztások és a riasztások felülete.
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 01/28/2018
ms.openlocfilehash: 26516b99f3ffd9a16a24a4d5d1906ed781a8034a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75396522"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>A Microsoft Azure riasztások áttekintése 

Ez a cikk a riasztásokat, azok előnyeit és a használatának első lépéseit ismerteti.  




## <a name="what-are-alerts-in-microsoft-azure"></a>Mik a riasztások a Microsoft Azureban?
A riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a problémák azonosítását és megcímzését, mielőtt a felhasználók a rendszerértesítéseket. 

Ez a cikk a Azure Monitor egységes riasztási szolgáltatását ismerteti, amely a korábban Log Analytics és Application Insights által kezelt riasztásokat tartalmaz. Az [előző riasztási élményt](alerts-classic.overview.md) és a riasztási típusokat *klasszikus riasztásoknak*nevezzük. A régi és a régebbi típusú riasztás típusát a riasztási oldal tetején található **klasszikus riasztások megtekintése** lehetőség kiválasztásával tekintheti meg. 

## <a name="overview"></a>Áttekintés

Az alábbi diagram a riasztások áramlását mutatja. 

![Riasztási folyamat ábrája](media/alerts-overview/Azure-Monitor-Alerts.svg)

A riasztási szabályok el vannak különítve a riasztásokból, és a riasztások indításakor végrehajtott műveletek. A riasztási szabály rögzíti a riasztás célját és feltételeit. A riasztási szabály lehet engedélyezett vagy letiltott állapotban. A riasztások csak akkor gyulladnak ki, ha engedélyezve vannak. 

A riasztási szabályok legfontosabb attribútumai a következők:

**Cél erőforrás**: meghatározza a riasztáshoz elérhető hatókört és jeleket. A cél lehet bármilyen Azure-erőforrás. Példa: egy virtuális gép, egy Storage-fiók, egy virtuálisgép-méretezési csoport, egy Log Analytics munkaterület vagy egy Application Insights-erőforrás. Bizonyos erőforrások (például a virtuális gépek) esetében több erőforrást is megadhat a riasztási szabály céljaként.

**Jel**: a cél erőforrás által kibocsátva. A jelek a következő típusúak lehetnek: metrika, műveletnapló, Application Insights és napló.

**Feltételek**: a cél erőforráson alkalmazott jel és logika kombinációja. Példák: 
   - Százalékos CPU > 70%
   - Kiszolgáló válaszideje > 4 MS 
   - > 100-es naplófájl-lekérdezés eredményének száma

**Riasztás neve**: a felhasználó által konfigurált riasztási szabály konkrét neve.

**Riasztás leírása**: a felhasználó által konfigurált riasztási szabály leírása.

**Súlyosság**: a riasztás súlyossága a riasztási szabályban megadott feltételek teljesülése után teljesül. A súlyosság 0 és 4 között lehet.
   - 0 = kritikus
   - 1\. sz = hiba
   - 2\. szint = figyelmeztetés
   - 3\. szint = tájékoztató
   - 4\. szint = részletes 

**Művelet**: a riasztás indításakor végrehajtott művelet. További információ: [Action groups](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>A riasztás bekapcsolása

Az [adatforrások figyelése](../../azure-monitor/platform/data-sources-reference.md)című témakörben leírtak szerint riasztást kaphat a mérőszámokról és naplókról. Ezeket a következőkben ismertetjük a teljesség igénye nélkül:
- Metrika értékei
- Naplóbeli keresési lekérdezések
- Tevékenységnapló eseményei
- A mögöttes Azure-platform állapota
- Webhely rendelkezésre állására vonatkozó tesztek

Korábban Azure Monitor a metrikák, a Application Insights, a Log Analytics és a Service Health külön riasztási képességekkel rendelkeztek. Az Azure az idő múlásával javította és kombinálja a felhasználói felületet és a riasztások különböző módszereit. Ez az összevonás még folyamatban van. Ennek eredményeképpen még mindig vannak olyan riasztási képességek, amelyek még nincsenek az új riasztások rendszerében.  

| **Forrás figyelése** | **Jel típusa**  | **Leírás** | 
|-------------|----------------|-------------|
| Szolgáltatások állapota | Tevékenységnapló  | Nem támogatott. Lásd: [műveletnapló riasztások létrehozása a szolgáltatási értesítéseken](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Webes rendelkezésre állási tesztek | Nem támogatott. Tekintse meg a [webes tesztek riasztásait](../../azure-monitor/app/monitor-web-app-availability.md). Minden olyan webhely számára elérhető, amely az adatküldés Application Insights. Értesítést kap, ha a webhely rendelkezésre állása vagy a válaszadás a vártnál kisebb. |

## <a name="manage-alerts"></a>Riasztások kezelése
Beállíthatja egy riasztás állapotát, hogy meghatározza, hol található a megoldási folyamat. Ha a riasztási szabályban megadott feltételek teljesülnek, a rendszer riasztást hoz létre vagy tüzelt, és *új*állapottal rendelkezik. Megváltoztathatja az állapotot, amikor visszaigazolja a riasztást, és amikor bezárta. A rendszer az összes állapotjelzőt a riasztás előzményeiben tárolja.

A következő riasztási állapotok támogatottak.

| Állami | Leírás |
|:---|:---|
| Új | A probléma nemrég észlelhető, és még nincs áttekintve. |
| Nyugtázva | A rendszergazda áttekintette a riasztást, és megkezdte a munkát. |
| Lezárt | A probléma megoldódott. A riasztások bezárása után újra megnyithatja azt egy másik állapotba való módosításával. |

A *riasztási állapot* különbözik, és független a *figyelő feltételtől*. A riasztási állapotot a felhasználó állítja be. A figyelő feltételét a rendszer állítja be. Riasztás esetén a riasztás figyelési feltétele az elindított állapotra van *állítva.* Ha az alapul szolgáló feltétel, amely miatt a riasztás tüzet töröl, a figyelő feltétele *megoldottra*van állítva. A riasztási állapot addig nem változik, amíg a felhasználó nem módosítja. Útmutató a [riasztások és az intelligens csoportok állapotának módosításához](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Intelligens csoportok 

Az intelligens csoportok a gépi tanulási algoritmusok alapján összesítik a riasztásokat, ami segít csökkenteni a riasztási zajt és a hibaelhárítási segítséget. [További információ az intelligens csoportokról](https://aka.ms/smart-groups) és [az intelligens csoportok kezeléséről](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Riasztások felhasználói élmény 
Az alapértelmezett riasztások lap egy adott időtartományon belül létrehozott riasztások összegzését tartalmazza. Megjeleníti az egyes súlyossági szintekhez tartozó összes riasztást, és azokat az oszlopokat, amelyek meghatározzák az egyes állapotokban lévő riasztások teljes számát. Válassza ki bármelyik csomópontot a súlyosság szerint szűrt [összes riasztás](#all-alerts-page) oldal megnyitásához.

Azt is megteheti, hogy [programozott módon enumerálja az előfizetésekben generált riasztási példányokat a REST API](#manage-your-alert-instances-programmatically)-k használatával.

> [!NOTE]
   >  Csak az elmúlt 30 napban generált riasztások érhetők el.

A klasszikus riasztások nem jelennek meg vagy követhetik nyomon. Módosíthatja az előfizetéseket vagy a szűrő paramétereit az oldal frissítéséhez. 

![A riasztások oldalának képernyőképe](media/alerts-overview/alerts-page.png)

A nézet szűréséhez válassza az értékek lehetőséget a lap tetején található legördülő menükben.

| Column | Leírás |
|:---|:---|
| Előfizetés | Válassza ki azokat az Azure-előfizetéseket, amelyeken meg szeretné tekinteni a riasztásokat. Igény szerint kiválaszthatja az összes előfizetést. Csak a kiválasztott előfizetésekben elérhető riasztások szerepelnek a nézetben. |
| Erőforráscsoport | Válasszon ki egy erőforráscsoportot. Csak a kijelölt erőforráscsoport céljaival rendelkező riasztások szerepelnek a nézetben. |
| Időtartomány | Csak a kijelölt időtartományon belül indított riasztások szerepelnek a nézetben. A támogatott értékek az elmúlt óra, az elmúlt 24 óra, az elmúlt 7 nap és az elmúlt 30 nap. |

Válassza ki az alábbi értékeket a riasztások lap tetején egy másik oldal megnyitásához:

| Value (Díj) | Leírás |
|:---|:---|
| Összes riasztás | A kiválasztott feltételeknek megfelelő riasztások teljes száma. Válassza ezt az értéket, ha a minden riasztás nézetet szűrő nélkül szeretné megnyitni. |
| Intelligens csoportok | Azon intelligens csoportok teljes száma, amelyek a kiválasztott feltételeknek megfelelő riasztásokból lettek létrehozva. Válassza ezt az értéket az intelligens csoportok listájának az összes riasztás nézetben való megnyitásához.
| Összes riasztási szabály | A kiválasztott előfizetéshez és erőforráscsoporthoz tartozó riasztási szabályok teljes száma. Ezzel az értékkel megnyithatja a kijelölt előfizetésben és erőforráscsoporthoz szűrt szabályok nézetet.


## <a name="manage-alert-rules"></a>Riasztási szabályok kezelése
A **szabályok** megjelenítéséhez válassza a **riasztási szabályok kezelése**lehetőséget. A szabályok lap egyetlen helyet biztosít az összes riasztási szabály kezeléséhez az Azure-előfizetések között. Felsorolja az összes riasztási szabályt, és a cél erőforrásai, erőforráscsoportok, szabály neve vagy állapota alapján rendezhető. Ezen a lapon is szerkesztheti, engedélyezheti vagy letilthatja a riasztási szabályokat.  

 ![Képernyőfelvétel a szabályok lapról](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Riasztási szabály létrehozása
A riasztásokat következetes módon hozhatja létre, a figyelési szolgáltatástól vagy a jel típusától függetlenül. Az összes kilőtt riasztás és a kapcsolódó részletek egyetlen lapon érhetők el.
 
Ebből a cikkből megtudhatja, hogyan hozhat létre új riasztási szabályt:
1. Válassza ki a riasztás _célját_ .
1. Válassza ki a kívánt _jelet_ a cél számára elérhető jelek közül.
1. Itt adhatja meg a jel alapján az adatokra alkalmazandó _logikát_ .
 
Ez az egyszerűsített létrehozási folyamat már nem igényli az Azure-erőforrások kiválasztása előtt a támogatott megfigyelési forrás vagy jelek megismerését. A rendszer automatikusan szűri az elérhető jelek listáját a kiválasztott cél erőforrás alapján. Emellett a cél alapján végigvezeti a riasztási szabály automatikus logikájának meghatározásán.  

További információt a riasztási szabályok létrehozásával kapcsolatban a [riasztások létrehozása, megtekintése és kezelése Azure monitor segítségével](../../azure-monitor/platform/alerts-metric.md)című témakörben olvashat.

A riasztások több Azure monitoring-szolgáltatásban is elérhetők. További információ az egyes szolgáltatások használatának módjáról és használatáról: [Azure-alkalmazások és-erőforrások figyelése](../../azure-monitor/overview.md). 


## <a name="all-alerts-page"></a>Minden riasztás lap 
A **minden riasztás** lap megjelenítéséhez válassza az összes **riasztás**elemet. Itt megtekintheti a kiválasztott időpontban létrehozott riasztások listáját. Megtekintheti az egyes riasztások listáját vagy a riasztásokat tartalmazó intelligens csoportok listáját. Válassza ki az oldal tetején található szalagcímet a nézetek közötti váltáshoz.

![Képernyőkép az összes riasztás oldalról](media/alerts-overview/all-alerts-page.png)

A nézet szűréséhez válassza a következő értékeket az oldal tetején található legördülő menükben:

| Column | Leírás |
|:---|:---|
| Előfizetés | Válassza ki azokat az Azure-előfizetéseket, amelyeken meg szeretné tekinteni a riasztásokat. Igény szerint kiválaszthatja az összes előfizetést. Csak a kiválasztott előfizetésekben elérhető riasztások szerepelnek a nézetben. |
| Erőforráscsoport | Válasszon ki egy erőforráscsoportot. Csak a kijelölt erőforráscsoport céljaival rendelkező riasztások szerepelnek a nézetben. |
| Erőforrás típusa | Válasszon ki egy vagy több erőforrástípust. Csak a kiválasztott típusú célokat tartalmazó riasztások szerepelnek a nézetben. Ez az oszlop csak az erőforráscsoport megadása után érhető el. |
| Erőforrás | Válasszon ki egy erőforrást. A nézet csak az adott erőforrással rendelkező riasztásokat tartalmazza célként. Ez az oszlop csak az erőforrástípus megadása után érhető el. |
| Súlyosság | Válassza ki a riasztás súlyosságát, vagy válassza az **összes** lehetőséget az összes súlyossági szintű riasztás befoglalásához. |
| A figyelőre érvényes feltétel | Válasszon ki egy figyelési feltételt, vagy válassza az **összes** lehetőséget az összes feltétel riasztásának befoglalásához. |
| A riasztás állapota | Válasszon ki egy riasztási állapotot, vagy válassza az **összes** lehetőséget az összes állapotú riasztások belefoglalásához. |
| Monitorozási szolgáltatás | Válasszon ki egy szolgáltatást, vagy válassza az **összes** lehetőséget az összes szolgáltatás belefoglalásához. A rendszer csak a szolgáltatást célként használó szabályok által létrehozott riasztásokat tartalmazza. |
| Időtartomány | Csak a kijelölt időtartományon belül indított riasztások szerepelnek a nézetben. A támogatott értékek az elmúlt óra, az elmúlt 24 óra, az elmúlt 7 nap és az elmúlt 30 nap. |

Válassza ki az **oszlopok** elemet a lap tetején a megjelenítendő oszlopok kiválasztásához. 

## <a name="alert-details-page"></a>Riasztás részletei lap
Ha kiválaszt egy riasztást, ez a lap a riasztás részleteit jeleníti meg, és lehetővé teszi az állapotának módosítását.

![A riasztás részletei lap képernyőképe](media/alerts-overview/alert-detail2.png)

A riasztás részletei lapon a következő témakörök találhatók:

| Section | Leírás |
|:---|:---|
| Összefoglalás | Megjeleníti a riasztás tulajdonságait és egyéb fontos információit. |
| Előzmények | Felsorolja a riasztás által végrehajtott műveleteket és a riasztáson végrehajtott módosításokat. Jelenleg csak az állapot változásaira korlátozódik. |
| Diagnosztika | Az intelligens csoportra vonatkozó információk, amelyekben a riasztás szerepel. A *riasztások* száma az intelligens csoportba tartozó riasztások számára utal. Az adott intelligens csoportban lévő, az elmúlt 30 napban létrehozott riasztásokat is tartalmazza, a riasztások listája lapon lévő időszűrőtől függetlenül. Válasszon ki egy riasztást a részletes adatok megtekintéséhez. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) a riasztási példányokhoz

A riasztási példányok felhasználása és kezelése megköveteli, hogy a felhasználó rendelkezzen a [figyelési közreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) vagy [figyelő olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)beépített RBAC szerepköreivel. Ezek a szerepkörök bármely Azure Resource Manager hatókörön támogatottak, az előfizetési szinttől kezdve a részletes hozzárendelésekhez az erőforrás szintjén. Ha például egy felhasználó csak a virtuális gép `ContosoVM1`figyeli a közreműködők hozzáférését, a felhasználó csak `ContosoVM1`által generált riasztásokat tud felhasználni és kezelni.

## <a name="manage-your-alert-instances-programmatically"></a>Riasztási példányok programozott kezelése

Előfordulhat, hogy programozott módon kívánja lekérdezni az előfizetéssel létrehozott riasztásokat. Előfordulhat, hogy a Azure Portalon kívül egyéni nézeteket is létre kell hoznia, vagy elemezni kell a riasztásokat a minták és trendek azonosításához.

Az előfizetésekhez kapcsolódó riasztásokat a [Alert Management REST API](https://aka.ms/alert-management-api) vagy az [Azure Resource Graph](../../governance/resource-graph/overview.md) és az [erőforrások REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)használatával kérdezheti le.

Az erőforrások erőforrás-gráf REST API lehetővé teszi a riasztási példányok lekérdezését a skálán. Ez akkor ajánlott, ha több előfizetésben létrehozott riasztásokat kell kezelnie. 

A következő minta-kérelem az erőforrás-gráf REST API visszaadja egy előfizetésen belüli riasztások számát:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

Ezen erőforrás-gráf lekérdezés eredményét a portálon is láthatja az Azure Resource Graph Explorer használatával: [Portal.Azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

A riasztásokat lekérdezheti az [alapvető](alerts-common-schema-definitions.md#essentials) mezőire.

Az [Alert Management REST API](https://aka.ms/alert-management-api) segítségével további információkat kaphat az egyes riasztásokról, például a [riasztási környezet](alerts-common-schema-definitions.md#alert-context) mezőiről.

## <a name="next-steps"></a>Következő lépések

- [További információ az intelligens csoportokról](https://aka.ms/smart-groups)
- [A műveleti csoportok ismertetése](../../azure-monitor/platform/action-groups.md)
- [Riasztási példányok kezelése az Azure-ban](https://aka.ms/managing-alert-instances)
- [Intelligens csoportok kezelése](https://aka.ms/managing-smart-groups)
- [További információ az Azure-riasztások díjszabásáról](https://azure.microsoft.com/pricing/details/monitor/)






