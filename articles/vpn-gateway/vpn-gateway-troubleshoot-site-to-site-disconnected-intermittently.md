---
title: Hibaelhárítás az Azure Site-to-Site VPN időnként megszakad |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatja el a problémát, amelyben a Site-to-Site VPN-kapcsolatot a megszakítja rendszeresen.
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
ms.openlocfilehash: 9d831f7f08af174dfc6ce429da4d0a0daaeda0e9
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240018"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Hibaelhárítás: Az Azure Site-to-Site VPN időnként megszakad

A probléma, hogy egy új vagy meglévő Microsoft Azure Site-to-Site VPN-kapcsolat nem stabil, vagy megszakad rendszeresen tapasztalhat. Ez a cikk ismerteti a lépéseket, és oldja meg a probléma okának elhárítása. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

### <a name="prerequisite-step"></a>Előfeltétel-ellenőrzési lépés

Ellenőrizze az Azure virtuális hálózati átjáró típusa:

1. Lépjen a [az Azure portal](https://portal.azure.com).
2. Ellenőrizze a **áttekintése** lapján a virtuális hálózati átjárót a típussal kapcsolatos információk.
    
    ![Az átjáró áttekintése](media\vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>1. Ellenőrizze. lépés: a helyszíni VPN-eszköz érvényesítve van-e

1. Ellenőrizze, hogy használ-e egy [ellenőrzött VPN-eszköz és az operációs rendszer verziója](vpn-gateway-about-vpn-devices.md#devicetable). Ha a VPN-eszköz nem érvényesíti, előfordulhat, hogy van-e bármelyik kompatibilitási probléma az eszköz gyártója.
2. Győződjön meg arról, hogy a VPN-eszköz megfelelően van-e konfigurálva. További információkért lásd: [eszköz konfigurációs mintáinak szerkesztése](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>2. lépés (házirend-alapú Azure virtuális hálózati átjárók) biztonsági társítás beállításainak ellenőrzése

1. Győződjön meg arról, hogy a virtuális hálózat, alhálózatok és a tartományokat a **helyi hálózati átjáró** definíciója a Microsoft Azure-ban is ugyanaz, mint a konfigurációt a helyszíni VPN-eszköz.
2. Győződjön meg arról, hogy a biztonsági társítás beállításai megfelelnek-e.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>3. lépés ellenőrzése a felhasználó által megadott útvonalak és hálózati biztonsági csoportok átjáró-alhálózat

Előfordulhat, hogy egy felhasználó által megadott útvonal, az átjáró-alhálózat korlátozása a forgalom egy része és egyéb forgalom engedélyezéséhez. Így jelenik meg, hogy a VPN-kapcsolat-e a forgalom egy része a megbízhatatlan és mások számára hasznos. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>4. lépés ellenőrizze a "egy VPN-alagút egy alhálózat pár" (a szabályzatalapú virtuális hálózati átjárók) beállítása

Győződjön meg arról, hogy rendelkezik a helyszíni VPN-eszköz értéke **egy pár alhálózatot egy VPN-alagút** szabályzatalapú virtuális hálózati átjárók esetében.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>5. lépés-ellenőrzés biztonsági társítás korlátozás (a szabályzatalapú virtuális hálózati átjárók)

A szabályzatalapú virtuális hálózati átjáró korlátja 200 alhálózat biztonsági társítás párok. Ha az Azure virtual network alhálózatok számát szorozva időpontok helyi alhálózatok száma nagyobb, mint 200, és láthatja, időnként alhálózat leválasztása.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>6. lépés – ellenőrizze a helyszíni VPN-eszköz külső adapter címe

- Ha az internetkapcsolattal rendelkező IP-cím a VPN-eszköz szerepel a **helyi hálózati átjáró** definíciója az Azure-ban, időnként szétkapcsolások tapasztalhat.
- Az eszköz külső adapternek kell lennie, közvetlenül az interneten. Nincs hálózati címfordítás (NAT) vagy a tűzfal az interneten és az eszköz közötti lehet.
-  Konfigurálja a tűzfalat a fürt rendelkezik egy virtuális IP-címet, ha kell, a fürt megszüntetése, és tegye elérhetővé a VPN-berendezés közvetlenül, egy nyilvános felülete, amely az átjáró úgy csatlakozhatnak a.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Ellenőrzés 7. lépés: a helyszíni VPN-eszköz teljes továbbítási titkosítása engedélyezve van-e

A **teljes továbbítási titkosítása** funkció a leválasztás problémákat okozhat. Ha a VPN-eszköz **továbbítását tökéletes** engedélyezve van, a funkció letiltásához. Ezután [a virtuális hálózati átjárót az IPSec-házirend](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>További lépések

- [Virtuális hálózat és egy helyek közötti kapcsolat konfigurálása](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Site-to-Site VPN-kapcsolat IPsec/IKE-házirend konfigurálása](vpn-gateway-ipsecikepolicy-rm-powershell.md)

