---
title: Azure Resource Manager-sablon üzembe helyezése egy Azure Automation runbook
description: Azure Storage-ban tárolt Azure Resource Manager-sablon üzembe helyezése egy runbook
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: PowerShell, runbook, JSON, Azure Automation
ms.openlocfilehash: 922a4e8d98405de9e2b8420da4abf0e157011546
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850924"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Azure Resource Manager-sablon üzembe helyezése Azure Automation PowerShell-runbookban

[Azure Automation PowerShell-runbook](automation-first-runbook-textual-powershell.md) is írhat, amely Azure Resource [Management-sablonnal](../azure-resource-manager/resource-manager-create-first-template.md)telepít egy Azure-erőforrást.

Ennek segítségével automatizálhatja az Azure-erőforrások üzembe helyezését. A Resource Manager-sablonokat egy központi, biztonságos helyen, például az Azure Storage-ban kezelheti.

Ebben a cikkben egy PowerShell-runbook hozunk létre, amely egy [Azure Storage](../storage/common/storage-introduction.md) -ban tárolt Resource Manager-sablont használ egy új Azure Storage-fiók üzembe helyezéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő elemek szükségesek:

* Egy Azure-előfizetés. Ha még nem rendelkezik ilyennel, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) , vagy [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
* [Automation-fiók](automation-sec-configure-azure-runas-account.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez.  Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* A Resource Manager-sablon tárolására szolgáló [Azure Storage-fiók](../storage/common/storage-create-storage-account.md)
* Az Azure PowerShell telepítve van egy helyi gépen. A Azure PowerShell beszerzésével kapcsolatos információkért lásd: az [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) .

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

Mentse a fájlt helyileg `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>A Resource Manager-sablon mentése az Azure Storage-ban

Most a PowerShell használatával hozzon létre egy Azure Storage-fájlmegosztást, és töltse fel a `TemplateTest.json` fájlt.
A fájlmegosztás létrehozásával és a Azure Portal fájl feltöltésével kapcsolatos utasításokért lásd: Ismerkedés [Az Azure file Storage szolgáltatással Windows](../storage/files/storage-dotnet-how-to-use-files.md)rendszeren.

Indítsa el a PowerShellt a helyi gépen, és futtassa a következő parancsokat egy fájlmegosztás létrehozásához, majd töltse fel a Resource Manager-sablont az adott fájlmegosztást.

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

## <a name="create-the-powershell-runbook-script"></a>A PowerShell-runbook parancsfájl létrehozása

Most létrehozunk egy PowerShell-szkriptet, amely beolvassa az Azure Storage-ból `TemplateTest.json` fájlt, és üzembe helyezi a sablont egy új Azure Storage-fiók létrehozásához.

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

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>A runbook importálása és közzététele a Azure Automation-fiókban

Most a PowerShell használatával importálja a runbook a Azure Automation-fiókjába, majd közzéteszi a runbook.
További információ a runbook importálásáról és közzétételéről a Azure Portalban: [Runbookok kezelése Azure Automation](manage-runbooks.md).

Ha PowerShell-runbook szeretné importálni `DeployTemplate.ps1` az Automation-fiókjába, futtassa a következő PowerShell-parancsokat:

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

Most kezdjük a runbook a [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) parancsmag meghívásával.

További információ a runbook elindításáról a Azure Portalban: [Runbook elindítása Azure Automationban](automation-starting-a-runbook.md).

Futtassa a következő parancsokat a PowerShell-konzolon:

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

A runbook fut, és `$job.Status`futtatásával megtekintheti az állapotát.

A runbook lekéri a Resource Manager-sablont, és egy új Azure Storage-fiók üzembe helyezéséhez használja azt.
A következő parancs futtatásával láthatja, hogy az új Storage-fiók létrejött:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Összefoglalás

Ennyi az egész! Most már használhatja a Azure Automation és az Azure Storage-t, valamint a Resource Manager-sablonokat az összes Azure-erőforrás üzembe helyezéséhez.

## <a name="next-steps"></a>Következő lépések

* További információ a Resource Manager-sablonokról: [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md)
* Az Azure Storage használatának megkezdéséhez tekintse [meg az Azure Storage bemutatása](../storage/common/storage-introduction.md)című témakört.
* További hasznos Azure Automation runbookok a következő témakörben talál: [a Azure Automationhoz tartozó Runbook és modul-galériák](automation-runbook-gallery.md).
* További hasznos Resource Manager-sablonokat a következő témakörben talál: [Azure Gyorsindítás sablonok](https://azure.microsoft.com/resources/templates/)


