---
title: 'Adja meg az ExpressRoute - kapcsolatcsoport társviszony-létesítést: PowerShell: Azure | Microsoft Docs'
description: A cikk az ExpressRoute-kapcsolatcsoportok privát, nyilvános és Microsoft társviszony-létesítéses létrehozásának és kiépítésének lépéseit ismerteti. A cikk azt is bemutatja, hogyan ellenőrizheti a kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti őket.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: bb1e71c4a665a25fa9b936c8777cf8fa0d5cd3e6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091919"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>A PowerShell használatával egy ExpressRoute-kapcsolatcsoport társviszony létesítése és módosítása

Ez a cikk segítséget nyújt a létrehozása és kezelése a PowerShell-lel Resource Manager-alapú üzemi modellben az ExpressRoute-Kapcsolatcsoportok útválasztási konfigurációjának. Ellenőrizze az állapotot, frissítési vagy törlési is, és ExpressRoute-kapcsolatcsoport társviszonyainak megszüntetése. Ha szeretne egy másik módszer használható a kapcsolatcsoport-, válassza ki a cikk az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Videó - privát társviszony](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Videó - nyilvános társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Videó – Microsoft társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 


Az utasítások csak 2. rétegbeli kapcsolatszolgáltatásokat kínáló szolgáltatóknál létrehozott kapcsolatcsoportokra vonatkoznak. Ha használja a szolgáltató által kínált felügyelt Layer 3 szolgáltatások (általában egy IPVPN, mint az MPLS), a kapcsolatszolgáltató konfigurálása és kezelése az útválasztást Ön helyett.

> [!IMPORTANT]
> A szolgáltatásfelügyeleti portálon jelenleg nem hirdetünk szolgáltatók által konfigurált társviszony-létesítéseket. Dolgozunk azon, hogy hamarosan bevezethessük ezt a képességet. Ellenőrizze a szolgáltatónál, mielőtt konfigurálná a BGP társviszony-létesítéseket.
> 
> 

Egy, két vagy akár mindhárom társviszony-létesítést (Azure privát, Azure nyilvános és Microsoft) is konfigurálhatja egy adott ExpressRoute-kapcsolatcsoportban. A társviszony-létesítéseket tetszőleges sorrendben konfigurálhatja. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. Útválasztási tartományok és a társviszony-létesítéseket kapcsolatos további információkért lásd: [ExpressRoute útválasztási tartományok](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A konfigurálás megkezdése előtt mindenképp tekintse át az [előfeltételek](expressroute-prerequisites.md), az [útválasztási követelmények](expressroute-routing.md) és a [munkafolyamatok](expressroute-workflows.md) lapot.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoport kiosztott és engedélyezett állapotban, hogy tud-parancsmagok futtatásához az ebben a cikkben kell lennie.

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="msft"></a>Microsoft társviszony-létesítés

Ez a szakasz segítséget nyújt a létrehozása, beolvasása, frissíteni és törölni az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítési konfigurációjának.

> [!IMPORTANT]
> Microsoft társviszony-létesítés voltak beállítva a 2017. augusztus 1. ExpressRoute-Kapcsolatcsoportok az összes szolgáltatás előtagkészletet hirdeti meg a Microsoft társviszony-létesítéshez, akkor is, ha nincsenek meghatározva útvonalszűrők fog rendelkezni. Microsoft társviszony-létesítésre vannak konfigurálva, vagy 2017. augusztus 1. után az ExpressRoute-Kapcsolatcsoportok, nem rendelkezik minden olyan előtagok mindaddig, amíg egy útvonalszűrőhöz csatolva van a kapcsolatcsoport hirdetve. További információkért lásd: [konfigurálása a Microsoft társviszony-létesítés egy útvonalszűrőhöz](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása

1. Jelentkezzen be, és válassza ki az előfizetését.

   Ha a PowerShell helyi telepítése, jelentkezzen be. Ha az Azure Cloud Shellt használja, kihagyhatja ezt a lépést.

   ```azurepowershell
   Connect-AzAccount
   ```

   ExpressRoute-kapcsolatcsoportot létrehozni kívánt előfizetés kiválasztásához.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot.

   Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-arm.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. f a kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti, hogy a kapcsolatszolgáltató engedélyezése a Microsoft társviszony-létesítést az Ön számára. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Azonban ha a kapcsolatszolgáltató felügyeli az útválasztást, a kapcsolatcsoport létrehozását követően továbbra is a konfiguráció a következő lépéseket. 

3. Ellenőrizze a ExpressRoute-kapcsolatcsoport győződjön meg arról, hogy kiosztott és engedélyezett. Használja a következő példát:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   A válasz a következő példához hasonló:

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

   * / 30-as vagy/126 alhálózatot az elsődleges kapcsolathoz. Ez lehet egy érvényes nyilvános IPv4- vagy IPv6-előtagot az Ön tulajdonában és regisztrálva van egy RIR / IRR jegyzék nevét.
   * / 30-as vagy/126 alhálózat a másodlagos kapcsolathoz. Ez lehet egy érvényes nyilvános IPv4- vagy IPv6-előtagot az Ön tulajdonában és regisztrálva van egy RIR / IRR jegyzék nevét.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha azt tervezi, az előtagok megadni, küldhet egy vesszővel tagolt lista. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt. IPv4-BGP-munkamenetek szükséges IPv4 meghirdetett előtagokat és az IPv6-alapú BGP-munkamenetek igényel IPv6 meghirdetett előtagok. 
   * Útválasztási jegyzék neve: Megadhatja az RIR / IRR jegyzék nevét, amely az AS-szám és az előtagok regisztrálva vannak.
   * Nem kötelező:
     * Customer ASN: Ha olyan előtagokat hirdet meg, amelyek nem a társviszony-létesítési AS-szám, megadhatja az AS-számot, amelyre regisztrálva vannak.
     * Egy MD5-kivonat, ha használni kívánja.

   Az alábbi példa használatával konfigurálhatja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="getmsft"></a>A Microsoft társviszony-létesítés részleteinek lekérése

Konfiguráció részleteit az alábbi példa használatával kérheti le:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="updatemsft"></a>A Microsoft társviszony-létesítés konfigurációjának frissítése

Az alábbi példa a konfiguráció bármelyik részét frissítheti:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletemsft"></a>A Microsoft társviszony-létesítés törlése

A társviszony-létesítés konfigurációját a következő parancsmag futtatásával távolíthatja el:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="private"></a>Az Azure privát társviszony-létesítés

Ez a szakasz segítséget nyújt a létrehozása, beolvasása, frissíteni és törölni az ExpressRoute-kapcsolatcsoport Azure privát társviszony-létesítési konfigurációjának.

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. Importálja az ExpressRoute PowerShell-modulját.

   Telepítse a legújabb PowerShell-telepítőt a [PowerShell-galériából](https://www.powershellgallery.com/), és importálja az Azure Resource Manager-modulokat a PowerShell-munkamenetbe az ExpressRoute-parancsmagok használatának elkezdéséhez. A PowerShellt rendszergazdaként kell futtatnia.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importálja a Az.\* modulok az ismert szemantikai verziótartományon belül.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Importálhatja az egyik választott modult az ismert szemantikai verziótartományon belül is.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Jelentkezzen be a fiókjába.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   ExpressRoute-kapcsolatcsoportot létrehozni kívánt előfizetés kiválasztásához.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot.

   Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-arm.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti engedélyezése az Azure privát társviszony-létesítést, a kapcsolat szolgáltatóját. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Azonban ha a kapcsolatszolgáltató felügyeli az útválasztást, a kapcsolatcsoport létrehozását követően továbbra is a konfiguráció a következő lépéseket.

3. Ellenőrizze a ExpressRoute-kapcsolatcsoport győződjön meg arról, hogy kiosztott és engedélyezett. Használja a következő példát:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   A válasz a következő példához hasonló:

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

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Az alhálózat nem kell minden olyan virtuális hálózatok számára lefoglalt címtér részét.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Az alhálózat nem kell minden olyan virtuális hálózatok számára lefoglalt címtér részét.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társviszony-létesítéshez használhat privát AS-számokat is. Ne használja a 65515 számot.
   * Nem kötelező:
     * Egy MD5-kivonat, ha használni kívánja.

   Konfigurálhatja az Azure privát társviszony-létesítést a kapcsolatcsoporthoz. használja a következő példát:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Ha MD5-kivonatot használja, használja a következő példát:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.
   > 
   >

### <a name="getprivate"></a>Az Azure privát társviszony-létesítés részleteinek lekérése

Konfiguráció részleteit az alábbi példa használatával kérheti le:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="updateprivate"></a>Azure privát társviszony-létesítés konfigurációjának frissítése

Az alábbi példa a konfiguráció bármelyik részét frissítheti. Ebben a példában a kör VLAN-azonosító frissítése folyamatban van a 100 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deleteprivate"></a>Az Azure privát társviszony-létesítés törlése

A társviszony-létesítés konfigurációját a következő példa futtatásával távolíthatja el:

> [!WARNING]
> Biztosítania kell, hogy a virtuális hálózatok és a globális elérhetőségű ExpressRoute-kapcsolatok eltávolítása a példa futtatása előtt. 
> 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="public"></a>Az Azure nyilvános társviszony-létesítés

Ez a szakasz segítséget nyújt a létrehozása, beolvasása, frissíteni és törölni az ExpressRoute-kapcsolatcsoport Azure nyilvános társviszony-létesítési konfigurációjának.

### <a name="to-create-azure-public-peering"></a>Azure nyilvános társviszony-létesítés létrehozása

1. Importálja az ExpressRoute PowerShell-modulját.

   Telepítse a legújabb PowerShell-telepítőt a [PowerShell-galériából](https://www.powershellgallery.com/), és importálja az Azure Resource Manager-modulokat a PowerShell-munkamenetbe az ExpressRoute-parancsmagok használatának elkezdéséhez. A PowerShellt rendszergazdaként kell futtatnia.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importálja a Az.\* modulok az ismert szemantikai verziótartományon belül.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Importálhatja az egyik választott modult az ismert szemantikai verziótartományon belül is.

   ```azurepowershell-interactive
   Import-Module Az.Network
   ```

   Jelentkezzen be a fiókjába.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   ExpressRoute-kapcsolatcsoportot létrehozni kívánt előfizetés kiválasztásához.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot.

   Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-arm.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti engedélyezése az Azure nyilvános társviszony-létesítést, a kapcsolat szolgáltatóját. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Azonban ha a kapcsolatszolgáltató felügyeli az útválasztást, a kapcsolatcsoport létrehozását követően továbbra is a konfiguráció a következő lépéseket.

3. Ellenőrizze a ExpressRoute-kapcsolatcsoport győződjön meg arról, hogy kiosztott és engedélyezett. Használja a következő példát:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   A válasz a következő példához hasonló:

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
4. Konfigurálja az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz. Győződjön meg arról, hogy rendelkezik a következő adatokat, mielőtt folytatná.

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Nem kötelező:
     * Egy MD5-kivonat, ha használni kívánja.

   Futtassa az alábbi példa konfigurálása az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Ha MD5-kivonatot használja, használja a következő példát:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.
   > 
   >

### <a name="getpublic"></a>Az Azure nyilvános társviszony-létesítés részleteinek lekérése

Konfiguráció részleteit az alábbi parancsmaggal kérheti le:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>Az Azure nyilvános társviszony-létesítés konfigurációjának frissítése

Az alábbi példa a konfiguráció bármelyik részét frissítheti. Ebben a példában a kör VLAN-azonosító frissítése folyamatban van 200-ról 600-ra.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Az Azure nyilvános társviszony-létesítés törlése

A társviszony-létesítés konfigurációját a következő példa futtatásával távolíthatja el:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>További lépések

A következő lépés egy [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md).

* Az ExpressRoute-munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-munkafolyamatok).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
* További információkért a virtuális hálózatok használatáról lásd: [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).
