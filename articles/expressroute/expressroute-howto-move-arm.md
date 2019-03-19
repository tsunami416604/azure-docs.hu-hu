---
title: 'Helyezze át Kapcsolatcsoportok a klasszikusból a Resource Manager - ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Ezen a lapon azt ismerteti, miként helyezhetők át klasszikus expressroute-kapcsolatcsoporthoz a PowerShell-lel Resource Manager-alapú üzemi modellbe.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 7bd554896d739a567d04e7b978fba72960762805
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111361"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Az ExpressRoute-Kapcsolatcsoportok áthelyezése a klasszikusból Resource Manager-alapú üzemi modellbe PowerShell-lel

Szeretne használni egy ExpressRoute-kapcsolatcsoportot a klasszikus és Resource Manager üzembe helyezési modellel, a kapcsolatcsoport kell áthelyezni a Resource Manager-alapú üzemi modellbe. A következő szakaszok segítenek a kapcsolatcsoport áthelyezése a PowerShell használatával.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Győződjön meg arról, hogy telepítette a klasszikus és Az Azure PowerShell-modulok helyileg a számítógépen. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.
* Győződjön meg arról, hogy áttekintette a [Előfeltételek](expressroute-prerequisites.md), [útválasztási követelmények](expressroute-routing.md), és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.
* Tekintse át az adatokat a megadott [az ExpressRoute-Kapcsolatcsoportok áthelyezése klasszikusból Resource Manager](expressroute-move.md). Győződjön meg arról, hogy megértette a korlátok és korlátozások.
* Ellenőrizze, hogy a kapcsolatcsoport teljesen működőképes, a klasszikus üzemi modellben.
* Győződjön meg arról, hogy a Resource Manager-alapú üzemi modellben létrehozott erőforráscsoport.

## <a name="move-an-expressroute-circuit"></a>ExpressRoute-Kapcsolatcsoportok áthelyezése

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>1. lépés: A klasszikus üzemi modellből gyűjtse össze a kapcsolatcsoport részletei

Jelentkezzen be a klasszikus Azure-környezet, és a kulcs összegyűjtése.

1. Jelentkezzen be Azure-fiókjába.

   ```powershell
   Add-AzureAccount
   ```

2. Válassza ki a megfelelő Azure-előfizetést.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. A PowerShell-modulok importálása az Azure és az ExpressRoute számára.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Az alábbi parancsmag használatával minden az ExpressRoute-Kapcsolatcsoportok a szolgáltatás-kulcsok beolvasása. A kulcsok beolvasása után másolja a **Szolgáltatáskulcs** a kapcsolatcsoport, amely szeretné helyezni a Resource Manager-alapú üzemi modellbe.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>2. lépés: Jelentkezzen be, és hozzon létre egy erőforráscsoportot

Jelentkezzen be a Resource Manager-környezetben, és hozzon létre egy új erőforráscsoportot.

1. Jelentkezzen be az Azure Resource Manager-környezetben.

   ```powershell
   Connect-AzAccount
   ```

2. Válassza ki a megfelelő Azure-előfizetést.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Módosítsa a hozzon létre egy új erőforráscsoportot, ha még nem rendelkezik egy erőforráscsoportot az alábbi kódrészletet.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>3. lépés: Az ExpressRoute-kapcsolatcsoport áthelyezése a Resource Manager-alapú üzemi modellbe

Most már készen áll az ExpressRoute-kapcsolatcsoport áthelyezése a klasszikus üzemi modellből a Resource Manager-alapú üzemi modellbe. A folytatás előtt ellenőrizze a megadott információkat [az ExpressRoute-Kapcsolatcsoportok áthelyezése a klasszikusból a Resource Manager-alapú üzemi modellbe](expressroute-move.md).

A kapcsolatcsoport áthelyezése, módosíthatja, és futtassa az alábbi kódrészletet:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

Klasszikus módban ExpressRoute-kapcsolatcsoport nem rendelkezik a régió nem kötődik fogalmát. Azonban a Resource Managerben minden erőforrás kell le kell képezni egy Azure-régióban. A régió van megadva. a Move-AzExpressRouteCircuit parancsmagban technikailag lehet bármelyik régióban. Szervezeti okokból érdemes válasszon, amely szorosan jelöli a társviszony-létesítési helyszínen régiót.

> [!NOTE]
> Az Áthelyezés befejezése után, az új nevet, amely az előző parancsmag szerepel-e, oldja meg az erőforrás használható. A kapcsolatcsoport lényegében neve lesz.
> 

## <a name="modify-circuit-access"></a>Kapcsolatcsoport hozzáférés módosítása

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Mindkét ExpressRoute kapcsolatcsoport-hozzáférés engedélyezése

Helyezze át a klasszikus ExpressRoute-kapcsolatcsoport a Resource Manager-alapú üzemi modellre, akkor lehetővé teszik, hogy mindkét üzemi modellben. Mindkét üzemi modellben való hozzáférés engedélyezéséhez a következő parancsmagok futtatásához:

1. Ismerje meg a kapcsolatcsoport részleteket.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Állítsa be a "Klasszikus működés engedélyezése" TRUE.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Frissítse a kapcsolatcsoportot. Ez a művelet sikeres futtatása után lesz megtekintheti a kapcsolatcsoportot a klasszikus üzemi modellben.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Az ExpressRoute-kapcsolatcsoport részleteit a következő parancsmag futtatásával. Megtekintheti a felsorolt kulcsot kell lennie.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Az ExpressRoute-kapcsolatcsoportot a klasszikus üzemi modell parancsok használatával a klasszikus virtuális hálózatok és a Resource Manager-parancsok a Resource Manager virtuális hálózatok mutató hivatkozásokat is kezelni. A következő cikkekben talál hivatkozásokat az ExpressRoute-kapcsolatcsoport kezeléséhez nyújt segítséget:

    * [A virtuális hálózat összekapcsolása a Resource Manager-alapú üzemi modellben az ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md)
    * [A virtuális hálózat összekapcsolása az ExpressRoute-kapcsolatcsoportot a klasszikus üzemi modellben](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>A klasszikus üzemi modellben az ExpressRoute kapcsolatcsoport elérésének letiltása

A klasszikus üzemi modellben való hozzáférés letiltása a következő parancsmagok futtatásához.

1. Az ExpressRoute-kapcsolatcsoport részleteinek beolvasása.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Állítsa be a "Klasszikus működés engedélyezése" FALSE.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Frissítse a kapcsolatcsoportot. Miután a művelet sikeresen befejeződött, nem tudja megtekinteni a kapcsolatcsoportot a klasszikus üzemi modellben.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>További lépések

* [Útválasztás az ExpressRoute-kapcsolatcsoport létrehozása vagy módosítása](expressroute-howto-routing-arm.md)
* [A virtuális hálózat összekapcsolása az ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md)
