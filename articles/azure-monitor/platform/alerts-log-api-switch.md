---
title: Váltás az örökölt Log Analytics-riasztások API-ról az új Azure Alerts API-ra
description: Az örökölt mentettKeresés alapú Log Analytics Riasztási API áttekintése és a riasztási szabályok új ScheduledQueryRules API-ra való váltásának folyamata, a gyakori ügyfélproblémákat megválaszoló részletekkel.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: c1ca85ba9c79f828b5ddcf0d640cfe7f8b6e097c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249437"
---
# <a name="switch-api-preference-for-log-alerts"></a>A naplóértesítések API-preferenciájának váltása

> [!NOTE]
> A felhasználók számára megadott tartalom csak az Azure-alapú nyilvános felhőben, az Azure Government vagy az Azure China felhő esetében **nem.**  

> [!NOTE]
> Ha a felhasználó úgy dönt, hogy az új [scheduledQueryRules API-t](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) részesíti előnyben, nem lehet visszaállítani a [régebbi, régebbi Log Analytics Riasztási API](api-alerts.md)használatát.

Egészen a közelmúltig a Microsoft Operations Management Suite portálon felügyelte a riasztási szabályokat. Az új riasztások felületét integrálták a Microsoft Azure különböző szolgáltatásaival, beleértve a Log Analytics szolgáltatást is, és kértük, hogy [az OMS-portálról az Azure-ra terjessze ki a riasztási szabályokat.](alerts-extend.md) De annak érdekében, hogy minimális zavar az ügyfelek számára, a folyamat nem változtatta meg a programozott felület a fogyasztás - [Log Analytics Alert API](api-alerts.md) alapján SavedSearch.

De most bejelenti a Log Analytics riasztást a felhasználók egy igazi Azure programozott alternatíva, [Az Azure Monitor - ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), amely szintén tükrözi az [Azure-számlázás - a naplóriasztások.](alerts-unified-log.md#pricing-and-billing-of-log-alerts) Ha többet szeretne tudni arról, hogyan kezelheti a naplóriasztásokat az API használatával, olvassa el [a naplóriasztások kezelése](alerts-log.md#managing-log-alerts-using-azure-resource-template) az Azure Resource Template használatával és [a naplóriasztások kezelése a PowerShell használatával című témakört.](alerts-log.md#managing-log-alerts-using-powershell)

## <a name="benefits-of-switching-to-new-azure-api"></a>Az új Azure API-ra való áttérés előnyei

A riasztások létrehozása és kezelése számos előnnyel jár az [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatával az [örökölt Log Analytics Riasztási API-val](api-alerts.md)szemben; már felsorolt néhány, a főbb az alábbiakban:

- A [munkaterületi naplókeresés átvizsgálása](../log-query/cross-workspace-query.md) riasztási szabályokban és külső erőforrások, például a Log Analytics-munkaterületek vagy akár az Application Insights-alkalmazások felölelődése
- Ha több mezőt használnak a lekérdezéscsoportosításhoz, [az scheduledQueryRules API-felhasználó](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) megadhatja, hogy melyik mező összesíthető az Azure Portalon
- Az [scheduledQueryRules API-val](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) létrehozott naplóriasztások időszaka legfeljebb 48 óra lehet, és az adatok lehívása hosszabb ideig tart, mint korábban
- Riasztási szabályok létrehozása egyetlen felvételen egyetlen erőforrásként anélkül, hogy három erőforrásszintet kellene létrehoznia a [régi Log Analytics riasztási API-val](api-alerts.md)
- Egyetlen programozott felület a lekérdezésalapú naplóriasztások összes változatához az Azure-ban – az új [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használható a Log Analytics és az Application Insights szabályainak kezelésére
- A naplóriasztások kezelése [Powershell-parancsmagokkal](alerts-log.md#managing-log-alerts-using-powershell)
- Minden új naplóriasztási funkció és jövőbeli fejlesztés csak az új [scheduledQueryRules API-n](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) keresztül lesz elérhető

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Az örökölt naplóriasztások API-ról való váltás folyamata

A felhasználók szabadon használhatják az [örökölt Log Analytics Riasztási API-t](api-alerts.md) vagy az új [scheduledQueryRules API-t.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) A bármelyik API által létrehozott riasztási szabályok *csak ugyanazzal az API-val kezelhetők* – valamint az Azure Portalon. Alapértelmezés szerint az Azure Monitor továbbra is a [korábbi Log Analytics-riasztási API-t](api-alerts.md) fogja használni az Azure Portalon lévő új riasztási szabályok létrehozásához a Log Analytics meglévő munkaterületeihez. [2019. június 1-jén vagy azt követően létrehozott új naplómunkaterület](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) alapértelmezés szerint automatikusan az új [scheduledQueryRules API-t](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használja, beleértve az Azure Portalon is.

A scheduledQueryRules API-ra való áttérés hatásai az alábbiakban kerülnek összeállításra:

- A naplóriasztások programozott felületeken keresztültörténő kezelésére szolgáló összes interakciót most [antól az scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatával kell elvégezni. További információ: [mintahasználat az Azure Resource Template on keresztül,](alerts-log.md#managing-log-alerts-using-azure-resource-template) és [mintahasználat a PowerShellen keresztül](alerts-log.md#managing-log-alerts-using-powershell)
- Az Azure Portalon létrehozott új naplóriasztási szabályok csak [a scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatával jönnek létre, és lehetővé teszik a felhasználók számára, hogy az [új API további funkcióit](#benefits-of-switching-to-new-azure-api) az Azure Portalon keresztül is használhassák.
- A naplóriasztási szabályok súlyossága a következőkről változik: *Kritikus, Figyelmeztetés & Információs*, *súlyossági értéke 0, 1 & 2.* A 3-as és 4-es súlyosságú riasztási szabályok létrehozása/frissítése mellett.

A riasztási szabályok [áthelyezése az örökölt Log Analytics riasztási API-ból](api-alerts.md) nem jár a riasztásdefiníció, a lekérdezés vagy a konfiguráció bármilyen módon történő módosításával. A riasztási szabályok és a figyelés nem változik, és a riasztások nem állnak le, vagy elakadnak a váltás alatt vagy után. Az egyetlen változás a következő:

- Az API-preferencia módosítása és a szabályokhoz való hozzáférés egy új API-n keresztül.
- Módosított riasztási szabály erőforrás-URI, amely az [örökölt Log Analytics riasztási API-ban](api-alerts.md) `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`használt azonosítókat tartalmazza a riasztási szabály neve helyett ebben a struktúrában. A figyelmeztetési szabály megjelenítendő neve változatlan marad.

Minden olyan ügyfél, aki önként szeretne váltani az új [scheduledQueryRules-ra,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) és le szeretné tiltani a használatát az [örökölt Log Analytics Alert API-ból;](api-alerts.md) ezt úgy teheti meg, hogy put-hívást hajt végre az alábbi API-n az adott Log Analytics-munkaterülethez társított összes riasztási szabály váltásához.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Az alábbi JSON-t tartalmazó kéréstörzsgel.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Az API powershell-parancssorból is elérhető az [ARMClient](https://github.com/projectkudu/ARMClient)nyílt forráskódú parancssori eszközzel, amely leegyszerűsíti az Azure Resource Manager API meghívását. Amint az alábbiakban látható, a minta PUT hívás armclient eszközzel váltani az adott Log Analytics-munkaterülethez társított összes riasztási szabályok.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Ha a Log Analytics munkaterületen az összes riasztási szabály váltása sikeres az új [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatára, a következő válasz jelenik meg.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

A felhasználók ellenőrizhetik a Log Analytics-munkaterület aktuális állapotát is, és ellenőrizhetik, hogy csak [az scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatára váltott-e át. Az ellenőrzéshez a felhasználók get hívást hajthatnak végre az alábbi API-n.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

A fenti végrehajtása a PowerShell parancssorból [ARMClient](https://github.com/projectkudu/ARMClient) eszközzel, lásd az alábbi mintát.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Ha a megadott Log Analytics-munkaterület csak [az scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatára lett átkapcsolva; akkor a JSON válasz az alábbiak szerint lesz elsorolva.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Ellenkező esetben, ha a megadott Log Analytic munkaterület még nem váltott a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) csak; akkor a JSON válasz az alábbiak szerint lesz elsorolva.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>További lépések

- További információ az [Azure-figyelő – naplóriasztások.](alerts-unified-log.md)
- Megtudhatja, hogyan hozhat létre [naplóriasztásokat az Azure Alerts szolgáltatásban.](alerts-log.md)
- További információ az [Azure Alerts szolgáltatásról.](../../azure-monitor/platform/alerts-overview.md)
