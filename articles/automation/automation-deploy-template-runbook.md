---
title: Azure Resource Manager-sablon üzembe helyezése Azure Automation PowerShell-runbookban
description: Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy Azure Storage-ban tárolt Azure Resource Manager sablont egy PowerShell-runbook.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: PowerShell, runbook, JSON, Azure Automation
ms.openlocfilehash: dcb1f5919db71bb233f59cc5b5ba92648b8476d9
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714287"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>Azure Resource Manager-sablon üzembe helyezése egy PowerShell-runbook

[Azure Automation PowerShell-runbook](automation-first-runbook-textual-powershell.md) is írhat, amely Azure Resource [Management-sablonnal](../azure-resource-manager/resource-manager-create-first-template.md)telepít egy Azure-erőforrást. A sablon használata lehetővé teszi, hogy a Azure Automation és az Azure Storage segítségével automatizálja Azure-erőforrásai üzembe helyezését. A Resource Manager-sablonokat egy központi, biztonságos helyen, például az Azure Storage-ban kezelheti.

Ebben a cikkben egy PowerShell-runbook hozunk létre, amely egy [Azure Storage](../storage/common/storage-introduction.md) -ban tárolt Resource Manager-sablont használ egy új Azure Storage-fiók üzembe helyezéséhez.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik ilyennel, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) , vagy [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
* [Automation-fiók](automation-sec-configure-azure-runas-account.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez.  Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* A Resource Manager-sablon tárolására szolgáló [Azure Storage-fiók](../storage/common/storage-create-storage-account.md)
* Azure PowerShell telepítve a helyi gépre. A Azure PowerShell beszerzésével kapcsolatos információkért tekintse meg [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) című témakört.

## <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása

Ebben a példában egy Resource Manager-sablont használunk, amely egy új Azure Storage-fiókot helyez üzembe.

A szövegszerkesztőben másolja a következő szöveget:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Mentse a fájlt helyileg a **TemplateTest. JSON**néven.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>A Resource Manager-sablon mentése az Azure Storage-ban

Most a PowerShell használatával hozzon létre egy Azure Storage-fájlmegosztást, és töltse fel a **TemplateTest. JSON** fájlt.
A fájlmegosztás létrehozásával és a Azure Portal fájl feltöltésével kapcsolatos utasításokért lásd: Ismerkedés [Az Azure file Storage szolgáltatással Windows](../storage/files/storage-dotnet-how-to-use-files.md)rendszeren.

Indítsa el a PowerShellt a helyi gépen, és futtassa a következő parancsokat egy fájlmegosztás létrehozásához, majd töltse fel a Resource Manager-sablont az adott fájlmegosztást.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>A PowerShell-runbook parancsfájl létrehozása

Most létrehozunk egy PowerShell-szkriptet, amely beolvassa az **TemplateTest. JSON** fájlt az Azure Storage-ból, és üzembe helyezi a sablont egy új Azure Storage-fiók létrehozásához.

Illessze be a következő szöveget egy szövegszerkesztőbe:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)

# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Mentse a fájlt helyileg a **DeployTemplate. ps1**néven.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>A runbook importálása és közzététele a Azure Automation-fiókban

Most a PowerShell használatával importálja a runbook a Azure Automation-fiókjába, majd közzéteszi a runbook. További információ a runbook importálásáról és közzétételéről a Azure Portalban: [Runbookok kezelése Azure Automation](manage-runbooks.md).

Ha a **DeployTemplate. Ps1** eszközt PowerShell-runbook szeretné importálni az Automation-fiókjába, futtassa a következő PowerShell-parancsokat:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>A runbook indítása

Most kezdjük a runbook a [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0
) parancsmag meghívásával. További információ a runbook elindításáról a Azure Portalban: [Runbook elindítása Azure Automationban](automation-starting-a-runbook.md).

Futtassa a következő parancsokat a PowerShell-konzolon:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

A runbook fut, és a futtatásával megtekintheti az állapotát `$job.Status` .

A runbook lekéri a Resource Manager-sablont, és egy új Azure Storage-fiók üzembe helyezéséhez használja azt.
A következő parancs futtatásával láthatja, hogy az új Storage-fiók létrejött:

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Következő lépések

* [Az Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md)
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)
* [Runbook- és modulkatalógusok az Azure Automationhöz](automation-runbook-gallery.md)
* [Azure Gyorsindítás sablonok](https://azure.microsoft.com/resources/templates/)
* [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
