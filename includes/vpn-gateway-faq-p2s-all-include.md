---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 709a8eb9b5e2ab1a3a9deb10e35a5e996c8f9d93
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52585720"
---
### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Hány VPN-ügyfélvégpont lehet a pont–hely konfigurációban?

Ez függ az átjáró-Termékváltozatot. A támogatott kapcsolatok számára vonatkozó további információkért lásd: [átjáró-termékváltozatok](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="supportedclientos"></a>Milyen ügyfél operációs rendszereket használhatok pont–hely kapcsolatokhoz?

A következő ügyféloldali operációs rendszerek támogatottak:

* Windows 7 (32 bites és 64 bites)
* Windows Server 2008 R2 (csak 64 bites)
* Windows 8.1 (32 bites és 64 bites)
* Windows Server 2012 (csak 64 bites)
* Windows Server 2012 R2 (csak 64 bites)
* Windows Server 2016 (csak 64 bites)
* Windows 10
* Mac OS X verziója 10.11-es vagy újabb
* Linux (StrongSwan)
* iOS

[!INCLUDE [TLS](vpn-gateway-tls-updates.md)]

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Lehetővé teszi-e a pont–hely kapcsolat a proxykon és tűzfalakon való áthaladást?

Az Azure kétféle pont-hely típusú VPN-lehetőséget támogat:

* Secure Socket Tunneling Protocol (SSTP). Az SSTP a Microsoft jogvédett, SSL-alapú megoldása, amely képes átjutni a tűzfalakon, mivel a legtöbb tűzfal nyitva hagyja az SSL által használt 443-as TCP-portot.

* IKEv2 VPN. Az IKEv2 VPN egy szabványalapú IPsec VPN-megoldás, amely az 500-as és 4500-as UDP-portokat, valamint a következő IP-protokollt használja: 50. A tűzfalak ezeket a portokat nem mindig nyitják meg, ezért elképzelhető, hogy az IKEv2 VPN nem képes átjutni egyes proxykon és tűzfalakon.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Ha újraindítok egy pont–hely kapcsolat használatára konfigurált ügyfélszámítógépet, a VPN automatikusan újracsatlakozik?

Alapértelmezés szerint az ügyfélszámítógép nem létesíti újra a VPN-kapcsolatot.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Támogatják a pont–hely kapcsolatok az automatikus újrakapcsolódást és a DDNS-t a VPN-ügyfeleken?

Az automatikus újrakapcsolódás és a DDNS jelenleg nem támogatott a pont–hely VPN-kapcsolatokhoz.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Lehetnek-e helyek közötti és pont–hely konfigurációk egyidejűleg egy virtuális hálózaton?

Igen. A Resource Manager-alapú üzemi modell esetén az átjáróhoz RouteBased (útvonalalapú) VPN-típust kell használni. A klasszikus üzemi modellhez dinamikus átjáróra van szükség. A pont–hely kapcsolat nem támogatott a statikus útválasztású vagy PolicyBased (házirendalapú) VPN-átjárókhoz.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Konfigurálhatok úgy egy pont–hely ügyfelet, hogy több virtuális hálózathoz csatlakozzon egyszerre?

Nem. A pont-hely típusú ügyfelek csak olyan erőforrásokhoz tudnak csatlakozni, amelyek ugyanazon a virtuális hálózaton találhatók, mint a virtuális hálózati átjáró.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Milyen átviteli sebességre számíthatok a helyek közötti és a pont–hely kapcsolatok esetében?

Az átviteli sebesség fenntartása nehéz a VPN-alagutakban. Az IPsec és az SSTP erős titkosítást használó VPN-protokoll. Az átviteli sebességet emellett a késés, valamint a helyszín és az internet közötti sávszélesség is korlátozza. Az olyan VPN-átjárók esetében, amelyek csak IKEv2 pont-hely típusú VPN-kapcsolattal rendelkeznek, a várható teljes átviteli sebesség az átjáró termékváltozatától függ. Az átviteli sebességekkel kapcsolatos további információkért lásd: [Az átjárók termékváltozatai](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Használhatok szoftveres VPN-ügyfelet az SSTP-t és/vagy IKEv2-t támogató pont–hely kapcsolatokhoz?

Nem. Az SSTP esetében csak a Windows natív VPN-ügyfele, az IKEv2 esetében pedig csak a Mac natív VPN-ügyfele használható. Tekintse át a támogatott ügyfél operációs rendszerek listáját.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Támogatja az Azure az IKEv2 VPN használatát Windows rendszeren?

Az IKEv2 Windows 10 és Server 2016 rendszeren támogatott. Ahhoz azonban, hogy használni tudja az IKEv2-t, helyileg telepítenie kell a frissítéseket, és meg kell adnia a beállításkulcs értékét. A Windows 10 előtti operációsrendszer-verziók nem támogatottak, és csak SSTP-t tudnak használni.

A Windows 10 vagy a Server 2016 előkészítése az IKEv2 használatára:

1. Telepítse a frissítést.

  | Operációs rendszer verziója | Dátum | Szám/hivatkozás |
  |---|---|---|---|
  | Windows Server 2016<br>Windows 10, 1607-es verzió | 2018. január 17. | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
  | Windows 10, 1703-as verzió | 2018. január 17. | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
  |  |  |  |  |

2. Adja meg a beállításkulcs értékét. Hozza létre a „HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload” REG_DWORD kulcsot a beállításjegyzékben, vagy állítsa az értékét 1-re.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>Mi történik, ha az SSTP-t és az IKEv2-t is konfigurálom a P2S VPN-kapcsolatokhoz?

Ha SSTP-t és IKEv2-t is konfigurál vegyes (Windows és Mac eszközökből álló) környezetben, a Windows VPN-ügyfél először mindig az IKEv2-alagutat próbálja meg használni, de átvált az SSTP-re, ha nem lehet IKEv2-kapcsolatot létesíteni. A MacOSX csak IKEv2-n keresztül fog csatlakozni.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>A Windows- és Mac-eszközökön kívül mely platformokhoz támogatja még az Azure a P2S VPN-t?

Az Azure a Windows-, Mac- és Linux-eszközökhöz támogatja a P2S VPN-t.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>Már rendelkezem üzembe helyezett Azure VPN-átjáróval. Engedélyezhetem rajta a RADIUS-t és/vagy az IKEv2 VPN-t?

Igen, ezeknek az új funkcióknak a működését már üzemelő átjárókon is engedélyezni lehet, mégpedig a PowerShellen vagy az Azure Portalon keresztül, amennyiben a használt átjáró termékváltozata támogatja a RADIUS-t és/vagy az IKEv2-t. A VPN Gateway alapszintű termékváltozata például nem támogatja az IKEv2-t vagy a RADIUS-t.
