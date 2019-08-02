---
title: Metrikák beolvasása a REST API
titlesuffix: Azure Load Balancer
description: Az Azure REST API-kkal összegyűjtheti a Load Balancer állapotának és kihasználtságának mérőszámait egy adott időtartamra és dátumra vonatkozóan.
services: sql-database
author: asudbring
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 06/06/2017
ms.author: allensu
ms.openlocfilehash: 0d12dc04aff58dd6273d8d29d422bdbd9e7c886b
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274529"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>Load Balancer kihasználtsági metrikák beszerzése a REST API használatával

Ez az útmutató bemutatja, hogyan gyűjtheti össze egy [standard Load Balancer](/azure/load-balancer/load-balancer-standard-overview) által feldolgozott bájtok számát az [Azure REST API](/rest/api/azure/)használatával.

A REST API dokumentációja és a további minták a [Azure monitor Rest](/rest/api/monitor)-referenciában érhetők el. 

## <a name="build-the-request"></a>A kérelem létrehozása

A következő GET kérelem használatával gyűjtheti össze a [ByteCount metrikáját](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) egy standard Load Balancer. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Kérelemfejlécek

A következő fejlécek szükségesek: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type:*|Kötelező. Állítsa a `application/json`következőre:.|  
|*Authorization:*|Kötelező. Érvényes `Bearer` [hozzáférési](/rest/api/azure/#authorization-code-grant-interactive-clients)jogkivonatra van beállítva. |  

### <a name="uri-parameters"></a>URI-paraméterek

| Name (Név) | Leírás |
| :--- | :---------- |
| subscriptionId | Az Azure-előfizetést azonosító előfizetés azonosítója. Ha több előfizetéssel rendelkezik, tekintse meg [a több előfizetés használata](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)című témakört. |
| resourceGroupName | Az erőforrást tartalmazó erőforráscsoport neve. Ezt az értéket a Azure Resource Manager API-ból, a CLI-ből vagy a portálról szerezheti be. |
| loadBalancerName | A Azure Load Balancer neve. |
| metricnames | Az érvényes [Load Balancer metrikák](/azure/load-balancer/load-balancer-standard-diagnostics)vesszővel tagolt listája. |
| api-version | A kérelemhez használandó API-verzió.<br /><br /> Ez a dokumentum a fenti URL `2018-01-01`-cím részét képező API-verziót tartalmazza.  |
| TimeSpan | A lekérdezés TimeSpan. Ez egy karakterlánc, amely a következő formátumú `startDateTime_ISO/endDateTime_ISO`. Ez a választható paraméter úgy van beállítva, hogy a példában egy nap értékű adatot adja vissza. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>A kérés törzse

Ehhez a művelethez nem szükséges a kérelem törzse.

## <a name="handle-the-response"></a>A válasz kezelése

A rendszer a 200-as állapotkódot adja vissza, ha a metrikai értékek listáját sikeresen visszaadja. A hibakódok teljes listája megtalálható a dokumentációban. [](/rest/api/monitor/metrics/list#errorresponse)

## <a name="example-response"></a>Példaválasz 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```
