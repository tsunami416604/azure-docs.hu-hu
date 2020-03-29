---
title: 'Azure ExpressRoute: Kapcsolat módosítása: PowerShell:klasszikus'
description: Ez a cikk végigvezeti a lépéseket, hogy ellenőrizze az állapotát, frissítését, vagy törölje és megszüntetheti az ExpressRoute klasszikus üzembe helyezési modell áramkör.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cherylmc
ms.openlocfilehash: e421a534b04f74d2a2eb0bc06aeffa52684ae17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931978"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>ExpressRoute-kapcsolat módosítása PowerShell használatával (klasszikus)

> [!div class="op_single_selector"]
> * [Azure-portál](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sablon](expressroute-howto-circuit-resource-manager-template.md)
> * [Videó – Azure-portál](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ez a cikk végigvezeti a lépéseket, hogy ellenőrizze az állapotát, frissítését, vagy törölje és megszüntetheti az ExpressRoute klasszikus üzembe helyezési modell áramkör. Ez a cikk a klasszikus üzembehelyezési modellre vonatkozik.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="get-the-status-of-a-circuit"></a>Áramkör állapotának beírása

Ezeket az információkat bármikor lekérheti `Get-AzureCircuit` a parancsmag használatával. A hívás paraméterek nélkül az összes áramkört listázza.

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

Egy adott ExpressRoute-kapcsolatról úgy kaphat információt, hogy a szolgáltatáskulcsot adja át paraméterként a híváshoz.

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

Az összes paraméter részletes leírását a következő példa futtatásával kaphatja meg:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Kapcsolatcsoport módosítása

Az ExpressRoute-áramkör bizonyos tulajdonságait a kapcsolat befolyásolása nélkül módosíthatja.

A következő feladatokat teheti meg állásidő nélkül:

* ExpressRoute-alapú bővítmény engedélyezése vagy letiltása az ExpressRoute-kapcsolaton.
* Növelje az ExpressRoute-kapcsolat sávszélességét, feltéve, hogy a porton rendelkezésre áll kapacitás. Az áramkör sávszélességének visszaminősítése nem támogatott.
* Módosítsa a mérési terv forgalmi díjas adatok korlátlan adat. A mérési terv módosítása korlátlan adatról forgalmi díjas adatokra nem támogatott.
* Engedélyezheti és letilthatja *a Klasszikus műveletek engedélyezése .*

A korlátokkal és korlátozásokkal kapcsolatos további információkért tekintse meg az [ExpressRoute gyIK-et.](expressroute-faqs.md)

### <a name="enable-the-expressroute-premium-add-on"></a>Az ExpressRoute prémium bővítmény engedélyezése

Az ExpressRoute prémium szintű bővítményt a meglévő kapcsolatcsoporthoz a következő PowerShell-parancsmag használatával engedélyezheti:

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

A kapcsolatcsoport mostantól engedélyezve lesz az ExpressRoute prémium szintű bővítményfunkcióival. Amint a parancs sikeresen futott, a prémium szintű bővítmény képesség számlázása megkezdődik.

### <a name="disable-the-expressroute-premium-add-on"></a>Az ExpressRoute prémium bővítmény letiltása

> [!IMPORTANT]
> Ez a művelet sikertelen lehet, ha olyan erőforrásokat használ, amelyek nagyobbak, mint a szabványos kapcsolatcsoport megengedett.
>
>

#### <a name="considerations"></a>Megfontolandó szempontok

* Győződjön meg arról, hogy az áramkörhöz kapcsolódó virtuális hálózatok száma kevesebb, mint 10, mielőtt prémiumról standardra minősítene. Ha ezt nem teszi meg, a frissítési kérelem sikertelen lesz, és a díjszabást kiszámlázjuk.
* Le kell kapcsolnia a többi geopolitikai régió összes virtuális hálózatát. Ha nem, a frissítési kérelem sikertelen lesz, és a díjszabást számlázunk.
* Az útvonaltáblának 4000-nél kisebb útvonalnak kell lennie a privát társviszony-létesítéshez. Ha az útvonaltábla mérete nagyobb, mint 4000 útvonal, a BGP-munkamenet csökken, és nem lesz újra engedélyezve, amíg a meghirdetett előtagok száma 4000 alá nem csökken.

#### <a name="to-disable-the-premium-add-on"></a>A prémium bővítmény letiltása

A meglévő kapcsolatcsoport ExpressRoute prémium szintű bővítményét a következő PowerShell-parancsmag használatával tilthatja le:

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

### <a name="update-the-expressroute-circuit-bandwidth"></a>Az ExpressRoute-áramkör sávszélességének frissítése

Ellenőrizze az [ExpressRoute gyIK-ben](expressroute-faqs.md) a szolgáltató támogatott sávszélesség-beállításait. Bármilyen méretet választhat, amely nagyobb, mint a meglévő áramkör mérete, amíg a fizikai port (amelyen az áramkör jön létre) lehetővé teszi.

> [!IMPORTANT]
> Előfordulhat, hogy újra létre kell hoznia az ExpressRoute-áramkört, ha a meglévő porton nincs megfelelő kapacitás. Nem frissítheti a kapcsolatcsoport, ha nincs további kapacitás áll rendelkezésre az adott helyen.
>
> Az ExpressRoute-áramkörök sávszélessége megszakítás nélkül nem csökkenthető. A sávszélesség visszaminősítéséhez az ExpressRoute-kapcsolat bontásához, majd egy új ExpressRoute-kapcsolat újbóli kiépítéséhez szükséges.
>
>

#### <a name="resize-a-circuit"></a>Áramkör átméretezése

Miután eldöntötte, hogy milyen méretre van szüksége, a következő paranccsal átméretezheti az áramkört:

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

Miután a kapcsolatcsoport mérete a Microsoft oldalán történt, kapcsolatba kell lépnie a kapcsolódási szolgáltatóval, hogy frissítse az oldalukon lévő konfigurációkat, hogy megfeleljenek ennek a változásnak. Ettől a ponttól kezdve megkezdődik a frissített sávszélesség-beállítás számlázása.

Ha a következő hibaüzenet jelenik meg az áramkör sávszélességének növelésekor, az azt jelenti, hogy nincs elegendő sávszélesség azon a fizikai porton, ahol a meglévő áramkör létrejön. Törölnie kell ezt az áramkört, és létre kell hoznia egy új, a szükséges méretű áramkört.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Áramkör kitörlése és törlése

### <a name="considerations"></a>Megfontolandó szempontok

* A művelet sikeres működéséhez le kell választania az összes virtuális hálózatot az ExpressRoute-kapcsolatról. Ellenőrizze, hogy van-e olyan virtuális hálózat, amely kapcsolódik a kapcsolatcsoporthoz, ha ez a művelet sikertelen.
* Ha az ExpressRoute-kapcsolatszolgáltató létesítési állapota **Kiépítés** vagy **Kiépítve,** együtt kell működnie a szolgáltatóval az adott kapcsolat i. Továbbra is fenntartjuk az erőforrásokat, és kiszámlázunk Önnek, amíg a szolgáltató be nem fejezi a kapcsolatcsoport kiürítését, és nem értesíti kedélyünket.
* Ha a szolgáltató megszüntette a körcsoportot (a szolgáltató létesítési állapota **Nincs kiépítve),** akkor törölheti a csoportot. Ez leállítja a kapcsolatcsoport számlázását.

#### <a name="delete-a-circuit"></a>Áramkör törlése

Az ExpressRoute-áramköra a következő parancs futtatásával törölhető:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
