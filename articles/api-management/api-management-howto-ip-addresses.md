---
title: Az Azure API Management szolgáltatás IP-címei | Microsoft dokumentumok
description: Ismerje meg, hogyan kérheti le egy Azure API Management szolgáltatás IP-címét, és mikor változnak.
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
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047747"
---
# <a name="ip-addresses-of-azure-api-management"></a>Az Azure API Management IP-címei

Ebben a cikkben bemutatjuk, hogyan lehet lekérni az Azure API Management szolgáltatás IP-címét. Az IP-címek lehetnek nyilvánosak vagy privátak, ha a szolgáltatás virtuális hálózatban van.

Az IP-címek segítségével tűzfalszabályokat hozhat létre, szűrheti a bejövő forgalmat a háttérszolgáltatásokhoz, vagy korlátozhatja a kimenő forgalmat.

## <a name="ip-addresses-of-api-management-service"></a>AZ API Management szolgáltatás IP-címei

A Fejlesztő, az Alapszintű, a Standard vagy a Prémium szint minden API Management szolgáltatáspéldányának nyilvános IP-címe van, amelyek kizárólag az adott szolgáltatáspéldányra tartoznak (nem vannak megosztva más erőforrásokkal). 

Az IP-címeket az Azure Portalon az erőforrás áttekintő irányítópultjáról kérheti le.

![API-kezelés IP-címe](media/api-management-howto-ip-addresses/public-ip.png)

Programozott módon is lehívhatja őket a következő API-hívással:

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

A [több régiós telepítéseken](api-management-howto-deploy-multi-region.md)minden regionális telepítéshez egy nyilvános IP-cím tartozik.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>AZ API Management szolgáltatás IP-címea virtuális hálózatban

Ha az API Management szolgáltatás egy virtuális hálózaton belül van, kétféle IP-címmel fog rendelkezni – nyilvános és privát.

Nyilvános IP-címek a porton `3443` belüli belső kommunikációhoz használatosak – a konfiguráció kezeléséhez (például az Azure Resource Manageren keresztül). A külső virtuális hálózat konfigurációjában is használják a futásidejű API-forgalom. Amikor egy kérelmet küld az API Management egy nyilvános (internet-alapú) háttérrendszer, egy nyilvános IP-cím lesz látható, mint a kérelem eredete.

Privát virtuális IP-címek (VIP) címek, **csak** a [belső virtuális hálózat módban](api-management-using-with-internal-vnet.md)érhető el, a hálózaton belül ről API Management végpontok - átjárók, a fejlesztői portál és a felügyeleti sík közvetlen API-hozzáférés. Ezeket a hálózaton belüli DNS-rekordok beállítására használhatja.

Mindkét típus címét láthatja az Azure Portalon és az API-hívás válaszában:

![API-kezelés a virtuális hálózat IP-címében](media/api-management-howto-ip-addresses/vnet-ip.png)


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

Az API Management nyilvános IP-címet használ a virtuális hálózaton kívüli kapcsolatokhoz, és egy privát IP-címet a virtuális hálózaton belüli kapcsolatokhoz.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>A Fogyasztási réteg API-kezelési szolgáltatásának IP-címei

Ha az API Management szolgáltatás egy felhasználási szint szolgáltatás, nem rendelkezik dedikált IP-címmel. A felhasználási szint szolgáltatás a megosztott infrastruktúrán fut, determinisztikus IP-cím nélkül. 

A forgalom korlátozása céljából használhatja az Azure-adatközpontok IP-címtartományát. A pontos lépéseket [az Azure Functions dokumentációs cikkében](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) találja.

## <a name="changes-to-the-ip-addresses"></a>Az IP-címek módosítása

Az API Management fejlesztői, alapszintű, standard és prémium szintű szintjein a nyilvános IP-címek (VIP) statikusak egy szolgáltatás élettartama alatt, a következő kivételekkel:

* A szolgáltatás törlődik, majd újra létre.
* A szolgáltatás-előfizetést [felfüggeszti](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) vagy [figyelmezteti](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (például nem fizetés esetén), majd visszaállítja.
* Az Azure virtuális hálózat hozzáadódik a szolgáltatáshoz, vagy törlődik a szolgáltatásból.
* Az API Management szolgáltatás a külső és a belső virtuális hálózat telepítési módja között vált.

A [többrégiós telepítéseknél](api-management-howto-deploy-multi-region.md)a regionális IP-cím megváltozik, ha egy régiót kiürít, majd visszaállít.
