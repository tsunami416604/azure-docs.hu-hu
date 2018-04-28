---
title: 'Létrehozásához és módosításához ExpressRoute-kapcsolatcsoportot: PowerShell: Azure Resource Manager |} Microsoft Docs'
description: Ez a cikk ismerteti, hogyan létrehozásához, telepítéséhez, győződjön meg arról, frissítése, törlése és kiosztásának megszüntetése ExpressRoute-kapcsolatcsoportot.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: f997182e-9b25-4a7a-b079-b004221dadcc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 0d45e97cc42826375a99df16a73c9a7b0c359224
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Létrehozása és módosítása a powershellel ExpressRoute-kapcsolatcsoportot
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video - Azure-portálon](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ez a cikk ismerteti, hogyan hozhat létre Azure ExpressRoute-kapcsolatcsoportot PowerShell-parancsmagok és az Azure Resource Manager telepítési modell használatával. Ez a cikk is bemutatja, hogyan ellenőrizze a kapcsolatcsoport állapotát, a frissítést, vagy törlés és kiosztásának megszüntetése azt.

## <a name="before-you-begin"></a>Előkészületek
* Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. További információkért lásd: [áttekintés az Azure PowerShell](/powershell/azure/overview).
* Tekintse át a [Előfeltételek](expressroute-prerequisites.md) és [munkafolyamatok](expressroute-workflows.md) konfigurálás elkezdése előtt.


## <a name="create"></a>Hozzon létre, és helyezze üzembe az ExpressRoute-kapcsolatcsoportot
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Jelentkezzen be az Azure-fiókjával, és jelölje ki az előfizetését
A konfiguráció első lépésként jelentkezzen be az Azure-fiókjával. Az alábbi példák segítségével csatlakozzon:

```powershell
Connect-AzureRmAccount
```

Ellenőrizze a fiókhoz tartozó előfizetések:

```powershell
Get-AzureRmSubscription
```

Válassza ki az előfizetést, az ExpressRoute-kapcsolatcsoportot létrehozni kívánt:

```powershell
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. A támogatott szolgáltatók, a helyek és a sávszélesség listájának lekérdezése
ExpressRoute-kapcsolatcsoportot létrehozni, meg kell támogatott kapcsolat szolgáltatókat, a helyek és a sávszélesség-beállítások listája.

A PowerShell-parancsmag **Get-AzureRmExpressRouteServiceProvider** adja vissza ezt az információt fogja használni a későbbi lépésekben:

```powershell
Get-AzureRmExpressRouteServiceProvider
```

Ellenőrizze, hogy ha a kapcsolat szolgáltatójánál nem szerepel-e. Jegyezze fel a következő adatokat, amelyek később kell a kapcsolat létrehozásakor:

* Name (Név)
* PeeringLocations
* BandwidthsOffered

Most már készen áll az ExpressRoute-kapcsolatcsoportot létrehozni.   

### <a name="3-create-an-expressroute-circuit"></a>3. ExpressRoute-kapcsolatcsoport létrehozása
Ha még nem rendelkezik egy erőforráscsoport, kell létrehoznia egyet az ExpressRoute-kapcsolatcsoportot létrehozása előtt. Ehhez futtassa a következő parancsot:

```powershell
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```


A következő példa bemutatja, hogyan szilícium Valley egy 200 MB/s Equinix keresztül ExpressRoute-kapcsolatcsoportot létrehozni. Különböző szolgáltatók és más beállítások használata, amikor a kérést helyettesítse be ezt az információt. Az alábbi példát követve egy új szolgáltatás kulcs kérése:

```powershell
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Győződjön meg arról, hogy megadja a helyes SKU réteg és SKU-család:

* Termékváltozat szint határozza meg, egy ExpressRoute standard vagy ExpressRoute prémium bővítmény engedélyezve van-e. Megadhat *szabványos* lekérni a standard Termékváltozat vagy *prémium* a prémium szintű bővítménnyel.
* SKU-család határozza meg a számlázási. Megadhat *Metereddata* díjköteles adatforgalom tervhez és *Unlimiteddata* egy adatforgalmi a. Módosíthatja a számlázási típusának *Metereddata* való *Unlimiteddata*, de nem módosíthatja a típusát a *Unlimiteddata* való *Metereddata*.

> [!IMPORTANT]
> Az ExpressRoute-kapcsolatcsoportot lesz számlázva abban a pillanatban a szolgáltatási kulcs ad ki. Győződjön meg arról, hogy a művelet végrehajtása, ha a kapcsolat szolgáltatójánál kiépíteni a kapcsolat készen áll-e.
> 
> 

A válasz tartalmazza a szolgáltatási kulcs. A paraméterek részletes leírását a következő parancs futtatásával szerezheti be:

```powershell
get-help New-AzureRmExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. A lista összes ExpressRoute-Kapcsolatcsoportok
Minden létrehozott ExpressRoute-Kapcsolatcsoportok listájának lekéréséhez futtassa a **Get-AzureRmExpressRouteCircuit** parancs:

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

A válasz a következő példa hasonlít:

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
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

Ezt az információt bármikor használatával kérheti le a `Get-AzureRmExpressRouteCircuit` parancsmag. A kapcsolatok a következő hívással paraméter nélküli sorolja fel. A szolgáltatás kulcs szerepel az *ServiceKey* mező:

```powershell
Get-AzureRmExpressRouteCircuit
```


A válasz a következő példa hasonlít:

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
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


A paraméterek részletes leírását a következő parancs futtatásával szerezheti be:

```powershell
get-help Get-AzureRmExpressRouteCircuit -detailed
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. A szolgáltatás kulcs küldése a kapcsolat szolgáltatójánál történő üzembe helyezéséhez
*ServiceProviderProvisioningState* információkat nyújt azokról a szolgáltatói oldalon kiépítés aktuális állapotával. Állapot állapotát biztosít a Microsoft oldalon. Kiépítés állapotok áramkör kapcsolatos további információkért lásd: [munkafolyamatok](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Amikor létrehoz egy új ExpressRoute-kapcsolatcsoportot, a kapcsolat a következő állapotban van:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



A kapcsolatcsoport módosítja a következő állapotot, ha a kapcsolat szolgáltatójánál folyamatban van, lehetővé téve az Ön:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Meg kell tudni használni az ExpressRoute-kapcsolatcsoportot a következő állapotban kell lennie:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Ellenőrizze rendszeresen a kapcsolatcsoport kulcs állapotát és az állapot
Az állapot és a kapcsolatcsoport kulcs állapotának ellenőrzése értesíti Önt arról, amikor a szolgáltató a kapcsolatcsoport engedélyezve van. A kapcsolat konfigurálása után *ServiceProviderProvisioningState* jelenik meg *kiépítve*, a következő példában látható módon:

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A válasz a következő példa hasonlít:

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

### <a name="7-create-your-routing-configuration"></a>7. Az útválasztó-konfiguráció létrehozása
Részletes útmutatásért lásd: a [ExpressRoute-áramkör útválasztási konfigurációja](expressroute-howto-routing-arm.md) cikk létrehozásához és módosításához a kapcsolatcsoport esetében.

> [!IMPORTANT]
> Ezek az utasítások csak a szolgáltatók által biztosított réteg 2 internetkapcsolati szolgáltatás használatával létrehozott kapcsolatok vonatkoznak. A szolgáltató által kezelt használata réteg (általában az IP VPN, például az MPLS) 3 szolgáltatások, a kapcsolat szolgáltatójánál konfigurálja és kezeli az Ön útválasztást.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal
A következő csatolja az ExpressRoute-kapcsolatcsoportot egy virtuális hálózatot. Használja a [virtuális hálózatok összekapcsolása ExpressRoute-Kapcsolatcsoportok](expressroute-howto-linkvnet-arm.md) a Resource Manager üzembe helyezési modellel végzett munka során a következő cikket.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Egy ExpressRoute-kapcsolatcsoport állapotának beolvasása
Ezt az információt bármikor használatával kérheti le a **Get-AzureRmExpressRouteCircuit** parancsmag. A kapcsolatok a következő hívással paraméter nélküli sorolja fel.

```powershell
Get-AzureRmExpressRouteCircuit
```


A válasz a következő példához hasonló:

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


Egy adott ExpressRoute-kapcsolatcsoportot tájékoztatást kaphat az erőforráscsoport-név és a kapcsolat neve átadott paraméterként a hívást:

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A válasz a következő példa hasonlít:

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


A paraméterek részletes leírását a következő parancs futtatásával szerezheti be:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>Egy ExpressRoute-kapcsolatcsoportot módosítása
ExpressRoute-kapcsolatcsoportot egyes tulajdonságait módosíthatja kapcsolat befolyásolása nélkül.

Állásidő nélkül a következő feladatokat is végezhet:

* Engedélyezi vagy letiltja az ExpressRoute-kapcsolatcsoportot prémium ExpressRoute bővítményt.
* Növelje a ExpressRoute-kapcsolatcsoportot sávszélességét, feltéve, hogy kapacitás érhető el a port. Alacsonyabb verziójúra változtatása a sávszélességet a kapcsolat nem támogatott. 
* Díjköteles adatforgalom korlátlan adatokhoz a mérési terv módosítása A mérési terv módosítása az korlátlan adatforgalom díjköteles adatok nem támogatott.
* Engedélyezheti és letilthatja *klasszikus műveletek engedélyezése*.

Korlátai és korlátozásai további információkért tekintse meg a [ExpressRoute – gyakori kérdések](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>A prémium szintű ExpressRoute-bővítmény engedélyezése
A prémium szintű ExpressRoute-bővítmény a következő PowerShell-részlet használatával engedélyezheti a meglévő kapcsolat:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

A kapcsolatcsoport most már engedélyezett ExpressRoute prémium bővítmény funkciók. Az első lépések számlázási, a prémium szintű bővítmény képességhez, amint sikeresen futtatta a parancsot.

### <a name="to-disable-the-expressroute-premium-add-on"></a>A prémium szintű ExpressRoute-bővítmény letiltása
> [!IMPORTANT]
> Ha erőforrásokat, amelyek nagyobbak, mint mi a szabványos kör megengedett használ, a művelet sikertelen lesz.
> 
> 

Vegye figyelembe a következőket:

* Mielőtt visszaminősítését prémiumról szabvány, meg kell győződnie arról, hogy a kapcsolatcsoport csatolt virtuális hálózatok száma kisebb, mint 10. Ha ezt elmulasztja, a frissítési kérelem sikertelen lesz, és a prémium ütemben számlázás meg.
* Minden virtuális hálózat más geopolitikai régiókban kell választható. Ha nem így tesz, a frissítési kérelem sikertelen lesz, és azt kiszámlázni Önnek prémium ütemben.
* Az útvonaltábla a magánhálózati társviszony-létesítés kisebb, mint 4000 útvonalait kell lennie. Az útvonal tábla mérete nagyobb, mint 4000 útvonalakat, ha a BGP-munkamenetet esik, és nem fog újra engedélyezve, amíg a hirdetett számához nem 4000 éri el.

Letilthatja a meglévő kapcsolat az ExpressRoute prémium szintű bővítmény a következő PowerShell-parancsmag segítségével:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Az ExpressRoute-kapcsolatcsoport sávszélessége frissítése
Támogatott sávszélesség-beállítások a szolgáltató, ellenőrizze a [ExpressRoute – gyakori kérdések](expressroute-faqs.md). Kiválaszthatja a mérete nagyobb, mint a meglévő expressroute méretét.

> [!IMPORTANT]
> Előfordulhat, hogy újra létrehozni az ExpressRoute-kapcsolatcsoport, ha nincs elég kapacitás a meglévő porton. A kapcsolat nem frissíthető, ha nincsenek további kapacitást érhető el az adott helyhez.
>
> Nem csökkenti a sávszélesség az ExpressRoute-kapcsolatcsoportot megszakítása nélkül. Alacsonyabb verziójúra változtatása a sávszélesség szükséges, hogy az ExpressRoute-kapcsolatcsoport kiosztásának megszüntetése és majd építenie az új ExpressRoute-kapcsolatcsoportot.
> 

Miután eldöntötte, hogy milyen méretű van szüksége, az alábbi parancs segítségével méretezze át a a kapcsolatcsoport:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```


A kapcsolatcsoport lesz a Microsoft oldalon kell méretezni. Majd kérje meg a kapcsolat szolgáltatójánál, ez a módosítás megfelelő a ügyféloldali konfigurációjának frissítése. Miután elvégezte ezt az értesítést, kezdjük el számlázást, a frissített sávszélesség-beállítás.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>A Termékváltozat áthelyezése díjköteles korlátlanra
A következő PowerShell-részlet segítségével módosíthatja egy ExpressRoute-kapcsolatcsoport Termékváltozata:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>A klasszikus és Resource Manager-környezetben való hozzáférés szabályozása
Tekintse át a utasításait [a Resource Manager üzembe helyezési modellben a klasszikus áthelyezése ExpressRoute-Kapcsolatcsoportok](expressroute-howto-move-arm.md).  

## <a name="delete"></a>Megszüntetés és az ExpressRoute-kapcsolatcsoport törlése
Vegye figyelembe a következőket:

* Az ExpressRoute-kapcsolatcsoport az összes virtuális hálózatot kell választható. Ha ez a művelet sikertelen, ellenőrizze, hogy ha a virtuális hálózatok a kapcsolatcsoport van csatolva.
* Ha az ExpressRoute-kapcsolatcsoport szolgáltatás szolgáltató üzembe helyezési állapota **kiépítési** vagy **kiépítve** kiosztásának megszüntetése a kapcsolatcsoport az oldalon, hogy a szolgáltató együttműködve. A Folytatás kiszámlázni Önnek, amíg a szolgáltató befejeződött, a kapcsolat megszüntetés, és értesíti, és az erőforrásokat.
* Ha a szolgáltató rendelkezik platformelőfizetés a kapcsolatcsoport (üzembe helyezési állapota szolgáltató értéke **nincs kiépítve**), a kapcsolatcsoport törlése. Ezzel leállítja a számlázási a kör.

Az ExpressRoute-kapcsolatcsoport törlése a következő parancs futtatásával:

```powershell
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>További lépések

Miután létrehozta a kapcsolatcsoport, ügyeljen arra, hogy a következő lépéseket:

* [Létrehozásához és módosításához az ExpressRoute-kapcsolatcsoport esetében routing](expressroute-howto-routing-arm.md)
* [A virtuális hálózat csatolása az ExpressRoute-kapcsolatcsoportot](expressroute-howto-linkvnet-arm.md)
