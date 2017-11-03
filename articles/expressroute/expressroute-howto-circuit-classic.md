---
title: "Létrehozásához és módosításához ExpressRoute-kapcsolatcsoportot: PowerShell: klasszikus Azure portál |} Microsoft Docs"
description: "Ez a cikk végigvezeti a létrehozásához, és a kiépítés ExpressRoute-kapcsolatcsoportot. Ez a cikk is bemutatja, hogyan ellenőrizze az állapot, update vagy delete, és a kapcsolatcsoport kiosztásának megszüntetése."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 93ddc2975db34053c6a776d1c3b931536f3f8ec7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell-classic"></a>Létrehozása és módosítása a powershellel (klasszikus) ExpressRoute-kapcsolatcsoportot
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video - Azure-portálon](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ez a cikk végigvezeti az Azure ExpressRoute-kapcsolatcsoportot létrehozása a PowerShell-parancsmagok és a klasszikus telepítési modell használatával. Ez a cikk is bemutatja, hogyan ellenőrizze az állapot, frissítési vagy törlési és kiosztásának megszüntetése ExpressRoute-kapcsolatcsoportot.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]


**Tudnivalók az Azure üzembe helyezési modelljeiről**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Előkészületek
### <a name="step-1-review-the-prerequisites-and-workflow-articles"></a>1. lépés Nézze át az Előfeltételek és a munkafolyamat cikkek
Győződjön meg arról, hogy tekintse át a [Előfeltételek](expressroute-prerequisites.md) és [munkafolyamatok](expressroute-workflows.md) konfigurálás elkezdése előtt.  

### <a name="step-2-install-the-latest-versions-of-the-azure-service-management-sm-powershell-modules"></a>2. lépés A legújabb verzióját az Azure Service Management (SM) PowerShell-modulok telepítése
Kövesse az utasításokat a [Ismerkedés az Azure PowerShell-parancsmagok](/powershell/azure/overview) kapcsolatos részletes útmutatás az Azure PowerShell-modulok használata a számítógép konfigurálása.

### <a name="step-3-log-in-to-your-azure-account-and-select-a-subscription"></a>3. lépés Jelentkezzen be az Azure-fiókjával, és válasszon egy előfizetést
1. Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

        Login-AzureRmAccount

2. Keresse meg a fiókot az előfizetésekben.

        Get-AzureRmSubscription

3. Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni szeretné.

        Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_id"

4. Győződjön meg arról, ha a kijelölt előfizetés-azonosító az alapértelmezett értéke.

        Get-AzureSubscription -default

## <a name="create-and-provision-an-expressroute-circuit"></a>Hozzon létre, és helyezze üzembe az ExpressRoute-kapcsolatcsoportot
### <a name="step-1-import-the-powershell-modules-for-expressroute"></a>1. lépés A PowerShell-modulok importálása az ExpressRoute
 Ha még nem tette meg, importálnia kell az Azure és az ExpressRoute modulok a PowerShell-munkamenetben megkezdéséhez az ExpressRoute-parancsmagok használatával. A modulok importálása a helyre, amely korábban telepítette őket a helyi számítógépen. A modulok telepítéséhez is használt módszernek a hely eltérhetnek az alábbi példában látható. Szükség esetén módosítsa a példa.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="step-2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. lépés A támogatott szolgáltatók, a helyek és a sávszélesség listájának lekérdezése
ExpressRoute-kapcsolatcsoportot létrehozni, meg kell támogatott kapcsolat szolgáltatókat, a helyek és a sávszélesség-beállítások listája.

A PowerShell-parancsmag `Get-AzureDedicatedCircuitServiceProvider` adja vissza ezt az információt fogja használni a későbbi lépésekben:

    Get-AzureDedicatedCircuitServiceProvider

Ellenőrizze, hogy ha a kapcsolat szolgáltatójánál nem szerepel-e. Jegyezze fel a következő adatokat, mert azt később szüksége expressroute-kapcsolatcsoporthoz létrehozásakor:

* Név
* PeeringLocations
* BandwidthsOffered

Most már készen áll az ExpressRoute-kapcsolatcsoportot létrehozni.         

### <a name="step-3-create-an-expressroute-circuit"></a>3. lépés ExpressRoute-kapcsolatcsoport létrehozása
A következő példa bemutatja, hogyan szilícium Valley egy 200 MB/s Equinix keresztül ExpressRoute-kapcsolatcsoportot létrehozni. Különböző szolgáltatók és más beállítások használata, amikor a kérést helyettesítse be ezt az információt.

> [!IMPORTANT]
> Az ExpressRoute-kapcsolatcsoportot abban a pillanatban a szolgáltatási kulcs kiadott lesz terhelve. Győződjön meg arról, hogy a művelet végrehajtása, ha a kapcsolat szolgáltatójánál kiépíteni a kapcsolat készen áll-e.
> 
> 

A következő egy példa egy kérelem egy új szolgáltatás kulcs:

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

Vagy, ha azt szeretné, a prémium szintű bővítmény ExpressRoute-kapcsolatcsoportot létrehozni, használja a következő példát. Tekintse meg a [ExpressRoute – gyakori kérdések](expressroute-faqs.md) a prémium szintű bővítmény kapcsolatos további részletekért.

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


A válasz a szolgáltatás kulcsot fogja tartalmazni. Részletes leírását, a paraméterek kaphat a következő futtatásával:

    get-help new-azurededicatedcircuit -detailed

### <a name="step-4-list-all-the-expressroute-circuits"></a>4. lépés Az ExpressRoute-Kapcsolatcsoportok felsorolása
Futtathatja a `Get-AzureDedicatedCircuit` parancs használatával beszerezheti az összes létrehozott ExpressRoute-Kapcsolatcsoportok listáját:

    Get-AzureDedicatedCircuit

A válasz az alábbi példához hasonló lesz:

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Ezt az információt bármikor használatával kérheti le a `Get-AzureDedicatedCircuit` parancsmag. A következő hívással paraméterek nélkül a Kapcsolatcsoportok sorolja fel. A szolgáltatás kulcs megjelenik a *ServiceKey* mező.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Részletes leírását, a paraméterek kaphat a következő futtatásával:

    get-help get-azurededicatedcircuit -detailed

### <a name="step-5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. lépés A szolgáltatás kulcs küldése a kapcsolat szolgáltatójánál történő üzembe helyezéséhez
*ServiceProviderProvisioningState* információt nyújt a jelenlegi állapotában a szolgáltatói oldalon kiépítés. *Állapot* állapotát biztosít a Microsoft oldalon. Kiépítés állapotok áramkör kapcsolatos további információkért tekintse meg a [munkafolyamatok](expressroute-workflows.md#expressroute-circuit-provisioning-states) cikk.

Amikor létrehoz egy új ExpressRoute-kapcsolatcsoportot, a kapcsolatcsoport lesz a következő állapotot okozta:

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


A kapcsolatcsoport halad át a következő állapotot, ha a kapcsolat szolgáltatójánál folyamatban van, lehetővé téve az Ön:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Egy ExpressRoute-kapcsolatcsoportot is használni tudja a következő állapotban kell lennie:

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="step-6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. lépés Ellenőrizze rendszeresen a kapcsolatcsoport kulcs állapotát és az állapot
Ez lehetővé teszi, hogy amikor a szolgáltató a kapcsolatcsoport engedélyezve van. A kapcsolat konfigurálása után *ServiceProviderProvisioningState* állapottal jelenik meg *kiépítve* a következő példában látható módon:

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="step-7-create-your-routing-configuration"></a>7. lépés Az útválasztó-konfiguráció létrehozása
Tekintse meg a [ExpressRoute-áramkör útválasztási konfigurációja (létrehozásához és módosításához a kapcsolatcsoport esetében)](expressroute-howto-routing-classic.md) cikk lépéseit.

> [!IMPORTANT]
> Ezek az utasítások csak a szolgáltatók által biztosított réteg 2 internetkapcsolati szolgáltatás használatával létrehozott kapcsolatok vonatkoznak. A szolgáltató által kezelt használata réteg (általában az IP VPN, például az MPLS) 3 szolgáltatások, a kapcsolat szolgáltatójánál konfigurálása és kezelése az Ön útválasztást.
> 
> 

### <a name="step-8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. lépés. Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal
A következő csatolja az ExpressRoute-kapcsolatcsoportot egy virtuális hálózatot. Tekintse meg [virtuális hálózatokhoz való csatolás ExpressRoute Kapcsolatcsoportok](expressroute-howto-linkvnet-classic.md) részletes útmutatásait. Ha egy virtuális hálózat létrehozása a klasszikus üzembe helyezési modellel az ExpressRoute című kell [hozzon létre egy virtuális hálózatot az ExpressRoute](expressroute-howto-vnet-portal-classic.md).

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Egy ExpressRoute-kapcsolatcsoport állapotának beolvasása
Ezt az információt bármikor használatával kérheti le a `Get-AzureCircuit` parancsmag. A következő hívással paraméterek nélkül a Kapcsolatcsoportok sorolja fel.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Egy adott ExpressRoute-kapcsolatcsoportot tájékoztatást kaphat úgy, hogy a szolgáltatás kulcs paraméterként a hívást.

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


Futtassa az alábbi példa is ki lehet részletes leírását, a Paraméterek:

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Egy ExpressRoute-kapcsolatcsoportot módosítása
ExpressRoute-kapcsolatcsoportot egyes tulajdonságait módosíthatja kapcsolat befolyásolása nélkül.

Állásidő nélkül a következőket teheti:

* Engedélyezi vagy letiltja az ExpressRoute-kapcsolatcsoportot prémium ExpressRoute bővítményt.
* Növelje a ExpressRoute-kapcsolatcsoportot sávszélességét, feltéve, hogy kapacitás érhető el a port. Vegye figyelembe, hogy a alacsonyabb verziójúra változtatása a sávszélességet a kapcsolat nem támogatott. 
* Díjköteles adatforgalom korlátlan adatokhoz a mérési terv módosítása Vegye figyelembe, hogy a mérési terv módosítása az korlátlan adatforgalom díjköteles adatok nem támogatott.
* Engedélyezheti és letilthatja *klasszikus műveletek engedélyezése*.

Tekintse meg a [ExpressRoute – gyakori kérdések](expressroute-faqs.md) korlátai és korlátozásai olvashat.

### <a name="to-enable-the-expressroute-premium-add-on"></a>A prémium szintű ExpressRoute-bővítmény engedélyezése
A prémium szintű ExpressRoute-bővítmény a következő PowerShell-parancsmag segítségével engedélyezheti a meglévő kapcsolat:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

A kapcsolatcsoport most lesz engedélyezett ExpressRoute prémium bővítmény funkciók. Vegye figyelembe, hogy azt megkezdődik, amint sikeresen futtatta a parancsot számlázást, a prémium szintű bővítmény képességhez.

### <a name="to-disable-the-expressroute-premium-add-on"></a>A prémium szintű ExpressRoute-bővítmény letiltása
> [!IMPORTANT]
> A művelet sikertelen lesz, amely nagyobb, mint mi a szabványos kör megengedett erőforrások használata.
> 
> 

#### <a name="considerations"></a>Megfontolandó szempontok

* Meg kell győződnie arról, hogy a kapcsolatcsoport kapcsolódó virtuális hálózatok száma kisebb, mint 10 szabvány prémiumról visszaminősítését előtt. Ha nem így tesz, a frissítési kérelem sikertelen lesz, és lesz számlázva a prémium szintű sebességet.
* Minden virtuális hálózat más geopolitikai régiókban kell választható. Ha nem így tesz, a frissítési kérelem sikertelen lesz, és lesz számlázva a prémium szintű sebességet.
* Az útvonaltábla a magánhálózati társviszony-létesítés kisebb, mint 4000 útvonalait kell lennie. Ha az útvonal tábla mérete nagyobb, mint 4000 útvonalakat, a BGP-munkamenetet eldobja, és nem fog újra engedélyezve, amíg a hirdetett számához nem 4000 éri el.

#### <a name="disable-the-premium-add-on"></a>A prémium szintű bővítmény letiltása
A prémium szintű ExpressRoute-bővítmény a következő PowerShell-parancsmag használatával kikapcsolhatja a meglévő expressroute:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Az ExpressRoute-kapcsolatcsoport sávszélessége frissítése
Ellenőrizze a [ExpressRoute – gyakori kérdések](expressroute-faqs.md) támogatott sávszélesség-beállítások a szolgáltatóhoz. Bármely mérete nagyobb, mint a meglévő expressroute mérete, amíg a fizikai port (amely a kapcsolatcsoport létre van hozva) lehetővé teszi, hogy ki tudja választani.

> [!IMPORTANT]
> Előfordulhat, hogy újra létrehozni az ExpressRoute-kapcsolatcsoport, ha nincs elég kapacitás a meglévő porton. A kapcsolat nem frissíthető, ha nincsenek további kapacitást érhető el az adott helyhez.
>
> Nem csökkenti a sávszélesség az ExpressRoute-kapcsolatcsoportot megszakítása nélkül. Alacsonyabb verziójúra változtatása a sávszélesség szükséges, hogy az ExpressRoute-kapcsolatcsoport kiosztásának megszüntetése és majd építenie az új ExpressRoute-kapcsolatcsoportot.
> 
> 

#### <a name="resize-a-circuit"></a>Expressroute-kapcsolatcsoporthoz átméretezése

Miután eldöntötte, hogy milyen méretű van szüksége, a következő paranccsal méretezze át a kapcsolatcsoport:

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

A kapcsolatcsoport fog rendelkezik lett méretű a Microsoft oldalon. A kapcsolat szolgáltatójánál konfigurációit, a módosítás megfelelő a kiszolgálóoldali frissítéséhez forduljon. Vegye figyelembe, hogy azt megkezdődik az ettől a frissített sávszélesség beállítás számlázási meg.

Ha megjelenik a következő hiba, amikor a kapcsolatcsoport sávszélessége növelése, az azt jelenti nincs nincs elegendő sávszélesség a fizikai porton, a meglévő expressroute létrehozási helyének bal. Hogy a kapcsolatcsoport törlése, és hozzon létre egy új kapcsolat van szüksége a mérete. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Megszüntetés és az ExpressRoute-kapcsolatcsoport törlése

### <a name="considerations"></a>Megfontolandó szempontok

* Az ExpressRoute-kapcsolatcsoport esetében ez a művelet sikeres legyen az összes virtuális hálózatot kell választható. Ellenőrizze, hogy van-e bármely virtuális hálózatot, amely a kapcsolatcsoport van csatolva, ha ez a művelet sikertelen.
* Ha az ExpressRoute-kapcsolatcsoport szolgáltatás szolgáltató üzembe helyezési állapota **kiépítési** vagy **kiépítve** kiosztásának megszüntetése a kapcsolatcsoport az oldalon, hogy a szolgáltató együttműködve. Folytatjuk erőforrásokat és kiszámlázni Önnek, amíg a szolgáltató befejeződött, a kapcsolat megszüntetés, és értesítést küld nekünk.
* Ha a szolgáltató rendelkezik platformelőfizetés a kapcsolatcsoport (üzembe helyezési állapota szolgáltató értéke **nincs kiépítve**) a kapcsolatcsoport törölhet. Ez a kapcsolat számlázási leáll.

#### <a name="delete-a-circuit"></a>A kapcsolat törlése

Az ExpressRoute-kapcsolatcsoport törlése a következő parancs futtatásával:

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Következő lépések
Miután létrehozta a kapcsolatcsoport, győződjön meg arról, hogy akkor tegye a következőket:

* [Létrehozásához és módosításához az ExpressRoute-kapcsolatcsoport esetében routing](expressroute-howto-routing-classic.md)
* [A virtuális hálózat csatolása az ExpressRoute-kapcsolatcsoportot](expressroute-howto-linkvnet-classic.md)

