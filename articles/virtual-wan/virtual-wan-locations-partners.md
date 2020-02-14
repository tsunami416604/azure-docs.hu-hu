---
title: Azure-beli virtuális WAN-partnerek és-helyszínek | Microsoft Docs
description: Ez a cikk az Azure-beli virtuális WAN-partnerek és a központ helyeinek listáját tartalmazza.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 68379fd2334e517df4011c05bb32543f7ce4a173
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190406"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuális WAN-partnerek és virtuális központ helyei

Ez a cikk a virtuális WAN által támogatott régiókkal és partnerekkel kapcsolatos információkat nyújt a virtuális központhoz való kapcsolódáshoz.

Az Azure Virtual WAN egy olyan hálózati szolgáltatás, amely optimalizált és automatizált ágak közötti kapcsolódást biztosít az Azure-on keresztül. A Virtual WAN segítségével ágeszközöket csatlakoztathat és konfigurálhat az Azure-ral való kommunikációra. Ezt manuálisan is megteheti, vagy egy virtuális WAN-partneren keresztül a szolgáltatói eszközök használatával. A partneri eszközök lehetővé teszik a könnyű használatot, a kapcsolatok egyszerűsítését és a konfiguráció felügyeletét.

A helyszíni eszköz kapcsolatát a virtuális központ automatizált módon hozza meg. A virtuális központ egy Microsoft által felügyelt virtuális hálózat. Az elosztó különféle szolgáltatásvégpontokat tartalmaz a helyszíni hálózatból (vpnsite) induló kapcsolatok biztosításához. Régiónként csak egy hubhoz tartozhat.

## <a name="automation"></a>Automatizálás a kapcsolati partnerektől

Az Azure Virtual WAN-hoz csatlakozó eszközökhöz beépített Automation szükséges a kapcsolódáshoz. Ez általában az Eszközkezelő felhasználói felületén (vagy ezzel egyenértékű) van beállítva, amely a VPN-ág eszköz és egy Azure Virtual hub VPN-végpont (VPN-átjáró) közötti kapcsolat és konfiguráció kezelését állítja be.

A következő magas szintű automatizálás van beállítva az Eszközkezelőben/felügyeleti központban:

* Megfelelő engedélyek az eszköz számára az Azure Virtual WAN-erőforráscsoport eléréséhez
* Ág-eszköz feltöltése az Azure Virtual WAN-ba
* Azure-kapcsolati információk automatikus letöltése
* Helyszíni ág-eszköz konfigurálása 

Néhány csatlakozási partner kiterjesztheti az automationt, hogy az tartalmazza az Azure Virtual hub VNet és VPN Gateway létrehozását. Ha többet szeretne megtudni az automatizálásról, tekintse meg [a virtuális WAN-partnerek automatizálási irányelveit](virtual-wan-configure-automation-providers.md).

## <a name="partners"></a>Kapcsolódás partnereken keresztül

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

A következő partnereink a közeljövőben: Arista, Aruba HPE, Cisco Systems, F5 Networks, Open Systems, Oracle SD-WAN, SharpLink és VMWare Velocloud.

## <a name="locations"></a>Helyek

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Következő lépések

* A virtuális WAN-ról további információt a [virtuális WAN gyakori kérdések](virtual-wan-faq.md)című témakörben talál.

* További információ az Azure Virtual WAN-kapcsolat automatizálásával kapcsolatban: [automatizálási irányelvek a virtuális WAN-partnerek számára](virtual-wan-configure-automation-providers.md).
