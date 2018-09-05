---
title: Azure virtuális gép használati adatok a REST API használatával |} A Microsoft Docs
description: Az Azure REST API-k használatával kihasználtsági mérőszámokat gyűjthet a virtuális gép.
services: virtual-machines
author: rloutlaw
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 2b888d1ac9b5ebffc95d35ecda4ab8d9d260fa6d
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669913"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>A REST API-val a virtuális gép használati metrikák beolvasása

Ez a példa bemutatja, hogyan kérheti le a CPU-használat egy [Linux rendszerű virtuális gép](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) használatával a [Azure REST API](/rest/api/azure/).

Teljes dokumentáció és a REST API további minták érhetők el a [Azure Monitor REST-referencia](/rest/api/monitor). 

## <a name="build-the-request"></a>A kérelem létrehozása

A következő GET kérelmet összegyűjtéséhez használja az [százalékos Processzorhasználat metrika](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) a virtuális gépről

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Kérelemfejlécek

A következő fejléceket szükség: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*A Content-Type:*|Kötelező. Állítsa be `application/json`.|  
|*Hitelesítés:*|Kötelező. Egy érvényes értékre `Bearer` [hozzáférési jogkivonat](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-paraméterek

| Name (Név) | Leírás |
| :--- | :---------- |
| subscriptionId | Az előfizetés-azonosítója, amely azonosítja az Azure-előfizetéssel. Ha több előfizetéssel rendelkezik, tekintse meg [több előfizetés használata](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). |
| resourceGroupName | Az erőforrás társított Azure-erőforráscsoport nevére. Ez az érték lekérése az Azure Resource Manager API, a CLI vagy a portálon. |
| vmname | Az Azure virtuális gép neve. |
| metricnames | Vesszővel tagolt listája érvényes [Load Balancer metrikái](/azure/load-balancer/load-balancer-standard-diagnostics). |
| API-verzió | A használandó a kérelem API-verzió.<br /><br /> Ez a dokumentum ismerteti az api-version `2018-01-01`, a fenti URL-címe megtalálható.  |
| Időtartam | A következő formátumú karakterlánc- `startDateTime_ISO/endDateTime_ISO` , amely meghatározza, hogy a visszaadott metrikák időtartománya. Ez nem kötelező paraméter értéke a példában egy nap alatt az adatok visszaadása. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Kérelem törzse

Ez a művelet nem a kérelem törzsében van szükség.

## <a name="handle-the-response"></a>A válasz kezeléséhez

200-as állapotkód adja vissza, ha sikeresen metrika értékek listáját adja vissza. Hibakódok teljes listája megtalálható a [referenciadokumentációt](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Példaválasz 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```