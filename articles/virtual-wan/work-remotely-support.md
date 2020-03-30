---
title: Azure Virtual WAN és távolról végzett munka
description: Ez az oldal bemutatja, hogyan használhatja ki az Azure Virtual WAN-t a covid-19-es világjárvány miatt távolról történő munkavégzés engedélyezéséhez.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: article
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: ce212b5da90906966025674b58884d0e2f5bb064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337127"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Az Azure Virtual WAN és a távoli munka támogatása

>[!NOTE]
>Ez a cikk bemutatja, hogyan használhatja ki az Azure Virtual WAN, az Azure, a Microsoft hálózatés az Azure partneri ökoszisztéma segítségével a távoli munka és a covid-19-es válság miatt felmerülő hálózati problémák enyhítése.
>

A távoli felhasználók számára biztosít kapcsolatot?
Hirtelen szükség van arra, hogy támogassa a felhasználók megugrását a tervezettnél nagyobb mértékben?
Szüksége van a felhasználóra, hogy otthonról csatlakozzon, és ne csak a felhőhöz férjen hozzá, hanem a helyszíni eléréshez is?
Szüksége van a távoli felhasználókszámára, hogy a privát WAN mögött idohez jussanak el?
Szükség van arra, hogy a felhasználók a régiók közötti kapcsolat beállítása nélkül is hozzáférjenek a felhőn belüli erőforrásokhoz?
Mivel ez a globális világjárvány példátlan változásokat hoz létre körülöttünk, az Azure Virtual WAN csapata itt van, hogy kielégítse a kapcsolódási igényeit.

Az Azure Virtual WAN egy hálózati szolgáltatás, amely számos hálózati, biztonsági és útválasztási funkciót egyesít, hogy egyetlen operatív felületet biztosítson. Ezek a funkciók magukban foglalják a branch kapcsolatot (a virtuális WAN-partnereszközök, például az SD-WAN vagy a VPN CPE kapcsolatautomatizálásán keresztül), a helyek közötti VPN-kapcsolat, a távoli felhasználói VPN (point-to-site) kapcsolat, a privát (ExpressRoute) kapcsolat, Felhőn belüli kapcsolat (tranzitív kapcsolat virtuális hálózatokhoz), VPN ExpressRoute interconnectivity, Útválasztás, Azure tűzfal, Titkosítás a privát kapcsolathoz stb. A Virtual WAN használatának megkezdéséhez nem kell rendelkeznie az összes ilyen használati esetsel. Csak egy használati tokkal kezdheti el a készüléket, és a hálózat ot is módosíthatja a fejlődés során.

![Virtuális WAN ábrája](./media/virtual-wan-about/virtualwan1.png)

Most beszélünk távoli felhasználók, lehetővé teszi, hogy nézd meg, mit kell kap a hálózat, és fut:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Távoli felhasználói kapcsolat beállítása

Az Erőforrásokhoz IPsec/IKE (IKEv2) vagy OpenVPN-kapcsolaton keresztül csatlakozhat az erőforrásokhoz. Az ilyen típusú kapcsolathoz vpn-ügyfél szükséges a távoli felhasználó számára. Ez az ügyfél lehet az [Azure VPN-ügyfél](https://go.microsoft.com/fwlink/?linkid=2117554) vagy OpenVPN-ügyfél, vagy bármely olyan ügyfél, amely támogatja az IKEv2.This client can be the Azure VPN client or OpenVPN Client or any client that supports IKEv2. További információt a [Pont-hely kapcsolat létrehozása](virtual-wan-point-to-site-portal.md)című témakörben talál.

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Kapcsolat a távoli felhasználóés a helyszíni

Két lehetőség van:

* Állítsa be a helyek közötti kapcsolatot bármely meglévő VPN-eszközzel. Amikor csatlakoztatja az IPsec VPN-eszközt az Azure Virtual WAN hubhoz, a pont-hely felhasználói VPN (távoli felhasználó) és a helyek közötti VPN közötti összekapcsolhatóság automatikus. A helyek közötti VPN-nek a helyszíni VPN-eszközről az Azure Virtual WAN-ra történő beállításáról további információt a [Helyek közötti kapcsolat létrehozása a Virtuális WAN használatával című](virtual-wan-site-to-site-portal.md)témakörben talál.

* Csatlakoztassa az ExpressRoute-áramkört a Virtual WAN hubhoz. Az ExpressRoute-kapcsolat csatlakoztatásához expressroute-átjárót kell telepíteni a Virtual WAN-ban. Amint telepített egyet, a point-to-site user VPN és expressroute-felhasználó közötti összekapcsolhatóság automatikus. Az ExpressRoute-kapcsolat létrehozásáról az [ExpressRoute-kapcsolat létrehozása a Virtual WAN használatával](virtual-wan-expressroute-portal.md)című témakörben található. Egy meglévő ExpressRoute-kapcsolatcsoport használatával csatlakozhat az Azure Virtual WAN-hoz.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Meglévő alapvető Virtual WAN-ügyfél

Az egyszerű virtuális WAN csak helyek közötti VPN-t biztosít. Ahhoz, hogy a távoli felhasználók csatlakozhassanak, frissítenie kell a virtuális WAN-t standard virtuális WAN-ra. A virtuális WAN frissítésének lépéseit a [Virtuális WAN frissítése alapról standardra című](upgrade-virtual-wan.md) témakörben olvassa el.

## <a name="additional-information"></a><a name="other considerations"></a>További információk

A Virtual WAN régiónként/helyenként egy elosztót támogat. A helyekkel kapcsolatos információkért tekintse meg a [Virtual WAN-partnerek és helyek](virtual-wan-locations-partners.md) című cikket. Minden központ legfeljebb 10 000 távoli felhasználói kapcsolatot, 1000 elágazási kapcsolatot, négy ExpressRoute-áramkört és legfeljebb 500 virtuális hálózati kapcsolatot támogat. A távoli felhasználók méretezése közben, ha bármilyen kérdése van, ne habozzon azurevirtualwan@microsoft.comsegítséget kérni e-mail küldésével a rendszernek. Ha technikai támogatásra van szüksége, mindenképpen nyisson meg egy támogatási jegyet az Azure Portalról, és a súgó útban lesz.

## <a name="faq"></a><a name="faq"></a>GYIK

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Következő lépések

A Virtual WAN-ról a [Virtual WAN áttekintése című témakörben olvashat bővebben.](virtual-wan-about.md)