---
title: 'Klasszikus ExpressRoute-Kapcsolatcsoportok áthelyezéséhez az erőforrás-kezelő: PowerShell: Azure |} Microsoft Docs'
description: Ez a lap ismerteti a klasszikus expressroute-kapcsolatcsoporthoz áthelyezése a Resource Manager üzembe helyezési modellel PowerShell használatával.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 37db218010288912bf75d00fd5868aab171aef71
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31592905"
---
# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model-using-powershell"></a>A klasszikus ExpressRoute-Kapcsolatcsoportok áthelyezése a Resource Manager üzembe helyezési modellel PowerShell használatával

Az ExpressRoute-kapcsolatcsoportot használja a klasszikus és Resource Manager üzembe helyezési modellel, át kell helyeznie a kapcsolatcsoport a Resource Manager üzembe helyezési modellben. Az alábbi szakaszok segítséget nyújtanak a kapcsolatcsoport áthelyezése a PowerShell használatával.

## <a name="before-you-begin"></a>Előkészületek

* Ellenőrizze, hogy rendelkezik-e a legújabb verzióját az Azure PowerShell-modulok (legalább 1.0-s verzió). További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.
* Győződjön meg arról, hogy tekintse át a [Előfeltételek](expressroute-prerequisites.md), [útválasztási követelmények](expressroute-routing.md), és [munkafolyamatok](expressroute-workflows.md) konfigurálás elkezdése előtt.
* Tekintse át az adatokat a megadott [klasszikus ExpressRoute-kapcsolatcsoportot áthelyezést az erőforrás-kezelő](expressroute-move.md). Győződjön meg arról, hogy megértette a korlátai és korlátozásai.
* Győződjön meg arról, hogy a kapcsolatcsoport teljesen működőképes, a klasszikus üzembe helyezési modellben.
* Győződjön meg arról, hogy rendelkezik-e a Resource Manager üzembe helyezési modellel létrehozott erőforráscsoport.

## <a name="move-an-expressroute-circuit"></a>Helyezze át az ExpressRoute-kapcsolatcsoportot

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>1. lépés: A klasszikus üzembe helyezési modellel áramkör részletek összegyűjtése

Jelentkezzen be a klasszikus Azure-környezetéhez, és a szolgáltatási kulcs összegyűjtése.

1. Jelentkezzen be az Azure-fiókjával.

  ```powershell
  Add-AzureAccount
  ```

2. Válassza ki a megfelelő Azure-előfizetést.

  ```powershell
  Select-AzureSubscription "<Enter Subscription Name here>"
  ```

3. A PowerShell modul importálása az Azure és az ExpressRoute.

  ```powershell
  Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
  Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'
  ```

4. Az alábbi parancsmag segítségével a szolgáltatás kulcsok lekérése az ExpressRoute-Kapcsolatcsoportok mindegyikét. A kulcsok beolvasása után másolja a **szolgáltatás kulcs** a kapcsolat a Resource Manager üzembe helyezési modellel áthelyezni kívánt.

  ```powershell
  Get-AzureDedicatedCircuit
  ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Jelentkezzen be a 2. lépés:, És hozzon létre egy erőforráscsoportot

Jelentkezzen be a Resource Manager-környezetben, és hozzon létre egy új erőforráscsoportot.

1. Jelentkezzen be az Azure Resource Manager-környezetben.

  ```powershell
  Connect-AzureRmAccount
  ```

2. Válassza ki a megfelelő Azure-előfizetést.

  ```powershell
  Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription
  ```

3. Hozzon létre egy új erőforráscsoportot, ha még nem rendelkezik egy erőforráscsoportot az alábbi részlet módosítása.

  ```powershell
  New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"
  ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>3. lépés: A Resource Manager üzembe helyezési modellel helyezze át az ExpressRoute-kapcsolatcsoportot

Most már készen áll a klasszikus telepítési modellből az ExpressRoute-kapcsolatcsoportot áthelyezése a Resource Manager üzembe helyezési modellben. A folytatás előtt ellenőrizze a foglalt információk [klasszikus ExpressRoute-kapcsolatcsoportot áthelyezését a Resource Manager üzembe helyezési modellel](expressroute-move.md).

A kapcsolatcsoport áthelyezéséhez módosíthatja, és futtassa a következő kódrészletet:

```powershell
Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

> [!NOTE]
> Az áthelyezés után, az új név szerepel-e az előző parancsmag használandó cím az erőforrás. A kapcsolatcsoport lényegében lehet átnevezni.
> 

## <a name="modify-circuit-access"></a>Módosítsa a kapcsolatcsoport hozzáférés

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Mindkét ExpressRoute-kapcsolatcsoport hozzáférés engedélyezése

Az a Resource Manager üzembe helyezési modellel, helyezze át a klasszikus ExpressRoute-kapcsolatcsoportot, akkor lehetővé teszik, hogy két üzembe helyezési modell. Két üzembe helyezési modell való hozzáférés engedélyezése a következő parancsmagok futtatásához:

1. Ezzel a kapcsolatcsoport adatokat.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
  ```

2. Állítsa be a "Klasszikus műveletek engedélyezése" TRUE.

  ```powershell
  $ckt.AllowClassicOperations = $true
  ```

3. A kapcsolatcsoport frissítése. Ez a művelet sikeresen befejeződött, miután lesz a kapcsolatcsoport megtekintése a klasszikus üzembe helyezési modellel.

  ```powershell
  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

4. Futtassa a következő parancsmagot, hogy megkapja az ExpressRoute-kapcsolatcsoport részleteit. A felsorolt Szolgáltatáskulcs látni kell lennie.

  ```powershell
  get-azurededicatedcircuit
  ```

5. Kezelheti a klasszikus üzembe helyezési modell parancsok használatát a hagyományos Vneteket, és a Resource Manager parancsok erőforrás-kezelő Vnetek ExpressRoute-kapcsolatcsoportot mutató hivatkozásokat tartalmaz. A következő cikkek az ExpressRoute-kapcsolatcsoport mutató hivatkozások kezeléséhez nyújt segítséget:

    * [A virtuális hálózat csatolása a Resource Manager üzembe helyezési modellben az ExpressRoute-kapcsolatcsoportot](expressroute-howto-linkvnet-arm.md)
    * [A virtuális hálózat csatolása a klasszikus üzembe helyezési modellben az ExpressRoute-kapcsolatcsoportot](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>A klasszikus üzembe helyezési modellel ExpressRoute-kapcsolatcsoport elérésének letiltása

A klasszikus üzembe helyezési modellben való hozzáférés letiltása a következő parancsmagok futtatásához.

1. Az ExpressRoute-kapcsolatcsoport részleteinek beolvasása.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
  ```

2. Állítsa be a "Klasszikus műveletek engedélyezése" FALSE.

  ```powershell
  $ckt.AllowClassicOperations = $false
  ```

3. A kapcsolatcsoport frissítése. Ez a művelet sikeresen befejeződött, után nem kezelhetők a kapcsolatcsoport megtekintheti a klasszikus üzembe helyezési modellben.

  ```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

## <a name="next-steps"></a>További lépések

* [Létrehozásához és módosításához az ExpressRoute-kapcsolatcsoport esetében routing](expressroute-howto-routing-arm.md)
* [A virtuális hálózat csatolása az ExpressRoute-kapcsolatcsoportot](expressroute-howto-linkvnet-arm.md)
