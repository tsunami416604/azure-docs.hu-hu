---
title: Riasztások kiterjesztése a Log Analytics az Azure Government Cloud
description: Ez a cikk ismerteti az eszközök és az API, amellyel bővítheti riasztások a Log Analytics az Azure Alerts szolgáltatáshoz.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 9d734f74c4e12b369e46c15dcb9d01a8185dddd6
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103377"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Riasztások kiterjesztése a Log Analytics az Azure-riasztások
A riasztások szolgáltatása az OMS-portálon váltja fel az Azure-riasztások Azure Government felhőben. Az átállás részeként a riasztásokat, amelyek eredetileg konfigurálták a Log Analytics kiterjesztik az Azure-bA. Ha nem szeretné automatikusan Azure-ba való áthelyezésének várja, is kezdeményezhet a folyamat:

- Az Operations Management Suite Portalról manuálisan. 
- Programozott módon az API-val AlertsVersion.  

> [!NOTE]
> Microsoft automatikusan kiterjeszti az Azure Government OMS portál példányok az Azure Alerts szolgáltatáshoz, a Log Analytics létrehozott riasztások 2019. március 1., a rendszeres módon indítása. Ha problémába ütközik létrehozása [Műveletcsoportok](../../azure-monitor/platform/action-groups.md), használjon [javítási lépések](alerts-extend-tool.md#troubleshooting) beolvasni a Műveletcsoportok jönnek létre automatikusan. 2019. március 15-ig ezeket a lépéseket használhatja az Azure Government OMS-portálon.

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Option 1: Az Operations Management Suite portálján kezdeményez
Az alábbi lépéseket a munkaterületre vonatkozó riasztások kiterjesztése az Operations Management Suite portálján az Azure Government-felhőbeli a ismertetik.  

1. Az Azure Portalon válassza a **Minden szolgáltatás** elemet. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. A Log Analytics-előfizetések ablaktábláján válasszon ki egy munkaterületet, és válassza a **OMS-portálon** csempére.
![Képernyőkép a Log Analytics előfizetés ablaktáblán kiemelt OMS-portálon csempével](media/alerts-extend-tool/azure-portal-01.png) 
3. Után a rendszer átirányítja az Operations Management Suite portálra, válassza ki a **beállítások** ikonra.
![Képernyőkép az Operations Management Suite portálján, a beállítások ikonja kiemelve](media/alerts-extend-tool/oms-portal-settings-option.png) 
4. Az a **beállítások** lapon jelölje be **riasztások**.  
5. Válassza ki **kiterjesztése az Azure-bA**.
![Képernyőkép az Operations Management Suite portál riasztási beállítások oldala, amelyen a kiemelt Azure-ba való bővítése](media/alerts-extend-tool/ExtendInto.png)
6. Megjelenik egy három lépéses varázsló, a **riasztások** ablaktáblán. Olvassa el az áttekintést, és válassza ki **tovább**.
![1. lépés a varázsló képernyőképe](media/alerts-extend-tool/ExtendStep1.png)  
7. A második lépésben látja összegzését, javasolt változtatás ajánlati megfelelő [Műveletcsoportok](../../azure-monitor/platform/action-groups.md) a riasztások. Hasonló műveletek közötti egynél több riasztás is látható, ha a varázsló azokat egyetlen művelettel csoport hozzárendelése javasolja.  Az elnevezési szabályt követik a következőképpen történik: *WorkspaceName_AG_#Number*. A továbblépéshez válasszon **tovább**.
![2. lépés a varázsló képernyőképe](media/alerts-extend-tool/ExtendStep2.png)  
8. A varázsló utolsó lépése, jelölje ki **Befejezés**, és győződjön meg arról, amikor a rendszer kéri a folyamat elindításához. Igény szerint megadhat egy e-mail-címet, így értesítést kap a folyamat befejeződik, és az összes riasztás sikeresen helyezte az Azure Alerts szolgáltatáshoz.
![3. lépés a varázsló képernyőképe](media/alerts-extend-tool/ExtendStep3.png)

Ha a varázsló nem végzett, a **riasztási beállítások** lap, riasztások kiterjesztése az Azure-bA lehetősége el lett távolítva. A háttérben a riasztásokat az Azure-ba kerülnek, és ez eltarthat egy ideig. A művelet során nem végezhet módosításokat a riasztásokat az Operations Management Suite-portálon. A portál tetején a szalagcímen az aktuális állapota látható. Ha korábban megadott e-mail-címmel, egy e-mailt kap, amikor a folyamat sikeresen befejeződött.  


Riasztások továbbra is megjelennek az Operations Management Suite-portálon, az Azure-bA sikeresen áthelyezett után is.
![Képernyőkép az Operations Management Suite portál riasztási beállítások lap](media/alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Option 2: A AlertsVersion API használata
A Log Analytics AlertsVersion API segítségével riasztások kiterjesztése a Log Analytics az Azure Alerts minden ügyfélről, amely segítségével meghívhatja a REST API-t. Hozzáférhet az API-t a PowerShell használatával [ARMClient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszközt. Az eredményeket JSON-fájlban.  

Az API használatához, először hozzon létre egy GET kéréssel. Kiértékeli, és ez a javasolt változtatások összegző eredménnyel tér vissza, mielőtt ténylegesen kiterjesztése az Azure-bA egy POST-kérés használatával. Az eredmények sorolják fel a riasztásokat és a egy javasolt listáját [Műveletcsoportok](../../azure-monitor/platform/action-groups.md), JSON formátumban. Hasonló műveletek közötti egynél több riasztás is látható, ha a szolgáltatás javaslatot tesz egy egyetlen művelettel csoporthoz társítandó azokat. Az elnevezési szabályt követik a következőképpen történik: *WorkspaceName_AG_#Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Ha a GET-kérés sikeres, 200-as HTTP-állapotkód ad vissza, és a riasztások listája, és javasolt műveletcsoportok a JSON-adatokat. A következő egy példa választ:

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
Ha a megadott munkaterület nem rendelkezik a riasztási szabályok meghatározva, a JSON-adatokat a következő adja vissza:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Ha a megadott munkaterület összes riasztási szabályt már ki van bővítve az Azure-ba, a GET kérelemre adott válasz a következő:

```json
{
    "version": 2
}
```

A riasztások az Azure-ba való migrálás elindításához egy POST-választ kezdeményezni. A bejegyzés választ megerősíti, hogy a leképezés, valamint elfogadó, szeretné, hogy a riasztások a Log Analytics az Azure Alerts kiterjesztett. A tevékenységek ütemezve van, és a riasztások feldolgozása történik jelöli, ha a GET-válasz korábban végrehajtott eredményei alapján. Szükség esetén is adja meg, amelyhez a Log Analytics jelentést küld, amikor ütemezett háttér folyamata az áttelepítéshez a riasztás sikeresen lefutott e-mail-címek listáját. Ez a példa kérelem is használhatja:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Eredményét áttelepítése riasztások az Azure Alerts GET válaszban által átadott alapján változhat. Ha ütemezve, riasztások a Log Analyticsben átmenetileg nem érhetők el a módosítás az Operations Management Suite portálján. Új riasztásokat is létrehozhat. 

Ha a POST-kérés sikeres, egy HTTP 200 OK állapota mellett a következő választ adja vissza:

```json
{
    "version": 2
}
```

Ez a válasz azt jelzi, hogy a riasztásokat az Azure Alerts sikeresen bővültek. A verzió tulajdonság értéke csak a rendszer ellenőrzi, hogy a riasztások az Azure-ban fut, és nem állnak kapcsolatban a [Log Analytics Search API](../../azure-monitor/platform/api-alerts.md). A riasztások ki vannak bővítve az Azure-bA sikeresen, ha bármely e-mail-címek biztosított a POST-kérelmet küldött jelentést. A megadott munkaterület minden riasztás már ütemezett bővíthetők, a POST-kérés adott válasz-e, hogy a kísérlet történt tiltott (a 403-as állapotkód). Hibaüzenetek megtekintéséhez, vagy ha elakadt a folyamat ismertetése, küldhet egy GET kéréssel. Ha hibaüzenet jelenik meg, ezt adja vissza, az összefoglaló információkkal együtt.

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```

## <a name="troubleshooting"></a>Hibaelhárítás 
Riasztások kiterjesztése az a folyamat során problémák megakadályozhatja a rendszer az ehhez szükséges [Műveletcsoportok](../../azure-monitor/platform/action-groups.md). Ezekben az esetekben a fejléc a hibaüzenetet látja a **riasztási** szakasz az Operations Management Suite portálján, vagy a GET hívja az API-nak kész.

> [!IMPORTANT]
> Ha az Azure Government felhőalapú OMS portálon a felhasználóknak nem kell a következő javítási lépéseket 2019. március 15. előtt, a riasztások az Azure-ban futnak, de nem indulnak el, bármilyen művelet vagy az értesítés. Értesítés küldése riasztások, kell manuálisan szerkessze a figyelmeztetési szabályok az Azure-ban, és adja hozzá [Műveletcsoportok](../../azure-monitor/platform/action-groups.md)

Minden egyes hibához szervizelés lépései a következők:
- **Hiba: Hatókör zár megtalálható előfizetés/erőforráscsoport szintjén az írási műveletek**:   ![Az Operations Management Suite portál riasztási beállítások oldalán kiemelt hatókör zárolási hibaüzenettel képernyőképe](media/alerts-extend-tool/ErrorScopeLock.png)

    Hatókör zár engedélyezve van, amikor a szolgáltatás korlátozza (Operations Management Suite) Log Analytics-munkaterületnek tartalmazó előfizetés vagy az erőforrás-csoport minden olyan új változásáról. A rendszer nem tudja riasztások kiterjesztése az Azure-ba, és a szükséges Műveletcsoportok létrehozása.
    
    Megoldásához, törölje a *ReadOnly* a a munkaterületet tartalmazó előfizetés vagy az erőforrás csoport zárolása. Ehhez az Azure portal, PowerShell, Azure CLI-vel vagy az API használatával. További tudnivalókért lásd: [erőforrás zárolása használati](../../azure-resource-manager/resource-group-lock-resources.md). 
    
    A cikkben bemutatott lépések segítségével hárítsa el a hibát, ha az Operations Management Suite az Azure-bA a riasztások kiterjeszti a következő napra ütemezett futtatását. Nem kell további műveleteket, vagy kezdeményezzen semmit.

- **Hiba: A házirend megtalálható előfizetés/erőforráscsoport szintjén**:   ![Képernyőkép az Operations Management Suite portál riasztási beállítások oldal, kiemelve a házirend hibaüzenet](media/alerts-extend-tool/ErrorPolicy.png)

    Amikor [Azure Policy](../../governance/policy/overview.md) van érvényben, korlátozza a Log Analytics (az Operations Management Suite) munkaterületet tartalmazó előfizetés vagy az erőforrás csoportban bármilyen új erőforrást. A rendszer nem tudja riasztások kiterjesztése az Azure-ba, és a szükséges Műveletcsoportok létrehozása.
    
    Megoldásához módosítsa a házirendet, amely miatt a *[RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* hiba, amely megakadályozza az új erőforrások a vagy erőforráscsoportonként csoport, amely tartalmazza a munkaterület létrehozását. Ehhez az Azure portal, PowerShell, Azure CLI-vel vagy az API használatával. Keresse meg a megfelelő szabályzatot hibát okozó műveletek naplózhatók. További tudnivalókért lásd: [végzett műveletek naplózásához tevékenységi naplóinak megtekintése](../../azure-resource-manager/resource-group-audit.md). 
    
    A cikkben bemutatott lépések segítségével hárítsa el a hibát, ha az Operations Management Suite az Azure-bA a riasztások kiterjeszti a következő napra ütemezett futtatását. Nem kell további műveleteket, vagy kezdeményezzen semmit.


## <a name="next-steps"></a>További lépések

* További információ az új [Azure Alerts élmény](../../azure-monitor/platform/alerts-overview.md).
* Ismerje meg [naplóriasztások az Azure Alerts](alerts-unified-log.md).

