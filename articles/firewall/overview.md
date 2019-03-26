---
title: Mi az Azure Firewall?
description: Az Azure Firewall szolgáltatásainak ismertetése.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 3/25/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: be490299d09e396e4bc589ebf777f64ce084d320
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418720"
---
# <a name="what-is-azure-firewall"></a>Mi az Azure Firewall?

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Ez egy szolgáltatásként nyújtott teljesen állapotalapú tűzfal, beépített magas rendelkezésre állással és korlátlan felhőalapú skálázhatósággal. 

![Tűzfal áttekintése](media/overview/firewall-threat.png)

Központilag hozhatja létre, érvényesítheti és naplózhatja az alkalmazás- és hálózatelérési szabályzatokat az előfizetésekre és a virtuális hálózatokra vonatkozóan. Az Azure Firewall statikus nyilvános IP-címet használ a virtuális hálózat erőforrásaihoz, így a külső tűzfalak azonosíthatják a virtuális hálózatból érkező forgalmat.  A szolgáltatás teljesen integrálva van az Azure Monitorral a naplózás és az elemzés érdekében.

## <a name="features"></a>Szolgáltatások

Az Azure Firewall az alábbi szolgáltatásokat kínálja:

### <a name="built-in-high-availability"></a>Beépített magas rendelkezésre állás

A magas rendelkezésre állás be van építve a rendszerbe, így nincs szükség további terheléselosztókra, és semmit nem kell konfigurálnia.

### <a name="unrestricted-cloud-scalability"></a>Korlátlan felhőalapú skálázhatóság

Az Azure Firewall akármeddig felskálázható a változó hálózati forgalom kezeléséhez, így a költségvetést nem szükséges a csúcsforgalomhoz igazítania.

### <a name="application-fqdn-filtering-rules"></a>Alkalmazások teljes tartománynevére vonatkozó szűrési szabályok

A kimenő HTTP/S-forgalom korlátozható teljes tartománynevek (FQDN) egy megadott listájára (helyettesítő karakterek is alkalmazhatók). Ehhez a szolgáltatáshoz nem szükséges SSL-lezárás.

### <a name="network-traffic-filtering-rules"></a>Hálózati forgalomra vonatkozó szűrési szabályok

Központilag hozhat létre *engedélyező* vagy *tiltó* hálózatszűrési szabályokat forrás és cél IP-cím, port és protokoll alapján. Az Azure Firewall teljes mértékben állapotalapú, így képes megkülönböztetni különböző típusú kapcsolatok érvényes csomagjait. A szabályok több előfizetésen és virtuális hálózaton érvényesíthetők és naplózhatók.

### <a name="fqdn-tags"></a>FQDN-címkék

Az FQDN-címkékkel egyszerűen engedélyezheti a jól ismert Azure-szolgáltatások hálózati forgalmát a tűzfalon keresztül. Tegyük fel például, hogy engedélyezni kívánja a Windows Update hálózati forgalmát a tűzfalon keresztül. Létrehozhat egy alkalmazásszabályt, és hozzáadhatja a Windows Update címkéjét. A Windows Update hálózati forgalma ezután akadálytalanul áthaladhat a tűzfalon.

### <a name="service-tags"></a>Szolgáltatáscímkék

A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége. Nem hozhat létre saját szolgáltatáscímkéket, és nem határozhatja meg, hogy melyik IP-címeket jelöljék az egyes címkék. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket.

### <a name="threat-intelligence"></a>Fenyegetésészlelési intelligencia

Threat intelligence szerinti szűrés a tűzfalat, hogy a riasztás és a forgalom, és az ismert kártékony IP-címek és tartományok esetén is engedélyezhető. The IP addresses and domains are sourced from the Microsoft Threat Intelligence feed.

### <a name="outbound-snat-support"></a>Kimenő SNAT-támogatás

A rendszer a kimenő virtuális hálózati forgalomhoz tartozó minden IP-címet lefordít az Azure Firewall nyilvános IP-címére (forráshálózati címfordítás, SNAT). Azonosíthatja és engedélyezheti a virtuális hálózatból a távoli internetes célhelyekre irányuló forgalmat.

### <a name="inbound-dnat-support"></a>Bejövő DNAT-támogatás

A tűzfal nyilvános IP-címére érkező bejövő hálózati forgalmat a rendszer lefordítja (Destination Network Address Translation, célhálózati címfordítás), és a virtuális hálózat magánhálózati IP-címeire szűri. 

### <a name="azure-monitor-logging"></a>Azure Monitor-naplózás

Az összes esemény és az Azure Monitor lehetővé teszi, hogy egy tárfiókba, az események streamelése az eseményközpontba naplóinak archiválása, vagy küldhet nekik az Azure Monitor naplóira integrált részei.

## <a name="known-issues"></a>Ismert problémák

Az Azure Firewall az alábbi ismert hibákkal rendelkezik:

|Probléma  |Leírás  |Kezelés  |
|---------|---------|---------|
|Ütközés az Azure Security Center (ASC) igény szerinti (JIT) szolgáltatásával|Ha a virtuális gépet a JIT használatával éri el, és az egy olyan alhálózaton található, amelynek a felhasználó által megadott útvonala alapértelmezett átjáróként az Azure Firewallra mutat, az ASC JIT szolgáltatása nem működik. Ez az eredménye, az aszimmetrikus útválasztás – a virtuális gép nyilvános IP-címen keresztül érhető el a csomagot (igény szerinti hozzáférés megnyitott), de a visszatérési elérési út a tűzfal eldobja, mert nincs munkamenetet létesítenek a tűzfalon keresztül.|A probléma megkerüléséhez helyezze a JIT használatával elért virtuális gépeket egy olyan külön alhálózatra, amelyen nincs felhasználó által megadott, a tűzfalra mutató útvonal.|
A nem TCP/UDP-protokollokra (például ICMP) vonatkozó hálózati szűrési szabályok nem működnek az internetre irányuló forgalom esetében|A nem TCP/UDP-protokollokra vonatkozó hálózati szűrési szabályok nem működnek a nyilvános IP-címre vonatkozó forráshálózati címfordítással. A nem TCP/UDP-protokollok a küllők alhálózatai és a virtuális hálózatok között támogatottak.|Az Azure Firewall a Standard Load Balancert használja, [amely jelenleg nem támogatja a forráshálózati címfordítást az IP-protokollokon](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Jelenleg vizsgáljuk a lehetőségeket, hogy ezt a forgatókönyvet valamelyik későbbi kiadás támogathassa majd.|
|A PowerShell és a CLI nem támogatja az ICMP-t|Az Azure PowerShell és a CLI nem támogatja az ICMP-t érvényes protokollként a hálózati szabályok között.|Az ICMP továbbra is használható protokollként a portálon vagy a REST API-n keresztül. Dolgozunk azon, hogy hamarosan a PowerShellben és a CLI-ben is elérhető legyen az ICMP.|
|Az FQDN-címkék protokoll: port megadását igénylik|Az FQDN-címkékkel rendelkező alkalmazásszabályok port:protokoll definíciót igényelnek.|A port:protokoll értékként használhat **https**-t. Dolgozunk azon, hogy ez a mező FQDN-címkék használatakor választható legyen.|
|A tűzfal áthelyezése másik erőforráscsoportba vagy előfizetésbe nem támogatott.|A tűzfalak áthelyezése más erőforráscsoportba vagy előfizetésbe nem támogatott.|Ez a funkció támogatása az ütemterv van. Ahhoz, hogy egy tűzfalat áthelyezzen másik erőforráscsoportba vagy előfizetésbe, először törölnie kell az aktuális példányt, és újra létre kell hoznia az új erőforráscsoportban vagy előfizetésben.|
|A hálózati és az szabályokban porttartomány|Portjait legfeljebb 64 000, magas portok vannak fenntartva, felügyeleti és állapotának mintavételei. |Dolgozunk a korlátozás enyhítése.|
|Előfordulhat, hogy első maszkolva intelligens veszélyforrás-riasztásai|A hálózati szabályok a 80-as/443-as kimenő szűrési maszkok célhelyet fenyegetésészlelési intelligencia riasztást, ha a riasztás csak módra konfigurálni.|Hozzon létre kimenő szűrés a 80-as/443-as alkalmazás szabályok használatával. Vagy módosítsa a threat intelligence módot **riasztás és a megtagadási**.|

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Telepítse és konfigurálja az Azure portal segítségével Azure-tűzfal](tutorial-firewall-deploy-portal.md)
- [Azure Firewall üzembe helyezése sablon használatával](deploy-template.md)
- [Azure Firewall-tesztkörnyezet létrehozása](scripts/sample-create-firewall-test.md)