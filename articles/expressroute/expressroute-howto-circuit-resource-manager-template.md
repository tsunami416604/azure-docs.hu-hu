---
title: 'Azure ExpressRoute-sablon: ExpressRoute-áramkör létrehozása'
description: ExpressRoute-áramkör létrehozása, kiépítése, törlése és megszüntetése.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.openlocfilehash: 25ed38e72f5a21622a87e36ad811ffd66f6a4c90
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083516"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>ExpressRoute-kör létrehozása Azure Resource Manager sablon használatával

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sablon](expressroute-howto-circuit-resource-manager-template.md)
> * [Videó – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Megtudhatja, hogyan hozhat létre ExpressRoute áramkört egy Azure Resource Manager-sablonnak a Azure PowerShell használatával történő telepítésével. A Resource Manager-sablonok fejlesztésével kapcsolatos további információkért tekintse meg a [Resource Manager dokumentációját](/azure/azure-resource-manager/) és a [sablonra vonatkozó referenciát](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Előkészületek

* Tekintse át a [Előfeltételek](expressroute-prerequisites.md) és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.
* Győződjön meg arról, hogy új hálózati erőforrások létrehozásához szükséges engedélyek. Ha nem rendelkezik a megfelelő engedélyekkel, lépjen kapcsolatba a fiókadminisztrátorral.
* Is [videó](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) megtudhatja, a lépések megkezdése előtt.

## <a name="create"></a>Létrehozása és kiépítése az ExpressRoute-kapcsolatcsoport

Az Azure rövid útmutató [sablonjai](https://azure.microsoft.com/resources/templates/) a Resource Manager-sablonok megfelelő gyűjteményével rendelkeznek. Az egyik [meglévő sablon](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) segítségével ExpressRoute-áramkört hozhat létre.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

További kapcsolódó sablonok megjelenítéséhez kattintson [ide](https://azure.microsoft.com/resources/templates/?term=expressroute).

ExpressRoute-kör létrehozása sablon üzembe helyezésével:

1. Válassza a **kipróbálás** a következő kódrészletből lehetőséget, majd kövesse az utasításokat az Azure Cloud shellbe való bejelentkezéshez.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * Az **SKU-szint** meghatározza, hogy a ExpressRoute áramkör [helyi](expressroute-faqs.md#expressroute-local), standard vagy [prémium](expressroute-faqs.md#expressroute-premium)szintű-e. Megadhat *helyi*, *standard* vagy *prémium*szintűeket is.
   * Az **SKU termékcsalád** meghatározza a számlázási típust. Megadhat *Metereddata* forgalmi díjas csomag, és *Unlimiteddata* a korlátlan adatforgalmú. Módosíthatja a számlázási típusát a *Metereddata* való *Unlimiteddata*, de nem módosíthatja a típust *Unlimiteddata* való *Metereddata*. Egy *helyi* áramkör csak *Unlimiteddata* .
   * **Társviszony-létesítési helyszínen** a fizikai hely, ahol van Microsoft-társviszony van.

     > [!IMPORTANT]
     > Azt jelzi, hogy a társviszony-létesítési helye a [fizikai helyének](expressroute-locations.md) hol vannak a Microsoft társviszony. Ez a **nem** "Hely" tulajdonság, amely hivatkozik a földrajzi hely, ahol az Azure hálózati erőforrás-szolgáltató csatolva. Amíg nem áll(nak), tanácsos válassza ki a hálózati erőforrás-szolgáltató földrajzilag közeli, a kapcsolatcsoport társviszony-létesítési helye.

    Az erőforráscsoport neve a Service Bus-névtér neve, **RG** hozzáfűzéssel.

2. Válassza ki **másolási** másolása a PowerShell-parancsfájlt.
3. Kattintson a jobb gombbal a rendszerhéj-konzolra, majd válassza a **Beillesztés**lehetőséget.

Az Event hub létrehozása néhány percet vesz igénybe.

A Azure PowerShell a sablon üzembe helyezésére szolgál ebben az oktatóanyagban. A sablon egyéb telepítési módszereivel kapcsolatban lásd:

* [A Azure Portal használatával](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Az Azure CLI használatával](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [REST API használatával](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="delete"></a>A megszüntetés és a egy ExpressRoute-kapcsolatcsoport törlése

Az ExpressRoute-kapcsolatcsoport kiválasztásával törölheti a **törlése** ikonra. Tekintse meg az alábbi információkat:

* Az összes virtuális hálózatot le kell választania az ExpressRoute-kapcsolatcsoportról. Ha ez a művelet sikertelen, ellenőrizze-e minden olyan virtuális hálózatok kapcsolódnak-e a kapcsolatcsoportot.
* Ha az ExpressRoute kapcsolatcsoport szolgáltató üzembe helyezési állapota **kiépítési** vagy **kiépített** -e, hogy azok oldalán a kapcsolatcsoport megszüntetése a szolgáltató. Továbbra is erőforrásokat tartalékolnia, és addig, amíg a szolgáltató befejeződött, a kapcsolatcsoport megszüntetése, és értesítést küld nekünk fel díjat.
* Ha a szolgáltató eltávolította a kapcsolatcsoportot (a kiépítési állapota szolgáltató beállítása **nincs kiépítve**), törölheti a kapcsolatcsoportot. Ez leállítja a kapcsolatcsoport számlázását.

A ExpressRoute áramkörét a következő PowerShell-parancs futtatásával törölheti:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Következő lépések

Miután létrehozta a kapcsolatcsoportot, folytassa a következő lépések:

* [Útválasztás az ExpressRoute-kapcsolatcsoport létrehozása vagy módosítása](expressroute-howto-routing-portal-resource-manager.md)
* [A virtuális hálózat összekapcsolása az ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md)
