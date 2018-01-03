---
title: "ARP-tábla beolvasása: klasszikus: Azure ExpressRoute-hibaelhárítási |} Microsoft Docs"
description: "Ezen a lapon a ARP tábla beolvasása az ExpressRoute-kapcsolatcsoportot ismerteti."
documentationcenter: na
services: expressroute
author: ganesr
manager: carolz
editor: tysonn
ms.assetid: b5856acf-03c2-4933-8111-6ce12998d92a
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: fcc847b7e30fd55ca759830e0254ab7542e7663e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>A klasszikus üzembe helyezési modellel tábla ARP beolvasása
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klasszikus](expressroute-troubleshooting-arp-classic.md)
> 
> 

Ez a cikk végigvezeti az Address Resolution Protocol (ARP) tábla beolvasása az Azure ExpressRoute-kör számára.

> [!IMPORTANT]
> Ez a dokumentum olyan egyszerű problémák megoldásában segítséget. Nem célja a helyettesítheti a Microsoft támogatási szolgálatához. Az alábbi útmutató segítségével nem tudja megoldani a problémát, ha a támogatási kérést nyithat [Azure a Microsoft Súgó és támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Cím Resolution Protocol (ARP) és a ARP
ARP egy 2. rétegbeli protokoll, amely meghatározott [RFC 826](https://tools.ietf.org/html/rfc826). ARP szolgál az Ethernet-címe (MAC-cím) leképezése egy IP-címet.

Egy táblázat ARP az IPv4-cím és MAC-címet adott társviszony-létesítés leképezéseket. Egy ExpressRoute-kapcsolatcsoport társviszonyt az ARP-táblázat az egyes csatolókra (elsődleges és másodlagos) a következő adatokat tartalmazza:

1. A MAC-címet a helyi útválasztó illesztő IP-címet leképezése
2. ExpressRoute útválasztó illesztő IP-címet a MAC-cím hozzárendelése
3. A leképezési korát

ARP-táblázatok segíthet a 2. rétegbeli konfigurációjának ellenőrzése és a 2. rétegbeli kapcsolatot alapvető problémák elhárításához.

Az alábbiakban egy egy ARP-táblázata – példa látható:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Az alábbi szakasz ismerteti a láthatók az ExpressRoute peremhálózati útválasztók ARP táblákhoz megtekintése.

## <a name="prerequisites-for-using-arp-tables"></a>ARP-táblázatok használatára vonatkozó Előfeltételek
Győződjön meg arról, hogy rendelkezik a következő, a folytatás előtt:

* Egy érvényes ExpressRoute-kapcsolatcsoportot legalább egy társviszony-létesítés konfigurálva van. A kapcsolatcsoport teljesen kell beállítani a kapcsolat szolgáltatóját. Ön (vagy a kapcsolat szolgáltatóját) kell konfigurálnia a társviszony (Azure saját, az Azure nyilvános, vagy a Microsoft) közül legalább egy ebben a kapcsolatcsoportban.
* IP-címtartományok, amelyeket a társviszony (Azure saját, az Azure nyilvános, és a Microsoft) konfigurálása. Tekintse át az IP-cím hozzárendelés szereplő példák a [ExpressRoute útválasztási követelmények lapon](expressroute-routing.md) megismerhesse, milyen IP-címek vannak leképezve a aise és az ExpressRoute oldalán felületek segítségével. Kaphat a társviszony-létesítési konfigurációs információinak megtekintésével a [ExpressRoute-társviszony-létesítési konfiguráció lapon](expressroute-howto-routing-classic.md).
* Származó információkat a hálózati csoport vagy a kapcsolat szolgáltatójánál MAC-címet az IP-címeket használt csatolók.
* A legújabb Windows PowerShell-modult az Azure-ba (1,50 vagy újabb verzió).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Az ExpressRoute-kapcsolatcsoportot ARP-táblázatok
Ez a témakör leírja, hogyan megtekintéséhez az ARP-táblázatok az egyes társviszony-létesítés PowerShell használatával. A folytatás előtt Ön vagy a kapcsolat szolgáltatójánál be kell állítania a társviszony-létesítést. Minden kapcsolat van két elérési útnak (elsődleges és másodlagos). Az egyes elérési utakat ARP-táblázat egymástól függetlenül ellenőrizheti.

### <a name="arp-tables-for-azure-private-peering"></a>Az Azure magánhálózati társviszony-létesítés ARP-táblázatok
A következő parancsmag nyújt az ARP táblák Azure magánhálózati társviszony-létesítés.

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Az alábbiakban látható a elérési utak közül legalább egy minta kimenet:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Az Azure nyilvános társviszony ARP-táblázatok:
A következő parancsmag biztosít az ARP táblák az Azure nyilvános társviszony:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Az alábbiakban látható a elérési utak közül legalább egy minta kimenet:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Az alábbiakban látható a elérési utak közül legalább egy minta kimenet:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>A Microsoft társviszony-létesítéshez ARP-táblázatok
A következő parancsmag táblák nyújt az ARP a Microsoft társviszony-létesítéshez:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Minta kimenet az elérési utak közül legalább egy alább találja:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Ezek az információk használata
A társviszony ARP-táblázat segítségével 2. rétegbeli konfiguráció és a kapcsolat ellenőrzése. Ez a szakasz áttekintést ARP-táblázatok megjelenését különböző helyzetekben.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>ARP táblából, ha expressroute-kapcsolatcsoporthoz (várt) működési állapotban van.
* ARP-táblázat bejegyzést tartalmaz, a helyszíni oldalának egy érvényes IP-cím és MAC-címmel rendelkező, és a Microsoft oldalon egy hasonló bejegyzést.
* A helyi IP-cím utolsó oktettje mindig páratlan szám.
* A Microsoft IP-cím utolsó oktettje mindig páros szám-e.
* Az azonos MAC-cím jelenik meg a Microsoft az összes három esetében (elsődleges és másodlagos).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Ha a helyszíni, vagy ha a kapcsolat-szolgáltató oldalon problémák vannak az ARP-táblázat
 Csak egy bejegyzés megjelenik az ARP-táblázatban. Azt mutatja, hogy a MAC-cím és az IP-cím, a Microsoft oldalon használt közötti leképezést.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Ehhez hasonló problémát tapasztal, a kapcsolat szolgáltatójánál megoldási támogatási kérést nyithat.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Ha a Microsoft oldalon problémák vannak az ARP-táblázat
* Nem látják az ARP-táblázat társviszony-létesítés Ha problémák vannak a Microsoft oldalán látható.
* A támogatási kérést nyithat [Azure a Microsoft Súgó és támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Adja meg, hogy rendelkezik-e 2. rétegbeli kapcsolatot kapcsolatos problémát.

## <a name="next-steps"></a>További lépések
* Ellenőrizze a ExpressRoute-kapcsolatcsoportot 3. rétegbeli konfigurációi:
  * Útvonal BGP-munkamenetek állapotának megállapításához ellenőrizze az összefoglaló lekérése.
  * Egy útválasztási táblázatot meghatározni, melyik előtagok ExpressRoute között van-e hirdetve beolvasása.
* Adatátvitel érvényesítése küldött bájtok megtekintésével.
* A támogatási kérést nyithat [Azure a Microsoft Súgó és támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Ha továbbra is problémákat tapasztal.

