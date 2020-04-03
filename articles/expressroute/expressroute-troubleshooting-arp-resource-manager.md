---
title: 'Azure ExpressRoute: ARP-táblák – Hibaelhárítás'
description: Ez a lap útmutatást nyújt az ExpressRoute-áramkör ARP-tábláinak
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: b31e9eb852c69d5f02eb855e319d2a4901942994
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618523"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>ARP-táblák beszerzése az Erőforrás-kezelő telepítési modelljében
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klasszikus](expressroute-troubleshooting-arp-classic.md)
> 
> 

Ez a cikk végigvezeti a lépéseket az ExpressRoute-kapcsolat ARP-tábláinak megismeréséhez.

> [!IMPORTANT]
> Ez a dokumentum egyszerű problémák diagnosztizálásának és javításának elősegítésére szolgál. Nem helyettesíti a Microsoft támogatását. Ha nem tudja megoldani a problémát az alábbi útmutató segítségével, meg kell nyitnia egy támogatási jegyet a [Microsoft támogatási szolgálatával.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Címfeloldási protokoll (ARP) és ARP-táblák
A címfeloldási protokoll (ARP) a [826.](https://tools.ietf.org/html/rfc826) Az ARP az Ethernet-cím (MAC-cím) ip-címmel történő leképezésére szolgál.

Az ARP-tábla egy adott társviszony-létesítés ipv4-címének és MAC-címének leképezését tartalmazza. Az ExpressRoute-kapcsolatlétesítés ARP-táblája a következő információkat biztosítja az egyes összeköttetések (elsődleges és másodlagos) kapcsolathoz

1. A helyszíni útválasztó-összeköttetés IP-címének hozzárendelése a MAC-címhez
2. ExpressRoute-útválasztó ip-címének hozzárendelése a MAC-címhez
3. A feltérképezés kora

Az ARP-táblák segíthetnek a 2. 

Példa ARP-táblázatra: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


A következő szakasz az ExpressRoute peremhálózati útválasztók által látható ARP-táblák megtekintésének módjáról nyújt tájékoztatást. 

## <a name="prerequisites-for-learning-arp-tables"></a>Az ARP-táblázatok tanulásának előfeltételei
Győződjön meg arról, hogy a következő, mielőtt továbbhaladna

* Érvényes ExpressRoute-áramkör, amely legalább egy társviszony-létesítéssel van konfigurálva. Az áramkört a kapcsolatszolgáltatónak teljesen konfigurálnia kell. Önnek (vagy a kapcsolatszolgáltatónak) konfigurálnia kell legalább egy társviszony-létesítést (Azure private, Azure public and Microsoft) ezen a kapcsolatcsoporton.
* A társviszony-létesítések konfigurálásaként használt IP-címtartományok (Azure private, Azure public és Microsoft). Tekintse át az IP-cím hozzárendelési példákat az [ExpressRoute útválasztási követelmények lapon](expressroute-routing.md) annak megértéséhez, hogy az IP-címek hogyan vannak leképezve az Ön és az ExpressRoute oldalán lévő összeköttetésekhez. A társviszony-létesítési konfigurációval kapcsolatos információkat az [ExpressRoute-társviszony-létesítési konfigurációs lap](expressroute-howto-routing-arm.md)áttekintésével kaphatja meg.
* Információ a hálózati csapat / kapcsolat szolgáltató a MAC-címek használt interfészek ezekkel az IP-címekkel.
* Rendelkeznie kell a legújabb PowerShell-modul az Azure-hoz (1.50-es vagy újabb verzió).

> [!NOTE]
> Ha a 3. Ez a művelet a megfelelő útválasztási konfigurációt fogja alkalmazni a kapcsolatcsoporton. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Az ExpressRoute-kapcsolat ARP-tábláinak beszerzése
Ez a szakasz a PowerShell használatával társviszony-létesítésenként megtekintheti az ARP-táblák megtekintését. Önnek vagy a kapcsolatszolgáltatónak konfigurálnia kell a társviszony-létesítést, mielőtt továbbhaladna. Minden körnek két elérési útja van (elsődleges és másodlagos). Az egyes görbék ARP-táblája egymástól függetlenül ellenőrizhető.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-táblák az Azure privát társviszony-létesítéséhez
A következő parancsmag az ARP-táblákat biztosítja az Azure privát társviszony-létesítéséhez

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

A mintakimenet az alábbiakban látható az egyik útvonalhoz

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP-táblák az Azure nyilvános társviszony-létesítéséhez
A következő parancsmag az ARP-táblákat biztosítja az Azure nyilvános társviszony-létesítéséhez

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


A mintakimenet az alábbiakban látható az egyik útvonalhoz

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>ARP-táblák Microsoft társviszony-létesítéshez
A következő parancsmag a Microsoft társviszony-létesítésének ARP-tábláit tartalmazza

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


A mintakimenet az alábbiakban látható az egyik útvonalhoz

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Az információk felhasználása
A társviszony-létesítés ARP-táblája a 2. Ez a szakasz áttekintést nyújt arról, hogyan fognak kinézni az ARP-táblák a különböző forgatókönyvek alatt.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>ARP-tábla, ha egy áramkör üzemi állapotban van (várt állapot)
* Az ARP-táblában lesz egy bejegyzés a helyszíni oldalra érvényes IP-címmel és MAC-címmel, valamint hasonló bejegyzéssel a Microsoft oldalán. 
* A helyszíni IP-cím utolsó oktettje mindig páratlan szám lesz.
* A Microsoft IP-cím utolsó oktettje mindig páros szám lesz.
* Ugyanaz a MAC-cím jelenik meg a Microsoft oldalán mind a 3 társviszony (elsődleges / másodlagos) esetében. 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>ARP tábla, ha a helyszíni / kapcsolódási szolgáltató oldalán problémák vannak
Ha problémák merülnek fel a helyszíni vagy a kapcsolatszolgáltatóval kapcsolatban, előfordulhat, hogy vagy csak egy bejegyzés jelenik meg az ARP-táblában, vagy a helyszíni MAC-cím nem lesz teljes. Ez a Microsoft oldalán használt MAC-cím és IP-cím közötti leképezést jeleníti meg. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

vagy
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Az ilyen problémák hibakereséséhez nyisson meg egy támogatási kérelmet a kapcsolódási szolgáltatónál. Ha az ARP-tábla nem rendelkezik a MAC-címekhez leképezett összeköttetések IP-címeivel, tekintse át a következő információkat:
> 
> 1. Ha az MSEE-PR és az MSEE közötti kapcsolathoz rendelt /30 alhálózat első IP-címét használja az MSEE-PR interfészen. Az Azure mindig a második IP-címet használja az MSEEs-hez.
> 2. Ellenőrizze, hogy az ügyfél (C-Tag) és a szolgáltatás (S-Tag) VLAN-címkék megfelelnek-e mind az MSEE-PR, mind az MSEE páron.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>ARP-tábla, ha a Microsoft oldalának problémái vannak
* Ha problémák merülnek fel a Microsoft oldalán, akkor nem jelenik meg a társviszony-létesítéshez a társviszony-létesítéshez megjelenő ARP-tábla. 
* Nyisson meg egy támogatási jegyet a [Microsoft támogatásával.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Adja meg, hogy probléma van-e a 2. 

## <a name="next-steps"></a>Következő lépések
* 3. rétegkonfigurációinak ellenőrzése az ExpressRoute-kapcsolaton
  * Útvonal-összegzés beszerezni a BGP-munkamenetek állapotának meghatározásához 
  * Útvonaltábla beszereznie annak meghatározásához, hogy mely előtagokat hirdeti meg az ExpressRoute-on keresztül
* Adatátvitel ellenőrzése bájtok véleményezésével/be
* Nyisson meg egy támogatási jegyet a [Microsoft támogatási szolgálatával,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ha továbbra is problémákat tapasztal.

