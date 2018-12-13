---
title: Meglévő problémát felügyeleti rendszerek egy webhook használatával állapotára vonatkozó értesítések konfigurálása |} A Microsoft Docs
description: Személyre szabott értesítések kapcsolatos a probléma meglévő felügyeleti rendszer szolgáltatásállapot-események beolvasása.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/27/2018
ms.author: shtabriz
ms.openlocfilehash: 5d32c3539446482f2dcdaeb954bb704dc9b78c58
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274922"
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>Meglévő problémát felügyeleti rendszerek egy webhook használatával állapotára vonatkozó értesítések konfigurálása

Ez a cikk bemutatja, hogyan konfigurálhatja a szolgáltatás állapotriasztások, Webhookok segítségével adatokat küldeni az értesítést a rendszer.

Service health-riasztások még ma, konfigurálhatja úgy, hogy az Azure-szolgáltatás incidens észleli, ha értesítés küldése SMS-üzenet vagy e-mailben.
Azonban előfordulhat, hogy már rendelkezik meglévő külső értesítési rendszer, amely csak szeretné használni.
Ez a dokumentum bemutatja a webhook hasznos adatai, és hogyan hozhat létre egyéni riasztásokat kaphat, amikor a szolgáltatással kapcsolatos problémák hatással vannak a legfontosabb részeit.

Ha szeretne egy előre konfigurált integrációt használja, lásd: hogyan:
* [Riasztások beállítása a ServiceNow](service-health-alert-webhook-servicenow.md)
* [Riasztások beállítása a pagerduty segítségével](service-health-alert-webhook-pagerduty.md)
* [Riasztások konfigurálás az opsgenie segítségével](service-health-alert-webhook-opsgenie.md)

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>A service health webhook hasznos adatai segítségével egyéni értesítés konfigurálása
Ha azt szeretné, a saját egyéni webhook-integráció beállítása során a szolgáltatás állapotával kapcsolatos értesítésekre küldött hasznos JSON elemzése szüksége.

Keresse meg [itt látható egy példa](../azure-monitor/platform/activity-log-alerts-webhook.md) , mi a `ServiceHealth` webhook hasznos adatai néz ki.

Ez egy szolgáltatásállapot-riasztás a naplófájlbejegyzéseket átnézve képes észlelni `context.eventSource == "ServiceHealth"`. Itt az, hogy leginkább releváns tulajdonságok a következők:
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-the-service-health-dashboard-for-an-incident"></a>A Service Health irányítópultján incidens közvetlen hivatkozás létrehozása
A Service Health irányítópultján, asztali vagy mobil mutató közvetlen hivatkozást hozhat létre egy speciális URL-cím létrehozásával. Használja a `trackingId`, az első és utolsó három számjegy, valamint a `subscriptionId`, kialakításához:
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

Például ha a `subscriptionId` van `bba14129-e895-429b-8809-278e836ecdb3` és a `trackingId` van `0DET-URB`, akkor a Service Health URL-címe:

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>A szint használatával észleli a probléma súlyossága
A legmagasabb súlyossági legalacsonyabb súlyosságot a `level` az adattartalomban szereplő tulajdonságot akkor lehet bármelyik `Informational`, `Warning`, `Error`, és `Critical`.

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>Az érintett szolgáltatások teljes körét az incidens megértéséhez-elemzés
Service health-riasztások segítségével azonosítható, hogy problémák több régióban és szolgáltatásokat. A teljes információért értékét elemezni kell `impactedServices`.
A tartalom belül egy [JSON-feloldójelekkel](http://json.org/) string, amikor unescaped, tartalmaz egy másik JSON-objektum, amely rendszeresen elemzi.

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

Ez jelzi, hogy nincsenek-e "Riasztásokat és metrikákat" Kelet-Ausztrália és Délkelet-problémák, valamint a problémák az "App Service" Délkelet-Ausztrália.


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Egy HTTP POST kérelem használatával a webhook-integráció tesztelése
1. Hozza létre a service health hasznos adatokat szeretne küldeni. Egy példa service health webhook hasznos adatai címen található [Webhookok az Azure-tevékenységi naplóriasztások](../azure-monitor/platform/activity-log-alerts-webhook.md).

2. HTTP POST-kérelmet a következőképpen hozhat létre:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Ekkor kap egy `2XX - Successful` választ.

4. Lépjen a [PagerDuty](https://www.pagerduty.com/) annak ellenőrzéséhez, hogy az integrációs sikeres volt-e beállítva.

## <a name="next-steps"></a>További lépések
- Tekintse át a [tevékenység log riasztási webhookséma](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Ismerje meg [szolgáltatás állapotára vonatkozó értesítések](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Tudjon meg többet [Műveletcsoportok](../azure-monitor/platform/action-groups.md).