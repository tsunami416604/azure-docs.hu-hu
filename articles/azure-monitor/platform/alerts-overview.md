---
title: A riasztások és az értesítések monitorozásának áttekintése az Azure-ban
description: Az Azure-beli riasztások áttekintése. Riasztások, klasszikus riasztások, a riasztások felülete.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/28/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 3ac6ec9fe55e901e593f201b5ff71668d80d66e3
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773008"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>A Microsoft Azure riasztások áttekintése 

Ez a cikk a riasztásokat, azok előnyeit és a használatának első lépéseit ismerteti.  




## <a name="what-are-alerts-in-microsoft-azure"></a>Mik a riasztások a Microsoft Azureban?
A riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a problémák azonosítását és megcímzését, mielőtt a felhasználók a rendszerértesítéseket. 

Ez a cikk a Azure Monitor egyesített riasztási felületét tárgyalja, amely mostantól Log Analytics és Application Insights által kezelt riasztásokat is tartalmaz. Az [előző riasztási élményt](alerts-classic.overview.md) és a riasztási típusokat **klasszikus riasztásoknak**nevezzük. A régebbi és a régebbi típusú riasztások megtekintéséhez kattintson a **klasszikus riasztások megtekintése** lehetőségre a riasztási oldal tetején. 

## <a name="overview"></a>Áttekintés

Az alábbi diagram a riasztások áramlását mutatja. 

![Riasztási folyamat](media/alerts-overview/Azure-Monitor-Alerts.svg)

A riasztási szabályok el vannak különítve a riasztásokból, valamint a riasztások által elindított műveletek. 

**Riasztási szabály** – a riasztási szabály rögzíti a riasztás célját és feltételeit. A riasztási szabály lehet engedélyezett vagy letiltott állapotban. A riasztások csak akkor gyulladnak ki, ha engedélyezve vannak. 

A riasztási szabályok legfontosabb attribútumai a következők:

**Cél erőforrás** – meghatározza a riasztáshoz elérhető hatókört és jeleket. A cél lehet bármilyen Azure-erőforrás. Példa: egy virtuális gép, egy Storage-fiók, egy virtuálisgép-méretezési csoport, egy Log Analytics munkaterület vagy egy Application Insights-erőforrás. Bizonyos erőforrások (például a Virtual Machines) esetében több erőforrást is megadhat a riasztási szabály céljaként.

A **jeleket a** cél erőforrás bocsátja ki, és számos típus lehet. Metrika, műveletnapló, Application Insights és napló.

**Feltételek** – a feltételek a cél erőforráson alkalmazott jel és logika kombinációja. Példák: 
   - Százalékos CPU > 70%
   - Kiszolgáló válaszideje > 4 MS 
   - > 100-es naplófájl-lekérdezés eredményének száma

**Riasztás neve** – a felhasználó által konfigurált riasztási szabály adott neve

**Riasztás leírása** – a felhasználó által konfigurált riasztási szabály leírása

**Súlyosság** – a riasztás súlyossága, ha a riasztási szabályban megadott feltételek teljesülnek. A súlyosság 0 és 4 között lehet.
   - 0 = kritikus
   - 1\. sz = hiba
   - 2\. szint = figyelmeztetés
   - 3\. szint = tájékoztató
   - 4\. szint = részletes 

**Művelet** – a riasztás indításakor végrehajtott művelet. További információ: [Action groups](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>A riasztás bekapcsolása

A metrikák és naplók riasztása az [adatforrások figyelése](../../azure-monitor/platform/data-sources-reference.md)című témakörben leírtak szerint végezhető el. Ilyenek például a következők:
- Metrika értékei
- Naplóbeli keresési lekérdezések
- Műveletnapló eseményei
- A mögöttes Azure platform állapota
- Webhely rendelkezésre állásának tesztelése

Korábban Azure Monitor a metrikák, a Application Insights, a Log Analytics és a Service Health külön riasztási képességekkel rendelkeztek. Az Azure az idő múlásával javította és kombinálja a felhasználói felületet és a riasztások különböző módszereit. Ez az összevonás még folyamatban van. Ennek eredményeképpen még mindig vannak olyan riasztási képességek, amelyek még nincsenek az új riasztások rendszerében.  

| **Forrás figyelése** | **Jel típusa**  | **Leírás** | 
|-------------|----------------|-------------|
| Szolgáltatások állapota | Tevékenységnapló  | Nem támogatott. Lásd: [műveletnapló riasztások létrehozása a szolgáltatási értesítéseken](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Webes rendelkezésre állási tesztek | Nem támogatott. Tekintse meg a [webes tesztek riasztásait](../../azure-monitor/app/monitor-web-app-availability.md). Minden olyan webhely számára elérhető, amely az adatküldés Application Insights. Értesítést kap, ha a webhely rendelkezésre állása vagy a válaszadás a vártnál kisebb. |

## <a name="manage-alerts"></a>Riasztások kezelése
Beállíthatja egy riasztás állapotát, hogy meghatározza, hol található a megoldási folyamat. Ha a riasztási szabályban megadott feltételek teljesülnek, a rendszer létrehoz vagy kilőtt egy riasztást, amely *új*állapottal rendelkezik. Megváltoztathatja az állapotot, amikor visszaigazolja a riasztást, és amikor bezárta. A rendszer az összes állapotjelzőt a riasztás előzményeiben tárolja.

A következő riasztási állapotok támogatottak.

| State | Leírás |
|:---|:---|
| Új | A probléma nemrég észlelhető, és még nem lett ellenőrizve. |
| Arra vonatkozik | A rendszergazda áttekintette a riasztást, és megkezdte a munkát. |
| Lezárva | A probléma megoldódott. A riasztások bezárása után újra megnyithatja azt egy másik állapotba való módosításával. |

A **riasztási állapot** különbözik, és független a **figyelő feltételtől**. A riasztási állapotot a felhasználó állítja be. A figyelő feltételét a rendszer állítja be. Riasztás esetén a riasztás figyelési feltétele az elindított állapotra van *állítva.* Ha az alapul szolgáló feltétel, amely miatt a riasztás tüzet töröl, a figyelő feltétele *megoldottra*van állítva. A riasztási állapot addig nem változik, amíg a felhasználó nem módosítja. Útmutató a [riasztások és az intelligens csoportok állapotának módosításához](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Intelligens csoportok 
Az intelligens csoportok előzetes verzióban érhetők el. 

Az intelligens csoportok a gépi tanulási algoritmusok alapján összesítik a riasztásokat, ami segít csökkenteni a riasztási zajt és a segítségét a hibaelhárításban. [További információ az intelligens csoportokról](https://aka.ms/smart-groups) és [az intelligens csoportok kezeléséről](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Riasztások felhasználói élmény 
Az alapértelmezett riasztások lap egy adott időszakon belül létrehozott riasztások összegzését tartalmazza. Megjeleníti az egyes súlyossági szintekhez tartozó összes riasztást, valamint azokat az oszlopokat, amelyek meghatározzák az egyes állapotokban lévő riasztások teljes számát az egyes súlyossági szintekhez. Válassza ki bármelyik csomópontot a súlyosság szerint szűrt [összes riasztás](#all-alerts-page) oldal megnyitásához.

Azt is megteheti, hogy [programozott módon enumerálja az előfizetése (ke) hoz generált riasztási példányokat a REST API-k használatával](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  Csak az elmúlt 30 napban generált riasztások érhetők el az UX-en vagy a REST API-kon keresztül.

Nem jeleníti meg és nem követi a régebbi [klasszikus riasztásokat](#classic-alerts). Módosíthatja az előfizetéseket vagy a szűrő paramétereit az oldal frissítéséhez. 

![Riasztások lap](media/alerts-overview/alerts-page.png)

A nézet szűréséhez válassza az értékek lehetőséget a lap tetején található legördülő menükben.

| Oszlop | Leírás |
|:---|:---|
| Subscription | Válassza ki azokat az Azure-előfizetéseket, amelyekhez meg szeretné tekinteni a riasztásokat. Igény szerint kiválaszthatja az összes előfizetést. Csak a kiválasztott előfizetésekben elérhető riasztások szerepelnek a nézetben. |
| Resource group | Válasszon ki egy erőforráscsoportot. Csak a kijelölt erőforráscsoport céljaival rendelkező riasztások szerepelnek a nézetben. |
| Időtartomány | A nézet csak a kijelölt időintervallumban kilőtt riasztásokat tartalmazza. A támogatott értékek az elmúlt óra, az elmúlt 24 óra, az elmúlt 7 nap és az elmúlt 30 nap. |

Válassza ki az alábbi értékeket a riasztások lap tetején egy másik oldal megnyitásához.

| Value | Leírás |
|:---|:---|
| Riasztások összesen | A kiválasztott feltételeknek megfelelő riasztások teljes száma. Válassza ezt az értéket, ha a minden riasztás nézetet szűrő nélkül szeretné megnyitni. |
| Intelligens csoportok | Azon intelligens csoportok teljes száma, amelyek a kiválasztott feltételeknek megfelelő riasztásokból lettek létrehozva. Válassza ezt az értéket az intelligens csoportok listájának az összes riasztás nézetben való megnyitásához.
| Riasztási szabályok összesen | A kiválasztott előfizetéshez és erőforráscsoporthoz tartozó riasztási szabályok teljes száma. Ezzel az értékkel megnyithatja a kijelölt előfizetésben és erőforráscsoporthoz szűrt szabályok nézetet.


## <a name="manage-alert-rules"></a>Riasztási szabályok kezelése
A **szabályok** lap megjelenítéséhez kattintson a **riasztási szabályok kezelése** elemre. A **szabályok** egyetlen helyet biztosítanak az összes riasztási szabály kezeléséhez az Azure-előfizetések között. Felsorolja az összes riasztási szabályt, és a cél erőforrásai, erőforráscsoportok, szabály neve vagy állapota alapján rendezhető. Ezen a lapon a riasztási szabályok szerkeszthetők, engedélyezhetők vagy Letiltva is lehetnek.  

 ![riasztások – szabályok](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Riasztási szabály létrehozása
A riasztások a figyelési szolgáltatástól vagy a jel típusától függetlenül konzisztens módon hozhatók létre. Az összes kilőtt riasztás és a kapcsolódó részletek egyetlen lapon érhetők el.
 
Hozzon létre egy új riasztási szabályt a következő három lépéssel:
1. Válassza ki a riasztás _célját_ .
1. Válassza ki a kívánt _jelet_ a cél számára elérhető jelek közül.
1. Itt adhatja meg a jel alapján az adatokra alkalmazandó _logikát_ .
 
Ez az egyszerűsített létrehozási folyamat már nem igényli az Azure-erőforrások kiválasztása előtt a támogatott megfigyelési forrás vagy jelek megismerését. A rendszer automatikusan szűri az elérhető jelek listáját a kiválasztott cél erőforrás alapján. Emellett a cél alapján végigvezeti a riasztási szabály automatikus logikájának meghatározásán.  

További információt a riasztási szabályok létrehozásával kapcsolatban a [riasztások létrehozása, megtekintése és kezelése Azure monitor segítségével](../../azure-monitor/platform/alerts-metric.md)című témakörben olvashat.

A riasztások több Azure monitoring-szolgáltatásban is elérhetők. További információ az egyes szolgáltatások használatának módjáról és használatáról: [Azure-alkalmazások és-erőforrások figyelése](../../azure-monitor/overview.md). 


## <a name="all-alerts-page"></a>Minden riasztás lap 
A minden riasztás lap megjelenítéséhez kattintson az összes riasztás elemre. Itt megtekintheti a kijelölt időtartományon belül létrehozott riasztások listáját. Megtekintheti az egyes riasztások listáját vagy a riasztásokat tartalmazó intelligens csoportok listáját. Válassza ki az oldal tetején található szalagcímet a nézetek közötti váltáshoz.

![Minden riasztás lap](media/alerts-overview/all-alerts-page.png)

A nézet szűréséhez válassza ki a következő értékeket az oldal tetején található legördülő menükben.

| Oszlop | Leírás |
|:---|:---|
| Subscription | Válassza ki azokat az Azure-előfizetéseket, amelyekhez meg szeretné tekinteni a riasztásokat. Igény szerint kiválaszthatja az összes előfizetést. Csak a kiválasztott előfizetésekben elérhető riasztások szerepelnek a nézetben. |
| Resource group | Válasszon ki egy erőforráscsoportot. Csak a kijelölt erőforráscsoport céljaival rendelkező riasztások szerepelnek a nézetben. |
| Erőforrás típusa | Válasszon ki egy vagy több erőforrástípust. Csak a kiválasztott típusú célokat tartalmazó riasztások szerepelnek a nézetben. Ez az oszlop csak az erőforráscsoport megadása után érhető el. |
| Resource | Válasszon ki egy erőforrást. A nézet csak az adott erőforrással rendelkező riasztásokat tartalmazza célként. Ez az oszlop csak az erőforrástípus megadása után érhető el. |
| severity | Válassza ki a riasztás súlyosságát, vagy válassza az *összes* lehetőséget az összes súlyossági szintű riasztás befoglalásához. |
| Figyelőre érvényes feltétel | Válasszon ki egy figyelési feltételt, vagy válassza az *összes* lehetőséget a feltételek riasztásához. |
| Riasztás állapota | Válasszon ki egy riasztási állapotot, vagy válassza az *összes* lehetőséget az állapotok riasztásának belefoglalásához. |
| Szolgáltatás monitorozása | Válasszon ki egy szolgáltatást, vagy válassza az *összes* lehetőséget az összes szolgáltatás belefoglalásához. A rendszer csak a szolgáltatást célként használó szabályok által létrehozott riasztásokat tartalmazza. |
| Időtartomány | A nézet csak a kijelölt időintervallumban kilőtt riasztásokat tartalmazza. A támogatott értékek az elmúlt óra, az elmúlt 24 óra, az elmúlt 7 nap és az elmúlt 30 nap. |

Válassza ki az **oszlopok** elemet a lap tetején a megjelenítendő oszlopok kiválasztásához. 

## <a name="alert-details-page"></a>Riasztás részletei lap
A riasztás részletei lap jelenik meg, amikor kiválaszt egy riasztást. Részletesen ismerteti a riasztás adatait, és lehetővé teszi az állapotának módosítását.

![Riasztás részletei](media/alerts-overview/alert-detail2.png)

A riasztás részletei lap a következő fejezeteket tartalmazza.

| `Section` | Leírás |
|:---|:---|
| Összegzés | Megjeleníti a riasztás tulajdonságait és egyéb fontos információit. |
| Előzmények | Felsorolja a riasztás által végrehajtott műveleteket és a riasztáson végrehajtott módosításokat. Jelenleg csak az állapot változásaira korlátozódik. |
| Diagnosztika | Az intelligens csoportra vonatkozó információk a riasztást tartalmazzák. A *riasztások* száma az intelligens csoportba tartozó riasztások számára utal. Más riasztásokat is tartalmaz ugyanabban az intelligens csoportban, amelyek az elmúlt 30 napban lettek létrehozva, a riasztások listája lapon lévő időszűrőtől függetlenül. Válasszon ki egy riasztást a részletes adatok megtekintéséhez. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) a riasztási példányokhoz

A riasztási példányok felhasználása és kezelése megköveteli, hogy a felhasználó rendelkezzen a [figyelési közreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) vagy [figyelő olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)beépített RBAC szerepköreivel. Ezeket a szerepköröket bármely Azure Resource Manager hatókör támogatja, az előfizetési szinttől kezdve a részletes hozzárendelésekhez az erőforrás szintjén. Ha például egy felhasználó csak a "ContosoVM1" virtuális gép "figyelő közreműködői" hozzáférését használja, akkor csak a "ContosoVM1" által generált riasztásokat használhatja fel és kezelheti.

## <a name="manage-your-alert-instances-programmatically"></a>Riasztási példányok programozott kezelése

Számos esetben érdemes az előfizetéssel létrehozott riasztásokat programozott módon lekérdezni. Ez lehet egyéni nézetek létrehozása a Azure Portalon kívül, vagy a riasztások elemzése a minták és trendek azonosításához.

Az előfizetésekhez kapcsolódó riasztásokat a [Alert Management REST API](https://aka.ms/alert-management-api) vagy a [riasztások Azure Resource Graph REST API](https://docs.microsoft.com/rest/api/azureresourcegraph/resources/resources)használatával kérdezheti le.

A [riasztásokhoz készült Azure Resource Graph REST API](https://docs.microsoft.com/rest/api/azureresourcegraph/resources/resources) lehetővé teszi a riasztási példányok lekérdezését a skálán. Ez olyan esetekben ajánlott, amikor a számos előfizetésben létrehozott riasztásokat kell kezelnie. 

Az API-hoz tartozó következő minta-kérelem egy előfizetésben lévő riasztások számát adja vissza:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()",
  "options": {
            "dataset":"alerts"
  }
}
```
A riasztásokat lekérdezheti az ["alapvető"](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields) mezőkre.

A [Alert Management REST API](https://aka.ms/alert-management-api) segítségével további információkhoz juthat az adott riasztásokról, beleértve a [riasztási környezet](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) mezőit is.

## <a name="classic-alerts"></a>Klasszikus riasztások 

A Azure Monitor-mérőszámok és a műveletnapló riasztási képességei a 2018 júniusa előtt "riasztások (klasszikus)" néven jelennek meg. 

További információ: [klasszikus riasztások](./../../azure-monitor/platform/alerts-classic.overview.md)


## <a name="next-steps"></a>További lépések

- [További információ az intelligens csoportokról](https://aka.ms/smart-groups)
- [A műveleti csoportok ismertetése](../../azure-monitor/platform/action-groups.md)
- [Riasztási példányok kezelése az Azure-ban](https://aka.ms/managing-alert-instances)
- [Intelligens csoportok kezelése](https://aka.ms/managing-smart-groups)






