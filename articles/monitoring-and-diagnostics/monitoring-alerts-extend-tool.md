---
title: Kiterjeszti az OMS Szolgáltatáshoz riasztások Azure kezdeményezése |} Microsoft Docs
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
ms.date: 03/16/2018
ms.author: vinagara
ms.openlocfilehash: 5e8a66214efc27e8e59da354e54d68bb48d274cc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="initiate-extending-alerts-from-oms-into-azure"></a>Az OMS kibővítése riasztásait az Azure kezdeményezése
Verziótól **2018. április 23.**, a konfigurált riasztások minden felhasználója [a Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), az Azure kiterjesztése. Riasztásokat, amelyek az Azure-bA bővítve lettek OMS a megszokott módon működik. Megfigyelési lehetőségek változatlanok maradnak. Az Azure-bA OMS létrehozott riasztások kiterjesztése számos előnyt kínál. Az előnyei és a folyamat, amely a riasztásokat az OMS Szolgáltatáshoz Azure kapcsolatos további információkért lásd: [terjessze ki a riasztások az OMS Szolgáltatáshoz Azure](monitoring-alerts-extend.md).

Az ügyfelek szeretne áthelyezi a riasztásokat az OMS Szolgáltatáshoz Azure azonnal, ehhez a megadott lehetőségek egyikének használatával.

## <a name="option-1---using-oms-portal"></a>1 - beállítás használatával OMS-portálon
A kiterjeszti a riasztások az OMS Szolgáltatáshoz Azure önkéntesen elindításához kövesse az alábbi lépéseket.

1. Az Operations Management Suite (OMS) – áttekintés oldalra lépjen a beállításokat, majd az értesítések szakasz. Kattintson a "Kiterjesztése az Azure", címkézve az alábbi ábrán a kijelölt gombra.

    ![Bővítése kapcsolóval OMS riasztási beállítások lap](./media/monitor-alerts-extend/ExtendInto.png)

2. A gombra történő kattintás után a 3. lépés varázsló jelenik meg, feltárja a folyamat első lépését. Kattintson a Tovább gombra, a folytatáshoz.

    ![Terjessze ki a riasztások a OMS az Azure - 1. lépés](./media/monitor-alerts-extend/ExtendStep1.png)

3. A második lépésben, a rendszer szerint jelennek meg az ajánlott változás összegzését megfelelő listaelem [művelet csoportok](monitoring-action-groups.md), a riasztások az OMS Szolgáltatáshoz. Ha hasonló műveletek közötti egynél több riasztás – a rendszer társítja az összes csoport egyetlen műveleti javasol.  A javasolt művelet csoport hajtsa végre az elnevezési: *WorkspaceName_AG_ #Number*. A folytatáshoz, kattintson a Tovább gombra.
Az alábbi minta képernyő.

    ![Terjessze ki a riasztások a OMS az Azure - 2. lépés](./media/monitor-alerts-extend/ExtendStep2.png)


4. A varázsló utolsó lépése megkérheti a OMS ütemezése, a riasztások kiterjeszti az Azure - művelet új csoportok létrehozása és társítása őket riasztásokat, a korábbi képernyőn látható módon. A folytatáshoz válasszon kattintson a Befejezés gombra, majd erősítse meg a folyamat kezdeményezéséhez a parancssorba. Szükség esetén az ügyfelek e-mail címet, amelyre szeretné elküldeni a jelentés a feldolgozás befejezése a OMS is megadhatja.

    ![Terjessze ki a riasztások a OMS az Azure - 3. lépés](./media/monitor-alerts-extend/ExtendStep3.png)

5. A varázsló befejezése után vezérlő visszatér a riasztási beállítások lapra, és "Kiterjesztése az Azure" beállítás törlődni fog. A háttérben OMS beütemezett riasztások az OMS ki kell terjeszteni az Azure; Ez eltarthat egy ideig, és egy rövid időszak riasztások az OMS a művelet kezdetekor csak akkor módosítható. Aktuális állapot jelenik meg szalagcím keresztül, és ha e-mail-címet where megadott során 4. lépés, majd fogja küldjenek, amikor háttérfolyamatként sikeresen kibővíti az összes riasztás az Azure. 

6. Riasztások OMS-ben, szereplő azok beolvasása sikeresen kiterjeszthetőek Azure után is folytatódik.

    ![Az Azure-ba történő bővítése a riasztások az OMS után](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>A beállítás 2 - API használatával
Felhasználók, akik programozott módon szabályozhatja, vagy automatizálhatja, a folyamat, amely a riasztások az OMS az Azure; A Microsoft közzétett új AlertsVersion API Naplóelemzési alatt.

A napló Analytics AlertsVersion API RESTful, és az Azure Resource Manager REST API-n keresztül érhető el. Ebben a dokumentumban található példák az elérését az API-t a PowerShell parancssori használatával [ARMClient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti az Azure Resource Manager API meghívása. A ARMClient és a PowerShell használata közül sok az API eléréséhez. Az API-t kimeneti fog eredmények JSON formátumban, lehetővé téve az eredmények használati programozott módon számos különböző módja.

GET API használatával egy úgy szerezheti be az eredmény az ajánlott változás összefoglalása megfelelő listájaként [művelet csoportok](monitoring-action-groups.md) OMS a riasztásokhoz a JSON formátumban. Ha hasonló műveletek közötti egynél több riasztás láthatók - létrehozásához javasolni fogja a rendszer társítja az összes egyetlen művelettel csoport.  A javasolt művelet csoport hajtsa végre az elnevezési: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> ELSŐ riasztások az OMS első kiterjeszthetőek az Azure az API hívása nem lesz. Csak biztosítja a válaszként az összegzés javasolt módosításait. Erősítse meg, ezek a változások riasztások kiterjeszti Azure végezhető, POST hívja meg a kell annak érdekében, az API-t.

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

Kezdeményezheti az ütemezést, amely a riasztásoknak az OMS az Azure-ba, az API-t mutató POST kezdeményeznek. Ennek a hívás/parancs megerősíti, hogy a felhasználó leképezés, valamint elfogadási figyelmeztetések az OMS Azure terjeszteni, majd hajtsa végre a módosításokat, a válasz GET hívás az API-hoz. Szükség esetén a felhasználó biztosíthatnak e-mail címét, amelyhez OMS lesz az e-mail egy jelentés, ha ütemezett háttérfolyamatként, amely a riasztásoknak az OMS Azure futtatása sikeresen befejeződött.

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Az összegzés által GET - bármilyen változás történik, a rendszer fiókjának a megadott eredményt, amely OMS riasztást küld az Azure, változhat. Ütemezett, riasztások az OMS állnak átmenetileg nem érhető el, szerkesztése vagy módosítása - az új riasztásokat is létrehozása közben. 

Ha a FELADÁS egy vagy több sikeres, valamint 200 OK választ térhet vissza:
```json
{
    "version": 2
}
```
Jelzi, hogy a riasztások fut az Azure, 2-es verzióját jelöli. Ezen verziója csak a ellenőrizni, hogy a riasztásokat az Azure fut, és nincs hatással a felhasználást a [napló Analytics Search API](../log-analytics/log-analytics-api-alerts.md). A riasztások kiterjeszthetőek az Azure sikeresen, miután az összes e-mail során megadott címek GET küld egy jelentés ezzel a végrehajtott változtatások részleteit.


És végül, ha a megadott munkaterület minden riasztás már ütemezett ki kell terjeszteni az Azure - POST válasz 403 Tiltott.


## <a name="next-steps"></a>További lépések

* További információ az új [Azure riasztások élmény](monitoring-overview-unified-alerts.md).
* További tudnivalók [riasztások jelentkezzen be Azure riasztások](monitor-alerts-unified-log.md).
