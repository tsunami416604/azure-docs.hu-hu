---
title: A REST API-val metrikák beolvasása
titlesuffix: Azure Load Balancer
description: Az Azure REST API-k használatával állapotát és a kihasználtság metrikák gyűjthetők Load Balancer az idő és dátumok adott tartományra.
services: sql-database
author: KumudD
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 06/06/2017
ms.author: KumudD
ms.openlocfilehash: 5f92ae386730032e7da278b9dd2b0effef313741
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752058"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>Load Balancer-kihasználtsági mérőszámokat a REST API-val beolvasása

Ez az útmutató bemutatja, hogyan gyűjthet által feldolgozott adatmennyiség bájtban egy [Standard Load Balancer](/azure/load-balancer/load-balancer-standard-overview) időközt idő használata a [Azure REST API](/rest/api/azure/).

Teljes dokumentációja és a REST API további példákat érhető el a [Azure Monitor REST-referencia](/rest/api/monitor). 

## <a name="build-the-request"></a>A kérelem létrehozása

A következő GET kérelmet összegyűjtéséhez használja az [ByteCount metrika](/azure/load-balancer/load-balancer-standard-diagnostics#a-name--multidimensionalmetricsamulti-dimensional-metrics) a Standard Load Balancer. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
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
| subscriptionId | Az előfizetés-azonosítója, amely azonosítja az Azure-előfizetéssel. Ha több előfizetéssel rendelkezik, tekintse meg [több előfizetés használata](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Az erőforrást tartalmazó erőforráscsoport neve. Ez az érték az Azure Resource Manager API, a parancssori felület vagy a portál szerezhet. |
| loadBalancerName | Az Azure Load Balancer neve. |
| metricnames | Vesszővel tagolt listája érvényes [Load Balancer metrikái](/azure/load-balancer/load-balancer-standard-diagnostics). |
| API-verzió | A használandó a kérelem API-verzió.<br /><br /> Ez a dokumentum ismerteti az api-version `2018-01-01`, a fenti URL-címe megtalálható.  |
| Időtartam | A lekérdezési időtartam. A karakterlánc a következő formátumú `startDateTime_ISO/endDateTime_ISO`. Ez nem kötelező paraméter értéke a példában egy nap alatt az adatok visszaadása. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>A kérés törzse

Ez a művelet nem a kérelem törzsében van szükség.

## <a name="handle-the-response"></a>A válasz kezelése

200-as állapotkód adja vissza, ha sikeresen metrika értékek listáját adja vissza. Hibakódok teljes listája megtalálható a [referenciadokumentációt](/rest/api/monitor/metrics/list#errorresponse).

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
