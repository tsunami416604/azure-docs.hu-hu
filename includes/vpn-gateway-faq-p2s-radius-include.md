---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f9b787413dd6e2aaeafd4aa3bcee0eff746abece
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310543"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="is-radius-authentication-supported-on-all-azure-vpn-gateway-skus"></a>Minden Azure VPN Gateway termékváltozaton támogatott a RADIUS-hitelesítés?

A RADIUS-hitelesítést a VpnGw1, VpnGw2 és VpnGw3 termékváltozatok támogatják. Örökölt termékváltozatok használata esetén a RADIUS-hitelesítést a szabványos és a nagy teljesítményű termékváltozatok támogatják. Az alapszintű átjáró termékváltozat nem támogatja. 
 
### <a name="is-radius-authentication-supported-for-the-classic-deployment-model"></a>A klasszikus üzemi modell támogatja a RADIUS-hitelesítést?
 
Nem. A klasszikus üzemi modell nem támogatja a RADIUS-hitelesítést.
 
### <a name="are-3rd-party-radius-servers-supported"></a>Támogatottak a külső RADIUS-kiszolgálók?

Igen, a külső RADIUS-kiszolgálók támogatottak.
 
### <a name="what-are-the-connectivity-requirements-to-ensure-that-the-azure-gateway-is-able-to-reach-an-on-premises-radius-server"></a>Milyen kapcsolati követelményei vannak annak, hogy az Azure-átjáró biztosan elérjen egy helyszíni RADIUS-kiszolgálót?

Egy helyek közötti VPN-kapcsolat szükséges a helyszíni helyhez, amelyhez be kell állítani a megfelelő útvonalakat.  
 
### <a name="can-traffic-to-an-on-premises-radius-server-from-the-azure-vpn-gateway-be-routed-over-an-expressroute-connection"></a>Lehetséges-e a helyszíni RADIUS-kiszolgáló felé (az Azure VPN-átjáróról) irányuló adatforgalmat egy ExpressRoute-kapcsolaton keresztül irányítani?

Nem. Csak helyek közötti kapcsolaton keresztül lehetséges.
 
### <a name="is-there-a-change-in-the-number-of-sstp-connections-supported-with-radius-authentication-what-is-the-maximum-number-of-sstp-and-ikev2-connections-supported"></a>Változik a RADIUS-hitelesítés használatakor a támogatott SSTP-kapcsolatok száma? Legfeljebb hány SSTP- vagy IKEv2-kapcsolat támogatott?

RADIUS-hitelesítést használó átjárókon nem változik a támogatott SSTP-kapcsolatok maximális száma. Az SSTP esetében 128 marad, de a IKEv2 átjáró SKU-jának függvénye. A támogatott kapcsolatok számával kapcsolatos további információkért lásd: [átjáró SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)-i.
 
### <a name="what-is-the-difference-between-doing-certificate-authentication-using-a-radius-server-vs-using-azure-native-certificate-authentication-by-uploading-a-trustedcertificate-to-azure"></a>Mi a különbség a tanúsítványalapú hitelesítés és a RADIUS-kiszolgáló használatával, illetve az Azure natív tanúsítványalapú hitelesítésének használatával (megbízható tanúsítvány az Azure-ba való feltöltésével).

A RADIUS tanúsítványalapú hitelesítése esetén a hitelesítési kérelem egy RADIUS-kiszolgálóra lesz továbbítva, amely a tényleges tanúsítványhitelesítést végzi. Ez a lehetőség egy olyan tanúsítványhitelesítő infrastruktúrával való integrációkor hasznos, amellyel a RADIUS révén már rendelkezik.
  
Ha az Azure használatával hitelesíti a tanúsítványokat, akkor az Azure VPN-átjáró végzi a tanúsítványok ellenőrzését. Ehhez fel kell tölteni az átjáróra a tanúsítvány nyilvános kulcsát. Megadhat egy listát a visszavont tanúsítványokról is, amelyek számára nem engedélyezett a kapcsolódás.

### <a name="does-radius-authentication-work-with-both-ikev2-and-sstp-vpn"></a>A RADIUS-hitelesítés az IKEv2 és az SSTP VPN esetében is működik?

Igen, a RADIUS-hitelesítés az IKEv2-höz és az SSTP VPN-hez is támogatott. 

### <a name="does-radius-authentication-work-with-the-openvpn-client"></a>Működik a RADIUS-hitelesítés az OpenVPN-ügyféllel?

Az OpenVPN-ügyfél nem támogatja a RADIUS-hitelesítést.
