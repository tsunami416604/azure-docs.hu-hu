---
title: 'Azure ExpressRoute: Társviszony-létesítés konfigurálása: PowerShell'
description: A cikk az ExpressRoute-kapcsolatcsoportok privát, nyilvános és Microsoft társviszony-létesítéses létrehozásának és kiépítésének lépéseit ismerteti. A cikk azt is bemutatja, hogyan ellenőrizheti a kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti őket.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 2c28df35eec862afb5b0078ca7693898e9b58533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264842"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Peering létrehozása és módosítása Egy ExpressRoute-kapcsolati kapcsolata a PowerShell használatával

Ez a cikk segít létrehozni és kezelni egy ExpressRoute-kapcsolatútválasztás az Erőforrás-kezelő telepítési modell ben powershell használatával. Egy ExpressRoute-kapcsolatkapcsolat állapotát, frissítését vagy törlését és megszüntetését is ellenőrizheti. Ha más módszert szeretne használni a kapcsolatcsoport használatához, válasszon ki egy cikket az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure-portál](expressroute-howto-routing-portal-resource-manager.md)
> * [Powershell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Nyilvános társviszony-létesítés](about-public-peering.md)
> * [Videó - Privát társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Videó – Microsoft-társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 


Az utasítások csak 2. rétegbeli kapcsolatszolgáltatásokat kínáló szolgáltatóknál létrehozott kapcsolatcsoportokra vonatkoznak. Ha olyan szolgáltatót használ, amely felügyelt Layer 3 szolgáltatásokat (általában IPVPN-t, például MPLS-t) kínál, a kapcsolatszolgáltató konfigurálja és kezeli az útválasztást.

> [!IMPORTANT]
> A szolgáltatásfelügyeleti portálon jelenleg nem hirdetünk szolgáltatók által konfigurált társviszony-létesítéseket. Dolgozunk azon, hogy hamarosan bevezethessük ezt a képességet. A BGP-társviszony-létesítések konfigurálása előtt érdeklődjön a szolgáltatónál.
> 
> 

Konfigurálhatja a privát társviszony-létesítésés és a Microsoft-társviszony-létesítés egy ExpressRoute-kapcsolati kapcsolat (Az Azure nyilvános társviszony-létesítés elavult az új áramkörök). A társviszony-létesítés tetszőleges sorrendben konfigurálható. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. A tartományok és társviszony-létesítések útválasztási tartományairól további információt az [ExpressRoute útválasztási tartományok című témakörben talál.](expressroute-circuit-peerings.md) A nyilvános társviszony-létesítésről az [ExpressRoute nyilvános társviszony-létesítéscímű témakörben](about-public-peering.md)talál.

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A konfigurálás megkezdése előtt mindenképp tekintse át az [előfeltételek](expressroute-prerequisites.md), az [útválasztási követelmények](expressroute-routing.md) és a [munkafolyamatok](expressroute-workflows.md) lapot.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoportnak kiépített és engedélyezett állapotban kell lennie ahhoz, hogy a cikkben szereplő parancsmagokat futtathatja.

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell együttműködése

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft társviszony-létesítés

Ez a szakasz segítséget nyújt az ExpressRoute-kapcsolati kapcsolatbeállításainak létrehozásában, behírlése, frissítésében és törlésében.

> [!IMPORTANT]
> 2017. augusztus 1-je előtt konfigurált ExpressRoute-áramkörök Microsoft-társviszony-létesítése a Microsoft társviszony-létesítésével a Microsoft társviszony-létesítésén keresztül lesz meghirdetve, még akkor is, ha nincsenek megadva útvonalszűrők. 2017. augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társviszony-létesítése nem rendelkezik előtagokkal, amíg egy útvonalszűrő nem csatlakozik az áramkörhöz. További információt a [Microsoft-társviszony-létesítés útvonalszűrőjének konfigurálása](how-to-routefilter-powershell.md)című témakörben talál.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása

1. Jelentkezzen be, és válassza ki az előfizetést.

   Ha helyileg telepítette a PowerShellt, jelentkezzen be. Ha az Azure Cloud Shellt használja, kihagyhatja ezt a lépést.

   ```azurepowershell
   Connect-AzAccount
   ```

   Válassza ki az ExpressRoute-kapcsolat létrehozásához kívánt előfizetést.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot.

   Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-arm.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. ha a kapcsolódási szolgáltató felügyelt Layer 3 szolgáltatásokat kínál, megkérheti a kapcsolódási szolgáltatót, hogy engedélyezze a Microsoft-társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató nem kezeli az útválasztást, a kapcsolatcsoport létrehozása után folytassa a konfigurációt a következő lépésekkel. 

3. Ellenőrizze az ExpressRoute-kapcsolatcsoportban, hogy ki van-e építve és engedélyezve van-e. Használja a következő példát:

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

   * Az elsődleges kapcsolat /30 vagy /126 alhálózata. Ennek az Ön tulajdonában lévő és RIR/IRR-ben regisztrált érvényes nyilvános IPv4- vagy IPv6-előtagnak kell lennie.
   * A /30 vagy /126 alhálózat a másodlagos kapcsolathoz. Ennek az Ön tulajdonában lévő és RIR/IRR-ben regisztrált érvényes nyilvános IPv4- vagy IPv6-előtagnak kell lennie.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha előtagok készletét tervezi elküldeni, vesszővel tagolt listát küldhet. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt. Az IPv4 BGP-munkamenetekhez IPv4-hirdetett előtagok, az IPv6 BGP-munkamenetekhez pedig IPv6-alapú meghirdetett előtagokra van szükség. 
   * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
   * Nem kötelező:
     * Ügyfél ASN: Ha olyan előtagokat hirdet meg, amelyek nem a társviszony-létesítési AS-számra vannak regisztrálva, megadhatja az AS-számot, amelyre regisztrálva vannak.
     * Egy MD5-kivonat, ha használni kívánja.

> [!IMPORTANT]
> A Microsoft ellenőrzi, hogy a megadott "Hirdetett nyilvános előtagok" és a "Társ ASN" (vagy "Customer ASN") hozzá vannak-e rendelve az Internetes útválasztási beállításjegyzékben. Ha a nyilvános előtagokat egy másik entitástól kapja, és a hozzárendelés nincs rögzítve az útválasztási beállításjegyzékben, az automatikus érvényesítés nem fejeződik be, és manuális érvényesítést igényel. Ha az automatikus érvényesítés sikertelen, a "Get-AzExpressRouteCircuitPeerPeeringConfig" (lásd a "Microsoft társviszony-létesítési részletek beolvassa" parancsot a "HirdetettPublicPrefixesState" a "Érvényesítés szükséges" parancsként fogja látni. 
> 
> Ha az "Érvényesítés szükséges" üzenet jelenik meg, gyűjtse össze azokat a dokumentum(okait), amelyek(ek)et az útválasztási jegyzékben az előtagok tulajdonosaként felsorolt entitás a szervezethez rendeli,(/a) gyűjtse össze azokat a dokumentum(okat), amelyek (2) bekezdéssel megegyezésre vannak. támogatási jegy megnyitását az alábbiak szerint. 
> 
>

   Az alábbi példában konfigurálhatja a Microsoft társviszony-létesítését a kapcsolatcsoporthoz:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>Microsoft társviszony-létesítés részleteinek lekérése

A konfiguráció részleteit a következő példával kaphatja meg:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Microsoft társviszony-létesítés konfigurációjának frissítése

A konfiguráció bármely részét a következő példával frissítheti:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft társviszony-létesítés törlése

A társviszony-létesítési konfigurációt a következő parancsmag futtatásával távolíthatja el:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-private-peering"></a><a name="private"></a>Azure privát társviszony-létesítés

Ez a szakasz segít létrehozni, beszerezni, frissíteni és törölni az Azure privát társviszony-létesítési konfiguráció egy ExpressRoute-kapcsolati kapcsolat.

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. Importálja az ExpressRoute PowerShell-modulját.

   Telepítse a legújabb PowerShell-telepítőt a [PowerShell-galériából](https://www.powershellgallery.com/), és importálja az Azure Resource Manager-modulokat a PowerShell-munkamenetbe az ExpressRoute-parancsmagok használatának elkezdéséhez. A PowerShellt rendszergazdaként kell futtatnia.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importálja az összes\* Az. modult az ismert szemantikai verziótartományon belül.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Az ismert szemantikai verziótartományon belül is importálhat egy kiválasztott modult.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Jelentkezzen be a fiókjába.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Válassza ki az ExpressRoute-kapcsolat létrehozásához kívánt előfizetést.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot.

   Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-arm.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha a kapcsolódási szolgáltató felügyelt Layer 3 szolgáltatásokat kínál, kérheti meg a kapcsolatszolgáltatótól, hogy engedélyezze az Azure privát társviszony-létesítését. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató nem kezeli az útválasztást, a kapcsolatcsoport létrehozása után folytassa a konfigurációt a következő lépésekkel.

3. Ellenőrizze az ExpressRoute-kapcsolatcsoportban, hogy ki van-e építve és engedélyezve van-e. Használja a következő példát:

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

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Az alhálózat nem lehet része a virtuális hálózatok számára fenntartott címtérnek.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Az alhálózat nem lehet része a virtuális hálózatok számára fenntartott címtérnek.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társviszony-létesítéshez használhat privát AS-számokat is. Ne használja a 65515 számot.
   * Nem kötelező:
     * Egy MD5-kivonat, ha használni kívánja.

   Az alábbi példában konfigurálhatja az Azure privát társviszony-létesítését a kapcsolatcsoporthoz:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Ha MD5-kivonatot szeretne használni, használja a következő példát:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>Az Azure privát társviszony-létesítési részleteinek beszerezni

A konfiguráció részleteit a következő példával kaphatja meg:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Azure privát társviszony-létesítés konfigurációjának frissítése

A következő példával a konfiguráció bármely részét frissítheti. Ebben a példában az áramkör VLAN-azonosítója 100-ról 500-ra frissül.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Azure privát társviszony-létesítés törlése

A társviszony-létesítési konfigurációt a következő példa futtatásával távolíthatja el:

> [!WARNING]
> A példa futtatása előtt gondoskodnia kell arról, hogy az összes virtuális hálózat és az ExpressRoute globális elérési kapcsolat törlődjön. 
> 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


## <a name="next-steps"></a>További lépések

A következő lépés egy [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md).

* Az ExpressRoute-munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-munkafolyamatok).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
* További információkért a virtuális hálózatok használatáról lásd: [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).
