---
title: Fejlesztői környezet konfigurálása telepítési parancsfájlok számára a sablonokban | Microsoft Docs
description: a Azure Resource Manager-sablonokban lévő telepítési parancsfájlok fejlesztői környezetének konfigurálása.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: jgao
ms.openlocfilehash: 232a1ae5d125a2ea1d5723e85073fb3dd02420cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87294219"
---
# <a name="configure-development-environment-for-deployment-scripts-in-templates-preview"></a>Fejlesztői környezet konfigurálása a sablonokban üzembe helyezési parancsfájlok számára (előzetes verzió)

Ismerje meg, hogyan hozhat létre fejlesztési környezetet az üzembe helyezési parancsfájlok kiépítéséhez és teszteléséhez egy telepítési parancsfájl használatával. Létrehozhat [Azure Container-példányt](../../container-instances/container-instances-overview.md) vagy használhatja a [Docker](https://docs.docker.com/get-docker/)-t is. Mindkettőt ebben a cikkben tárgyaljuk.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik üzembehelyezési parancsfájllal, létrehozhat egy **hello.ps1** fájlt a következő tartalommal:

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

## <a name="use-azure-container-instance"></a>Az Azure Container instance használata

A parancsfájlok a számítógépen való létrehozásához létre kell hoznia egy Storage-fiókot, és csatlakoztatnia kell a Storage-fiókot a Container-példányhoz. Így feltöltheti a parancsfájlt a Storage-fiókba, és futtathatja a szkriptet a Container példányon.

> [!NOTE]
> A parancsfájl teszteléséhez létrehozott Storage-fiók nem ugyanaz a Storage-fiók, amelyet a telepítési parancsfájl-szolgáltatás a parancsfájl végrehajtásához használ. Az üzembe helyezési parancsfájl szolgáltatás minden végrehajtáskor egy egyedi nevet hoz létre fájlmegosztásként.

### <a name="create-an-azure-container-instance"></a>Azure Container-példány létrehozása

A következő ARM-sablon létrehoz egy tároló-példányt és egy fájlmegosztást, majd csatlakoztatja a fájlmegosztást a tároló lemezképéhez.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "deploymentScript",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('projectName'), 'store')]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```
A csatlakoztatási útvonal alapértelmezett értéke **deploymentScript**.  Ez a tároló példányának elérési útja, amely a fájlmegosztás számára van csatlakoztatva.

A sablonban megadott alapértelmezett tároló-rendszerkép **MCR.microsoft.com/azuredeploymentscripts-PowerShell:az4.3 "**.  A támogatott Azure PowerShell-verziók és az Azure CLI-verziók listáját az [Azure PowerShell vagy az Azure CLI](./deployment-script-template.md#prerequisites)című részben tekintheti meg.

A sablon felfüggeszti a 1800 másodperces tároló-példányt. 30 perccel azelőtt, hogy a tároló példánya terminál állapotba kerül, és a munkamenet véget ér.

A sablon üzembe helyezése:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-azResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-deployment-script"></a>Üzembe helyezési parancsfájl feltöltése

Töltse fel a telepítési szkriptet a Storage-fiókba. A következő egy PowerShell-példa:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

A fájlt a Azure Portal és az Azure CLI használatával is feltöltheti.

### <a name="test-the-deployment-script"></a>Az üzembe helyezési parancsfájl tesztelése

1. A Azure Portal nyissa meg azt az erőforráscsoportot, amelyben üzembe helyezte a tároló-példányt és a Storage-fiókot.
1. Nyissa meg a tároló csoportot. Az alapértelmezett tároló csoport neve a projekt neve a **CG** -vel hozzáfűzve. Látni fogja, hogy a tároló példánya **fut** állapotban van.
1. A bal oldali menüben válassza a **tárolók** lehetőséget. Ekkor meg kell tekintenie egy tároló-példányt.  A tároló példányának neve a projekt neve a hozzáfűzött **tárolóval** .

    ![üzembe helyezési parancsfájl összekapcsolási tárolójának példánya](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Válassza a **kapcsolat**, majd a **kapcsolat**lehetőséget. Ha nem tud csatlakozni a Container-példányhoz, indítsa újra a tároló csoportot, és próbálkozzon újra.
1. A konzol ablaktábláján futtassa a következő parancsokat:

    ```
    cd deploymentScript
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    A kimenet **Hello John Dole**.

    ![üzembehelyezési parancsfájl-tároló példányának tesztelése](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-docker"></a>A Docker használata

Egy előre konfigurált Docker-tároló rendszerképet is használhat a telepítési parancsfájl fejlesztési környezete számára. A Docker telepítéséhez lásd: [Docker beolvasása](https://docs.docker.com/get-docker/).
A fájlmegosztást úgy is be kell állítania, hogy csatlakoztassa a könyvtárat, amely tartalmazza az üzembe helyezési parancsfájlokat a Docker-tárolóba.

1. Kérje le a telepítési parancsfájl tárolójának rendszerképét a helyi számítógépre:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    A példa a PowerShell-4.3.0 verzióját használja.

    CLI-rendszerkép lekérése Microsoft Container Registryról (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Ez a példa a CLI-2.0.80 verzióját használja. Az üzembe helyezési parancsfájl az [itt](https://hub.docker.com/_/microsoft-azure-cli)található alapértelmezett CLI-tárolók rendszerképeit használja.

1. Futtassa helyileg a Docker-rendszerképet.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Cserélje le a ** &lt; gazdagép illesztőprogramjának betűjelét>** és az ** &lt; állomásnév nevét>** egy meglévő mappát a megosztott meghajtón.  Leképezi a mappát a tároló **/Data** mappájába. Példák a D:\docker leképezésére:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **– Ez azt jelenti, hogy** a tároló rendszerképét életben tartja.

    Egy CLI-példa:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Az alábbi képernyőfelvételen egy PowerShell-szkript futtatását láthatja, mivel a megosztott meghajtón helloworld.ps1-fájl található.

    ![Resource Manager-sablon üzembe helyezési parancsfájl Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

A parancsfájl sikeres tesztelése után a sablonban használható üzembe helyezési parancsfájlként.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használhatja a telepítési parancsfájlokat. Útmutató az üzembe helyezési parancsfájlhoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: telepítési parancsfájlok használata Azure Resource Manager-sablonokban](./template-tutorial-deployment-script.md)
