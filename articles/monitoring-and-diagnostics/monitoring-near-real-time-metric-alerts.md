---
title: "Majdnem valós idejű metrika riasztások az Azure figyelő |} Microsoft Docs"
description: "Közel valós idejű metrika riasztások engedélyezése az Azure erőforrás-metrikák 1 perces gyakorisággal figyelheti."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: d3e88a98e0ba93a630d131c25ca4dd5cb16f1b1a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Közel valós idejű metrika riasztások (előzetes verzió)
Az Azure figyelő mostantól támogatja a közel valós idejű metrika riasztások (előzetes verzió) nevű metrika riasztások új típusú. Ez a funkció jelenleg nyilvános előzetes verziójához.
Ezek a riasztások eltér a normál metrika riasztások néhány módszer az

- **Továbbfejlesztett késés** -közel valós idejű metrika riasztások figyelheti metrika értékek változásait, amint 1 perc.
- **Metrika feltételek teljesebb körű vezérlése** -közel valós idejű metrika riasztások engedélyezése a felhasználók szélesebb riasztási szabályok meghatározásához. Az értesítések mostantól támogatják az a maximális, minimális, átlagos, és az összes érték a metrikák a figyelést.
- **Egyszerre több metrikák figyelését** -közel valós idejű metrika riasztások több metrikák (jelenleg két) kezelhető egyetlen szabállyal képes figyelni. A figyelmeztetés lekérdezi Ha mindkét a metrikák a megadott ideig megsértik a a vonatkozó küszöbértéket.
- **Moduláris értesítési rendszer** - metrika valós idejű riasztások használata mellett [művelet csoportok](monitoring-action-groups.md). Ez a funkció lehetővé teszi, hogy a felhasználók hozhatnak létre műveletek moduláris módon, és használja fel őket a sok riasztási szabályok.

> [!NOTE]
> Közel valós idejű metrika riasztások funkció jelenleg nyilvános előzetes verziójához. A funkció és a felhasználói élmény van változhat.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Milyen erőforrásokat hozhat létre közel valós idejű metrika riasztások?
Közel valós idejű metrika riasztások által támogatott típusú erőforrások teljes listája:

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>A metrikák többdimenziósak közel valós idejű metrika riasztások
Közel valós idejű metrika riasztások támogatja a metrikák többdimenziósak riasztást küld. Dimenziók egy módszerre, amellyel szűrheti a metrika a megfelelő szintre. Közel valós idejű metrika riasztások a metrikák többdimenziósak támogatottak a következő típusú erőforrások

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (USA régiókban storage-fiókok csak támogatott)
* Microsoft.Storage/storageAccounts/services (USA régiókban storage-fiókok csak támogatott)


## <a name="create-a-near-real-time-metric-alert"></a>Riasztás létrehozása, a közel valós idejű metrika
Jelenleg közel valós idejű metrika riasztásokat csak hozhatók létre az Azure portálon keresztül. Közel valós idejű metrika riasztások PowerShell parancssori felület (CLI) és Azure figyelő REST API-n keresztül konfigurálása támogatása hamarosan elérhető.

A riasztási tapasztalat létrehozása közel valós idejű metrika riasztás át lett helyezve az új **Alerts(Preview)** tapasztalhat. Akkor is, ha az aktuális riasztások lapon látható **hozzáadása közel valós idejű metrika riasztás**, ekkor megnyílik az új felületre.

Létrehozhat egy közel valós idejű metrika riasztás ismertetett lépések [Itt](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="managing-near-real-time-metric-alerts"></a>Közel valós idejű metrika riasztások kezelése
Miután létrehozta a **közel valós idejű metrika riasztás**, ismertetett lépések kezelhető [Itt](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="payload-schema"></a>Hasznos séma

A POST műveletet tartalmazza a következő JSON-adattartalmat és a séma összes közel valós idejű metrika riasztásokat.

```json
{
    "WebhookName": "Alert1510875839452",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "condition": {
                "metricName": "Percentage CPU",
                "metricUnit": "Percent",
                "metricValue": "17.7654545454545",
                "threshold": "1",
                "windowSize": "10",
                "timeAggregation": "Average",
                "operator": "GreaterThan"
            },
            "resourceName": "ContosoVM1",
            "resourceType": "microsoft.compute/virtualmachines",
            "resourceRegion": "westus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/automationtest/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "timestamp": "2017-11-16T23:54:03.9517451Z",
            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/microsoft.insights/alertrules/VMMetricAlert1",
            "name": "VMMetricAlert1",
            "description": "A metric alert for the VM Win2012R2",
            "conditionType": "Metric",
            "subscriptionId": "00000000-0000-0000-0000-000000000000",
            "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "resourceGroupName": "ContosoVM"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    },
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "azure-insights/0.9",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="next-steps"></a>További lépések

* [További tudnivalók a változásról riasztások (előzetes verzió)](monitoring-overview-unified-alerts.md)
* [További tudnivalók a napló riasztásait az Azure-riasztások (előzetes verzió)](monitor-alerts-unified-log.md)
* [További tudnivalók az Azure-ban riasztások](monitoring-overview-alerts.md)