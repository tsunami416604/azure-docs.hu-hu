---
title: Azure VMware-megoldás a CloudSimple-től – Hálózati ellenőrzőlista
description: Ellenőrzőlista a hálózati CIDR hozzárendeléséhez a CloudSimple Által az Azure VMware-megoldáson
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025010"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Az Azure VMware-megoldás hálózati előfeltételei a CloudSimple által

Az Azure VMware Solution by CloudSimple olyan VMware privát felhőkörnyezetet kínál, amely a helyszíni környezetekből, nagyvállalati felügyelt eszközökről és Azure-erőforrásokból származó felhasználók és alkalmazások számára érhető el. A kapcsolat hálózati szolgáltatásokon, például VPN-eken és Azure ExpressRoute-kapcsolatokon keresztül érhető el. A hálózati szolgáltatások némelyike megköveteli, hogy hálózati címtartományokat adjon meg a szolgáltatások engedélyezéséhez. 

Ebben a cikkben található táblázatok a megadott címeket használó címtartományok és megfelelő szolgáltatások készletét ismertetik. Egyes címek kötelezőek, és néhány a telepíteni kívánt szolgáltatásoktól függ. Ezek a címterek nem fedhetik át a helyszíni alhálózatok, az Azure virtuális hálózat alhálózatait vagy a tervezett CloudSimple számítási feladatok alhálózatait.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>A magánfelhő létrehozásához szükséges hálózati címtartományok

A CloudSimple szolgáltatás és a magánfelhő létrehozása során meg kell felelnie a megadott hálózati osztály nélküli tartományok közötti útválasztási (CIDR) tartományok közötti útválasztás (CIDR) tartományok, az alábbiak szerint.

| Név/használt     | Leírás                                                                                                                            | Címtartomány            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Átjáró CIDR      | Peremhálózati szolgáltatásokhoz (VPN-átjárókhoz) szükséges.  Ez a CIDR a CloudSimple szolgáltatás létrehozása során szükséges, és az RFC 1918 területről kell származnia. | /28                      |
| vSphere/vSAN CIDR | VMware felügyeleti hálózatokesetén szükséges. Ezt a CIDR-t meg kell adni a magánfelhő létrehozása során.                                    | /24 vagy /23 vagy /22 vagy /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Az Azure hálózati kapcsolatához helyszíni hálózathoz szükséges hálózati címtartomány

A helyszíni [hálózatról a magánfelhő-hálózathoz az ExpressRoute-on keresztül](on-premises-connection.md) történő csatlakozás globális elérési kapcsolatot hoz létre.  A kapcsolat a Border Gateway Protocol (BGP) protokollt használja a helyszíni hálózat, a magánfelhő-hálózat és az Azure-hálózatok közötti útvonalak cseréjére.

| Név/használt             | Leírás                                                                                                                                                                             | Címtartomány |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute-társviszony-létesítési CIDR | Az ExpressRoute globális elérési használata a helyszíni kapcsolathoz szükséges. Ezt a CIDR-t akkor kell megadni, ha a globális elérés-kapcsolat kérése támogatási jegyen keresztül történik. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>A helyszíni hálózattal létesített helyek közötti VPN-kapcsolat hoz szükséges hálózati címtartomány

A helyszíni [hálózatról a magánfelhő-hálózathoz való csatlakozáshoz a helyek közötti VPN-kapcsolathoz](vpn-gateway.md) a következő IP-címekre, helyszíni hálózatra és azonosítókra van szükség. 

| Cím/cím tartomány | Leírás                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Társ IP-címe               | Helyszíni VPN-átjáró nyilvános IP-címe. A helyszíni adatközpont és a CloudSimple szolgáltatás régió közötti helyek közötti VPN-kapcsolat létrehozásához szükséges. Erre az IP-címre a helyek közötti VPN-átjáró létrehozása során van szükség.                                         |
| Társazonosító       | A helyszíni VPN-átjáró társazonosítója. Ez általában ugyanaz, mint a **peer IP**.  Ha a helyszíni VPN-átjárón egyedi azonosító van megadva, meg kell adni az azonosítót.  A helyek közötti VPN-átjáró létrehozása során társazonosító szükséges.   |
| Helyszíni hálózatok   | Helyszíni előtagok, amelyek hozzáférést igényelnek a régióban lévő CloudSimple-hálózatokhoz.  Tartalmazza a helyszíni hálózat összes olyan előtagjait, amelyek hozzáférnek a CloudSimple hálózathoz, beleértve azt az ügyfélhálózatot is, ahonnan a felhasználók hozzáférhetnek a hálózathoz.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>A pont-hely VPN-kapcsolatok kezeléséhez szükséges hálózati címtartomány

A pont-hely VPN-kapcsolat lehetővé teszi a hozzáférést a CloudSimple hálózat egy ügyfélgépről.  [A pont-hely VPN beállításához](vpn-gateway.md)a következő hálózati címtartományt kell megadnia.

| Cím/cím tartomány | Leírás                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ügyfél alhálózata         | A DHCP-címeket az ügyfélalhálózat biztosítja, ha pont-hely VPN-nel csatlakozik. Erre az alhálózatra akkor van szükség, amikor egy cloudsimple portálon pont-hely VPN-átjárót hoz létre.  A hálózat két alhálózatra oszlik; egyet az UDP-kapcsolathoz, a másikat a TCP-kapcsolatokhoz. |

## <a name="next-steps"></a>További lépések

* [Helyszíni tűzfal beállítása a magánfelhő eléréséhez](on-premises-firewall-configuration.md)
* [Rövid útmutató – CloudSimple szolgáltatás létrehozása](quickstart-create-cloudsimple-service.md)
* [Rövid útmutató – Magánfelhő konfigurálása](quickstart-create-private-cloud.md)
* További információ az [Azure hálózati kapcsolatairól](cloudsimple-azure-network-connection.md)
* További információ a [VPN-átjárókról](cloudsimple-vpn-gateways.md)
