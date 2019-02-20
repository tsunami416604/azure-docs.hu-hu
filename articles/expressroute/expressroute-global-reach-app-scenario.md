---
title: Az Azure ExpressRoute globális érhető el a forgatókönyvet |} A Microsoft Docs
description: Ezen a lapon globális elérhetőségű biztosít egy forgatókönyvet.
documentationcenter: na
services: networking
author: cherylmc
manager: tracsman
ms.service: expressroute,global reach
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: cherylmc
ms.openlocfilehash: 2adb8debf641a9b3875c5c386df7a35273f2a258
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428238"
---
# <a name="expressroute-global-reach-application-scenario"></a>Az ExpressRoute globális elérhetőségű alkalmazások forgatókönyv

Az ExpressRoute globális elérhetőségű kapcsolatos további információkért lásd: [ExpressRoute globális elérhetőségű][Global Reach]. Ebben a cikkben nézzük végig egy forgatókönyvet, hasonlítsa össze az ExpressRoute globális elérhetőségű megoldások néhány egyéb megoldások, konfigurálja a globális elérhetőségű a példaforgatókönyvhöz a és a kapcsolatok ellenőrzése. 

##<a name="application-scenario"></a>Alkalmazási forgatókönyv

A Fabrikam, Inc. rendelkezik egy nagy méretű fizikai jelenlét és az Azure-telepítés az USA keleti régiójában. A Fabrikam rendelkezik háttér-kapcsolattal a helyszíni és Azure expressroute-on keresztül-környezetek között. Contoso Ltd. jelenléte és az USA nyugati Azure üzembehelyezési rendelkezik. Contoso rendelkezik háttér-kapcsolattal a helyszíni és Azure expressroute-on keresztül-környezetek között.  

A Fabrikam, Inc. beszerzi a Contoso Kft. A fúzió, a következő Fabrikam azt szeretné, a hálózatok összekapcsolhatók. Az alábbi ábra a forgatókönyvet mutatja be:

 [![1]][1]

A fenti ábrán közepén szaggatott nyilak jelzik a szükséges hálózati csatlakozás. Az alábbi táblázat az útválasztási táblázatot, az elsődleges magánhálózati társviszony-létesítés, az ExpressRoute a Contoso Ltd. a egyesülés előtt.

[![2]][2]

Az alábbi táblázat az útválasztási táblázatot, az elsődleges magánhálózati társviszony-létesítés, az ExpressRoute a Fabrikam Inc. az egyesülés előtt.

[![3]][3]

## <a name="traditional-solutions"></a>A hagyományos megoldások

### <a name="option-1-interconnect-on-premises-networks"></a>Option 1: Ezek a helyszíni hálózatok

Az alábbi ábra ezt a beállítást. Ahogy látható, a két helyszíni hálózat, a site-to-site VPN vagy más szélessávú kapcsolat beállításai követelő, és minden átirányításának kezelését a két entitás között. 

[![4]][4]

Ez a beállítás a következő hátrányokkal tartalmazza:

- Optimálisnál rosszabb útvonal keresztül kényszeríti a régiók közötti Azure kommunikáció a központi telepítés.
- Ön elutasítja az az előnye, hogy a Microsoft gerinchálózatán a régiók közötti kommunikáció magas rendelkezésre állását.
- A régiók közötti kommunikáció felelősséget teljes útválasztási még tart.

### <a name="option-2-expressroute-cross-connectivity-and-interconnect-on-premises-networks"></a>Option 2: Az ExpressRoute közötti kapcsolatot, és a helyszíni hálózatok összekapcsolására

Az alábbi ábra ezt a beállítást.

[![5]][5]

A fenti ábrán látható, ezen kívül is létrehozhat az ExpressRoute-Kapcsolatcsoportok, a keresztszűrés regionális virtuális hálózatok közötti kapcsolatot. Az ExpressRoute-Kapcsolatcsoportok a VNet közötti regionális közötti kapcsolatot lehetővé tenné a következő kommunikációhoz:

- Az USA nyugati régiója / az USA keleti régiójában virtuális hálózatok a Fabrikam/Contoso megfelelő kommunikálni a helyszíni hálózatokkal.
- Az USA nyugati régiója virtuális hálózat számára az USA keleti régiójában virtuális hálózatok közötti kommunikációhoz.

A két helyszíni hálózat közötti kapcsolatot biztosítja továbbra is szükség van a helyszíni hálózatok egymással kommunikálni.

Ez a beállítás lehetővé teszi, hogy a régiók közötti Azure a Microsoft gerinchálózatán keresztül ledolgozni privát központi telepítésére vonatkozó és a kommunikációt a külső hálózaton kell végezni a helyszíni hálózat között. A fő hátrány, a megoldás azonban nem kell több közötti regionális kapcsolatokat, amelyek karbantartási és hibaelhárítási nehezíti. Is a beállítás nem teszi lehetővé a két helyszíni hálózat közötti kommunikáció magas rendelkezésre állású Microsoft globális gerincét előnyeit.

### <a name="option-3-vnet-peering-and-interconnect-on-premises-networks"></a>3. lehetőség: Virtuális hálózatok közötti társviszony és a helyszíni hálózatok összekapcsolására

Az alábbi ábra ezt a beállítást. A beállítás a virtuális hálózatok közötti társviszonyt a régiók közötti virtuális hálózatok közötti kommunikációhoz használ. A beállítás, szemléltetett módon nem fejeződött be, azt nem oldja meg a régiófüggetlen virtuális hálózat és helyszíni közötti kommunikációhoz (a két pontozott nyíl sort a középső keresztül jelzi). Használja a helyszíni helyszíni kapcsolatot (ahogy az 1. lehetőség) vagy ExpressRoute kapcsolattal (ahogy a 2. lehetőség) a régiók közötti és a helyszíni közötti kommunikációhoz.

[![6]][6]

Ez a beállítás a régiók közötti virtuális hálózatok közötti kommunikáció optimális útválasztást biztosítanak. Azonban esik-e a Fabrikam vagy a Contoso egy összetettebb Azure üzembe helyezési Központ-küllő virtuális hálózatok közötti modell például rövid. Is például az előző két beállítás ezzel a beállítással nem engedi kihasználhatja a magas rendelkezésre állású Microsoft globális gerinchálózatán keresztül a két helyszíni hálózat közötti kommunikáció.

## <a name="global-reach"></a>Globális elérhetőség

Globális elérhetőségű ExpressRoute privát társviszony-létesítés, az ExpressRoute-Kapcsolatcsoportok az alábbi ábrán szemléltetett módon hivatkozik:

[![7]][7]

Globális elérhetőségű konfigurálása a két ExpressRoute-Kapcsolatcsoportok között lehetővé teszi a privát kommunikációt a két helyszíni hálózat és az Azure üzembe helyezés a két régió között. Ebből kifolyólag a globális elérhetőségű eleget tesz a kívánt kommunikációt (keresztül ez a cikk az első ábra szaggatott vonal jelzi) a Microsoft gerinchálózatán keresztül.

### <a name="configure-global-reach"></a>Globális elérhetőség konfigurálása

ExpressRoute globális elérhetőségű konfigurálásával kapcsolatban lásd: [globális elérhetőségű konfigurálása][Configure Global Reach]. 

Mivel a Fabrikam, Inc., és a Contoso Ltd előkészítve az Azure, külön vállalat, az ExpressRoute-Kapcsolatcsoportok a két vállalat két különböző Azure-előfizetésekben találhatók. A globális elérhetőségű létrehozni az előfizetések között, hozzon létre egy engedélyezési a Contoso Ltd tartozó ExpressRoute-kapcsolatcsoportot, és adja át azt a Fabrikam Inc. szeretne ExpressRoute-kapcsolatcsoportot.


Hozzon létre egy ExpressRoute-kapcsolatcsoport a Contoso engedélyezési, az első bejelentkezésnél a Contoso Azure-fiókba, és (Ha több előfizetése van), válassza ki a megfelelő előfizetést. Ezen lépésekhez a PowerShell-parancsok a következők:

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```
Az ExpressRoute-kapcsolatcsoport engedélyezése létrehozásának lépései az alábbiak:

```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```

A "Set-AzureRmExpressRouteCircuit" kimenete felsorolja az expressroute-kapcsolatcsoport sávszélességének. Vegye figyelembe a privát társviszony-létesítési azonosítója és a hitelesítési kulcs, amely a lista végén lesz feltüntetve. Egy példa PowerShell kimeneti alábbi kódrészlethez lásd:

```powershell
ServiceKey                       : <serviceKey>
Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 65020,
                                       "PrimaryPeerAddressPrefix": "192.168.11.16/30",
                                       "SecondaryPeerAddressPrefix": "192.168.11.20/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 110,
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "4",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
Authorizations                   : [
                                     {
                                       "Name": "Auth4-ASH-Cust11",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/authorizations/Auth4-ASH-Cust11",
                                       "AuthorizationKey": "<authorizationKey>",
                                       "AuthorizationUseStatus": "Used",
                                       "ProvisioningState": "Succeeded"
                                     }
                                   ]
AllowClassicOperations           : False
GatewayManagerEtag               : 4
```

A társviszony-létesítési Azonosítójú és a hitelesítési kulcs hozhat létre a globális elérhetőségű a Fabrikam ExpressRoute-kapcsolatcsoport alatt. Jelentkezzen be a Fabrikam az Azure-fiókjába. Ha több előfizetéssel, válassza ki a megfelelő előfizetést.

Globális elérhetőség jönnek létre a redundáns pontok közti kapcsolatok a két MSEE párok között. A két pont-pont típusú kapcsolatokhoz, meg kell adnia egy/29 címelőtag (a futó példában használjuk 192.168.11.64/29). A következő parancsokat használja a globális elérhetőségű kapcsolat létrehozása:

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix "__.__.__.__/29" -AuthorizationKey "########-####-####-####-############"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

A fenti parancsok lesznek végrehajtva, ha eltarthat néhány percig, a redundáns globális elérhetőségű kapcsolatok létrehozásához.

### <a name="verify-global-reach"></a>Globális elérhetőség ellenőrzése

Az alábbi táblázat az útválasztási táblázatot, az elsődleges magánhálózati társviszony-létesítés, a Contoso Ltd. ExpressRoute globális elérhetőségű konfigurálása után.

[![8]][8]

Az alábbi táblázat az útválasztási táblázatot, az elsődleges magánhálózati társviszony-létesítés, ExpressRoute Fabrikam Inc. globális elérhetőségű konfigurálása után.

[![9]][9]

A fenti táblázatokban látható összes várt cél "Hálózat" előtag, és a megfelelő "következő ugrás" vannak felsorolva.

Újabb tekintse meg az ExpressRoute-Kapcsolatcsoportok privát társviszony alatt az Azure Portalon elérhető "Get útvonaltábla" panelen. Az ExpressRoute útválasztási táblázatot a következő PowerShell-paranccsal is listázhatja:

```powershell
Get-AzExpressRouteCircuitRouteTable -DevicePath 'primary' -ExpressRouteCircuitName "Your_circuit_name" -PeeringType AzurePrivatePeering -ResourceGroupName "Your_resource_group"
```

Az útválasztási táblázatot, a másodlagos MSEE megtekintéséhez helyettesítse be az "elsődleges" a "másodlagos", a fenti parancsban kulcsszóval.

Nézzük is ellenőrizheti az adatkapcsolat adatsík által a különböző hálózatokról származó pingelése másik cél. A következő három pingelésre. Ellenőrizze a data adatsík kapcsolatot a Contoso a helyszíni hálózathoz, a Contoso Azure virtuális hálózatot, és a Fabrikam Fabrikam Azure-beli virtuális helyi hálózat.


    C:\Users\FabrikamUser>ping 10.1.11.10
    
    Pinging 10.1.11.10 with 32 bytes of data:
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    
    Ping statistics for 10.1.11.10:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 69ms, Maximum = 69ms, Average = 69ms
    
    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=82ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=81ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 82ms, Average = 78ms
    
    C:\Users\FabrikamUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=2ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 2ms, Maximum = 3ms, Average = 2ms


A következő két pingelésre. Ellenőrizze a adatok adatsík kapcsolatot a Contoso Azure virtuális hálózat és a Fabrikam Azure-beli virtuális Contoso a helyszíni hálózatról.

    C:\Users\ContosoUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=6ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 5ms, Maximum = 6ms, Average = 5ms
    
    C:\Users\ContosoUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=74ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 73ms, Maximum = 74ms, Average = 73ms

A következő ping adatkapcsolat adatsík a Contoso Azure vnethez a Fabrikam az Azure vnet ellenőrzi.

    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=78ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 78ms, Average = 77ms

## <a name="optimization-for-latency-sensitive-traffic"></a>A késés bizalmas forgalom optimalizálása

Globális elérhetőség irányítja a forgalmat a Microsoft Edge-eszközök használatával. Az adott forgatókönyvhöz részt ebben a cikkben akkor érhető el, további optimális útválasztást engedélyezi a virtuális hálózatok közötti társviszony-létesítés közöttük a két virtuális hálózat között. Ugyanígy akkor érhető el, további optimális útválasztást egy szolgáltató, amely biztosít a helyek között egy közvetlenebb WAN-kapcsolaton keresztül a két helyszíni hálózat között. Ilyen esetekben használhatja a globális elérhetőségű útválasztás sikertelen vissza szeretné végrehajtani ezeket a kapcsolatokat. 

## <a name="next-steps"></a>További lépések

Globális elérhetőség ország szerint ország alapján jelennek meg. Ha a globális elérhetőségű országban érhető el a kívánt megtekintéséhez lásd: [ExpressRoute globális elérhetőségű][Global Reach].

<!--Image References-->
[1]: ./media/expressroute-globalreach-applicationscenario/PreMergerScenario.png "az alkalmazási forgatókönyv"
[2]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Premerger.png "Contoso ExpressRoute útválasztási táblázat egyesülés előtt"
[3]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Premerger.png "Fabrikam ExpressRoute útválasztási táblázat egyesülés előtt"
[4]: ./media/expressroute-globalreach-applicationscenario/S2SVPN-Solution.png "biztosítva a helyszíni hálózatok"
[5]: ./media/expressroute-globalreach-applicationscenario/ExR-XConnect-Solution.png "ExpressRoute Cross Connect"
[6]: ./media/expressroute-globalreach-applicationscenario/VNet-peering-Solution.png "virtuális hálózatok közötti társviszony"
[7]: ./media/expressroute-globalreach-applicationscenario/GlobalReach-Solution.png "globális elérhetőséggel"
[8]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Postmerger.png "Contoso ExpressRoute útválasztási táblázatot, a globális elérhetőségű"
[9]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Postmerger.png "Fabrikam ExpressRoute útválasztási táblázatot, a globális elérhetőségű"

<!--Link References-->
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach





