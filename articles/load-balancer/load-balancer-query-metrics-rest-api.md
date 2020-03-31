---
title: Metrikák lekérése a REST API-val
titleSuffix: Azure Load Balancer
description: Ebben a cikkben az Azure REST API-k használatával az Azure Load Balancer állapot- és használati metrikák gyűjtésére.
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 760ec8a945ab88b63dde2de75f5354818facf4f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225255"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Terheléselosztó használati metrikáinak begyűjtése a REST API használatával

Gyűjtse össze a [standard terheléselosztó](/azure/load-balancer/load-balancer-standard-overview) által egy ideig feldolgozott bájtok számát az [Azure REST API használatával.](/rest/api/azure/)

A REST API-hoz teljes körű referenciadokumentáció és további minták érhetők el az [Azure Monitor REST-hivatkozásában.](/rest/api/monitor) 

## <a name="build-the-request"></a>A kérelem felépítése

A következő GET-kérelem segítségével gyűjtse össze a [ByteCount metrikát](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) egy standard terheléselosztótól. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Kérésfejlécek

A következő fejlécek megadása kötelező: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type* (Tartalomtípus):|Kötelező. Állítsa `application/json` értékre.|  
|*Authorization* (Engedélyezés):|Kötelező. Állítsa egy érvényes `Bearer` [hozzáférési jogkivonatra](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-paraméterek

| Név | Leírás |
| :--- | :---------- |
| subscriptionId | Az Azure-előfizetést azonosító előfizetés-azonosító. Ha több előfizetéssel rendelkezik, olvassa el [a Több előfizetés sel végzett témakört.](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest) |
| resourceGroupName | Az erőforrást tartalmazó erőforráscsoport neve. Ezt az értéket az Azure Resource Manager API-ból, a CLI-ből vagy a portálról szerezheti be. |
| loadBalancerName | Az Azure Load Balancer neve. |
| metrikus nevek | Az érvényes [terheléselosztó mérőszámok](/azure/load-balancer/load-balancer-standard-diagnostics)vesszővel tagolt listája . |
| api-verzió | A kérelemhez használandó API-verzió.<br /><br /> Ez a dokumentum `2018-01-01`a fenti URL-ben található api-verzióra vonatkozik.  |
| időtartomány | A lekérdezés időtartománya. Ez egy karakterlánc a következő `startDateTime_ISO/endDateTime_ISO`formátumban . Ez a választható paraméter úgy van beállítva, hogy egy napi adatait adja vissza a példában. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>A kérés törzse

Ehhez a művelethez nincs szükség kéréstörzsre.

## <a name="handle-the-response"></a>A válasz kezelése

A 200-as állapotkódot a rendszer akkor adja vissza, amikor a metrikaértékek listáját sikeresen visszaadja. A hibakódok teljes listája a [referenciadokumentációban](/rest/api/monitor/metrics/list#errorresponse)található .

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
