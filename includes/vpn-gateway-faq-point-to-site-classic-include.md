---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c0ad6ea5a687d066c78533b45a7f531561661bf
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323744"
---
Ez a GYIK a klasszikus üzemi modellt használó P2S-kapcsolatokra vonatkozik.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Milyen ügyfél operációs rendszereket használhatok pont–hely kapcsolatokhoz?

A következő ügyféloldali operációs rendszerek támogatottak:

* Windows 7 (32 bites és 64 bites)
* Windows Server 2008 R2 (csak 64 bites)
* Windows 8 (32 bites és 64 bites)
* Windows 8.1 (32 bites és 64 bites)
* Windows Server 2012 (csak 64 bites)
* Windows Server 2012 R2 (csak 64 bites)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>Használható a pont – hely SSTP támogató szoftveres VPN-ügyfelet?

Nem. Támogatása csak a listában szereplő Windows operációs rendszerek verzióinak korlátozódik.

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>Hány VPN ügyfél-végpontok létezhet a pont – hely konfigurációban?

Legfeljebb 128 VPN ügyfelek csatlakozhatnak egy virtuális hálózatot egy időben.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Használhatom a saját PKI legfelső szintű hitelesítésszolgáltatómat a pont–hely kapcsolathoz?

Igen. Korábban csak önaláírt főtanúsítványt lehetett használni. Továbbra is legfeljebb 20 főtanúsítványt tölthet fel.

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>Is áthaladást proxykon és tűzfalakon pont – hely használatával?

Igen. Secure Socket Tunneling Protocol (SSTP) tűzfalakon áthaladó alagutakhoz használjuk. Ezt az alagutat a HTTPS-kapcsolatként jelenik meg.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Ha újraindítok egy pont–hely kapcsolat használatára konfigurált ügyfélszámítógépet, a VPN automatikusan újracsatlakozik?

Alapértelmezés szerint az ügyfélszámítógép nem helyreállítani a VPN-kapcsolatot automatikusan.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Pont – hely támogatja az automatikus újracsatlakozás nem és a DDNS-t a VPN-ügyfeleken?

Nem. Az automatikus újracsatlakozás, és a DDNS jelenleg nem támogatott a pont – hely VPN-eket.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>Site-to-Site, és ugyanahhoz a virtuális hálózathoz pont – hely konfigurációk is használhatok?

Igen. A két megoldás fog működni, ha az átjárójához Útvonalalapú VPN-típust. A klasszikus üzemi modellhez dinamikus átjáróra van szükség. Nem tudjuk támogatási pont – hely a statikus útválasztású VPN-átjárókhoz vagy a használó átjárók a **- VpnType PolicyBased** parancsmagot.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Konfigurálhatok úgy egy pont–hely ügyfelet, hogy több virtuális hálózathoz csatlakozzon egyszerre?

Igen. Azonban a virtuális hálózatok nem lehetnek átfedő IP-címelőtagokat, és a pont – hely címterei nem lehetnek egymással átfedésben a virtuális hálózatok között.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Milyen átviteli sebességre számíthatok a helyek közötti és a pont–hely kapcsolatok esetében?

Az átviteli sebesség fenntartása nehéz a VPN-alagutakban. Az IPsec és az SSTP erős titkosítást használó VPN-protokoll. Átviteli sebességet a késés, valamint a helyszín és az internet közötti sávszélesség is korlátozza.
