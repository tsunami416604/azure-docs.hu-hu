---
title: 'Azure ExpressRoute: Klasszikus áramkörök áthelyezése az Erőforrás-kezelőbe'
description: Ez a lap bemutatja, hogyan helyezhet át egy klasszikus áramkört az Erőforrás-kezelő központi telepítési modelljébe a PowerShell használatával.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.openlocfilehash: 4e49a3bc803733f5e78207fa3573c93395924d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74080172"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>ExpressRoute-áramkörök áthelyezése klasszikusról erőforrás-kezelői telepítési modellre a PowerShell használatával

Ha egy ExpressRoute-áramkört szeretne használni a klasszikus és az Erőforrás-kezelő központi telepítési modelljeihez is, át kell helyeznie az áramkört az Erőforrás-kezelő központi telepítési modelljébe. A következő szakaszok segítségével áthelyezheti az áramkört a PowerShell használatával.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Ellenőrizze, hogy a klasszikus és az Az Azure PowerShell-modulokat is helyileg telepítette-e a számítógépre. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.
* A konfiguráció megkezdése előtt ellenőrizze, hogy áttekintette-e az [előfeltételeket,](expressroute-prerequisites.md) [az útválasztási követelményeket](expressroute-routing.md)és [a munkafolyamatokat.](expressroute-workflows.md)
* Tekintse át az [ExpressRoute-kapcsolat áthelyezése klasszikusról erőforrás-kezelőre](expressroute-move.md)című csoportban megadott információkat. Győződjön meg arról, hogy teljes mértékben megértette a korlátokat és korlátozásokat.
* Ellenőrizze, hogy az áramkör teljes mértékben működőképes-e a klasszikus üzembe helyezési modellben.
* Győződjön meg arról, hogy rendelkezik egy erőforráscsoporttal, amely et az Erőforrás-kezelő telepítési modelljében hoztak létre.

## <a name="move-an-expressroute-circuit"></a>ExpressRoute-kapcsolat áthelyezése

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>1. lépés: Circuit részletek összegyűjtése a klasszikus üzembe helyezési modellből

Jelentkezzen be a klasszikus Azure-környezetbe, és gyűjtse össze a szolgáltatáskulcsot.

1. Jelentkezzen be Azure-fiókjába.

   ```powershell
   Add-AzureAccount
   ```

2. Válassza ki a megfelelő Azure-előfizetést.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Importálja a PowerShell-modulokat az Azure-hoz és az ExpressRoute-hoz.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Az alábbi parancsmag segítségével az összes ExpressRoute-kapcsolathoz lekéri a szolgáltatáskulcsokat. A kulcsok beolvasása után másolja a **szolgáltatáskulcs** áthelyezni a Resource Manager telepítési modell.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>2. lépés: Bejelentkezés és erőforráscsoport létrehozása

Jelentkezzen be az Erőforrás-kezelő környezetbe, és hozzon létre egy új erőforráscsoportot.

1. Jelentkezzen be az Azure Resource Manager környezetbe.

   ```powershell
   Connect-AzAccount
   ```

2. Válassza ki a megfelelő Azure-előfizetést.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Módosítsa az alábbi kódrészletet új erőforráscsoport létrehozásához, ha még nem rendelkezik erőforráscsoporttal.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>3. lépés: Az ExpressRoute-kapcsolat áthelyezése az Erőforrás-kezelő telepítési modelljére

Most már készen áll az ExpressRoute-kapcsolat a klasszikus üzembe helyezési modellről az Erőforrás-kezelő telepítési modellre. A folytatás előtt tekintse át az [ExpressRoute-kapcsolat áthelyezése a klasszikusról az Erőforrás-kezelő telepítési modelljére](expressroute-move.md)című csoportban megadott információkat.

Az áramkör áthelyezéséhez módosítsa és futtassa a következő kódrészletet:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

Klasszikus módban az ExpressRoute-áramkör nem rendelkezik a régióhoz való kötődés fogalmával. Azonban az Erőforrás-kezelőben minden erőforrást hozzá kell rendelve egy Azure-régióhoz. A Move-AzExpressRouteCircuit parancsmagban megadott régió technikailag bármely régió lehet. Szervezeti célokra érdemes lehet olyan régiót választani, amely szorosan képviseli a társviszony-létesítési helyet.

> [!NOTE]
> Az áthelyezés befejezése után az előző parancsmagban felsorolt új név lesz használva az erőforrás címzéséhez. Az áramkör tőszárra kerül.
> 

## <a name="modify-circuit-access"></a>Áramköri hozzáférés módosítása

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>ExpressRoute-kapcsolathozzáférés engedélyezése mindkét telepítési modellhez

Miután a klasszikus ExpressRoute-csoportot az Erőforrás-kezelő telepítési modelljébe helyezi át, mindkét telepítési modellhez hozzáférést biztosíthat. Futtassa a következő parancsmagokat, hogy mindkét telepítési modellhez hozzáférhessen:

1. Szerezd meg az áramkör részleteit.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Állítsa a "Klasszikus műveletek engedélyezése" értéket TRUE értékre.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Frissítse az áramkört. Miután ez a művelet sikeresen befejeződött, megtekintheti a klasszikus üzembe helyezési modell áramkörét.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Futtassa a következő parancsmast az ExpressRoute-kapcsolat részleteinek leéséhez. A szolgáltatáskulcsnak láthatónak kell lennie.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Most már kezelheti az ExpressRoute-kapcsolatcsoportra mutató kapcsolatokat a klasszikus központi telepítési modell parancsaival a klasszikus virtuális hálózatokhoz, valamint az Erőforrás-kezelő virtuális hálózatainak Erőforrás-kezelő parancsaival. Az alábbi cikkek segítséget nyújtanak az ExpressRoute-kapcsolatra mutató hivatkozások kezeléséhez:

    * [A virtuális hálózat összekapcsolása az ExpressRoute-kapcsolattal az Erőforrás-kezelő telepítési modelljében](expressroute-howto-linkvnet-arm.md)
    * [A virtuális hálózat összekapcsolása az ExpressRoute-kapcsolattal a klasszikus telepítési modellben](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Az ExpressRoute-kapcsolat hozzáférésének letiltása a klasszikus telepítési modellhez

Futtassa a következő parancsmagokat a klasszikus központi telepítési modellhez való hozzáférés letiltásához.

1. Az ExpressRoute-kapcsolat részletei.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Állítsa a "Klasszikus műveletek engedélyezése" értéket FALSE értékűre.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Frissítse az áramkört. A művelet sikeres befejezése után nem fogja tudni megtekinteni a klasszikus központi telepítési modell áramkörét.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>További lépések

* [Útválasztás létrehozása és módosítása az ExpressRoute-kapcsolathoz](expressroute-howto-routing-arm.md)
* [A virtuális hálózat összekapcsolása az ExpressRoute-áramkörrel](expressroute-howto-linkvnet-arm.md)
