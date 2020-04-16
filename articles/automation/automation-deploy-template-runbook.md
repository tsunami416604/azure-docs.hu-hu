---
title: Azure Resource Manager-sablon üzembe helyezése Azure Automation-runbookban
description: Az Azure Storage-ban tárolt Azure Resource Manager-sablon üzembe helyezése runbookból
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: powershell, runbook, json, azure automatizálás
ms.openlocfilehash: 2a6652c988eb77a1c5c7dbf800586b1c5fb756c4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392207"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Azure Resource Manager-sablon üzembe helyezése Azure Automation PowerShell-runbookban

Azure Automation [PowerShell-runbookot](automation-first-runbook-textual-powershell.md) írhat, amely egy Azure-erőforrás-kezelési sablon használatával telepít egy [Azure-erőforrás-erőforrást.](../azure-resource-manager/resource-manager-create-first-template.md)

Ezzel automatizálhatja az Azure-erőforrások üzembe helyezését. A Resource Manager-sablonokat egy központi, biztonságos helyen, például az Azure Storage-ban tarthatja karban.

Ebben a cikkben létrehozunk egy PowerShell-runbookot, amely az [Azure Storage-ban](../storage/common/storage-introduction.md) tárolt Resource Manager-sablont használ egy új Azure Storage-fiók üzembe helyezéséhez.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez a következő elemekre van szükség:

* Egy Azure-előfizetés. Ha még nem rendelkezik ilyen, [aktiválhatja az MSDN-előfizetői előnyöket,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) vagy [regisztrálhat egy ingyenes fiókra.](https://azure.microsoft.com/free/)
* [Automation-fiók](automation-sec-configure-azure-runas-account.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez.  Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* [Az Azure Storage-fiók,](../storage/common/storage-create-storage-account.md) amelyben az Erőforrás-kezelő sablon tárolandó
* Az Azure PowerShell egy helyi számítógépen telepítve. Az [Azure PowerShell-modul beszereléséről](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) az Azure PowerShell beszereléséről.

## <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása

Ebben a példában egy Erőforrás-kezelő sablont használunk, amely egy új Azure Storage-fiókot telepít.

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

Mentse a fájlt helyileg **TemplateTest.json néven.**

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Az Erőforrás-kezelő sablon mentése az Azure Storage szolgáltatásban

Most a PowerShell segítségével hozzon létre egy Azure Storage-fájlmegosztást, és töltse fel a **TemplateTest.json** fájlt.
A fájlmegosztás okait és fájlfeltöltését az Azure Portalon az [Azure File storage windowsos – lépések című](../storage/files/storage-dotnet-how-to-use-files.md)témakörben találja.

Indítsa el a PowerShellt a helyi számítógépen, és futtassa a következő parancsokat egy fájlmegosztás létrehozásához, és töltse fel az Erőforrás-kezelő sablont a fájlmegosztásra.

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

## <a name="create-the-powershell-runbook-script"></a>A PowerShell-runbook-parancsfájl létrehozása

Most hozzon létre egy PowerShell-parancsfájlt, amely leveszi a **TemplateTest.json** fájlt az Azure Storage-ból, és telepíti a sablont egy új Azure Storage-fiók létrehozásához.

A szövegszerkesztőbe illessze be a következő szöveget:

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

Mentse a fájlt helyileg **DeployTemplate.ps1 néven.**

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>A runbook importálása és közzététele az Azure Automation-fiókban

Most a PowerShell segítségével importálja a runbookot az Azure Automation-fiókba, majd tegye közzé a runbookot. A runbookok Azure-portálon való importálásáról és közzétételéről a [Runbookok kezelése az Azure Automationben című témakörben](manage-runbooks.md)talál további információt.

A **DeployTemplate.ps1 fájl** PowerShell-runbookként történő importálásához futtassa a következő PowerShell-parancsokat:

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

Most elindítjuk a runbookot a [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0
) parancsmag hívásával. A runbook oktatásáról az Azure Automation ben című [témakörben](automation-starting-a-runbook.md)talál további információt.

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

A runbook fut, és a futásával `$job.Status`ellenőrizheti az állapotát.

A runbook lefoglalja a Resource Manager-sablont, és egy új Azure Storage-fiók üzembe helyezéséhez használja.
Láthatja, hogy az új tárfiókot a következő parancs futtatásával hozták létre:

```powershell
Get-AzStorageAccount
```

## <a name="summary"></a>Összefoglalás

Ennyi az egész! Mostantól használhatja az Azure Automation és az Azure Storage erőforrás-kezelői sablonokkal az összes Azure-erőforrás üzembe helyezéséhez.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni az Erőforrás-kezelő sablonjairól, olvassa el az [Azure Resource Manager áttekintése című témakört.](../azure-resource-manager/management/overview.md)
* Az Azure Storage megismeréséhez olvassa el az Azure Storage bemutatása című [témakört.](../storage/common/storage-introduction.md)
* További hasznos Azure Automation-runbookok, lásd: [Runbook és modulgalériák az Azure Automation.](automation-runbook-gallery.md)
* További hasznos Erőforrás-kezelő sablonokat az [Azure gyorsútmutató-sablonok](https://azure.microsoft.com/resources/templates/)ban talál.
* A PowerShell-parancsmag referencia, lásd: [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
