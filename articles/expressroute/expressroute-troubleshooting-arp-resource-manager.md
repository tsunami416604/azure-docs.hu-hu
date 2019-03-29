---
title: 'Első ARP-táblák - hibáinak elhárítása – ExpressRoute: Azure| Microsoft Docs'
description: Ez a lap utasításokat tartalmaz az ARP-a táblák ExpressRoute-kapcsolatcsoport
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 76e242adb07f4e6176bbdc6c03c75950e3732c2b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622076"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>ARP-táblák a Resource Manager-alapú üzemi modellben
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klasszikus](expressroute-troubleshooting-arp-classic.md)
> 
> 

Ez a cikk bemutatja az ExpressRoute-kapcsolatcsoport ARP-táblák további lépéseit.

> [!IMPORTANT]
> A dokumentum célközönsége, egyszerű hibák azonosítása és kijavítása érdekében. Nem célja a helyettesítheti a Microsoft ügyfélszolgálatához. Meg kell nyitnia egy támogatási jegyet a [a Microsoft támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Ha nem tudja megoldani a problémát, használja az alább leírt útmutatást.
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Cím Resolution Protocol (ARP) és az ARP-táblák
Address Resolution Protocol (ARP) definiálva a 2. rétegbeli protokoll [RFC 826](https://tools.ietf.org/html/rfc826). ARP az Ethernet-címe (MAC-cím) ip-címmel leképezésére szolgál.

ARP-táblázat tartalmazza az ipv4-cím és MAC-cím egy adott társviszony-létesítéshez. Egy ExpressRoute-kapcsolatcsoport társviszony az ARP-táblázat a következő információkkal az egyes kapcsolatok (elsődleges és másodlagos)

1. A helyi útválasztó adapter ip-címet a MAC-cím hozzárendelése
2. Az ExpressRoute útválasztó adapter ip-címet a MAC-cím hozzárendelése
3. A hozzárendelés élettartama

ARP-táblák segítségével ellenőrizze a 2. rétegbeli konfigurációját, és 2 csatlakozási problémák hibaelhárítása alapszintű réteg. 

ARP-táblázat például: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


A következő szakaszban látható az ExpressRoute peremhálózati útválasztók ARP-táblák megtekintésének információkat biztosít. 

## <a name="prerequisites-for-learning-arp-tables"></a>ARP-táblák tanulási előfeltételei
Gondoskodjon arról, hogy a következő további végrehajtási előtt

* Egy érvényes ExpressRoute-kapcsolatcsoport legalább egy társviszony-létesítés konfigurálva. A kapcsolatcsoport a kapcsolatszolgáltató által teljes kell konfigurálni. Ön (vagy a kapcsolatszolgáltató) kell konfigurálni kell legalább egy társviszony-létesítések (az Azure privát, Azure nyilvános és Microsoft) ebben a kapcsolatcsoportban.
* Az IP-címtartományok a társviszony-létesítések (az Azure privát, Azure nyilvános és Microsoft) konfigurálásához használt. Tekintse át az ip-cím hozzárendelés szereplő példák a [ExpressRoute útválasztási követelményei lapon](expressroute-routing.md) végigvezetnek, hogyan az ügyféloldali és az ExpressRoute oldalán felületek ip-címek vannak leképezve. A társviszony-létesítés konfigurációját a információkat szerezhet a áttekintésével a [ExpressRoute-társviszony-létesítési konfiguráció lap](expressroute-howto-routing-arm.md).
* A hálózatkezelésért felelős csapat adatait / adapterek MAC-címek a kapcsolatszolgáltató használja a rendszer ezen IP-címmel.
* A legújabb PowerShell-modult az Azure-hoz (1,50 vagy újabb verzió) kell rendelkeznie.

> [!NOTE]
> Ha a szolgáltató által biztosított 3. rétegbeli és az ARP-táblák üres, a portál/kimenet az alábbi, a frissítés gombra a portál használatával kapcsolatcsoport beállításainak frissítése. Ez a művelet a kapcsolatcsoport alkalmazzák a megfelelő útválasztási konfigurációja. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>ARP-a táblák az ExpressRoute-kapcsolatcsoport
A szakasz útmutatásai segítségével hogyan az ARP-táblák támaszkodunk a PowerShell használatával tekintheti meg. Ön vagy a kapcsolatszolgáltató rendelkezik konfigurálni kell a társviszony-létesítés előtt halad tovább. Minden egyes-kapcsolatcsoport két elérési útját (elsődleges és másodlagos) rendelkezik. ARP-táblázat minden útvonalhoz egymástól függetlenül megtekinthető.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-táblák az Azure privát társviszony-létesítéshez
A következő parancsmagot biztosít az ARP táblák az Azure privát társviszony-létesítés

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Kimeneti elérési utak egyik az alább látható

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP-táblák, Azure nyilvános társviszony-létesítéshez
A következő parancsmagot biztosít az ARP táblák az Azure nyilvános társviszony-létesítés

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Kimeneti elérési utak egyik az alább látható

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>A Microsoft társviszony-létesítéshez ARP-táblák
A következő parancsmagot biztosít az ARP táblák Microsoft társviszony-létesítés

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Kimeneti elérési utak egyik az alább látható

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Ezek az információk használata
A társviszony-létesítések ARP-táblázat segítségével határozza meg 2. rétegbeli konfiguráció és a kapcsolat ellenőrzése. Ebben a szakaszban arról, hogyan fog megjelenni az ARP-táblák különböző forgatókönyvek áttekintése.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>ARP táblából, ha a kapcsolatcsoport van a működési állapot (várt állapot)
* ARP-táblázat egy érvényes IP-cím és MAC-címet a helyszíni oldal és a Microsoft oldal egy hasonló bejegyzés lesz. 
* Az utolsó oktettet helyi ip-cím minden esetben páratlan szám.
* Az utolsó oktettet Microsoft ip-cím minden esetben páros szám.
* Az azonos MAC-cím jelenik meg a Microsoft társviszony-létesítéshez az összes 3 (elsődleges / másodlagos) oldalon. 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>ARP táblából a helyszíni / szolgáltató kiszolgálóoldali csatlakozási problémák vannak
Ha probléma adódik a helyszíni vagy a kapcsolatszolgáltató látni, hogy mindkét csak egy bejegyzés jelenik meg a saját ARP- vagy a helyi MAC-címe nem teljes jelennek meg. Ez a MAC-cím és a Microsoft oldalán használt IP-cím közötti jelennek meg. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

vagy
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Nyisson egy támogatási kérelmet a kapcsolat szolgáltatójánál problémák hibakeresése. Ha ARP-táblázat nem rendelkezik a MAC-címek hozzárendelve felületek IP-címeket, tekintse át a következő információkat:
> 
> 1. Ha az első IP-cím / 30 alhálózat hozzárendelt MSEE-közösségbeli adapterén használja az MSEE-PR és MSEE közötti kapcsolat Azure mindig Msee a második IP-címet használ.
> 2. Győződjön meg arról, ha az ügyfél (C-címke) és a VLAN címkéket (S-címke) szolgáltatás megfelelő mindkét MSEE-PR-MSEE párt.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Ha problémája van a Microsoft oldalán ARP-táblázat
* Nem láthatja az ARP-táblázat egy társviszony-létesítéshez probléma merül fel a Microsoft oldalán látható. 
* Nyisson meg egy támogatási jegyet a [a Microsoft támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Adja meg, hogy a 2. rétegbeli kapcsolódási probléma. 

## <a name="next-steps"></a>További lépések
* 3. rétegbeli konfigurációi az ExpressRoute-kapcsolatcsoport ellenőrzése
  * Route BGP-munkamenetek állapotának meghatározásához összefoglalójának lekérése 
  * Szerezze be az útvonaltábla meghatározni, melyik előtagokat hirdet meg között ExpressRoute
* Ellenőrizze az adatátvitel / kimenő bájtok áttekintésével
* Nyisson meg egy támogatási jegyet a [a Microsoft támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Ha továbbra is problémákat tapasztal.

