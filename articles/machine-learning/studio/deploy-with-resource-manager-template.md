---
title: Az Azure Resource Manager Machine Learning-munkaterület központi telepítése |} Microsoft Docs
description: A munkaterület telepítése az Azure Machine Learning Azure Resource Manager-sablonnal
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 4955ac4d-ff99-4908-aa27-69b6bfcc8e85
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/05/2018
ms.openlocfilehash: 82d2316b3f72fbb0c5c3ee1ea9424afcc7661361
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833971"
---
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Machine Learning-munkaterület üzembe helyezése az Azure Resource Manager használatával
## <a name="introduction"></a>Bevezetés
A telepítési sablonnak adjon meg egy méretezhető módja, időt takaríthat meg Azure Resource Manager összekapcsolt érvényesség-összetevők telepítéséhez, majd próbálja megismételni a mechanizmus. Azure Machine Learning munkaterületek beállítása, például szüksége a munkaterület majd alkalmaznia kell konfigurálnia az Azure storage-fiók. Tegyük fel, így manuálisan munkaterületek több száz. Megkönnyíti a másik lehetőség az Azure Resource Manager-sablonok segítségével központi telepítése egy Azure Machine Learning munkaterülettel és annak függőségeit. Ez a cikk végigvezeti a részletes folyamat. Az Azure Resource Manager kiváló, áttekintés: [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Részletes útmutató: a Machine Learning-munkaterület létrehozása
Azt fogja hozzon létre egy Azure-erőforráscsoportot, majd a központi telepítése egy új Azure-tárfiókot és egy új Azure Machine Learning munkaterülettel Resource Manager-sablon használatával. Ha a telepítés befejeződött, azt fogja nyomtassa ki a munkaterületek (az elsődleges kulcs, a workspaceID és az URL-cím a munkaterületre) létrehozott fontos információkat.

### <a name="create-an-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon létrehozása
A Machine Learning-munkaterület kapcsolni az adatkészlet tárolásához Azure storage-fiók szükséges.
Az alábbi sablont létrehozni a tárfiók nevét az erőforráscsoport nevét és a munkaterület nevét használja.  Azt is használja a tárfiók neve tulajdonságként létrehozásakor a munkaterületen.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Ez a sablon mentése c:\temp\ mlworkspace.json fájlt.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Az erőforráscsoport a sablon telepítése
* A PowerShell megnyitása
* Az Azure Resource Manager és az Azure Service Management-modulok telepítése  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Ezeket a lépéseket töltse le és telepítse a további lépéseket befejezéséhez szükséges modulokat. Ez csak azért van szükség, egyszer a környezetben, ahol a PowerShell-parancsok végrehajtása.   

* Hitelesítés az Azure-bA  

```
# Authenticate (enter your credentials in the pop-up window)
Connect-AzureRmAccount
```
Ebben a lépésben meg kell ismételni, mindegyik munkamenethez. Ha hitelesítése megtörtént, az előfizetési adatok üzenetnek kell megjelennie.

![Azure-fiók][1]

Most, hogy Azure-hozzáférést, vannak, létrehozható az erőforráscsoportot.

* Hozzon létre egy erőforráscsoportot

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Győződjön meg arról, hogy az erőforráscsoport megfelelően lett kiépítve. **ProvisioningState** kell lennie "sikeresen befejeződött."
Az erőforráscsoport neve a sablon a tárfiók neve használt. A tárfiók nevét kell 3 és 24 karakter hosszúságúnak és kell használnia csak számokat és kisbetűket tartalmazhatnak.

![Erőforráscsoport][2]

* Egy új Machine Learning-munkaterület használata az erőforrás-csoport központi telepítése, központi telepítése.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Ha a telepítés befejeződött, akkor magától értetődő telepítette a munkaterület tulajdonságainak hozzáférést. Például végezheti el az elsődleges kulcs Token.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Egy másik meglévő munkaterület jogkivonatok beolvasása módja az Invoke-AzureRmResourceAction parancs használata. Például listázhatja az összes munkaterületek elsődleges és másodlagos jogkivonatok.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
A munkaterület üzembe helyezése után is automatizálhatja a sok Azure Machine Learning Studio feladatok a [Azure Machine Learning PowerShell-modul](http://aka.ms/amlps).

## <a name="next-steps"></a>További lépések
* További információ [Azure Resource Manager sablonok készítése](../../azure-resource-manager/resource-group-authoring-templates.md). 
* Tekintse meg a következő a [Azure gyors üzembe helyezés sablonok tárházba](https://github.com/Azure/azure-quickstart-templates). 
* Ezt a videót kapcsolatos [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 

<!--Image references-->
[1]: ./media/deploy-with-resource-manager-template/azuresubscription.png
[2]: ./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->
