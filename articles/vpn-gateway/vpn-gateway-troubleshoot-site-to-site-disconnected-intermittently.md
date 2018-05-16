---
title: Hibaelhárítás az Azure-webhelyek közötti VPN időnként megszakad |} Microsoft Docs
description: Megtudhatja, hogyan, amelyben a telephelyek közötti VPN-kapcsolat a megszakítja rendszeresen a probléma elhárításához.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 9c827469080195054d4ff70ab72fc123365a73df
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Hibaelhárítás: Az időnként megszakad az Azure--webhelyek közötti VPN

A probléma, hogy egy új vagy meglévő Microsoft Azure-webhelyek VPN-kapcsolat nincs stabil-e, vagy leválik rendszeresen Ön is szembesülhet. Ez a cikk ismerteti lépések segítségével meghatározhatja, és oldja meg a probléma okának elhárítása. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

### <a name="prerequisite-step"></a>Előfeltétel-ellenőrzési lépés

Ellenőrizze az Azure virtuális hálózati átjáró típusát:

1. Ugrás a [Azure-portálon](https://portal.azure.com).
2. Ellenőrizze a **áttekintése** a típussal kapcsolatos információk a virtuális hálózati átjáró oldalán.
    
    ![Az átjáró áttekintése](media\vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Lépés 1. Ellenőrizze, hogy a helyszíni VPN-eszköz van hitelesítve

1. Ellenőrizze, hogy használ-e egy [érvényesíteni a VPN-eszköz és az operációs rendszer verziója](vpn-gateway-about-vpn-devices.md#devicetable). Ha a VPN-eszköz nincs érvényesítve, előfordulhat, ha bármely kompatibilitási probléma van az eszköz gyártójától.
2. Győződjön meg arról, hogy a VPN-eszköz megfelelően van-e beállítva. További információkért lásd: [eszköz konfigurációs minták szerkesztése](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>2. lépés (Csoportházirend-alapú Azure virtuális hálózati átjárók) biztonsági társítás beállításainak ellenőrzése

1. Győződjön meg arról, hogy a virtuális hálózat, alhálózatok és a tartományokat a **helyi hálózati átjáró** definíciót a Microsoft Azure-ban ugyanaz, mint a konfigurációt a helyszíni VPN-eszközön.
2. Ellenőrizze, hogy megfelel-e a biztonsági társítás beállításait.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>3. lépés-ellenőrzése átjáró-alhálózatot a felhasználó által definiált útvonalak és hálózati biztonsági csoportok

Az átjáró alhálózatának felhasználói útvonalon néhány forgalom korlátozása és egyéb forgalom engedélyezéséhez történhet. Így megjelenik, hogy a VPN-kapcsolat megbízhatatlanná néhány adatforgalomhoz és mások helyes. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>4. lépés ellenőrizze a "egy VPN-alagút egy alhálózat pár" beállítást (a csoportházirend-alapú virtuális hálózati átjárók)

Győződjön meg arról, hogy rendelkezik a helyszíni VPN-eszköz beállításai **egy VPN-alagút egy alhálózat pár** csoportházirend-alapú virtuális hálózati átjárók.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>5. lépés (a csoportházirend-alapú virtuális hálózati átjárók) a biztonsági társítás korlátozás-ellenőrzés

A csoportházirend-alapú virtuális hálózati átjáró legfeljebb 200 alhálózati biztonsági társítás párok rendelkezik. Ha az Azure-beli virtuális hálózat alhálózatok száma meg kell szorozni helyi alhálózatok számú alkalommal érték nagyobb, mint 200 úgy, hogy időnként alhálózatok leválasztása.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>6. lépés ellenőrizze a helyi VPN-eszköz kapcsolat külső címét

- Ha az internetes IP-címe a VPN-eszköz szerepel-e a a **helyi hálózati átjáró** definition Azure, szórványos kapcsolat megszakadása tapasztalhatja.
- Az eszköz külső adapter közvetlenül az interneten kell lennie. Egyetlen hálózati címfordítás (NAT) vagy az interneten és az eszköz között tűzfal kell.
-  Ha konfigurálja a tűzfal a fürt rendelkezik egy virtuális IP-cím, akkor a fürt megszüntetése, és teszi közzé a VPN-készülék közvetlenül a egy nyilvános felületet, amely az átjáró úgy csatlakozhatnak a.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Lépés 7 ellenőrizze, hogy a helyszíni VPN-eszköz teljes továbbítási titkosítása engedélyezve van

A **teljes továbbítási titkosítása** szolgáltatás a leválasztás problémákat okozhat. Ha a VPN-eszköz **továbbítását tökéletes** engedélyezve van, tiltsa le a szolgáltatást. Majd [a virtuális hálózati átjáró IPSec-házirend frissítése](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>További lépések

- [A virtuális hálózati helyek kapcsolat beállítása](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Telephelyek közötti VPN-kapcsolatok IPsec/IKE-házirend konfigurálása](vpn-gateway-ipsecikepolicy-rm-powershell.md)

