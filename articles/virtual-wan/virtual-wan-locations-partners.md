---
title: Az Azure virtuális WAN helyek-partnerek |} A Microsoft Docs
description: Ez a cikk az Azure virtuális WAN-partnerek és hub helyek listáját tartalmazza.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: f38cd0565b2e90fe0803d8e815c622e22e954a18
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409703"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuális WAN-partnerek és a virtuális központ helye

Ez a cikk tájékoztatást virtuális WAN támogatott régiók és a partnerek virtuális központ kapcsolatot.

Az Azure Virtual WAN egy olyan hálózati szolgáltatás, amely optimalizált és automatizált ágak közötti kapcsolódást biztosít az Azure-on keresztül. A Virtual WAN segítségével ágeszközöket csatlakoztathat és konfigurálhat az Azure-ral való kommunikációra. Ez elvégezhető manuálisan vagy egy virtuális WAN-partneren keresztül szolgáltató eszközök használatával. Partner eszközökkel, könnyen használható, kapcsolat és a konfigurációkezelés egyszerűsítése lehetővé teszi.

A helyszíni eszköz kapcsolat jön létre, a virtuális központ automatikus módon átalakítson. Egy virtuális központtal egy Microsoft által felügyelt virtuális hálózatot. Az elosztó különféle szolgáltatásvégpontokat tartalmaz a helyszíni hálózatból (vpnsite) induló kapcsolatok biztosításához. Régiónként csak egy eseményközpont rendelkezhet.

## <a name="automation"></a>Automation kapcsolati partnerek

Azure virtuális WAN csatlakozó eszközökön való kapcsolódáshoz a beépített automation rendelkezik. Ez általában be felfelé a Eszközfelügyelet – felhasználói felület (vagy egyenértékű), amely állítja be a kapcsolat és a konfigurációkezelés egy Azure-beli virtuális központ VPN-végpontnak (VPN-átjáró) ágat VPN-eszköz között.

A következő magas szintű automatizálási van beállítva, az eszköz konzol/felügyeleti központban:

* Az Azure virtuális WAN erőforráscsoport elérését az eszköz számára a megfelelő engedélyeket
* Ág eszköz tölthet fel az Azure virtuális WAN
* Az Azure hálózati adatai automatikus letöltése
* Az a helyszíni ág eszköz konfigurálása 

Néhány kapcsolati partnerek meghosszabbíthatja az automatizálás, többek között az Azure virtuális agyi virtuális hálózat és VPN-átjáró létrehozása. Ha szeretne többet megtudni az automation, [konfigurálása Automation – WAN partnerek](virtual-wan-configure-automation-providers.md).

## <a name="partners"></a>Kapcsolódás a partnerek keresztül

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

## <a name="locations"></a>helyek

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>További lépések

* Virtuális WAN kapcsolatos további információkért lásd: a [virtuális WAN – gyakori kérdések](virtual-wan-faq.md).

* Hogyan automatizálható a kapcsolatot az Azure virtuális WAN kapcsolatos további információkért lásd: [virtuális WAN-partnerek – automatizálása](virtual-wan-configure-automation-providers.md).
