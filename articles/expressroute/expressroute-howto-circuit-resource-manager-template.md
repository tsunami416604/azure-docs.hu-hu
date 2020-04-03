---
title: 'Azure ExpressRoute-sablon: ExpressRoute-kapcsolat létrehozása'
description: ExpressRoute-kapcsolat létrehozása, kiépítése, törlése és megszüntetése.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: charwen
ms.openlocfilehash: ac2fb2f884feb0fbe6fbb1731ebe7c8bdad5114b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616507"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>ExpressRoute-kapcsolat létrehozása az Azure Resource Manager-sablon használatával

> [!div class="op_single_selector"]
> * [Azure-portál](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sablon](expressroute-howto-circuit-resource-manager-template.md)
> * [Videó – Azure-portál](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ismerje meg, hogyan hozhat létre expressroute-csoportot egy Azure Resource Manager-sablon azure PowerShell használatával történő üzembe helyezésével. Az Erőforrás-kezelő sablonjainak fejlesztéséről az [Erőforrás-kezelő dokumentációja](/azure/azure-resource-manager/) és a sablon hivatkozása című [témakörben olvashat bővebben.](/azure/templates/microsoft.network/expressroutecircuits)

## <a name="before-you-begin"></a>Előkészületek

* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és [a munkafolyamatokat.](expressroute-workflows.md)
* Győződjön meg arról, hogy rendelkezik engedéllyel új hálózati erőforrások létrehozásához. Ha nem rendelkezik a megfelelő engedélyekkel, forduljon a fiók rendszergazdájához.
* A lépések jobb megértése érdekében a videót a kezdés előtt [megtekintheti.](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute-kapcsolat létrehozása és kiépítése

[Az Azure gyorsindítási sablonjai](https://azure.microsoft.com/resources/templates/) a Resource Manager-sablon okán rendelkeznek. A [meglévő sablonok](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) egyikével hozzon létre egy ExpressRoute-áramkört.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

További kapcsolódó sablonok megtekintéséhez itt válassza [az itt](https://azure.microsoft.com/resources/templates/?term=expressroute)lehetőséget.

ExpressRoute-kapcsolat létrehozása sablon telepítésével:

1. Válassza **a Próbálja ki** a következő kódblokkból, majd kövesse az utasításokat az Azure Cloud rendszerhéjba való bejelentkezéshez.

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

   * **A Termékváltozat-szint** határozza meg, hogy az ExpressRoute-kör helyi, standard vagy prémium szintű.SKU tier determines whether an ExpressRoute circuit is [Local,](expressroute-faqs.md#expressroute-local)Standard vagy [Premium.](expressroute-faqs.md#expressroute-premium) Megadhatja *a Helyi*, *Standard vagy *Prémium*értéket.
   * **Termékváltozat család** határozza meg a számlázási típus. Megadhatja *metereddata* egy forgalmi díjas adatcsomag és *Unlimiteddata* korlátlan adatcsomag. A számlázási típust *Metereddata-ról* *Unlimiteddata-ra módosíthatja,* de a típust nem módosíthatja *Korlátlan adatról* *Metereddata-ra.* A *helyi* áramkör csak *Korlátlan adat.*
   * **A társviszony-létesítési hely** az a fizikai hely, ahol a Microsofttal társviszony-létesít.

     > [!IMPORTANT]
     > A társviszony-létesítési hely azt a [fizikai helyet](expressroute-locations.md) jelzi, ahol a Microsofttal társviszonyban áll. Ez **nem** kapcsolódik a "Location" tulajdonsághoz, amely az Azure hálózati erőforrás-szolgáltató földrajzi helyére hivatkozik. Bár nem kapcsolódnak egymáshoz, célszerű a hálózati erőforrás-szolgáltatót földrajzilag a kapcsolatlétesítési hely közelében választani.

    Az erőforráscsoport neve a szolgáltatásbusz névtérneve **rg** csatolt.

2. Válassza **a Másolás lehetőséget** a PowerShell-parancsfájl másolásához.
3. Kattintson a jobb gombbal a rendszerhéj-konzolra, és válassza **a Beillesztés parancsot.**

Az eseményközpont létrehozása néhány percet vesz igénybe.

Az Azure PowerShell ebben az oktatóanyagban a sablon üzembe helyezéséhez szolgál. A sablonok további telepítési módszereiről a következő témakörben olvashat:

* [Az Azure Portal használatával.](../azure-resource-manager/templates/deploy-portal.md)
* [Az Azure CLI használatával.](../azure-resource-manager/templates/deploy-cli.md)
* [Rest API használatával.](../azure-resource-manager/templates/deploy-rest.md)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute-kapcsolatcsoport megszüntetése és törlése

Az ExpressRoute-kapcsolat a **törlés** ikon kiválasztásával törölheti az ExpressRoute-áramkört. Tekintse meg az alábbi információkat:

* Az összes virtuális hálózatot le kell választania az ExpressRoute-kapcsolatcsoportról. Ha ez a művelet sikertelen, ellenőrizze, hogy vannak-e virtuális hálózatok a kapcsolatcsoporthoz.
* Ha az ExpressRoute-kapcsolatszolgáltató létesítési állapota **Kiépítés** vagy **Kiépítve,** együtt kell működnie a szolgáltatóval az adott kapcsolat i. Továbbra is fenntartjuk az erőforrásokat, és kiszámlázunk Önnek, amíg a szolgáltató be nem fejezi a kapcsolatcsoport kiürítését, és nem értesíti kedélyünket.
* Ha a szolgáltató megszüntette a körcsoportot (a szolgáltató létesítési állapota **Nincs kiépítve)** értékre van állítva, törölheti a csoportot. Ez leállítja a kapcsolatcsoport számlázását.

Az ExpressRoute-kapcsolat a következő PowerShell-parancs futtatásával törölheti:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>További lépések

A kapcsolatcsoport létrehozása után folytassa a következő lépésekkel:

* [Útválasztás létrehozása és módosítása az ExpressRoute-kapcsolathoz](expressroute-howto-routing-portal-resource-manager.md)
* [A virtuális hálózat összekapcsolása az ExpressRoute-áramkörrel](expressroute-howto-linkvnet-arm.md)
