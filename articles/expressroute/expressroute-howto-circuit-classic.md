---
title: 'Azure ExpressRoute: áramkör módosítása: PowerShell: klasszikus'
description: Ez a cikk végigvezeti a lépéseken ellenőrizze az állapotot, update vagy delete és az ExpressRoute klasszikus üzemi modell kapcsolatcsoport megszüntetése.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cherylmc
ms.openlocfilehash: a2a3bdeb0ee454185c92c6e49a17bda7e4e5d5f0
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083577"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>PowerShell (klasszikus) használatával egy ExpressRoute-kapcsolatcsoport módosítása

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sablon](expressroute-howto-circuit-resource-manager-template.md)
> * [Videó – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ez a cikk végigvezeti a lépéseken ellenőrizze az állapotot, update vagy delete és az ExpressRoute klasszikus üzemi modell kapcsolatcsoport megszüntetése. Ez a cikk a klasszikus üzembehelyezési modellre vonatkozik.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Előkészületek

Telepítse az Azure Service Management (SM) PowerShell-modulok és az ExpressRoute-modul legújabb verzióit. Az Azure Cloudshellben-környezet nem használható SM-modulok futtatására.

1. Az Azure Service Management modul telepítéséhez kövesse a [Service Management modul telepítése](/powershell/azure/servicemanagement/install-azure-ps) című cikk utasításait. Ha az az vagy az RM modul már telepítve van, ne felejtse el használni a "-AllowClobber".
2. Importálja a telepített modulokat. A következő példa használatakor állítsa be úgy az elérési utat, hogy tükrözze a telepített PowerShell-modulok helyét.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Az Azure-fiókba való bejelentkezéshez nyissa meg a PowerShell-konzolt emelt szintű jogokkal, és kapcsolódjon a fiókjához. A következő példa segítségével csatlakozhat a Service Management modul használatával:

   ```powershell
   Add-AzureAccount
   ```

## <a name="get-the-status-of-a-circuit"></a>A kapcsolat állapotának lekérése

Ezt az információt bármikor használatával lekérheti a `Get-AzureCircuit` parancsmagot. Minden kapcsolatcsoportra paraméterek nélkül a következő hívással sorolja fel.

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

Egy adott ExpressRoute-kapcsolatcsoport a információkat szerezhet a kulcs paraméterként adja át a hívást.

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

A paraméterek a részletes leírását a következő példa futtatásával kaphat:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Kapcsolatcsoport módosítása

Egyes ExpressRoute-kapcsolatcsoport tulajdonságainak kapcsolat befolyásolása nélkül módosíthatja.

A következő feladatok leállás nélkül teheti meg:

* Engedélyezi vagy letiltja az ExpressRoute prémium bővítmény az ExpressRoute-kapcsolatcsoport esetében.
* Növelje az ExpressRoute-kapcsolatcsoport sávszélességét, feltéve, hogy kapacitás érhető el a porton. A sávszélesség csökkentése azonban a kapcsolat nem támogatott.
* Módosítsa a mérési terv díjas a korlátlan. Mérési csomag módosítása a korlátlan, a forgalmi díjas adatok nem támogatott.
* Engedélyezheti és letilthatja az *klasszikus működés engedélyezése*.

Tekintse meg a [ExpressRoute – gyakori kérdések](expressroute-faqs.md) korlátok és korlátozások további tájékoztatást.

### <a name="enable-the-expressroute-premium-add-on"></a>Az ExpressRoute prémium bővítmény engedélyezése

Az ExpressRoute prémium bővítmény a következő PowerShell-parancsmag segítségével engedélyezheti a meglévő kapcsolatcsoport:

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

A kapcsolatcsoport most már rendelkezik az ExpressRoute prémium bővítmény funkcióját. Amint a parancs sikeresen lefutott, a prémium szintű kiegészítő funkció használati díjának felszámolása kezdődik.

### <a name="disable-the-expressroute-premium-add-on"></a>Az ExpressRoute prémium bővítmény letiltása

> [!IMPORTANT]
> Ez a művelet sikertelen lehet, ha erőforrást, amely nagyobb, mint a megengedett a standard szintű kapcsolatcsoportot használ.
>
>

#### <a name="considerations"></a>Megfontolandó szempontok

* Győződjön meg arról, hogy a kapcsolatcsoporthoz kapcsolt virtuális hálózatok száma kevesebb mint 10 előtt standard Visszaléptetés a prémium szintű. Ha ezt nem teszi meg, a frissítési kérelem meghiúsul, és a számlázás a prémium szintű díjszabás.
* Más geopolitikai régiókban lévő összes virtuális hálózatot kell leválasztása. Ha nem, a frissítési kérelem meghiúsul, és számítunk fel díjat a prémium szintű díjszabás.
* Az útvonaltábla kisebb, mint 4000 útvonalak privát társviszony-létesítés kell lennie. Ha az útválasztási táblázat mérete nagyobb, mint 4000, a BGP-munkamenetben csökken, és nem fog újra engedélyezve, amíg a meghirdetett előtagok száma 4000 alá nem csökken.

#### <a name="to-disable-the-premium-add-on"></a>A premium bővítmény

Az ExpressRoute prémium bővítmény a következő PowerShell-parancsmag segítségével letilthatja a meglévő kapcsolatcsoport:

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

### <a name="update-the-expressroute-circuit-bandwidth"></a>Az ExpressRoute-kapcsolatcsoport sávszélességét frissítése

Ellenőrizze a [ExpressRoute – gyakori kérdések](expressroute-faqs.md) támogatott sávszélesség-lehetőségek a szolgáltató. Választhat, amely nagyobb, mint a meglévő kapcsolatcsoport méretétől, amennyiben a fizikai port (amelyeken a kapcsolatcsoport létrehozása) lehetővé teszi, hogy bármilyen méretű.

> [!IMPORTANT]
> Az ExpressRoute-kapcsolatcsoport hozza létre újra, ha nincs elegendő kapacitás a meglévő porton is. A kapcsolatcsoport nem frissíthető, ha nincsenek további kapacitás érhető el az adott helyhez.
>
> Megszakítás nélküli ExpressRoute-kapcsolatcsoport sávszélességét nem csökkenthető. Alacsonyabb verziójúra változtatása sávszélesség megköveteli, hogy az ExpressRoute-kapcsolatcsoport megszüntetése, és ezután építse ki újra a egy új ExpressRoute-kapcsolatcsoportot.
>
>

#### <a name="resize-a-circuit"></a>Méretezze át a kapcsolatcsoport

Miután eldöntötte, milyen méretű van szüksége, a következő parancs segítségével méretezze át a kapcsolatcsoport:

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

Ha a kapcsolatcsoport van a Microsoft oldalán lett méretezve, a kapcsolatszolgáltató frissíteni a saját oldalán, a módosítás megfelelő konfigurációk kapcsolatba kell lépnie. A számlázás kezdete ettől a frissített sávszélesség-beállítás.

Ha a kapcsolatcsoport sávszélessége növelése a következő hibát látja, ha az azt jelenti van nincs elegendő sávszélesség a fizikai port, ahol jön létre a meglévő expressroute-kapcsolatcsoporttal a left. A kapcsolatcsoport törölnie kell, majd hozzon létre új köröket a szükséges méret.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Megszüntetése és a Kapcsolatcsoportok törlése

### <a name="considerations"></a>Megfontolandó szempontok

* Az ExpressRoute-kapcsolatcsoport esetében ez a művelet sikeres végrehajtásához az összes virtuális hálózatot kell leválasztása. Ellenőrizze, hogy van-e minden olyan virtuális hálózatok, amelyek kapcsolódnak a kapcsolatcsoportot, ha ez a művelet sikertelen.
* Ha az ExpressRoute kapcsolatcsoport szolgáltató üzembe helyezési állapota **kiépítési** vagy **kiépített** -e, hogy azok oldalán a kapcsolatcsoport megszüntetése a szolgáltató. Továbbra is erőforrásokat tartalékolnia, és addig, amíg a szolgáltató befejeződött, a kapcsolatcsoport megszüntetése, és értesítést küld nekünk fel díjat.
* Ha a szolgáltató eltávolította a kapcsolatcsoportot (a szolgáltató kiépítési állapot értéke **nincs kiépítve**), megvan, törölheti a kapcsolatcsoportot. Ez leállítja a kapcsolatcsoport számlázását.

#### <a name="delete-a-circuit"></a>Kapcsolatcsoport törlése

Az ExpressRoute-kapcsolatcsoport a következő parancs futtatásával törölheti:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
