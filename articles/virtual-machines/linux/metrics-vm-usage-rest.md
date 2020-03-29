---
title: Az Azure virtuálisgép-használati adatok beszereznie a REST API-val
description: Az Azure REST API-k segítségével egy virtuális gép kihasználtsági metrikák gyűjtéséhez.
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 07e91f3d9fd32f01db91415bfd90746cd1aef403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944751"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Virtuálisgép-használati metrikák begyűjtése a REST API használatával

Ez a példa bemutatja, hogyan lehet lekérni egy [Linux virtuális gép](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) processzorhasználatát az Azure REST API [használatával.](/rest/api/azure/)

A REST API-hoz teljes körű referenciadokumentáció és további minták érhetők el az [Azure Monitor REST-hivatkozásában.](/rest/api/monitor) 

## <a name="build-the-request"></a>A kérelem felépítése

A következő GET-kérelem segítségével gyűjtse össze a [Százalékos CPU-metrikát](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) egy virtuális gépről

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
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
| resourceGroupName | Az erőforráshoz társított Azure-erőforráscsoport neve. Ezt az értéket az Azure Resource Manager API-ból, a CLI-ből vagy a portálról szerezheti be. |
| vmname | Az Azure virtuális gép neve. |
| metrikák neve | Az érvényes [terheléselosztó mérőszámok](/azure/load-balancer/load-balancer-standard-diagnostics)vesszővel tagolt listája . |
| api-verzió | A kérelemhez használandó API-verzió.<br /><br /> Ez a dokumentum `2018-01-01`a fenti URL-ben található api-verzióra vonatkozik.  |
| időtartomány | Karakterlánc a következő `startDateTime_ISO/endDateTime_ISO` formátumban, amely meghatározza a visszaadott metrikák időtartományát. Ez a választható paraméter úgy van beállítva, hogy egy napi adatait adja vissza a példában. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>A kérés törzse

Ehhez a művelethez nincs szükség kéréstörzsre.

## <a name="handle-the-response"></a>A válasz kezelése

A 200-as állapotkódot a rendszer akkor adja vissza, amikor a metrikaértékek listáját sikeresen visszaadja. A hibakódok teljes listája a [referenciadokumentációban](/rest/api/monitor/metrics/list#errorresponse)található .

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
