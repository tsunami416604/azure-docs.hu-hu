---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/03/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72ddd0b6cd6c3e12417d3698c403f89312b531f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67178940"
---
A virtuális hálózat és a virtuális hálózat között gyakori kérdések a VPN-átjárókapcsolatokra vonatkoznak. A virtuális hálózati társviszony-létesítésről a [Virtuális hálózati társviszony-létesítés](../articles/virtual-network/virtual-network-peering-overview.md)című témakörben talál további információt.

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Felszámol az Azure díjat a virtuális hálózatok közötti adatforgalomért?

Virtuális hálózat-vnet forgalom ugyanazon a régión belül mindkét irányban, ha vpn-átjáró kapcsolatot használ. Régióközi virtuális hálózat-vnet kimenő forgalom a forrásrégiók alapján a kimenő virtuális hálózatok közötti adatátviteli díjakkal van felszámítva. További információt a [VPN-átjáró díjszabási oldalán talál.](https://azure.microsoft.com/pricing/details/vpn-gateway/) Ha a virtuális hálózatokat VPN-átjáró helyett virtuális hálózati társviszony-létesítéssel kapcsolja össze, olvassa el a [Virtuális hálózati díjszabás](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>A vnet-vnetes forgalom az interneten keresztül közlekedik?

Nem. A vnet-vnet-forgalom a Microsoft Azure gerinchálózatán, nem pedig az interneten keresztül halad.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Létrehozhatok virtuális hálózatés virtuális hálózat közötti kapcsolatot az Azure Active Directory (AAD) bérlői között?

Igen, az Azure VPN-átjárókat használó virtuális hálózat-virtuális hálózat kapcsolatok az AAD-bérlők között működnek.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Biztonságos-e a virtuális hálózatok közötti adatforgalom?

Igen, iPsec/IKE titkosítás védi.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Szükségem van VPN-eszközre a virtuális hálózatok egymáshoz kapcsolásához?

Nem. Az Azure Virtual Networkök összekapcsolása nem igényel VPN-eszközöket, hacsak nem szükséges a létesítmények közötti kapcsolat.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>A virtuális hálózataimnak ugyanabban a régióban kell lenniük?

Nem. A virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban (helyeken).

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Ha a virtuális hálózatok nem ugyanabban az előfizetésben, az előfizetések kell társ-val ugyanaz tazI Directory-bérlő?

Nem.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Összekapcsolhatok egymással különböző Azure-példányokban található virtuális hálózatokat? 

Nem. A virtuális hálózatok közötti kapcsolat az azonos Azure-példányon belüli virtuális hálózatok csatlakoztatását támogatja. Például nem hozhat létre kapcsolatot a globális Azure és a kínai/német/amerikai kormányzati Azure-példányok között. Fontolja meg a helyek közötti VPN-kapcsolat használatát ezekhez a forgatókönyvekhez.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>A virtuális hálózatok közötti kapcsolatot használhatom többhelyes kapcsolatokhoz?

Igen. A virtuális hálózati kapcsolat használható többhelyes virtuális VPN-ekkel együtt.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Hány helyszíni helyhez és virtuális hálózathoz kapcsolódhat egyetlen virtuális hálózat?

Tekintse meg az [átjáró követelmények](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements) tábla.

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>A virtuális hálózatok közötti kapcsolattal csatlakoztathatok a virtuális hálózaton kívüli virtuális gépeket vagy felhőszolgáltatásokat?

Nem. A virtuális hálózatok közötti kapcsolat támogatja a virtuális hálózatok csatlakoztatását, Nem támogatja a virtuális gépek vagy a virtuális hálózaton nem található felhőszolgáltatások csatlakoztatását.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Egy felhőalapú szolgáltatás vagy terheléselosztási végpont átnyúlhat a virtuális hálózatokon?

Nem. Egy felhőalapú szolgáltatás vagy egy terheléselosztási végpont nem terjedhet ki a virtuális hálózatokközött, még akkor sem, ha össze vannak kapcsolva.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Használhatok policybased VPN-típust virtuális hálózat ról virtuális hálózatra vagy többhelyre történő kapcsolatokhoz?

Nem. Virtuális hálózat-vnet és többhelyű kapcsolatok igényelnek Azure VPN-átjárók RouteBased (korábbi nevén dinamikus útválasztás) VPN-típusú.

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Összekapcsolhatok egy RouteBased (útvonalapú) VPN-típussal rendelkező virtuális hálózatot egy házirendalapú VPN-típussal rendelkezővel?

Nem, mindkét virtuális hálózatnak útvonalalapú (korábban dinamikus útválasztási) VPN-eket kell használnia.

### <a name="do-vpn-tunnels-share-bandwidth"></a>A VPN-alagutak osztoznak a sávszélességen?

Igen. A virtuális hálózat minden VPN-alagútja az Azure VPN Gateway átjárón elérhető sávszélességet használja, és azonos VPN-átjáró üzemidőre vonatkozó SLA-t az Azure-ban.

### <a name="are-redundant-tunnels-supported"></a>Támogatottak a redundáns alagutak?

A virtuális hálózatok párjai közötti redundáns alagutak nem támogatottak, amikor a virtuális hálózati átjáró aktív-aktívként van konfigurálva.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Lehetnek átfedő címterek a virtuális hálózatok közötti konfigurációkhoz?

Nem. Nem lehetnek átfedő IP-címtartományok.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Lehetnek-e egymással átfedésben lévő címterek a csatlakoztatott virtuális hálózatok és helyszíni helyek között?

Nem. Nem lehetnek átfedő IP-címtartományok.



