---
title: A Studio (klasszikus) munkaterületének üzembe helyezése Azure Resource Manager
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasszikus) munkaterületének üzembe helyezése Azure Resource Manager sablon használatával
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: d7311dce938ea575a27cf006e7469670813b7f10
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153502"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Azure Machine Learning Studio (klasszikus) munkaterület üzembe helyezése Azure Resource Manager használatával

Az egy Azure Resource Manager központi telepítési sablont, így Ön egy skálázható módon, hogy Ön időt takarít az érvényesítést összekapcsolt összetevők üzembe helyezése és újrapróbálkozási mechanizmus. Azure Machine Learning Studio (klasszikus) munkaterületek beállításához például először be kell állítania egy Azure Storage-fiókot, majd telepítenie kell a munkaterületet. Tegyük fel, így manuálisan munkaterületek több száz. Egyszerűbb alternatíva egy Azure Resource Manager sablon használata Studio (klasszikus) munkaterület és annak összes függőségének üzembe helyezéséhez. Ez a cikk végigvezeti a részletes folyamat. A Azure Resource Manager nagyszerű áttekintését az [Azure Resource Manager áttekintése](../../azure-resource-manager/management/overview.md)című témakörben tekintheti meg.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Részletes útmutató: Machine Learning-munkaterület létrehozása
Létre fogunk hozni egy Azure-erőforráscsoportot, majd üzembe kell helyezni egy új Azure Storage-fiókot és egy új Azure Machine Learning Studio (klasszikus) munkaterületet egy Resource Manager-sablon használatával. Az üzembe helyezés befejezése után a rendszer (az elsődleges kulcsot, a munkaterület azonosítója és az URL-cím a munkaterületre) létrehozott munkaterületeket fontos információinak kinyomtatásához.

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

* Erőforráscsoport létrehozása

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Győződjön meg arról, hogy az erőforráscsoport megfelelően van-e kiépítve. A **ProvisioningState** "sikeres" értékűnek kell lennie.
Az erőforráscsoport nevét használják a sablont létrehozni a tárfiók nevét. A tárfiók neve lehet 3 – 24 karakter közötti hosszúságúnak, és csak számokat és kisbetűket tartalmazhat.

![Erőforráscsoport](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Használja az erőforráscsoport-telepítés, üzembe helyezése egy új Machine Learning-munkaterületet.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Az üzembe helyezés befejezése után az üzembe helyezett munkaterület tulajdonságai magától értetődő. Ha például is elérheti az elsődleges kulcs Token.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

A meglévő munkaterület jogkivonatának beolvasására egy másik módszer a Meghívási-AzResourceAction parancs használata. Például listázhatja az összes munkaterületet az elsődleges és másodlagos jogkivonatokat.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
A munkaterület kiépítés után számos Azure Machine Learning Studio (klasszikus) feladatot is automatizálhat [Azure Machine learning Studio (klasszikus) PowerShell-modul](https://aka.ms/amlps)használatával.

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Resource Manager-sablonok létrehozásáról](../../azure-resource-manager/templates/template-syntax.md).
* Tekintse meg az [Azure Gyorsindítás sablonok tárházát](https://github.com/Azure/azure-quickstart-templates).
* Tekintse meg ezt a videót a [Azure Resource Managerról](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Tekintse meg a [Resource Manager-sablon referenciájának súgóját](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions) .

<!--Link references-->
