---
title: Azure-beli virtuális WAN-partnerek és-helyszínek | Microsoft Docs
description: Ez a cikk az Azure-beli virtuális WAN-partnerek és a központ helyeinek listáját tartalmazza.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 188bc14de502640ae05f1d1eb55330d86cee50b4
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621479"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuális WAN-partnerek és virtuális központ helyei

Ez a cikk a virtuális WAN által támogatott régiókkal és partnerekkel kapcsolatos információkat nyújt a virtuális központhoz való kapcsolódáshoz.

Az Azure Virtual WAN egy olyan hálózati szolgáltatás, amely optimalizált és automatizált ágak közötti kapcsolódást biztosít az Azure-on keresztül. A Virtual WAN segítségével ágeszközöket csatlakoztathat és konfigurálhat az Azure-ral való kommunikációra. Ezt manuálisan is megteheti, vagy egy virtuális WAN-partneren keresztül a szolgáltatói eszközök használatával. A partneri eszközök lehetővé teszik a könnyű használatot, a kapcsolatok egyszerűsítését és a konfiguráció felügyeletét.

A helyszíni eszköz kapcsolatát a virtuális központ automatizált módon hozza meg. A virtuális központ egy Microsoft által felügyelt virtuális hálózat. Az elosztó különféle szolgáltatásvégpontokat tartalmaz a helyszíni hálózatból (vpnsite) induló kapcsolatok biztosításához. Régiónként csak egy hubhoz tartozhat.

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>Ág IPSec-kapcsolatának automatizálása a partnerektől

Az Azure Virtual WAN-hoz csatlakozó eszközökhöz beépített Automation szükséges a kapcsolódáshoz. Ez általában az Eszközkezelő felhasználói felületén (vagy ezzel egyenértékű) van beállítva, amely a VPN-ág eszköz és egy Azure Virtual hub VPN-végpont (VPN-átjáró) közötti kapcsolat és konfiguráció kezelését állítja be.

A következő magas szintű automatizálás van beállítva az Eszközkezelőben/felügyeleti központban:

* Megfelelő engedélyek az eszköz számára az Azure Virtual WAN-erőforráscsoport eléréséhez
* Ág-eszköz feltöltése az Azure Virtual WAN-ba
* Azure-kapcsolati információk automatikus letöltése
* Helyszíni ág-eszköz konfigurálása 

Néhány csatlakozási partner kiterjesztheti az automationt, hogy az tartalmazza az Azure Virtual hub VNet és VPN Gateway létrehozását. Ha többet szeretne megtudni az automatizálásról, tekintse meg [a virtuális WAN-partnerek automatizálási irányelveit](virtual-wan-configure-automation-providers.md).

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>Ág IPSec-kapcsolati partnerei

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

A következő partnereink a vállalatok közötti, a partner-eszköz és az Azure virtuális WAN VPN-átjárók közötti IPsec-kapcsolat automatizálására szolgáló munkaterületet jelző, a vállalat által aláírt használati feltételeken alapuló ütemtervet mutatnak: 128 Technologies, Arista, F5 Networks, Oracle SD-WAN (Torda) és SharpLink.

## <a name="partners-with-integrated-virtual-hub-offerings"></a>Integrált virtuális hub-ajánlatokat tartalmazó partnerek
Az automatikus fiókirodai IPSec-kapcsolat mellett egyes partnerek olyan **hálózati virtuális berendezéseket (NVA)** is kínálnak, amelyek közvetlenül az Azure Virtual WAN-hubhoz integrálhatók.  Ez lehetővé teszi, hogy az ügyfelek az ág kapcsolataikat egy kompatibilis, harmadik féltől származó berendezéssel szakítsák meg a virtuális központban.  

A virtuális WAN-központban NVA kínáló partnereknek a következőket kell tenniük:

* Implementálták az IPSec-kapcsolat automatizálását a fiókirodából, és az Azure Virtual WAN hub-ra felkészítettük a NVA ajánlatot.
* Az Azure Marketplace-en rendelkezésre áll egy meglévő hálózati virtuális berendezési ajánlat.

Ha Ön partner, és kérdései vannak a virtuális központ által felügyelt NVA kapcsolatban, kérjük, lépjen kapcsolatba velünk e-mailben a következő címre: vwannvaonboarding@microsoft.com

## <a name="integrated-virtual-hub-nva-partners"></a>Integrált Virtual hub NVA-partnerek
Ezek a partnerek **felügyelt alkalmazási** ajánlatokat biztosítanak, amelyek mostantól elérhetők a virtuális WAN-hubhoz való üzembe helyezéshez.

|Partnerek|Konfiguráció/útmutató/üzembe helyezési útmutató|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Barracuda CloudGen WAN üzembe helyezési útmutató](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[Cisco Cloud Service router (CSR) VWAN](https://aka.ms/ciscoMarketPlaceOffer)| A Cisco Cloud Services (CSR) WAN és a VWAN hub nyilvános előzetese során a Cisco megköveteli, hogy a végfelhasználók a Cisco EFT (korai mező próbaverziója) ügyfelet regisztrálják a vwan_public_preview@external.cisco.com VManage üzembe helyezési útmutatójának elküldésével és a kérések megadásával. |

A következő partnerek a NVA a virtuális központhoz a közeljövőben: Aviatrix, Citrix, VeloCloud és Versed Networks.

## <a name="locations"></a><a name="locations"></a>Helyek

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>További lépések

* A virtuális WAN-ról további információt a [virtuális WAN gyakori kérdések](virtual-wan-faq.md)című témakörben talál.

* További információ az Azure Virtual WAN-kapcsolat automatizálásával kapcsolatban: [automatizálási irányelvek a virtuális WAN-partnerek számára](virtual-wan-configure-automation-providers.md).
