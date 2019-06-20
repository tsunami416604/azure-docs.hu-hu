---
title: CI/CD Azure-adatcsatornák és a Resource Manager-sablonokkal
description: Ismerteti, hogyan lehet Azure folyamatokban folyamatos integráció beállítása az Azure erőforráscsoport-telepítési projekt a Visual Studióban történő üzembe helyezése Resource Manager-sablonok alapján.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: article
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: b70b38904c0373c53c3731dd0442511116d9c4de
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191459"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Resource Manager-sablonok integrálása az Azure-folyamatok

A Visual Studio IDE sablonok létrehozásához és üzembe helyezéséhez őket az Azure-előfizetéshez az Azure erőforráscsoport-projekt. Ez a projekt integrálható Azure folyamatok, a folyamatos integráció és készregyártás (CI/CD).

A sablonok Azure folyamatok üzembe helyezése két módja van:

* **Adja hozzá az Azure PowerShell-szkript futó feladat**. Ez a beállítás biztosítja azt az előnyt biztosító konzisztencia a fejlesztési életciklus során, mert ugyanezt a szkriptet, amely megtalálható a Visual Studio-projektet (üzembe helyezés – AzureResourceGroup.ps1) használja. A parancsfájl szakaszok összetevők a projektből a storage-fiók Resource Manager eléréséhez. Összetevők olyan elemek, például a hivatkozott sablonok, szkriptek és alkalmazás bináris fájljainak a projektben. Ezt követően a szkript üzembe helyezi a sablont.

* **Másolja ki és üzembe helyezése a feladatok tevékenységek hozzáadása a**. Ez a beállítás alternatívája a projekt parancsfájlt. A folyamat két feladatot konfigurálja. Egy feladat előkészíti az összetevők, és a többi feladat üzembe helyezi a sablont.

Ez a cikk mindkét módszert bemutatja.

## <a name="prepare-your-project"></a>-Projektek előkészítése

Ez a cikk feltételezi, hogy a Visual Studio-projekt és az Azure DevOps-szervezet készen áll a folyamat létrehozásához. A következő lépések bemutatják, hogyan győződjön meg arról, készen áll:

* Az Azure DevOps-szervezet rendelkezik. Ha nem rendelkezik ilyennel, [ingyenesen](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Ha a csapat már rendelkezik az Azure DevOps szervezetekben, ellenőrizze, hogy Ön rendszergazda a használni kívánt Azure DevOps-projekt.

* Konfigurált egy [kapcsolat szolgáltatással](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) az Azure-előfizetéshez. A folyamat a feladatokat az egyszerű szolgáltatás identitása alatt futnak. A kapcsolat létrehozásához lépéseiért lásd: [DevOps-projekt létrehozása](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Visual Studio-projekt alapján létrehozott rendelkezik a **Azure-erőforráscsoport** kiindulási sablonját. A projekt típusa létrehozásával kapcsolatos információkért lásd: [létrehozása és telepítése az Azure erőforráscsoport-sablonok a Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

* A Visual Studio-projekt [csatlakozik az Azure DevOps-projekt](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Folyamat létrehozása

1. Ha korábban még nem vett egy folyamatot, hozzon létre egy új folyamatot szeretné. Válassza ki a szervezet Azure DevOps **folyamatok** és **új adatcsatorna**.

   ![Új adatcsatorna hozzáadása](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. A kód helyének meghatározásához. Az alábbi képen látható kiválasztásával **Azure Git-Adattárakkal**.

   ![Kód forrás kiválasztása](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. Az adatforráshoz jelölje ki a tárház, amely rendelkezik a kódot a projekthez.

   ![Tárház kiválasztása](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. Válassza ki a folyamat létrehozásához. Választhat **alapszintű folyamat**.

   ![Folyamat kiválasztása](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

Készen áll az Azure PowerShell-feladat vagy a fájl másolása és a feladatok üzembe helyezése.

## <a name="azure-powershell-task"></a>Az Azure PowerShell-tevékenység

Ez a szakasz bemutatja, hogyan folyamatos üzembe helyezés konfigurálása, amely futtatja a PowerShell-parancsfájlt a projektben egy feladat használatával. A következő YAML-fájlt hoz létre egy [Azure PowerShell-lel feladat](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

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

Ha beállította a feladat `AzurePowerShell@3`, a folyamat az AzureRM-modul a parancsokat használja a kapcsolat hitelesítéséhez. Alapértelmezés szerint a PowerShell-parancsprogram a Visual Studio-projektet az AzureRM-modul használja. Ha már frissítette a használandó parancsfájlt a [Az modul](/powershell/azure/new-azureps-module-az), a feladat beállítása `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

A `azureSubscription`, adja meg a létrehozott szolgáltatáskapcsolódási nevét.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

A `scriptPath`, adja meg a relatív elérési útja a folyamat fájlból a szkriptet. Tekintse meg az elérési út tárház megtalálhatja.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Ha nincs szüksége az fázis összetevőket, adja át a nevét és a egy erőforráscsoport, a telepítéshez helyét. A Visual Studio parancsfájl létrehozza az erőforráscsoportot, ha az még nem létezik.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Ha egy meglévő tárfiókot fázis összetevőkhöz kell, használja:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Most, hogy megismerkedett a feladat létrehozása, vegyük végig a lépéseket, a folyamat szerkesztése.

1. Nyissa meg a folyamatot, és cserélje ki annak tartalmát a YAML:

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

   ![Folyamat mentése](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. Adja meg a véglegesítési üzenetet, és közvetlenül a véglegesítés **fő**.

1. Ha bejelöli **mentése**, a létrehozási folyamat automatikusan elindul. Lépjen vissza az összegzés a buildelési folyamat, és tekintse meg az állapotát.

   ![Eredmények megtekintése](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Kiválaszthatja, hogy a jelenleg futó folyamat a feladatokkal kapcsolatos további részletek megtekintéséhez. Amikor befejeződik, megjelenik az egyes lépések eredményét.

## <a name="copy-and-deploy-tasks"></a>Másolja ki és feladatok üzembe helyezése

Ez a szakasz bemutatja, hogyan konfigurálhatja a folyamatos üzembe helyezés előkészítéséhez az összetevőket, és a sablon üzembe helyezése két feladat segítségével. 

A következő YAML-bemutatja a [Azure-beli másolási feladat](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

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

Ennek a feladatnak felül környezetében kell több részből áll. A `SourcePath` azt jelzi, hogy az összetevők, a folyamat fájlhoz viszonyítva helyét. A fájlok léteznek ebben a példában lévő szkriptfájlokat `AzureResourceGroup1` lett a projekt nevét.

```yaml
SourcePath: '<path-to-artifacts>'
```

A `azureSubscription`, adja meg a létrehozott szolgáltatáskapcsolódási nevét.

```yaml
azureSubscription: '<your-connection-name>'
```

Tárolás és a tároló nevét adja meg a tárfiók és tároló a munkadarabok tárolásához használni kívánt nevét. A tárfiók léteznie kell.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

A következő YAML-bemutatja a [Azure-erőforrás csoport üzembe helyezési feladat](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    azureSubscription: 'demo-deploy-sp'
    resourceGroupName: 'demogroup'
    location: 'centralus'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
```

Ennek a feladatnak felül környezetében kell több részből áll. A `azureSubscription`, adja meg a létrehozott szolgáltatáskapcsolódási nevét.

```yaml
azureSubscription: '<your-connection-name>'
```

A `resourceGroupName` és `location`, adja meg a nevét, és telepíteni kívánja az erőforráscsoport helyét. A feladatot az erőforráscsoport jön létre, ha még nem létezik.

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

Az üzembe helyezési feladat szereplő hivatkozások a sablont `WebSite.json` és a egy paraméterfájl nevű WebSite.parameters.json. A sablonnal és paraméterfájlokkal fájlok neveit használja.

Most, hogyan hozhat létre a feladatokat, vegyük végig a lépéseket, a folyamat szerkesztése megértette.

1. Nyissa meg a folyamatot, és cserélje ki annak tartalmát a YAML:

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
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. Kattintson a **Mentés** gombra.

1. Adja meg a véglegesítési üzenetet, és közvetlenül a véglegesítés **fő**.

1. Ha bejelöli **mentése**, a létrehozási folyamat automatikusan elindul. Lépjen vissza az összegzés a buildelési folyamat, és tekintse meg az állapotát.

   ![Eredmények megtekintése](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Kiválaszthatja, hogy a jelenleg futó folyamat a feladatokkal kapcsolatos további részletek megtekintéséhez. Amikor befejeződik, megjelenik az egyes lépések eredményét.

## <a name="next-steps"></a>További lépések

Részletes folyamat a folyamatokat az Azure Resource Manager-sablonokkal való használatához, tekintse meg a [oktatóanyag: Folyamatos integráció az Azure Resource Manager-sablonok az Azure-folyamatok](resource-manager-tutorial-use-azure-pipelines.md).
