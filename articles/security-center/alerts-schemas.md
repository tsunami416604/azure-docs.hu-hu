---
title: Sémák az Azure Security Center riasztásaihoz
description: Ez a cikk ismerteti a különböző sémák által használt Azure Security Center biztonsági riasztások.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 19ca17f66f6818ed4c3ef532e2030cc03f0e73ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062953"
---
# <a name="security-alerts-schemas"></a>Biztonsági riasztások sémák

Az Azure Security Center szabványos rétegének felhasználói biztonsági riasztásokat kapnak, ha a Security Center fenyegetést észlel az erőforrásaikszámára.

Ezeket a biztonsági riasztásokat az Azure Security Center **fenyegetésvédelmi** lapjain vagy külső eszközökön, például a következőkön tekintheti meg:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) – a Microsoft natív SIEM-je. A Sentinel-összekötő értesítéseket kap az Azure Security Centertől, és elküldi azokat az Azure Sentinel [Log Analytics-munkaterületére.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Külső gyártók által im-ek – A Security Center [folyamatos exportálási](continuous-export.md) eszközeivel adatokat küldhet az [Azure Event Hubs szolgáltatásnak.](https://docs.microsoft.com/azure/event-hubs/) Ezután integrálja az Event Hub-adatokat egy külső Gyártóhekkel.
- [A REST API](https://docs.microsoft.com/rest/api/securitycenter/) – Ha a REST API-t használja a riasztások eléréséhez, tekintse meg az [online riasztások API dokumentációját.](https://docs.microsoft.com/rest/api/securitycenter/alerts)

Ha bármilyen programozott módszert használ a riasztások felhasználásához, a megfelelő sémára lesz szüksége az Ön számára releváns mezők megkereséséhez. Ha egy eseményközpontba exportál, vagy általános HTTP-összekötőkkel próbálja elindítani a munkafolyamat-automatizálást, használja a sémákat a JSON-objektumok megfelelő elemzéséhez.

>[!IMPORTANT]
> A séma némileg eltér az egyes forgatókönyvek, ezért győződjön meg róla, hogy válassza ki a megfelelő lapot alább.


## <a name="the-schemas"></a>A sémák 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Munkafolyamat-automatizálás és folyamatos exportálás az Eseményközpontba](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Minta JSON a Logic Apps, event hub és külső SIEM-ek számára küldött riasztásokhoz

Az alábbiakban megtalálja a sémát a riasztási események átadott:

- A Security Center munkafolyamat-automatizálásában konfigurált Azure Logic App-példányok
- Az Azure Event Hub a Security Center folyamatos exportálási funkciójával

A munkafolyamat-automatizálási funkcióról a [Riasztásokra és javaslatokra adott válaszok automatizálása](workflow-automation.md)című témakörben talál további információt.
A folyamatos exportálásról további információt a Riasztások és javaslatok exportálása című [témakörben talál.](continuous-export.md)

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Az Azure Sentinel és a Log Analytics munkaterületei](#tab/schema-sentinel)

A Sentinel-összekötő értesítéseket kap az Azure Security Centertől, és elküldi azokat az Azure Sentinel Log Analytics-munkaterületére. 

Ha a Security Center riasztásait használva sentinelesetvagy incidens létrehozásához szüksége lesz az alább látható riasztások sémájára. 

Az Azure Sentinelről további információt [a dokumentációban](https://docs.microsoft.com/azure/sentinel/)talál.

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure-tevékenységnapló](#tab/schema-activitylog)

Az Azure Security Center naplózása generált biztonsági riasztások eseményekként az Azure-tevékenységnaplóban.

A biztonsági riasztások eseményeit a Tevékenységnaplóban az alábbi módon tekintheti meg:

[![A tevékenységnaplóban az Aktiválási riasztás esemény keresése](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Minta JSON az Azure-tevékenységnaplóba küldött riasztásokhoz

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>A séma adatmodellje

|Mező|Leírás|
|----|----|
|**Csatornák**|Állandó, "Művelet"|
|**korrelációazonosító**|Az Azure Security Center riasztásazonosítója|
|**Leírás**|A figyelmeztető jelzés leírása|
|**eventDataId**|Lásd correlationId|
|**eventName**|Az érték és a localizedValue almezők tartalmazzák a riasztás megjelenítendő nevét|
|**Kategória**|Az érték és a localizedValue almezők állandóak - "Biztonság"|
|**eventTimestamp**|UTC időbélyeg a riasztás létrehozásának időpontjára|
|**id**|A teljesen minősített riasztásazonosító|
|**Szinten**|Állandó, "Információs"|
|**műveletazonosító**|Lásd correlationId|
|**operationName**|Az értékmező állandó - "Microsoft.Security/locations/alerts/activate/action", és a honosított érték "Riasztás aktiválása" lesz (potenciálisan honosítható a felhasználó területi beállításával)|
|**resourceGroupName**|Tartalmazni fogja az erőforráscsoport nevét|
|**resourceProviderName**|Az érték és a localizedValue almezők állandóak - "Microsoft.Security"|
|**resourceType típus**|Az érték és a localizedValue almezők állandóak - "Microsoft.Security/locations/alerts"|
|**resourceId**|A teljesen minősített Azure-erőforrásazonosító|
|**Állapot**|Az érték és a localizedValue almezők állandóak - "Aktív"|
|**alállapot**|Az érték és a localizedValue almezők üresek|
|**beküldésIdőbélyeg**|A tevékenységnaplóba küldött eseménybeküldött eseményUTC-időbélyegzője|
|**előfizetésazonosító**|A feltört erőforrás előfizetési azonosítója|
|**Tulajdonságok**|Egy JSON táska további tulajdonságok kal kapcsolatos riasztást. Ezek az egyik riasztásról a másikra változhatnak, azonban a következő mezők jelennek meg az összes riasztásban:<br>- súlyossága: A támadás súlyossága<br>- compromisedEntity: A feltört erőforrás neve<br>- felszámolásLépések: A javítási lépések sora<br>- A riasztás gyilkos láncú szándéka. A lehetséges szándékokat a [Szándékok táblázat dokumentálja](alerts-reference.md#intentions)|
|**relatedEvents (kapcsolódó események)**|Állandó - üres tömb|
|||





### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

A Microsoft Graph a Microsoft 365-ben található adatok és intelligencia átjárója. Egységes programozhatósági modellt biztosít, amelyekkel az Office 365, a Windows 10 és az Enterprise Mobility + Security hatalmas mennyiségű adathoz férhet hozzá. A Microsoft Graph rengeteg adatával olyan szervezetek és fogyasztók számára hozhat létre alkalmazásokat, amelyek felhasználók millióival lépnek kapcsolatba.

Az MS Graph-nak küldött biztonsági riasztások sémája és JSON-ábrázolása [a Microsoft Graph dokumentációjában](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0)található.

---


## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti a sémákat, amelyeket az Azure Security Center fenyegetésvédelmi eszközei használnak a biztonsági riasztási információk küldésekor.

A biztonsági riasztások biztonsági központon kívülről való elérésének módjairól a következő oldalakon talál további információt:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) - a Microsoft natív SIEM-je
- [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) – a Microsoft teljes körűen felügyelt, valós idejű adatbetöltési szolgáltatása
- A Security Center [folyamatos exportálási funkciója](continuous-export.md)
- [Log Analytics-munkaterületek](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) – Az Azure Monitor naplóadatokat tárol egy Log Analytics-munkaterületen, amely adatokat és konfigurációs adatokat tartalmazó tárolóban tárolja a naplóadatokat
