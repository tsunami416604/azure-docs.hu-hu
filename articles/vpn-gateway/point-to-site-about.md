---
title: Az Azure Point-to-Site VPN-kapcsolatok | VPN-átjáró
description: Ez a cikk segít megérteni a pont-hely kapcsolatokat, és segít eldönteni, hogy melyik P2S VPN-átjáró hitelesítési típusát használja.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: cherylmc
ms.openlocfilehash: 381aad5d0a56362d9966ed54b931a8478f2f6bf2
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410504"
---
# <a name="about-point-to-site-vpn"></a>A pont-hely VPN

A pont–hely (P2S) VPN-átjátókapcsolat lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a megoldás főleg távmunkások számára hasznos, akik egy távoli helyről szeretnének csatlakozni egy Azure virtuális hálózatokhoz, például otthonról vagy konferenciáról. A pont–hely VPN emellett akkor is hasznos megoldás lehet a helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy virtuális hálózathoz csatlakoztatni. Ez a cikk a Resource Manager-alapú üzemi modell vonatkozik.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Milyen protokollt használ a P2S?

A pont-hely VPN az alábbi protokollok egyikét használhatja:

* **OpenVPN® Protocol**, egy SSL/TLS alapú VPN protokoll. A TLS VPN-megoldás áthatolhat a tűzfalakon, mivel a legtöbb tűzfal megnyitja a 443-as TCP-portot, amelyet a TLS használ. OpenVPN lehet használni, hogy csatlakozzon az Android, iOS (11.0-s és újabb verziók), Windows, Linux és Mac eszközök (OSX verzió10.13 és újabb).

* Secure Socket Tunneling Protocol (SSTP), egy saját TLS-alapú VPN protokoll. A TLS VPN-megoldás áthatolhat a tűzfalakon, mivel a legtöbb tűzfal megnyitja a 443-as TCP-portot, amelyet a TLS használ. Az SSTP csak Windows-eszközökön támogatott. Az Azure támogatja a Windows összes SSTP-vel (Windows 7 és újabb) verzióit.

* IKEv2 VPN, egy szabványalapú IPsec VPN-megoldás. Az IKEv2 VPN segítségével Macről is lehetségessé válik a csatlakozás (OSX 10.11-es vagy újabb verziók használata esetén).


>[!NOTE]
>Az IKEv2 és az OpenVPN for P2S csak az Erőforrás-kezelő telepítési modelljéhez érhető el. Ezek nem érhetők el a klasszikus üzembe helyezési modellhez.
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>Hogyan hitelesítik a P2S VPN-ügyfeleket?

Mielőtt az Azure elfogadja a P2S VPN-kapcsolatot, a felhasználót először hitelesíteni kell. Az Azure két mechanizmust kínál a csatlakozó felhasználó hitelesítéséhez.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Hitelesítés natív Azure-tanúsítványhitelesítéssel

A natív Azure-tanúsítvány hitelesítése, az eszközön található ügyféltanúsítvány a csatlakozó felhasználó hitelesítésére szolgál. Az ügyféltanúsítványok megbízható főtanúsítványból jönnek létre, majd minden ügyfélszámítógépen telepítve lesznek. Használhat olyan főtanúsítványt, amely vállalati megoldással jött létre, vagy létrehozhat önaláírt tanúsítványt.

Az ügyféltanúsítvány érvényesítését a VPN-átjáró végzi, és a P2S VPN-kapcsolat létrehozása során történik. A főtanúsítvány szükséges az érvényesítéshez, és fel kell tölteni az Azure-ba.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Hitelesítés natív Azure Active Directory-hitelesítéssel

Az Azure AD-hitelesítés lehetővé teszi a felhasználók számára, hogy az Azure Active Directory hitelesítő adataikkal csatlakozzanak az Azure-hoz. A natív Azure AD-hitelesítés csak az OpenVPN protokoll és a Windows 10 esetében támogatott, és az [Azure VPN-ügyfél](https://go.microsoft.com/fwlink/?linkid=2117554)használatát igényli.

A natív Azure AD-hitelesítéssel kihasználhatja az Azure AD feltételes hozzáférését, valamint a többtényezős hitelesítés (MFA) funkcióit a VPN-hez.

Magas szinten az Azure AD-hitelesítés konfigurálásához a következő lépéseket kell végrehajtania:

1. [Azure AD-bérlő konfigurálása](openvpn-azure-ad-tenant.md)

2. [Az Azure AD-hitelesítés engedélyezése az átjárón](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Az Azure VPN-ügyfél letöltése és konfigurálása](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Hitelesítés az Active Directory (AD) tartományi kiszolgálóhasználatával

Az AD-tartomány hitelesítése lehetővé teszi a felhasználók számára, hogy a szervezeti tartomány hitelesítő adataival csatlakozzanak az Azure-hoz. Olyan RADIUS-kiszolgálóra van szükség, amely integrálható az AD-kiszolgálóval. A szervezetek a meglévő RADIUS-telepítésüket is kihasználhatják.
  
A RADIUS-kiszolgáló a helyszínen vagy az Azure virtuális hálózatban is telepíthető. A hitelesítés során az Azure VPN-átjáró a RADIUS-kiszolgáló és a csatlakozó eszköz közötti hitelesítési üzenetek továbbítására és továbbítására szolgál. Ezért fontos az átjáró elérhetősége a RADIUS-kiszolgálóhoz. Ha a RADIUS-kiszolgáló a helyszínen található, akkor az Azure és a helyszíni hely közötti VPN-S2S-kapcsolat szükséges az eléréshez.  
  
A RADIUS-kiszolgáló az AD tanúsítványszolgáltatásokkal is integrálható. Ez lehetővé teszi, hogy a RADIUS-kiszolgáló és a vállalati tanúsítvány központi telepítése a P2S tanúsítvány hitelesítése alternatívájaként az Azure tanúsítvány hitelesítése. Ennek előnye, hogy nem kell főtanúsítványokat és visszavont tanúsítványokat feltölteni az Azure-ba.

A RADIUS-kiszolgáló más külső identitáskezelő rendszerekkel is integrálható. Ez számos hitelesítési lehetőséget nyit meg a P2S VPN számára, beleértve a többtényezős beállításokat is.

![pontról helyre](./media/point-to-site-about/p2s.png "Pont–hely kapcsolat")

## <a name="what-are-the-client-configuration-requirements"></a>Mik az ügyfél konfigurációs követelményei?

>[!NOTE]
>Windows-ügyfelek esetén rendszergazdai jogokkal kell rendelkeznie az ügyféleszközön ahhoz, hogy kezdeményezze a VPN-kapcsolatot az ügyféleszközről az Azure-ba.
>

A felhasználók a natív VPN-ügyfeleket használják a Windows és a Mac p2s-eszközökön. Az Azure egy VPN-ügyfélkonfigurációs zip-fájlt biztosít, amely tartalmazza az azure-hoz való csatlakozáshoz szükséges natív ügyfelek által igényelt beállításokat.

* Windows-eszközök esetén a VPN-ügyfél konfigurációja egy telepítőcsomagból áll, amelyet a felhasználók telepítenek az eszközeikre.
* Mac-eszközök esetében ez a mobilkonfigurációs fájlból áll, amelyet a felhasználók telepítenek az eszközeikre.

A zip-fájl is biztosítja az értékeket néhány fontos beállítások at the Azure oldalon, amely segítségével hozzon létre saját profilt ezekhez az eszközökhöz. Az értékek közé tartozik a VPN-átjáró címe, a konfigurált bújtatástípusai, az útvonalak és az átjáró érvényesítéséhez szükséges főtanúsítvány.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Mely átjárótermék-kiszolgálók támogatják a P2S VPN-t?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Az átjáró termékváltozatára vonatkozó [javaslatokról a VPN-átjáró beállításairól olvashat.](vpn-gateway-about-vpn-gateway-settings.md#gwsku)

>[!NOTE]
>Az alapszintű termékváltozat nem támogatja az IKEv2- vagy RADIUS-hitelesítést.
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Milyen IKE/IPsec-házirendek vannak konfigurálva a P2S VPN-átjáróin?


**IKEv2**

|**Rejtjel** | **Integritás** | **PRF** | **DH-csoport** |
|---        | ---           | ---       | ---   |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP256 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384      | SHA384    | GROUP_24 |
|AES256     |   SHA384      | SHA384    | GROUP_14 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_24 |
|AES256     |   SHA256      | SHA256    | GROUP_14 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_2 |

**IPsec**

|**Rejtjel** | **Integritás** | **PFS-csoport** |
|---        | ---           | ---       |
|GCM_AES256 | GCM_AES256 | GROUP_NONE |
|GCM_AES256 | GCM_AES256 | GROUP_24 |
|GCM_AES256 | GCM_AES256 | GROUP_14 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Milyen TLS-házirendek vannak konfigurálva a VpN-átjárók p2s?
**TLS**

|**Házirendek** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Hogyan konfigurálhatók a P2S-kapcsolat?

A P2S-konfiguráció hoz szükség jó néhány konkrét lépést. Az alábbi cikkek a P2S-konfiguráción való végigvezető lépéseket, valamint a VPN-ügyféleszközök konfigurálásához szükséges hivatkozásokat tartalmazzák:

* [P2S-kapcsolat konfigurálása – RADIUS-hitelesítés](point-to-site-how-to-radius-ps.md)

* [P2S-kapcsolat konfigurálása – Azure natív tanúsítványhitelesítése](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Az OpenVPN konfigurálása](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>P2S-kapcsolat konfigurációjának eltávolítása

A lépéseket lásd az alábbi [GYIK](#removeconfig).
 
## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Gyakori kérdések a natív Azure-tanúsítványhitelesítéssel kapcsolatban

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Gyakran feltett kérdések a RADIUS-hitelesítéssel kapcsolatban

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Következő lépések

* [P2S-kapcsolat konfigurálása – RADIUS-hitelesítés](point-to-site-how-to-radius-ps.md)

* [P2S-kapcsolat konfigurálása – Azure natív tanúsítványhitelesítése](vpn-gateway-howto-point-to-site-rm-ps.md)

**Az "OpenVPN" az OpenVPN Inc. védjegye.**
