---
title: "Alkalmazás üzembe helyezése Azure virtuálisgép-méretezési csoportban | Microsoft Docs"
description: "Elsajátíthatja, hogyan lehet üzembe helyezni egy automatikus méretezést végző egyszerű alkalmazást egy virtuálisgép-méretezési csoportban egy Azure Resource Manager-sablon használatával."
services: virtual-machine-scale-sets
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/4/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: b2b0bbe2c8e07a9ee2f21983262a948acb90d03d
ms.contentlocale: hu-hu
ms.lasthandoff: 06/09/2017


---

<a id="deploy-an-autoscaling-app-using-a-template" class="xliff"></a>

# Automatikus méretezést végző alkalmazás üzembe helyezése sablon használatával

Az [Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) remek megoldást kínálnak egymáshoz kapcsolódó erőforráscsoportok üzembe helyezésére. Ez az oktatóanyag az [egyszerű méretezési csoport üzembe helyezését](virtual-machine-scale-sets-mvss-start.md) ismertető cikkre épít, és bemutatja, hogyan lehet üzembe helyezni egy automatikus méretezést végző egyszerű alkalmazást egy méretezési csoportban Azure Resource Manager-sablon használatával.  Az automatikus méretezést a PowerShell, a CLI vagy a portál használatával is beállíthatja. További információért lásd az [automatikus méretezést áttekintő](virtual-machine-scale-sets-autoscale-overview.md) témakört.

<a id="two-quickstart-templates" class="xliff"></a>

## Két gyorsindítási sablon
Amikor üzembe helyez egy méretezési csoportot, egy [virtuálisgép-bővítmény](../virtual-machines/virtual-machines-windows-extensions-features.md) segítségével új szoftvereket telepíthet a platformlemezképre. A virtuálisgép-bővítmény egy kisméretű alkalmazás, amely üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosít az Azure-beli virtuális gépeken, például lehetővé teszi egy alkalmazás üzembe helyezését. Az [Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) sablonok két különböző mintasablont kínálnak, amelyek bemutatják, hogyan lehet üzembe helyezni egy automatikus méretezést végző alkalmazást egy méretezési csoportban virtuálisgép-bővítmények használatával.

<a id="python-http-server-on-linux" class="xliff"></a>

### Python HTTP-kiszolgáló Linux rendszeren
A [Python HTTP-kiszolgáló Linux rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) mintasablon egy automatikus méretezést végző egyszerű alkalmazást helyez üzembe, amely egy Linux rendszerű méretezési csoporton fut.  A sablon egy egyéni virtuálisgép-szkriptbővítmény segítségével a méretezési csoport minden virtuális gépén üzembe helyezi a [Bottle](http://bottlepy.org/docs/dev/) Python webes keretrendszert és egy egyszerű HTTP-kiszolgálót. A méretezési csoport akkor skálázódik fel, ha az átlagos processzorhasználat az összes virtuális gépen meghaladja a 60%-ot, és akkor skálázódik le, ha az átlagos processzorhasználat 30% alá esik.

A méretezésicsoport-erőforrás mellett az *azuredeploy.json* mintasablon a virtuális hálózat, nyilvános IP-cím, terheléselosztó és automatikus méretezési beállítások erőforrásokat is deklarálja.  Az erőforrások sablonban való létrehozásával kapcsolatos további információkért lásd [a Linux rendszerű méretezési csoporttal és az automatikus méretezéssel](virtual-machine-scale-sets-linux-autoscale.md) foglalkozó cikket.

Az *azuredeploy.json* sablonban a `extensionProfile` erőforrás `Microsoft.Compute/virtualMachineScaleSets` tulajdonsága egy egyéni szkriptbővítményt határoz meg. A `fileUris` a szkript(ek) helyét határozza meg. Ebben az esetben két fájlról van szó, az egyik a *workserver.py*, amely egy egyszerű HTTP-kiszolgálót határoz meg, és az *installserver.sh*, amely telepíti a Bottle keretrendszert és elindítja a HTTP-kiszolgálót. A `commandToExecute` a méretezési csoport üzembe helyezése után futtatandó parancsot határozza meg.

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "lapextension",
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

<a id="aspnet-mvc-application-on-windows" class="xliff"></a>

### ASP.NET MVC alkalmazás a Windows rendszeren
Az [ASP.NET MVC alkalmazás a Windows rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) mintasablon egy egyszerű ASP.NET MVC alkalmazást helyez üzembe, amely az IIS-ben, egy Windows rendszerű méretezési csoporton fut.  Az IIS-t és az MVC alkalmazást a sablon a [PowerShell Desired State Configuration (DSC)](virtual-machine-scale-sets-dsc.md) virtuálisgép-bővítmény használatával helyezi üzembe.  A méretezési csoport akkor skálázódik fel (egyszerre egy virtuálisgép-példányonként), ha a processzorhasználat 5 percen át 50% fölött van. 

A méretezésicsoport-erőforrás mellett az *azuredeploy.json* mintasablon a virtuális hálózat, nyilvános IP-cím, terheléselosztó és automatikus méretezési beállítások erőforrásokat is deklarálja. Ez a sablon az alkalmazásfrissítés módját is bemutatja.  Az erőforrások sablonban való létrehozásával kapcsolatos további információkért lásd [a Windows rendszerű méretezési csoporttal és az automatikus méretezéssel](virtual-machine-scale-sets-windows-autoscale.md) foglalkozó cikket.

Az *azuredeploy.json* sablonban a `extensionProfile` erőforrás `Microsoft.Compute/virtualMachineScaleSets` tulajdonsága egy [Desired State Configuration (DSC)](virtual-machine-scale-sets-dsc.md) bővítményt határoz meg, amely telepíti az IIS-t és egy WebDeploy csomagból származó alapértelmezett webalkalmazást.  A *DSC* mappában található *IISInstall.ps1* szkript telepíti az IIS-t a virtuális gépre.  Az MVC webalkalmazás a *WebDeploy* mappában található.  A telepítési szkript és a webalkalmazás elérési útja az *azuredeploy.parameters.json* fájl `powershelldscZip` és `webDeployPackage` paraméterében van meghatározva. 

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
                  "forceUpdateTag": "[parameters('powershelldscUpdateTagVersion')]",
                  "settings": {
                    "configuration": {
                      "url": "[variables('powershelldscZipFullPath')]",
                      "script": "IISInstall.ps1",
                      "function": "InstallIIS"
                    },
                    "configurationArguments": {
                      "nodeName": "localhost",
                      "WebDeployPackagePath": "[variables('webDeployPackageFullPath')]"
                    }
                  }
                }
              }
            ]
          }
```

<a id="deploy-the-template" class="xliff"></a>

## A sablon üzembe helyezése
A [Python HTTP-kiszolgáló Linux rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) vagy az [ASP.NET MVC alkalmazás a Windows rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) sablon üzembe helyezésének legegyszerűbb módja a GitHub információs fájljaiban található **Deploy to Azure** (Üzembe helyezés az Azure-ban) gomb használata.  A mintasablonokat a PowerShell vagy az Azure CLI segítségével is üzembe helyezheti.

<a id="powershell" class="xliff"></a>

### PowerShell
Másolja át a [Python HTTP-kiszolgáló Linux rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) vagy az [ASP.NET MVC alkalmazás a Windows rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) fájljait a GitHub-adattárból a helyi számítógép egyik mappájába.  Nyissa meg az *azuredeploy.parameters.json* fájlt, és frissítse a `vmssName`, `adminUsername` és `adminPassword` paraméterek alapértelmezett értékeit. Mentse a következő PowerShell-szkriptet a *deploy.ps1* fájlba, ugyanabba a mappába, amelyben az *azuredeploy.json* sablon található. A mintasablon üzembe helyezéséhez futtassa a *deploy.ps1* szkriptet egy PowerShell-parancsablakból.

```powershell
param(
 [Parameter(Mandatory=$True)]
 [string]
 $subscriptionId,

 [Parameter(Mandatory=$True)]
 [string]
 $resourceGroupName,

 [string]
 $resourceGroupLocation,

 [Parameter(Mandatory=$True)]
 [string]
 $deploymentName,

 [string]
 $templateFilePath = "azuredeploy.json",

 [string]
 $parametersFilePath = "azuredeploy.parameters.json"
)

<#
.SYNOPSIS
    Registers RPs
#>
Function RegisterRP {
    Param(
        [string]$ResourceProviderNamespace
    )

    Write-Host "Registering resource provider '$ResourceProviderNamespace'";
    Register-AzureRmResourceProvider -ProviderNamespace $ResourceProviderNamespace;
}

#******************************************************************************
# Script body
# Execution begins here
#******************************************************************************
$ErrorActionPreference = "Stop"

# sign in
Write-Host "Logging in...";
Login-AzureRmAccount;

# select subscription
Write-Host "Selecting subscription '$subscriptionId'";
Select-AzureRmSubscription -SubscriptionID $subscriptionId;

# Register RPs
$resourceProviders = @("microsoft.resources");
if($resourceProviders.length) {
    Write-Host "Registering resource providers"
    foreach($resourceProvider in $resourceProviders) {
        RegisterRP($resourceProvider);
    }
}

#Create or check for existing resource group
$resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
if(!$resourceGroup)
{
    Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
    if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
    }
    Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else{
    Write-Host "Using existing resource group '$resourceGroupName'";
}

# Start the deployment
Write-Host "Starting deployment...";
if(Test-Path $parametersFilePath) {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
} else {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
}
```

<a id="next-steps" class="xliff"></a>

## Következő lépések

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

