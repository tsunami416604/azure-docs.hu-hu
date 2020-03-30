---
title: Privát IP-cím használata belső útválasztáshoz egy kimenő végponthoz
description: Ez a cikk arról nyújt tájékoztatást, hogyan használhatja a privát IP-címeket a belső útválasztáshoz, és így a fürtön belüli be- és kimenő végpontot a virtuális hálózat többi része számára.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795489"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Privát IP-cím használata belső útválasztáshoz egy be- és előtér-végponthoz 

Ez a funkció lehetővé teszi a be- és adható végpont egy `Virtual Network` privát IP használatával.

## <a name="pre-requisites"></a>Előfeltételek  
Alkalmazásátjáró [privát IP-konfigurációval](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

Kétféleképpen konfigurálhatja a vezérlőt a privát IP-cím használatára a

## <a name="assign-to-a-particular-ingress"></a>Hozzárendelés egy adott be- és be- és be- és visszakvagyathoz
Egy adott privát IP-cím re vonatkozó bejövő [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) adatok ki, használja a jegyzet a bejövő adatokat.

### <a name="usage"></a>Használat
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Privát IP-cím nélküli alkalmazásátjárók esetén a program figyelmen kívül hagyja a jegyzetekkel `appgw.ingress.kubernetes.io/use-private-ip: "true"` ellátott adatokat. Ez a bejövő eseményekben és az AGIC pod logban jelenik meg.

* Hiba a bejövő támadások eseményében jelzettek szerint

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Hiba az AGIC naplókban jelzettek szerint

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Hozzárendelés globálisan
Abban az esetben, követelmény, hogy korlátozza az összes bejövő, hogy `appgw.usePrivateIP: true` `helm` ki vannak téve a privát IP, használja a config.

### <a name="usage"></a>Használat
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Ez teszi a be- ésres-vezérlő szűrő az IP-cím konfigurációk egy privát IP-cím konfigurálásakor az előtér-figyelők az Application Gateway.
AGIC pánik és `usePrivateIP: true` összeomlik, ha nem privát IP van rendelve.

> [!NOTE]
> Az Application Gateway v2 termékváltozat nyilvános IP-címet igényel. Ha az Application Gateway-nek magánjellegűnek kell lennie, csatoljon egy [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) alkalmazást az Application Gateway alhálózatához a forgalom korlátozása érdekében.
