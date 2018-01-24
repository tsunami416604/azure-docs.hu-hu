---
title: "Útválasztási (társviszony-létesítés) a egy ExpressRoute-áramkör konfigurálása: erőforrás-kezelő: PowerShell: Azure |} Microsoft Docs"
description: "A cikk az ExpressRoute-kapcsolatcsoportok privát, nyilvános és Microsoft társviszony-létesítéses létrehozásának és kiépítésének lépéseit ismerteti. A cikk azt is bemutatja, hogyan ellenőrizheti a kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti őket."
documentationcenter: na
services: expressroute
author: osamazia
manager: jonor
editor: 
tags: azure-resource-manager
ms.assetid: 0a036d51-77ae-4fee-9ddb-35f040fbdcdf
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/3/2018
ms.author: osamaz, jaredr80
ms.openlocfilehash: 26165748c88629bacdaed6f12a540302ea12513b
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Létrehozása és módosítása a powershellel ExpressRoute-kör társviszony

Ez a cikk segítséget nyújt a létrehozása és kezelése a PowerShell használatával Resource Manager üzembe helyezési modellel ExpressRoute-kapcsolatcsoportot útválasztási konfigurációja. Ellenőrizze az állapot, frissítési vagy törlési is, és az ExpressRoute-kör társviszony kiosztásának megszüntetése. Ha más módszert használja a kapcsolatcsoport dolgozni szeretne, válassza ki egy cikk az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - privát társviszony-létesítés](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - nyilvános társviszony-létesítés](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Videó – a Microsoft társviszony-létesítés](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* Szüksége lesz az Azure Resource Manager PowerShell-parancsmagok legújabb verzióját. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. 
* A konfigurálás megkezdése előtt mindenképp tekintse át az [előfeltételek](expressroute-prerequisites.md), az [útválasztási követelmények](expressroute-routing.md) és a [munkafolyamatok](expressroute-workflows.md) lapot.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoport ahhoz, hogy ebben a cikkben a parancsmagok futtatásához kiépített és engedélyezett állapotban kell lennie.

Az utasítások csak 2. rétegbeli kapcsolatszolgáltatásokat kínáló szolgáltatóknál létrehozott kapcsolatcsoportokra vonatkoznak. A szolgáltató által kezelt használata réteg (általában egy IPVPN, például az MPLS) 3 szolgáltatások, a kapcsolat szolgáltatójánál konfigurálása és kezelése az Ön útválasztást.

> [!IMPORTANT]
> A szolgáltatásfelügyeleti portálon jelenleg nem hirdetünk szolgáltatók által konfigurált társviszony-létesítéseket. Dolgozunk azon, hogy hamarosan bevezethessük ezt a képességet. A BGP társviszony konfigurálása előtt ellenőrizze a szolgáltató.
> 
> 

Egy, két vagy akár mindhárom társviszony-létesítést (Azure privát, Azure nyilvános és Microsoft) is konfigurálhatja egy adott ExpressRoute-kapcsolatcsoportban. A társviszony-létesítéseket tetszőleges sorrendben konfigurálhatja. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. További információ az útválasztási tartományok és esetében: [ExpressRoute útválasztási tartományok](expressroute-circuit-peerings.md).

## <a name="msft"></a>A Microsoft társviszony-létesítés

Ez a szakasz segítséget nyújt a létrehozása, beolvasása, frissítése és a Microsoft társviszony-létesítési konfiguráció az ExpressRoute-kapcsolatcsoportot törli.

> [!IMPORTANT]
> A Microsoft társviszony-létesítést az ExpressRoute-Kapcsolatcsoportok 2017. augusztus 1. előtt konfigurált meghirdetett keresztül a Microsoft társviszony-létesítést, még akkor is, ha az útvonal-szűrők nem definiált összes szolgáltatás előtagok fog rendelkezni. A Microsoft társviszony-létesítést az ExpressRoute-Kapcsolatcsoportok vannak konfigurálva, vagy azt követően 2017. augusztus 1. nem rendelkezik a előtagokat amíg útvonal szűrő nem csatlakoztatja a kapcsolatcsoport hirdetve. További információkért lásd: [konfigurálása a Microsoft társviszony-létesítéshez útvonal szűrő](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása

[!INCLUDE [Premium](../../includes/expressroute-mspeering-premium-include.md)]

1. Importálja az ExpressRoute PowerShell-modulját.

  Telepítse a legújabb PowerShell-telepítőt a [PowerShell-galériából](http://www.powershellgallery.com/), és importálja az Azure Resource Manager-modulokat a PowerShell-munkamenetbe az ExpressRoute-parancsmagok használatának elkezdéséhez. A PowerShellt rendszergazdaként kell futtatnia.

  ```powershell
  Install-Module AzureRM

  Install-AzureRM
  ```

  Importálja a AzureRM.* modulok ismert szemantikai verziója tartományon belül.

  ```powershell
  Import-AzureRM
  ```

  Az ismert szemantikai verziója tartományon belüli select modul csak is importálhatja.

  ```powershell
  Import-Module AzureRM.Network
  ```

  Jelentkezzen be a fiókjába.

  ```powershell
  Login-AzureRmAccount
  ```

  Válassza ki az előfizetést, ExpressRoute-kapcsolatcsoportot létrehozni kívánja.

  ```powershell
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot.

  Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-arm.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. f a kapcsolat szolgáltatójánál felügyelt réteg 3-szolgáltatásokat biztosít, kérje meg a kapcsolat szolgáltatójánál engedélyezése a Microsoft társviszony-létesítés meg. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Azonban ha a kapcsolat szolgáltatójánál nem kezeli az útválasztást, a kapcsolat létrehozása után továbbra is a konfiguráció a következő lépéseket. 

3. Ellenőrizze, hogy üzembe helyezve, és is engedélyezve van az ExpressRoute-kapcsolatcsoportot. Használja a következő példát:

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  A rendszer a választ az alábbi példához hasonló:

  ```
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
  ```
4. Konfigurálja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz. Mielőtt folytatná, ellenőrizze az alábbi információk meglétét.

  * / 30-as vagy /126 alhálózat az elsődleges kapcsolathoz. Ez lehet egy érvényes nyilvános IPv4- vagy IPv6-előtagot a saját tulajdonában és egy RIR-ben regisztrált vagy IRR-ben.
  * / 30-as vagy /126 alhálózat a másodlagos kapcsolathoz. Ez lehet egy érvényes nyilvános IPv4- vagy IPv6-előtagot a saját tulajdonában és egy RIR-ben regisztrált vagy IRR-ben.
  * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
  * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
  * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha szeretne elküldhető a előtagokat, elküldheti egy vesszővel elválasztott listában. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt. IPv4-alapú BGP-munkamenetek szükséges IPv4 meghirdetett előtagok és az IPv6 BGP-munkamenetek IPv6 előtagok meghirdetett szükséges. 
  * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
  * Nem kötelező:
    * Ügyfél ASN: Ha olyan előtagokat hirdet meg, amelyek nem a társviszony-létesítési AS-számra vannak regisztrálva, megadhatja az AS-számot, amelyre regisztrálva vannak.
    * Egy MD5-kivonat, ha használni kívánja.

  Az alábbi példa használatával konfigurálja a kör társviszony Microsoft:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

### <a name="getmsft"></a>A Microsoft társviszony-létesítési részletes

Az alábbi példa konfigurációs részletek szerezheti be:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="updatemsft"></a>A Microsoft társviszony-létesítési konfiguráció frissítése

Frissítheti, az alábbi példa konfiguráció bármely részeként:

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletemsft"></a>A Microsoft társviszony törlése

A társviszony-létesítési konfiguráció a következő parancsmag futtatásával távolíthatja el:

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="private"></a>Az Azure magánhálózati társviszony-létesítés

Ez a szakasz segítséget nyújt a létrehozása, beolvasása, frissítése és törölni az Azure magánhálózati társviszony-létesítési ExpressRoute-kapcsolatcsoportot.

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. Importálja az ExpressRoute PowerShell-modulját.

  Telepítse a legújabb PowerShell-telepítőt a [PowerShell-galériából](http://www.powershellgallery.com/), és importálja az Azure Resource Manager-modulokat a PowerShell-munkamenetbe az ExpressRoute-parancsmagok használatának elkezdéséhez. A PowerShellt rendszergazdaként kell futtatnia.

  ```powershell
  Install-Module AzureRM
  Install-AzureRM
  ```

  Importálja a AzureRM.* modulok ismert szemantikai verziója tartományon belül.

  ```powershell
  Import-AzureRM
  ```

  Az ismert szemantikai verziója tartományon belüli select modul csak is importálhatja.

  ```powershell
  Import-Module AzureRM.Network 
  ```

  Jelentkezzen be a fiókjába.

  ```powershell
  Login-AzureRmAccount
  ```

  Válassza ki az előfizetést, ExpressRoute-kapcsolatcsoportot létrehozni kívánja.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot.

  Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-arm.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha a kapcsolat szolgáltatójánál felügyelt 3. rétegbeli szolgáltatásokat nyújt, kérje meg a kapcsolat szolgáltatójánál engedélyezése az Azure magánhálózati társviszony-létesítés meg. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Azonban ha a kapcsolat szolgáltatójánál nem kezeli az útválasztást, a kapcsolat létrehozása után továbbra is a konfiguráció a következő lépéseket.

3. Ellenőrizze, hogy üzembe helyezve, és is engedélyezve van az ExpressRoute-kapcsolatcsoportot. Használja a következő példát:

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  A rendszer a választ az alábbi példához hasonló:

  ```
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
  ```
4. Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoport számára. Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:

  * Egy /30 alhálózat az elsődleges kapcsolat számára. Az alhálózat nem virtuális hálózatok számára fenntartott címtartomány részének kell lennie.
  * Egy /30 alhálózat a másodlagos kapcsolat számára. Az alhálózat nem virtuális hálózatok számára fenntartott címtartomány részének kell lennie.
  * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
  * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társviszony-létesítéshez használhat privát AS-számokat is. Ne használja a 65515 számot.
  * Nem kötelező:
    * Egy MD5-kivonat, ha használni kívánja.

  Az alábbi példát követve Azure magánhálózati társviszony-létesítés a kapcsolat konfigurálása:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Ha az MD5 kivonatoló használatát választja, használja a következő példát:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
  ```

  > [!IMPORTANT]
  > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.
  > 
  >

### <a name="getprivate"></a>Az Azure magánhálózati társviszony-létesítési részletes

Konfigurációs részletek kaphat az alábbi példa:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="updateprivate"></a>Az Azure magánhálózati társviszony-létesítési konfiguráció frissítése

Frissítheti, az alábbi példa konfiguráció bármely részeként. Ebben a példában a VLAN-Azonosítót a áramkör frissül 100 500.

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deleteprivate"></a>Az Azure magánhálózati társviszony-létesítés törlése

Futtassa a következő példa a társviszony-létesítési konfiguráció eltávolítása:

> [!WARNING]
> Bizonyosodjon meg, hogy az összes virtuális hálózatot megszüntetni az ExpressRoute-kapcsolatcsoport az ebben a példában futtatása előtt. 
> 
> 

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="public"></a>Az Azure nyilvános társviszony-létesítés

Ez a szakasz segítséget nyújt a létrehozása, beolvasása, frissítése és törlése az Azure nyilvános társviszony-létesítési konfiguráció ExpressRoute-kör.

### <a name="to-create-azure-public-peering"></a>Azure nyilvános társviszony-létesítés létrehozása

1. Importálja az ExpressRoute PowerShell-modulját.

  Telepítse a legújabb PowerShell-telepítőt a [PowerShell-galériából](http://www.powershellgallery.com/), és importálja az Azure Resource Manager-modulokat a PowerShell-munkamenetbe az ExpressRoute-parancsmagok használatának elkezdéséhez. A PowerShellt rendszergazdaként kell futtatnia.

  ```powershell
  Install-Module AzureRM

  Install-AzureRM
```

  Importálja a AzureRM.* modulok ismert szemantikai verziója tartományon belül.

  ```powershell
  Import-AzureRM
  ```

  Az ismert szemantikai verziója tartományon belüli select modul csak is importálhatja.

  ```powershell
  Import-Module AzureRM.Network
```

  Jelentkezzen be a fiókjába.

  ```powershell
  Login-AzureRmAccount
  ```

  Válassza ki az előfizetést, ExpressRoute-kapcsolatcsoportot létrehozni kívánja.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot.

  Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-arm.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha a kapcsolat szolgáltatójánál felügyelt 3. rétegbeli szolgáltatásokat nyújt, kérje meg a kapcsolat szolgáltatójánál engedélyezése az Azure nyilvános társviszony-létesítés meg. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Azonban ha a kapcsolat szolgáltatójánál nem kezeli az útválasztást, a kapcsolat létrehozása után továbbra is a konfiguráció a következő lépéseket.

3. Ellenőrizze a ExpressRoute-kapcsolatcsoportot annak érdekében van üzembe helyezve, és is engedélyezve van. Használja a következő példát:

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  A rendszer a választ az alábbi példához hasonló:

  ```
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
  ```
4. Konfigurálja az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz. Győződjön meg arról, hogy rendelkezik a következő adatokat, mielőtt végrehajtásának folytatásához.

  * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
  * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
  * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
  * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
  * Nem kötelező:
    * Egy MD5-kivonat, ha használni kívánja.

  Futtassa az alábbi példa az Azure nyilvános társviszony-létesítés a kapcsolat konfigurálása

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Ha az MD5 kivonatoló használatát választja, használja a következő példát:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  > [!IMPORTANT]
  > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.
  > 
  >

### <a name="getpublic"></a>Az Azure nyilvános társviszony-létesítési részletes

Konfigurációs adatait az alábbi parancsmag segítségével szerezheti be:

```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>Az Azure nyilvános társviszony-létesítési konfiguráció frissítése

Frissítheti, az alábbi példa konfiguráció bármely részeként. Ebben a példában a VLAN-Azonosítót a kapcsolatcsoport, frissül a 200-as 600 értékre.

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Az Azure nyilvános társviszony törlése

Futtassa a következő példa a társviszony-létesítési konfiguráció eltávolítása:

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>További lépések

A következő lépés egy [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md).

* Az ExpressRoute-munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-munkafolyamatok).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
* További információkért a virtuális hálózatok használatáról lásd: [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).
