---
title: "Virtuálisgép-méretezési csoport létrehozása Azure-sablonnal | Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre gyorsan virtuálisgép-méretezési csoportot egy Azure Resource Manager-sablonnal"
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
ms.date: 11/16/2017
ms.author: iainfou
ms.openlocfilehash: 614c7c82aabab212753529a21d7a770b7a02027e
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Virtuálisgép-méretezési csoport létrehozása az Azure CLI 2.0 használatával
A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A méretezési csoportban lévő virtuális gépek számát skálázhatja manuálisan, vagy megadhat automatikus skálázási szabályokat is az erőforrás-használat (például processzorhasználat, memóriaigény vagy hálózati forgalom) alapján. Ebben az első lépéseket bemutató cikkben egy virtuálisgép-méretezési csoportot hozunk létre egy Azure Resource Manager-sablon használatával. Méretezési csoportokat az [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md) és az [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) használatával, illetve az [Azure Portalon](virtual-machine-scale-sets-create-portal.md) is létrehozhat.


## <a name="overview-of-templates"></a>A sablonok áttekintése
Az Azure Resource Manager-sablonok segítségével egymáshoz kapcsolódó erőforráscsoportokat helyezhet üzembe. A sablonok a JavaScript Object Notation (JSON) formátumban vannak megírva, továbbá az alkalmazás teljes Azure-infrastruktúra környezetét meghatározzák. Egyetlen sablonban hozhatja létre a virtuálisgép-méretezési csoportot, telepítheti az alkalmazásokat és konfigurálhatja az automatikus méretezési szabályokat. Különféle változók és paraméterek segítségével a sablon többször is felhasználható meglévő méretezési csoportok frissítésére vagy újabbak létrehozására. A sablonokat az Azure Portal, az Azure CLI 2.0 vagy az Azure PowerShell használatával telepítheti, illetve folyamatos integrációs (CI) / folyamatos továbbítási (CD) folyamatokon keresztül hívhatja meg.

További információ a sablonokkal kapcsolatban: [Az Azure Resource Manager áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment).


## <a name="define-a-scale-set"></a>Méretezési csoport meghatározása
A sablonok határozzák meg az egyes erőforrástípusok konfigurációját. A virtuálisgép-méretezési csoport erőforrástípus az önálló virtuális gépekhez hasonló. A virtuálisgép-méretezési csoport erőforrástípus alapvető elemei a következők:

| Tulajdonság                     | A tulajdonság leírása                                  | Példa sablonérték                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | A létrehozandó Azure-erőforrástípus                            | Microsoft.Compute/virtualMachineScaleSets |
| név                         | A méretezési csoport neve                                       | myScaleSet                                |
| location                     | A méretezési csoport létrehozásának helye                     | USA keleti régiója                                   |
| sku.name                     | A méretezési csoport egyes példányainak virtuálisgép-mérete                  | Standard_A1                               |
| sku.capacity                 | Az először létrehozandó virtuálisgép-példányok száma           | 2                                         |
| upgradePolicy.mode           | A virtuálisgép-példányok frissítésének módja módosítás esetén              | Automatikus                                 |
| imageReference               | A virtuálisgép-példányokhoz használandó platform vagy egyéni rendszerkép | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | Az egyes virtuálisgép-példányokhoz tartozó név előtagja                     | myvmss                                    |
| osProfile.adminUsername      | Az egyes virtuálisgép-példányokhoz tartozó felhasználónév                        | azureuser                                 |
| osProfile.adminPassword      | Az egyes virtuálisgép-példányokhoz tartozó jelszó                        | P@ssw0rd!                                 |

 Az alábbi részlet az alapvető méretezésicsoport-erőforrás definícióját mutatja be egy sablonban. A minta rövidségének megőrzése érdekében a virtuális hálózati kártya (NIC) konfigurációját lehagytuk a képről. A méretezési csoport sablonjának testreszabásához módosíthatja a virtuális gépek méretét vagy kezdeti kapacitását, illetve használhat egy másik platformot vagy egy egyedi rendszerképet.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2016-04-30-preview",
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
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
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


## <a name="install-an-application"></a>Alkalmazások telepítése
A méretezési csoportok üzembe helyezésekor a virtuálisgép-bővítményekkel biztosíthatóak az üzembe helyezést követő konfigurációs és automatizálási feladatok, például az alkalmazások telepítése. A szkriptek az Azure Storage-ből vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Ha alkalmazni szeretné valamelyik bővítményt a méretezési csoportra, egészítse ki az erőforrás előző példáját az *extensionProfile* szakasszal. A bővítményprofil általában az alábbi tulajdonságokat határozza meg:

- Bővítmény típusa
- Bővítmény kiadója
- Bővítmény verziója
- A konfigurációs vagy telepítési szkriptek helye
- A virtuálisgép-példányokon végrehajtandó parancsok

Most lássunk két módot az alkalmazások bővítményekkel való telepítésére: egy Python-alkalmazás Linux rendszeren való telepítésére az egyéni szkriptbővítménnyel, valamint egy ASP.NET-alkalmazás Windows rendszeren való telepítésére a PowerShell DSC bővítménnyel.

### <a name="python-http-server-on-linux"></a>Python HTTP-kiszolgáló Linux rendszeren
A [Linux rendszeren futó Python HTTP-kiszolgáló](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) az egyéni szkriptbővítmény segítségével telepíti a [Bottle](http://bottlepy.org/docs/dev/) Python webes keretrendszert és egy egyszerű HTTP-kiszolgálót. 

A *fileUris*alatt két szkript van meghatározva - az *installserver.sh* és a *workserver.py*. A rendszer letölti a fájlokat a GitHubról, majd a *commandToExecute* meghatározza a `bash installserver.sh` fájlt a telepítendő és konfigurálandó alkalmazáshoz:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
          ],
          "commandToExecute": "bash installserver.sh"
        }
      }
    }
  ]
}
```

### <a name="aspnet-application-on-windows"></a>ASP.NET alkalmazás a Windows rendszeren
Az [ASP.NET alkalmazás a Windows rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) mintasablon a PowerShell DSC bővítmény segítségével telepít egy ASP.NET MVC alkalmazást, amely az IIS-ben fut. 

Letölt egy telepítő szkriptet a GitHubról – ez az *url* beállításban van definiálva. A bővítmény ezután futtatja az *InstallIIS* függvényt az *IISInstall.ps1* szkriptből a *function* és a *script* beállításoknak megfelelően. Maga az ASP.NET alkalmazás webes telepítési csomagként érhető el, amely szintén a GitHubról lesz letöltve a *WebDeployPackagePath* argumentumnak megfelelően:

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
A [Python HTTP-kiszolgáló Linux rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) vagy az [ASP.NET MVC alkalmazás a Windows rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) sablon üzembe helyezésének legegyszerűbb módja a GitHub információs fájljaiban található **Deploy to Azure** (Üzembe helyezés az Azure-ban) gomb használata.  A mintasablonokat a PowerShell vagy az Azure CLI segítségével is üzembe helyezheti.

### <a name="azure-cli-20"></a>Azure CLI 2.0
Az Azure CLI 2.0 használatával a következőképpen telepítheti a Python HTTP-kiszolgálót a Linux rendszeren:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Ha működés közben szeretné megtekinteni az alkalmazást, kérje le a terheléselosztó nyilvános IP-címét az [az network public-ip list](/cli/azure/network/public-ip#show) paranccsal a következők szerint:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Adja meg a terheléselosztó nyilvános IP-címét egy webböngészőben a következő formátumban: *http://<publicIpAddress>:9000/do_work*. A terheléselosztó az egyik virtuálisgép-példányra terjeszti a forgalmat, ahogy az a következő példában látható:

![Alapértelmezett weboldal az NGINX-ben](media/virtual-machine-scale-sets-create-template/running-python-app.png)


### <a name="azure-powershell"></a>Azure PowerShell
Az Azure PowerShell használatával a következőképp telepítheti az ASP.NET alkalmazást a Windows rendszeren:

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json
```

Ha működés közben szeretné megtekinteni az alkalmazást, kérje le a terheléselosztó nyilvános IP-címét a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) paranccsal a következők szerint:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Adja meg a terheléselosztó nyilvános IP-címét egy webböngészőben a következő formátumban: *http://<publicIpAddress>/MyApp*. A terheléselosztó az egyik virtuálisgép-példányra terjeszti a forgalmat, ahogy az a következő példában látható:

![Futó IIS-hely](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, az [az group delete](/cli/azure/group#delete) paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás a következők szerint:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>További lépések
