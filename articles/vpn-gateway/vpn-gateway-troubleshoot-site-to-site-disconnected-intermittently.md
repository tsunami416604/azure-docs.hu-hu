---
title: Az Azure helyek közötti VPN időnként megszakad– problémamegoldása
description: Útmutató a helyről helyekre irányuló VPN-kapcsolat rendszeres leszakadásával kapcsolatos probléma elhárításához.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 82054099a5a496e99c49135ab98ee1163af19784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862560"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Hibaelhárítás: Az Azure helyek közötti VPN időnként bontja a kapcsolatot

Előfordulhat, hogy az a probléma jelentkezik, hogy egy új vagy meglévő Microsoft Azure-helyek közötti VPN-kapcsolat nem stabil, vagy rendszeresen bontja a kapcsolatot. Ez a cikk a probléma okának azonosítását és megoldását segítő lépésekkel elhárítására szolgál. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

### <a name="prerequisite-step"></a>Előfeltételi lépés

Ellenőrizze az Azure virtuális hálózati átjáró típusát:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. A típusadatokért ellenőrizze a virtuális hálózati átjáró **Áttekintés** lapját.
    
    ![Az átjáró áttekintése](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>1. lépés Annak ellenőrzése, hogy a helyszíni VPN-eszköz érvényesítve van-e

1. Ellenőrizze, hogy [érvényesített VPN-eszközt és operációs rendszerverziót](vpn-gateway-about-vpn-devices.md#devicetable)használ-e. Ha a VPN-eszköz nincs érvényesítve, előfordulhat, hogy kapcsolatba kell lépnie az eszköz gyártójával, hogy ellenőrizze, van-e kompatibilitási probléma.
2. Ellenőrizze, hogy a VPN-eszköz megfelelően van-e konfigurálva. További információt az [Eszközkonfigurációs minták szerkesztése című témakörben talál.](vpn-gateway-about-vpn-devices.md#editing)

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>2. lépés A biztonsági társítás beállításainak ellenőrzése (házirendalapú Azure virtuális hálózati átjárók esetén)

1. Győződjön meg arról, hogy a virtuális hálózat, az alhálózatok és a Microsoft Azure **helyi hálózati átjáródefiníciójának** tartományai megegyeznek a helyszíni VPN-eszköz konfigurációjával.
2. Ellenőrizze, hogy a Biztonsági társítás beállításai egyeznek-e.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>3. lépés A felhasználó által definiált útvonalak vagy hálózati biztonsági csoportok ellenőrzése az átjáróalhálózaton

Előfordulhat, hogy az átjáró alhálózatán lévő felhasználó által definiált útvonal korlátozza a forgalmat, és engedélyezi a többi forgalmat. Ez azt a látszatot teszi, hogy a VPN-kapcsolat megbízhatatlan bizonyos forgalom és jó mások számára. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>4. lépés Ellenőrizze az "alhálózati páronként egy VPN-alagút" beállítást (házirendalapú virtuális hálózati átjárók esetén)

Győződjön meg arról, hogy a helyszíni VPN-eszköz van beállítva, hogy **egy VPN-alagút alhálózati páronként** a házirend-alapú virtuális hálózati átjárók.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>5. lépés A biztonsági társításkorlátozás ellenőrzése (házirendalapú virtuális hálózati átjárók esetén)

A házirend-alapú virtuális hálózati átjáró legfeljebb 200 alhálózati biztonsági társítás pár. Ha az Azure virtuális hálózati alhálózatok száma szorozva a helyi alhálózatok száma nagyobb, mint 200, látható, hogy szórványos alhálózatok leválasztása.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>6. lépés A helyszíni VPN-eszköz külső összeköttetésének ellenőrzése

- Ha a VPN-eszköz internetfelé néző IP-címe szerepel az Azure **helyi hálózati átjáródefiníciójában,** szórványos kapcsolatbontást tapasztalhat.
- Az eszköz külső felületének közvetlenül az interneten kell lennie. Az internet és az eszköz között nem lehet hálózati címfordítás (NAT) vagy tűzfal.
-  Ha a tűzfalfürt ötös IP-címre állítja be a fürtöt, meg kell szakítania a fürtöt, és a VPN-készüléket közvetlenül egy nyilvános felületnek kell elérhetővé tennie, amelyhez az átjáró kapcsolatba léphet.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>7. lépés Annak ellenőrzése, hogy a helyszíni VPN-eszköz rendelkezik-e a tökéletes továbbítási titkosság engedélyezve

A **tökéletes továbbítási titkosság** szolgáltatás a lekapcsolási problémákat okozhatja. Ha a VPN-eszköznél engedélyezve van a **Perfect forward Secretcy** funkció, tiltsa le a funkciót. Ezután [frissítse a virtuális hálózati átjáró IPsec-házirendet.](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy)

## <a name="next-steps"></a>További lépések

- [Helyek közötti kapcsolat konfigurálása virtuális hálózattal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [IPsec/IKE-házirend konfigurálása helyek közötti VPN-kapcsolatokhoz](vpn-gateway-ipsecikepolicy-rm-powershell.md)

