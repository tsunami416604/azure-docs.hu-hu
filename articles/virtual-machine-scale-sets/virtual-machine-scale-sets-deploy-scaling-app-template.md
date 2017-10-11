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
ms.date: 08/24/2017
ms.author: ryanwi
ms.openlocfilehash: 07883a33382cc660b043c99872312a9e77228253
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="deploy-an-autoscaling-app-using-a-template"></a>Automatikus méretezést végző alkalmazás üzembe helyezése sablon használatával

Az [Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) remek megoldást kínálnak egymáshoz kapcsolódó erőforráscsoportok üzembe helyezésére. Ez az oktatóanyag az [egyszerű méretezési csoport üzembe helyezését](virtual-machine-scale-sets-mvss-start.md) ismertető cikkre épít, és bemutatja, hogyan lehet üzembe helyezni egy automatikus méretezést végző egyszerű alkalmazást egy méretezési csoportban Azure Resource Manager-sablon használatával.  Az automatikus méretezést a PowerShell, a CLI vagy a portál használatával is beállíthatja. További információért lásd az [automatikus méretezést áttekintő](virtual-machine-scale-sets-autoscale-overview.md) témakört.

## <a name="two-quickstart-templates"></a>Két gyorsindítási sablon
Amikor üzembe helyez egy méretezési csoportot, egy [virtuálisgép-bővítmény](../virtual-machines/virtual-machines-windows-extensions-features.md) segítségével új szoftvereket telepíthet a platformlemezképre. A virtuálisgép-bővítmény egy kisméretű alkalmazás, amely üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosít az Azure-beli virtuális gépeken, például lehetővé teszi egy alkalmazás üzembe helyezését. Az [Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) sablonok két különböző mintasablont kínálnak, amelyek bemutatják, hogyan lehet üzembe helyezni egy automatikus méretezést végző alkalmazást egy méretezési csoportban virtuálisgép-bővítmények használatával.

### <a name="python-http-server-on-linux"></a>Python HTTP-kiszolgáló Linux rendszeren
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

### <a name="aspnet-mvc-application-on-windows"></a>ASP.NET MVC alkalmazás a Windows rendszeren
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

## <a name="deploy-the-template"></a>A sablon üzembe helyezése
A [Python HTTP-kiszolgáló Linux rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) vagy az [ASP.NET MVC alkalmazás a Windows rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) sablon üzembe helyezésének legegyszerűbb módja a GitHub információs fájljaiban található **Deploy to Azure** (Üzembe helyezés az Azure-ban) gomb használata.  A mintasablonokat a PowerShell vagy az Azure CLI segítségével is üzembe helyezheti.

### <a name="powershell"></a>PowerShell
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
 $templateFilePath = "template.json",

 [string]
 $parametersFilePath = "parameters.json"
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
$resourceProviders = @("microsoft.compute","microsoft.insights","microsoft.network");
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

### <a name="azure-cli"></a>Azure CLI
```azurecli
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'

# -e: immediately exit if any command has a non-zero exit status
# -o: prevents errors in a pipeline from being masked
# IFS new value is less likely to cause confusing bugs when looping arrays or arguments (e.g. $@)

usage() { echo "Usage: $0 -i <subscriptionId> -g <resourceGroupName> -n <deploymentName> -l <resourceGroupLocation>" 1>&2; exit 1; }

declare subscriptionId=""
declare resourceGroupName=""
declare deploymentName=""
declare resourceGroupLocation=""

# Initialize parameters specified from command line
while getopts ":i:g:n:l:" arg; do
    case "${arg}" in
        i)
            subscriptionId=${OPTARG}
            ;;
        g)
            resourceGroupName=${OPTARG}
            ;;
        n)
            deploymentName=${OPTARG}
            ;;
        l)
            resourceGroupLocation=${OPTARG}
            ;;
        esac
done
shift $((OPTIND-1))

#Prompt for parameters is some required parameters are missing
if [[ -z "$subscriptionId" ]]; then
    echo "Subscription Id:"
    read subscriptionId
    [[ "${subscriptionId:?}" ]]
fi

if [[ -z "$resourceGroupName" ]]; then
    echo "ResourceGroupName:"
    read resourceGroupName
    [[ "${resourceGroupName:?}" ]]
fi

if [[ -z "$deploymentName" ]]; then
    echo "DeploymentName:"
    read deploymentName
fi

if [[ -z "$resourceGroupLocation" ]]; then
    echo "Enter a location below to create a new resource group else skip this"
    echo "ResourceGroupLocation:"
    read resourceGroupLocation
fi

#templateFile Path - template file to be used
templateFilePath="template.json"

if [ ! -f "$templateFilePath" ]; then
    echo "$templateFilePath not found"
    exit 1
fi

#parameter file path
parametersFilePath="parameters.json"

if [ ! -f "$parametersFilePath" ]; then
    echo "$parametersFilePath not found"
    exit 1
fi

if [ -z "$subscriptionId" ] || [ -z "$resourceGroupName" ] || [ -z "$deploymentName" ]; then
    echo "Either one of subscriptionId, resourceGroupName, deploymentName is empty"
    usage
fi

#login to azure using your credentials
az account show 1> /dev/null

if [ $? != 0 ];
then
    az login
fi

#set the default subscription id
az account set --name $subscriptionId

set +e

#Check for existing RG
az group show $resourceGroupName 1> /dev/null

if [ $? != 0 ]; then
    echo "Resource group with name" $resourceGroupName "could not be found. Creating new resource group.."
    set -e
    (
        set -x
        az group create --name $resourceGroupName --location $resourceGroupLocation 1> /dev/null
    )
    else
    echo "Using existing resource group..."
fi

#Start deployment
echo "Starting deployment..."
(
    set -x
    az group deployment create --name $deploymentName --resource-group $resourceGroupName --template-file $templateFilePath --parameters $parametersFilePath
)

if [ $?  == 0 ];
 then
    echo "Template has been successfully deployed"
fi
```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
