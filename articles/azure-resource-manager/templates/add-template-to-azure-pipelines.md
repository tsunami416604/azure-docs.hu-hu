---
title: CI/CD Azure-folyamatokés sablonok használatával
description: Ez a témakör azt ismerteti, hogy miként állítható be a folyamatos integráció az Azure-folyamatokban az Azure Resource Group üzembe helyezési projektjeinek használatával a Visual Studióban az Erőforrás-kezelő-sablonok üzembe helyezéséhez.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 7617bf47595fce7baa533b0f7cc94a1803ddd349
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153454"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Arm-sablonok integrálása az Azure-folyamatokba

A Visual Studio az Azure Resource Group projektet biztosítja az Azure Resource Manager (ARM) sablonok létrehozásához és üzembe helyezéséhez az Azure-előfizetésben. Ezt a projektet integrálhatja az Azure Pipelines a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD).

Az Azure Pipelines segítségével kétféleképpen helyezhet üzembe sablonokat:

* **Adja hozzá az Azure PowerShell-parancsfájlt futtató feladatot.** Ezzel a beállítással a fejlesztési életciklus során konzisztenciát biztosít, mivel ugyanazt a parancsfájlt használja, amely a Visual Studio-projektben (Deploy-AzureResourceGroup.ps1) szerepel. A parancsfájl a projekt összetevőit egy olyan tárfiókba rendezi, amelyhez az Erőforrás-kezelő hozzáférhet. Az összetevők olyan elemek a projektben, mint a csatolt sablonok, parancsfájlok és alkalmazásbináris fájlok. Ezután a parancsfájl telepíti a sablont.

* **Feladatok hozzáadása a feladatok másolásához és telepítéséhez.** Ez a beállítás kényelmes alternatívát kínál a projekt szkriptjéhez képest. Két feladatot konfigurálhat a folyamatban. Az egyik feladat az összetevőket, a másik pedig a sablont telepíti.

Ez a cikk mindkét megközelítést mutatja be.

## <a name="prepare-your-project"></a>A projekt előkészítése

Ez a cikk feltételezi, hogy a Visual Studio-projekt és az Azure DevOps-szervezet készen áll a folyamat létrehozására. A következő lépések bemutatják, hogyan győződjön meg arról, hogy készen áll:

* Azure DevOps-szervezetsel rendelkezik. Ha még nem rendelkezik ilyen, [hozzon létre egyet ingyen](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Ha csapata már rendelkezik egy Azure DevOps-szervezettel, győződjön meg arról, hogy ön a használni kívánt Azure DevOps-projekt rendszergazdája.

* Szolgáltatási [kapcsolatot](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) konfigurált az Azure-előfizetéséhez. A folyamatban lévő feladatok a szolgáltatásnév identitása alatt hajthatók végre. A kapcsolat létrehozásának lépéseit a [DevOps-projekt létrehozása](template-tutorial-use-azure-pipelines.md#create-a-devops-project)című témakörben található.

* Van egy Visual Studio-projekt, amely az **Azure Resource Group** kezdősablonból jött létre. Az ilyen típusú projektek létrehozásáról az [Azure-erőforráscsoportok létrehozása és üzembe helyezése a Visual Studióban című](create-visual-studio-deployment-project.md)témakörben talál további információt.

* A Visual Studio-projekt [egy Azure DevOps-projekthez kapcsolódik.](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)

## <a name="create-pipeline"></a>Folyamat létrehozása

1. Ha korábban még nem adott hozzá folyamatot, új folyamatot kell létrehoznia. Az Azure DevOps-szervezetből válassza **a Folyamatok** és **az Új folyamat lehetőséget.**

   ![Új folyamat hozzáadása](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Adja meg a kód tárolási helyét. Az alábbi képen az **Azure Repos Git lehetőséget választja.**

   ![Kódforrás kiválasztása](./media/add-template-to-azure-pipelines/select-source.png)

1. Ebből a forrásból válassza ki azt a tárházat, amely rendelkezik a projekt kódjával.

   ![Tárház kiválasztása](./media/add-template-to-azure-pipelines/select-repo.png)

1. Válassza ki a létrehozandó folyamat típusát. Kiválaszthatja **a Starter folyamatot**.

   ![Folyamat kijelölése](./media/add-template-to-azure-pipelines/select-pipeline.png)

Készen áll egy Azure PowerShell-feladat hozzáadására, vagy a másolási fájlra, és üzembe helyezheti a feladatokat.

## <a name="azure-powershell-task"></a>Azure PowerShell-feladat

Ez a szakasz bemutatja, hogyan konfigurálhatja a folyamatos üzembe helyezés egyetlen feladat használatával, amely futtatja a PowerShell-parancsfájlt a projektben. A következő YAML-fájl létrehoz egy [Azure PowerShell-feladatot:](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus'
    azurePowerShellVersion: LatestVersion
```

Ha a feladatot `AzurePowerShell@3`a beállítás, a folyamat az AzureRM-modul parancsait használja a kapcsolat hitelesítéséhez. Alapértelmezés szerint a PowerShell-parancsfájl a Visual Studio-projektben az AzureRM-modult használja. Ha a parancsfájlt az [Az modul](/powershell/azure/new-azureps-module-az)használatára frissítette, állítsa a feladatot a beállításra. `AzurePowerShell@4`

```yaml
steps:
- task: AzurePowerShell@4
```

A `azureSubscription`esetén adja meg a létrehozott szolgáltatáskapcsolat nevét.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

A `scriptPath`esetében adja meg a folyamatfájl és a parancsfájl relatív elérési útját. Megkeresheti a tárházban az elérési utat.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Ha nem kell összetevőket, csak adja meg a nevét és helyét egy erőforráscsoport központi telepítéshez használható. A Visual Studio parancsfájl létrehozza az erőforráscsoportot, ha még nem létezik.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Ha egy meglévő tárfiókba kell rendeznie az összetevőket, használja a következőket:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Most, hogy megértette, hogyan lehet létrehozni a feladatot, menjünk végig a lépéseket, hogy a folyamat szerkesztéséhez.

1. Nyissa meg a folyamatot, és cserélje le a tartalmát a YAML-re:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. Kattintson a **Mentés** gombra.

   ![Folyamat mentése](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. Adjon meg egy üzenetet a véglegesítéshez, és kötelezze el magát közvetlenül a **master**.

1. Ha a Mentés lehetőséget **választja,** a buildfolyamat automatikusan elindul. Lépjen vissza a buildfolyamat összegzéséhez, és figyelje az állapotot.

   ![Eredmények megtekintése](./media/add-template-to-azure-pipelines/view-results.png)

A feladatok részleteinek megtekintéséhez kiválaszthatja az éppen futó folyamatot. Amikor befejeződik, az egyes lépéseket láthatja.

## <a name="copy-and-deploy-tasks"></a>Feladatok másolása és üzembe helyezése

Ez a szakasz bemutatja, hogyan konfigurálhatja a folyamatos üzembe helyezést két feladat használatával az összetevők színpadra helyezéséhez és a sablon központi telepítéséhez.

A következő YAML az [Azure fájlmásolási feladatot](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)jeleníti meg:

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

A feladatnak több részét is felül kell vizsgálnia a környezetéhez. A `SourcePath` jelzi a rendszerhibák helyét a folyamatfájlhoz képest. Ebben a példában a fájlok `AzureResourceGroup1` egy projekt neve nevű mappában találhatók.

```yaml
SourcePath: '<path-to-artifacts>'
```

A `azureSubscription`esetén adja meg a létrehozott szolgáltatáskapcsolat nevét.

```yaml
azureSubscription: '<your-connection-name>'
```

A tárolási és tárolónév, adja meg a nevét a tárfiók és a tároló, amelyet használni kíván a műtermékek tárolására. A tárfióknak léteznie kell.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

A következő YAML az [Azure Resource Manager-sablon telepítési feladatát](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)mutatja:

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    deploymentScope: 'Resource Group'
    ConnectedServiceName: 'demo-deploy-sp'
    subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'Central US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
    deploymentMode: 'Incremental'
```

A feladatnak több részét is felül kell vizsgálnia a környezetéhez.

- `deploymentScope`: Válassza ki a telepítés `Management Group`hatókörét a következő lehetőségek közül: és `Subscription` `Resource Group`. Használja **az Erőforráscsoport** ebben a séta. A hatókörökről a [Telepítési hatókörök című témakörben olvashat bővebben.](deploy-rest.md#deployment-scope)

- `ConnectedServiceName`: Adja meg a létrehozott szolgáltatáskapcsolat nevét.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: Adja meg a cél-előfizetés azonosítóját. Ez a tulajdonság csak az Erőforráscsoport központi telepítési hatókörére és az előfizetés telepítési hatókörére vonatkozik.

- `resourceGroupName`és `location`: adja meg annak az erőforráscsoportnak a nevét és helyét, amelybe telepíteni szeretné. A tevékenység létrehozza az erőforráscsoportot, ha az nem létezik.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

A telepítési feladat egy `WebSite.json` website.parameters.json nevű sablonra és paraméterfájlra hivatkozik. Használja a sablon és a paraméterfájlok nevét.

Most, hogy megértette, hogyan hozhat létre a feladatokat, menjünk végig a lépéseket, hogy a folyamat szerkesztéséhez.

1. Nyissa meg a folyamatot, és cserélje le a tartalmát a YAML-re:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
    - task: AzureResourceGroupDeployment@2
      displayName: 'Deploy template'
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: 'demo-deploy-sp'
        subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
        action: 'Create Or Update Resource Group'
        resourceGroupName: 'demogroup'
        location: 'Central US'
        templateLocation: 'URL of the file'
        csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
        csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
        overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
        deploymentMode: 'Incremental'
   ```

1. Kattintson a **Mentés** gombra.

1. Adjon meg egy üzenetet a véglegesítéshez, és kötelezze el magát közvetlenül a **master**.

1. Ha a Mentés lehetőséget **választja,** a buildfolyamat automatikusan elindul. Lépjen vissza a buildfolyamat összegzéséhez, és figyelje az állapotot.

   ![Eredmények megtekintése](./media/add-template-to-azure-pipelines/view-results.png)

A feladatok részleteinek megtekintéséhez kiválaszthatja az éppen futó folyamatot. Amikor befejeződik, az egyes lépéseket láthatja.

## <a name="next-steps"></a>További lépések

Az Azure Pipelines ARM-sablonokkal való használatának lépésről lépésre történő folyamatáról az [Oktatóanyag: ARM-sablonok folyamatos integrációja az Azure Pipelines-szal.](template-tutorial-use-azure-pipelines.md)
