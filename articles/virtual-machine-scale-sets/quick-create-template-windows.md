---
title: Rövid útmutató – Windowsos virtuálisgép-méretezési csoport létrehozása Azure-sablonnal
description: Tudnivalók Windowsos virtuálisgép-méretezési csoport gyors létrehozásáról egy mintaalkalmazást üzembe helyező és az automatikus méretezési szabályokat konfiguráló Azure Resource Manager-sablonnal
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 03/27/2020
ms.author: jushiman
ms.openlocfilehash: 030479a02b33a92c3917ba112d99c9bcef4f7f32
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81010443"
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>Rövid útmutató: Windowsos virtuálisgép-méretezési csoport létrehozása Azure-sablonnal

A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A méretezési csoportban lévő virtuális gépek számát beállíthatja manuálisan, de automatikus méretezési szabályokat is megadhat az erőforrás-használat (például processzorhasználat, memóriaigény vagy hálózati forgalom) alapján. Egy Azure-terheléselosztó ezután elosztja a forgalmat a méretezési csoportban lévő virtuálisgép-példányok között. Ebben a rövid útmutatóban egy virtuálisgép-méretezési csoportot hozunk létre, és üzembe helyezünk egy mintaalkalmazást egy Azure Resource Manager-sablon használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Nincs.

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Az Azure Resource Manager-sablonok segítségével egymáshoz kapcsolódó erőforráscsoportokat helyezhet üzembe. Egyetlen sablonban hozhatja létre a virtuálisgép-méretezési csoportot, telepítheti az alkalmazásokat és konfigurálhatja az automatikus méretezési szabályokat. Különféle változók és paraméterek segítségével a sablon többször is felhasználható meglévő méretezési csoportok frissítésére vagy újabbak létrehozására. A sablonok a Azure Portal, az Azure CLI, a Azure PowerShell vagy a folyamatos integrációs/folyamatos kézbesítési (CI/CD) folyamatokon keresztül helyezhetők üzembe.

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/201-vmss-windows-webapp-dsc-autoscale/)származik.

:::code language="json" source="~/quickstart-templates/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json" range="1-397" highlight="236-325":::

Ezek az erőforrások a következő sablonokban vannak definiálva:

- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft. bepillantások/autoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings)

#### <a name="define-a-scale-set"></a>Méretezési csoport meghatározása

A kiemelt rész a méretezési csoport erőforrás-definíciója. Méretezési csoport sablon alapján való létrehozásához definiálnia kell a megfelelő erőforrásokat. A virtuálisgép-méretezési csoport erőforrástípus alapvető elemei a következők:

| Tulajdonság                     | A tulajdonság leírása                                  | Példa sablonérték                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | A létrehozandó Azure-erőforrástípus                            | Microsoft.Compute/virtualMachineScaleSets |
| név                         | A méretezési csoport neve                                       | myScaleSet                                |
| location                     | A méretezési csoport létrehozásának helye                     | USA keleti régiója                                   |
| sku.name                     | A méretezési csoport egyes példányainak virtuálisgép-mérete                  | Standard_A1                               |
| sku.capacity                 | Az először létrehozandó virtuálisgép-példányok száma           | 2                                         |
| upgradePolicy.mode           | A virtuálisgép-példányok frissítésének módja módosítás esetén              | Automatikus                                 |
| imageReference               | A virtuálisgép-példányokhoz használandó platform vagy egyéni rendszerkép | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | Az egyes virtuálisgép-példányokhoz tartozó név előtagja                     | myvmss                                    |
| osProfile.adminUsername      | Az egyes virtuálisgép-példányokhoz tartozó felhasználónév                        | azureuser                                 |
| osProfile.adminPassword      | Az egyes virtuálisgép-példányokhoz tartozó jelszó                        | P@ssw0rd!                                 |

A méretezési csoport sablonjának testreszabásához módosíthatja a virtuális gép méretét vagy kezdeti kapacitását. Egy másik lehetőség, hogy más platformot vagy egyéni rendszerképet használjon.

#### <a name="add-a-sample-application"></a>Mintaalkalmazás hozzáadása

A méretezési csoport teszteléséhez telepítsen egy alapszintű webalkalmazást. A méretezési csoportok üzembe helyezésekor a virtuálisgép-bővítményekkel biztosíthatóak az üzembe helyezést követő konfigurációs és automatizálási feladatok, például az alkalmazások telepítése. A szkriptek az Azure Storage-ből vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Ha alkalmazni szeretné valamelyik bővítményt a méretezési csoportra, egészítse ki az erőforrás előző példáját az *extensionProfile* szakasszal. A bővítményprofil általában az alábbi tulajdonságokat határozza meg:

- Bővítmény típusa
- Bővítmény kiadója
- Bővítmény verziója
- A konfigurációs vagy telepítési szkriptek helye
- A virtuálisgép-példányokon végrehajtandó parancsok

A sablon a PowerShell DSC bővítmény használatával telepít egy ASP.NET MVC-alkalmazást, amely az IIS-ben fut.

Letölt egy telepítő szkriptet a GitHubról – ez az *url* beállításban van definiálva. A bővítmény ezután futtatja az *InstallIIS* függvényt az *IISInstall.ps1* szkriptből a *function* és a *Script* beállításoknak megfelelően. Maga az ASP.NET alkalmazás webes telepítési csomagként érhető el, amely szintén a GitHubról lesz letöltve a *WebDeployPackagePath* argumentumnak megfelelően:

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablon üzembe helyezéséhez kattintson az **üzembe helyezés az Azure-** ban gombra. A gomb megnyomására megnyílik az Azure Portal, betöltődik a teljes sablon, és a rendszer néhány paraméter megadását kéri (például a méretezési csomag neve, a példányok száma és a rendszergazdai hitelesítő adatok).

[![Sablon üzembe helyezése az Azure-ban](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

Azure PowerShell használatával a Resource Manager-sablonokat is üzembe helyezheti:

```azurepowershell-interactive
# Create a resource group
New-AzResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateURI https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

A kérdések megválaszolásával adja meg a méretezési csoport nevét és a virtuálisgép-példányok rendszergazdai hitelesítő adatait. A méretezési csoport létrehozása és a bővítmény alkalmazása az alkalmazás konfigurálásához 10–15 percet is igénybe vehet.

## <a name="test-the-deployment"></a>Az üzemelő példány tesztelése

Ha ellenőrizni szeretné, hogyan működik a méretezési csoport, lépjen egy böngészőben a mintául szolgáló webalkalmazáshoz. Szerezze be a terheléselosztó nyilvános IP-címét a [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) a következőképpen:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Adja meg a terheléselosztó nyilvános IP-címét a webböngészőben a következő formátumban *: http:\//publicIpAddress/MyApp*. A terheléselosztó az egyik virtuálisgép-példányra terjeszti a forgalmat, ahogy az a következő példában látható:

![Futó IIS-hely](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) használatával eltávolíthatja az erőforráscsoportot, a méretezési csoportot. A `-Force` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat. A `-AsJob` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Windows rendszerű virtuálisgép-méretezési csoportot hoztunk létre egy Azure-sablonnal, és a PowerShell DSC bővítménnyel egy alapszintű ASP.NET-alkalmazást telepítettünk a VM-példányokon. Ha bővebb információra van szüksége, lépjen tovább az Azure-beli virtuálisgép-méretezési csoportok létrehozásáról és kezeléséről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Azure-beli virtuálisgép-méretezési csoportok létrehozása és kezelése](tutorial-create-and-manage-powershell.md)
