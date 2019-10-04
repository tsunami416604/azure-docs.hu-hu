---
title: Az Azure API Management Service IP-címei | Microsoft Docs
description: Megtudhatja, hogyan kérhet le egy Azure API Management-szolgáltatás IP-címeit és azok változását.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 7224c6a77df496624903830f0a2cbd8d193517cc
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178195"
---
# <a name="ip-addresses-of-azure-api-management"></a>Az Azure API Management IP-címei

Ebben a cikkben azt ismertetjük, hogyan lehet lekérdezni az Azure API Management Service IP-címeit. Az IP-címek nyilvánosak vagy magánjellegűek lehetnek, ha a szolgáltatás virtuális hálózaton van.

Az IP-címek használatával tűzfalszabályok hozhatók létre, szűrheti a háttér-szolgáltatás bejövő forgalmát, vagy korlátozhatja a kimenő forgalmat.

## <a name="ip-addresses-of-api-management-service"></a>API Management szolgáltatás IP-címei

A fejlesztői, az alapszintű, a standard vagy a prémium szintű API Management Service-példányok nyilvános IP-címekkel rendelkeznek, amelyek kizárólag az adott szolgáltatási példányra vonatkoznak (ezek nem vannak megosztva más erőforrásokkal). 

Az IP-címeket a Azure Portal erőforrásának áttekintés irányítópultján kérheti le.

![API Management IP-cím](media/api-management-howto-ip-addresses/public-ip.png)

Ezeket programozott módon is beolvashatja a következő API-hívással:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

A nyilvános IP-címek a válasz részét képezik:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

A [többrégiós](api-management-howto-deploy-multi-region.md)környezetekben minden regionális telepítés egy nyilvános IP-címmel rendelkezik.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>API Management szolgáltatás IP-címei a VNET-ben

Ha a API Management szolgáltatás egy virtuális hálózaton belül van, akkor két típusú IP-címmel rendelkezik – nyilvános és privát.

A nyilvános IP-címek a porton `3443` belüli belső kommunikációhoz használatosak – a konfiguráció kezeléséhez (például Azure Resource Manageron keresztül). A külső VNET konfigurációjában a futtatókörnyezet API-forgalmára is használhatók. Ha API Management küld egy nyilvános (internetes) háttérre irányuló kérést, a rendszer a kérelem forrásaként egy nyilvános IP-címet fog látni.

A magánhálózati virtuális IP-címek a hálózaton belülről API Management végpontokra, a fejlesztői portálra és a közvetlen API-hozzáférés felügyeleti síkjával való kapcsolódásra szolgálnak. Használhatja őket a DNS-rekordok hálózaton belüli beállításához.

Mindkét típus címét látni fogja a Azure Portal és az API-hívás válaszában:

![API Management a VNET IP-címe](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>A használati szintek API Management szolgáltatás IP-címei

Ha a API Management szolgáltatás egy felhasználási rétegű szolgáltatás, akkor nem rendelkezik dedikált IP-címmel. A használati szintek szolgáltatás megosztott infrastruktúrán fut, és determinisztikus IP-cím nélkül működik. 

Forgalmi korlátozási célokra használhatja az Azure-adatközpontok IP-címeinek tartományát. A pontos lépésekért tekintse meg [a Azure functions dokumentációs cikkét](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) .

## <a name="changes-to-the-ip-addresses"></a>Az IP-címek változásai

A API Management fejlesztői, alapszintű, standard és prémium szintjein a nyilvános IP-címek (VIP) statikusak a szolgáltatás élettartama szempontjából, a következő kivételekkel:

* A szolgáltatás törlődik, majd újra létrejön.
* A szolgáltatás előfizetése fel van [függesztve](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) vagy [figyelmeztetve](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) van (például nem fizetés esetén), majd visszaállították.
* Az Azure Virtual Network hozzáadva vagy eltávolítva a szolgáltatásból.
* API Management szolgáltatás külső és belső vnet-telepítési mód között van átváltva.

A többrégiós környezetekben a regionális IP-cím akkor [változik, ha](api-management-howto-deploy-multi-region.md)egy régiót kiürítettek, majd visszaállították.
