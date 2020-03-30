---
title: Az Azure Service Health-értesítések küldése webhookokon keresztül
description: Személyre szabott értesítéseket küldhet a szolgáltatásállapot-eseményekről a meglévő problémakezelő rendszernek.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 2609a267bd151354f83482ab16c4b9345aa88cc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062859"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Webhook használata állapotértesítések konfigurálásához problémakezelő rendszerekhez

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Service Health riasztások at webhookokon keresztül a meglévő értesítési rendszer adatok küldése.

Beállíthatja, hogy a Service Health riasztások sms-ben vagy e-mailben értesítsék Önt, ha egy Azure-szolgáltatási incidens hatással van Önre.

De előfordulhat, hogy már rendelkezik egy meglévő külső értesítési rendszerrel, amelyet szeretne használni. Ez a cikk azonosítja a webhook-hasznos adat legfontosabb részeit. És azt ismerteti, hogyan hozhat létre egyéni riasztásokat, hogy értesítse Önt, ha releváns szolgáltatási problémák fordulnak elő.

Ha előre konfigurált integrációt szeretne használni, olvassa el a következő témakört:
* [Riasztások konfigurálása a ServiceNow szolgáltatással](service-health-alert-webhook-servicenow.md)
* [Riasztások konfigurálása a PagerDuty segítségével](service-health-alert-webhook-pagerduty.md)
* [Riasztások konfigurálása opsgenie](service-health-alert-webhook-opsgenie.md)

**Nézzen meg egy bevezető videót:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Egyéni értesítés konfigurálása a Service Health webhook hasznos adatával
Saját egyéni webhook-integráció beállításához elemeznie kell a Szolgáltatásállapot-értesítésen keresztül küldött JSON-hasznos adat.

Tekintse meg a webhook-hasznos adatra [példa.](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth`

Megerősítheti, hogy ez egy szolgáltatás állapotriasztása a . `context.eventSource == "ServiceHealth"` A következő tulajdonságok a legfontosabbak:
- **data.context.activityLog.status**
- **data.context.activityLog.level**
- **data.context.activityLog.subscriptionId**
- **data.context.activityLog.properties.title**
- **data.context.activityLog.properties.impactStartTime**
- **data.context.activityLog.properties.communication**
- **data.context.activityLog.properties.impactedServices**
- **data.context.activityLog.properties.trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>A Szolgáltatásállapot irányítópultjára mutató hivatkozás létrehozása incidenshez
Közvetlen hivatkozást hozhat létre a Service Health irányítópultjára asztali számítógépen vagy mobileszközön egy speciális URL-cím létrehozásával. Használja a *trackingId* és az első három és az utolsó három számjegy a *subscriptionId* ebben a formátumban:

<i></i>https://app.azure.com/h/*&lt;trackingId&gt;*/*&lt;első három és utolsó három&gt; számjegye subscriptionId*

Ha például az *előfizetésazonosítója* bba14129-e895-429b-8809-278e836ecdb3, és a *követőazonosítója* 0DET-URB, a Szolgáltatás állapotának URL-címe:

https://app.azure.com/h/0DET-URB/bbadb3<i></i>

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>A szint használata a probléma súlyosságának észlelésére
A legalacsonyabbtól a legmagasabb súlyosságig a tartalom **szinttulajdonsága** lehet *tájékoztató*, *figyelmeztetés*, *hiba*vagy *kritikus.*

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Az érintett szolgáltatások elemzése az incidens hatókörének meghatározásához
A Szolgáltatásállapot-riasztások több régióban és szolgáltatásban is tájékoztathatnak a problémákról. A teljes részletek hez elemeznie kell a `impactedServices`értékét.

A tartalom, amely belsejében egy escaped [JSON-karakterlánc,](https://json.org/) amely, ha nem escaped, tartalmaz egy másik JSON-objektumot, amely rendszeresen elemezhető. Példa:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Lesz:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Ez a példa a következő problémákat mutatja be:
- "Riasztások & mérőszámok" Ausztráliában Kelet-és Ausztrália délkeleti.
- "App Service" Ausztráliában délkeletre.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Tesztelje webhook-integrációját HTTP POST-kérelemmel

Kövesse az alábbi lépéseket:

1. Hozza létre a küldeni kívánt szolgáltatás állapothasznos adatát. Tekintse meg a szolgáltatás állapotának webhook-hasznos adatát [az Azure-tevékenységnapló-riasztások webhookok on.see](../azure-monitor/platform/activity-log-alerts-webhook.md)a example service health webhook payload at Webhooks for Azure activity log alerts.

1. Http POST-kérelem létrehozása az alábbiak szerint:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Meg kell kapnia a "2XX - Sikeres" választ.

1. Nyissa meg a [PagerDuty](https://www.pagerduty.com/) webhelyet, és ellenőrizze, hogy az integráció sikeresen be van-e állítva.

## <a name="next-steps"></a>További lépések
- Tekintse át a [tevékenységnapló-riasztási webhook-sémáját.](../azure-monitor/platform/activity-log-alerts-webhook.md) 
- További információ a [szolgáltatásállapot-értesítésekről.](../azure-monitor/platform/service-notifications.md)
- További információ a [műveletcsoportokról](../azure-monitor/platform/action-groups.md).