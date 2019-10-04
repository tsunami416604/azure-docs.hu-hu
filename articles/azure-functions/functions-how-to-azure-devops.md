---
title: Folyamatos elérhetővé tétele függvény kódfrissítéseket Azure DevOps - az Azure Functions használatával
description: Ismerje meg, hogyan állítható be az Azure DevOps-folyamat, amelynek a célja az Azure Functions.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 0fdad0caa2deef0d7d55b30a85632f72f4ff0ecc
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594464"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Folyamatos készregyártás az Azure DevOps használatával

Automatikusan telepítheti a függvény alkalmazásokhoz az Azure Functions használatával [Azure folyamatok](/azure/devops/pipelines/).

Meghatározása a folyamatban, két lehetősége van:

- **YAML-fájl**: Egy YAML-fájlt a folyamatot ismerteti. Előfordulhat, hogy a fájl egy build lépéseket és a kiadási szakaszban. A YAML-fájlt kell lennie, mint az alkalmazás az egyazon adattárban lévő.
- **Sablon**: A sablonok olyan előre elkészített feladatokat hozhat létre, vagy helyezze üzembe az alkalmazást.

## <a name="yaml-based-pipeline"></a>YAML-alapú folyamatok

Hozzon létre egy YAML-alapú folyamatot, először hozza létre az alkalmazását, és majd az alkalmazás telepítése.

### <a name="build-your-app"></a>Alkalmazás létrehozása

Hogyan hozhat létre az alkalmazás Azure folyamatokban attól függ, hogy az alkalmazás programozási nyelvet. Minden nyelv, amely egy központi telepítési összetevő létrehozása adott létrehozási lépések rendelkezik. A központi telepítési összetevő az Azure-ban a függvényalkalmazás üzembe helyezésére szolgál.

#### <a name="net"></a>.NET

A következő minta segítségével hozzon létre egy YAML-fájlt a .NET-alkalmazás készítése:

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

A következő minta segítségével hozzon létre egy YAML-fájlt hozhat létre egy JavaScript-alkalmazást:

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

A következő minta segítségével hozzon létre egy YAML-fájlt hozhat létre egy Python-alkalmazást. Amely csak a Linux Azure Functions támogatja a Pythont.

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

A következő minta segítségével hozzon létre egy YAML-fájlt a csomaghoz, egy PowerShell-alkalmazást. PowerShell csak a Windows Azure Functions esetében támogatott.

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

Szerepelnie kell a következő YAML-minták egyikét, attól függően, a üzemeltetési operációs rendszer a YAML-fájlt.

#### <a name="windows-function-app"></a>Windows-függvényalkalmazás

Az alábbi kódrészlet használatával függvény Windows-alkalmazás üzembe helyezése:

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

#### <a name="linux-function-app"></a>A függvényalkalmazás Linux

Az alábbi kódrészlet használatával függvényalkalmazás üzembe helyezése Linux:

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

Sablonok az Azure fejlesztési és üzemeltetési feladatokat hozhat létre és helyezhet üzembe egy alkalmazást, előre definiált csoportjai.

### <a name="build-your-app"></a>Alkalmazás létrehozása

Hogyan hozhat létre az alkalmazás Azure folyamatokban attól függ, hogy az alkalmazás programozási nyelvet. Minden nyelv, amely egy központi telepítési összetevő létrehozása adott létrehozási lépések rendelkezik. A központi telepítési összetevő segítségével frissítse a függvényalkalmazást az Azure-ban.

Beépített build sablonok használatával, amikor létrehoz egy új build folyamatot, válassza ki a **klasszikus szerkesztő segítségével** létrehoz egy folyamatot a Tervező sablonok használatával.

![Válassza ki az Azure-folyamatok klasszikus szerkesztő](media/functions-how-to-azure-devops/classic-editor.png)

Miután konfigurálta a kód forrását, keresse meg az Azure Functions sablonokkal hozhat létre. Válassza ki a sablont, amely megfelel a nyelve.

![Az Azure Functions buildsablon kiválasztása](media/functions-how-to-azure-devops/build-templates.png)

Bizonyos esetekben a build-összetevőket egy adott mappájába struktúrával rendelkeznek. Előfordulhat, hogy ki kell választania a **Prepend gyökérmappájának neve elérési utak archiválására** jelölőnégyzetet.

![A beállítás illesztenie a legfelső szintű mappa neve](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript-alkalmazásokhoz

Ha a JavaScript-alkalmazását Windows natív modulok maga, frissítenie kell a készlet ügynökverzió, **Hosted VS2017**.

![Az ügynök készlet verziójának frissítése](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Az alkalmazás üzembe helyezése

Amikor egy új kiadási folyamatot hoz létre, keressen rá az Azure Functions kiadási sablon.

![Keresse meg az Azure Functions kiadási sablon](media/functions-how-to-azure-devops/release-template.png)

A kiadási sablon üzembe helyezése egy üzembe helyezési pont nem támogatott.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Felépítési folyamat létrehozása az Azure CLI-vel

Felépítési folyamat létrehozása az Azure-ban, használja a `az functionapp devops-pipeline create` [parancs](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). A létrehozási folyamat készítése és kiadása a tárházban végzett kódváltozások kell létrehozni. A parancs létrehoz egy új YAML-fájlt, amely meghatározza a build és kiadás folyamat, és majd véglegesítések, az adattárhoz. Ez a parancs előfeltételei a kód helyét függenek.

- Ha a kódját a Githubon:

    - Rendelkeznie kell **írási** az előfizetéshez tartozó engedélyeket.

    - Az Azure DevOps project rendszergazdájának kell lennie.

    - Hozzon létre egy GitHub-személyes hozzáférési jogkivonat (PAT), amely jogosult engedéllyel kell rendelkeznie. További információkért lásd: [GitHub PAT engedélye követelményeit.](https://aka.ms/azure-devops-source-repos)

    - Hogy a főágban, a GitHub-adattárban, az automatikusan létrehozott YAML-fájl véglegesítheti engedélyekkel kell rendelkeznie.

- Ha a kódot az Azure-kódtárak:

    - Rendelkeznie kell **írási** az előfizetéshez tartozó engedélyeket.

    - Az Azure DevOps project rendszergazdájának kell lennie.

## <a name="next-steps"></a>További lépések

- Tekintse át a [Azure Functions áttekintő](functions-overview.md).
- Tekintse át a [Azure DevOps – áttekintés](/azure/devops/pipelines/).
