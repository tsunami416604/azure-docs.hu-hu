---
title: Azure automatikus skálázású naplóesemények sémája
description: Naplók formátuma az automatikus skálázási műveletek figyeléséhez és hibaelhárításához
ms.topic: conceptual
ms.date: 11/14/2019
ms.subservice: autoscale
ms.openlocfilehash: 3c32f15208a8e692054ee6c1f7effc6b7c89de3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75395946"
---
# <a name="azure-monitor-autoscale-actions-resource-log-schema"></a>Az Azure Monitor automatikus skálázási műveletek erőforrásnapló-sémája

Az alábbiakban az automatikus skálázási erőforrásnaplók általános formátumait, példát is tartalmaz. Az alábbi példák közül nem mindegyik megfelelően formázott JSON, mert több olyan értéket is tartalmazhatnak, amelyek egy adott mezőre érvényesek lehetnek. 

Az ilyen típusú események segítségével elháríthatja az automatikus skálázással kapcsolatos problémákat. További információt az [Automatikus skálázási problémák elhárítása című témakörben talál.](autoscale-troubleshoot.md)


## <a name="profile-evaluation"></a>Profil kiértékelése

Automatikus skálázási profil első megtekintésekor rögzített

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": ["FixedDateProfileEvaluation", "RecurrentProfileEvaluation", "DefaultProfileEvaluation"],
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "profile": "defaultProfile",
    "profileSelected": [true, false]
  }
}
```

## <a name="profile-cooldown-evaluation"></a>Profil újratöltődési idő értékelése

Automatikus skálázás kiértékeli, ha nem kell csinálni a skálát, mert a lehűlési időszak. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "ScaleRuleCooldownEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "selectedProfile": "defaultProfile",
    "scaleDirection": ["Increase", "Decrease"]
    "lastScaleActionTime": "2018-09-10 18:08:00.6132593",
    "cooldown": "00:30:00",
    "evaluationTime": "2018-09-10 18:11:00.6132593",
    "skipRuleEvaluationForCooldown": true
  }
}
```

## <a name="rule-evaluation"></a>Szabály értékelése

Akkor kerül rögzítésre, amikor az automatikus skálázás először elkezdi kiértékelni egy adott léptékszabályt. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "ScaleRuleEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "timeGrain": "00:01:00",
    "timeGrainStatistic": "Average",
    "timeWindow": "00:10:00",
    "timeAggregationType": "Average",
    "operator": "GreaterThan",
    "threshold": 70,
    "observedValue": 25,
    "estimateScaleResult": ["Triggered", "NotTriggered", "Unknown"]
  }
}
```

## <a name="metric-evaluation"></a>Metrika kiértékelése

Automatikus skálázás kiértékelése során a metrika használt egy méretezési művelet aktiválásához. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "MetricEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "timeGrain": "00:01:00",
    "timeGrainStatistic": "Average",
    "startTime": "2018-09-10 18:00:00.43833793",
    "endTime": "2018-09-10 18:10:00.43833793",
    "data": [0.33333333333333331,0.16666666666666666,1.0,0.33333333333333331,2.0,0.16666666666666666,9.5]
  }
}
```

## <a name="instance-count-evaluation"></a>Példányszám kiértékelése

Akkor rögzített, amikor az automatikus skálázás kiértékeli a már futó példányok számát annak eldöntésére, hogy többet kell-e kezdenie, le kell-e állítania néhányat, vagy ne tegyen semmit. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceCountEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "currentInstanceCount": 20,
    "minimumInstanceCount": 15,
    "maximumInstanceCount": 30,
    "defaultInstanceCount": 20
  }
}
```

## <a name="scale-action-evaluation"></a>Méretarányos műveletek értékelése

Amikor az automatikus skálázás elindul a kiértékelés, ha egy skálázási műveletet kell végrehajtani. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "ScaleActionOperationEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "lastScaleActionOperationId": "378ejr-7yye-892d-17dd-92ndijfe1738",
    "lastScaleActionOperationStatus": ["InProgress", "Timeout"]
    "skipCurrentAutoscaleEvaluation": [true, false]
  }
}
```

## <a name="instance-update-evaluation"></a>Példányfrissítés kiértékelése

Automatikus skálázás frissítésekfrissítések a számítási példányok száma fut, akár fel, akár le.

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceUpdateEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "currentInstanceCount": 20,
    "newInstanceCount": 21,
    "shouldUpdateInstance": [true, false],
    "reason": ["Scale down action triggered", "Scale up to default instance count", ...]
  }
}
```

## <a name="scale-action"></a>Méretezési művelet

Akkor rögzítve, amikor az automatikus skálázás léptékezési műveletet kezdeményez fel- vagy leskálázási műveletben. 
```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceScaleAction",
  "category": "AutoscaleScaleActions",
  "resultType": ["Succeeded", "InProgress", "Failed"],
  "resultDescription": ["Create async operation job failed", ...]
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "currentInstanceCount": 20,
    "newInstanceCount": 21,
    "scaleDirection": ["Increase", "Decrease"],
    ["createdAsyncScaleActionJob": [true, false],]
    ["createdAsyncScaleActionJobId": "378ejr-7yye-892d-17dd-92ndijfe1738",]
  }
}
```

## <a name="scale-action-tracking"></a>Műveletkövetés méretezése

Egy példányméretezési művelet különböző időközönként rögzítve.

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceScaleAction",
  "category": "AutoscaleScaleActions",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "scaleActionOperationId": "378ejr-7yye-892d-17dd-92ndijfe1738",
    "scaleActionOperationStatus": ["InProgress", "Timeout", "Canceled", ...],
    "scaleActionMessage": ["Scale action is inprogress", ...]
  }
}
```

## <a name="next-steps"></a>További lépések
További információ az [automatikus skálázásról](autoscale-overview.md)
