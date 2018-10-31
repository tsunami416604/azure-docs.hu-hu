---
title: 'Létrehozása és módosítása egy ExpressRoute-kapcsolatcsoport: PowerShell: az Azure Resource Manager |} A Microsoft Docs'
description: Ez a cikk bemutatja, hogyan hozhat létre, üzembe helyezése, győződjön meg arról, frissítése, törlése és ExpressRoute-kapcsolatcsoport megszüntetése.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 10/18/2018
ms.author: ganesr;cherylmc
ms.openlocfilehash: e8af299967b3f7d8010004cc60058733d7c80163
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249287"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Létrehozása és módosítása a PowerShell használatával egy ExpressRoute-kapcsolatcsoporttal
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Videó – Azure portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ez a cikk ismerteti az Azure ExpressRoute-kapcsolatcsoport létrehozása a PowerShell-parancsmagok és az Azure Resource Manager üzemi modell használatával. Ez a cikk emellett bemutatja, hogyan ellenőrizheti a kapcsolatcsoport állapotát, a frissítés után, vagy törlése és megszüntetése azt.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené, tekintse át a [Előfeltételek](expressroute-prerequisites.md) és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell használata
[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create"></a>Létrehozása és kiépítése az ExpressRoute-kapcsolatcsoport
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Jelentkezzen be az Azure-fiókjával, és válassza ki az előfizetését
A konfiguráció első lépésként jelentkezzen be az Azure-fiókjával. Az alábbi példák segítségével segíthet a kapcsolódásban:

Az Azure cloud Shell használatakor, nem kell Connect-AzureRmAccount, futtassa, automatikusan csatlakozik.

```azurepowershell
Connect-AzureRmAccount
```

Keresse meg a fiókot az előfizetésekben:

```azurepowershell-interactive
Get-AzureRmSubscription
```

Hozzon létre egy ExpressRoute-kapcsolatcsoporttal a kívánt előfizetés kiválasztásához:

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. A támogatott szolgáltatók, a helyek és a sávszélesség-lista lekérése
ExpressRoute-kapcsolatcsoport létrehozása, birtokában támogatott kapcsolatszolgáltatók, helyek és sávszélesség-lehetőségek listája.

A PowerShell-parancsmag **Get-AzureRmExpressRouteServiceProvider** adja vissza ezt az információt fogja használni a későbbi lépésekben:

```azurepowershell-interactive
Get-AzureRmExpressRouteServiceProvider
```

Ellenőrizze, hogy a kapcsolatszolgáltató szerepel-e ott. Jegyezze fel az alábbi adatokat, amelyek később szüksége lesz egy kapcsolatcsoport létrehozásakor:

* Name (Név)
* PeeringLocations
* BandwidthsOffered

Most már készen áll az ExpressRoute-kapcsolatcsoport létrehozása.   

### <a name="3-create-an-expressroute-circuit"></a>3. ExpressRoute-kapcsolatcsoport létrehozása
Ha még nem rendelkezik egy erőforráscsoport, egy az ExpressRoute-kapcsolatcsoport létrehozása előtt kell létrehoznia. Ehhez futtassa a következő parancsot:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```


Az alábbi példa bemutatja, hogyan hozhat létre egy 200 Mbps ExpressRoute-kapcsolatcsoporton keresztül Equinix szilícium-völgy. Másik szolgáltatóhoz, és különböző beállításokat használja, ha helyettesítse be ezt az információt, amikor a kérést. Használja az alábbi példa egy új kulcs kérése:

```azurepowershell-interactive
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Ügyeljen arra, hogy megadja a helyes Termékváltozat-szint és a Termékváltozat-család:

* Termékváltozat-szint határozza meg, hogy egy ExpressRoute-standard vagy az ExpressRoute prémium bővítmény engedélyezve van. Megadhat *Standard* beolvasni a standard Termékváltozat vagy *prémium* for a premium bővítményt.
* Termékváltozat-család határozza meg a számlázási. Megadhat *Metereddata* forgalmi díjas csomag, és *Unlimiteddata* a korlátlan adatforgalmú. Módosíthatja a számlázási típusát a *Metereddata* való *Unlimiteddata*, de nem módosíthatja a típust *Unlimiteddata* való *Metereddata*.

> [!IMPORTANT]
> Az ExpressRoute-kapcsolatcsoport számlázása a szolgáltatáskulcs pillanatától kezdve. Győződjön meg arról, hogy ha a kapcsolatszolgáltató üzembe helyezi a kapcsolatcsoportot készen áll a művelet végrehajtása.
> 
> 

A válasz tartalmazza a kulcsot. A paraméterek a részletes leírását a következő parancs futtatásával kaphat:

```azurepowershell-interactive
get-help New-AzureRmExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Minden ExpressRoute-kapcsolatcsoport listázása
Az összes Ön által létrehozott ExpressRoute-Kapcsolatcsoportok listájának lekéréséhez futtassa a **Get-AzureRmExpressRouteCircuit** parancsot:

```azurepowershell-interactive
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

A válasz a következő példához hasonlóan néz ki:

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

Ezt az információt bármikor használatával lekérheti a `Get-AzureRmExpressRouteCircuit` parancsmagot. Minden kapcsolatcsoportra paraméterek nélkül a következő hívással sorolja fel. A szolgáltatás kulcs szerepel az *ServiceKey* mező:

```azurepowershell-interactive
Get-AzureRmExpressRouteCircuit
```


A válasz a következő példához hasonlóan néz ki:

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


A paraméterek a részletes leírását a következő parancs futtatásával kaphat:

```azurepowershell-interactive
get-help Get-AzureRmExpressRouteCircuit -detailed
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. A kulcs küldése a kapcsolatszolgáltató a kiépítés
*ServiceProviderProvisioningState* provisioning service-szolgáltató oldalán aktuális állapotát ismerteti. Állapot a Microsoft oldalon az állapot biztosít. A kapcsolatcsoport kiépítési állapotok kapcsolatos további információkért lásd: [munkafolyamatok](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Amikor létrehoz egy új ExpressRoute-kapcsolatcsoportot, a kapcsolatcsoport a következő állapotban van:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



A kapcsolatcsoport változik a következő állapotot, amikor a kapcsolatszolgáltató van folyamatban, amely lehetővé teszi az Ön számára:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

ExpressRoute-kapcsolatcsoport segítségével tudja meg a következő állapotban kell lennie:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Rendszeresen ellenőrizze a kapcsolatcsoport kulcs állapotát és az állapot
A kapcsolatcsoport kulcs állapotát és az állapot ellenőrzése jelzi, ha a szolgáltató engedélyezve van a kapcsolatcsoport. A kapcsolatcsoport konfigurálása után *ServiceProviderProvisioningState* jelenik meg *kiépített*, az alábbi példában látható módon:

```azurepowershell-interactive
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A válasz a következő példához hasonlóan néz ki:

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

### <a name="7-create-your-routing-configuration"></a>7. Az útválasztási konfiguráció létrehozása
Részletes útmutatásért tekintse meg a [ExpressRoute-Kapcsolatcsoportok útválasztási konfigurációja](expressroute-howto-routing-arm.md) cikk létrehozásához és módosításához a kapcsolatcsoport társviszony-létesítéseket.

> [!IMPORTANT]
> Ezek az utasítások csak 2 réteg szolgáltatás kínáló szolgáltatóknál létrehozott Kapcsolatcsoportok vonatkoznak. Ha használja a szolgáltató által kínált felügyelt réteg (általában egy IP VPN, mint az MPLS) 3 szolgáltatások, a kapcsolatszolgáltató konfigurálja és kezeli az útválasztást Ön helyett.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal
Ezután egy virtuális hálózat összekapcsolása az ExpressRoute-kapcsolatcsoportot. Használja a [virtuális hálózatok összekapcsolása az ExpressRoute-Kapcsolatcsoportok](expressroute-howto-linkvnet-arm.md) című cikket, amikor a Resource Manager üzemi modellel dolgozik.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Az ExpressRoute-Kapcsolatcsoportok állapotának beolvasása
Ezt az információt bármikor használatával lekérheti a **Get-AzureRmExpressRouteCircuit** parancsmagot. Minden kapcsolatcsoportra paraméterek nélkül a következő hívással sorolja fel.

```azurepowershell-interactive
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


Az egy adott ExpressRoute-kapcsolatcsoport információkat szerezhet az erőforráscsoport-nevet és a kapcsolatcsoport neve paraméterként átadva a hívást:

```azurepowershell-interactive
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A válasz a következő példához hasonlóan néz ki:

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


A paraméterek a részletes leírását a következő parancs futtatásával kaphat:

```azurepowershell-interactive
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>Az ExpressRoute-Kapcsolatcsoportok módosítása
Egyes ExpressRoute-kapcsolatcsoport tulajdonságainak kapcsolat befolyásolása nélkül módosíthatja.

A következő feladatok leállás nélkül teheti meg:

* Engedélyezi vagy letiltja az ExpressRoute prémium bővítmény az ExpressRoute-kapcsolatcsoport esetében.
* Növelje az ExpressRoute-kapcsolatcsoport sávszélességét, feltéve, hogy kapacitás érhető el a porton. A sávszélesség csökkentése azonban a kapcsolat nem támogatott. 
* Módosítsa a mérési terv díjas a korlátlan. Mérési csomag módosítása a korlátlan, a forgalmi díjas adatok nem támogatott.
* Engedélyezheti és letilthatja az *klasszikus működés engedélyezése*.

További információ a korlátok és korlátozások: a [ExpressRoute – gyakori kérdések](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Az ExpressRoute prémium bővítmény engedélyezése
Az ExpressRoute prémium bővítmény használatával a következő PowerShell-kódrészlettel a meglévő kapcsolatcsoport engedélyezheti:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

A kapcsolatcsoport most már engedélyezve van az ExpressRoute prémium bővítmény szolgáltatásokat. Az első lépések a premium bővítmény funkció számlázása, amint a parancs már sikeresen futott.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Az ExpressRoute prémium bővítmény letiltása
> [!IMPORTANT]
> Ha az erőforrást, amely nagyobb, mint a megengedett a standard szintű kapcsolatcsoportot használ, ez a művelet sikertelen lehet.
> 
> 

Tekintse meg az alábbi információkat:

* Mielőtt standard Visszaléptetés a prémium szintű, biztosítania kell, hogy a kapcsolatcsoporthoz kapcsolt virtuális hálózatok szám 10-nél kisebb. Ha nem, akkor a frissítési kérelem meghiúsul, és a megadott prémium szintű alapján számlázunk.
* Más geopolitikai régiókban lévő összes virtuális hálózatot kell leválasztása. Ha nem így tesz, a frissítési kérelem meghiúsul, és a megadott prémium szintű alapján számlázunk.
* Az útvonaltábla kisebb, mint 4000 útvonalak privát társviszony-létesítés kell lennie. Ha az útválasztási táblázat mérete nagyobb, mint 4000, a BGP-munkamenetben csökken, és nem fog újra engedélyezve, amíg a meghirdetett előtagok száma 4000 alá nem csökken.

Az ExpressRoute prémium bővítmény a meglévő kapcsolatcsoport letilthatja az alábbi PowerShell-parancsmag segítségével:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Az ExpressRoute-kapcsolatcsoport sávszélességét frissítése
Támogatott sávszélesség-lehetőségek a szolgáltató, ellenőrizze a [ExpressRoute – gyakori kérdések](expressroute-faqs.md). Választhat a meglévő kapcsolatcsoport méreténél nagyobb méretű.

> [!IMPORTANT]
> Az ExpressRoute-kapcsolatcsoport hozza létre újra, ha nincs elegendő kapacitás a meglévő porton is. A kapcsolatcsoport nem frissíthető, ha nincsenek további kapacitás érhető el az adott helyhez.
>
> Megszakítás nélküli ExpressRoute-kapcsolatcsoport sávszélességét nem csökkenthető. Alacsonyabb verziójúra változtatása sávszélesség megköveteli, hogy az ExpressRoute-kapcsolatcsoport megszüntetése, és ezután építse ki újra a egy új ExpressRoute-kapcsolatcsoportot.
> 

Miután eldöntötte, milyen méretű van szüksége, a következő paranccsal méretezheti át a kapcsolatcsoport:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```


A kapcsolatcsoport fogja méretezni, a Microsoft oldalán. Ezután kapcsolatba kell frissíteni a konfigurációk, azok oldalán, a módosítás megfelelő a kapcsolatszolgáltató. Miután végrehajtotta ezt az értesítést, megkezdjük a számlázást, a frissített sávszélesség-beállítás.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>A Termékváltozat a áthelyezése díjköteles korlátlan
Az ExpressRoute-kapcsolatcsoport SKU-ja a következő PowerShell-kódrészlettel módosíthatja:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>A klasszikus és Resource Manager-környezetben való hozzáférés szabályozásához
Tekintse át a következő témakör utasításait [helyezze át az ExpressRoute-Kapcsolatcsoportok a klasszikusból a Resource Manager-alapú üzemi modellbe](expressroute-howto-move-arm.md).  

## <a name="delete"></a>A megszüntetés és a egy ExpressRoute-kapcsolatcsoport törlése
Tekintse meg az alábbi információkat:

* Az összes virtuális hálózatot le kell választania az ExpressRoute-kapcsolatcsoportról. Ha ez a művelet nem sikerül, ellenőrizze, hogy ha bármely virtuális hálózatokhoz kapcsolódnak-e a kapcsolatcsoportot.
* Ha az ExpressRoute kapcsolatcsoport szolgáltató üzembe helyezési állapota **kiépítési** vagy **kiépített** -e, hogy azok oldalán a kapcsolatcsoport megszüntetése a szolgáltató. Továbbra is erőforrásokat tartalékolnia, és addig, amíg a szolgáltató befejeződött, a kapcsolatcsoport megszüntetése, és értesítést küld nekünk fel díjat.
* Ha a szolgáltató eltávolította a kapcsolatcsoportot (a kiépítési állapota szolgáltató beállítása **nincs kiépítve**), törölheti a kapcsolatcsoportot. Ez leállítja a kapcsolatcsoport számlázását.

Az ExpressRoute-kapcsolatcsoport a következő parancs futtatásával törölheti:

```azurepowershell-interactive
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>További lépések

Miután létrehozta a kapcsolatcsoportot, győződjön meg arról, hogy nincs-e a következő lépések:

* [Útválasztás az ExpressRoute-kapcsolatcsoport létrehozása vagy módosítása](expressroute-howto-routing-arm.md)
* [A virtuális hálózat összekapcsolása az ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md)
