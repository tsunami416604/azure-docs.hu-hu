---
title: (Másolás) kiterjesztése riasztások az OMS-portálon az Azure |} Microsoft Docs
description: Eszközök, amely kiterjeszti az OMS Szolgáltatáshoz riasztások Azure riasztások végezhető el az ügyfelek önkéntesen API.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: vinagara
ms.openlocfilehash: 241ac027a0606f901f51d6a20b9a48a2cf7a9fcf
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-extend-copy-alerts-from-oms-into-azure"></a>Az OMS Szolgáltatáshoz, az Azure bővítése (Másolás) riasztások
Verziótól **2018. május 14.**, a konfigurált riasztások minden felhasználója [a Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), az Azure kiterjesztése. Riasztásokat, amelyek az Azure-bA bővítve lettek OMS a megszokott módon működik. Megfigyelési lehetőségek változatlanok maradnak. Az Azure-bA OMS létrehozott riasztások kiterjesztése számos előnyt kínál. Az előnyei és a folyamat, amely a riasztásokat az OMS Szolgáltatáshoz Azure kapcsolatos további információkért lásd: [terjessze ki a riasztások az OMS Szolgáltatáshoz Azure](monitoring-alerts-extend.md).

> [!NOTE]
> Május 14 2018 - megkezdése a Microsoft megkezdődik a folyamat automatikusan kiterjeszti a riasztások az Azure-bA. Nem minden munkaterületekkel és riasztások kibővíti a mai napon; helyette a Microsoft megkezdik terjesztheti ki az értesítések automatikusan részletben jövőbeli hét. Ezért a riasztásokat az OMS-portálon fog automatikus-terjed ki az Azure azonnal a 2018. május 14., és felhasználó továbbra is futtathatja manuálisan kiterjesztheti a riasztásokról beállítások részleteit az alábbiakban olvashatja.

Az ügyfelek szeretne áthelyezi a riasztásokat az OMS Szolgáltatáshoz Azure azonnal, ehhez a megadott lehetőségek egyikének használatával.

## <a name="option-1---using-oms-portal"></a>1 - beállítás használatával OMS-portálon
A kiterjedő riasztások OMS-portálon az Azure önkéntesen elindításához kövesse az alábbi lépéseket.

1. Az OMS portál – áttekintés oldalra keresse meg beállításokat, majd az értesítések szakasz. Kattintson a "Kiterjesztése az Azure", címkézve az alábbi ábrán a kijelölt gombra.

    ![A riasztási beállítások portállapon OMS bővítése beállítással](./media/monitor-alerts-extend/ExtendInto.png)

2. A gombra történő kattintás után a 3. lépés varázsló jelenik meg, feltárja a folyamat első lépését. Kattintson a Tovább gombra, a folytatáshoz.

    ![Terjessze ki a riasztások a OMS-portálon az Azure - 1. lépés](./media/monitor-alerts-extend/ExtendStep1.png)

3. A második lépésben, a rendszer szerint jelennek meg az ajánlott változás összegzését megfelelő listaelem [művelet csoportok](monitoring-action-groups.md), a riasztások az OMS-portálon. Ha hasonló műveletek közötti egynél több riasztás – a rendszer társítja az összes csoport egyetlen műveleti javasol.  A javasolt művelet csoport hajtsa végre az elnevezési: *WorkspaceName_AG_ #Number*. A folytatáshoz, kattintson a Tovább gombra.
Az alábbi minta képernyő.

    ![Terjessze ki a riasztások a OMS-portálon az Azure - 2. lépés](./media/monitor-alerts-extend/ExtendStep2.png)


4. A varázsló utolsó lépése kérje meg az OMS-portálon ütemezése, a riasztások kiterjeszti az Azure - művelet új csoportok létrehozása és társítása őket riasztásokat, a korábbi képernyőn látható módon. A folytatáshoz válasszon kattintson a Befejezés gombra, majd erősítse meg a folyamat kezdeményezéséhez a parancssorba. Szükség esetén az ügyfelek e-mail címet, amelyre szeretné elküldeni a jelentés a feldolgozás befejezése a OMS-portálon is megadhatja.

    ![Terjessze ki a riasztások a OMS-portálon az Azure - 3. lépés](./media/monitor-alerts-extend/ExtendStep3.png)

5. A varázsló befejezése után vezérlő visszatér a riasztási beállítások lapra, és "Kiterjesztése az Azure" beállítás törlődni fog. A háttérben OMS-portálon riasztásokat Naplóelemzési ki kell terjeszteni az Azure; a művelet ütemezi Ez eltarthat egy ideig, és egy rövid időszak riasztások az OMS-portálon a művelet kezdetekor csak akkor módosítható. Aktuális állapot jelenik meg szalagcím keresztül, és ha e-mail-címet where megadott során 4. lépés, majd fogja küldjenek, amikor háttérfolyamatként sikeresen kibővíti az összes riasztás az Azure. 

6. Riasztások listázásának az OMS-portálon, ezek beolvasása sikeresen kiterjeszthetőek az Azure után is folytatódik.

    ![Riasztások az OMS-portálon az Azure-bA kiterjesztése után](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>A beállítás 2 - API használatával
Az ügyfelek számára, akik programozott módon vagy automatizálhatja, a folyamat, amely a riasztásokat az OMS-portálon az Azure; A Microsoft közzétett új AlertsVersion API Naplóelemzési alatt.

A napló Analytics AlertsVersion API RESTful, és az Azure Resource Manager REST API-n keresztül érhető el. Ebben a dokumentumban található példák az elérését az API-t a PowerShell parancssori használatával [ARMClient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti az Azure Resource Manager API meghívása. A ARMClient és a PowerShell használata közül sok az API eléréséhez. Az API-t kimeneti fog eredmények JSON formátumban, lehetővé téve az eredmények használati programozott módon számos különböző módja.

GET API használatával egy úgy szerezheti be az eredmény az ajánlott változás összefoglalása megfelelő listájaként [művelet csoportok](monitoring-action-groups.md) a riasztások az OMS-portálon, a JSON formátumban. Ha hasonló műveletek közötti egynél több riasztás láthatók - létrehozásához javasolni fogja a rendszer társítja az összes egyetlen művelettel csoport.  A javasolt művelet csoport hajtsa végre az elnevezési: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> ELSŐ hívás az API nem eredményez az első Azure kiterjeszthetőek OMS-portálon riasztásokat. Csak biztosítja a válaszként az összegzés javasolt módosításait. Erősítse meg, ezek a változások riasztások kiterjeszti Azure végezhető, POST hívja meg a kell annak érdekében, az API-t.

Ha API-hívás GET sikeres, 200 OK válasz együtt, a riasztás javasolt művelet csoportok együtt JSON listáját biztosítaná. Az alábbi mintaválasz:

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
                            "serviceUri": "http://test.com"
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
Abban az esetben, a megadott munkaterület egyetlen riasztás sem szerepelnek, a GET művelethez 200 OK válasz együtt a JSON a következő lesz:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Ha a megadott munkaterület minden riasztás már fut az Azure - GET hívást válasz a következő lesz:
```json
{
    "version": 2
}
```

Kezdeményezheti az ütemezést, amely a riasztásokat az OMS-portálon az Azure-ba, az API-t mutató POST kezdeményeznek. Ennek a hívás/parancs megerősíti, hogy a felhasználó leképezés, valamint elfogadási figyelmeztetések az OMS-portálon az Azure-bA kiterjesztett, majd hajtsa végre a módosításokat, a válasz GET hívás az API-hoz. Szükség esetén a felhasználó mely OMS portal fog mail egy jelentés ütemezett háttérfolyamatként, amely a riasztásokat az OMS-portálon az Azure-bA sikeres befejezése után e-mail címek listájának biztosíthat.

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> OMS kiterjesztése eredményét átadott által GET - bármilyen változás történik, a rendszer a fiókja a portálon az Azure, riasztást küld változhat. Ütemezett, az OMS-portálon riasztásokat állnak átmenetileg nem érhető el, szerkesztése vagy módosítása – az során hozható létre új riasztásokat. 

Ha a FELADÁS egy vagy több sikeres, valamint 200 OK választ térhet vissza:
```json
{
    "version": 2
}
```
Jelzi, hogy a riasztások fut az Azure, 2-es verzióját jelöli. Ezen verziója csak a ellenőrizni, hogy a riasztásokat az Azure fut, és nincs hatással a felhasználást a [napló Analytics Search API](../log-analytics/log-analytics-api-alerts.md). A riasztások kiterjeszthetőek az Azure sikeresen, miután az összes e-mail során megadott címek GET küld egy jelentés ezzel a végrehajtott változtatások részleteit.

És végül, ha a megadott munkaterület minden riasztás már ütemezve kiterjeszthetőek az Azure - POST válasz 403 Tiltott. Hibaüzenetek megtekintéséhez, vagy hogy kiterjesztése folyamat Beragadt, a felhasználói műveleteket végezhetnek GET hívást és hiba üzenetet, ha bármelyik visszaadott összegzés együtt.

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
                            "serviceUri": "http://test.com"
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
A során, amely a riasztásokat az OMS Szolgáltatáshoz az Azure lehet alkalmi hibát, amely megakadályozza, hogy a rendszer létrehozásához szükséges [művelet csoportok](monitoring-action-groups.md). Ilyen esetekben egy hibaüzenet jelenik meg az OMS-portálon keresztül szalagcím figyelmeztetési és a GET hívást API történik.

Az alábbiakban a javítási lépéseket minden egyes hibához:
1. **Hiba: Az előfizetés nincs regisztrálva a következő névtérben: "microsoft.insights" használandó**: ![OMS portál riasztási beállítások lapján regisztrációs hibaüzenet](./media/monitor-alerts-extend/ErrorMissingRegistration.png)

    a. Az OMS-munkaterület - társított előfizetés nincs regisztrálva Azure Monitor (microsoft.insights) funkciójával; miatt mely OMS kiterjeszti, riasztások Azure figyelő & értesítések nem.
    
    b. Regisztrálja microsoft.insights (Azure figyelő & riasztások) használata az előfizetésében Powershell, az Azure parancssori felület vagy Azure-portál használatával. További tudnivalókért tekintse meg a cikk a [erőforrás-szolgáltató regisztrálása a hibák megoldása](../azure-resource-manager/resource-manager-register-provider-errors.md)
    
    c. Miután feloldotta a cikkben ismertetett lépések szerint, OMS kiterjed a riasztásokat az Azure belül a következő napra ütemezett futtatását; nincs szükség semmilyen művelet vagy kezdeményezés.
2. **Hiba: Hatókör zárolási szerepel előfizetés-erőforráscsoport szintjén az írási műveletek**: ![OMS portál riasztási beállítások lapján ScopeLock hibaüzenet](./media/monitor-alerts-extend/ErrorScopeLock.png)

    a. Ha hatókör zárolása engedélyezett, előfizetéshez vagy erőforráscsoporthoz Naplóelemzés (OMS) munkaterületet; tartalmazó új változást korlátozása a rendszer nem tud (Másolás) riasztások kiterjeszti az Azure és a szükséges intézkedéseket csoportok létrehozása.
    
    b. Megoldásához törölje a *ReadOnly* az előfizetés vagy az erőforrás-részleg a munkaterületet tartalmazó; az Azure-portálon, Powershell, az Azure parancssori felület vagy API zárolását. További tudnivalókért tekintse meg a cikk a [erőforrás zárolási kihasználtsága](../azure-resource-manager/resource-group-lock-resources.md). 
    
    c. Miután feloldotta a cikkben ismertetett lépések szerint, OMS kiterjed a riasztásokat az Azure belül a következő napra ütemezett futtatását; nincs szükség semmilyen művelet vagy kezdeményezés.

3. **Hiba: A előfizetés-erőforráscsoport szintjén szerepel házirend**: ![OMS portál riasztási beállítások lapján házirend hibaüzenet](./media/monitor-alerts-extend/ErrorPolicy.png)

    a. Ha [Azure házirend](../azure-policy/azure-policy-introduction.md) van érvényben, bármely új erőforrás előfizetéshez vagy erőforráscsoporthoz Naplóelemzés (OMS) munkaterületet; tartalmazó korlátozása a rendszer nem tudja (Másolás) riasztások kiterjeszti az Azure és a szükséges intézkedéseket csoportok létrehozása.
    
    b. Megoldásához módosítsa a házirendet, amely *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* hiba, amely megakadályozza az új erőforrások az előfizetés vagy az erőforrás a munkaterületet tartalmazó csoport létrehozását. Az Azure-portált használja, Powershell, az Azure parancssori felület vagy API; Hiba, amely a megfelelő házirend kereséséhez műveletek naplózhatók. További tudnivalókért tekintse meg a cikk a [műveletek naplózása tevékenység naplók megtekintése](../azure-resource-manager/resource-group-audit.md). 
    
    c. Miután feloldotta a cikkben ismertetett lépések szerint, OMS kiterjed a riasztásokat az Azure belül a következő napra ütemezett futtatását; nincs szükség semmilyen művelet vagy kezdeményezés.


## <a name="next-steps"></a>További lépések

* További információ az új [Azure riasztások élmény](monitoring-overview-unified-alerts.md).
* További tudnivalók [riasztások jelentkezzen be Azure riasztások](monitor-alerts-unified-log.md).
