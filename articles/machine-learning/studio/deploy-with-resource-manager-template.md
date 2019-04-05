---
title: Studio-munkaterület az Azure Resource Manager üzembe helyezése
titleSuffix: Azure Machine Learning Studio
description: Az Azure Machine Learning Studiót az Azure Resource Manager-sablon üzembe helyezése a munkaterület
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: 91413aa461261824782717ae4edacc2757ad5405
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59048724"
---
# <a name="deploy-azure-machine-learning-studio-workspace-using-azure-resource-manager"></a>Az Azure Machine Learning Studio-munkaterület használata az Azure Resource Manager üzembe helyezése

Az egy Azure Resource Manager központi telepítési sablont, így Ön egy skálázható módon, hogy Ön időt takarít az érvényesítést összekapcsolt összetevők üzembe helyezése és újrapróbálkozási mechanizmus. Az Azure Machine Learning Studio-munkaterületek beállításával kapcsolatban például szeretne konfigurálja egy Azure storage-fiókot, majd üzembe helyezése a munkaterületen. Tegyük fel, így manuálisan munkaterületek több száz. Egyszerűbb a másik lehetőség az Azure Resource Manager-sablon segítségével üzembe helyezése a Studio-munkaterülethez, és minden függőségét. Ez a cikk végigvezeti a részletes folyamat. Az Azure Resource Manager, nagyszerű áttekintése: [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Részletes útmutató: Machine Learning-munkaterület létrehozása
Azt fogja hozzon létre egy Azure-erőforráscsoportot, majd egy új Azure storage-fiókot és a egy új Azure Machine Learning Studio-munkaterület használatával a Resource Manager-sablon üzembe helyezése. Az üzembe helyezés befejezése után a rendszer (az elsődleges kulcsot, a munkaterület azonosítója és az URL-cím a munkaterületre) létrehozott munkaterületeket fontos információinak kinyomtatásához.

### <a name="create-an-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon létrehozása

A Machine Learning-munkaterület szükséges Azure storage-fiók, hozzá kell kapcsolni az adatkészlet tárolásához.
Az alábbi sablont használ, az az erőforráscsoport létrehozásához a tárfiók nevét és a munkaterület nevét.  Azt is használ a tárfiók nevének tulajdonságként a munkaterület létrehozásakor.

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
Ez a sablon mentése c:\temp\ mlworkspace.json fájlt.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Az erőforráscsoport a sablon üzembe helyezése

* A PowerShell megnyitása
* Az Azure Resource Manager és az Azure Service Management-modulok telepítése

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Ezeket a lépéseket töltse le és telepítse a modulokat, amelyek szükségesek ahhoz, hogy a hátralévő lépéseket. Ezt csak egyszer kell megtennie a környezetben, ahol futtatja a PowerShell-parancsokat kell.

* Azure-beli hitelesítésre

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Ebben a lépésben meg kell ismételni minden munkamenethez. A hitelesítést követően az előfizetési adatai üzenetnek kell megjelennie.

![Azure-fiók](./media/deploy-with-resource-manager-template/azuresubscription.png)

Most, hogy már az Azure-ba, hogy hozhat létre az erőforráscsoportot.

* Hozzon létre egy erőforráscsoportot

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Győződjön meg arról, hogy az erőforráscsoport megfelelően van-e kiépítve. **ProvisioningState** kell lennie "sikeres volt."
Az erőforráscsoport nevét használják a sablont létrehozni a tárfiók nevét. A tárfiók neve lehet 3 – 24 karakter közötti hosszúságúnak, és csak számokat és kisbetűket tartalmazhat.

![Erőforráscsoport](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Használja az erőforráscsoport-telepítés, üzembe helyezése egy új Machine Learning-munkaterületet.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Az üzembe helyezés befejezése után az üzembe helyezett munkaterület tulajdonságai magától értetődő. Ha például is elérheti az elsődleges kulcs Token.

```powershell
# Access Azure Machine Learning studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Egy másik meglévő munkaterület-jogkivonatok módja az Invoke-AzResourceAction parancs használata. Például listázhatja az összes munkaterületet az elsődleges és másodlagos jogkivonatokat.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
A munkaterület kiépítése, után számos Azure Machine Learning Studio feladatokat is automatizálhatja a [PowerShell-modul az Azure Machine Learning Studio](https://aka.ms/amlps).

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [Azure Resource Manager-sablonok készítése](../../azure-resource-manager/resource-group-authoring-templates.md).
* Tekintse meg a [Azure-Gyorssablonok-adattárában](https://github.com/Azure/azure-quickstart-templates).
* Ebben a videóban kapcsolatos [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Tekintse meg a [Resource Manager-sablon referencia Súgó](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->
