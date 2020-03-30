---
title: Studio (klasszikus) munkaterület telepítése az Azure Resource Managerrel
titleSuffix: ML Studio (classic) - Azure
description: Munkaterület üzembe helyezése az Azure Machine Learning Studio (klasszikus) számára az Azure Resource Manager-sablon használatával
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: 34333d4fe6e9b34a0c8b56cca8123f4ed93a917a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218119"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Az Azure Machine Learning Studio (klasszikus) munkaterület üzembe helyezése az Azure Resource Manager használatával

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Az Azure Resource Manager központi telepítési sablon használatával időt takaríthat meg azáltal, hogy méretezhető módon telepítheti az összekapcsolt összetevőket egy érvényesítési és újrapróbálkozási mechanizmussal. Az Azure Machine Learning Studio (klasszikus) munkaterületek beállításához például először konfigurálnia kell egy Azure-tárfiókot, majd telepítenie kell a munkaterületet. Képzelje el, hogy ezt manuálisan több száz munkaterülethez teszi. Egy egyszerűbb alternatíva egy Azure Resource Manager sablon használatával egy Studio (klasszikus) munkaterület és annak összes függőségét. Ez a cikk lépésről lépésre végigvezeti ezt a folyamatot. Az Azure Resource Manager nagyszerű áttekintést az [Azure Resource Manager áttekintése című témakörben talál.](../../azure-resource-manager/management/overview.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Lépésről lépésre: Machine Learning-munkaterület létrehozása
Létrehozunk egy Azure-erőforráscsoportot, majd üzembe helyezünk egy új Azure storage-fiókot és egy új Azure Machine Learning Studio (klasszikus) munkaterületet egy Resource Manager-sablon használatával. A központi telepítés befejezése után fontos információkat nyomtatunk ki a létrehozott munkaterületekről (az elsődleges kulcsról, a munkaterületazonosítóról és a munkaterület URL-címéről).

### <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager-sablon létrehozása

A Machine Learning Workspace használatához egy Azure-tárfiók szükséges a hozzá kapcsolt adatkészlet tárolásához.
A következő sablon az erőforráscsoport nevét használja a tárfiók nevének és a munkaterület nevének létrehozásához.  A munkaterület létrehozásakor a tárfiók nevét is használja tulajdonságként.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
Mentse ezt a sablont mlworkspace.json fájlként a c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Az erőforráscsoport telepítése a sablon alapján

* A PowerShell megnyitása
* Modulok telepítése az Azure Resource Manager hez és az Azure Service Managementhez

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   Ezek a lépések letöltik és telepítik a fennmaradó lépések végrehajtásához szükséges modulokat. Ezt csak egyszer kell elvégezni abban a környezetben, ahol a PowerShell-parancsokat futtatja.

* Hitelesítés az Azure-ban

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Ezt a lépést minden munkamenetnél meg kell ismételni. A hitelesítést követően meg kell jelennie az előfizetési adatoknak.

![Azure-fiók](./media/deploy-with-resource-manager-template/azuresubscription.png)

Most, hogy hozzáférünk az Azure-hoz, létrehozhatjuk az erőforráscsoportot.

* Erőforráscsoport létrehozása

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Ellenőrizze, hogy az erőforráscsoport megfelelően van-e kiépítve. **ProvisioningState** kell "Sikeres.".
Az erőforráscsoport nevét a sablon a tárfiók nevének létrehozásához használja. A tárfiók nevének 3 és 24 karakter közötti hosszúságúnak kell lennie, és csak számokat és kisbetűket használhat.

![Erőforráscsoport](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Az erőforráscsoport központi telepítésével telepítsen egy új Machine Learning-munkaterületet.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

A központi telepítés befejezése után egyszerű a telepített munkaterület tulajdonságainak elérése. Például elérheti az elsődleges kulcsjogkivonatot.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

A meglévő munkaterület tokenjének beolvasásának másik módja az Invoke-AzResourceAction parancs használata. Például az összes munkaterület elsődleges és másodlagos jogkivonatait is listázhatja.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
A munkaterület kiépítése után számos Azure Machine Learning Studio (klasszikus) feladatot automatizálhat az [Azure Machine Learning Studio (klasszikus) PowerShell-modul](https://aka.ms/amlps)használatával.

## <a name="next-steps"></a>További lépések

* További információ [az Azure Resource Manager-sablonok szerzői ről.](../../azure-resource-manager/templates/template-syntax.md)
* Tekintse meg az [Azure gyorsútmutató sablonok tárházát.](https://github.com/Azure/azure-quickstart-templates)
* Tekintse meg az [Azure Resource Managerről](https://channel9.msdn.com/Events/Ignite/2015/C9-39)szóló videót.
* Tekintse meg az [Erőforrás-kezelő sablonhivatkozási súgóját](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->
