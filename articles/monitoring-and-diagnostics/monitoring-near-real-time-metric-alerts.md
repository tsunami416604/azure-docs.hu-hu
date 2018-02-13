---
title: "Majdnem valós idejű metrika riasztások az Azure-figyelő |} Microsoft Docs"
description: "Megtudhatja, hogyan közel valós idejű metrika riasztások a figyelheti az Azure-erőforrás metrikáit mérete legyen 1 perces gyakorisággal."
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
ms.openlocfilehash: 6370f4596e6b20962c6de0dbcbd5f86c3b7777cc
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Közel valós idejű metrika riasztások (előzetes verzió)
Az Azure használatát támogatja közel valós idejű metrika riasztások (előzetes verzió) nevű új riasztási típus. Ez a funkció jelenleg nyilvános előzetes verziójához.

Közel valós idejű metrika riasztások eltér a rendszeres metrika riasztások néhány módon:

- **Továbbfejlesztett késés**: közel valós idejű metrika riasztások figyelheti a mérete legyen 1 perces gyakorisággal metrika értékek változásait.
- **Metrika feltételek teljesebb körű vezérlése**: gazdagabb riasztási szabályok a közel valós idejű metrika riasztásokat definiálhat. A riasztások a maximális, minimális, átlagos, és az összes érték a mérőszámok figyelésére alkalmas.
- **Egyszerre több metrikák figyelését**: közel valós idejű metrika riasztások több metrikák (jelenleg legfeljebb két metrikák) kezelhető egyetlen szabállyal képes figyelni. Riasztást vált ki, ha mindkét metrikák számítógépeinek a megadott időszakra vonatkozó megsértik a a vonatkozó küszöbértéket.
- **Moduláris értesítési rendszer**: közel valós idejű metrika riasztások használata [művelet csoportok](monitoring-action-groups.md). Művelet csoportokat hozhat létre moduláris műveletek. A riasztási szabályok több művelet csoportok is felhasználhatja.

> [!NOTE]
> A közel valós idejű metrika riasztási funkció jelenleg nyilvános előzetes verziójához. A funkció és a felhasználói élmény van változhat.
>

## <a name="resources-you-can-use-with-near-real-time-metric-alerts"></a>Erőforrások közel valós idejű metrika riasztások használata
A közel valós idejű metrika riasztások támogató típusú erőforrások teljes listája itt található:

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

## <a name="near-real-time-metric-alerts-for-metrics-that-use-dimensions"></a>Közel valós idejű metrika riasztások dimenziók metrikák
Közel valós idejű metrika riasztások támogatja a dimenziók metrikák vonatkozó riasztások elküldésére. A metrika a megfelelő szintre szűréséhez használja a dimenziók is. Közel valós idejű metrika dimenziók metrikák riasztásai a következő típusú erőforrások támogatottak:

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (csak tárfiókok USA régiókban támogatott)
* Microsoft.Storage/storageAccounts/services (csak tárfiókok USA régiókban támogatott)

## <a name="create-a-near-real-time-metric-alert"></a>Riasztás létrehozása, a közel valós idejű metrika
Jelenleg közel valós idejű metrika riasztásokat csak az Azure-portálon hozhat létre. PowerShell Azure parancssori felület (CLI), és az Azure figyelő REST API-k használatával közel valós idejű metrika riasztások konfigurálásának a támogatását hamarosan elérhető.

A felhasználói élmény, a közel valós idejű metrika riasztások létrehozásához át lett helyezve az új **riasztások (előzetes verzió)** lap. Akkor is, ha az aktuális riasztások oldal **hozzáadása közel valós idejű metrika riasztás**, ekkor a **riasztások (előzetes verzió)** lap.

Riasztás létrehozása, a közel valós idejű metrika lásd: [riasztási szabályt létrehozni az Azure portálon](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-near-real-time-metric-alerts"></a>Közel valós idejű metrika riasztások kezelése
Miután létrehozta a közel valós idejű metrika riasztások, a riasztás ismertetett lépések segítségével kezelheti [a riasztásokat az Azure portálon](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="payload-schema"></a>Hasznos séma

A POST műveletet tartalmazza a következő JSON-adattartalmat és a séma összes közel valós idejű metrika riasztások:

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

* További információ az új [(előzetes verzió) élmény riasztások](monitoring-overview-unified-alerts.md).
* További tudnivalók [riasztások jelentkezzen be Azure riasztások (előzetes verzió)](monitor-alerts-unified-log.md).
* További tudnivalók [értesítések az Azure-ban](monitoring-overview-alerts.md).