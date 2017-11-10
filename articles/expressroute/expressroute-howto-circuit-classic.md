---
title: "Módosítsa az ExpressRoute-kapcsolatcsoportot: PowerShell: klasszikus Azure portál |} Microsoft Docs"
description: "Ez a cikk végigvezeti a ellenőrizze az állapot, update vagy delete és az ExpressRoute-klasszikus telepítési modell kapcsolatcsoportot kiosztásának megszüntetése."
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
ms.date: 11/08/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 457bb74fa15d31fecbf668038ac880cafb8a897d
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Módosítsa a powershellel (klasszikus) ExpressRoute-kapcsolatcsoportot

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video - Azure-portálon](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ez a cikk is bemutatja, hogyan ellenőrizze az állapot, frissítési vagy törlési és kiosztásának megszüntetése ExpressRoute-kapcsolatcsoportot.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembe helyezési modelljeiről**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Előkészületek

Az Azure Service Management (SM) PowerShell-modulok legújabb verziói kövesse az utasításokat a telepítés [Ismerkedés az Azure PowerShell-parancsmagok](/powershell/azure/overview) kapcsolatos részletes útmutatás a számítógép konfigurálása a Az Azure PowerShell-modulok.

## <a name="get-the-status-of-a-circuit"></a>A kapcsolat állapotának beolvasása

Ezt az információt bármikor használatával kérheti le a `Get-AzureCircuit` parancsmag. A következő hívással paraméterek nélkül a Kapcsolatcsoportok sorolja fel.

```powershell
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
```

Egy adott ExpressRoute-kapcsolatcsoportot tájékoztatást kaphat úgy, hogy a szolgáltatás kulcs paraméterként a hívást.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Futtassa az alábbi példa is ki lehet részletes leírását, a Paraméterek:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>A kapcsolat módosítása

ExpressRoute-kapcsolatcsoportot egyes tulajdonságait módosíthatja kapcsolat befolyásolása nélkül.

Állásidő nélkül a következő feladatokat is végezhet:

* Engedélyezi vagy letiltja az ExpressRoute-kapcsolatcsoportot prémium ExpressRoute bővítményt.
* Növelje a ExpressRoute-kapcsolatcsoportot sávszélességét, feltéve, hogy kapacitás érhető el a port. Alacsonyabb verziójúra változtatása a sávszélességet a kapcsolat nem támogatott. 
* Díjköteles adatforgalom korlátlan adatokhoz a mérési terv módosítása A mérési terv módosítása az korlátlan adatforgalom díjköteles adatok nem támogatott.
* Engedélyezheti és letilthatja *klasszikus műveletek engedélyezése*.

Tekintse meg a [ExpressRoute – gyakori kérdések](expressroute-faqs.md) korlátai és korlátozásai olvashat.

### <a name="enable-the-expressroute-premium-add-on"></a>A prémium szintű ExpressRoute-bővítmény engedélyezése

A prémium szintű ExpressRoute-bővítmény a következő PowerShell-parancsmag segítségével engedélyezheti a meglévő kapcsolat:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

A kapcsolatcsoport most lesz engedélyezett ExpressRoute prémium bővítmény funkciók. Amint a parancs sikeresen lefutott, a prémium szintű bővítmény képességhez számlázási kezdődik.

### <a name="disable-the-expressroute-premium-add-on"></a>A prémium szintű ExpressRoute-bővítmény letiltása

> [!IMPORTANT]
> A művelet sikertelen lesz, amely nagyobb, mint mi a szabványos kör megengedett erőforrások használata.
> 
> 

#### <a name="considerations"></a>Megfontolandó szempontok

* Ellenőrizze, hogy a kapcsolatcsoport kapcsolódó virtuális hálózatok száma kisebb, mint 10 szabvány prémiumról visszaminősítését előtt. Ha nem így tesz, a frissítési kérelem sikertelen lesz, és díját is felszámítjuk a prémium szintű sebességet.
* Minden virtuális hálózat más geopolitikai régiókban kell választható. Ha nem, a frissítési kérelem sikertelen lesz, és díját is felszámítjuk a prémium szintű sebességet.
* Az útvonaltábla a magánhálózati társviszony-létesítés kisebb, mint 4000 útvonalait kell lennie. Az útvonal tábla mérete nagyobb, mint 4000 útvonalakat, ha a BGP-munkamenetet esik, és nem fog újra engedélyezve, amíg a hirdetett számához nem 4000 éri el.

#### <a name="to-disable-the-premium-add-on"></a>A prémium szintű bővítmény

A prémium szintű ExpressRoute-bővítmény a következő PowerShell-parancsmag használatával kikapcsolhatja a meglévő expressroute:

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>Frissítés az ExpressRoute-kapcsolatcsoport sávszélessége

Ellenőrizze a [ExpressRoute – gyakori kérdések](expressroute-faqs.md) támogatott sávszélesség-beállítások a szolgáltatóhoz. Bármely mérete nagyobb, mint a meglévő expressroute mérete, amíg a fizikai port (amely a kapcsolatcsoport létre van hozva) lehetővé teszi, hogy ki tudja választani.

> [!IMPORTANT]
> Előfordulhat, hogy újra létrehozni az ExpressRoute-kapcsolatcsoport, ha nincs elég kapacitás a meglévő porton. A kapcsolat nem frissíthető, ha nincsenek további kapacitást érhető el az adott helyhez.
>
> Nem csökkenti a sávszélesség az ExpressRoute-kapcsolatcsoportot megszakítása nélkül. Alacsonyabb verziójúra változtatása a sávszélesség szükséges, hogy az ExpressRoute-kapcsolatcsoport kiosztásának megszüntetése és majd építenie az új ExpressRoute-kapcsolatcsoportot.
> 
> 

#### <a name="resize-a-circuit"></a>Expressroute-kapcsolatcsoporthoz átméretezése

Miután eldöntötte, hogy milyen méretű van szüksége, a következő paranccsal méretezze át a kapcsolatcsoport:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Miután a kapcsolatcsoport van a Microsoft oldalon lett méretű, forduljon a kapcsolat szolgáltatójánál, ez a módosítás megfelelő a ügyféloldali konfigurációjának frissítése. Számlázási kezdődik ettől a frissített sávszélesség-beállítás.

Ha megjelenik a következő hiba, amikor a kapcsolatcsoport sávszélessége növelése, az azt jelenti nincs nincs elegendő sávszélesség a fizikai porton, a meglévő expressroute létrehozási helyének bal. A kapcsolat törölnie kell, majd hozzon létre egy új kapcsolat van szüksége a mérete.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Kiosztásának megszüntetése és a kapcsolatok törlése

### <a name="considerations"></a>Megfontolandó szempontok

* Az ExpressRoute-kapcsolatcsoport esetében ez a művelet sikeres legyen az összes virtuális hálózatot kell választható. Ellenőrizze, hogy van-e bármely virtuális hálózatot, amely a kapcsolatcsoport van csatolva, ha ez a művelet sikertelen.
* Ha az ExpressRoute-kapcsolatcsoport szolgáltatás szolgáltató üzembe helyezési állapota **kiépítési** vagy **kiépítve** kiosztásának megszüntetése a kapcsolatcsoport az oldalon, hogy a szolgáltató együttműködve. A Folytatás kiszámlázni Önnek, amíg a szolgáltató befejeződött, a kapcsolat megszüntetés, és értesíti, és az erőforrásokat.
* Ha a szolgáltató rendelkezik platformelőfizetés a kapcsolatcsoport (üzembe helyezési állapota szolgáltató értéke **nincs kiépítve**), majd törölheti a kapcsolatcsoport. Ezzel leállítja a számlázási a kör.

#### <a name="delete-a-circuit"></a>A kapcsolat törlése

Az ExpressRoute-kapcsolatcsoport törlése a következő parancs futtatásával:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```