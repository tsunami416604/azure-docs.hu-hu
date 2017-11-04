---
title: "Egy Azure-webhelyek VPN-kapcsolatot, amelyek nem kapcsolódnak hibaelhárítása |} Microsoft Docs"
description: "Ismerje meg, amely hirtelen leáll, és nem kell csatlakoztatni a pont-pont VPN-kapcsolat hibaelhárítása."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: genli
ms.openlocfilehash: 55cfba5e9730b123bba20dfdc5d10c1157352a35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Hibáinak elhárítása: Egy Azure-webhelyek VPN-kapcsolatot nem lehet kapcsolódni, és nem működik

Miután konfigurált egy a helyszíni hálózat és az Azure virtuális hálózat közötti pont-pont VPN kapcsolatot, a VPN-kapcsolat hirtelen leáll, és nem kell csatlakoztatni. Ez a cikk nyújt hibaelhárítási segítséget nyújtanak a probléma megoldásához. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

A probléma megoldása érdekében először próbálja meg [alaphelyzetbe állítani az Azure VPN gateway](vpn-gateway-resetgw-classic.md) , és állítsa alaphelyzetbe az alagutat a helyszíni VPN-eszközön. Ha a probléma továbbra is fennáll, kövesse az alábbi lépéseket a probléma okának megállapításához.

### <a name="prerequisite-step"></a>Előfeltétel-ellenőrzési lépés

Ellenőrizze az Azure VPN gateway típusú.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

2. Ellenőrizze a **áttekintése** a típus információ a VPN-átjáró oldalán.
    
    ![Az átjáró – áttekintés](media\vpn-gateway-troubleshoot-site-to-site-cannot-connect\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>1. lépés Ellenőrizze, hogy a rendszer érvényesíti a helyszíni VPN-eszköz

1. Ellenőrizze, hogy használ-e egy [érvényesíteni a VPN-eszköz és az operációs rendszer verziója](vpn-gateway-about-vpn-devices.md#devicetable). Ha az eszköz nem ellenőrzött VPN-eszközhöz, lehetséges, hogy az eszköz gyártójától kompatibilitási probléma van.

2. Győződjön meg arról, hogy a VPN-eszköz megfelelően van-e beállítva. További információkért lásd: [eszköz konfigurációs minták szerkesztése](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>2. lépés A megosztott kulcs ellenőrzése

Hasonlítsa össze a helyszíni VPN-eszköz az Azure virtuális hálózat VPN győződjön meg arról, hogy a kulcsok egyezik-e a megosztott kulcsot. 

A megosztott kulcsot az Azure VPN-kapcsolathoz megtekintéséhez használja a következő módszerek egyikét:

**Azure Portal**

1. Nyissa meg a VPN-átjáró webhelyek kapcsolatnak, létrehozott.

2. Az a **beállítások** kattintson **megosztott kulcs**.
    
    ![Megosztott kulcs](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

Az Azure Resource Manager telepítési modell:

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

A klasszikus üzembe helyezési modell:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>3. lépés A VPN-társ IP-cím ellenőrzése

-   Az IP-definíciójában a **helyi hálózati átjáró** objektum az Azure-ban meg kell felelnie az helyszíni eszköz IP-cím.
-   Az Azure átjáró IP-definíciót, amely a helyi eszközön értéke meg kell felelnie az Azure átjáró IP-cím.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>4. lépés Az átjáró alhálózatának UDR és NSG-k ellenőrzése

Ellenőrizze a felhasználó által definiált útválasztási (UDR) vagy a hálózati biztonsági csoportokkal (NSG-k) eltávolítása az átjáró-alhálózat és tesztelje az eredmény. Ha a probléma megoldódott, ellenőrizze a UDR vagy NSG alkalmazott beállítások.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>5. lépés Ellenőrizze a helyszíni VPN-eszköz külső adapterén címét

- Ha a VPN-eszköz Internet felé néző IP-címe is szerepel a **helyi hálózati** definition Azure-ban az Ön is szembesülhet szórványos kapcsolat megszakadása.
- Az eszköz külső adapter közvetlenül az interneten kell lennie. Egyetlen hálózati címfordítás vagy az interneten és az eszköz között tűzfal kell.
- A fürt rendelkezik egy virtuális IP-cím tűzfal konfigurálása, a fürt megszüntetése, és teszi közzé a VPN-készülék közvetlenül a egy nyilvános felületet, amely az átjáró úgy csatlakozhatnak a.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>6. lépés Győződjön meg arról, hogy az alhálózatok pontosan megegyezik-e (az Azure csoportházirend-alapú átjárók)

-   Ellenőrizze, hogy megfelel-e az alhálózatok pontosan Azure-beli virtuális hálózat és a helyszíni definíciókat az Azure virtuális hálózat között.
-   Ellenőrizze, hogy az alhálózatok közötti pontosan egyezik-e a **helyi hálózati átjáró** és a helyszíni a helyszíni hálózat definíciói.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>7. lépés Az Azure átjáró állapotmintáihoz ellenőrzése

1. Lépjen a [állapotmintáihoz](https://&lt;YourVirtualNetworkGatewayIP&gt;:8081/healthprobe).

2. Kattintson a tanúsítványfigyelmeztetésre.
3. Ha választ kap, a VPN-átjáró megfelelő minősül. Ha nem érkezik válasz, az átjáró nem lesz kifogástalan, vagy az átjáró-alhálózat egy NSG okozza a problémát. A következő mintát választ áll:

    &lt;? XML-verzió = "1.0"? > <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">elsődleges példány: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6 < / karakterlánc&gt;

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>8. lépés. Ellenőrizze, hogy rendelkezik-e a PFS-szolgáltatás engedélyezve van a helyszíni VPN-eszköz

A PFS szolgáltatás kapcsolatbontás problémákat okozhat. Ha a VPN-eszköz PFS engedélyezve van, tiltsa le a szolgáltatást. Frissítse a VPN gateway IPSec-házirend.

## <a name="next-steps"></a>Következő lépések

-   [A virtuális hálózati helyek kapcsolat beállítása](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Pont-pont a VPN-kapcsolatok IPsec/IKE-szabályzat beállítása](vpn-gateway-ipsecikepolicy-rm-powershell.md)
