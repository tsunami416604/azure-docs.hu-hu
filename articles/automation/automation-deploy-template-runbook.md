---
title: Egy Azure Automation-runbook Azure Resource Manager sablon telepítése
description: Az Azure Resource Manager-sablon egy runbookból Azure Storage-ban tárolt központi telepítése
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: PowerShell, a runbook, json, az azure automation
ms.openlocfilehash: 489676736e0a3dcff463fae954f0250d90ba3d0f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Azure Resource Manager-sablon üzembe helyezése Azure Automation PowerShell-runbookban

Írhat egy [Azure Automation PowerShell-forgatókönyv](automation-first-runbook-textual-powershell.md) használatával egy Azure-erőforrás, amely telepít egy [Azure Resource Manager sablon](../azure-resource-manager/resource-manager-create-first-template.md).

Ezzel az Azure-erőforrások telepítési automatizálható. Akkor is fenntartható a Resource Manager-sablonok, például az Azure Storage központi, biztonságos helyen.

Ebben a témakörben egy Resource Manager-sablon tárolt használó PowerShell runbookot létrehozhatunk [Azure Storage](../storage/common/storage-introduction.md) központi telepítése egy új Azure Storage-fiók.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve <a href="/pricing/free-account/" target="_blank">[regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
* [Automation-fiók](automation-sec-configure-azure-runas-account.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez.  Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* [Az Azure Storage-fiók](../storage/common/storage-create-storage-account.md) a Resource Manager-sablon tárolására
* Az Azure Powershell telepítve a helyi számítógépen. Lásd: [telepítse és konfigurálja az Azure Powershellt](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) hogyan Azure PowerShell kérhet információt.

## <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása

A jelen példában használjuk egy Resource Manager-sablon, amely telepít egy új Azure Storage-fiók.

Egy szövegszerkesztőben másolja a következő szöveget:

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
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
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

Mentse a fájlt helyileg `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>A Resource Manager sablon mentése az Azure Storage

Most már PowerShell Azure Storage-fájlmegosztás létrehozása és feltöltése használjuk a `TemplateTest.json` fájlt.
A fájl létrehozásához útmutatást megosztani, és az Azure portálon-fájl feltöltése, a következő témakörben: [Ismerkedés az Azure File storage on Windows](../storage/files/storage-dotnet-how-to-use-files.md).

A helyi számítógépen indítsa el a Powershellt, és a következő parancsok futtatásával hozzon létre egy fájlmegosztást, és töltse fel a Resource Manager-sablon a fájlmegosztás.

```powershell
# Login to Azure
Connect-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>A PowerShell runbook parancsfájl létrehozása

Most létrehozhatunk egy PowerShell-parancsfájlt, amely lekérdezi a `TemplateTest.json` Azure Storage-ból fájlt, és telepíti a sablont egy új Azure Storage-fiók létrehozásához.

Egy szövegszerkesztőben illessze be a következő szöveget:

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
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Mentse a fájlt helyileg `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importálja és közzétenni a runbookot be az Azure Automation-fiók

Most azt a runbook importálása az Azure Automation-fiók a PowerShell segítségével, és tegye közzé a runbookot.
Importálja és runbook közzététele az Azure portálon kapcsolatos információkért lásd: [létrehozása vagy importálása az Azure Automationben runbook](automation-creating-importing-runbook.md).

Importálandó `DeployTemplate.ps1` az Automation-fiók egy PowerShell-forgatókönyv szerint, a következő PowerShell-parancsok futtatásához:

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
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>A runbook indítása

Most a runbook meghívásával először a [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook?view=azurermps-4.1.0) parancsmag.

Elindít egy forgatókönyvet az Azure-portálon kapcsolatos információkért lásd: [runbook elindítása az Azure Automationben](automation-starting-a-runbook.md).

A következő parancsokat a PowerShell-konzolon:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

A runbook fut, és annak állapotát futtatásával ellenőrizheti `$job.Status`.

A runbook lekérdezi a Resource Manager-sablon, és központi telepítése egy új Azure-tárfiókot használja.
Láthatja, hogy az új tárfiók létrejött-e a következő parancs futtatásával:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Összegzés

Ennyi az egész! Azure Automation és az Azure Storage és a Resource Manager sablonok segítségével most már telepítheti az összes Azure-erőforrások.

## <a name="next-steps"></a>További lépések

* Erőforrás-kezelő sablonokkal kapcsolatos további tudnivalókért lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md)
* Az Azure Storage első lépései, lásd: [Azure Storage bemutatása](../storage/common/storage-introduction.md).
* Egyéb hasznos Azure Automation-runbook talál [az Azure Automation forgatókönyv- és gyűjtemények](automation-runbook-gallery.md).
* Egyéb hasznos Resource Manager-sablonok, lásd: [Azure gyors üzembe helyezés sablonok](https://azure.microsoft.com/resources/templates/)

