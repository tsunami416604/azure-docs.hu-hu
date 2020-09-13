---
title: 'Azure-ExpressRoute: áramkör módosítása: PowerShell'
description: ExpressRoute-áramkör létrehozása, kiépítése, ellenőrzése, frissítése, törlése és megszüntetése.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/08/2020
ms.author: duau
ms.openlocfilehash: e9bf9dbe0f4146101513ab9786b298ac6b43b6a3
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566297"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>ExpressRoute-kapcsolatcsoport létrehozása és módosítása a PowerShell-lel
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sablon](expressroute-howto-circuit-resource-manager-template.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ebből a cikkből megtudhatja, hogyan hozhat létre ExpressRoute-áramkört PowerShell-parancsmagok és a Azure Resource Manager üzemi modell használatával. Az áramkör állapotát, frissítését, törlését vagy megszüntetését is megtekintheti.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené, tekintse át az [előfeltételeket](expressroute-prerequisites.md) és a [munkafolyamatokat](expressroute-workflows.md) a konfigurálás megkezdése előtt.

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute-kör létrehozása és kiépítése
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. a támogatott szolgáltatók, helyszínek és sávszélességek listájának beolvasása
A ExpressRoute kör létrehozása előtt szüksége lesz a támogatott kapcsolati szolgáltatók, helyszínek és sávszélesség-beállítások listájára.

A **Get-AzExpressRouteServiceProvider PowerShell-** parancsmag ezt az információt adja vissza, amelyet a későbbi lépésekben fog használni:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Ellenőrizze, hogy szerepel-e a kapcsolat szolgáltatója. Jegyezze fel a következő információkat, amelyekre később szüksége lesz az áramkör létrehozásakor:

* Name
* PeeringLocations
* BandwidthsOffered

Most már készen áll egy ExpressRoute-áramkör létrehozására.

### <a name="3-create-an-expressroute-circuit"></a>3. ExpressRoute áramkör létrehozása
Ha még nem rendelkezik erőforráscsoporthoz, létre kell hoznia egyet a ExpressRoute áramkör létrehozása előtt. Ezt a következő parancs futtatásával teheti meg:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

Az alábbi példa bemutatja, hogyan hozhat létre egy 200 Mbps ExpressRoute áramkört a Equinix a Szilícium-völgyben. Ha más szolgáltatót használ, és különböző beállításokkal rendelkezik, akkor a kérés végrehajtásakor cserélje le ezt az információt. Az alábbi példa egy új szolgáltatási kulcs igénylésére használható:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Győződjön meg arról, hogy a megfelelő SKU-szintet és SKU-családot adta meg:

* Az SKU-szint meghatározza, hogy a ExpressRoute áramkör [helyi](expressroute-faqs.md#expressroute-local), standard vagy [prémium](expressroute-faqs.md#expressroute-premium)szintű-e. Megadhat *helyi*, *standard* vagy *prémium*szintűeket is. A SKU nem módosítható a *standard/prémium* szintről a *helyi*értékre.
* Az SKU termékcsalád meghatározza a számlázási típust. Megadhatja a *Metereddata* a forgalmi díjas csomaghoz és a *Unlimiteddata* korlátlan adatcsomag esetén. A számlázási típust a *Metereddata* és a *Unlimiteddata*között is módosíthatja, de a típus nem módosítható a *Unlimiteddata* értékről a *Metereddata*-re. A *helyi* áramkör mindig *Unlimiteddata*.

> [!IMPORTANT]
> A ExpressRoute-áramkör számlázása a szolgáltatási kulcs kiállításának pillanatától számítva történik. Győződjön meg arról, hogy ezt a műveletet akkor hajtja végre, ha a kapcsolati szolgáltató készen áll az áramkör kiépítésére.
>
>

A válasz tartalmazza a szolgáltatás kulcsát. Az összes paraméter részletes leírását a következő parancs futtatásával érheti el:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. az összes ExpressRoute-áramkör listázása
Az összes létrehozott ExpressRoute-áramkör listájának lekéréséhez futtassa a **Get-AzExpressRouteCircuit** parancsot:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

A válasz az alábbi példához hasonlóan néz ki:

```azurepowershell
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
```

Ezeket az adatokat bármikor lekérheti a `Get-AzExpressRouteCircuit` parancsmag használatával. A hívás paraméterek nélkül való megadásával az összes áramkör szerepel. A szolgáltatási kulcs megjelenik a *serviceKey* mezőben:

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


A válasz az alábbi példához hasonlóan néz ki:

```azurepowershell
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
```


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. küldje el a szolgáltatási kulcsot a kapcsolat szolgáltatójának az üzembe helyezéshez
A *ServiceProviderProvisioningState* információt nyújt a szolgáltatói oldalon a kiépítés aktuális állapotáról. Az állapot a Microsoft oldalon található állapotot biztosítja. További információ az áramkör kiépítési állapotáról: [munkafolyamatok](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Új ExpressRoute-kör létrehozásakor az áramkör a következő állapotban van:

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```



Az áramkör a következő állapotra vált, amikor a kapcsolati szolgáltató a következő állapotban van:

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

Ahhoz, hogy használni tudja a ExpressRoute áramkört, a következő állapotban kell lennie:

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. az áramköri kulcs állapotának és állapotának rendszeres időközönkénti keresése
Az állapot és az áramköri kulcs állapotának ellenőrzése lehetővé teszi, hogy a szolgáltató engedélyezte az áramkört. Az áramkör konfigurálása után a *ServiceProviderProvisioningState* *kiépítve*jelenik meg, ahogy az az alábbi példában is látható:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A válasz az alábbi példához hasonlóan néz ki:

```azurepowershell
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

### <a name="7-create-your-routing-configuration"></a>7. hozza létre az útválasztási konfigurációt
Részletes útmutatásért lásd az [ExpressRoute-áramköri útválasztási konfiguráció](expressroute-howto-routing-arm.md) című cikket az áramköri társítások létrehozásához és módosításához.

> [!IMPORTANT]
> Ezek az utasítások csak a 2. rétegbeli kapcsolati szolgáltatásokat nyújtó szolgáltatók által létrehozott áramkörökre vonatkoznak. Ha olyan szolgáltatót használ, amely felügyelt 3. rétegbeli szolgáltatásokat (jellemzően IP VPN, például MPLS) kínál, a kapcsolati szolgáltatója konfigurálja és kezeli az útválasztást.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. virtuális hálózat összekapcsolása egy ExpressRoute-áramkörrel
Ezután csatoljon egy virtuális hálózatot a ExpressRoute-áramkörhöz. Ha a Resource Manager-alapú üzemi modellel dolgozik, használja a [virtuális hálózatok összekapcsolása a ExpressRoute áramkörökkel](expressroute-howto-linkvnet-arm.md) című cikket.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>ExpressRoute kör állapotának beolvasása
Ezeket az adatokat bármikor lekérheti a **Get-AzExpressRouteCircuit** parancsmag használatával. A hívás paraméterek nélkül való megadásával az összes áramkör szerepel.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


A válasz a következő példához hasonló:

```azurepowershell
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


Az adott ExpressRoute-áramkörre vonatkozó információk lekéréséhez adja meg az erőforráscsoport nevét és az áramkör nevét paraméterként a híváshoz:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A válasz az alábbi példához hasonlóan néz ki:

```azurepowershell
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


Az összes paraméter részletes leírását a következő parancs futtatásával érheti el:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>ExpressRoute áramkör módosítása
Egy ExpressRoute-kör bizonyos tulajdonságait módosíthatja a kapcsolat befolyásolása nélkül.

A következő feladatokat a leállás nélkül végezheti el:

* Engedélyezheti vagy letilthatja a ExpressRoute-áramkörhöz tartozó ExpressRoute Premium-bővítményt. Az SKU *szabványos/prémiumról* *helyire* történő módosítása nem támogatott.
* Növelje a ExpressRoute-áramkör sávszélességét, ha rendelkezésre áll a porton elérhető kapacitás. Az áramkör sávszélességének visszaminősítése nem támogatott.
* Módosítsa a mérési tervet a mért adatokról a korlátlan mennyiségű adatokra. A mérési terv korlátlan mennyiségű és mért adatok közötti módosítása nem támogatott.
* Engedélyezheti és letilthatja a *klasszikus műveletek engedélyezését*.

A korlátozásokkal és korlátozásokkal kapcsolatos további információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Az ExpressRoute Premium bővítmény engedélyezése
A ExpressRoute Premium bővítményt a következő PowerShell-kódrészlettel engedélyezheti a meglévő áramkörhöz:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Az áramkör most már engedélyezve van a ExpressRoute Premium kiegészítő funkciói. Ha sikeresen futtatta a parancsot, megkezdjük a prémium szintű kiegészítő funkció számlázását.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Az ExpressRoute Premium bővítmény letiltása
> [!IMPORTANT]
> Ha olyan erőforrásokat használ, amelyek nagyobbak a standard szintű áramkör esetében, ez a művelet sikertelen lehet.
>
>

Tekintse meg az alábbi információkat:

* A prémiumról a standard szintre való visszalépés előtt meg kell győződnie arról, hogy az áramkörhöz kapcsolódó virtuális hálózatok száma kevesebb, mint 10. Ha nem, a frissítési kérelem meghiúsul, és a prémium díjszabás szerint számoljuk el.
* A többi geopolitikai régióban lévő összes virtuális hálózatot le kell kapcsolni. Ha ezt nem teszi meg, a frissítési kérelem meghiúsul, és a prémium díjszabás szerint számoljuk el.
* Az útválasztási táblázatnak kisebbnek kell lennie, mint 4 000 útvonal a privát társak számára. Ha az útválasztási táblázat mérete nagyobb, mint 4 000 útvonal, a BGP-munkamenet elveszik, és nem lesz újra engedélyezve, amíg a meghirdetett előtagok száma nem haladja meg a 4 000 értéket.

A ExpressRoute Premium bővítményt a következő PowerShell-parancsmaggal tilthatja le a meglévő áramkörhöz:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>A ExpressRoute-áramkör sávszélességének frissítése
A szolgáltató által támogatott sávszélesség-beállításokért olvassa el a [ExpressRoute – gyakori kérdések](expressroute-faqs.md)című részt. A meglévő áramköri méretnél nagyobb méretet is kiválaszthat.

> [!IMPORTANT]
> Előfordulhat, hogy újra létre kell hoznia a ExpressRoute áramkört, ha nincs elegendő kapacitás a meglévő porton. Az áramkör nem frissíthető, ha az adott helyen nem érhető el további kapacitás.
>
> Egy ExpressRoute áramkör sávszélessége nem csökkenthető megszakítás nélkül. A lefokozási sávszélességhez meg kell szüntetnie a ExpressRoute áramkört, majd újra kell telepítenie egy új ExpressRoute áramkört.
>

Miután eldöntötte, hogy milyen méretűre van szüksége, az alábbi paranccsal méretezheti át az áramkört:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Az áramkör a Microsoft oldalán lesz felméretezve. Ezt követően kapcsolatba kell lépnie a kapcsolat szolgáltatójával, hogy frissítse a konfigurációkat a saját oldalán a változásnak megfelelően. Az értesítés elvégzése után megkezdjük a frissített sávszélesség-beállítás számlázását.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Az SKU áthelyezése a mért értékről korlátlanra
A ExpressRoute áramkör SKU-jának módosításához a következő PowerShell-kódrészletet használhatja:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>A klasszikus és a Resource Manager-környezetekhez való hozzáférés szabályozása
Tekintse át a [ExpressRoute-áramkörök áthelyezése a Klasszikusból a Resource Manager](expressroute-howto-move-arm.md)-alapú üzemi modellbe című témakör utasításait.

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute-kapcsolatcsoport megszüntetése és törlése
Tekintse meg az alábbi információkat:

* Az összes virtuális hálózatot le kell választania az ExpressRoute-kapcsolatcsoportról. Ha a művelet meghiúsul, ellenőrizze, hogy a virtuális hálózatok az áramkörhöz vannak-e kapcsolva.
* Ha a ExpressRoute Circuit Service Provider kiépítési állapota kiépítés vagy **kiépítve** **van,** akkor a szolgáltatóval kell együttműködni, hogy kiépítse az áramkört a saját oldalán. Továbbra is fenntartjuk az erőforrásokat és számlázunk, amíg a szolgáltató befejezi az áramkör kiépítését, és értesítést küld nekünk.
* Ha a szolgáltató kiépítte az áramkört (a szolgáltató kiépítési állapota **nincs kiépítve**), törölheti az áramkört. Ez leállítja a kapcsolatcsoport számlázását.

A ExpressRoute-áramkört a következő parancs futtatásával törölheti:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Következő lépések

Az áramkör létrehozása után győződjön meg arról, hogy a következő lépéseket hajtja végre:

* [Az ExpressRoute-áramkör útválasztásának létrehozása és módosítása](expressroute-howto-routing-arm.md)
* [A virtuális hálózat összekapcsolása a ExpressRoute-áramkörrel](expressroute-howto-linkvnet-arm.md)
