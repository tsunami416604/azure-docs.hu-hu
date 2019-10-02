---
title: Azure VMware-megoldás a CloudSimple-Network ellenőrzőlista
description: Ellenőrzőlista az Azure VMware-megoldás hálózati CIDR CloudSimple általi lefoglalásához
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c31942b198697ddc913f3732fd41409334ff0ef6
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817474"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Az Azure VMware-megoldás hálózatkezelési előfeltételei a CloudSimple

A CloudSimple által kínált Azure VMware-megoldás olyan VMware Private Cloud Environment-környezetet kínál, amely a felhasználók és alkalmazások számára elérhető a helyi környezetekben, a vállalat által felügyelt eszközöktől, valamint az Azure-erőforrásokból. A kapcsolat a hálózati szolgáltatások, például a VPN-EK és a ExpressRoute-kapcsolatok használatával érhető el.  Néhány Network Services megköveteli a szolgáltatások engedélyezéséhez szükséges hálózati címtartományok megadását.  A cikkben szereplő táblázatok a címtartományok és a megadott címeket használó kapcsolódó szolgáltatások leírását ismertetik.  A címzettek némelyike kötelező, és néhány a telepíteni kívánt szolgáltatástól függ.  Ezek a Címterület nem fedik át egymást a helyszíni alhálózatok, az Azure Virtual Network alhálózatok vagy a tervezett CloudSimple számítási feladatok alhálózatai között.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>A privát felhő létrehozásához szükséges hálózati címtartományok

A CloudSimple szolgáltatás és a privát felhő létrehozása során a következő hálózati CIDR-tartományra van szükség.

| Név/használat a következőhöz:     | Leírás                                                                                                                            | Címtartomány            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Átjáró – CIDR      | Az Edge-szolgáltatásokhoz (VPN-átjárók) szükséges.  Ez a CIDR a CloudSimple szolgáltatás létrehozásakor szükséges, és az RFC 1918 területről kell származnia. | /28                      |
| vSphere/vSAN CIDR | A VMware felügyeleti hálózatokhoz szükséges. Ezt a CIDR meg kell adni a saját felhő létrehozásakor.                                    | /24 vagy/23 vagy/22 vagy/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-on-premises-network"></a>A helyszíni hálózathoz való Azure-beli hálózati kapcsolódáshoz szükséges hálózati címtartomány

A helyszíni [hálózatról a ExpressRoute](on-premises-connection.md) -hez való csatlakozás Global REACH kapcsolatot létesít.  A kapcsolat a BGP-n keresztül továbbítja az útvonalakat a helyszíni hálózat, a saját felhőalapú hálózat és az Azure-hálózatok között.

| Név/használat a következőhöz:             | Leírás                                                                                                                                                                             | Címtartomány |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute peering CIDR | A helyszíni kapcsolathoz használt ExpressRoute-Global Reach használata esetén kötelező. Ezt a CIDR akkor kell megadni, ha egy Global Reach kapcsolódási kérelem egy támogatási jegyen keresztül történik. | /29           |

## <a name="network-address-range-required-for-using-site-to-site-vpn-connection-to-on-premises-network"></a>A helyek közötti VPN-kapcsolat helyszíni hálózathoz történő használatához szükséges hálózati címtartomány

A helyszíni [hálózatról a privát felhőalapú hálózatra helyek közötti VPN](vpn-gateway.md) -kapcsolaton keresztüli csatlakozáshoz a következő IP-címekre, helyszíni hálózatra és azonosítóra van szükség. 

| Címek/címtartomány | Leírás                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Társ IP-címe               | Helyszíni VPN-átjáró nyilvános IP-címe. A helyek közötti VPN-kapcsolat létesítéséhez szükséges a helyszíni adatközpont és a CloudSimple szolgáltatási régiója között. Ez az IP-cím szükséges a helyek közötti VPN-átjáró létrehozásakor.                                         |
| Társ azonosítója       | A helyszíni VPN-átjáró társ-azonosítója. Ez általában ugyanaz, mint a **társ IP-címe**.  Ha a helyszíni VPN-átjárón egyedi azonosító van megadva, az azonosítót meg kell adni.  A társ-azonosító megadása szükséges a helyek közötti VPN Gateway létrehozásához.   |
| Helyszíni hálózatok   | A régióban elérhető CloudSimple-hálózatokat igénylő helyszíni előtagok.  Adja meg az összes olyan előtagokat a helyszíni hálózatból, amelyek hozzáférnek a CloudSimple-hálózathoz, beleértve azt a kliens hálózatot, ahonnan a felhasználók hozzáférnek.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>A pont – hely VPN-kapcsolatok használatához szükséges hálózati címtartomány

A pont – hely VPN-kapcsolat lehetővé teszi a CloudSimple-hálózat elérését az ügyfélgépről.  A [pont – hely típusú VPN beállításához](vpn-gateway.md) a következő hálózati címtartomány megadása szükséges.

| Címek/címtartomány | Leírás                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ügyfél-alhálózat         | A DHCP-címeket a rendszer a pont – hely típusú VPN-kapcsolattal való csatlakozáskor adja meg az ügyfél alhálózatáról. Ezt az alhálózatot egy pont – hely típusú VPN-átjáró CloudSimple-portálon való létrehozásakor kell megadni.  A hálózat két alhálózatra lesz felosztva, és az UDP-kapcsolathoz és a többi TCP-kapcsolathoz lesz használva. |

## <a name="next-steps"></a>További lépések

* [Rövid útmutató – CloudSimple szolgáltatás létrehozása](quickstart-create-cloudsimple-service.md)
* [rövid útmutató – saját felhő létrehozása](quickstart-create-private-cloud.md)
* További információ az [Azure hálózati kapcsolatairól](cloudsimple-azure-network-connection.md)
* További információ a [VPN-átjárókkal](cloudsimple-vpn-gateways.md) kapcsolatban
