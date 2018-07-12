---
title: Tudnivalók Azure pont – hely VPN-kapcsolatok |} A Microsoft Docs
description: Ez a cikk segít megérteni a pont – hely kapcsolatokhoz, és segít eldönteni, melyik P2S VPN gateway hitelesítési típus használata.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2018
ms.author: cherylmc
ms.openlocfilehash: 2668d92b5b933f7ccf8ebcccbe7ea77ea6ea1e86
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38721428"
---
# <a name="about-point-to-site-vpn"></a>Tudnivalók a pont – hely VPN

A pont–hely (P2S) VPN-átjátókapcsolat lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a megoldás főleg távmunkások számára hasznos, akik egy távoli helyről szeretnének csatlakozni egy Azure virtuális hálózatokhoz, például otthonról vagy konferenciáról. A pont–hely VPN emellett akkor is hasznos megoldás lehet a helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy virtuális hálózathoz csatlakoztatni. Ez a cikk a Resource Manager-alapú üzemi modell vonatkozik.

## <a name="protocol"></a>Milyen protokollokat használ P2S?

Pont – hely VPN a következő protokollok egyikét használhatja:

* Secure Socket Tunneling Protocol (SSTP), egy egyéni SSL-alapú VPN-protokollt. Az SSL VPN-megoldás behatolásának tűzfalak, mivel a legtöbb tűzfal nyitva TCP 443-as portot, amely az SSL. Az SSTP csak a Windows-eszközökön támogatott. Az Azure Windows SSTP (Windows 7 és újabb) rendelkező összes verziója támogatja.

* IKEv2 VPN, egy szabványalapú IPsec VPN-megoldás. Az IKEv2 VPN segítségével Macről is lehetségessé válik a csatlakozás (OSX 10.11-es vagy újabb verziók használata esetén).

Ha a Windows és Mac-eszközök tartalmazó vegyes környezetet, konfigurálja SSTP és IKEv2 is.

>[!NOTE]
>Az IKEv2 for P2S csak a Resource Manager üzemi modell érhető el. Ez nem áll rendelkezésre, a klasszikus üzemi modellhez.
>

## <a name="authentication"></a>P2S VPN-ügyfelek hitelesítésének módját?

Azure a P2S VPN kapcsolatot fogad el, mielőtt a felhasználó rendelkezik, először hitelesíteni. Nincsenek két Azure által kínált csatlakozó felhasználó hitelesítéséhez mechanizmus.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Hitelesítés az Azure natív tanúsítványalapú hitelesítésének használatával

Amikor az Azure natív tanúsítványalapú hitelesítésének használatával, egy ügyféltanúsítvány, amely az eszköz segítségével hitelesíteni a csatlakozó felhasználó. Ügyféltanúsítvány egy megbízható főtanúsítványból létrehozott ügyféltanúsítvány és majd az egyes ügyfélszámítógépekre telepített. Egy nagyvállalati megoldás használatával létrehozott főtanúsítványt is használhatja, vagy létrehozhat egy önaláírt tanúsítványt.

Az ügyféltanúsítvány érvényesítése a VPN-átjáró végzi, és a P2S VPN-kapcsolat létrehozása során történik. A legfelső szintű tanúsítvány szükséges az érvényesítés, és fel kell tölteni az Azure-bA.

### <a name="authenticate-using-active-directory-ad-domain-server"></a>Hitelesítés az Active Directory (AD) tartományhoz kiszolgáló használatával

AD-tartományhitelesítés lehetővé teszi a felhasználóknak csatlakoztatása az Azure-szervezeti tartományi hitelesítő adataik használatával. Egy RADIUS-kiszolgáló, amely integrálható az AD-kiszolgáló szükséges. Szervezetek is használhatják a meglévő RADIUS üzembe helyezésükben.   
  A RADIUS-kiszolgáló lehet üzembe helyezhető a helyszínen vagy az Azure virtuális hálózat. A hitelesítés során az Azure VPN Gateway funkcionál továbbítása, és oda-vissza a RADIUS-kiszolgáló és a csatlakozó eszköz között továbbított hitelesítési üzenetekben. Ezért fontos átjáró elérhető legyen a RADIUS-kiszolgáló. Ha a RADIUS-kiszolgáló nem található a helyszínen, a VPN S2S kapcsolat az Azure-ból a helyszíni helyre szükség a lemezekről.  
  A RADIUS-kiszolgáló AD tanúsítványszolgáltatások is integrálható. Ez lehetővé teszi a RADIUS-kiszolgáló és a vállalati tanúsítvány üzembe helyezés a P2S-tanúsítvány hitelesítéséhez az Azure-tanúsítvány hitelesítése alternatívájaként. Az előnye, hogy nem kell legfelső szintű tanúsítványok és a visszavont tanúsítványok feltöltése az Azure-bA.

Egy RADIUS-kiszolgáló is integrálhatók a más külső identitáskezelő rendszerekkel. Megnyílik bőségesen több hitelesítési lehetőség a P2S VPN-t, beleértve a multi-factor Authentication beállításait is.

! [pont – hely]] (./media/point-to-site-about/p2s.png "Pont – hely")

## <a name="what-are-the-client-configuration-requirements"></a>Mik azok az ügyfél-konfigurációs követelményei?

>[!NOTE]
>A Windows-ügyfelek esetében, rendszergazdai jogosultságokkal kell rendelkeznie az ügyféleszköz VPN-kapcsolatot az ügyféleszközön az Azure-bA kezdeményezése céljából.
>

Felhasználók a natív VPN-ügyfelek Windows és Mac-eszközök p2s használja. Az Azure biztosít a VPN-ügyfél konfigurációs zip fájlt, amely ezen natív ügyfelek által az Azure-bA a csatlakozáshoz szükséges beállításokat tartalmazza.

* Windows-eszközök esetén a VPN-ügyfél konfigurációja áll egy installer-csomag, amely a felhasználók telepítik az eszközeikre.
* A Mac-eszközök esetében áll a mobileconfig fájlt, amely a felhasználók telepítik az eszközeikre.

A zip-fájl néhány fontos beállítás értékét is biztosít, amely segítségével ezek az eszközök a saját profil létrehozása az Azure oldalán. Az értékek közé tartozik a VPN-átjáró címével, konfigurált alagút típusa, útvonalak és a legfelső szintű tanúsítvány az átjáró érvényesítése.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="gwsku"></a>Melyik átjáró SKU-k támogatása P2S VPN?

[!INCLUDE [p2s-skus](../../includes/vpn-gateway-table-point-to-site-skus-include.md)]

* Az Összesített átviteli sebesség tesztje több alagút egyetlen átjárón keresztül összesített mérésein alapul. Már nem a garantált átviteli internetes forgalom körülményei és az alkalmazás viselkedése miatt.
* Díjszabási információk találhatók a díjszabást ismertető oldalon 
* A SZOLGÁLTATÁSSZINT-szerződésünk SLA (szolgáltatásiszint-szerződés) adatai találhatók.

>[!NOTE]
>Az alapszintű termékváltozat nem támogatja az IKEv2- vagy RADIUS-hitelesítést.
>

## <a name="configure"></a>Hogyan konfigurálhatom a P2S-kapcsolat?

P2S konfiguráció még néhány konkrét lépésre van szükség. A következő cikkek részletesen P2S konfiguráció és a VPN-ügyfél eszközök konfigurálása mutató hivatkozásokat a lépéseket tartalmazza:

* [P2S-kapcsolat – RADIUS-hitelesítés konfigurálása](point-to-site-how-to-radius-ps.md)

* [P2S-kapcsolat – az Azure natív tanúsítványalapú hitelesítésének konfigurálása](vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="faqcert"></a>Az Azure natív tanúsítványalapú hitelesítésének – gyakori kérdések

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>RADIUS-hitelesítés – gyakori kérdések

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>További lépések

* [P2S-kapcsolat – RADIUS-hitelesítés konfigurálása](point-to-site-how-to-radius-ps.md)

* [P2S-kapcsolat – az Azure natív tanúsítványalapú hitelesítésének konfigurálása](vpn-gateway-howto-point-to-site-rm-ps.md)