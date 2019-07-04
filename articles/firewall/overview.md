---
title: Mi az Azure Firewall?
description: Az Azure Firewall szolgáltatásainak ismertetése.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 6/26/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 9a875f4450b700fc9db74b4402471e282f8e9dab
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442913"
---
# <a name="what-is-azure-firewall"></a>Mi az Azure Firewall?

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Teljes állapot-nyilvántartó tűzfal beépített magas rendelkezésre állás és méretezhetőség korlátlan felhőalapú szolgáltatásként is.

![Tűzfal áttekintése](media/overview/firewall-threat.png)

Központilag hozhatja létre, érvényesítheti és naplózhatja az alkalmazás- és hálózatelérési szabályzatokat az előfizetésekre és a virtuális hálózatokra vonatkozóan. Az Azure Firewall statikus nyilvános IP-címet használ a virtuális hálózat erőforrásaihoz, így a külső tűzfalak azonosíthatják a virtuális hálózatból érkező forgalmat.  A szolgáltatás teljesen integrálva van az Azure Monitorral a naplózás és az elemzés érdekében.

Az Azure Firewall az alábbi szolgáltatásokat kínálja:

## <a name="built-in-high-availability"></a>Beépített magas rendelkezésre állás

Magas rendelkezésre állású épül, így további terheléselosztók szükségesek, és semmit nem kell konfigurálnia.

## <a name="availability-zones-public-preview"></a>A rendelkezésre állási zónák (nyilvános előzetes verzió)

Az Azure tűzfal span megnövelt rendelkezésre állás érdekében több rendelkezésre állási zónában való üzembe helyezéskor konfigurálható. A rendelkezésre állási zónákban a rendelkezésre állás 99,99 %-os növekszik. További információkért tekintse meg az Azure-tűzfal [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). A 99,99 %-os SLA-t érhető el, ha két vagy több rendelkezésre állási zónák vannak kijelölve.

Is társíthat Azure tűzfal egy adott zóna csak a közelségi okok miatt a szolgáltatás standard 99,95 %-os SLA-t használja.

Van egy rendelkezésre állási zónában üzembe helyezett tűzfal további költség nélkül. Vannak azonban a rendelkezésre állási zónák társított bejövő és kimenő adatforgalom további költségekkel. További információkért lásd: [adatforgalmi díjszabás részletei](https://azure.microsoft.com/pricing/details/bandwidth/).

Azure-beli tűzfal rendelkezésre állási zónák rendelkezésre állási zónákat támogató régiók érhetők el. További információkért lásd: [Mik a rendelkezésre állási zónák az Azure-ban?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> A rendelkezésre állási zónák csak a telepítés során lehet konfigurálni. A rendelkezésre állási zónák felvenni egy meglévő tűzfal nem konfigurálható.

Rendelkezésre állási zónákkal kapcsolatos további információkért lásd: [Mik a rendelkezésre állási zónák az Azure-ban?](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Korlátlan felhőalapú skálázhatóság

Az Azure Firewall akármeddig felskálázható a változó hálózati forgalom kezeléséhez, így a költségvetést nem szükséges a csúcsforgalomhoz igazítania.

## <a name="application-fqdn-filtering-rules"></a>Alkalmazások teljes tartománynevére vonatkozó szűrési szabályok

A kimenő HTTP/S-forgalom korlátozható teljes tartománynevek (FQDN) egy megadott listájára (helyettesítő karakterek is alkalmazhatók). Ez a funkció nem szükséges SSL-lezárást.

## <a name="network-traffic-filtering-rules"></a>Hálózati forgalomra vonatkozó szűrési szabályok

Központilag hozhat létre *engedélyező* vagy *tiltó* hálózatszűrési szabályokat forrás és cél IP-cím, port és protokoll alapján. Az Azure Firewall teljes mértékben állapotalapú, így képes megkülönböztetni különböző típusú kapcsolatok érvényes csomagjait. A szabályok több előfizetésen és virtuális hálózaton érvényesíthetők és naplózhatók.

## <a name="fqdn-tags"></a>FQDN-címkék

Az FQDN-címkékkel egyszerűen engedélyezheti a jól ismert Azure-szolgáltatások hálózati forgalmát a tűzfalon keresztül. Tegyük fel például, hogy engedélyezni kívánja a Windows Update hálózati forgalmát a tűzfalon keresztül. Létrehozhat egy alkalmazásszabályt, és hozzáadhatja a Windows Update címkéjét. A Windows Update hálózati forgalma ezután akadálytalanul áthaladhat a tűzfalon.

## <a name="service-tags"></a>Szolgáltatáscímkék

A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége. Nem lehet a hozzon létre saját szolgáltatáscímkéket, és adja meg, hogy mely IP-címek egyes címkék. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket.

## <a name="threat-intelligence"></a>Fenyegetésészlelési intelligencia

Engedélyezheti a veszélyforrás-felderítésen alapuló szűrést a tűzfalon az ismert kártékony IP-címekről és tartományokból származó vagy azokba irányuló adatforgalomról való riasztáshoz és annak letiltásához. Az IP-címek és a tartományok a Microsoft Threat Intelligence hírcsatornájáról származnak.

## <a name="outbound-snat-support"></a>Kimenő SNAT-támogatás

A rendszer a kimenő virtuális hálózati forgalomhoz tartozó minden IP-címet lefordít az Azure Firewall nyilvános IP-címére (forráshálózati címfordítás, SNAT). Azonosíthatja és engedélyezheti a virtuális hálózatból a távoli internetes célhelyekre irányuló forgalmat. Az Azure tűzfal nem SNAT, ha a cél IP-cím egy magánhálózati IP-címtartományt egy [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Ha a szervezet magánhálózatok egy nyilvános IP-címtartományt használja, Azure tűzfal fog SNAT a forgalmat a tűzfal magánhálózati IP-címek AzureFirewallSubnet egyikére.

## <a name="inbound-dnat-support"></a>Bejövő DNAT-támogatás

A tűzfal nyilvános IP-címére érkező bejövő hálózati forgalmat a rendszer lefordítja (Destination Network Address Translation, célhálózati címfordítás), és a virtuális hálózat magánhálózati IP-címeire szűri.

## <a name="multiple-public-ips-public-preview"></a>Több nyilvános IP-címek (nyilvános előzetes verzió)

(Legfeljebb 100) több nyilvános IP-címet társíthatja a tűzfal.

Ez lehetővé teszi a következő esetekben:

- **DNAT** – több standard port példány lefordíttathatja a háttérkiszolgálókhoz. Például ha két nyilvános IP-címek, szerint lefordíttathatja a TCP-portját 3389 (RDP) IP-címe.
- **SNAT** – további portokat a kimenő SNAT-kapcsolatok, csökkentve az esetleges SNAT portfogyás érhető el. Jelenleg az Azure-tűzfal véletlenszerűen kiválaszt a nyilvános IP-forráscím-kapcsolathoz való használatra. Ha bármelyik alárendelt szűrés a hálózaton, lehetővé teszik a tűzfal társított összes nyilvános IP-címeket szeretne.

> [!NOTE]
> A nyilvános előzetes során Ha hozzáad vagy eltávolít egy nyilvános IP-címet a futó tűzfal DNAT-szabályok használatával meglévő bejövő kapcsolat funkció 40 – 120 másodpercen. A tűzfal rendelt, kivéve, ha a tűzfal nincs lefoglalva, vagy törölve első nyilvános IP-cím nem lehet eltávolítani.

## <a name="azure-monitor-logging"></a>Azure Monitor-naplózás

Az összes esemény és az Azure Monitor lehetővé teszi, hogy egy tárfiókba, az események streamelése az eseményközpontba naplóinak archiválása, vagy küldhet nekik az Azure Monitor naplóira integrált részei.

## <a name="known-issues"></a>Ismert problémák

Az Azure Firewall az alábbi ismert hibákkal rendelkezik:

|Probléma  |Leírás  |Kezelés  |
|---------|---------|---------|
|Ütközés az Azure Security Center (ASC) igény szerinti (JIT) szolgáltatásával|Ha a virtuális gépet a JIT használatával éri el, és az egy olyan alhálózaton található, amelynek a felhasználó által megadott útvonala alapértelmezett átjáróként az Azure Firewallra mutat, az ASC JIT szolgáltatása nem működik. Ez az eredménye, az aszimmetrikus útválasztás – a virtuális gép nyilvános IP-címen keresztül érhető el a csomagot (igény szerinti hozzáférés megnyitott), de a visszatérési elérési út a tűzfal eldobja, mert nincs munkamenetet létesítenek a tűzfalon keresztül.|A probléma megkerüléséhez helyezze a JIT használatával elért virtuális gépeket egy olyan külön alhálózatra, amelyen nincs felhasználó által megadott, a tűzfalra mutató útvonal.|
A nem TCP/UDP-protokollokra (például ICMP) vonatkozó hálózati szűrési szabályok nem működnek az internetre irányuló forgalom esetében|A nem TCP/UDP-protokollokra vonatkozó hálózati szűrési szabályok nem működnek a nyilvános IP-címre vonatkozó forráshálózati címfordítással. A nem TCP/UDP-protokollok a küllők alhálózatai és a virtuális hálózatok között támogatottak.|Az Azure Firewall a Standard Load Balancert használja, [amely jelenleg nem támogatja a forráshálózati címfordítást az IP-protokollokon](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Ennek támogatásához egy későbbi kiadásban beállítások vizsgál azt.|
|A PowerShell és a CLI nem támogatja az ICMP-t|Az Azure PowerShell és a CLI nem támogatja az ICMP-t érvényes protokollként a hálózati szabályok között.|Az ICMP protokollt a portálon, és a REST API-n keresztül használandó továbbra is lehetőség. Hamarosan a PowerShell és CLI adja hozzá az ICMP dolgozunk.|
|Az FQDN-címkék protokoll: port megadását igénylik|Alkalmazás teljes tartománynév-címke szabályokhoz port: definíció protokoll.|A port:protokoll értékként használhat **https**-t. Győződjön meg arról, ez a mező nem kötelező FQDN címkék használatakor dolgozunk.|
|A tűzfal áthelyezése másik erőforráscsoportba vagy előfizetésbe nem támogatott|A tűzfal áthelyezése másik erőforráscsoportba vagy előfizetésbe nem támogatott.|Ez a funkció támogatása az ütemterv van. Ahhoz, hogy egy tűzfalat áthelyezzen másik erőforráscsoportba vagy előfizetésbe, először törölnie kell az aktuális példányt, és újra létre kell hoznia az új erőforráscsoportban vagy előfizetésben.|
|A hálózati és az szabályokban porttartomány|Portjait legfeljebb 64 000, magas portok vannak fenntartva, felügyeleti és állapotának mintavételei. |Dolgozunk a korlátozás enyhítése.|
|Előfordulhat, hogy első maszkolva intelligens veszélyforrás-riasztásai|A hálózati szabályok a 80-as/443-as kimenő szűrési maszkok célhelyet fenyegetésészlelési intelligencia riasztást, ha a riasztás csak módra konfigurálni.|Hozzon létre kimenő szűrés a 80-as/443-as alkalmazás szabályok használatával. Vagy módosítsa a threat intelligence módot **riasztás és a megtagadási**.|
|Az Azure tűzfal használja az Azure DNS csak a névfeloldáshoz|Azure tűzfal teljes tartománynevek csak az Azure DNS használatával oldja fel. Egyéni DNS-kiszolgáló nem támogatott. Nincs nincs hatással a DNS-feloldás más alhálózatokon.|Dolgozunk a korlátozás enyhítése.|
|Az Azure tűzfal SNAT/DNAT nem működik a magánhálózati IP-célhelyek|Az Azure tűzfal SNAT/DNAT-támogatás internetes kimenő és bejövő korlátozódik. SNAT/DNAT jelenleg nem működik a magánhálózati IP-célhelyeket. Ha például a küllős topológiájú.|Ez a jelenlegi korlátozását.|
|Első nyilvános IP-cím nem távolítható el.|A tűzfal rendelt, kivéve, ha a tűzfal nincs lefoglalva, vagy törölve első nyilvános IP-cím nem lehet eltávolítani.|Ez az elvárt működés.|
|Ha ad hozzá, vagy távolítsa el a nyilvános IP-címet, a DNAT szabályok funkció ideiglenesen.| Hozzáadásakor, vagy távolítsa el a futó tűzfal egy nyilvános IP-címet, meglévő bejövő kapcsolat DNAT-szabályok használatával nem működnek a 40-120 másodperc.|Ez az egy korlátozás ennek a funkciónak a nyilvános előzetes verzióját.|
|A rendelkezésre állási zónák csak a telepítés során lehet konfigurálni.|A rendelkezésre állási zónák csak a telepítés során lehet konfigurálni. A rendelkezésre állási zónák nem lehet konfigurálni, a tűzfal üzembe helyezése után.|Ez az elvárt működés.|
|A bejövő kapcsolatoknál SNAT|DNAT, a nyilvános IP-cím tűzfalon keresztüli kapcsolatokat mellett egy, a tűzfal a forgalmon forrásoldali hálózati címfordítást (bejövő) azok magánhálózati IP-címek. Ez a követelmény ma (is az aktív nva-k) szimmetrikus útválasztásának biztosításához.|Megőrizheti az eredeti forrásra HTTP/s, érdemes lehet [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) fejlécek. Például, mint például az szolgáltatást használ [Azure bejárati ajtajának](../frontdoor/front-door-http-headers-protocol.md#front-door-service-to-backend) a tűzfal előtt. WAF Azure bejárati ajtajának és lánc részeként a tűzfalhoz is hozzáadhat.

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Telepítse és konfigurálja az Azure portal segítségével Azure-tűzfal](tutorial-firewall-deploy-portal.md)
- [Azure Firewall üzembe helyezése sablon használatával](deploy-template.md)
- [Azure Firewall-tesztkörnyezet létrehozása](scripts/sample-create-firewall-test.md)
