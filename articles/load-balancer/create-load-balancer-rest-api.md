---
title: Terheléselosztó létrehozása REST API használatával
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Load Balancer a REST API használatával.
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
ms.openlocfilehash: b52c554617bdcbe88b65639473044eb9c5eb7fa8
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045425"
---
# <a name="create-an-azure-basic-load-balancer-using-rest-api"></a>Azure alapszintű Load Balancer létrehozása a REST API használatával

A Azure Load Balancer a szabályok és az állapot-mintavételek alapján osztja el a terheléselosztó előtérben lévő új bejövő folyamatokat a háttérbeli készlet példányaiba. A Load Balancer két SKU-ban érhető el: alapszintű és standard. A két SKU-verzió közötti különbség megismeréséhez [Load BALANCER SKU-összehasonlításokat](concepts-limitations.md#skus).
 
Ez az útmutató azt mutatja be, hogyan hozható létre Azure alapszintű Load Balancer az [azure REST API](/rest/api/azure/) használatával a bejövő kérelmek terheléselosztásához egy Azure-beli virtuális hálózaton belül több virtuális gép között. A részletes dokumentáció és a további minták a [Azure Load BALANCER Rest-referenciában](/rest/api/load-balancer/)érhetők el.
 
## <a name="build-the-request"></a>A kérelem felépítése
Az alábbi HTTP PUT-kérelem használatával hozzon létre egy új Azure alapszintű Load Balancer.
 ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}?api-version=2018-02-01
  ```
### <a name="uri-parameters"></a>URI-paraméterek

|Név  |Az  |Szükséges |Type (Típus) |Leírás |
|---------|---------|---------|---------|--------|
|subscriptionId   |  path       |  Igaz       |   sztring      |  Az előfizetés hitelesítő adatai, amelyek egyedileg azonosítják az Microsoft Azure-előfizetést. Az előfizetés-azonosító az összes szolgáltatási hívás URI-JÁT képezi.      |
|resourceGroupName     |     path    | Igaz        |  sztring       |   Az erőforráscsoport neve.     |
|loadBalancerName     |  path       |      Igaz   |    sztring     |    A terheléselosztó neve.    |
|api-verzió    |   lekérdezés     |  Igaz       |     sztring    |  Ügyfél API-verziója.      |



### <a name="request-body"></a>A kérés törzse

Az egyetlen kötelező paraméter `location`. Ha nem határozza meg az *SKU* verzióját, a rendszer alapértelmezés szerint létrehoz egy alapszintű Load Balancer.  A terheléselosztó testreszabásához használjon [opcionális paramétereket](https://docs.microsoft.com/rest/api/load-balancer/loadbalancers/createorupdate#request-body) .

| Név | Type (Típus) | Leírás |
| :--- | :--- | :---------- |
| location | sztring | Erőforrás helye. A helyszínek aktuális listájának beolvasása a [lista helyei](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations) művelettel. |


## <a name="example-create-and-update-a-basic-load-balancer"></a>Példa: alapszintű Load Balancer létrehozása és frissítése

Ebben a példában először létre kell hoznia egy alapszintű Load Balancer az erőforrásaival együtt. Ezután konfigurálnia kell a terheléselosztó erőforrásait, amelyek tartalmazzák a előtérbeli IP-konfigurációt, a háttér-címkészletet, a terheléselosztási szabályt, az állapot-mintavételt és a bejövő NAT-szabályt.

Mielőtt létrehoz egy terheléselosztó-t az alábbi példával, hozzon létre egy *vnetlb* nevű virtuális hálózatot egy *subnetlb* nevű alhálózattal az **USA keleti** régiójának *rg1* nevű erőforráscsoporthoz.

### <a name="step-1-create-a-basic-load-balancer"></a>1\. LÉPÉS Alapszintű Load Balancer létrehozása
Ebben a lépésben létrehoz egy alapszintű Load Balancer *LB* néven az **USA keleti** régiójában, a *rg1* -erőforráscsoport alatt.
#### <a name="sample-request"></a>Példa a kérelemre

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>A kérés törzse

  ```JSON
   {
    "location": "eastus",
   }
  ```
### <a name="step-2-configure-load-balancer-resources"></a>2\. LÉPÉS Terheléselosztó-erőforrások konfigurálása
Ebben a lépésben a (NAT-szabály) előtérbeli IP-konfigurációt (*Fe-LB*), egy háttér-címkészlet (*be-LB*), egy terheléselosztási szabályt (*rulelb*), egy állapot-mintavételt (mintavétel *-LB*) és egy bejövő NAT-szabályt (*hálózati címfordítási*szabályt) tartalmazó terheléselosztó *LB* -erőforrásait konfigurálja.
#### <a name="sample-request"></a>Példa a kérelemre

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
