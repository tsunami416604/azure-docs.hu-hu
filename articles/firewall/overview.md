---
title: Mi az Azure Firewall?
description: Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 04/07/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 951396afc95a215a6ff9f4885f83fcdf6efdeb72
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810332"
---
# <a name="what-is-azure-firewall"></a>Mi az Azure Firewall?

![ICSA-tanúsítvány](media/overview/icsa-cert-firewall-small.png)

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Egy teljes mértékben állapotalapú tűzfalszolgáltatás, beépített magas rendelkezésre állással és korlátlan felhőméretezhetőséggel.

![Tűzfal áttekintése](media/overview/firewall-threat.png)

Központilag hozhatja létre, érvényesítheti és naplózhatja az alkalmazás- és hálózatelérési szabályzatokat az előfizetésekre és a virtuális hálózatokra vonatkozóan. Az Azure Firewall statikus nyilvános IP-címet használ a virtuális hálózat erőforrásaihoz, így a külső tűzfalak azonosíthatják a virtuális hálózatból érkező forgalmat.  A szolgáltatás teljesen integrálva van az Azure Monitorral a naplózás és az elemzés érdekében.

Az Azure Firewall az alábbi szolgáltatásokat kínálja:

## <a name="built-in-high-availability"></a>Beépített magas rendelkezésre állás

A magas rendelkezésre állás be van építve, így nincs szükség további terheléselosztókra, és nincs mit konfigurálnia.

## <a name="availability-zones"></a>Rendelkezésre állási zónák

Az Azure Firewall konfigurálható a központi telepítés során, hogy több rendelkezésre állási zónára is kiterjedjen a nagyobb rendelkezésre állás érdekében. A rendelkezésre állási zónák esetén a rendelkezésre állás 99,99%-ra nő. További információt az Azure firewall [szolgáltatásiszint-szerződésében (SLA) talál.](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) A 99,99%-os rendelkezésre állási SLA két vagy több rendelkezésre állási zóna kiválasztásakor érhető el.

Az Azure Firewall-t is társíthatja egy adott zónához csak közelségi okokból, a 99.95%-os SLA szolgáltatás használatával.

A rendelkezésre állási zónában telepített tűzfalak nem járnak további költségekért. A rendelkezésre állási zónákhoz kapcsolódó bejövő és kimenő adatátvitelek további költségei azonban további költségekkel járnak. További információt a [Sávszélesség díjszabásának részletei című témakörben talál.](https://azure.microsoft.com/pricing/details/bandwidth/)

Az Azure tűzfal rendelkezésre állási zónái olyan régiókban érhetők el, amelyek támogatják a rendelkezésre állási zónákat. További információ: [Mik a rendelkezésre állási zónák az Azure-ban?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> A rendelkezésre állási zónák csak a központi telepítés során konfigurálhatók. Meglévő tűzfal nem konfigurálható rendelkezésre állási zónákra.

A rendelkezésre állási zónákról a [Mik azok a rendelkezésre állási zónák az Azure-ban?](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Korlátlan felhőalapú skálázhatóság

Az Azure Firewall akármeddig felskálázható a változó hálózati forgalom kezeléséhez, így a költségvetést nem szükséges a csúcsforgalomhoz igazítania.

## <a name="application-fqdn-filtering-rules"></a>Alkalmazások teljes tartománynevére vonatkozó szűrési szabályok

Korlátozhatja a kimenő HTTP/S-forgalmat vagy az Azure SQL-forgalmat (előzetes verzió) a teljesen minősített tartománynevek (FQDN) megadott listájára, beleértve a helyettesítő karaktereket is. Ez a funkció nem igényel SSL-megszüntetést.

## <a name="network-traffic-filtering-rules"></a>Hálózati forgalomra vonatkozó szűrési szabályok

Központilag hozhat létre *engedélyező* vagy *tiltó* hálózatszűrési szabályokat forrás és cél IP-cím, port és protokoll alapján. Az Azure Firewall teljes mértékben állapotalapú, így képes megkülönböztetni különböző típusú kapcsolatok érvényes csomagjait. A szabályok több előfizetésen és virtuális hálózaton érvényesíthetők és naplózhatók.

## <a name="fqdn-tags"></a>FQDN-címkék

Az FQDN-címkék megkönnyítik a jól ismert Azure-szolgáltatáshálózati forgalmat a tűzfalon keresztül. Tegyük fel például, hogy engedélyezni kívánja a Windows Update hálózati forgalmát a tűzfalon keresztül. Létrehozhat egy alkalmazásszabályt, és hozzáadhatja a Windows Update címkéjét. A Windows Update hálózati forgalma ezután akadálytalanul áthaladhat a tűzfalon.

## <a name="service-tags"></a>Szolgáltatáscímkék

A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége. Nem hozhat létre saját szolgáltatáscímkét, és nem adhatja meg, hogy mely IP-címek szerepeljenek a címkén. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket.

## <a name="threat-intelligence"></a>Fenyegetésészlelési intelligencia

A fenyegetésfelderítésen alapuló szűrés engedélyezhető a tűzfal számára, hogy figyelmeztesse és megtagadja az ismert rosszindulatú IP-címekről és tartományokról érkező/azokba irányuló forgalmat. Az IP-címek és tartományok a Microsoft Threat Intelligence hírcsatornából származnak.

## <a name="outbound-snat-support"></a>Kimenő SNAT-támogatás

A rendszer a kimenő virtuális hálózati forgalomhoz tartozó minden IP-címet lefordít az Azure Firewall nyilvános IP-címére (forráshálózati címfordítás, SNAT). Azonosíthatja és engedélyezheti a virtuális hálózatból a távoli internetes célhelyekre irányuló forgalmat. Az Azure Firewall nem snat, ha a cél IP egy privát IP-tartomány [iANA RFC 1918.](https://tools.ietf.org/html/rfc1918) 

Ha a szervezet nyilvános IP-címtartományt használ a magánhálózatokhoz, az Azure Firewall sNAT-ot használ az AzureFirewallSubnet tűzfal egyik privát IP-címére irányuló forgalom. Beállíthatja, hogy az Azure Firewall **ne** snat a nyilvános IP-címtartományban. További információ: [Azure Firewall SNAT private IP address ranges](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Bejövő DNAT-támogatás

A tűzfal nyilvános IP-címére irányuló bejövő internetes hálózati forgalom lefordításra kerül (célhálózati cím fordítása), és a virtuális hálózatok magánhálózati IP-címeire szűrve történik.

## <a name="multiple-public-ip-addresses"></a>Több nyilvános IP-cím

A tűzfalhoz több nyilvános IP-címet (legfeljebb 100) társíthat.

Ez a következő eseteket teszi lehetővé:

- **DNST** – Több szabványos portpéldányt is lefordíthat a háttérkiszolgálókra. Ha például két nyilvános IP-címmel rendelkezik, a 3389-es TCP-portot (RDP) mindkét IP-címhez lefordíthatja.
- **SNAT** - További portok állnak rendelkezésre a kimenő SNAT-kapcsolatokhoz, csökkentve az SNAT-port okának kimerülését. Ebben az időben az Azure Firewall véletlenszerűen kiválasztja a forrás nyilvános IP-címet használni a kapcsolatot. Ha a hálózaton van-e alsóbb rétegbeli szűrés, engedélyeznie kell a tűzfalhoz társított összes nyilvános IP-címet.

## <a name="azure-monitor-logging"></a>Azure Monitor-naplózás

Minden esemény integrálva van az Azure Monitorral, így archiválhatja a naplókat egy tárfiókba, eseményeket streamelhet az Event Hubra, vagy elküldheti őket az Azure Monitor naplóiba.

## <a name="certifications"></a>Tanúsítványok

Az Azure Firewall a Payment Card Industry (PCI), a Service Organization Controls (SOC), a International Organization for Standardization (ISO) és az ICSA Labs előírása. További információt az [Azure Firewall megfelelőségi tanúsítványai című témakörben talál.](compliance-certifications.md)


## <a name="known-issues"></a>Ismert problémák

Az Azure Firewall az alábbi ismert hibákkal rendelkezik:

|Probléma  |Leírás  |Kezelés  |
|---------|---------|---------|
A nem TCP/UDP-protokollokra (például ICMP) vonatkozó hálózati szűrési szabályok nem működnek az internetre irányuló forgalom esetében|A nem TCP/UDP protokollok hálózati szűrési szabályai nem működnek az SNAT-tal a nyilvános IP-címhez. A nem TCP/UDP-protokollok a küllők alhálózatai és a virtuális hálózatok között támogatottak.|Az Azure Firewall a Standard Load Balancert használja, [amely jelenleg nem támogatja a forráshálózati címfordítást az IP-protokollokon](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Megvizsgáljuk a lehetőségeket, hogy támogassa ezt a forgatókönyvet egy későbbi kiadásban.|
|A PowerShell és a CLI nem támogatja az ICMP-t|Az Azure PowerShell és a CLI nem támogatja az ICMP-t érvényes protokollként a hálózati szabályokban.|Továbbra is használható az ICMP protokollként a portálon és a REST API-n keresztül. Azon dolgozunk, hogy icmp-t adjunk hozzá a PowerShellhez és a CLI-hez.|
|Az FQDN-címkék protokoll: port megadását igénylik|Az FQDN-címkékkel rendelkező alkalmazásszabályok port: protokolldefiníciót igényelnek.|A port:protokoll értékként használhat **https**-t. Azon dolgozunk, hogy ezt a mezőt választhatóvá tegyük a teljes tartománynév-címkék használata esetén.|
|Tűzfal áthelyezése másik erőforráscsoportba vagy előfizetésbe nem támogatott|Tűzfal áthelyezése egy másik erőforráscsoportba vagy előfizetésbe nem támogatott.|Ezt a funkciót támogatja az útitervünkben. Ahhoz, hogy egy tűzfalat áthelyezzen másik erőforráscsoportba vagy előfizetésbe, először törölnie kell az aktuális példányt, és újra létre kell hoznia az új erőforráscsoportban vagy előfizetésben.|
|A fenyegetésfelderítési riasztások maszkosak lehetnek|A kimenő szűrési 80/443 céllal rendelkező hálózati szabályok csak riasztási módra konfigurálva a fenyegetésfelderítési riasztásokat maszkok.|Kimenő szűrés létrehozása a 80/443-hoz alkalmazásszabályok használatával. Vagy módosítsa a fenyegetésfelderítési módot **Riasztás és megtagadás**módra.|
|Az Azure Firewall csak névfeloldáshoz használja az Azure DNS-t|Az Azure Firewall csak Azure DNS-sel oldja fel a teljes tartománynokat. Az egyéni DNS-kiszolgáló nem támogatott. Nincs hatással a DNS-feloldás más alhálózatok.|Azon dolgozunk, hogy enyhítsük ezt a korlátozást.|
|Az Azure Firewall SNAT/DNAT nem működik privát IP-helyek esetén|Az Azure Firewall SNAT/DNAT támogatás az internetes kimenő forgalomra/be- és bejutásra korlátozódik. Az SNAT/DNAT jelenleg nem működik a privát IP-célok esetében. Például, beszélt beszélt.|Ez egy jelenlegi korlátozás.|
|Nem lehet eltávolítani az első nyilvános IP-konfigurációt|Minden Azure Tűzfal nyilvános IP-cím van rendelve egy *IP-konfigurációhoz.*  Az első IP-konfiguráció a tűzfal központi telepítése során van hozzárendelve, és általában a tűzfal alhálózatára mutató hivatkozást is tartalmaz (kivéve, ha a sablon telepítésén keresztül kifejezetten eltérően van konfigurálva). Ez az IP-konfiguráció nem törölhető, mert az a tűzfal lefoglalását lecsökkentené. Továbbra is módosíthatja vagy eltávolíthatja az IP-konfigurációhoz társított nyilvános IP-címet, ha a tűzfal legalább egy másik nyilvános IP-címmel rendelkezik.|Ez az elvárt működés.|
|A rendelkezésre állási zónák csak a központi telepítés során konfigurálhatók.|A rendelkezésre állási zónák csak a központi telepítés során konfigurálhatók. A tűzfal telepítése után nem konfigurálható a rendelkezésre állási zónák.|Ez az elvárt működés.|
|SNAT bejövő kapcsolatokon|A DNST mellett a tűzfalon keresztüli (bejövő) kapcsolatok a tűzfal egyik privát IP-címéhez snated.Addition to DNAT, connections via the firewall public IP address (inbound) are SNATed to one of the firewall private IP. Ez a követelmény ma (aktív/aktív nva-k esetében is) a szimmetrikus útválasztás biztosítása érdekében.|A HTTP/S eredeti forrásának megőrzéséhez érdemes [lehet XFF-fejléceket](https://en.wikipedia.org/wiki/X-Forwarded-For) használni. Például használjon egy szolgáltatást, például [az Azure bejárati ajtaját](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) vagy az [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) a tűzfal előtt. WaF-ot is hozzáadhat az Azure Bejárati ajtajához és láncolatához.
|Az SQL FQDN-szűrés támogatása csak proxy módban (1433-as port)|Az Azure SQL Database, az Azure SQL Data Warehouse és az Azure SQL felügyelt példány esetén:<br><br>Az előzetes verzió során az SQL FQDN-szűrés csak proxy módban (1433-as port) támogatott.<br><br>Az Azure SQL IaaS esetén:<br><br>Ha nem szabványos portokat használ, megadhatja ezeket a portokat az alkalmazásszabályokban.|Sql átirányítási módban, amely az alapértelmezett, ha az Azure-on belülről csatlakozik, ehelyett szűrheti a hozzáférést az SQL szolgáltatáscímke használatával az Azure Firewall hálózati szabályok részeként.
|A 25-ös TCP-port kimenő forgalma nem engedélyezett| A 25-ös TCP-portot használó kimenő SMTP-kapcsolatok le vannak tiltva. A 25-ös portot elsősorban a nem hitelesített e-mailek kézbesítéséhez használják. Ez a virtuális gépek alapértelmezett platformviselkedése. További információt a [Kimenő SMTP-kapcsolattal kapcsolatos problémák elhárítása az Azure-ban](../virtual-network/troubleshoot-outbound-smtp-connectivity.md)című témakörben talál. Azonban a virtuális gépekkel ellentétben jelenleg nem lehetséges ez a funkció az Azure Firewall.|Kövesse az ajánlott módszert az E-mail küldéséhez az SMTP hibaelhárítási cikkben dokumentált módon. Vagy zárja ki azt a virtuális gépet, amelynek kimenő SMTP-hozzáférést kell biztosítania az alapértelmezett útvonalról a tűzfalhoz, és ehelyett közvetlenül az internethez konfigurálja a kimenő hozzáférést.
|Az aktív FTP nem támogatott|Az aktív FTP le van tiltva az Azure Tűzfalon az FTP-port paranccsal történő FTP-visszafordulási támadások elleni védelem érdekében.|Használhatja passzív FTP helyett. A tűzfalon továbbra is kifejezetten meg kell nyitnia a 20-as és 21-es TCP-portokat.
|Az SNAT-port kihasználtsági mutatója 0%-ot mutat|Az Azure Firewall SNAT-port kihasználtsági metrikája 0%-os használatot mutathat még SNAT-portok használata esetén is. Ebben az esetben a metrika használata a tűzfal állapotmetrika részeként helytelen eredményt ad.|Ezt a problémát kijavították, és a termelésre való bevezetés 2020 májusára irányul. Bizonyos esetekben a tűzfal újratelepítése megoldja a problémát, de nem konzisztens. Köztes megoldásként csak a tűzfal állapotával keresse meg az *állapot=degradálódott*, az *állapot=nem kifogástalan.* Port kimerültség jelenik meg *a leromlott*. *Nem kifogástalan* van fenntartva a későbbi használatra, ha a több metrikák hatással van a tűzfal állapotát.
|A DNST nem támogatott a kényszerített bújtatás engedélyezve|A kényszerített bújtatásengedélyezéssel telepített tűzfalak az aszimmetrikus útválasztás miatt nem támogatják az internetről érkező bejövő hozzáférést.|Ez szándékosan az aszimmetrikus útválasztás miatt van. A bejövő kapcsolatok visszatérési útvonala a helyszíni tűzfalon keresztül történik, amely nem látta a létrehozott kapcsolatot.
|Kimenő passzív FTP nem működik a tűzfalak több nyilvános IP-címet.|A passzív FTP különböző kapcsolatokat hoz létre a vezérlő- és adatcsatornákhoz. Ha egy több nyilvános IP-címmel rendelkező tűzfal kimenő adatokat küld, véletlenszerűen kiválasztja az egyik nyilvános IP-címét a forrás IP-címhez. Az FTP sikertelen lesz, ha az adat- és vezérlőcsatornák különböző forrás IP-címeket használnak.|Explicit SNAT-konfiguráció t tervez. Addig is érdemes egyetlen IP-címet használni ebben a helyzetben.|

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon](tutorial-firewall-deploy-portal.md)
- [Azure Firewall üzembe helyezése sablon használatával](deploy-template.md)
- [Azure Firewall-tesztkörnyezet létrehozása](scripts/sample-create-firewall-test.md)
