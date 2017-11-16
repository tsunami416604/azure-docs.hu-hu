---
title: "Konfigurálja a meglévő probléma felügyeleti rendszerekhez használatával olyan webhook állapotával kapcsolatos értesítésekre |} Microsoft Docs"
description: "Személyre szabott értesítések a probléma meglévő felügyeleti rendszer eseményekről a szolgáltatás állapotának beolvasása."
author: shawntabrizi
manager: scotthit
editor: 
services: service-health
documentationcenter: service-health
ms.assetid: 
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: b6a5f61f61675b825dcfe9c706c80944f5890538
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>Konfigurálja a meglévő probléma felügyeleti rendszerekhez használatával olyan webhook állapotával kapcsolatos értesítésekre

Ez a cikk bemutatja, hogyan Webhookok keresztül adatokat küldeni a értesítést a rendszer a szolgáltatás állapotát riasztások konfigurálása.

Health-riasztások ma, beállíthatja, hogy az Azure szolgáltatás incidensek észleli, ha értesítést kap arról keresztül szöveges üzenetben vagy e-mailben.
Azonban előfordulhat, hogy már meglévő külső értesítési rendszer, amely használni szeretné.
Ez a dokumentum azt ismerteti, a webhook forgalma, és hogyan hozhat létre egyéni riasztások szolgáltatásokkal kapcsolatos problémákról hatása a felhasználókra tájékoztatást kaphat a legfontosabb részeit.

Ha egy előre konfigurált integrációt használni kívánt, lásd: hogyan:
* [A ServiceNow riasztások konfigurálása](service-health-alert-webhook-servicenow.md)
* [Riasztások konfigurálása PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Riasztások konfigurálása OpsGenie](service-health-alert-webhook-opsgenie.md)

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>A szolgáltatás állapotának webhook forgalma használatával egyéni értesítést konfigurálása
Ha azt szeretné, a saját egyéni webhook integráció beállítása, a szolgáltatás állapotával kapcsolatos értesítésekre során elküldött JSON-adattartalmat elemezni szeretné.

Keresse meg [lásd például itt](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md) határozzák meg a `Service Health` webhook hasznos tűnik.

Ez egy olyan szolgáltatás állapota riasztás, ha megnézi észlelését `context.eventSource == "ServiceHealth"`. Onnan, amelyek leginkább megfelelnek a betöltési tulajdonságai vannak:
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-azure-service-health-for-an-incident"></a>Egy közvetlen hivatkozást az Azure szolgáltatás állapotát az incidens létrehozása
Létrehozhat egy közvetlen hivatkozást a személyre szabott Azure szolgáltatás állapota incidens asztali vagy hordozható létrehozott egy speciális URL-címet. Használja a `trackingId`, az első és az utolsó három számjegy, valamint a `subscriptionId`, kialakításához:
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

Például ha a `subscriptionId` van `bba14129-e895-429b-8809-278e836ecdb3` és a `trackingId` van `0DET-URB`, akkor a személyre szabott Azure szolgáltatás állapota URL-címe:

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>A szint használatával észleli a probléma súlyossága
A legalacsonyabb súlyosságát a legmagasabb súlyossági a `level` tulajdonság található a hasznos adatok között bármelyike lehet `Informational`, `Warning`, `Error`, és `Critical`.

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>Az érintett szolgáltatások az incidens teljes körének ismerete elemzése
Health-riasztások is tájékoztatnak, problémák több régiók és-szolgáltatások. Ahhoz, hogy a teljes körű információkat, értékének elemezni kell `impactedServices`.
A tartalom belül egy [escape-karaktersorozatot JSON](http://json.org/) karakterlánc, amikor unescaped, rendszeresen elemezhető egy másik JSON-objektumot tartalmaz.

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Válik:

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

Ez azt jelenti, hogy vannak-e "Riasztásokat és metrikák" Kelet-Ausztrália és délkeleti problémákat, valamint az "App Service" Ausztrália délkeleti problémák.


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>A webhook integrációs via HTTP POST-kérelmet tesztelése
1. Hozzon létre a szolgáltatás állapotát forgalma szeretne küldeni. Példa szolgáltatásának állapota webhook payloadot címen található [Webhookok Azure tevékenység naplózása riasztások](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Hozzon létre egy HTTP POST-kérelmet az alábbiak szerint:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Ekkor egy `2XX - Successful` választ.

4. Ugrás a [PagerDuty](https://www.pagerduty.com/) annak ellenőrzéséhez, hogy integráció sikeres volt-e beállítva.

## <a name="next-steps"></a>Következő lépések
- Tekintse át a [műveletnapló riasztási webhook séma](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- További tudnivalók [szolgáltatás állapotával kapcsolatos értesítésekre](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- További információ [művelet csoportok](../monitoring-and-diagnostics/monitoring-action-groups.md).