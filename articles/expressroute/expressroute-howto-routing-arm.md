---
title: ExpressRoute-kapcsolatcsoport útválasztásának konfigurálása | Microsoft Docs
description: A cikk az ExpressRoute-kapcsolatcsoportok privát, nyilvános és Microsoft társviszony-létesítéses létrehozásának és kiépítésének lépéseit ismerteti. A cikk azt is bemutatja, hogyan ellenőrizheti a kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti őket.
documentationcenter: na
services: expressroute
author: ganesr
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: expressroute
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/05/2016
ms.author: ganesr

---
# ExpressRoute-kapcsolatcsoport útválasztásának létrehozása és módosítása
> [!div class="op_single_selector"]
> [Azure Portal – Resource Manager](expressroute-howto-routing-portal-resource-manager.md)
> [PowerShell – Resource Manager](expressroute-howto-routing-arm.md)
> [PowerShell – Klasszikus](expressroute-howto-routing-classic.md)
> 
> 

A cikk az ExpressRoute-kapcsolatcsoportok útválasztási konfigurációjának létrehozási és módosítási lépéseit ismerteti a PowerShell és az Azure Resource Manager-alapú üzemi modell használatával.  Az alábbi lépések azt is bemutatják, hogyan ellenőrizheti az ExpressRoute-kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti és szüntetheti meg őket. 

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Konfigurációs előfeltételek
* Az Azure PowerShell-modulok legújabb. 1.0-s vagy újabb verzióira lesz szükség. 
* A konfigurálás megkezdése előtt mindenképp tekintse át az [előfeltételek](expressroute-prerequisites.md), az [útválasztási követelmények](expressroute-routing.md) és a [munkafolyamatok](expressroute-workflows.md) lapot.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoportnak kiosztott és engedélyezett állapotban kell lennie, hogy az alább ismertetett parancsmagokat futtatni lehessen.

Az utasítások csak 2. rétegbeli kapcsolatszolgáltatásokat kínáló szolgáltatóknál létrehozott kapcsolatcsoportokra vonatkoznak. Ha 3. rétegbeli szolgáltatásokat kínáló szolgáltatóval rendelkezik (tipikusan IPVPN, mint az MPLS), a kapcsolatszolgáltató konfigurálja és felügyeli az útválasztást Ön helyett.

> [!IMPORTANT]
> A szolgáltatásfelügyeleti portálon jelenleg nem hirdetünk szolgáltatók által konfigurált társviszony-létesítéseket. Dolgozunk azon, hogy hamarosan bevezethessük ezt a képességet. Mielőtt konfigurálná a BGP társviszony-létesítéseket, tájékozódjon szolgáltatójánál.
> 
> 

Egy, két vagy akár mindhárom társviszony-létesítést (Azure privát, Azure nyilvános és Microsoft) is konfigurálhatja egy adott ExpressRoute-kapcsolatcsoportban. A társviszony-létesítéseket tetszőleges sorrendben konfigurálhatja. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. 

## Azure privát társviszony-létesítés
Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Azure privát társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat. 

### Azure privát társviszony-létesítés létrehozása
1. Importálja az ExpressRoute PowerShell-modulját.
   
    Telepítse a legújabb PowerShell-telepítőt a [PowerShell-galériából](http://www.powershellgallery.com/), és importálja az Azure Resource Manager-modulokat a PowerShell-munkamenetbe az ExpressRoute-parancsmagok használatának elkezdéséhez. A PowerShellt rendszergazdaként kell futtatnia.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    Importálja az összes AzureRM.* modult az ismert szemantikai verziótartományon belül.
   
        Import-AzureRM
   
    Importálhatja csak az egyik választott modult is az ismert szemantikai verziótartományon belül. 
   
        Import-Module AzureRM.Network 
   
    Jelentkezzen be a fiókjába
   
        Login-AzureRmAccount
   
    Válassza ki az előfizetést, amelyben az ExpressRoute-kapcsolatcsoportot létre kívánja hozni.
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot.
   
    Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-arm.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. 
   
    Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure privát társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat. 
3. Ellenőrizze, hogy az ExpressRoute-kapcsolatcsoport ki van-e osztva.
   
    Először ellenőrizze, hogy az ExpressRoute-kapcsolatcsoport Kiosztott és Engedélyezett állapotban van-e. Lásd az alábbi példát.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    A válasz az alábbi példához hasonló lesz:
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []
4. Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoport számára.
   
    Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:
   
   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ez nem képezheti semmilyen, virtuális hálózatok számára lefoglalt címtér részét.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ez nem képezheti semmilyen, virtuális hálózatok számára lefoglalt címtér részét.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társviszony-létesítéshez használhat privát AS-számokat is. Ne használja a 65515 számot.
   * Egy MD5-kivonat, ha használni kívánja. **Ez nem kötelező**.
     
     A következő parancsmag futtatásával konfigurálhatja az Azure privát társviszony-létesítést a kapcsolatcsoporthoz.
     
       Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200
     
       Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
     Használhatja az alábbi parancsmagot, ha MD5-kivonatot kíván használni.
     
       Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
     
       Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
     > [!IMPORTANT]
     > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.
     > 
     > 

### Azure privát társviszony-létesítés részleteinek megtekintése
A konfiguráció részleteit az alábbi parancsmaggal kérheti le.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt   


### Azure privát társviszony-létesítés konfigurációjának frissítése
A konfiguráció bármelyik részét frissítheti az alábbi parancsmag használatával. Az alábbi példában a kör VLAN azonosítóját 100-ról 500-ra frissítjük.

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Azure privát társviszony-létesítés törlése
A társviszony-létesítés konfigurációját a következő parancsmag futtatásával törölheti.

> [!WARNING]
> A parancsmag futtatása előtt győződjön meg róla, hogy az összes virtuális hálózat le van választva az ExpressRoute-kapcsolatcsoportról. 
> 
> 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## Azure nyilvános társviszony-létesítés
Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Azure nyilvános társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat.

### Azure nyilvános társviszony-létesítés létrehozása
1. Importálja az ExpressRoute PowerShell-modulját.
   
    Telepítse a legújabb PowerShell-telepítőt a [PowerShell-galériából](http://www.powershellgallery.com/), és importálja az Azure Resource Manager-modulokat a PowerShell-munkamenetbe az ExpressRoute-parancsmagok használatának elkezdéséhez. A PowerShellt rendszergazdaként kell futtatnia.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    Importálja az összes AzureRM.* modult az ismert szemantikai verziótartományon belül.
   
        Import-AzureRM
   
    Importálhatja csak az egyik választott modult is az ismert szemantikai verziótartományon belül. 
   
        Import-Module AzureRM.Network 
   
    Jelentkezzen be a fiókjába
   
        Login-AzureRmAccount
   
    Válassza ki az előfizetést, amelyben az ExpressRoute-kapcsolatcsoportot létre kívánja hozni.
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot.
   
    Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-arm.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. 
   
    Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure nyilvános társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat.
3. Ellenőrizze, hogy az ExpressRoute-kapcsolatcsoport ki van-e osztva.
   
    Először ellenőrizze, hogy az ExpressRoute-kapcsolatcsoport Kiosztott és Engedélyezett állapotban van-e. Lásd az alábbi példát.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    A válasz az alábbi példához hasonló lesz:
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   
4. Konfigurálja az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz.
   
    Mielőtt folytatná, ellenőrizze az alábbi információk meglétét:
   
   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Egy MD5-kivonat, ha használni kívánja. **Ez nem kötelező**.
     
     A következő parancsmag futtatásával konfigurálhatja az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz.
     
       Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100
     
       Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
     Használhatja az alábbi parancsmagot, ha MD5-kivonatot kíván használni.
     
       Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"
     
       Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    >[AZURE.IMPORTANT] Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.

### Azure nyilvános társviszony-létesítés részleteinek megtekintése
A konfiguráció részleteit az alábbi parancsmaggal kérheti le.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### Azure nyilvános társviszony-létesítés konfigurációjának frissítése
A konfiguráció bármelyik részét frissítheti az alábbi parancsmag használatával.

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

A fenti példában a kör VLAN-azonosítóját 200-ról 600-ra frissítjük.

### Azure nyilvános társviszony-létesítés törlése
A társviszony-létesítés konfigurációját a következő parancsmag futtatásával törölheti.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## Microsoft társviszony-létesítés
Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat. 

### Microsoft társviszony-létesítés létrehozása
1. Importálja az ExpressRoute PowerShell-modulját.
   
    Telepítse a legújabb PowerShell-telepítőt a [PowerShell-galériából](http://www.powershellgallery.com/), és importálja az Azure Resource Manager-modulokat a PowerShell-munkamenetbe az ExpressRoute-parancsmagok használatának elkezdéséhez. A PowerShellt rendszergazdaként kell futtatnia.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    Importálja az összes AzureRM.* modult az ismert szemantikai verziótartományon belül.
   
        Import-AzureRM
   
    Importálhatja csak az egyik választott modult is az ismert szemantikai verziótartományon belül. 
   
        Import-Module AzureRM.Network 
   
    Jelentkezzen be a fiókjába
   
        Login-AzureRmAccount
   
    Válassza ki az előfizetést, amelyben az ExpressRoute-kapcsolatcsoportot létre kívánja hozni.
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot.
   
    Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-arm.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. 
   
    Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure privát társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat.
3. Ellenőrizze, hogy az ExpressRoute-kapcsolatcsoport ki van-e osztva.
   
    Először ellenőrizze, hogy az ExpressRoute-kapcsolatcsoport Kiosztott és Engedélyezett állapotban van-e. Lásd az alábbi példát.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    A válasz az alábbi példához hasonló lesz:
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   
4. Konfigurálja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz.
   
    Mielőtt folytatná, ellenőrizze az alábbi információk meglétét.
   
   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha több előtagot kíván megadni, vesszővel tagolt listában adhatja meg őket. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.
   * Ügyfél ASN: Ha olyan előtagokat hirdet meg, amelyek nem a társviszony-létesítési AS-számra vannak regisztrálva, megadhatja az AS-számot, amelyre regisztrálva vannak. **Ez nem kötelező**.
   * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
   * Egy MD5-kivonat, ha használni kívánja. **Ez nem kötelező.**
     
     A következő parancsmag futtatásával konfigurálhatja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz.
     
       Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"
     
       Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### Microsoft társviszony-létesítés részleteinek lekérése
A konfiguráció részleteit az alábbi parancsmaggal kérheti le.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt


### Microsoft társviszony-létesítés konfigurációjának frissítése
A konfiguráció bármelyik részét frissítheti az alábbi parancsmag használatával.

        Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Microsoft társviszony-létesítés törlése
A társviszony-létesítés konfigurációját a következő parancsmag futtatásával törölheti.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## Következő lépések
A következő lépés egy [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md).

* Az ExpressRoute-munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-munkafolyamatok).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
* További információkért a virtuális hálózatok használatáról lásd: [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).

<!--HONumber=Oct16_HO3-->


