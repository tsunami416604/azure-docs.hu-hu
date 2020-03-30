---
title: Az Azure csatlakoztatása nyilvános felhőkhöz | Microsoft dokumentumok
description: Az Azure más nyilvános felhőkhöz való csatlakoztatásának különböző módjai
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: b8a454c2a104dfe8545cf734bf0b020b8f749bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889626"
---
# <a name="connecting-azure-with-public-clouds"></a>Az Azure összekapcsolása nyilvános felhőkkel

Számos vállalat többfelhős stratégiát követ az üzleti és műszaki célok miatt. Ezek közé tartozik a költség, a rugalmasság, a szolgáltatás rendelkezésre állása, redundancia, adatszuverenitás stb. Ez a stratégia segít nekik kihasználni a legjobb mindkét felhők. 

Ez a megközelítés a hálózat- és alkalmazásarchitektúra tekintetében is kihívást jelent a vállalat számára. Ezek a kihívások a késés és az adatátviteli. E kihívások kezelése érdekében az ügyfelek közvetlenül több felhőhöz szeretnének csatlakozni. Egyes szolgáltatók megoldást kínálnak több felhőszolgáltató ügyfelek számára való csatlakoztatására. Más esetekben az ügyfél telepítheti saját útválasztóját, hogy több nyilvános felhőt csatlakoztasson.
## <a name="connectivity-via-expressroute"></a>Kapcsolat az ExpressRoute-on keresztül
Az ExpressRoute lehetővé teszi az ügyfelek számára, hogy kiterjesszék helyszíni hálózataikat a Microsoft felhőbe egy kapcsolatszolgáltató által támogatott magánkapcsolaton keresztül. Az ExpressRoute segítségével az ügyfelek kapcsolatokat létesíthetnek a Microsoft felhőszolgáltatásaival.

Az ExpressRoute-on keresztül háromféleképpen csatlakozhat.

1. Layer3 szolgáltató
2. Layer2 szolgáltató
3. Közvetlen kapcsolat

### <a name="layer3-provider"></a>Layer3 szolgáltató

A Layer3 szolgáltatók at általában IP VPN- vagy MPLS VPN-szolgáltatóknak nevezik. Az ügyfelek ezeket a szolgáltatókat használják ki az adatközpontjaik, fiókjaik és a felhő közötti többpontos kapcsolatra. Az ügyfelek bgp-n vagy statikus alapértelmezett útvonalon keresztül csatlakoznak az L3 szolgáltatóhoz. A szolgáltató útvonalakat hirdet az ügyfélhelyek, az adatközpontok és a nyilvános felhő között. 
 
A Layer3 szolgáltatón keresztül történő csatlakozáskor a Microsoft a BGP-n keresztül hirdeti az ügyfél virtuális hálózat útvonalait a szolgáltatóhoz. A szolgáltató nak két különböző implementációja lehet.

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

Előfordulhat, hogy a szolgáltató minden felhőszolgáltatót külön VRF-ben fog letenni, ha az összes felhőszolgáltató forgalma eléri az ügyfél útválasztóját. Ha az ügyfél a BGP-t szolgáltatóval futtatja, akkor ezeket az útvonalakat alapértelmezés szerint újra meghirdeti más felhőszolgáltatóknak. 

Ha a szolgáltató az összes felhőszolgáltatót ugyanabban a VRF-ben teszi meg, akkor az útvonalakat közvetlenül a szolgáltatótól más felhőszolgáltatóknak hirdetjük meg. Ez a szokásos BGP-művelet, ahol az eBGP-útvonalakat alapértelmezés szerint más eBGP-szomszédok nak hirdetik.

Minden nyilvános felhő különböző előtag-korláttal rendelkezik, ezért az útvonalak terjesztése során a szolgáltatónak óvatosan kell eljárnia az útvonalak elosztásakor.

### <a name="layer2-provider-and-direct-connection"></a>Layer2 szolgáltató és közvetlen kapcsolat

Bár a fizikai kapcsolat mindkét modellben eltérő, de layer3 BGP jön létre közvetlenül az MSEE és az ügyfél router. Az ExpressRoute Direct esetén az ügyfél közvetlenül csatlakozik az MSEE-hez. Míg a Layer2 esetében a szolgáltató kiterjeszti a VLAN-t az ügyfél telephelyéről a felhőre. Az ügyfelek a BGP-t a layer2 hálózaton keresztül futtatják, hogy tartományvezérlőiket a felhőhöz csatlakoztassák.
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
Mindkét esetben az ügyfél pont-pont kapcsolatokkal rendelkezik az egyes nyilvános felhőkhöz. Az ügyfél külön BGP-kapcsolatot hoz létre minden nyilvános felhővel. Az egyik felhőszolgáltató által fogadott útvonalakat alapértelmezés szerint más felhőszolgáltató nak is meghirdetjük. Minden felhőszolgáltató nak különböző előtag-korlátja van, ezért miközben az útvonalakat hirdeti, az ügyfeleknek gondoskodniuk kell ezekről a korlátokról. Az ügyfél a szokásos BGP-gombokat használhatja a Microsofttal, miközben más nyilvános felhőkből származó útvonalakat hirdet.

## <a name="direct-connection-with-expressroute"></a>Közvetlen kapcsolat az ExpressRoute-tal

Az ügyfelek választhatnak, hogy közvetlenül csatlakoztatják az ExpressRoute-ot a felhőszolgáltató közvetlen kapcsolódási ajánlatához. Két felhőszolgáltató csatlakozik háttal, és a BGP közvetlenül az útválasztóik között jön létre. Ez a fajta kapcsolat ma érhető el az Oracle-nél.

## <a name="site-to-site-vpn"></a>Helyek közötti VPN

Az ügyfelek az internet segítségével csatlakoztathatják példányaikat az Azure-ban más nyilvános felhőkhöz. Szinte az összes felhőszolgáltató kínál helyek közötti VPN-képességeket. Bizonyos változatok hiánya miatt azonban összeférhetetlenség állhat fenn. Egyes felhőszolgáltatók például csak az IKEv1-et támogatják, így az adott felhőben szükség van egy VPN-végpontra. Az IKEv2-t támogató felhőszolgáltatók számára közvetlen alagút létesíthető a VPN-átjárók között mindkét felhőszolgáltatónál.

A helyek közötti VPN nem tekinthető nagy átviteli és alacsony késleltetésű megoldásnak. Azonban használható a fizikai kapcsolat biztonsági mentéseként.

## <a name="next-steps"></a>További lépések
Az ExpressRoute-tal és a virtuális hálózati kapcsolattal kapcsolatos további kérdésekért tekintse meg az [ExpressRoute gyakori kérdéseit.][ER-FAQ]

Lásd: [Közvetlen kapcsolat beállítása az Azure és az Oracle Cloud között][ER-OCI] az Azure és az Oracle közötti kapcsolatért

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



