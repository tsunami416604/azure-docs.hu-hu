---
title: CI/CD Azure-folyamatokkal és-sablonokkal
description: Ismerteti, hogyan lehet folyamatos integrációt beállítani az Azure-folyamatokban az Azure erőforráscsoport-telepítési projektek használatával a Visual Studióban Resource Manager-sablonok üzembe helyezéséhez.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d8eff1c7efae319106eb8a85af7823a820a0da39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82084651"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>ARM-sablonok integrálása az Azure-folyamatokkal

A Visual Studio Azure erőforráscsoport-projektet biztosít Azure Resource Manager (ARM) Sablonok létrehozásához és az Azure-előfizetéshez való üzembe helyezéséhez. A projektet integrálhatja Azure-folyamatokkal a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD) érdekében.

A sablonok Azure-folyamatokkal való üzembe helyezésének két módja van:

* **Azure PowerShell parancsfájlt futtató feladat hozzáadása**. Ennek a beállításnak az az előnye, hogy a teljes fejlesztési életciklus során a konzisztencia biztosítható, mivel a Visual Studio-projektben (Deploy-AzureResourceGroup.ps1) található parancsfájlt használja. A parancsfájl a projektből egy olyan Storage-fiókra mutat, amelyet a Resource Manager elérhet. Az összetevők olyan elemek a projektben, mint például a csatolt sablonok, a parancsfájlok és az alkalmazás bináris fájljai. Ezt követően a parancsfájl üzembe helyezi a sablont.

* Feladatok **hozzáadása a feladatok másolásához és üzembe helyezéséhez**. Ez a beállítás kényelmes alternatívát kínál a projekt parancsfájlhoz. A folyamat két feladatot konfigurál. Egy feladat az összetevők és a másik tevékenység fázisában telepíti a sablont.

Ez a cikk mindkét megközelítést mutatja be.

## <a name="prepare-your-project"></a>A projekt előkészítése

Ez a cikk feltételezi, hogy a Visual Studio-projekt és az Azure DevOps-szervezet készen áll a folyamat létrehozására. A következő lépések bemutatják, hogyan ellenőrizheti, hogy készen áll-e:

* Rendelkezik egy Azure DevOps-szervezettel. Ha még nem rendelkezik ilyennel, [hozzon létre egyet ingyen](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Ha a csapata már rendelkezik Azure DevOps-szervezettel, ellenőrizze, hogy a használni kívánt Azure DevOps-projekt rendszergazdája-e.

* Konfigurált egy [szolgáltatási kapcsolódást](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) az Azure-előfizetéséhez. A folyamat feladatai az egyszerű szolgáltatás identitása alatt futnak. A kapcsolatok létrehozásának lépéseiért tekintse meg a [DevOps-projekt létrehozása](deployment-tutorial-pipeline.md#create-a-devops-project)című témakört.

* Van egy Visual Studio-projektje, amely az **Azure Resource Group** Starter sablonból lett létrehozva. Az ilyen típusú projekt létrehozásával kapcsolatos további információkért lásd: [Azure-erőforráscsoportok létrehozása és telepítése a Visual Studióval](create-visual-studio-deployment-project.md).

* A Visual Studio-projekt [egy Azure DevOps-projekthez csatlakozik](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Folyamat létrehozása

1. Ha korábban még nem adott hozzá egy folyamatot, létre kell hoznia egy új folyamatot. Az Azure DevOps-szervezetből válassza a **folyamatok** és az **új folyamat**elemet.

   ![Új folyamat hozzáadása](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Adja meg a kód tárolási helyét. Az alábbi képen az **Azure Repos git**kiválasztása látható.

   ![Kód forrásának kiválasztása](./media/add-template-to-azure-pipelines/select-source.png)

1. Ebből a forrásból válassza ki azt a tárházat, amely rendelkezik a projekt kódjával.

   ![Adattár kiválasztása](./media/add-template-to-azure-pipelines/select-repo.png)

1. Válassza ki a létrehozandó folyamat típusát. Kiválaszthatja az **induló folyamat**elemet.

   ![Folyamat kiválasztása](./media/add-template-to-azure-pipelines/select-pipeline.png)

Készen áll Azure PowerShell feladat hozzáadására vagy a fájl másolására és a feladatok üzembe helyezésére.

## <a name="azure-powershell-task"></a>Azure PowerShell feladat

Ez a szakasz bemutatja, hogyan konfigurálhatja a folyamatos üzembe helyezést egyetlen olyan feladattal, amely futtatja a PowerShell-parancsfájlt a projektben. A következő YAML-fájl egy [Azure PowerShell feladatot](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)hoz létre:

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

A feladat beállításakor `AzurePowerShell@3` a folyamat a AzureRM modulból származó parancsokat használ a kapcsolatok hitelesítéséhez. Alapértelmezés szerint a Visual Studio-projektben a PowerShell-szkript a AzureRM modult használja. Ha frissítette a szkriptet az az [modul](/powershell/azure/new-azureps-module-az)használatára, állítsa a feladatot a következőre: `AzurePowerShell@4` .

```yaml
steps:
- task: AzurePowerShell@4
```

A (z) esetében `azureSubscription` adja meg a létrehozott szolgáltatási kapcsolatok nevét.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

A esetében `scriptPath` adja meg a folyamat fájljának relatív elérési útját a parancsfájlnak. A tárházban megtekintheti az elérési utat.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Ha nincs szüksége az összetevők előkészítésére, csak adja meg egy erőforráscsoport nevét és helyét, amelyet az üzembe helyezéshez szeretne használni. A Visual Studio-parancsfájl létrehozza az erőforráscsoportot, ha még nem létezik.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Ha az összetevőket egy meglévő Storage-fiókba kell bemutatnia, használja a következőt:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Most, hogy megértette, hogyan hozhatja létre a feladatot, ugorjon végig a folyamat szerkesztésének lépésein.

1. Nyissa meg a folyamatot, és cserélje le a tartalmát a YAML:

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

1. Adjon meg egy üzenetet a végrehajtáshoz, és véglegesítse közvetlenül a **főkiszolgálónak**.

1. Ha a **Mentés**lehetőséget választja, a folyamat automatikusan elindul. Térjen vissza a build-folyamat összegzéséhez, és tekintse meg az állapotot.

   ![Eredmények megtekintése](./media/add-template-to-azure-pipelines/view-results.png)

Az aktuálisan futó folyamat kiválasztásával megtekintheti a feladatok részleteit. Amikor befejeződik, megjelenik az egyes lépések eredményei.

## <a name="copy-and-deploy-tasks"></a>Feladatok másolása és üzembe helyezése

Ez a szakasz bemutatja, hogyan konfigurálhatja a folyamatos üzembe helyezést két feladat használatával az összetevők előkészítéséhez és a sablon üzembe helyezéséhez.

Az alábbi YAML az [Azure file Copy feladatot](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)jeleníti meg:

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

A feladat több részből áll, hogy átvizsgálja a környezetét. A az összetevők `SourcePath` helyét jelzi a folyamat fájljához viszonyítva. Ebben a példában a fájlok egy nevű mappában találhatók, `AzureResourceGroup1` amely a projekt neve volt.

```yaml
SourcePath: '<path-to-artifacts>'
```

A (z) esetében `azureSubscription` adja meg a létrehozott szolgáltatási kapcsolatok nevét.

```yaml
azureSubscription: '<your-connection-name>'
```

A tárolási és a tároló neve mezőben adja meg az összetevők tárolásához használni kívánt Storage-fiók és-tároló nevét. A Storage-fióknak léteznie kell.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

A következő YAML a [Azure Resource Manager sablon telepítési feladatát](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)mutatja:

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

A feladat több részből áll, hogy átvizsgálja a környezetét.

- `deploymentScope`: Válassza ki az üzembe helyezés hatókörét a következő lehetőségek közül: `Management Group` `Subscription` és `Resource Group` . Ebben az útmutatóban használhatja az **erőforráscsoportot** . További információ a hatókörökről: [telepítési hatókörök](deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: Adja meg a létrehozott szolgáltatási kapcsolatok nevét.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: Adja meg a cél előfizetés-AZONOSÍTÓját. Ez a tulajdonság csak az erőforráscsoport központi telepítési hatókörére és az előfizetés központi telepítési hatókörére vonatkozik.

- `resourceGroupName`és `location` : adja meg a telepíteni kívánt erőforráscsoport nevét és helyét. A feladat akkor hozza létre az erőforráscsoportot, ha az nem létezik.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

A központi telepítési feladat egy nevű sablonra `WebSite.json` és egy WebSite.parameters.jsnevű Parameters fájlra hivatkozik. Használja a sablon és a paraméter fájljainak nevét.

Most, hogy megértette, hogyan hozhatja létre a feladatokat, nézzük végig a folyamat szerkesztésének lépéseit.

1. Nyissa meg a folyamatot, és cserélje le a tartalmát a YAML:

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

1. Adjon meg egy üzenetet a végrehajtáshoz, és véglegesítse közvetlenül a **főkiszolgálónak**.

1. Ha a **Mentés**lehetőséget választja, a folyamat automatikusan elindul. Térjen vissza a build-folyamat összegzéséhez, és tekintse meg az állapotot.

   ![Eredmények megtekintése](./media/add-template-to-azure-pipelines/view-results.png)

Az aktuálisan futó folyamat kiválasztásával megtekintheti a feladatok részleteit. Amikor befejeződik, megjelenik az egyes lépések eredményei.

## <a name="next-steps"></a>További lépések

Az Azure-folyamatok ARM-sablonokkal való használatának lépésenkénti folyamatát lásd [: oktatóanyag: Azure Resource Manager sablonok folyamatos integrációja az Azure-folyamatokkal](deployment-tutorial-pipeline.md).
