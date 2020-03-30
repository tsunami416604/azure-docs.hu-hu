---
title: Átmenet az OpenVPN-re vagy az IKEv2-re az SSTP-ből | Azure VPN-átjáró
description: Ez a cikk segít megérteni az SSTP 128 egyidejű kapcsolati korlátjának leküzdésének módjait.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: alzam
ms.openlocfilehash: 60c3a7f4f12993f475b3d03fd0915971d7673a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80143104"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Áttérés openVPN protokollra vagy IKEv2-re az SSTP-ből

A pont–hely (P2S) VPN-átjátókapcsolat lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a cikk az Erőforrás-kezelő telepítési modelljére vonatkozik, és az SSTP 128 egyidejű kapcsolati korlátjának az OpenVPN protokollra vagy az IKEv2 protokollra való áttéréssel történő leküzdési módjairól beszél.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Milyen protokollt használ a P2S?

A pont-hely VPN az alábbi protokollok egyikét használhatja:

* **OpenVPN&reg; Protocol**, egy SSL/TLS alapú VPN protokoll. Az SSL VPN-megoldás áthatolhat a tűzfalakon, mivel a legtöbb tűzfal megnyitja a 443-as TCP-portot, amelyet az SSL használ. OpenVPN lehet használni, hogy csatlakozzon az Android, iOS (11.0-s és újabb verziók), Windows, Linux és Mac eszközök (OSX verzió10.13 és újabb).

* **Secure Socket Tunneling Protocol (SSTP),** egy saját SSL-alapú VPN protokoll. Az SSL VPN-megoldás áthatolhat a tűzfalakon, mivel a legtöbb tűzfal megnyitja a 443-as TCP-portot, amelyet az SSL használ. Az SSTP csak Windows-eszközökön támogatott. Az Azure támogatja a Windows összes SSTP-vel (Windows 7 és újabb) verzióit. **Az SSTP legfeljebb 128 egyidejű kapcsolatot támogat, függetlenül az átjáró termékváltozatától.**

* IKEv2 VPN, egy szabványalapú IPsec VPN-megoldás. Az IKEv2 VPN segítségével Macről is lehetségessé válik a csatlakozás (OSX 10.11-es vagy újabb verziók használata esetén).


>[!NOTE]
>Az IKEv2 és az OpenVPN for P2S csak az Erőforrás-kezelő telepítési modelljéhez érhető el. Ezek nem érhetők el a klasszikus üzembe helyezési modellhez. Az egyszerű átjáró termékváltozata nem támogatja az IKEv2 vagy OpenVPN protokollokat. Ha az alapvető termékváltozat, törölnie kell, és újra létre kell hoznia egy éles SKU virtuális hálózati átjáró.
>

## <a name="migrating-from-sspt-to-ikev2-or-openvpn"></a>Áttelepítés az SSPT-ről az IKEv2-re vagy az OpenVPN-re

Előfordulhatnak olyan esetek, amikor több mint 128 egyidejű P2S-kapcsolatot szeretne támogatni egy VPN-átjáróval, de SSTP-t használ. Ebben az esetben át kell helyeznie az IKEv2 vagy openvpn protokollra.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>1. lehetőség - Az SSTP mellett az IKEv2 hozzáadása az átjáróhoz

Ez a legegyszerűbb megoldás. Az SSTP és az IKEv2 együtt létezhetnek ugyanazon az átjárón, és nagyobb számú egyidejű kapcsolatot biztosíthatnak. Egyszerűen engedélyezheti az IKEv2-t a meglévő átjárón, és újra letöltheti az ügyfelet.

Az IKEv2 hozzáadása egy meglévő SSTP VPN-átjáróhoz nincs hatással a meglévő ügyfelekre, és beállíthatja őket úgy, hogy az IKEv2-t kis kötegekben használják, vagy egyszerűen csak konfigurálják az új ügyfeleket az IKEv2 használatára. Ha egy Windows-ügyfél sstp-hez és IKEv2-hez is konfigurálva van, akkor először az IKEV2 használatával próbál csatlakozni, és ha ez nem sikerül, akkor visszaáll az SSTP-re.

**Az IKEv2 nem szabványos UDP-portokat használ, ezért biztosítania kell, hogy ezek a portok ne legyenek letiltva a felhasználó tűzfalán. A használt portok UDP 500 és 4500.**

Ha az IKEv2-t egy meglévő átjáróhoz szeretné hozzáadni, egyszerűen lépjen a "pont-hely konfiguráció" fülre a virtuális hálózati átjáró alatt a portálon, és válassza az **IKEv2 és az SSTP (SSTP)** lehetőséget a legördülő mezőből.

![pontról helyre](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>2. lehetőség - Az SSTP eltávolítása és az OpenVPN engedélyezése az átjárón

Mivel az SSTP és az OpenVPN egyaránt TLS-alapú protokoll, nem létezhetnek egymás mellett ugyanazon az átjárón. Ha úgy dönt, hogy eltávolodik az SSTP-től az OpenVPN-ig, le kell tiltania az SSTP-t, és engedélyeznie kell az OpenVPN-t az átjárón. Ez a művelet hatására a meglévő ügyfelek elveszítik a kapcsolatot a VPN-átjáróval, amíg az új profil nincs konfigurálva az ügyfélen.

Engedélyezheti OpenVPN mellett IKEv2, ha szeretné. OpenVPN TLS-alapú, és használja a szabványos TCP 443 port. Az OpenVPN-re való váltáshoz lépjen a "point-to-site configuration" fülre a virtuális hálózati átjáró alatt a portálon, és válassza az **OpenVPN (SSL)** vagy az **IKEv2 és openVPN (SSL)** lehetőséget a legördülő lista.

![pontról helyre](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Az átjáró konfigurálása után a meglévő ügyfelek nem tudnak csatlakozni, [amíg nem telepíti és nem konfigurálja az OpenVPN-ügyfeleket.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients)

Windows 10 használata esetén használhatja az [Azure VPN-ügyfelet is A Windows rendszerhez](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client)


## <a name="frequently-asked-questions"></a>Gyakori kérdések
### <a name="what-are-the-client-configuration-requirements"></a>Mik az ügyfél konfigurációs követelményei?

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

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Mely átjárótermék-kiszolgálók támogatják a P2S VPN-t?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Az átjáró termékváltozatára vonatkozó [javaslatokról a VPN-átjáró beállításairól olvashat.](vpn-gateway-about-vpn-gateway-settings.md#gwsku)

>[!NOTE]
>Az alapszintű termékváltozat nem támogatja az IKEv2- vagy RADIUS-hitelesítést.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Milyen IKE/IPsec-házirendek vannak konfigurálva a P2S VPN-átjáróin?


**IKEv2**

|**Rejtjel** | **Integritás** | **PRF** | **DH-csoport** |
|---        | ---            | ---        | ---     |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP256 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384        | SHA384    | GROUP_24 |
|AES256     |   SHA384        | SHA384    | GROUP_14 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_24 |
|AES256     |   SHA256        | SHA256    | GROUP_14 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_2 |

**IPsec**

|**Rejtjel** | **Integritás** | **PFS-csoport** |
|---        | ---            | ---        |
|GCM_AES256    | GCM_AES256 | GROUP_NONE |
|GCM_AES256    | GCM_AES256 | GROUP_24 |
|GCM_AES256    | GCM_AES256 | GROUP_14 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Milyen TLS-házirendek vannak konfigurálva a VpN-átjárók p2s?
**TLS**

|**Szabályzatok** |
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

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Hogyan konfigurálhatók a P2S-kapcsolat?

A P2S-konfiguráció hoz szükség jó néhány konkrét lépést. Az alábbi cikkek a P2S-konfiguráción való végigvezető lépéseket, valamint a VPN-ügyféleszközök konfigurálásához szükséges hivatkozásokat tartalmazzák:

* [P2S-kapcsolat konfigurálása – RADIUS-hitelesítés](point-to-site-how-to-radius-ps.md)

* [P2S-kapcsolat konfigurálása – Azure natív tanúsítványhitelesítése](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Az OpenVPN konfigurálása](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>További lépések

* [P2S-kapcsolat konfigurálása – RADIUS-hitelesítés](point-to-site-how-to-radius-ps.md)

* [P2S-kapcsolat konfigurálása – Azure natív tanúsítványhitelesítése](vpn-gateway-howto-point-to-site-rm-ps.md)

**Az "OpenVPN" az OpenVPN Inc. védjegye.**
