---
title: 'Azure VPN-átjáró: Kriptográfiai követelmények'
description: Ez a cikk a kriptográfiai követelményeket és az Azure VPN-átjárókat ismerteti
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 93e13592d9c434b159ad4f4c10ef30328941c64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902827"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Kriptográfiai követelmények és Azure VPN-átjárók –

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure VPN-átjárók at a kriptográfiai követelmények nek mind a létesítmények közötti S2S VPN-alagutak és a Virtuálishálózat-vNet-kapcsolatok az Azure-on belül.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Az IKEv1 és az IKEv2 az Azure VPN-kapcsolatokhoz

Hagyományosan csak az Alapvető termékkészlethez engedélyeztük az IKEv1-kapcsolatokat, és az Egyszerű termékkészlettől eltérő összes VPN-átjáró termékkészlethez engedélyeztük az IKEv1-kapcsolatokat. Az alapszintű termékfelhasználó készletek csak 1 kapcsolatot engedélyeznek, és más korlátozásokkal, például a teljesítménnyel együtt a csak IKEv1 protokollokat támogató örökölt eszközöket használó ügyfelek korlátozott tapasztalattal rendelkeztek. Az IKEv1 protokollokat használó ügyfelek élményének fokozása érdekében mostantól engedélyezzük az IKEv1-kapcsolatokat az összes VPN átjáró termékváltozatához, kivéve az alapszintű termékváltozatokat. További információ: [VPN Gateway SKS](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku).

![Az Azure VPN Gateway IKEv1 és IKEv2 kapcsolatai](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

Ha az IKEv1 és ikev2 kapcsolatokat ugyanarra a VPN-átjáróra alkalmazza, a két kapcsolat közötti átvitel automatikusan engedélyezve van.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Az Azure VPN-átjárók IPsec- és IKE-házirendparamétereinek

Az IPsec és az IKE protokollszabvány a kriptográfiai algoritmusok széles skáláját támogatja különböző kombinációkban. Ha nem kér egy adott kombinációja kriptográfiai algoritmusok és paraméterek, az Azure VPN-átjárók alapértelmezett javaslatok at használ. Az alapértelmezett házirend-készleteket úgy választottuk ki, hogy maximalizálják az interoperabilitást a külső VPN-eszközök széles választékával az alapértelmezett konfigurációkban. Ennek eredményeképpen a házirendek és a javaslatok száma nem fedheti le a rendelkezésre álló kriptográfiai algoritmusok és kulcserősségek összes lehetséges kombinációját.

Az Azure VPN-átjáró alapértelmezett házirend-készlete a következő cikkben található: [A VPN-eszközök és az IPsec/IKE paraméterek a helyek közötti VPN-átjárókapcsolatokhoz.](vpn-gateway-about-vpn-devices.md)

## <a name="cryptographic-requirements"></a>Kriptográfiai követelmények

Bizonyos kriptográfiai algoritmusokat vagy paramétereket igénylő kommunikációk esetében, általában a megfelelőségi vagy biztonsági követelmények miatt, most már beállíthatja az Azure VPN-átjárókat, hogy egy egyéni IPsec/IKE-szabályzatot használjanak adott kriptográfiai algoritmusokkal és kulcserősségekkel, nem pedig az Azure alapértelmezett házirend-készleteit.

Például az Azure VPN-átjárók IKEv2 alapmódú szabályzatai csak a Diffie-Hellman Group 2 (1024 bit) csoportot használják, míg az IKE-ben használandó erősebb csoportokat kell megadnia, például a 14-es csoportban (2048 bites), a 24-es csoportban (2048 bites MODP-csoport) vagy az ECP-ben (elliptikus görbe) 256 vagy 384 bites (19-es, illetve 20-as csoport). Hasonló követelmények vonatkoznak az IPsec gyorsmódú házirendekre is.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Egyéni IPsec-/IKE-szabályzat Az Azure VPN-átjárókkal

Az Azure VPN-átjárók mostantól támogatják a kapcsolatonkénti egyéni IPsec/IKE-házirendet. Helyek közötti vagy virtuális hálózat közötti kapcsolatok esetén az IPsec és az IKE kriptográfiai algoritmusainak egy adott kombinációját választhatja a kívánt kulcserősséggel, ahogy az a következő példában látható:

![ipsec-ike-politika](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

IPsec/IKE-házirendet hozhat létre, és új vagy meglévő kapcsolatra vonatkozhat.

### <a name="workflow"></a>Munkafolyamat

1. Hozza létre a virtuális hálózatokat, VPN-átjárókat vagy helyi hálózati átjárókat a kapcsolódási topológiához, ahogyan azt más útmutató dokumentumok is ismertetik
2. IPsec/IKE-házirend létrehozása
3. A házirendet S2S- vagy VNet-to-VNet-kapcsolat létrehozásakor alkalmazhatja.
4. Ha a kapcsolat már létrejött, alkalmazhatja vagy frissítheti a házirendet egy meglévő kapcsolatra

## <a name="ipsecike-policy-faq"></a>Gyakran feltett kérdések az IPsec/IKE-házirendről

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>További lépések

Az Egyéni IPsec/IKE-házirend konfigurálásához a kapcsolategyéni IPsec/IKE-házirendjének részletes beállításával kapcsolatos részletes útmutatásért olvassa el az [IPsec/IKE-házirend konfigurálása](vpn-gateway-ipsecikepolicy-rm-powershell.md) című témakört.

A UsePolicyBasedTrafficSelectors beállításról további információ: [Több házirendalapú VPN-eszköz](vpn-gateway-connect-multiple-policybased-rm-ps.md) csatlakoztatása.
