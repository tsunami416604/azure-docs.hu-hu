---
title: GitHub-műveletek munkafolyamatai az Azure statikus Web Apps
description: Ismerje meg, hogyan állíthat be folyamatos üzembe helyezést az Azure statikus Web Apps a GitHub-Tárházak használatával.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: chnwamba
ms.openlocfilehash: 44472eb697a4d191d4ed99b7879654fcca61383b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655201"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>GitHub-műveletek munkafolyamatok az Azure statikus Web Apps előzetes verziójában

Új Azure-beli statikus webalkalmazás-erőforrás létrehozásakor az Azure egy GitHub-műveletek munkafolyamatot hoz létre az alkalmazás folyamatos üzembe helyezésének vezérléséhez. A munkafolyamatot egy YAML-fájl vezérli. Ez a cikk a munkafolyamat-fájl szerkezetét és beállításait ismerteti.

A központi telepítéseket [Eseményindítók](#triggers)kezdeményezik, amelyek az egyes [lépések](#steps)által definiált [feladatokat](#jobs) futtatják.

## <a name="file-location"></a>Fájl helye

Amikor a GitHub-tárházat az Azure statikus Web Appshoz csatolja, a rendszer egy munkafolyamat-fájlt ad hozzá a tárházhoz.

A létrehozott munkafolyamat-fájl megtekintéséhez kövesse az alábbi lépéseket.

1. Nyissa meg az alkalmazás tárházát a GitHubon.
1. A _Code (kód_ ) lapon kattintson a `.github/workflows` mappára.
1. Kattintson a fájlra, amelynek a neve hasonlít `azure-static-web-apps-<RANDOM_NAME>.yml` .

A tárházban található YAML-fájl az alábbi példához hasonló lesz:

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
    - uses: actions/checkout@v1
    - name: Build And Deploy
      id: builddeploy
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
        action: 'upload'
        ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
        app_location: '/' # App source code path
        api_location: 'api' # Api source code path - optional
        app_artifact_location: 'dist' # Built app content directory - optional
        ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
    - name: Close Pull Request
      id: closepullrequest
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        action: 'close'
```

## <a name="triggers"></a>Triggerek

Egy GitHub-művelet [elindítja](https://help.github.com/actions/reference/events-that-trigger-workflows) a GitHub-műveletek munkafolyamatot, hogy az eseményindítók alapján futtasson egy feladatot. Az eseményindítók a `on` munkafolyamat-fájl tulajdonsága alapján vannak felsorolva.

```yml
on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master
```

A tulajdonsághoz társított beállításokon keresztül `on` meghatározhatja, hogy mely ágak indítanak el egy feladatot, és hogyan állíthatók be eseményindítók a különböző lekéréses kérelmek állapotára.

Ebben a példában egy munkafolyamatot indít el a _főág_ módosításaival. A munkafolyamatot elindító módosítások közé tartozik a véglegesítések továbbítása és a lekéréses kérelmek megnyitása a kiválasztott ág esetében.

## <a name="jobs"></a>Feladatok

Minden esemény-eseményindítóhoz szükség van egy eseménykezelőre. A [feladatok](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) határozzák meg, hogy mi történjen egy esemény indításakor.

A statikus Web Apps munkafolyamat-fájlban két elérhető feladat van.

| Name  | Description |
|---------|---------|
|`build_and_deploy_job` | Végrehajtja a leküldéses végrehajtást, vagy egy lekéréses kérelmet nyit meg a `on` tulajdonságban felsorolt ág alapján. |
|`close_pull_request_job` | CSAK a lekéréses kérelem lezárásakor hajtható végre. |

## <a name="steps"></a>Lépések

A lépések a feladatok szekvenciális feladatai. A lépések olyan műveleteket hajtanak végre, mint például a függőségek telepítése, a tesztek futtatása és az alkalmazás üzembe helyezése éles környezetben.

A munkafolyamat-fájlok a következő lépéseket határozzák meg.

| Feladat  | Lépések  |
|---------|---------|
| `build_and_deploy_job` |<ol><li>Kikeresi a tárházat a művelet környezetében.<li>Létrehozza és telepíti a tárházat az Azure statikus Web Appsba.</ol>|
| `close_pull_request_job` | <ol><li>Értesíti az Azure statikus Web Apps, hogy egy lekéréses kérelem bezárult.</ol>|

## <a name="build-and-deploy"></a>Létrehozás és üzembe helyezés

A nevű lépés az `Build and Deploy` Azure statikus Web Apps példányára épít és helyez üzembe. A `with` szakasz alatt testreszabhatja az üzemelő példány következő értékeit.

```yml
with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
    repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
    action: 'upload'
    ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
    app_location: '/' # App source code path
    api_location: 'api' # Api source code path - optional
    app_artifact_location: 'dist' # Built app content directory - optional
    ###### End of Repository/Build Configurations ######
```

| Tulajdonság | Description | Kötelező |
|---|---|---|
| `app_location` | Az alkalmazás kódjának helye.<br><br>Adja meg például, `/` hogy az alkalmazás forráskódja a tárház gyökerében található-e, vagy `/app` Ha az alkalmazás kódja egy nevű könyvtárban található `app` . | Igen |
| `api_location` | A Azure Functions kódjának helye.<br><br>Adja meg például a következőt:, `/api` Ha az alkalmazás kódja egy nevű mappában található `api` . Ha nem észleli Azure Functions alkalmazást a mappában, a Build nem sikerül, a munkafolyamat feltételezi, hogy nem szeretne API-t használni. | Nem |
| `app_artifact_location` | A Build kimeneti könyvtárának helye a következőhöz képest: `app_location` .<br><br>Ha például az alkalmazás forráskódja a (z) helyen található `/app` , és a Build szkript a mappába helyezi a fájlt, `/app/build` akkor a értékeként állítsa be a `build` `app_artifact_location` értéket. | Nem |

A `repo_token` , a `action` és az értékeket az `azure_static_web_apps_api_token` Azure statikus Web Apps állítja be, ezért nem szabad manuálisan módosítani.

## <a name="custom-build-commands"></a>Egyéni Build-parancsok

A központi telepítés során futtatott parancsok részletes szabályozása is megadható. A feladatok szakasza a következő parancsokat definiálhatja `with` .

Az üzembe helyezés mindig `npm install` minden egyéni parancs előtt meghívja a-t.

| Parancs            | Description |
|---------------------|-------------|
| `app_build_command` | A statikus tartalom alkalmazásának üzembe helyezése során futtatandó egyéni parancsot határozza meg.<br><br>Például egy szögletes alkalmazás üzemi buildének konfigurálásához adja meg a következőt: `ng build --prod` . Ha üresen hagyja, a munkafolyamat megpróbálja futtatni a `npm run build` vagy a `npm run build:Azure` parancsokat.  |
| `api_build_command` | A Azure Functions API-alkalmazás üzembe helyezése során futtatandó egyéni parancsot határozza meg. |

## <a name="route-file-location"></a>Útvonal fájljának helye

Testreszabhatja a munkafolyamatot, hogy megkeresse a [Routes. JSON](routes.md) fájlt a tárház bármely mappájából. A következő tulajdonság definiálható a feladatok `with` szakasza alatt.

| Tulajdonság            | Description |
|---------------------|-------------|
| `routes_location` | Meghatározza azt a könyvtárat, ahol a _Routes. JSON_ fájl található. Ez a hely a tárház gyökeréhez képest relatív. |

 A _Routes. JSON_ fájl helyével kapcsolatos explicit módon különösen fontos, ha az előtér-keretrendszer létrehozási lépése nem helyezi át ezt a fájlt a `app_artifact_location` alapértelmezésbe.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Lekéréses kérelmek áttekintése az üzem előtti környezetekben](review-publish-pull-requests.md)
