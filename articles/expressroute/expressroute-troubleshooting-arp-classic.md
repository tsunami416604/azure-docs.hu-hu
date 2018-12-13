---
title: 'ARP táblák – hibaelhárítás ExpressRoute első: klasszikus: Azure |} A Microsoft Docs'
description: Ez az oldal nyújt útmutatást az ARP-táblák beolvasása egy ExpressRoute-kapcsolatcsoport – klasszikus üzemi modellben.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 367a79b04a8736e2eafb6851b682f2c244e80522
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272286"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>ARP-táblák a klasszikus üzemi modellben
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klasszikus](expressroute-troubleshooting-arp-classic.md)
> 
> 

Ez a cikk végigvezeti az Address Resolution Protocol (ARP) tábla beolvasása az Azure ExpressRoute-kapcsolatcsoport szükséges lépések.

> [!IMPORTANT]
> A dokumentum célközönsége, egyszerű hibák azonosítása és kijavítása érdekében. Nem célja a helyettesítheti a Microsoft ügyfélszolgálatához. Ha nem tudja megoldani a problémát az alábbi útmutató segítségével, nyissa meg egy támogatási kérést az [Microsoft Azure súgó + támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Cím Resolution Protocol (ARP) és az ARP-táblák
ARP egy 2. rétegbeli protokoll definiált [RFC 826](https://tools.ietf.org/html/rfc826). ARP szolgál egy Ethernet-címe (MAC-cím) leképezése az IP-címet.

ARP-táblázat tartalmazza az IPv4-cím és MAC-cím egy adott társviszony-létesítéshez. Egy ExpressRoute-kapcsolatcsoport társviszony az ARP-táblázat minden egyes interfész (elsődleges és másodlagos) a következő információkat biztosítja:

1. A helyi útválasztó felület IP-címet a MAC-cím hozzárendelése
2. Az ExpressRoute útválasztó adapter IP-címet a MAC-cím hozzárendelése
3. A hozzárendelés élettartama

ARP-táblák segíthet a 2. rétegbeli konfigurációjának ellenőrzése és az alapszintű 2. rétegbeli csatlakozási problémák elhárítása.

Következő ARP-táblázat példázza:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Az alábbi szakasz ismerteti a ARP-táblák, amely az ExpressRoute-peremhálózati útválasztóhoz láthatók.

## <a name="prerequisites-for-using-arp-tables"></a>ARP-táblák használatának előfeltételei
Győződjön meg arról, hogy rendelkezik a következő, a folytatás előtt:

* Egy érvényes ExpressRoute-kapcsolatcsoport, amely legalább egy társviszony-létesítés van konfigurálva. A kapcsolatcsoport a kapcsolatszolgáltató által teljes kell konfigurálni. Ön (vagy a kapcsolatszolgáltató) kell konfigurálnia a társviszony-létesítések (az Azure privát, Azure nyilvános vagy Microsoft) legalább egy ebben a kapcsolatcsoportban.
* IP-címtartományok a társviszony-létesítések (az Azure privát, Azure nyilvános és Microsoft) konfigurálásához használt. Tekintse át az IP-cím hozzárendelés szereplő példák a [ExpressRoute útválasztási követelményei lapon](expressroute-routing.md) végigvezetnek, hogyan a aise és az ExpressRoute oldalán felületek IP-címek vannak leképezve. A társviszony-létesítési konfigurálásával kapcsolatos információkat szerezhet a áttekintésével a [ExpressRoute-társviszony-létesítési konfiguráció lap](expressroute-howto-routing-classic.md).
* A hálózati csapata vagy a kapcsolat szolgáltatójától vonatkozó adatokat a MAC-címek ezen IP-címek használt csatolók közül.
* A legújabb Windows PowerShell-modul az Azure-hoz (1,50 vagy újabb verzió).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Az ExpressRoute-kapcsolatcsoport ARP-táblák
Ez a szakasz a ARP-táblák, az egyes társviszony-létesítés PowerShell-lel kapcsolatos útmutatást. A folytatás előtt, vagy a kapcsolatszolgáltató be kell állítania a társviszony-létesítést. Minden egyes-kapcsolatcsoport két elérési útját (elsődleges és másodlagos) rendelkezik. ARP-táblázat minden útvonalhoz egymástól függetlenül megtekinthető.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-táblák az Azure privát társviszony-létesítéshez
A következő parancsmagot biztosít az ARP táblák az Azure privát társviszony-létesítés:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Következő egy minta kimenet az egyik elérési út:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP-táblák, Azure nyilvános társviszony-létesítéshez:
A következő parancsmagot biztosít az ARP táblák az Azure nyilvános társviszony-létesítés:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Következő egy minta kimenet az egyik elérési út:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Következő egy minta kimenet az egyik elérési út:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>A Microsoft társviszony-létesítéshez ARP-táblák
A következő parancsmagot biztosít az ARP táblák Microsoft társviszony-létesítés:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Kimeneti elérési utak egyik az alább látható:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Ezek az információk használata
ARP-táblázat egy társviszony-létesítések használható 2. rétegbeli konfiguráció és a kapcsolat ellenőrzése. Ez a szakasz áttekintést ARP-táblák különböző helyzetekben megjelenését.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>ARP táblából, ha a kapcsolatcsoport működési (várt) állapotban van.
* ARP-táblázat egy bejegyzés a helyszíni oldalon egy érvényes IP-cím és MAC-címmel rendelkező, és a egy hasonló bejegyzést a Microsoft oldalán van.
* Az utolsó oktettet helyi IP-cím, mindig páratlan szám.
* Az utolsó oktettet Microsoft IP-cím, mindig páros szám.
* Az azonos MAC-cím jelenik meg a Microsoft az összes mindhárom társviszony-létesítést (elsődleges-másodlagos).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Ha a helyszíni, vagy ha a kapcsolatszolgáltató oldalon problémák vannak az ARP-táblázat
 ARP-táblázat csak egy bejegyzés jelenik meg. A MAC-cím és a Microsoft oldalán használt IP-cím közötti jeleníti meg.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Ha ehhez hasonló hibát tapasztal, nyisson meg egy támogatási kérést oldható meg a kapcsolat szolgáltatóját.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Ha problémája van a Microsoft oldalon ARP-táblázat
* Nem láthatja az ARP-táblázat egy társviszony-létesítéshez probléma merül fel a Microsoft oldalán látható.
* Nyisson egy támogatási kérelmet a [Microsoft Azure súgó + támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Adja meg, hogy a 2. rétegbeli kapcsolódási probléma.

## <a name="next-steps"></a>További lépések
* 3. rétegbeli konfigurációi az ExpressRoute-kapcsolatcsoport ellenőrzése:
  * Szerezze be egy útvonalat a BGP-munkamenetek állapotának megállapítása összefoglalását.
  * Határozza meg, melyik előtagokat hirdet meg között ExpressRoute útválasztási táblázat beolvasása.
* Ellenőrizze az adatátviteli bájt és áttekintésével.
* Nyisson egy támogatási kérelmet a [Microsoft Azure súgó + támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Ha továbbra is problémákat tapasztal.

