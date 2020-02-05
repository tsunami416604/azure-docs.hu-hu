---
title: Azure VMware Solutions (AVS) – hálózati ellenőrzőlista
description: Ellenőrzőlista az Azure VMware-megoldás hálózati CIDR AVS-vel való lefoglalásához
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5cbb0fc0514c17fe34f8e57806e6620cfa8b3f68
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025010"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-avs"></a>Az Azure VMware-megoldás hálózatkezelési előfeltételei az AVS használatával

Az Azure VMware Solutions (AVS) olyan VMware Private Cloud Environment-környezetet kínál, amely elérhető a helyszíni környezetek, a nagyvállalati eszközök és az Azure-erőforrások felhasználói és alkalmazásai számára. A kapcsolat olyan hálózati szolgáltatásokon keresztül történik, mint a VPN-EK és az Azure ExpressRoute-kapcsolatok. Néhány hálózati szolgáltatáshoz meg kell adnia a szolgáltatások engedélyezéséhez szükséges hálózati címtartományt. 

A cikkben szereplő táblázatok a címtartományok és a megadott címeket használó kapcsolódó szolgáltatások leírását ismertetik. Néhány cím megadása kötelező, és néhány a telepíteni kívánt szolgáltatástól függ. Ezek a Címterület nem fedik át egymást a helyszíni alhálózatok, az Azure Virtual Network alhálózatok vagy a tervezett AVS munkaterhelés-alhálózatok között.

## <a name="network-address-ranges-required-for-creating-an-avs-private-cloud"></a>Az AVS Private Cloud létrehozásához szükséges hálózati címtartományok

Az AVS szolgáltatás és az AVS Private Cloud létrehozása során be kell tartania a megadott hálózati osztály nélküli tartományok közötti útválasztási (CIDR) tartományokat a következők szerint.

| Név/használat a következőhöz:     | Leírás                                                                                                                            | Címtartomány            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Átjáró CIDR      | Az Edge-szolgáltatásokhoz (VPN-átjárók) szükséges. Ez a CIDR az AVS szolgáltatás létrehozásakor szükséges, és az RFC 1918 területről kell származnia. | /28                      |
| vSphere/vSAN CIDR | A VMware felügyeleti hálózatokhoz szükséges. Ezt a CIDR meg kell adni az AVS Private Cloud létrehozásakor.                                    | /24 vagy/23 vagy/22 vagy/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Egy helyszíni hálózathoz való Azure-beli hálózati kapcsolódáshoz szükséges hálózati címtartomány

[A helyszíni hálózatról az AVS Private Cloud Network ExpressRoute](on-premises-connection.md) -kapcsolaton keresztüli csatlakoztatása Global REACH kapcsolatot létesít. A kapcsolat Border Gateway Protocol (BGP) protokollt használ a helyszíni hálózat, az AVS Private Cloud Network és az Azure-hálózatok közötti útvonalak cseréjéhez.

| Név/használat a következőhöz:             | Leírás                                                                                                                                                                             | Címtartomány |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute peering CIDR | A helyszíni kapcsolat ExpressRoute-Global Reach használata esetén szükséges. Ezt a CIDR akkor kell megadni, ha egy Global Reach kapcsolódási kérelem egy támogatási jegyen keresztül történik. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>A helyek közötti VPN-kapcsolat helyszíni hálózathoz való használatának szükséges hálózati címtartomány

A helyek közötti VPN-kapcsolattal a helyszíni [hálózatról az AVS Private Cloud Network szolgáltatáshoz](vpn-gateway.md) a következő IP-címek, helyszíni hálózatok és azonosítók szükségesek. 

| Címek/címtartomány | Leírás                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Társ IP-címe               | Helyszíni VPN-átjáró nyilvános IP-címe. Helyek közötti VPN-kapcsolat létesítéséhez szükséges egy helyszíni adatközpont és az AVS szolgáltatási régiója között. Ez az IP-cím szükséges a helyek közötti VPN-átjáró létrehozásakor.                                         |
| Társ-azonosító       | A helyszíni VPN-átjáró társ-azonosítója. Ez általában ugyanaz, mint a **társ IP-címe**.  Ha a helyszíni VPN-átjárón egyedi azonosító van megadva, az azonosítót meg kell adni.  A társ-azonosító megadása kötelező a helyek közötti VPN-átjáró létrehozásakor.   |
| Helyszíni hálózatok   | A régióban elérhető AVS-hálózatokat igénylő helyszíni előtagok.  Minden olyan előtagok belefoglalása egy helyszíni hálózatból, amely hozzáfér az AVS-hálózathoz, beleértve az ügyfél hálózatát, ahonnan a felhasználók hozzáférnek a hálózathoz.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>A pont – hely VPN-kapcsolatok használatához szükséges hálózati címtartomány

A pont – hely típusú VPN-kapcsolat lehetővé teszi, hogy egy ügyfélgépről hozzáférjen az AVS-hálózathoz. [Pont – hely típusú VPN beállításához](vpn-gateway.md)a következő hálózati címtartományt kell megadnia.

| Címek/címtartomány | Leírás                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ügyfél-alhálózat         | A DHCP-címeket az ügyfél alhálózata teszi elérhetővé, ha pont – hely típusú VPN-kapcsolaton keresztül csatlakozik. Erre az alhálózatra akkor van szükség, amikor pont – hely típusú VPN-átjárót hoz létre egy AVS-portálon. A hálózat két alhálózatra oszlik, egyet az UDP-kapcsolathoz, a másikat pedig a TCP-kapcsolatokhoz. |

## <a name="next-steps"></a>Következő lépések

* [Helyszíni tűzfal beállítása az AVS saját felhőhöz való hozzáféréshez](on-premises-firewall-configuration.md)
* [Rövid útmutató – AVS-szolgáltatás létrehozása](quickstart-create-cloudsimple-service.md)
* [Rövid útmutató – az AVS Private Cloud konfigurálása](quickstart-create-private-cloud.md)
* További információ az [Azure hálózati kapcsolatairól](cloudsimple-azure-network-connection.md)
* További információ a [VPN-átjárókkal](cloudsimple-vpn-gateways.md) kapcsolatban
