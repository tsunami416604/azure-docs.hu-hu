---
title: Hibaelhárítása az Azure site-to-site VPN-kapcsolat, amely nem lehet csatlakoztatni |} A Microsoft Docs
description: Ismerje meg, hogy hirtelen leáll, és nem lehet újra csatlakozni site-to-site VPN-kapcsolat hibaelhárítása.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 18900c4a1dbc25526a3f60c7410ad87e7dd9a9fa
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507299"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Hibaelhárítás: Az Azure site-to-site VPN-kapcsolat nem lehet csatlakozni, és nem működik

Miután konfigurálta a site-to-site VPN-kapcsolatot egy helyszíni hálózat és a egy Azure virtuális hálózat között, a VPN-kapcsolat hirtelen leáll, és nem lehet újra csatlakozni. Ez a cikk segítséget nyújt a probléma megoldásához hibaelhárítási lépéseket. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

A probléma megoldása érdekében először próbálja meg [az Azure VPN gateway alaphelyzetbe állítása](vpn-gateway-resetgw-classic.md) és az alagutat a helyszíni VPN-eszköz alaphelyzetbe állítása. Ha a probléma nem szűnik meg, kövesse az alábbi lépéseket a probléma okának azonosításához.

### <a name="prerequisite-step"></a>Előfeltétel-ellenőrzési lépés

Ellenőrizze az Azure VPN gateway típusa.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

2. Ellenőrizze a **áttekintése** a típussal kapcsolatos információk a VPN-átjáró oldalán.
    
    ![Az átjáró – áttekintés](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>1. lépés Ellenőrizze, hogy a rendszer érvényesíti a helyszíni VPN-eszköz

1. Ellenőrizze, hogy használ-e egy [ellenőrzött VPN-eszköz és az operációs rendszer verziója](vpn-gateway-about-vpn-devices.md#devicetable). Ha az eszköz nem ellenőrzött VPN-eszközt, akkor előfordulhat, hogy lépjen kapcsolatba az eszköze gyártójával e kompatibilitási probléma.

2. Győződjön meg arról, hogy a VPN-eszköz megfelelően van-e konfigurálva. További információkért lásd: [az eszköz konfigurációs mintáinak szerkesztése](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>2. lépés A megosztott kulcs ellenőrzése

Hasonlítsa össze a helyszíni VPN-eszközét, az Azure Virtual Network VPN, győződjön meg arról, hogy a kulcsok egyeznek, a megosztott kulcsot. 

Az Azure VPN-kapcsolat megosztott kulcs megtekintéséhez használja a következő módszerek egyikét:

**Azure Portal**

1. Nyissa meg a VPN gateway-helyek közötti kapcsolat, amelyet Ön hozott létre.

2. Az a **beállítások** területén kattintson **megosztott kulcs**.
    
    ![Megosztott kulcs](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

Az Azure Resource Manager üzembe helyezési modelljének:

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

A klasszikus üzemi modell:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>3. lépés Ellenőrizze a VPN-társ IP-címek

-   Az IP-definíció a a **helyi hálózati átjáró** objektumot az Azure-ban meg kell egyeznie a helyszíni eszközök IP-cím.
-   Az Azure-átjáró IP-definíciót, amely a helyi eszközön van beállítva, meg kell egyeznie a Azure-átjáró IP-cím.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>4. lépés Az átjáró alhálózatán található UDR és NSG-kkel ellenőrizze

Keressen, és távolítsa el a felhasználó által meghatározott útválasztás (UDR) vagy a hálózati biztonsági csoportok (NSG-k) az átjáró-alhálózat, majd ellenőrizze az eredményt. Ha a probléma megoldódott, ellenőrizze a beállításokat, az URD vagy az NSG-t a alkalmazni.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>5. lépés Ellenőrizze a helyszíni VPN-eszköz külső adapter címe

- Ha az Internet felé néző IP-cím a VPN-eszköz szerepel a **helyi hálózati** definíciója az Azure-ban, időnként szétkapcsolások tapasztalhat.
- Az eszköz külső adapternek kell lennie, közvetlenül az interneten. Nincs hálózati címfordítás vagy a tűzfal az interneten és az eszköz közötti lehet.
- Konfigurálja a tűzfalat, hogy rendelkezik egy virtuális IP-címet a fürtözés, a fürt megszüntetése, és tegye elérhetővé a VPN-berendezés közvetlenül, egy nyilvános felülete, amely az átjáró úgy csatlakozhatnak a.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>6. lépés Ellenőrizze, hogy az alhálózatok pontosan egyezik-e (az Azure házirendalapú átjárók)

-   Győződjön meg arról, hogy a virtuális hálózati címét (tárolóhelyeit) felel meg pontosan az Azure virtuális hálózat és a helyszíni definíciók között.
-   Győződjön meg arról, hogy az alhálózatok közötti pontosan egyezik-e a **helyi hálózati átjáró** és a helyszíni a helyszíni hálózat definíciói.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>7. lépés Ellenőrizze az Azure-átjáró állapotadat-mintavétel

1. Nyissa meg az állapotfigyelő mintavételező keresse a következő URL-címet:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Kattintson végig a tanúsítványfigyelmeztetésre.
3. Ha választ kap, a VPN-átjáró megfelelő számít. Ha nem kap választ, az átjáró nem feltétlenül működik megfelelően, vagy az átjáró-alhálózatot a hálózati biztonsági csoportok okozza a problémát. A következő szöveg egy mintaválasz:

    &lt;?xml version="1.0"?>  <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6 < / string&gt;

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>8. lépés Ellenőrizze, hogy a helyszíni VPN-eszköz rendelkezik-e a titkosságvédelem szolgáltatás engedélyezve van

A titkosságvédelem funkció leválasztásának problémákat okozhat. Ha a VPN-eszköz titkosságvédelem engedélyezve van, a funkció letiltásához. Frissítse a VPN gateway IPsec-házirend.

## <a name="next-steps"></a>További lépések

-   [Virtuális hálózat és egy helyek közötti kapcsolat konfigurálása](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Site-to-site VPN-kapcsolatok egy IPsec/IKE-házirend konfigurálása](vpn-gateway-ipsecikepolicy-rm-powershell.md)
