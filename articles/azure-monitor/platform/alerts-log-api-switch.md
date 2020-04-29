---
title: Váltás az új Azure riasztási API-ra
description: Az örökölt savedSearch-alapú Log Analytics riasztási API áttekintése és a riasztási szabályok új ScheduledQueryRules API-ra való váltásának folyamata, az általános felhasználói problémákkal foglalkozó részletekkel.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 7950b3f584c36b68a4eff66b05e83ba94c1ec1dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114368"
---
# <a name="switch-api-preference-for-log-alerts"></a>API-beállítások váltása naplózási riasztásokhoz

> [!NOTE]
> Csak az Azure nyilvános felhőre vonatkozó, a Azure Government- **vagy az Azure** China Cloud-beli felhasználók számára elérhető tartalom.  

> [!NOTE]
> Ha a felhasználó úgy dönt, hogy az új [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) -ra vált, nem lehet visszatérni a régebbi [örökölt log Analytics riasztási API](api-alerts.md)használatára.

A legutóbbi időpontig felügyeli a riasztási szabályokat a Microsoft Operations Management Suite-portálon. Az új riasztások a Microsoft Azure különböző szolgáltatásaival lettek integrálva, beleértve a Log Analytics, és a [riasztási szabályokat a OMS portálról az Azure-ra is kiterjesztjük](alerts-extend.md). Az ügyfelek minimális megszakadásának biztosítása érdekében azonban a folyamat nem változtatta meg az SavedSearch alapuló [log Analytics riasztási API](api-alerts.md) programozási felületét.

Most azonban bejelentheti, hogy Log Analytics riasztást ad a felhasználóknak a valódi Azure programozott alternatív, [Azure monitor-SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)-t, amely az Azure-számlázásban is tükröződik, [és naplózza a riasztásokat](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Ha többet szeretne megtudni a naplók API-val történő kezeléséről, tekintse meg a [riasztások kezelése az Azure Resource template használatával](alerts-log.md#managing-log-alerts-using-azure-resource-template) és a [naplózási riasztások kezelése a PowerShell használatával](alerts-log.md#managing-log-alerts-using-powershell)című témakört.

## <a name="benefits-of-switching-to-new-azure-api"></a>Az új Azure API-ra való áttérés előnyei

Számos előnnyel jár a riasztások létrehozása és kezelése a [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) -val az [örökölt log Analytics riasztási API](api-alerts.md)-val. a legfontosabbak alább láthatók:

- [Több munkaterület-napló keresésének](../log-query/cross-workspace-query.md) lehetősége a riasztási szabályokban, valamint a külső erőforrások (például log Analytics munkaterületek, vagy akár Application Insights alkalmazások) összevetése
- Ha több mező van használatban a lekérdezés csoportosításához, a [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) -felhasználó megadhatja, hogy melyik mező összesíthető Azure Portal
- A [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatával létrehozott naplózási riasztások hossza 48 óra lehet, és az adatlekérdezés hosszabb ideig is megadható.
- Riasztási szabályokat hozhat létre egyetlen, egyetlen erőforrásként, anélkül, hogy három erőforrást kellene létrehoznia az [örökölt log Analytics riasztási API](api-alerts.md) -val
- Egyetlen programozott felület az Azure-beli lekérdezés-alapú naplózási riasztások összes változatához – az új [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) -val a Log Analytics és Application Insights szabályai kezelhetők.
- A log-riasztások kezelése [PowerShell-parancsmagok](alerts-log.md#managing-log-alerts-using-powershell) használatával
- Minden új naplózási riasztási funkció és a jövőbeli fejlesztés csak az új [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) -n keresztül érhető el

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>A régi log-riasztások API-ból való váltás folyamata

A felhasználók az [örökölt log Analytics a riasztási API](api-alerts.md) -t vagy az új [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)-t használhatják. Az API-k által létrehozott riasztási szabályok *csak az API* -val kezelhetők, valamint a Azure Portal. Alapértelmezés szerint a Azure Monitor továbbra is a [régi log Analytics riasztási API](api-alerts.md) -t használja az új riasztási szabály létrehozásához a Azure Portal a meglévő munkaterületek log Analytics. Az [2019-es június 1-én vagy azt követően létrehozott új napló-munkaterület bejelentése](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) alapértelmezés szerint automatikusan az új [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) -t fogja használni, beleértve a Azure Portal.

A preferencia scheduledQueryRules API-ra gyakorolt hatásait az alábbiakban fordítjuk le:

- A naplózási riasztások programozási felületen keresztül történő kezeléséhez elvégzett összes műveletet most [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatával kell elvégezni. További információkért lásd: [minta használata az Azure Resource template](alerts-log.md#managing-log-alerts-using-azure-resource-template) használatával és a [minta használata a PowerShell segítségével](alerts-log.md#managing-log-alerts-using-powershell)
- Az Azure Portal-ben létrehozott új naplózási riasztási szabály csak a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatával jön létre, és lehetővé teszi, hogy a felhasználók az [új API további funkcióit](#benefits-of-switching-to-new-azure-api) használják Azure Portaleken keresztül is.
- A naplózási riasztási szabályok súlyossága: *kritikus, figyelmeztetés & tájékoztatás*, *0, 1 & 2 súlyossági értékekre*. Továbbá a riasztási szabályok létrehozásához/frissítéséhez a 3. és a 4. súlyossági szintű beállítást is.

A riasztási szabályok a [régi log Analytics riasztási API](api-alerts.md) -ból való áthelyezésének folyamata nem jár semmilyen módon a riasztás definíciójának, lekérdezésének vagy konfigurációjának módosításával. A riasztási szabályok és a figyelés nem érintett, és a riasztások nem állnak le, illetve nem állnak le a kapcsoló alatt vagy azt követően. Az egyetlen változás a következő:

- Az API-beállítások módosítása és a szabályokhoz való hozzáférés új API-n keresztül.
- Egy módosított riasztási szabály erőforrás-URI-ja, amely az [örökölt log Analytics riasztási API](api-alerts.md) -ban használt azonosítókat tartalmazza `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`a riasztási szabály neve helyett ebben a struktúrában. A riasztási szabály megjelenítendő neve változatlan marad.

Minden olyan ügyfél, aki önként kíván váltani az új [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) , és letiltja az [örökölt log Analytics riasztási API](api-alerts.md)használatát. Ezt úgy teheti meg, hogy egy PUT hívást hajt végre az alábbi API-ban az adott Log Analytics munkaterülethez társított összes riasztási szabály átváltásához.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Az alábbi JSON-t tartalmazó kérelem törzse.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Az API egy PowerShell-parancssorból is elérhető a [ARMClient](https://github.com/projectkudu/ARMClient)használatával, amely leegyszerűsíti a Azure Resource Manager API meghívását. Ahogy az alábbi ábrán is látható, a minta PUT Call (ARMclient) eszköz használatával az adott Log Analytics munkaterülethez társított összes riasztási szabályt átválthatja.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Ha a Log Analytics munkaterületen az új [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatára vonatkozó riasztási szabályok váltása sikeres, a következő válasz lesz megadva.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

A felhasználók a Log Analytics munkaterület aktuális állapotát is betekinthetik, és megtekinthetik, hogy a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) csak a használatára van-e kapcsolva. Annak ellenőrzéséhez, hogy a felhasználók elvégezhetnek egy GET hívást az alábbi API-ban.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Ha a fentieket a PowerShell parancssor használatával szeretné végrehajtani a [ARMClient](https://github.com/projectkudu/ARMClient) eszközzel, tekintse meg az alábbi mintát.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Ha a megadott Log Analytics munkaterület csak a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatára lett átváltva; Ezután a válasz JSON lesz az alábbi listában.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Máskülönben ha a megadott log analitikai munkaterület még nem lett átváltva a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatára; Ezután a válasz JSON lesz az alábbi listában.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>További lépések

- További információ a [Azure monitor-log riasztásokról](alerts-unified-log.md).
- Megtudhatja, hogyan hozhat létre [naplózási riasztásokat az Azure-riasztásokban](alerts-log.md).
- További információ az [Azure-riasztások felhasználói élményéről](../../azure-monitor/platform/alerts-overview.md).
