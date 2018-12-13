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
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111914"
---
A VNet – VNet – gyakori kérdések VPN gateway-kapcsolatok vonatkozik. További információ a virtuális hálózatok közötti társviszony-létesítés: [virtuális hálózatok közötti társviszony](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Felszámol az Azure díjat a virtuális hálózatok közötti adatforgalomért?

Az azonos régión belüli virtuális hálózatok közötti forgalom a VPN gateway-kapcsolat használata esetén ingyenes mindkét irányban. Régiók közötti virtuális hálózatok közötti kimenő forgalom díjának megállapítása a kimenő virtuális hálózatok közötti adatátvitelt, amely a forrásrégiók alapján. További információkért lásd: [VPN-átjáró díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/vpn-gateway/). Ha a virtuális hálózatok közötti társviszony-létesítés helyett egy VPN-átjáró használatával csatlakoztatja a virtuális hálózatokat, tekintse meg a [Virtual network díjszabása](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>A virtuális hálózatok közötti adatforgalom az interneten keresztül továbbítani nem?

Nem. A virtuális hálózatok közötti forgalom a Microsoft Azure gerinchálózatán keresztül, nem az interneten halad át.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Is a VNet – VNet kapcsolat is létesíteni az Azure Active Directory (AAD) bérlők között?

Igen, a VNet – VNet kapcsolat arra az Azure VPN-átjárókkal AAD-bérlők között működik.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Biztonságos-e a virtuális hálózatok közötti adatforgalom?

Igen, akkor az IPsec/IKE-titkosítás által védett.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Szükségem van VPN-eszközre a virtuális hálózatok egymáshoz kapcsolásához?

Nem. Az Azure Virtual Networkök összekapcsolása nem igényel VPN-eszközöket, hacsak nem szükséges a létesítmények közötti kapcsolat.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>A virtuális hálózataimnak ugyanabban a régióban kell lenniük?

Nem. A virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban (helyeken).

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Ha a virtuális hálózatok ugyanabban az előfizetésben nem találhatók, az előfizetések szükséges társítható ugyanahhoz az Active Directory-bérlőhöz?

Nem.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Összekapcsolhatok egymással különböző Azure-példányokban található virtuális hálózatokat? 

Nem. A virtuális hálózatok közötti kapcsolat az azonos Azure-példányon belüli virtuális hálózatok csatlakoztatását támogatja. Például egy globális Azure-ban és kínai/német/US government Azure közötti kapcsolat nem hozható létre példányok. Fontolja meg az ilyen feladatokhoz szükséges Site-to-Site VPN-kapcsolat használatával.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>A virtuális hálózatok közötti kapcsolatot használhatom többhelyes kapcsolatokhoz?

Igen. A virtuális hálózati kapcsolat használható többhelyes virtuális VPN-ekkel együtt.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Hány helyszíni helyhez és virtuális hálózathoz kapcsolódhat egyetlen virtuális hálózat?

Tekintse meg a [átjáróra vonatkozó követelmények](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements) tábla.

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>A virtuális hálózatok közötti kapcsolattal csatlakoztathatok a virtuális hálózaton kívüli virtuális gépeket vagy felhőszolgáltatásokat?

Nem. A virtuális hálózatok közötti kapcsolat támogatja a virtuális hálózatok csatlakoztatását, Nem támogatja a csatlakozó virtuális gépeket vagy felhőszolgáltatásokat, amelyek nem a virtuális hálózat.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Képes a felhőszolgáltatás és a terheléselosztási végpont átívelhetnek több virtuális hálózaton?

Nem. A felhőszolgáltatás és a terheléselosztási végpont nem ívelhet át több virtuális hálózaton, akkor is, ha ezek össze van kapcsolva.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Használhatok házirendalapú VPN-típussal virtuális hálózatok közötti vagy többhelyes kapcsolatokhoz?

Nem. Virtuális hálózatok közötti és többhelyes kapcsolatokhoz Azure VPN-átjárók az Útvonalalapú (korábbi nevén dinamikus útválasztású) VPN-típussal.

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Összekapcsolhatok egy RouteBased (útvonalapú) VPN-típussal rendelkező virtuális hálózatot egy házirendalapú VPN-típussal rendelkezővel?

Nem, mindkét virtuális hálózatnak útvonalalapú (korábban dinamikus útválasztású) VPN-eket kell használnia.

### <a name="do-vpn-tunnels-share-bandwidth"></a>A VPN-alagutak osztoznak a sávszélességen?

Igen. A virtuális hálózat minden VPN-alagútja az Azure VPN Gateway átjárón elérhető sávszélességet használja, és azonos VPN-átjáró üzemidőre vonatkozó SLA-t az Azure-ban.

### <a name="are-redundant-tunnels-supported"></a>Támogatottak a redundáns alagutak?

A virtuális hálózatok párjai közötti redundáns alagutak nem támogatottak, amikor a virtuális hálózati átjáró aktív-aktívként van konfigurálva.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Lehetnek átfedő címterek a virtuális hálózatok közötti konfigurációkhoz?

Nem. Nem lehetnek átfedő IP-címtartományok.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Lehetnek-e egymással átfedésben lévő címterek a csatlakoztatott virtuális hálózatok és helyszíni helyek között?

Nem. Nem lehetnek átfedő IP-címtartományok.



