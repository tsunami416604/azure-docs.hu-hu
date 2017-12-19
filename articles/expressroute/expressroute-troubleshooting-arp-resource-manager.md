---
title: "ARP-tábla beolvasása: erőforrás-kezelő: Azure ExpressRoute-hibaelhárítási |} Microsoft Docs"
description: "Ezen a lapon utasításokkal szolgál az ExpressRoute-kapcsolatcsoportot az ARP tábla beolvasása"
documentationcenter: na
services: expressroute
author: ganesr
manager: carolz
editor: tysonn
ms.assetid: 0a6bf1d5-6baf-44dd-87d3-1ebd2fd08bdc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: a65b1ba2998eae33b3e73bd2492fbbf025eb5946
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>A Resource Manager üzembe helyezési modellel tábla ARP beolvasása
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klasszikus](expressroute-troubleshooting-arp-classic.md)
> 
> 

Ez a cikk végigvezeti az ExpressRoute-kapcsolatcsoportot ARP táblázatokban további lépéseket. 

> [!IMPORTANT]
> Ez a dokumentum olyan egyszerű problémák megoldásában segítséget. Nem célja a helyettesítheti a Microsoft támogatási szolgálatához. Meg kell nyitnia a támogatási jegy [Microsoft támogatási szolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Ha nem tudja megoldani a problémát az alább ismertetett útmutatás.
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Cím Resolution Protocol (ARP) és a ARP
Address Resolution Protocol (ARP) definiálva a 2. réteg protokoll [RFC 826](https://tools.ietf.org/html/rfc826). ARP szolgál az Ethernet-cím (MAC-cím) IP-címet hozzárendelni.

A táblázat ARP az ipv4-cím és MAC-címet adott társviszony-létesítés leképezéseket. Egy ExpressRoute-kapcsolatcsoport társviszonyt ARP táblázatban a következő információkkal csatolóhoz (elsődleges és másodlagos)

1. A helyi útválasztó illesztő IP-címet a MAC-cím hozzárendelése
2. ExpressRoute útválasztó illesztő IP-címet a MAC-cím hozzárendelése
3. A leképezési korát

ARP-táblázatok érvényesíteni a 2. réteg segítségével, és alapvető hibaelhárítási réteg 2 kapcsolódási problémák. 

Példa ARP-táblázat: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


A következő szakasz tájékoztatást nyújt a láthatók az ExpressRoute peremhálózati útválasztók ARP-táblázatok megtekintésének. 

## <a name="prerequisites-for-learning-arp-tables"></a>ARP-táblázatok tanulási előfeltételei
Győződjön meg arról, hogy rendelkezik a következő, mielőtt további előrehaladás

* Egy érvényes ExpressRoute-kapcsolatcsoportot legalább egy társviszony-létesítés konfigurálva. A kapcsolatcsoport teljesen kell beállítani a kapcsolat szolgáltatóját. Ön (vagy a kapcsolat szolgáltatóját) kell konfigurálni kell a társviszony (Azure saját, az Azure nyilvános és a Microsoft) közül legalább egy ebben a kapcsolatcsoportban.
* A társviszony (Azure saját, az Azure nyilvános és a Microsoft) konfigurálásához használt IP-címtartományok. Tekintse át az ip-cím hozzárendelés szereplő példák a [ExpressRoute útválasztási követelmények lapon](expressroute-routing.md) megértéséhez hogyan IP-címek vannak leképezve az ügyféloldali és az ExpressRoute oldalán felületek segítségével. A társviszony-létesítési konfiguráció tájékoztatást kaphat megtekintésével a [ExpressRoute-társviszony-létesítési konfiguráció lapon](expressroute-howto-routing-arm.md).
* A hálózati csoport adatait / ezek IP-címekkel rendelkező használt adapterek MAC-címet a kapcsolat szolgáltatóját.
* A legújabb PowerShell-modult az Azure-ba (1,50 vagy újabb verzió) kell rendelkeznie.

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Az ExpressRoute-kapcsolatcsoportot az ARP tábla beolvasása
Ez a szakasz utasításokat biztosít a ARP-táblázatok / társviszony a PowerShell használatával megtekinteni. Ön vagy a kapcsolat szolgáltatójánál úgy kell konfigurálnia a társviszony-létesítés mielőtt elmélyedne a további. Minden kapcsolat van két elérési útnak (elsődleges és másodlagos). Az egyes elérési utakat ARP-táblázat egymástól függetlenül ellenőrizheti.

### <a name="arp-tables-for-azure-private-peering"></a>Az Azure magánhálózati társviszony-létesítés ARP-táblázatok
A következő parancsmag biztosít az ARP táblák Azure magánhálózati társviszony-létesítés

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Az elérési utak közül legalább egy alább minta kimenet

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Az Azure nyilvános társviszony ARP-táblázatok
A következő parancsmag biztosít az ARP táblák az Azure nyilvános társviszony-létesítés

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Az elérési utak közül legalább egy alább minta kimenet

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>A Microsoft társviszony-létesítéshez ARP-táblázatok
A következő parancsmag biztosít az ARP táblák Microsoft társviszony-létesítés

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Az elérési utak közül legalább egy alább minta kimenet

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Ezek az információk használata
A társviszony ARP-táblázat segítségével határozza meg 2. réteg konfiguráció és a kapcsolat ellenőrzése. Ez a szakasz áttekintést ARP-táblázatok megjelenését a különböző helyzetekben.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>ARP-táblázat Ha expressroute-kapcsolatcsoporthoz működési állapot (várt állapot)
* ARP-táblázat egy bejegyzést a helyszíni oldalon egy érvényes IP-cím és MAC-cím és egy hasonló bejegyzést a Microsoft oldalon lesz. 
* A helyi IP-cím utolsó oktettje mindig lesz páratlan szám.
* A Microsoft IP-cím utolsó oktettje mindig lesz páros szám.
* Az azonos MAC-cím jelenik meg a Microsoft oldalon az összes 3 társviszony (elsődleges / másodlagos). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>ARP táblából helyszíni / szolgáltató kiszolgálóoldali csatlakozási problémák vannak
Ha a helyszíni problémák vagy láthatja, hogy vagy csak egy bejegyzés az ARP-táblázat vagy a helyszíni MAC-cím fog megjelenni a kapcsolat szolgáltatójánál hiányos jelennek meg. Ez megjeleníti a MAC-cím és a Microsoft oldal használt IP-cím közötti leképezést. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

vagy
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> A kapcsolat szolgáltatójánál, az ilyen problémák hibakeresését támogatási kérést nyithat. Ha az ARP-táblázat nem rendelkezik a MAC-címek hozzárendelve felületek IP-címét, áttekintheti a következőket:
> 
> 1. Ha az első IP-címét a/30-as alhálózat hozzárendelt MSEE-PR és MSEE közötti kapcsolat MSEE-PR. felületén használja a rendszer Azure mindig MSEEs a második IP-címet használja.
> 2. Győződjön meg arról, ha az ügyfél (C-címke) és a VLAN-címkék szolgáltatás (S-címke) felel meg a MSEE-PR és MSEE pár mindkét.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Ha a Microsoft ügyféloldali problémák vannak az ARP-táblázat
* Nem látják az ARP-táblázat társviszony-létesítés Ha problémák vannak a Microsoft oldalán látható. 
* A támogatási jegy megnyitása [Microsoft támogatási szolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Adja meg, hogy rendelkezik-e 2. rétegbeli kapcsolatot kapcsolatos problémát. 

## <a name="next-steps"></a>Következő lépések
* Ellenőrizze a ExpressRoute-kapcsolatcsoportot 3. rétegbeli konfigurációi
  * A BGP-munkamenetek állapotának megállapításához ellenőrizze az összefoglaló lekérése útvonal 
  * Annak meghatározásához, hogy mely előtagok ExpressRoute között van-e hirdetve útvonaltábla beolvasása
* Be- / kimeneti bájt megtekintésével adatátvitel ellenőrzése
* A támogatási jegy megnyitása [Microsoft támogatási szolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Ha továbbra is problémákat tapasztal.

