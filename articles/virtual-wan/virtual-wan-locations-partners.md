---
title: Azure Virtual WAN partnerek és helyszínek | Microsoft dokumentumok
description: Ez a cikk az Azure Virtual WAN-partnerek és a központi helyek listáját tartalmazza.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 3976f4114df6222d34f19e2bb44fd05d53057321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123265"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuális WAN-partnerek és virtuális központi helyek

Ez a cikk a Virtual WAN által támogatott régiókról és partnerekről nyújt tájékoztatást a virtuális központba való kapcsolódáshoz.

Az Azure Virtual WAN egy olyan hálózati szolgáltatás, amely optimalizált és automatizált ágak közötti kapcsolódást biztosít az Azure-on keresztül. A Virtual WAN segítségével ágeszközöket csatlakoztathat és konfigurálhat az Azure-ral való kommunikációra. Ez történhet manuálisan, vagy a szolgáltató eszközök használatával egy virtuális WAN-partneren keresztül. A partnereszközök használata lehetővé teszi a könnyű használatot, a kapcsolat egyszerűsítését és a konfigurációkezelést.

A helyszíni eszközről a virtuális központhoz való automatikus kapcsolat jön létre. A virtuális központ a Microsoft által felügyelt virtuális hálózat. Az elosztó különféle szolgáltatásvégpontokat tartalmaz a helyszíni hálózatból (vpnsite) induló kapcsolatok biztosításához. Régiónként csak egy központ lehet.

## <a name="automation-from-connectivity-partners"></a><a name="automation"></a>Automatizálás a kapcsolódási partnerektől

Az Azure Virtual WAN-hoz csatlakozó eszközök beépített automatizálással rendelkeznek a csatlakozáshoz. Ez általában az eszközfelügyeleti felhasználói felületen (vagy azzal egyenértékű) van beállítva, amely beállítja a VPN-fiókeszköz közötti kapcsolat- és konfigurációkezelést egy Azure Virtual Hub VPN-végpontra (VPN-átjáró).

Az eszközkonzolon/felügyeleti központban a következő magas szintű automatizálás van beállítva:

* Az Azure Virtual WAN resource Group eléréséhez szükséges megfelelő engedélyek az eszköz számára
* Branch Eszköz feltöltése az Azure Virtual WAN-ba
* Az Azure-kapcsolati adatok automatikus letöltése
* A helyszíni ágeszköz konfigurációja 

Egyes kapcsolódási partnerek kiterjeszthetik az automatizálást az Azure Virtual Hub virtuális hálózat és a VPN-átjáró létrehozására. Ha többet szeretne tudni az automatizálásról, olvassa el [a Virtual WAN-partnerek automatizálási irányelvei című témakört.](virtual-wan-configure-automation-providers.md)

## <a name="connectivity-through-partners"></a><a name="partners"></a>Kapcsolódás a partnereken keresztül

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

A következő partnerek szerepelnek a közeljövőben tervezett ütemtervünkben: 128 Technologies, Arista, Aruba HPE, Cisco Systems, F5 Networks, Open Systems, Oracle SD-WAN, SharpLink és VMWare Velocloud.

## <a name="locations"></a><a name="locations"></a>Helyek

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>További lépések

* A Virtual WAN-ról további információt a Virtual WAN GYQ című [témakörben talál.](virtual-wan-faq.md)

* Az Azure Virtual WAN-nal való kapcsolat automatizálásáról a [Virtual WAN-partnerek automatizálási irányelvei](virtual-wan-configure-automation-providers.md)című témakörben talál további információt.
