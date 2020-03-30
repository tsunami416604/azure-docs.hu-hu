---
title: 'Az Azure-tól a helyek közötti VPN-kapcsolatokhoz, amelyek nem tudnak csatlakozni a titleUtótaghoz: Azure VPN-átjáró'
description: Megtudhatja, hogy miként hárítható el a hirtelen leálló és nem újracsatlakoztatható helyek közötti VPN-kapcsolat.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 4e827c5f6eedc819bc3635cb09a28f65df51312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862577"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Hibaelhárítás: Az Azure-ból a helyek közötti VPN-kapcsolatok nem tudnak csatlakozni, és nem működik

Miután konfigurálta a helyszíni hálózat és az Azure virtuális hálózat közötti hely-hely VPN-kapcsolatot, a VPN-kapcsolat hirtelen leáll, és nem csatlakoztatható újra. Ez a cikk hibaelhárítási lépéseket tartalmaz a probléma megoldásához. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

A probléma megoldásához először próbálja [meg alaphelyzetbe állítani az Azure VPN-átjárót,](vpn-gateway-resetgw-classic.md) és állítsa alaphelyzetbe az alagutat a helyszíni VPN-eszközről. Ha a probléma továbbra is fennáll, kövesse az alábbi lépéseket a probléma okának azonosításához.

### <a name="prerequisite-step"></a>Előfeltételi lépés

Ellenőrizze az Azure VPN-átjáró típusát.

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)

2. A típusadatokat a VPN-átjáró **Áttekintés** lapján találja.
    
    ![Az átjáró áttekintése](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>1. lépés Annak ellenőrzése, hogy a helyszíni VPN-eszköz érvényesítve van-e

1. Ellenőrizze, hogy [érvényesített VPN-eszközt és operációs rendszerverziót](vpn-gateway-about-vpn-devices.md#devicetable)használ-e. Ha az eszköz nem érvényesített VPN-eszköz, előfordulhat, hogy kapcsolatba kell lépnie az eszköz gyártójával, hogy ellenőrizze, van-e kompatibilitási probléma.

2. Ellenőrizze, hogy a VPN-eszköz megfelelően van-e konfigurálva. További információt az [Eszközkonfigurációs minták szerkesztése című témakörben talál.](vpn-gateway-about-vpn-devices.md#editing)

### <a name="step-2-verify-the-shared-key"></a>2. lépés A megosztott kulcs ellenőrzése

Hasonlítsa össze a helyszíni VPN-eszköz megosztott kulcsát az Azure virtuális hálózati VPN-nel, és győződjön meg arról, hogy a kulcsok egyeznek.Compare the shared key for the on-premises VPN device to the Azure Virtual Network VPN to make sure that the keys match. 

Az Azure VPN-kapcsolat megosztott kulcsának megtekintéséhez használja az alábbi módszerek egyikét:

**Azure-portál**

1. Nyissa meg a létrehozott VPN-átjáró hely-hely kapcsolatát.

2. A **Beállítások csoportban** kattintson a **Megosztott kulcs gombra.**
    
    ![Megosztott kulcs](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az Azure Resource Manager telepítési modelljéhez:

    Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

A klasszikus üzembe helyezési modell hez:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>3. lépés A VPN-társ IP-szolgáltatóinak ellenőrzése

-   Az Azure-beli **helyi hálózati átjáró** objektum IP-definíciójának meg kell egyeznie a helyszíni eszköz IP-címével.
-   A helyszíni eszközön beállított Azure-átjáró IP-definíciójának meg kell egyeznie az Azure-átjáró IP-címével.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>4. lépés UDR- és NSG-k ellenőrzése az átjáró alhálózatán

Ellenőrizze és távolítsa el a felhasználó által definiált útválasztást (UDR) vagy hálózati biztonsági csoportokat (NSG) az átjáró alhálózatán, majd tesztelje az eredményt. Ha a probléma megoldódott, ellenőrizze az UDR vagy az NSG által alkalmazott beállításokat.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>5. lépés A helyszíni VPN-eszköz külső összeköttetésének ellenőrzése

- Ha a VPN-eszköz internetre néző IP-címe szerepel az Azure **helyi hálózati** definíciójában, előfordulhat, hogy szórványos kapcsolatbontást tapasztal.
- Az eszköz külső felületének közvetlenül az interneten kell lennie. Az internet és az eszköz között nem lehet hálózati címfordítás vagy tűzfal.
- Ha a tűzfalfürtöt virtuális IP-címre szeretné beállítani, meg kell szakítania a fürtöt, és a VPN-készüléket közvetlenül egy nyilvános felületnek kell elérhetővé tennie, amelyhez az átjáró kapcsolatba léphet.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>6. lépés Ellenőrizze, hogy az alhálózatok pontosan egyeznek-e (Azure-szabályzatalapú átjárók)

-   Ellenőrizze, hogy a virtuális hálózati címterület(ek) pontosan egyeznek-e az Azure virtuális hálózat és a helyszíni definíciók között.
-   Ellenőrizze, hogy az alhálózatok pontosan egyeznek-e a **helyi hálózati átjáró** és a helyszíni hálózat helyszíni definíciói között.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>7. lépés Az Azure-átjáró állapotvizsgálatának ellenőrzése

1. Nyissa meg az állapotmintát a következő URL-címen való böngészéssel:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Kattintson a tanúsítványfigyelmeztetésen.
3. Ha választ kap, a VPN-átjáró kifogástalan állapotúnak minősül. Ha nem kap választ, előfordulhat, hogy az átjáró nem kifogástalan állapotú, vagy egy NSG az átjáró alhálózaton okozza a problémát. A következő szöveg egy mintaválasz:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>8. lépés Annak ellenőrzése, hogy a helyszíni VPN-eszköz rendelkezik-e a tökéletes továbbítási titkossági funkcióval

A tökéletes továbbítási titkossági funkció lekapcsolási problémákat okozhat. Ha a VPN-eszköz teljes továbbítási titkosság engedélyezve van, tiltsa le a funkciót. Ezután frissítse a VPN-átjáró IPsec-házirendet.

## <a name="next-steps"></a>További lépések

-   [Helyek közötti kapcsolat konfigurálása virtuális hálózattal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [IPsec/IKE-házirend konfigurálása helyek közötti VPN-kapcsolatokhoz](vpn-gateway-ipsecikepolicy-rm-powershell.md)
