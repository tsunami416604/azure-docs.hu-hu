---
title: 'Azure ExpressRoute: Titkosítás – ismeret'
description: További információ az ExpressRoute-titkosításról.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: cherylmc
ms.openlocfilehash: d48f83df4e83fd1d1fe38eea76432e1b1c711b76
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461463"
---
# <a name="expressroute-encryption"></a>ExpressRoute titkosítás
 
Az ExpressRoute néhány titkosítási technológiát támogat a hálózat és a Microsoft hálózata közötti adatátvitel titkosságának és integritásának biztosítása érdekében.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Pont-pont titkosítás MACsec GYIK-vel
A MACsec egy [IEEE szabvány.](https://1.ieee802.org/security/802-1ae/) A Media Access Control (MAC) vagy a Network Layer 2 szintjén titkosítja az adatokat. A MACsec segítségével titkosíthatja a hálózati eszközök és a Microsoft hálózati eszközei közötti fizikai kapcsolatokat, amikor [az ExpressRoute Direct](expressroute-erdirect-about.md)en keresztül csatlakozik a Microsofthoz. A MACsec alapértelmezés szerint le van tiltva az ExpressRoute Direct portokon. Saját MACsec-kulcsot hoz a titkosításhoz, és tárolja az [Azure Key Vaultban.](../key-vault/general/overview.md) Ön dönti el, hogy mikor kell elforgatni a kulcsot. Lásd az alábbi gyIK-et.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Engedélyezhetem a MACsec-et az ExpressRoute-szolgáltató által kiépített ExpressRoute-kapcsolaton?
Nem. A MACsec titkosítja a fizikai kapcsolaton lévő összes forgalmat egy entitás (azaz ügyfél) tulajdonában lévő kulccsal. Ezért csak az ExpressRoute Directen érhető el.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Titkosíthatom az ExpressRoute Direct-portok egyes ExpressRoute-áramköreit, és titkosítás nélkül hagyhatom más áramköröket ugyanazon a porton? 
Nem. Ha a MACsec engedélyezve van, az összes hálózati vezérlőforgalom, például a BGP-adatforgalom és az ügyféladat-forgalom titkosítva lesz. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Amikor engedélyezem/letiltom a MACsec-kulcsot, vagy frissítem a MACsec kulcsot, a helyszíni hálózatom elveszíti a kapcsolatot a Microsofttal az ExpressRoute-on keresztül?
Igen. A MACsec konfiguráció esetében csak az előmegosztott kulcs módot támogatjuk. Ez azt jelenti, hogy frissítenie kell a kulcsot mind az eszközökön, mind a Microsofton (az API-n keresztül). Ez a változás nem atomi, így elveszíti a kapcsolatot, ha a két fél között kulcseltérés van. Javasoljuk, hogy ütemezze a karbantartási időszak a konfiguráció s. Az állásidő minimalizálása érdekében javasoljuk, hogy frissítse a konfigurációt az ExpressRoute Direct egyik hivatkozásán, miután a hálózati forgalmat a másik kapcsolatra váltotta.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>Folytatódik a forgalom, ha a MACsec-kulcs nem egyezik meg az eszközeim és a Microsoft kulcsa között?
Nem. Ha a MACsec konfigurálva van, és kulcseltérés következik be, megszakad a kapcsolat a Microsofttal. Más szóval, nem fogunk visszatérni egy titkosítatlan kapcsolatra, felfedve az adatait. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>A MACsec engedélyezése az ExpressRoute Directen csökkenti a hálózati teljesítményt?
MACsec titkosítás és visszafejtés történik hardver az útválasztók általunk használt. Nincs teljesítményhatással a mi oldalunkon. A használt eszközökről azonban érdeklődjön a hálózat forgalmazójával, és ellenőrizze, hogy a MACsec-nek van-e teljesítménybeli hatása.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>melyik titkosítási csomagok támogatottak a titkosításhoz?
Az AES128 és az AES256 támogatást nyújtjuk.

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Végpontok közötti titkosítás IPsec gyik alatt
Az IPsec egy [IETF szabvány.](https://tools.ietf.org/html/rfc6071) Az internetprotokoll (IP) szintjén vagy a 3. Az IPsec segítségével titkosíthatja a helyszíni hálózat és a virtuális hálózat (VNET) közötti végpontok közötti kapcsolatot az Azure-ban. Lásd az alábbi gyIK-et.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Engedélyezhetem az IPsec-et a MACsec mellett az ExpressRoute Direct portjaimon?
Igen. A MACsec biztosítja az Ön és a Microsoft közötti fizikai kapcsolatokat. Az IPsec biztosítja az Ön és az Azure virtuális hálózatai közötti végpontok közötti kapcsolatot. Ezek egymástól függetlenül engedélyezhetők. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>Használhatom az Azure VPN-átjárót az IPsec-alagút beállításához a helyszíni hálózatom és az Azure virtuális hálózatom között?
Igen. Ezt az IPsec-alagutat az ExpressRoute-kapcsolati kapcsolat Microsoft társviszony-létesítésén keresztül állíthatja be. Kövesse [konfigurációs útmutatónkat](site-to-site-vpn-over-microsoft-peering.md).
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Használhatom az Azure VPN-átjárót az IPsec-alagút beállításához az Azure privát társviszony-létesítésén?
Ha elfogadja az Azure Virtual WAN-t, kövesse [az alábbi lépéseket](../virtual-wan/vpn-over-expressroute.md) a végpontok között lévő kapcsolat titkosításához. Ha rendszeres Azure-virtuális hálózattal rendelkezik, telepíthet egy külső VPN-átjárót a virtuális hálózatban, és létrehozhat egy IPsec-alagutat a közöttés a helyszíni VPN-átjáró között.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Mi az az átviteli merteát kap, miután engedélyezte az IPsec az én ExpressRoute-kapcsolat?
Ha Azure VPN-átjárót használ, ellenőrizze a [teljesítményszámokat itt.](../vpn-gateway/vpn-gateway-about-vpngateways.md) Ha külső VPN-átjárót használ, érdeklődjön a szállítónál a teljesítményszámokról.

## <a name="next-steps"></a>További lépések
A MACsec konfigurációval kapcsolatos további információkért lásd: [MACsec konfigurálása.](expressroute-howto-macsec.md)

Az [IPsec-konfigurációról az IPsec konfigurálása](site-to-site-vpn-over-microsoft-peering.md) című témakörben talál további információt.
