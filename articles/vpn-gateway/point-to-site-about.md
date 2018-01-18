---
title: "Azure pont – hely típusú VPN-kapcsolatok |} Microsoft Docs"
description: "Ez a cikk segít megérteni a pont – hely kapcsolatok, és segít eldönteni, melyik P2S VPN-átjáró hitelesítési típus használata."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2018
ms.author: cherylmc
ms.openlocfilehash: 74cfa8f54c52463ac0b42c5cc6abab7b0366ac29
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="about-point-to-site-vpn"></a>Pont – hely típusú VPN kapcsolatos

A pont–hely (P2S) VPN-átjátókapcsolat lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a megoldás főleg távmunkások számára hasznos, akik egy távoli helyről szeretnének csatlakozni egy Azure virtuális hálózatokhoz, például otthonról vagy konferenciáról. A pont–hely VPN emellett akkor is hasznos megoldás lehet a helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy virtuális hálózathoz csatlakoztatni. Ez a cikk a Resource Manager-alapú üzemi modell vonatkozik.

## <a name="protocol"></a>Milyen protokollt használ P2S?

Pont – hely típusú VPN az alábbi protokollok egyikét használhatja:

* Secure Socket Tunneling Protocol (SSTP), a saját SSL-alapú VPN-protokoll. Egy SSL VPN-megoldás is behatoljanak tűzfalak, mivel a legtöbb tűzfalon nyissa meg a TCP 443-as portot, amely SSL használja. SSTP csak a Windows rendszerű eszközök támogatják. Azure támogatja az SSTP (Windows 7 és újabb verziók) rendelkező Windows összes verzióján.

* IKEv2 VPN, egy szabványalapú IPsec VPN-megoldás. Az IKEv2 VPN segítségével Macről is lehetségessé válik a csatlakozás (OSX 10.11-es vagy újabb verziók használata esetén).

Ha álló Windows és Mac rendszerű ügyfelek különböző környezettel rendelkezik, konfigurálja az SSTP és IKEv2 is.

>[!NOTE]
>IKEv2 rendszer P2S a Resource Manager üzembe helyezési modellben érhető el. Nincs a klasszikus üzembe helyezési modellben érhető el.
>

## <a name="authentication"></a>P2S VPN-ügyfelek hitelesítésének módját?

Azure P2S VPN-kapcsolatot fogad el, mielőtt a felhasználó rendelkezik, először hitelesíti. Az Azure kínál a csatlakozó felhasználó hitelesítéséhez kétféle módszer van.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Hitelesítés a natív Azure Tanúsítványalapú hitelesítés használatával

A natív Azure tanúsítvány hitelesítése során egy ügyfél az eszközön található tanúsítvánnyal hitelesíti a csatlakozó felhasználó. Ügyféltanúsítványok által létrehozott egy megbízható legfelső szintű tanúsítványt, és települ ügyfélszámítógépekre. Egy legfelső szintű tanúsítvány lett létrehozva, vállalati megoldás is használható, vagy létrehozhat egy önaláírt tanúsítványt.

Az ügyféltanúsítvány érvényesítése VPN-átjáró által végzett, és a P2S VPN-kapcsolat létrehozása során történik. A legfelső szintű tanúsítvány szükséges az ellenőrzést, és fel kell tölteni, az Azure-bA. 

### <a name="authenticate-using-active-directory-ad-domain-server"></a>Hitelesítés Active Directory (AD) tartományhoz kiszolgáló használatával

Active Directory-tartománynak hitelesítés lehetővé teszi a felhasználók az Azure-ba való a szervezet tartományi hitelesítő adataik használatával. Egy RADIUS-kiszolgáló, amely integrálható az Active server igényel. A szervezetek is kihasználhatja a meglévő RADIUS-telepítésekhez.   
 A RADIUS-kiszolgáló lehet a telepített helyszíni vagy az Azure virtuális hálózat. A hitelesítés során az Azure VPN Gateway funkcionál továbbítása és továbbítást hitelesítési üzeneteket a RADIUS-kiszolgáló és a csatlakozó eszköz között. Ezért fontos átjáró reachability a RADIUS-kiszolgálónak. Ha a RADIUS-kiszolgáló nem szerepel a helyszíni VPN S2S csatlakozni az Azure-ból a helyszíni hely reachability szükség.  
 A RADIUS-kiszolgáló az AD tanúsítványszolgáltatásokkal integrálhatja. Ez lehetővé teszi a RADIUS-kiszolgáló és a vállalati tanúsítvány telepítési P2S tanúsítvány hitelesítéshez használandó ahelyett, hogy az Azure-tanúsítvány hitelesítése. Ennek előnye, hogy nem kell legfelső szintű tanúsítványok és a visszavont tanúsítványok feltöltése az Azure-bA.

Egy RADIUS-kiszolgáló integrálhatja más külső identitáskezelő rendszerekkel. Ekkor megnyílik a bőven hitelesítési beállítások P2S VPN, beleértve a multi-factor Authentication beállításainak mentése.

! [pont-pont]] (./media/point-to-site-about/p2s.png "Pont-pont")

### <a name="configuration-requirements-for-client-devices"></a>Ügyféleszközök konfigurációs követelményei

Felhasználók használni a natív VPN-ügyfelek Windows és Mac rendszerű eszközökön P2S. Azure biztosít a VPN-ügyfél konfigurációs zip ezen natív ügyfelek által Azure való kapcsolódáshoz szükséges beállításokat tartalmazó fájl.

* Windows-eszközök esetén a VPN-ügyfél konfigurációja egy, a felhasználók telepítik az eszközeikre telepítőcsomag áll.
* Mac-eszközök esetén áll a mobileconfig fájlt, amely a felhasználók telepítik az eszközeikre.

A zip-fájl néhány fontos beállítás értékének is biztosít, amelyek ezen eszközök saját profil létrehozása az Azure oldalán. Az értékek közé tartoznak a VPN-átjáró címét, konfigurált típusról, útvonalak és a legfelső szintű tanúsítvány átjáró érvényesítéshez.

### <a name="gwsku"></a>Mely Gateway SKU támogatási P2S VPN?

[!INCLUDE [p2s-skus](../../includes/vpn-gateway-table-point-to-site-skus-include.md)]

* Az Összesített átviteli sebesség tesztje több alagút egyetlen átjárón keresztül összesített mérésein alapul. Garantált átviteli sebességgel internetes forgalom feltételek és az alkalmazások működése miatt nincs.
* Az árazás oldalon található díjszabási információkat 
* SLA (szolgáltatásiszint-megállapodások) adatai a szolgáltatásiszint-szerződés lapon található.

>[!NOTE]
>Az alapszintű Termékváltozat nem támogatja az IKEv2 vagy RADIUS-hitelesítés.
>

## <a name="configure"></a>Hogyan konfigurálhatom P2S kapcsolatot?

P2S-konfigurációs számos adott lépést igényel. A következő cikkek végigvezetik Önt P2S konfigurációs, valamint hivatkozásokat a VPN-ügyfél eszközök lépéseit tartalmazza:

* [Kapcsolat P2S - RADIUS-hitelesítés konfigurálása](point-to-site-how-to-radius-ps.md)

* [Kapcsolat P2S - Azure natív tanúsítványhitelesítés konfigurálása](vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="faqcert"></a>Gyakran ismételt kérdések a natív Azure Tanúsítványalapú hitelesítés

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>Gyakran ismételt kérdések a RADIUS-hitelesítés

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>További lépések

* [Kapcsolat P2S - RADIUS-hitelesítés konfigurálása](point-to-site-how-to-radius-ps.md)

* [Kapcsolat P2S - Azure natív tanúsítványhitelesítés konfigurálása](vpn-gateway-howto-point-to-site-rm-ps.md)