---
title: Az Azure virtuális WAN helyek-partnerek |} A Microsoft Docs
description: Ez a cikk az Azure virtuális WAN-partnerek és hub helyek listáját tartalmazza
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: 76542392522ff14642ed7ccc6de8ed543d66513f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984993"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuális WAN-partnerek és a virtuális központ helye

Ez a cikk tájékoztatást virtuális WAN által támogatott régiók és előnyben részesített partnerek virtuális központ kapcsolatot.

Az Azure Virtual WAN egy olyan hálózati szolgáltatás, amely optimalizált és automatizált ágak közötti kapcsolódást biztosít az Azure-on keresztül. A Virtual WAN segítségével ágeszközöket csatlakoztathat és konfigurálhat az Azure-ral való kommunikációra. Ez elvégezhető manuálisan vagy egy virtuális WAN előnyben részesített partneren keresztül előnyben részesített szolgáltató eszközök használatával. Partnereink eszközök használata lehetővé teszi, könnyen használható, kapcsolat és a konfigurációkezelés egyszerűsítése. A helyszíni eszköz kapcsolat jön létre, a virtuális központ automatikus módon átalakítson. Egy virtuális központtal egy Microsoft által felügyelt virtuális hálózatot. Az elosztó különféle szolgáltatásvégpontokat tartalmaz a helyszíni hálózatból (vpnsite) induló kapcsolatok biztosításához. Régiónként csak egy eseményközpont rendelkezhet.

## <a name="regions"></a>Régiók

Támogatott és az elérhető régiók listája, a következők:

|Geopolitikai régió | Azure-régiók|
|---|---|
|Észak-Amerika | USA keleti régiója, USA nyugati régiója, USA 2. keleti régiója, USA 2. nyugati régiója, USA középső régiója, USA déli középső régiója, USA északi középső régiója, USA középnyugati régiója, Közép-Kanada, Kelet-Kanada |
|Dél-Amerika |Dél-Brazília |
| Európa | Közép-Franciaország, Dél-Franciaország, Észak-Európa, Nyugat-Európa, az Egyesült Királyság nyugati régiója, az Egyesült Királyság déli régiója |
| Ázsia | Kelet-Ázsia, Délkelet-Ázsia |
| Japán  | Nyugat-Japán, Kelet-Japán |
| Ausztrália | Délkelet-Ausztrália, Kelet-Ausztrália | 
| Ausztrál kormányzati | Ausztrália középső régiója, Ausztrália 2. középső régiója |
| India | Nyugat-India, Közép-India, Dél-India |
| Dél-Korea | Korea középső régiója, Korea déli régiója | 

## <a name="automation-from-connectivity-partners"></a>Automation kapcsolati partnerek

Ez a szakasz ismerteti a kapcsolatszolgáltatók az automation magas szintű részleteit.

Azure virtuális WAN csatlakozó eszközökön való kapcsolódáshoz a beépített automation rendelkezik. Ez általában be felfelé a Eszközfelügyelet – felhasználói felület (vagy egyenértékű), amely állítja be a kapcsolat és a konfigurációkezelés egy Azure-beli virtuális központ VPN-végpontnak (VPN-átjáró) ágat VPN-eszköz között.

A következő magas szintű automatizálási van beállítva, az eszköz konzol/felügyeleti központban:

* Az Azure virtuális WAN erőforráscsoport elérését az eszköz számára a megfelelő engedélyeket
* Ág eszköz tölthet fel az Azure virtuális WAN
* Az Azure hálózati adatai automatikus letöltése 
* Az a helyszíni ág eszköz konfigurálása 

Néhány kapcsolati partnerek meghosszabbíthatja az automatizálás, többek között az Azure virtuális agyi virtuális hálózat és VPN-átjáró létrehozása. Ha szeretne többet megtudni az automation, [konfigurálása Automation – WAN partnerek](virtual-wan-configure-automation-providers.md).

## <a name="connectivity-through-preferred-partners"></a>Kapcsolódás előnyben részesített partnerek keresztül

Ha a fiókiroda eszköz partner nem szerepel az alábbi szakaszban, lépjen kapcsolatba a fiókiroda eszköz szolgáltató, és őket lépjen kapcsolatba velünk az e-mail azurevirtualwan@microsoft.com.

Ellenőrizheti, hogy a következő hivatkozásokon további információ az előnyben részesített partnerek által kínált szolgáltatásokkal kapcsolatban. 

|Előnyben részesített partnerek|
|---|
|[Barracuda Networks](https://www.barracuda.com/AzurevWAN)|
| [A Check Point](https://www.checkpoint.com/solutions/microsoft-azure-virtual-wan/) |
| [A Citrix](https://www.citrix.com/global-partners/microsoft/sd-wan-for-azure-virtual-wan.html)|
|[Netfoundry](https://netfoundry.io/solutions/netfoundry-for-microsoft-azure-virtual-wan/)|
|[Palo Alto Networks](https://researchcenter.paloaltonetworks.com/2018/09/azure-vwan-integration/) |
|[Riverbed technológia](https://www.riverbed.com/go/steelconnect-azurewan.html)|
|[128 technológia](https://www.128technology.com/partners/azure) |

## <a name="next-steps"></a>További lépések

Virtuális WAN kapcsolatos további információkért lásd: a [virtuális WAN – gyakori kérdések](virtual-wan-faq.md).

Hogyan automatizálható a kapcsolatot az Azure virtuális WAN kapcsolatos további információkért lásd: [virtuális WAN-partnerek – automatizálása](virtual-wan-configure-automation-providers.md).
