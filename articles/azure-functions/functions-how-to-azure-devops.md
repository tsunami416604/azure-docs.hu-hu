---
title: Folyamatos elérhetővé tétele az Azure DevOps használatával függvény kódfrissítéseket
description: Ismerje meg, hogyan állítható be az Azure Functions célzó Azure DevOps-folyamattal.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: ''
ms.openlocfilehash: ce57aae1119261c0545b59a037226fdc12ec115f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050657"
---
# <a name="continuous-delivery-using-azure-devops"></a>Folyamatos készregyártás az Azure DevOps használatával

A függvény automatikusan telepíthet egy Azure-függvény használatával való [Azure folyamatok](/azure/devops/pipelines/).
A folyamat definiálásához használhatja:

- YAML File: Ez a fájl ismerteti a folyamatot, előfordulhat, hogy egy build lépéseket, és a kiadási szakaszban. A YAML-fájlt kell lennie, mint az alkalmazás az egyazon adattárban lévő.

- Sablonok: Sablonok készen áll a végrehajtott feladatokat hozhat létre, vagy helyezze üzembe az alkalmazást.

## <a name="yaml-based-pipeline"></a>YAML-alapú folyamatok

### <a name="build-your-app"></a>Alkalmazás létrehozása

Az alkalmazás Azure folyamatokban attól függ, hogy az alkalmazás a programozási nyelv.
Minden nyelv rendelkezik az adott létrehozási lépések hozhat létre egy központi telepítési összetevő, amely a függvényalkalmazás Azure-beli üzembe helyezéséhez használható.

#### <a name="net"></a>.NET

A következő minta segítségével hozzon létre egy YAML-fájlt a .NET-alkalmazás létrehozásához.

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
    modifyOutputPath: true
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
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

A következő minta segítségével hozzon létre egy YAML-fájlt a JavaScript-alkalmazást hozhat létre:

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
    name: 'drop'
```

#### <a name="python"></a>Python

A következő minta segítségével hozzon létre egy YAML-fájlt a Python-alkalmazást hozhat létre, a Python csak a Linux Azure Functions támogatott:

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
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```
#### <a name="powershell"></a>PowerShell

A következő minta segítségével hozzon létre egy YAML-fájlt az PowerShell alkalmazás, a PowerShell csak a Windows Azure Functions támogatott:

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
    name: 'drop'
```

### <a name="deploy-your-app"></a>Az alkalmazás üzembe helyezése

Attól függően, a üzemeltetési operációs rendszeren kell felvenni a következő YAML-minta a YAML-fájlt.

#### <a name="windows-function-app"></a>Windows-funkció alkalmazás

A következő kódrészlet használható Windows függvényalkalmazás üzembe helyezése

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
```

#### <a name="linux-function-app"></a>Linux-függvény alkalmazás

A következő kódrészlet használható Linux függvényalkalmazás üzembe helyezése

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
```

## <a name="template-based-pipeline"></a>Sablonalapú folyamat

Azure DevOps, a sablonok olyan feladatokat hozhat létre és helyezhet üzembe egy alkalmazást, előre meghatározott csoportban.

### <a name="build-your-app"></a>Alkalmazás létrehozása

Az alkalmazás Azure folyamatokban attól függ, hogy az alkalmazás a programozási nyelv. Minden nyelv rendelkezik az adott létrehozási lépések hozhat létre egy központi telepítési összetevő, a függvényalkalmazás Azure-ban frissítéséhez használható.
Egy új buildelési folyamat létrehozásakor a beépített build sablonok használatához válasszon **klasszikus szerkesztő segítségével** hozhat létre egy folyamatot, a Tervező sablonok használatával

![Az Azure folyamatok klasszikus szerkesztő](media/functions-how-to-azure-devops/classic-editor.png)

Miután a kód forrását, keresse meg az Azure Functions build sablonokat, és válassza ki a sablont, amely megfelel az alkalmazás nyelvi.

![Az Azure Functions sablonok készítése](media/functions-how-to-azure-devops/build-templates.png)

Bizonyos esetekben a build-összetevőket kell egy adott mappastruktúra, és ellenőrizze, hogy szükség lehet a **Prepend gyökérmappájának neve elérési utak archiválására** lehetőséget.

![Gyökérmappa jogosultságokat](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript-alkalmazásokhoz

Ha a JavaScript-alkalmazását Windows natív modulok függőség van, frissíteni kell:

- Az Ügynökkészlet verziójára **Hosted VS2017**

  ![Fordító-ügynökhöz az operációs rendszer módosítása](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Az alkalmazás üzembe helyezése

Amikor egy új kiadási folyamatot hoz létre, keresse meg az Azure Functions kiadási sablon.

![](media/functions-how-to-azure-devops/release-template.png)

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>Az Azure CLI használatával az Azure-folyamat létrehozása

Használatával a `az functionapp devops-pipeline create` [parancs](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create), egy Azure-folyamatot a rendszer létrehozza a készítése és kiadása a tárházon belüli kódváltozások. A parancs egy új YAML-fájlt, amely meghatározza a buildelési és kiadási folyamatok létrehozásához, és a véglegesítheti az adattárhoz.
Ez a parancs előfeltételei attól függ, hogy a kód helyét:

- Ha a kódját a Githubon:

    - Rendelkeznie kell **írási** engedélyt az előfizetéshez.

    - Ön az Azure DevOps project rendszergazdájának.

    - A megfelelő engedélyekkel a GitHub személyes hozzáférési jogkivonat létrehozásához szükséges engedéllyel rendelkezik. [GitHub PAT engedélye követelményeit.](https://aka.ms/azure-devops-source-repos)

    - Rendelkezik engedéllyel, hogy a főágban, a GitHub-adattárában véglegesítéséhez automatikusan létrehozott YAML-fájlt.

- Ha a kódot az Azure-kódtárak:

    - Rendelkeznie kell **írási** engedélyt az előfizetéshez.

    - Ön az Azure DevOps project rendszergazdájának.

## <a name="next-steps"></a>További lépések

+ [Az Azure Functions áttekintése](functions-overview.md)
+ [Az Azure DevOps – áttekintés](/azure/devops/pipelines/)
