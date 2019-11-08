---
title: Magánhálózati IP-cím használata belső útválasztáshoz egy bejövő végpont esetében
description: Ez a cikk azt ismerteti, hogyan használhatók a magánhálózati IP-címek a belső útválasztáshoz, és így a fürtön belüli bejövő végpontok a többi VNet legyenek kitéve.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795489"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Magánhálózati IP használata belső útválasztáshoz egy bejövő végpont esetében 

Ez a funkció lehetővé teszi, hogy a `Virtual Network` belül elérhetővé tegye a bejövő végpontot a magánhálózati IP-cím használatával.

## <a name="pre-requisites"></a>Előfeltételek  
Application Gateway [magánhálózati IP-konfigurációval](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

A vezérlőt kétféleképpen lehet úgy konfigurálni, hogy magánhálózati IP-címet használjon a bejövő forgalom számára.

## <a name="assign-to-a-particular-ingress"></a>Hozzárendelés egy adott bejövő forgalomhoz
Ha a magánhálózati IP-címekről szeretne beérkező adatokat közzétenni, használja a jegyzet [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) a bejövő adatok között.

### <a name="usage"></a>Használat
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

A magánhálózati IP-címek nélküli Application Gateway esetén a rendszer figyelmen kívül hagyja a `appgw.ingress.kubernetes.io/use-private-ip: "true"` Ingresses megjegyzéseit. Ezt a rendszer a bejövő események és a AGIC Pod naplójában fogja jelezni.

* Hiba a bejövő eseményben jelzett módon

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Hiba a AGIC-naplókban jelzett módon

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Globális hozzárendelés
Abban az esetben, ha a követelmény az, hogy az összes Ingresses a privát IP-címekre korlátozza, használja a `appgw.usePrivateIP: true` `helm` config.

### <a name="usage"></a>Használat
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Így a bejövő vezérlő szűri az IP-címek konfigurációit egy magánhálózati IP-címen, amikor a Application Gatewayon konfigurálja a előtér-figyelőket.
A AGIC pánikba kerül és összeomlik, ha `usePrivateIP: true`, és nincs hozzárendelve privát IP-cím.

> [!NOTE]
> Application Gateway v2 SKU-nak nyilvános IP-címet kell megadnia. Ha a Application Gateway magánjellegűnek kell lennie, csatoljon egy [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) a Application Gateway alhálózatához a forgalom korlátozásához.
