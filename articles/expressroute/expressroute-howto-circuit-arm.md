---
title: 'Azure ExpressRoute: Kapcsolat módosítása: PowerShell'
description: ExpressRoute-kapcsolat létrehozása, kiépítése, ellenőrzése, frissítése, törlése és megszüntetése.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/08/2020
ms.author: cherylmc
ms.openlocfilehash: ab44d5358154c17622eef68205ac2326e81377a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75770968"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>ExpressRoute-kapcsolatcsoport létrehozása és módosítása a PowerShell-lel
> [!div class="op_single_selector"]
> * [Azure-portál](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sablon](expressroute-howto-circuit-resource-manager-template.md)
> * [Videó – Azure-portál](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ez a cikk segít egy ExpressRoute-kapcsolat létrehozásában a PowerShell-parancsmagok és az Azure Resource Manager telepítési modell használatával. Azt is ellenőrizheti az állapotát, frissítése, törlése vagy megszüntetése egy kapcsolatcsoport.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené, a konfiguráció megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és [a munkafolyamatokat.](expressroute-workflows.md)

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell együttműködése

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute-kapcsolat létrehozása és kiépítése
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Jelentkezzen be Azure-fiókjába, és válassza ki az előfizetést

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. A támogatott szolgáltatók, helyek és sávszélességek listájának beszerezni
ExpressRoute-kapcsolat létrehozása előtt szüksége van a támogatott kapcsolatszolgáltatók, helyek és sávszélesség-beállítások listájára.

A **Get-AzExpressRouteServiceProvider PowerShell-parancsmag** ezt az információt adja vissza, amelyet a későbbi lépésekben fog használni:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Ellenőrizze, hogy a kapcsolatszolgáltató szerepel-e a listán. Jegyezze fel a következő információkat, amelyekre később szüksége lesz egy áramkör létrehozásakor:

* Név
* Társviszony-létesítési helyek
* Kínált sávszélességek

Most már készen áll egy ExpressRoute-kapcsolat létrehozására.

### <a name="3-create-an-expressroute-circuit"></a>3. ExpressRoute-kapcsolat létrehozása
Ha még nem rendelkezik erőforráscsoporttal, létre kell hoznia egyet az ExpressRoute-kapcsolat létrehozása előtt. Ezt a következő parancs futtatásával teheti meg:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

A következő példa bemutatja, hogyan hozhat létre egy 200 Mbps-es ExpressRoute-áramkört az Equinixen keresztül a Szilícium-völgyben. Ha más szolgáltatót és más beállításokat használ, ezt az információt a kérés benyújtásakor helyettesítse. Az alábbi példában kérhet új szolgáltatáskulcsot:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Győződjön meg arról, hogy a megfelelő Termékváltozat-szintet és termékváltozat-családot adja meg:

* A Termékváltozat-szint határozza meg, hogy az ExpressRoute-áramkör helyi, standard vagy prémium szintű.SKU tier determines whether an ExpressRoute circuit is [Local,](expressroute-faqs.md#expressroute-local)Standard or [Premium.](expressroute-faqs.md#expressroute-premium) Megadhatja *a helyi*, *standard* vagy *prémium*értéket.
* Termékváltozat család határozza meg a számlázási típus. Megadhatja *metereddata* egy forgalmi díjas adatcsomag és *Unlimiteddata* korlátlan adatcsomag. A számlázási típust *Metereddata-ról* *Unlimiteddata-ra módosíthatja,* de a típust nem módosíthatja *Korlátlan adatról* *Metereddata-ra.* A *helyi* áramkör mindig *Unlimiteddata*.

> [!IMPORTANT]
> Az ExpressRoute-kapcsolat számlázása a szolgáltatáskulcs kiadásának pillanatától kezdve történik. Győződjön meg arról, hogy ezt a műveletet akkor hajtja végre, amikor a kapcsolatszolgáltató készen áll a kapcsolat létesítésére.
>
>

A válasz tartalmazza a szolgáltatáskulcsot. Az összes paraméter részletes leírását a következő parancs futtatásával kaphatja meg:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Az összes ExpressRoute-kapcsolat listázása
A létrehozott ExpressRoute-áramkörök listájának leválasztásához futtassa a **Get-AzExpressRouteCircuit** parancsot:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

Ezeket az információkat bármikor lekérheti `Get-AzExpressRouteCircuit` a parancsmag használatával. A paraméterek nélküli hívás felsorolja az összes áramkört. A szolgáltatáskulcs a *ServiceKey* mezőben található:

```azurepowershell-interactive
Get-AzExpressRouteCircuit
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


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Küldje el a szolgáltatáskulcsot a kapcsolódási szolgáltatónak a kiépítéshez
*ServiceProviderProvisioningState* a szolgáltatói oldalon a kiépítés aktuális állapotáról nyújt tájékoztatást. Az állapot biztosítja az állapotot a Microsoft oldalán. A körlétesítési állapotokról a Munkafolyamatok című [témakörben talál](expressroute-workflows.md#expressroute-circuit-provisioning-states)további információt.

Új ExpressRoute-kapcsolat létrehozásakor az áramkör a következő állapotban van:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Az áramkör a következő állapotra változik, amikor a kapcsolatszolgáltató az Ön számára engedélyezi azt:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Ahhoz, hogy egy ExpressRoute-áramkört használhasson, a következő állapotban kell lennie:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Rendszeresen ellenőrizze az áramköri kulcs állapotát és állapotát
Az áramköri kulcs állapotának és állapotának ellenőrzése jelzi, ha a szolgáltató engedélyezte a kapcsolatcsoport használatát. Az áramkör konfigurálása után a *ServiceProviderProvisioningState* *kiépítettként*jelenik meg, ahogy az a következő példában látható:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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
Lépésenkénti útmutatást az [ExpressRoute-áramkör-útválasztási konfigurációs](expressroute-howto-routing-arm.md) cikkben talál a kapcsolatlétesítések létrehozásáról és módosításáról.

> [!IMPORTANT]
> Ezek az utasítások csak a 2. Ha olyan szolgáltatót használ, amely 3.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Virtuális hálózat összekapcsolása ExpressRoute-áramkörrel
Ezután kapcsolja össze a virtuális hálózatot az ExpressRoute-kapcsolattal. Használja a [virtuális hálózatok összekapcsolása ExpressRoute-áramkörök](expressroute-howto-linkvnet-arm.md) cikket, amikor az Erőforrás-kezelő telepítési modell.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>ExpressRoute-kapcsolat állapotának beszerzése
Ezeket az információkat a **Get-AzExpressRouteCircuit** parancsmag használatával bármikor lekérheti. A paraméterek nélküli hívás felsorolja az összes áramkört.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
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


Egy adott ExpressRoute-kapcsolatról úgy kaphat információt, hogy az erőforráscsoport nevét és a kapcsolatcsoport nevét adja meg a hívás paramétereként:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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


Az összes paraméter részletes leírását a következő parancs futtatásával kaphatja meg:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>ExpressRoute-kapcsolat módosítása
Az ExpressRoute-áramkör bizonyos tulajdonságait a kapcsolat befolyásolása nélkül módosíthatja.

A következő feladatokat teheti meg állásidő nélkül:

* ExpressRoute-alapú bővítmény engedélyezése vagy letiltása az ExpressRoute-kapcsolaton.
* Növelje az ExpressRoute-kapcsolat sávszélességét, feltéve, hogy a porton rendelkezésre áll kapacitás. Az áramkör sávszélességének visszaminősítése nem támogatott.
* Módosítsa a mérési terv forgalmi díjas adatok korlátlan adat. A mérési terv módosítása korlátlan adatról forgalmi díjas adatokra nem támogatott.
* Engedélyezheti és letilthatja *a Klasszikus műveletek engedélyezése .*

A korlátozásokról és korlátozásokról az [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakörben talál további információt.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Az ExpressRoute prémium bővítmény engedélyezése
Az ExpressRoute prémium szintű bővítményt a meglévő kapcsolatcsoporthoz a következő PowerShell-kódrészlet használatával engedélyezheti:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

A kapcsolatcsoport mostantól engedélyezve van az ExpressRoute prémium szintű bővítményfunkcióival. A mint a parancs sikeresen futott, megkezdjük a prémium szintű bővítményfunkció számlázását.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Az ExpressRoute prémium bővítmény letiltása
> [!IMPORTANT]
> Ha olyan erőforrásokat használ, amelyek nagyobbak, mint a szabványos kapcsolatcsoport engedélyezett, ez a művelet sikertelen lehet.
>
>

Tekintse meg az alábbi információkat:

* Mielőtt prémiumról normál ra dulakálna, gondoskodnia kell arról, hogy az áramkörhöz kapcsolódó virtuális hálózatok száma 10-nél kevesebb legyen. Ha nem, a frissítési kérelem sikertelen lesz, és prémium díjakat számlázunk.
* Le kell kapcsolnia a többi geopolitikai régió összes virtuális hálózatát. Ha ezt nem teszi meg, a frissítési kérelem sikertelen lesz, és prémium díjakat számlázunk.
* Az útvonaltáblának 4000-nél kisebb útvonalnak kell lennie a privát társviszony-létesítéshez. Ha az útvonaltábla mérete nagyobb, mint 4000 útvonal, a BGP-munkamenet csökken, és nem lesz újra engedélyezve, amíg a meghirdetett előtagok száma 4000 alá nem csökken.

A meglévő kapcsolatcsoport ExpressRoute prémium szintű bővítményét a következő PowerShell-parancsmag használatával tilthatja le:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Az ExpressRoute-áramkör sávszélességének frissítése
A szolgáltató számára támogatott sávszélesség-beállításokért tekintse meg az [ExpressRoute gyakori kérdések című, ikonját.](expressroute-faqs.md) A meglévő áramkör méreténél nagyobb méretet választhat.

> [!IMPORTANT]
> Előfordulhat, hogy újra létre kell hoznia az ExpressRoute-áramkört, ha a meglévő porton nincs megfelelő kapacitás. Nem frissítheti a kapcsolatcsoport, ha nincs további kapacitás áll rendelkezésre az adott helyen.
>
> Az ExpressRoute-áramkörök sávszélessége megszakítás nélkül nem csökkenthető. A sávszélesség visszaminősítéséhez az ExpressRoute-kapcsolat bontásához, majd egy új ExpressRoute-kapcsolat újbóli kiépítéséhez szükséges.
>

Miután eldöntötte, hogy milyen méretre van szüksége, a következő paranccsal méretezze át az áramkört:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Az áramkör mérete a Microsoft oldalán lesz. Ezután kapcsolatba kell lépnie a kapcsolatszolgáltatóval, hogy frissítse a konfigurációkat az ő oldalukon, hogy megfeleljen eknek a változásnak. Az értesítés után megkezdjük a frissített sávszélesség-beállítás számlázását.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>A termékváltozat áthelyezése forgalmi díjasról korlátlanra
Az ExpressRoute-kapcsolat termékváltozatát a következő PowerShell-kódrészlet használatával módosíthatja:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>A klasszikus és az Erőforrás-kezelő környezethez való hozzáférés szabályozása
Tekintse át a [Move ExpressRoute-áramkörök utasításait a klasszikustól az Erőforrás-kezelő telepítési modelljéig.](expressroute-howto-move-arm.md)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute-kapcsolatcsoport megszüntetése és törlése
Tekintse meg az alábbi információkat:

* Az összes virtuális hálózatot le kell választania az ExpressRoute-kapcsolatcsoportról. Ha ez a művelet sikertelen, ellenőrizze, hogy vannak-e virtuális hálózatok a kapcsolatcsoporthoz.
* Ha az ExpressRoute-kapcsolatszolgáltató létesítési állapota **Kiépítés** vagy **Kiépítve,** együtt kell működnie a szolgáltatóval az adott kapcsolat i. Továbbra is fenntartjuk az erőforrásokat, és kiszámlázunk Önnek, amíg a szolgáltató be nem fejezi a kapcsolatcsoport kiürítését, és nem értesíti kedélyünket.
* Ha a szolgáltató megszüntette a körcsoportot (a szolgáltató létesítési állapota **Nincs kiépítve)** értékre van állítva, törölheti a csoportot. Ez leállítja a kapcsolatcsoport számlázását.

Az ExpressRoute-áramköra a következő parancs futtatásával törölhető:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>További lépések

Az áramkör létrehozása után tegye meg a következő lépéseket:

* [Útválasztás létrehozása és módosítása az ExpressRoute-kapcsolathoz](expressroute-howto-routing-arm.md)
* [A virtuális hálózat összekapcsolása az ExpressRoute-áramkörrel](expressroute-howto-linkvnet-arm.md)
