---
title: A Function Code frissítéseinek folyamatos kézbesítése az Azure DevOps-Azure Functions használatával
description: Megtudhatja, hogyan állíthat be olyan Azure DevOps-folyamatot, amely Azure Functions célozza meg.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 4fb01eac53151799a0def00d13f18619faf437f6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72927528"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Folyamatos kézbesítés az Azure DevOps használatával

A függvényt automatikusan üzembe helyezheti egy Azure Functions alkalmazásban az [Azure-folyamatok](/azure/devops/pipelines/)használatával.

A folyamat definiálásának két lehetősége van:

- **YAML-fájl**: egy YAML-fájl leírja a folyamatot. Lehetséges, hogy a fájl létrehozási lépések szakasza és a kiadás szakasza. A YAML-fájlnak ugyanabban a tárházban kell lennie, mint az alkalmazásnak.
- **Sablon**: a sablonok készen állnak az alkalmazás létrehozására vagy üzembe helyezésére.

## <a name="yaml-based-pipeline"></a>YAML-alapú folyamat

YAML-alapú folyamat létrehozásához először létre kell hoznia az alkalmazást, majd telepítenie kell az alkalmazást.

### <a name="build-your-app"></a>Alkalmazás létrehozása

Az alkalmazás Azure-folyamatokban való létrehozása az alkalmazás programozási nyelvtől függ. Az egyes nyelveken olyan speciális összeállítási lépések vannak, amelyek üzembe helyezési összetevőt hoznak létre. Az üzembe helyezési összetevő használatával üzembe helyezhetők a Function alkalmazás az Azure-ban.

#### <a name="net"></a>.NET

A következő minta használatával létrehozhat egy YAML-fájlt egy .NET-alkalmazás létrehozásához:

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

A következő minta használatával létrehozhat egy YAML-fájlt egy JavaScript-alkalmazás létrehozásához:

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

A következő minta használatával YAML-fájlt hozhat létre egy Python-alkalmazás létrehozásához. A Python csak Linux Azure Functions esetén támogatott. A Python 3,7-es verziójának YAML a 3,6-es és a 3,7-as összes példányának lecserélése ezzel a YAML.

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

A következő minta használatával létrehozhat egy YAML-fájlt egy PowerShell-alkalmazás előkészítéséhez. A PowerShell csak Windows Azure Functions esetén támogatott.

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

Az üzemeltetési operációs rendszertől függően az alábbi YAML-minták egyikét kell tartalmaznia a YAML-fájlban.

#### <a name="windows-function-app"></a>Windows-függvény alkalmazás

A következő kódrészlet használatával telepítheti a Windows Function alkalmazást:

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

#### <a name="linux-function-app"></a>Linuxos Function-alkalmazás

A Linux-függvények alkalmazásának üzembe helyezéséhez a következő kódrészletet használhatja:

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

## <a name="template-based-pipeline"></a>Sablon alapú folyamat

Az Azure DevOps lévő sablonok előre definiált, alkalmazások létrehozására vagy üzembe helyezésére szolgáló tevékenységek.

### <a name="build-your-app"></a>Alkalmazás létrehozása

Az alkalmazás Azure-folyamatokban való létrehozása az alkalmazás programozási nyelvtől függ. Az egyes nyelveken olyan speciális összeállítási lépések vannak, amelyek üzembe helyezési összetevőt hoznak létre. Az üzembe helyezési összetevő segítségével frissítheti a Function alkalmazást az Azure-ban.

Ha beépített Build-sablonokat szeretne használni, hozzon létre egy új összeállítási folyamatot, és válassza **a klasszikus szerkesztő használata** folyamat létrehozásához tervezői sablonok használatával lehetőséget.

![Válassza ki az Azure-folyamatok klasszikus szerkesztőjét](media/functions-how-to-azure-devops/classic-editor.png)

A kód forrásának konfigurálása után keressen rá Azure Functions Build sablonok elemre. Válassza ki azt a sablont, amely megfelel az alkalmazás nyelvének.

![Azure Functions létrehozási sablon kiválasztása](media/functions-how-to-azure-devops/build-templates.png)

Bizonyos esetekben az összetevők kiépítése egy adott mappastruktúrát is tartalmaz. Előfordulhat, hogy ki kell jelölnie a kiindulási **gyökérmappa nevét az archiválási elérési utakhoz** jelölőnégyzet.

![A gyökérmappa nevének megadására szolgáló lehetőség](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript-alkalmazások

Ha a JavaScript-alkalmazás függőséget tartalmaz a Windows natív moduljaival, frissítenie kell az ügynök készletének verzióját az **üzemeltetett VS2017**.

![Az ügynök-készlet verziójának frissítése](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Az alkalmazás üzembe helyezése

Új kiadási folyamat létrehozásakor keresse meg a Azure Functions kiadási sablont.

![A Azure Functions kiadási sablon keresése](media/functions-how-to-azure-devops/release-template.png)

A kiadási sablon nem támogatja az üzembe helyezést az üzembe helyezési pontokon.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Build folyamat létrehozása az Azure CLI használatával

Az Azure-beli build-folyamat létrehozásához használja a `az functionapp devops-pipeline create` [parancsot](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). A létrehozási folyamat úgy jön létre, hogy felépítse és felszabadítsa a tárházban végrehajtott bármely kód módosításait. A parancs létrehoz egy új YAML-fájlt, amely meghatározza a létrehozási és a kiadási folyamatot, majd véglegesíti azt a tárházban. A parancs előfeltételei a kód helyétől függenek.

- Ha a kód a GitHubban található:

    - Az előfizetéséhez **írási** engedéllyel kell rendelkeznie.

    - A projekt rendszergazdájának kell lennie az Azure DevOps-ben.

    - A megfelelő engedélyekkel rendelkező GitHub személyes hozzáférési jogkivonat (PAT) létrehozásához engedélyekkel kell rendelkeznie. További információ: [GITHUB Pat engedélyekre vonatkozó követelmények.](https://aka.ms/azure-devops-source-repos)

    - Az automatikusan létrehozott YAML-fájl elvégzéséhez jogosultnak kell lennie arra, hogy véglegesítse a fő ágat a GitHub-tárházban.

- Ha a kódja az Azure Reposban van:

    - Az előfizetéséhez **írási** engedéllyel kell rendelkeznie.

    - A projekt rendszergazdájának kell lennie az Azure DevOps-ben.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [Azure functions áttekintését](functions-overview.md).
- Tekintse át az [Azure DevOps áttekintését](/azure/devops/pipelines/).
