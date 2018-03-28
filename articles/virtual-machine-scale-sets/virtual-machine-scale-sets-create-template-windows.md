---
title: "Windowsos virtuálisgép-méretezési csoport létrehozása Azure-sablonnal | Microsoft Docs"
description: "Tudnivalók Windowsos virtuálisgép-méretezési csoport gyors létrehozásáról egy mintaalkalmazást üzembe helyező és az automatikus méretezési szabályokat konfiguráló Azure Resource Manager-sablonnal"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 1632411b0cfc2f8fa59f323436ee386e763a1ae0
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>Windowsos virtuálisgép-méretezési csoport létrehozása Azure-sablonnal
A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A méretezési csoportban lévő virtuális gépek számát skálázhatja manuálisan, vagy megadhat automatikus skálázási szabályokat is az erőforrás-használat (például processzorhasználat, memóriaigény vagy hálózati forgalom) alapján. Ebben az első lépéseket bemutató cikkben egy virtuálisgép-méretezési csoportot hozunk létre egy Azure Resource Manager-sablon használatával. Méretezési csoportokat az [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md) és az [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) használatával, illetve az [Azure Portalon](virtual-machine-scale-sets-create-portal.md) is létrehozhat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 4.4.1-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.


## <a name="define-a-scale-set-in-a-template"></a>Méretezési csoport meghatározása sablonban
Az Azure Resource Manager-sablonok segítségével egymáshoz kapcsolódó erőforráscsoportokat helyezhet üzembe. A sablonok a JavaScript Object Notation (JSON) formátumban vannak megírva, továbbá az alkalmazás teljes Azure-infrastruktúra környezetét meghatározzák. Egyetlen sablonban hozhatja létre a virtuálisgép-méretezési csoportot, telepítheti az alkalmazásokat és konfigurálhatja az automatikus méretezési szabályokat. Különféle változók és paraméterek segítségével a sablon többször is felhasználható meglévő méretezési csoportok frissítésére vagy újabbak létrehozására. A sablonokat az Azure Portal, az Azure CLI 2.0 vagy az Azure PowerShell használatával, illetve folyamatos integrációs (CI) / folyamatos továbbítási (CD) folyamatokon keresztül telepítheti.

További információ a sablonokkal kapcsolatban: [Az Azure Resource Manager áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment).

A sablonok határozzák meg az egyes erőforrástípusok konfigurációját. A virtuálisgép-méretezési csoport erőforrástípus az önálló virtuális gépekhez hasonló. A virtuálisgép-méretezési csoport erőforrástípus alapvető elemei a következők:

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

 Az alábbi példa az alapvető méretezésicsoport-erőforrás definícióját mutatja be. A méretezési csoport sablonjának testreszabásához módosíthatja a virtuális gépek méretét vagy kezdeti kapacitását, illetve használhat egy másik platformot vagy egy egyedi rendszerképet.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 A minta rövidségének megőrzése érdekében a virtuális hálózati kártya (NIC) konfigurációját lehagytuk a képről. A további összetevők, például a terheléselosztók szintén nem láthatók. [A cikk végén](#deploy-the-template) egy teljes méretezésicsoport-sablon található.


## <a name="install-an-application"></a>Alkalmazások telepítése
A méretezési csoportok üzembe helyezésekor a virtuálisgép-bővítményekkel biztosíthatóak az üzembe helyezést követő konfigurációs és automatizálási feladatok, például az alkalmazások telepítése. A szkriptek az Azure Storage-ből vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Ha alkalmazni szeretné valamelyik bővítményt a méretezési csoportra, egészítse ki az erőforrás előző példáját az *extensionProfile* szakasszal. A bővítményprofil általában az alábbi tulajdonságokat határozza meg:

- Bővítmény típusa
- Bővítmény kiadója
- Bővítmény verziója
- A konfigurációs vagy telepítési szkriptek helye
- A virtuálisgép-példányokon végrehajtandó parancsok

Az [ASP.NET alkalmazás a Windows rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) mintasablon a PowerShell DSC bővítmény segítségével telepít egy ASP.NET MVC alkalmazást, amely az IIS-ben fut. 

Letölt egy telepítő szkriptet a GitHubról – ez az *url* beállításban van definiálva. A bővítmény ezután futtatja az *InstallIIS* függvényt az *IISInstall.ps1* szkriptből a *function* és a *Script* beállításoknak megfelelően. Maga az ASP.NET alkalmazás webes telepítési csomagként érhető el, amely szintén a GitHubról lesz letöltve a *WebDeployPackagePath* argumentumnak megfelelően:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Powershell.DSC",
      "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.9",
        "autoUpgradeMinorVersion": true,
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése
Az [ASP.NET MVC alkalmazás a Windows rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) sablont a következő **Üzembe helyezés az Azure-ban** gombbal helyezheti üzembe. A gomb megnyomására megnyílik az Azure Portal, betöltődik a teljes sablon, és a rendszer néhány paraméter megadását kéri (például a méretezési csomag neve, a példányok száma és a rendszergazdai hitelesítő adatok).

[![Sablon üzembe helyezése az Azure-ban](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

Az Azure PowerShell-lel is telepítheti az ASP.NET alkalmazást Windows rendszeren a [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) parancsmaggal:

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzureRmVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

A kérdések megválaszolásával adja meg a méretezési csoport nevét és a virtuálisgép-példányok rendszergazdai hitelesítő adatait. A méretezési csoport létrehozása és a bővítmény alkalmazása az alkalmazás konfigurálásához 10–15 percet is igénybe vehet.


## <a name="test-your-sample-application"></a>A mintaalkalmazás tesztelése
Ha működés közben szeretné megtekinteni az alkalmazást, kérje le a terheléselosztó nyilvános IP-címét a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) paranccsal a következők szerint:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Adja meg a terheléselosztó nyilvános IP-címét egy webböngészőben *http://publicIpAddress/MyApp* formátumban. A terheléselosztó az egyik virtuálisgép-példányra terjeszti a forgalmat, ahogy az a következő példában látható:

![Futó IIS-hely](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás a következők szerint:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```


## <a name="next-steps"></a>További lépések
Ebben az első lépéseket bemutató cikkben egy Windows virtuálisgép-méretezési csoportot hoztunk létre egy Azure-sablonnal, és a PowerShell DSC bővítménnyel egy alapszintű ASP.NET alkalmazást telepítettünk a VM-példányokon. A nagyobb méretezhetőség és automatizáció érdekében bővítse méretezési csoportját az alábbi útmutatók segítségével:

- [Alkalmazások üzembe helyezése virtuálisgép-méretezési csoportokon](virtual-machine-scale-sets-deploy-app.md)
- Automatikus méretezés az [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md), az [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md), illetve az [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md) használatával.
- [Az operációs rendszer automatikus frissítéseinek használata a méretezési csoport virtuálisgép-példányain](virtual-machine-scale-sets-automatic-upgrade.md)
