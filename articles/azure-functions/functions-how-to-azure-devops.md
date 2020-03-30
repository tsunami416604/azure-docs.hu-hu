---
title: A függvényalkalmazás-kód folyamatos frissítése az Azure DevOps használatával
description: Ismerje meg, hogyan állíthat be egy Azure DevOps-folyamatot, amely az Azure Functionst célozza meg.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.openlocfilehash: 5e2fc8fb06248e2cdad9067c56647da6d9626b50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255753"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Folyamatos kézbesítés az Azure DevOps használatával

A függvényt automatikusan telepítheti egy Azure Functions-alkalmazásba az [Azure Pipelines](/azure/devops/pipelines/)használatával.

A folyamat definiálására két lehetőség közül választhat:

- **YAML-fájl**: A YAML-fájl a folyamatot írja le. Előfordulhat, hogy a fájl rendelkezik egy buildlépés-szakaszsal és egy kiadási szakaszsal. A YAML-fájlnak ugyanabban a tárházban kell lennie, mint az alkalmazásnak.
- **Sablon:** A sablonok kész feladatok, amelyek az alkalmazást építik vagy telepítik.

## <a name="yaml-based-pipeline"></a>YAML-alapú folyamat

YAML-alapú folyamat létrehozásához először hozza létre az alkalmazást, majd telepítse az alkalmazást.

### <a name="build-your-app"></a>Az alkalmazás létrehozása

Az, hogy hogyan hoz létre alkalmazást az Azure-folyamatokban, az alkalmazás programozási nyelvététől függ. Minden nyelv rendelkezik konkrét build lépéseket, amelyek egy központi telepítési összetevő létrehozása. A központi telepítési összetevő a függvényalkalmazás azure-beli üzembe helyezéséhez szolgál.

# <a name="c"></a>[C\#](#tab/csharp)

Az alábbi minta segítségével YAML-fájlt hozhat létre .NET alkalmazás létrehozásához:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Az alábbi minta segítségével Létrehozhat egy YAML-fájlt JavaScript-alkalmazás létrehozásához:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi minták egyikével létrehozhat egy YAML-fájlt egy adott Python-verzióhoz tartozó alkalmazás létrehozásához. A Python csak linuxos függvényalkalmazások esetén támogatott.

**3.7-es verzió**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**3.6-os verzió**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Az alábbi minta segítségével létrehozhat egy YAML-fájlt egy PowerShell-alkalmazás csomagolásához. A PowerShell csak a Windows Azure Functions számára támogatott.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>Az alkalmazás telepítése

A gazdaoperációs rendszertől függően az alábbi YAML-minták egyikét is tartalmaznia kell a YAML-fájlban.

#### <a name="windows-function-app"></a>Windows függvényalkalmazás

A Windows függvényalkalmazás telepítéséhez a következő kódrészletet használhatja:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Linux függvényalkalmazás

A következő kódrészlet segítségével telepítheti a Linux függvényalkalmazást:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Sablonalapú folyamat

Az Azure DevOps-ban lévő sablonok előre definiált feladatcsoportok, amelyek egy alkalmazást hoznak létre vagy telepítenek.

### <a name="build-your-app"></a>Az alkalmazás létrehozása

Az, hogy hogyan hoz létre alkalmazást az Azure-folyamatokban, az alkalmazás programozási nyelvététől függ. Minden nyelv rendelkezik konkrét build lépéseket, amelyek egy központi telepítési összetevő létrehozása. A központi telepítési összetevő a függvényalkalmazás azure-beli frissítéséhez szolgál.

A beépített buildsablonok használatához új buildfolyamat létrehozásakor válassza **a Klasszikus szerkesztő használata** tervezősablonok használatával folyamat létrehozásához lehetőséget.

![Válassza ki az Azure Pipelines klasszikus szerkesztőjét](media/functions-how-to-azure-devops/classic-editor.png)

A kód forrásának konfigurálása után keresse meg az Azure Functions build sablonjait. Válassza ki az alkalmazás nyelvének megfelelő sablont.

![Válasszon egy Azure Functions buildsablont](media/functions-how-to-azure-devops/build-templates.png)

Bizonyos esetekben a buildösszetevők adott mappastruktúrával rendelkeznek. Előfordulhat, hogy be kell jelölnie a **Gyökérmappa nevének prepend nevét az elérési utak archiválásához** jelölőnégyzet.

![A gyökérmappa nevének előkészítésére](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript-alkalmazások

Ha a JavaScript-alkalmazás a Windows natív moduljaitól függ, frissítenie kell az ügynökkészlet verzióját **hosted VS2017-re.**

![Az ügynökkészlet verziójának frissítése](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Az alkalmazás telepítése

Amikor új kiadási folyamatot hoz létre, keresse meg az Azure Functions kiadási sablont.

![Az Azure Functions kiadási sablonjának keresése](media/functions-how-to-azure-devops/release-template.png)

A kiadási sablon nem támogatja a központi telepítési helyre való üzembe helyezést.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Build-folyamat létrehozása az Azure CLI használatával

Build-folyamat létrehozásához az Azure-ban használja a `az functionapp devops-pipeline create` [parancsot.](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create) A buildfolyamat jön létre, hogy hozza létre, és engedje el a tárházban végrehajtott kódmódosításokat. A parancs létrehoz egy új YAML-fájlt, amely meghatározza a létrehozási és kiadási folyamatot, majd véglegesíti azt a tártárba. A parancs előfeltételei a kód helyétől függenek.

- Ha a kód a GitHubon van:

    - Írási **write** engedéllyel kell rendelkeznie az előfizetéshez.

    - Az Azure DevOps projektrendszergazdájának kell lennie.

    - Rendelkeznie kell engedélyekkel, hogy hozzon létre egy GitHub személyes hozzáférési jogkivonat (PAT), amely rendelkezik a megfelelő engedélyekkel. További információ: [GitHub PAT engedélykövetelmények.](https://aka.ms/azure-devops-source-repos)

    - Az automatikusan létrehozott YAML-fájl véglegesítéséhez engedéllyel kell rendelkeznie a GitHub-tárházban lévő főágra való véglegesítéshez.

- Ha a kód az Azure Repos:

    - Írási **write** engedéllyel kell rendelkeznie az előfizetéshez.

    - Az Azure DevOps projektrendszergazdájának kell lennie.

## <a name="next-steps"></a>További lépések

- Tekintse át az [Azure Functions áttekintését.](functions-overview.md)
- Tekintse át az [Azure DevOps áttekintését.](/azure/devops/pipelines/)
