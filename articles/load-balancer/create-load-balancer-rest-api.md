---
title: Azure Load Balancer létrehozása REST API használatával
titlesuffix: Azure Load Balancer
description: Ismerje meg, hogyan hozhat létre egy Azure Load Balancer – REST API használatával.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: load-balancer
ms.date: 06/06/2018
ms.author: allensu
ms.openlocfilehash: ae8fb4494d27d0c145963c9b32757bdb802e0cc7
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275545"
---
# <a name="create-an-azure-basic-load-balancer-using-rest-api"></a>Az Azure alapszintű terheléselosztó létrehozása a REST API használatával

Egy Azure Load Balancer osztja el az új bejövő folyamatok, amelyek a terheléselosztó előtérbeli, a háttérbeli készletet példányok, szabályok és az állapotadat-mintavételek alapján az ügyfélszámítógépekre érkeznek. A Load Balancer két SKU-ban érhető el: alap- és standard szinten. A két SKU-verziók közötti különbségek megértése [Load Balancer Termékváltozat összehasonlítások](load-balancer-overview.md#skus).
 
Ez az útmutató bemutatja, hogyan hozzon létre egy Azure alapszintű Load Balancer a [Azure REST API](/rest/api/azure/) egyenleg bejövő kérelem terhelés egy Azure virtuális hálózaton belül több virtuális gép között. Teljes dokumentációt és további példákat érhető el a [Azure Load Balancer – REST-referencia](/rest/api/load-balancer/).
 
## <a name="build-the-request"></a>A kérelem létrehozása
A következő HTTP PUT kérelmet használatával hozzon létre egy új Azure alapszintű Load Balancert.
 ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}?api-version=2018-02-01
  ```
### <a name="uri-parameters"></a>URI-paraméterek

|Name (Név)  |A  |Szükséges |Típus |Leírás |
|---------|---------|---------|---------|--------|
|subscriptionId   |  elérési út       |  True (Igaz)       |   sztring      |  Az előfizetés hitelesítő adatait, amelyek egyértelműen azonosítják a Microsoft Azure-előfizetéshez. Az előfizetés-Azonosítót az URI-t minden szolgáltatás hívás részét képezi.      |
|resourceGroupName     |     elérési út    | True (Igaz)        |  sztring       |   Az erőforráscsoport neve.     |
|loadBalancerName     |  elérési út       |      True (Igaz)   |    sztring     |    A terheléselosztó neve.    |
|api-version    |   lekérdezés     |  True (Igaz)       |     sztring    |  API-verzió.      |



### <a name="request-body"></a>A kérés törzse

Csak a szükséges paraméter `location`. Ha nem adja meg a *Termékváltozat* verziója, egy alapszintű Load Balancer alapértelmezés szerint létrejön.  Használat [választható paraméterek](https://docs.microsoft.com/rest/api/load-balancer/loadbalancers/createorupdate#request-body) szabhatja testre a terheléselosztóhoz.

| Name (Név) | Típus | Leírás |
| :--- | :--- | :---------- |
| location | sztring | Erőforrás helye. Helyek használatával aktuális listájának lekérése a [lista helyek](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations) műveletet. |


## <a name="example-create-and-update-a-basic-load-balancer"></a>Példa: Alapszintű Load Balancer létrehozása és frissítése

Ebben a példában először létrehozhat egy alapszintű Load Balancer erőforrásait együtt. Ezután állítsa be a terheléselosztó erőforrásainak, beleértve az előtérbeli IP-konfiguráció, egy háttér címkészletet, a terheléselosztási szabály az állapotfigyelő mintavételező és bejövő NAT-szabály.

Az alábbi példa egy terheléselosztót hoz létre, mielőtt nevű virtuális hálózat létrehozása *vnetlb* nevű alhálózattal *subnetlb* nevű erőforráscsoportból *rg1* a a **USA keleti Régiójában** helyét.

### <a name="step-1-create-a-basic-load-balancer"></a>1\. LÉPÉS. Alapszintű Load Balancer létrehozása
Alapszintű Load Balancer, nevű létrehozása ebben a lépésben *lb* , a **USA keleti Régiójában** helyen belül a *rg1* erőforráscsoportot.
#### <a name="sample-request"></a>Mintakérelem

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>A kérés törzse

  ```JSON
   {
    "location": "eastus",
   }
  ```
### <a name="step-2-configure-load-balancer-resources"></a>2\. LÉPÉS. Terheléselosztó erőforrásainak konfigurálása
Ebben a lépésben konfigurálja a terheléselosztó *lb* erőforrásokat, például az előtérbeli IP-konfiguráció (*fe-lb*), egy háttércímkészlet (*kell lb*), a terheléselosztási szabály () *rulelb*), az állapotfigyelő mintavételező (*mintavétel-lb*), és a egy bejövő NAT-szabály ( *-nat-szabályok*).
#### <a name="sample-request"></a>Mintakérelem

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>A kérés törzse

  ```JSON
{
  "properties": {
    "frontendIPConfigurations": [
      {
        "name": "fe-lb",
        "properties": {
          "subnet": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnetlb/subnets/subnetlb"
          },
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ],
          "inboundNatRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/inboundNatRules/in-nat-rule"
            }  ]  }  }  ],
    "backendAddressPools": [
      {
        "name": "be-lb",
        "properties": {
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]   }   }   ],
    "loadBalancingRules": [
      {
        "name": "rulelb",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 80,
          "backendPort": 80,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp",
          "loadDistribution": "Default",
          "backendAddressPool": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/be-lb"
          },
          "probe": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/probes/probe-lb"
          }  }  }  ],
    "probes": [
      {
        "name": "probe-lb",
        "properties": {
          "protocol": "Http",
          "port": 80,
          "requestPath": "healthcheck.aspx",
          "intervalInSeconds": 15,
          "numberOfProbes": 2,
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]  }  } ],
    "inboundNatRules": [
      {
        "name": "in-nat-rule",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 3389,
          "backendPort": 3389,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp"
        } } ],
    "inboundNatPools": [],
    "outboundNatRules": []
  }  }
```
