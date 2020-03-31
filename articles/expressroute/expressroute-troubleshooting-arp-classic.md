---
title: 'Azure ExpressRoute: ARP-táblák – Hibaelhárítás: klasszikus'
description: Ez a lap utasításokat tartalmaz egy ExpressRoute-kapcsolat - klasszikus üzembe helyezési modell ARP-táblák beszerzéséhez.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: 7c223e3802d499e002b12580b17cb9ee3f1bea97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076615"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>ARP-táblák beszerzése a klasszikus üzembe helyezési modellben
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klasszikus](expressroute-troubleshooting-arp-classic.md)
> 
> 

Ez a cikk bemutatja az Azure ExpressRoute-kapcsolathoz a Címfeloldási protokoll (ARP) táblák beszerzésének lépéseit.

> [!IMPORTANT]
> Ez a dokumentum egyszerű problémák diagnosztizálásának és javításának elősegítésére szolgál. Nem helyettesíti a Microsoft támogatását. Ha a probléma nem oldható meg az alábbi útmutatással, nyisson meg egy támogatási kérelmet a [Microsoft Azure Súgó+támogatás sal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Címfeloldási protokoll (ARP) és ARP-táblák
Az ARP egy Layer 2 protokoll, amelyet az [RFC 826](https://tools.ietf.org/html/rfc826)határoz meg. Az ARP az Ethernet-cím (MAC-cím) IP-címhez való hozzárendelésére szolgál.

Az ARP-tábla egy adott társviszony-létesítés IPv4-címét és MAC-címét tartalmazza. Az ExpressRoute-kapcsolatlétesítés ARP-táblája a következő információkat biztosítja az egyes összeköttetésekhez (elsődleges és másodlagos):

1. Helyszíni útválasztó-összeköttetés IP-címének hozzárendelése MAC-címhez
2. ExpressRoute-útválasztó-összeköttetés IP-címének hozzárendelése MAC-címhez
3. A feltérképezés kora

Az ARP-táblák segíthetnek a Layer 2 konfigurációjának érvényesítésében és a Layer 2 alapvető kapcsolódási problémáinak elhárításában.

Az alábbiakban egy ARP-táblázat látható:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


A következő szakasz az ExpressRoute peremhálózati útválasztók által látható ARP-táblák megtekintésével kapcsolatban tartalmaz tájékoztatást.

## <a name="prerequisites-for-using-arp-tables"></a>Az ARP-táblák használatának előfeltételei
A folytatás előtt győződjön meg arról, hogy a következőket kell biztosítania:

* Érvényes ExpressRoute-kapcsolat, amely legalább egy társviszony-létesítéssel van konfigurálva. Az áramkört a kapcsolatszolgáltatónak teljesen konfigurálnia kell. Önnek (vagy a kapcsolatszolgáltatónak) konfigurálnia kell legalább egy társviszony-létesítést (Azure private, Azure public vagy Microsoft) ezen a kapcsolatcsoporton.
* A társviszony-létesítések konfigurálásakor használt IP-címtartományok (Azure private, Azure public és Microsoft). Tekintse át az IP-címhozzárendelési példákat az [ExpressRoute útválasztási követelmények lapon,](expressroute-routing.md) hogy megismerhesse, hogyan vannak leképezve az IP-címek az Ön és az ExpressRoute oldalán lévő összeköttetésekhez. A társviszony-létesítési konfigurációval kapcsolatos információkat az [ExpressRoute társviszony-létesítési konfigurációs lapjának](expressroute-howto-routing-classic.md)áttekintésével kaphatja meg.
* A hálózati csoporttól vagy a kapcsolatszolgáltatótól származó információk az ip-címekhez használt összeköttetések MAC-címeiről.
* A legújabb Windows PowerShell-modul az Azure-hoz (1.50-es vagy újabb verzió).

## <a name="arp-tables-for-your-expressroute-circuit"></a>ARP-táblák az ExpressRoute-kapcsolathoz
Ez a szakasz az egyes társviszony-létesítési típusok ARP-tábláinak powershell használatával való megtekintésével kapcsolatos útmutatást tartalmaz. A folytatás előtt önnek vagy a kapcsolatszolgáltatónak konfigurálnia kell a társviszony-létesítést. Minden körnek két elérési útja van (elsődleges és másodlagos). Az egyes görbék ARP-táblája egymástól függetlenül ellenőrizhető.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-táblák az Azure privát társviszony-létesítéséhez
A következő parancsmag biztosítja az ARP-táblákat az Azure privát társviszony-létesítéséhez:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

A következőkben az egyik elérési út mintakimenete látható:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP-táblák az Azure nyilvános társviszony-létesítéséhez:
A következő parancsmag biztosítja az ARP-táblákat az Azure nyilvános társviszony-létesítéséhez:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

A következőkben az egyik elérési út mintakimenete látható:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


A következőkben az egyik elérési út mintakimenete látható:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>ARP-táblák Microsoft társviszony-létesítéshez
A következő parancsmag a Microsoft társviszony-létesítéshez az ARP-táblákat tartalmazza:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


A mintakimenet az alábbi útvonalra mutat be:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Az információk felhasználása
A társviszony-létesítés ARP-táblája a Layer 2 konfigurációjának és kapcsolatának érvényesítésére használható. Ez a szakasz áttekintést nyújt arról, hogyan néznek ki az ARP-táblák a különböző forgatókönyvekben.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>ARP-tábla, ha egy áramkör üzemi (várt) állapotban van
* Az ARP-tábla rendelkezik egy érvényes IP- és MAC-címmel rendelkező, a helyszíni oldalra, a Microsoft-oldal hasonló bejegyzésével.
* A helyszíni IP-cím utolsó oktettje mindig páratlan szám.
* A Microsoft IP-cím utolsó oktettje mindig páros szám.
* Ugyanaz a MAC-cím jelenik meg a Microsoft oldalán mindhárom társviszony-létesítéshez (elsődleges/másodlagos).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>ARP-tábla, ha a helyszínen van, vagy ha a kapcsolat-szolgáltató oldalon problémák vannak
 Csak egy bejegyzés jelenik meg az ARP táblában. A MAC-cím és a Microsoft oldalán használt IP-cím közötti leképezést jeleníti meg.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Ha ilyen problémát tapasztal, nyisson meg egy támogatási kérelmet a kapcsolódási szolgáltatónál a probléma megoldásához.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>ARP tábla, ha a Microsoft oldalán problémák
* Ha problémák merülnek fel a Microsoft oldalán, akkor nem jelenik meg a társviszony-létesítéshez a társviszony-létesítéshez megjelenő ARP-tábla.
* Nyisson meg egy támogatási kérelmet a [Microsoft Azure Súgó+támogatással.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Adja meg, hogy problémája van-e a Layer 2 kapcsolattal.

## <a name="next-steps"></a>További lépések
* 3. rétegkonfigurációinak ellenőrzése az ExpressRoute-kapcsolaton:
  * A BGP-munkamenetek állapotának meghatározásához útvonalösszegzést kaphat.
  * Egy útvonaltábla segítségével határozza meg, hogy mely előtagok at hirdeti az ExpressRoute-on keresztül.
* Az adatátvitel ellenőrzése bájtokkal.
* Ha továbbra is problémákat tapasztal, nyisson meg egy támogatási kérelmet a [Microsoft Azure Súgó+támogatással.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

