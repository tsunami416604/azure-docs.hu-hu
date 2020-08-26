---
title: Mi az Azure Firewall?
description: Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperfq1
ms.date: 08/25/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 914f267edd5a8168fc11af7186e322c306718a4a
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852640"
---
# <a name="what-is-azure-firewall"></a>Mi az Azure Firewall?

![ICSA-tanúsítvány](media/overview/icsa-cert-firewall-small.png)

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Egy teljes mértékben állapotalapú tűzfalszolgáltatás, beépített magas rendelkezésre állással és korlátlan felhőméretezhetőséggel.

![Tűzfal áttekintése](media/overview/firewall-threat.png)

Központilag hozhatja létre, érvényesítheti és naplózhatja az alkalmazás- és hálózatelérési szabályzatokat az előfizetésekre és a virtuális hálózatokra vonatkozóan. Az Azure Firewall statikus nyilvános IP-címet használ a virtuális hálózat erőforrásaihoz, így a külső tűzfalak azonosíthatják a virtuális hálózatból érkező forgalmat.  A szolgáltatás teljesen integrálva van az Azure Monitorral a naplózás és az elemzés érdekében.

## <a name="features"></a>Funkciók

Azure Firewall szolgáltatásokkal kapcsolatos további tudnivalókért lásd: [Azure Firewall szolgáltatások](features.md).

## <a name="known-issues"></a>Ismert problémák

Az Azure Firewall az alábbi ismert hibákkal rendelkezik:

|Probléma  |Leírás  |Kockázatcsökkentés  |
|---------|---------|---------|
A nem TCP/UDP-protokollokra (például ICMP) vonatkozó hálózati szűrési szabályok nem működnek az internetre irányuló forgalom esetében|A nem TCP/UDP protokollok hálózati szűrési szabályai nem működnek a SNAT a nyilvános IP-címével. A nem TCP/UDP-protokollok a küllők alhálózatai és a virtuális hálózatok között támogatottak.|Az Azure Firewall a Standard Load Balancert használja, [amely jelenleg nem támogatja a forráshálózati címfordítást az IP-protokollokon](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). A forgatókönyv egy későbbi kiadásban való támogatásának lehetőségeit vizsgálja.|
|A PowerShell és a CLI nem támogatja az ICMP-t|A Azure PowerShell és a CLI nem támogatja az ICMP-t érvényes protokollként a hálózati szabályokban.|Az ICMP protokollt a Portálon és a REST API is használhatja protokollként. Hamarosan felvesszük az ICMP-t a PowerShellben és a CLI-ben.|
|Az FQDN-címkék protokoll: port megadását igénylik|Az FQDN-címkékkel rendelkező alkalmazás-szabályok port: protokoll-definíciót igényelnek.|A port:protokoll értékként használhat **https**-t. Azon dolgozunk, hogy ezt a mezőt nem kötelező megadni, ha FQDN-címkéket használunk.|
|A tűzfal más erőforráscsoporthoz vagy előfizetésbe való áthelyezése nem támogatott|A tűzfal más erőforráscsoporthoz vagy előfizetésbe való áthelyezése nem támogatott.|A funkció támogatása a közúti térképen is elérhető. Ahhoz, hogy egy tűzfalat áthelyezzen másik erőforráscsoportba vagy előfizetésbe, először törölnie kell az aktuális példányt, és újra létre kell hoznia az új erőforráscsoportban vagy előfizetésben.|
|A fenyegetések felderítésével kapcsolatos riasztások maszkolása is lehetséges|A kimenő szűréshez használt 80/443-as célként megadott hálózati szabályok a fenyegetések felderítésére vonatkozó riasztásokat észlelnek, ha csak riasztás módra vannak konfigurálva.|Hozzon létre kimenő szűrést az 80/443-hoz az alkalmazási szabályok használatával. Vagy módosítsa a fenyegetés intelligencia módot a **riasztás és a Megtagadás**értékre.|
|Azure Firewall DNAT nem működik a magánhálózati IP-címekhez|Azure Firewall DNAT-támogatás csak az internetes kimenő/bejövő forgalomra korlátozódik. A DNAT jelenleg nem működik a magánhálózati IP-címekhez. Tegyük fel például, hogy küllős volt.|Ez egy aktuális korlátozás.|
|Nem lehet eltávolítani az első nyilvános IP-konfigurációt|Minden Azure Firewall nyilvános IP-cím hozzá van rendelve egy *IP-konfigurációhoz*.  Az első IP-konfiguráció a tűzfal központi telepítése során lesz hozzárendelve, és általában a tűzfal alhálózatára mutató hivatkozást is tartalmaz (kivéve, ha explicit módon másképpen van konfigurálva a sablon központi telepítésen keresztül). Ezt az IP-konfigurációt nem lehet törölni, mert a tűzfal lefoglalása megtörtént. Továbbra is módosíthatja vagy eltávolíthatja az IP-konfigurációhoz társított nyilvános IP-címet, ha a tűzfalon legalább egy másik nyilvános IP-cím használható.|Ez az elvárt működés.|
|A rendelkezésre állási zónák konfigurálása csak az üzembe helyezés során lehetséges.|A rendelkezésre állási zónák konfigurálása csak az üzembe helyezés során lehetséges. A tűzfal telepítése után nem konfigurálható Availability Zones.|Ez az elvárt működés.|
|SNAT a bejövő kapcsolatokon|A DNAT kívül a tűzfal nyilvános IP-címén (bejövő) keresztül létesített kapcsolatok a címfordítást egyikéhez tartoznak. Ez a követelmény ma (aktív/aktív NVA esetén is) biztosítja a szimmetrikus útválasztást.|A HTTP/S eredeti forrásának megőrzése érdekében érdemes lehet [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) -fejléceket használni. Például olyan szolgáltatást használhat, mint például az [Azure bejárati ajtó](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) vagy az [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) a tűzfal előtt. A WAF az Azure bejárati ajtajának részeként is hozzáadhatja a tűzfalhoz.
|Az SQL FQDN szűrése csak proxy módban támogatott (1433-es port)|Azure SQL Database, Azure SQL Data Warehouse és Azure SQL felügyelt példány esetén:<br><br>Az előzetes verzióban az SQL FQDN-szűrés csak proxy módban támogatott (1433-es port).<br><br>Azure SQL-IaaS esetén:<br><br>Ha nem szabványos portokat használ, megadhatja ezeket a portokat az alkalmazási szabályokban.|SQL-ben átirányítási módban (az alapértelmezett, ha az Azure-on keresztül csatlakozik), ehelyett a Azure Firewall hálózati szabályok részeként szűrheti a hozzáférést az SQL-szolgáltatás címkével.
|A 25-ös TCP-porton nem engedélyezett a kimenő forgalom| A 25-ös TCP-portot használó kimenő SMTP-kapcsolatok le vannak tiltva. A 25-ös port elsődlegesen a nem hitelesített e-mailek kézbesítéséhez használatos. Ez a virtuális gépek alapértelmezett platform-viselkedése. További információ: [a kimenő SMTP-kapcsolatokkal kapcsolatos problémák elhárítása az Azure-ban](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). A virtuális gépektől eltérően azonban jelenleg nem lehet engedélyezni ezt a funkciót Azure Firewallon. Megjegyzés: Ha a hitelesített SMTP-portot (587-as port) vagy az SMTP-t nem a 25-ös porton keresztül szeretné engedélyezni, győződjön meg arról, hogy a hálózati szabály konfigurálása nem egy alkalmazási szabály, mivel az SMTP-vizsgálat jelenleg nem támogatott.|Kövesse az e-mailek küldésére vonatkozó ajánlott módszert az SMTP hibaelhárítási cikkében leírtak szerint. Vagy zárja ki azt a virtuális gépet, amelynek a kimenő SMTP-hozzáférésre van szüksége az alapértelmezett útvonalról a tűzfalra. Ehelyett konfigurálja a kimenő hozzáférést közvetlenül az internethez.
|Az aktív FTP nem támogatott|Az aktív FTP szolgáltatás le van tiltva Azure Firewall az FTP-PORT parancs használatával történő FTP-visszafordulási támadásokkal szembeni védelem érdekében.|Ehelyett használhatja a passzív FTP-t. A tűzfalon továbbra is explicit módon meg kell nyitnia a 20. és a 21. TCP-portot.
|Az SNAT-portok kihasználtsága metrika 0%-ot mutat|A Azure Firewall SNAT-kihasználtsági mérőszáma 0%-os használatot is megjeleníthet, még akkor is, ha SNAT-portok vannak használatban. Ebben az esetben a metrika a tűzfal állapota metrika részeként való használata helytelen eredményt ad.|Ezt a problémát megjavítottuk, és az éles környezetbe való bevezetésük a 2020-es májusira irányul. Bizonyos esetekben a tűzfal újratelepítése megoldja a problémát, de nem konzisztens. Közbenső megkerülő megoldásként csak a tűzfal állapota alapján keresse meg a *status = lecsökkentett állapotot*, nem a *status = sérült*állapotot. A port kimerülése *csökkentett teljesítményű*fog megjelenni. A *nem kifogástalan* állapot a jövőbeli használatra van fenntartva, ha a további mérőszámok hatással vannak a tűzfal állapotára.
|A DNAT nem támogatott a kényszerített bújtatás használata esetén|A kényszerített bújtatással telepített tűzfalak esetében az aszimmetrikus útválasztás miatt nem támogatott a bejövő hozzáférés az internetről.|Ezt az aszimmetrikus útválasztás miatt tervezték meg. A bejövő kapcsolatok visszatérési útvonala a helyszíni tűzfalon megy keresztül, amely még nem látta el a kapcsolatot.
|A kimenő passzív FTP nem működik több nyilvános IP-címmel rendelkező tűzfalak esetében|A passzív FTP különböző kapcsolatokat hoz létre a vezérlési és az adatcsatornákhoz. Ha egy több nyilvános IP-címmel rendelkező tűzfal kimenő adatokat küld, véletlenszerűen kiválasztja az egyik nyilvános IP-címét a forrás IP-címéhez. Az FTP meghiúsul, ha az adat-és vezérlési csatornák eltérő forrás IP-címeket használnak.|A rendszer explicit SNAT-konfigurációt tervez. Addig is érdemes egyetlen IP-címet használni ebben a helyzetben.|
|A NetworkRuleHit metrika nem tartalmaz protokoll-dimenziót|A ApplicationRuleHit metrika lehetővé teszi a szűrési alapú protokoll használatát, de ez a képesség hiányzik a megfelelő NetworkRuleHit-metrikában.|A rendszer kivizsgálja a javítást.|
|A 64000 és 65535 közötti portokkal rendelkező NAT-szabályok nem támogatottak|A Azure Firewall engedélyezi az 1-65535 tartomány bármely portját a hálózati és az alkalmazási szabályokban, azonban a NAT-szabályok csak a 1-63999 tartományba tartozó portokat támogatják.|Ez egy aktuális korlátozás.
|A konfigurációs frissítések átlagosan öt percet vehetnek igénybe|Egy Azure Firewall konfigurációs frissítés átlagosan három-öt percet vehet igénybe, és a párhuzamos frissítések nem támogatottak.|A rendszer kivizsgálja a javítást.|
|Azure Firewall SNI TLS-fejléceket használ a HTTPS-és MSSQL-forgalom szűréséhez|Ha a böngésző vagy a kiszolgáló szoftver nem támogatja a kiszolgálónév-jelző (SNI) bővítményt, nem fog tudni csatlakozni a Azure Firewallon keresztül.|Ha a böngésző vagy a kiszolgáló szoftvere nem támogatja a SNI-t, akkor az alkalmazás szabálya helyett hálózati szabály használatával is vezérelheti a kapcsolódást. Tekintse meg a SNI-t támogató szoftverek [kiszolgálónév jelzése](https://wikipedia.org/wiki/Server_Name_Indication) .|
|Az egyéni DNS (előzetes verzió) nem működik a kényszerített bújtatással|Ha a kényszerített bújtatás engedélyezve van, az egyéni DNS (előzetes verzió) nem működik.|A rendszer kivizsgálja a javítást.

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon](tutorial-firewall-deploy-portal.md)
- [Azure Firewall üzembe helyezése sablon használatával](deploy-template.md)
- [Azure Firewall-tesztkörnyezet létrehozása](scripts/sample-create-firewall-test.md)
