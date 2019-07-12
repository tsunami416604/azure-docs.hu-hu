---
title: 'ExpressRoute-kapcsolatcsoport – Resource Manager-sablon létrehozása: Azure | Microsoft Docs'
description: Hozzon létre, üzembe helyezése, törlése és ExpressRoute-kapcsolatcsoport megszüntetése.
services: expressroute;azure-resource-manager
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 07/05/2019
ms.author: cherylmc;ganesr
ms.openlocfilehash: bf56145d0a8cd3b01d0d74fcaf3348c1916cee5a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659683"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>ExpressRoute-kapcsolatcsoport létrehozása Azure Resource Manager-sablon használatával

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sablon](expressroute-howto-circuit-resource-manager-template.md)
> * [Videó – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ismerje meg, hogyan hozhat létre egy ExpressRoute-kapcsolatcsoport Azure PowerShell-lel az Azure Resource Manager-sablon üzembe helyezésével. A Resource Manager-sablonok fejlesztéséhez további információkért lásd: [Resource Manager dokumentációja](/azure/azure-resource-manager/) és a [sablonreferenciája](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Előkészületek

* Tekintse át a [Előfeltételek](expressroute-prerequisites.md) és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.
* Győződjön meg arról, hogy új hálózati erőforrások létrehozásához szükséges engedélyek. Ha nem rendelkezik a megfelelő engedélyekkel, lépjen kapcsolatba a fiókadminisztrátorral.
* Is [videó](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) megtudhatja, a lépések megkezdése előtt.

## <a name="create"></a>Létrehozása és kiépítése az ExpressRoute-kapcsolatcsoport

[Az Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) Resource Manager-sablon helyes gyűjteményét. Valamelyikét használja a [meglévő sablonok](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) hozhat létre egy ExpressRoute-kapcsolatcsoportot.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Több kapcsolódó sablonok megtekintéséhez válasszon [Itt](https://azure.microsoft.com/resources/templates/?term=expressroute).

ExpressRoute-kapcsolatcsoport létrehozása a sablon üzembe helyezésével:

1. Válassza ki **kipróbálás** az az alábbi kód letiltása, és kövesse az utasításokat követve jelentkezzen be az Azure Cloud shell.

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

   * **Réteg** határozza meg, hogy egy ExpressRoute-standard vagy az ExpressRoute prémium bővítmény engedélyezve van. Megadhat **Standard** beolvasni a standard Termékváltozat vagy **prémium** for a premium bővítményt.

   * **Társviszony-létesítési helyszínen** a fizikai hely, ahol van Microsoft-társviszony van.

     > [!IMPORTANT]
     > Azt jelzi, hogy a társviszony-létesítési helye a [fizikai helyének](expressroute-locations.md) hol vannak a Microsoft társviszony. Ez a **nem** "Hely" tulajdonság, amely hivatkozik a földrajzi hely, ahol az Azure hálózati erőforrás-szolgáltató csatolva. Amíg nem áll(nak), tanácsos válassza ki a hálózati erőforrás-szolgáltató földrajzilag közeli, a kapcsolatcsoport társviszony-létesítési helye.

    Az erőforráscsoport neve a service bus-névtér neve a **rg** hozzáfűzve.

2. Válassza ki **másolási** másolása a PowerShell-parancsfájlt.
3. Kattintson a jobb gombbal a rendszerhéj-konzolon, és válassza **beillesztési**.

Eseményközpont létrehozása néhány percet vesz igénybe.

Ebben az oktatóanyagban a sablon üzembe helyezése az Azure PowerShell segítségével. Egyéb sablon központi telepítési módszer lásd:

* [Az Azure portal használatával](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Azure CLI-vel](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [REST API-val](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="delete"></a>A megszüntetés és a egy ExpressRoute-kapcsolatcsoport törlése

Az ExpressRoute-kapcsolatcsoport kiválasztásával törölheti a **törlése** ikonra. Tekintse meg az alábbi információkat:

* Az összes virtuális hálózatot le kell választania az ExpressRoute-kapcsolatcsoportról. Ha ez a művelet sikertelen, ellenőrizze-e minden olyan virtuális hálózatok kapcsolódnak-e a kapcsolatcsoportot.
* Ha az ExpressRoute kapcsolatcsoport szolgáltató üzembe helyezési állapota **kiépítési** vagy **kiépített** -e, hogy azok oldalán a kapcsolatcsoport megszüntetése a szolgáltató. Továbbra is erőforrásokat tartalékolnia, és addig, amíg a szolgáltató befejeződött, a kapcsolatcsoport megszüntetése, és értesítést küld nekünk fel díjat.
* Ha a szolgáltató eltávolította a kapcsolatcsoportot (a kiépítési állapota szolgáltató beállítása **nincs kiépítve**), törölheti a kapcsolatcsoportot. Ez leállítja a kapcsolatcsoport számlázását.

Az ExpressRoute-kapcsolatcsoportot az alábbi PowerShell-parancs futtatásával törölheti:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>További lépések

Miután létrehozta a kapcsolatcsoportot, folytassa a következő lépések:

* [Útválasztás az ExpressRoute-kapcsolatcsoport létrehozása vagy módosítása](expressroute-howto-routing-portal-resource-manager.md)
* [A virtuális hálózat összekapcsolása az ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md)
