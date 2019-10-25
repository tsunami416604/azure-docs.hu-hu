---
title: A kód üzembe helyezése CI/CD-folyamatból GitHub-műveletekkel – Azure App Service | Microsoft Docs
description: Megtudhatja, hogyan használhatja a GitHub-műveleteket a kód App Service való üzembe helyezéséhez
services: app-service
documentationcenter: ''
author: jasonfreeberg
writer: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: jafreebe
ms.openlocfilehash: b7ec1ae1d04fb1dbe16fd9f4a2640b2b3d9584c2
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809772"
---
# <a name="github-actions-for-deploying-to-app-service"></a>A App Service üzembe helyezéséhez szükséges GitHub-műveletek

A [GitHub-műveletek](https://help.github.com/en/articles/about-github-actions) révén rugalmasan hozhat létre automatizált szoftverfejlesztési életciklus-munkafolyamatot. A GitHub Azure App Service műveleteivel automatizálhatja a munkafolyamatot, hogy az [Azure web Appst](https://azure.microsoft.com/services/app-service/web/) a GitHub-műveletek használatával telepítse.

> [!IMPORTANT]
> A GitHub-műveletek jelenleg béta verzióban vannak. Először [regisztrálnia kell, hogy csatlakozzon az előzetes](https://github.com/features/actions) verzióhoz a GitHub-fiók használatával.
> 

A munkafolyamatot egy YAML-fájl (. YML) határozza meg a tárház `/.github/workflows/` útvonalán. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

Az Azure-webalkalmazások munkafolyamataihoz a fájl három szakaszt tartalmaz:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. egyszerű szolgáltatásnév megadása <br /> 2. GitHub-titok létrehozása |
|**Építeni** | 1. a környezet beállítása <br /> 2. a webalkalmazás összeállítása |
|**Üzembe helyezés** | 1. a webalkalmazás üzembe helyezése |

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

[Egyszerű szolgáltatásnév](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) létrehozásához használja az az [ad SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot az [Azure CLI](https://docs.microsoft.com/cli/azure/)-ben. Ezt a parancsot a Azure Portal [Azure Cloud Shell](https://shell.azure.com/) használatával vagy a **kipróbálás** gombra kattintva futtathatja.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

Ebben a példában az erőforrásban található helyőrzőket cserélje le az előfizetés-AZONOSÍTÓra, az erőforráscsoport és a webalkalmazás nevére. A kimenet a szerepkör-hozzárendelés hitelesítő adatai, amelyek hozzáférést biztosítanak a webalkalmazáshoz. Másolja ezt a JSON-objektumot, amelyet a GitHubról történő hitelesítéshez használhat.

> [!NOTE]
> Nem kell létrehoznia egy egyszerű szolgáltatásnevet, ha úgy dönt, hogy közzétételi profilt használ a hitelesítéshez.

> [!IMPORTANT]
> Mindig jó gyakorlat a minimális hozzáférés megadására. Ezért az előző példában szereplő hatókör az adott webalkalmazásra korlátozódik, nem a teljes erőforráscsoporthoz.

## <a name="configure-the-github-secret"></a>A GitHub-titok konfigurálása

Használhatja az alkalmazás szintű hitelesítő adatokat is, például a közzétételi profilt az üzembe helyezéshez. Kövesse a titkos kód konfigurálásához szükséges lépéseket:

1. Töltse le a webalkalmazás közzétételi profilját a portálról a **közzétételi profil beolvasása** lehetőség használatával.

2. A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása** elemet.

    ![titkok](media/app-service-github-actions/secrets.png)

3. Illessze be a letöltött közzétételi profil tartalmát a titok Value mezőjébe.

4. Most az ág munkafolyamat-fájljában: `.github/workflows/workflow.yml` cserélje le az Azure-webalkalmazás üzembe helyezése művelet bemeneti `publish-profile`éhez tartozó titkos kulcsot.
    
    ```yaml
        - uses: azure/webapps-deploy@v1
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. A titkot az alább látható módon fogja látni.

    ![titkok](media/app-service-github-actions/app-service-secrets.png)

## <a name="setup-the-environment"></a>A környezet beállítása

A környezet beállítása a telepítési műveletek egyikével végezhető el.

|**Nyelv**  |**Telepítési művelet**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Az alábbi példák a munkafolyamatnak azt a részét mutatják be, amely a környezetet a különböző támogatott nyelvekhez állítja be:

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>A webalkalmazás összeállítása

Ez a nyelvtől és az Azure Web Apps által támogatott nyelvektől függ. Ez a szakasz az egyes nyelvek standard Build lépései.

Az alábbi példák a webalkalmazást a különböző támogatott nyelveken felépítő munkafolyamat részét mutatják be.

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-the-web-app"></a>A webalkalmazás üzembe helyezése

A kód webalkalmazásba való üzembe helyezéséhez a `Azure/appservice-actions/webapp@master` műveletet kell használnia. Ez a művelet 4 paraméterrel rendelkezik:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **alkalmazás neve** | Szükséges Az Azure-webalkalmazás neve | 
| **közzétételi profil** | Választható Profil fájl tartalmának közzététele a web Deploy Secrets szolgáltatásban |
| **csomag** | Választható Csomag vagy mappa elérési útja. *. zip, *. War, *. jar vagy üzembe helyezni kívánt mappa |
| **tárolóhely neve** | Választható Adja meg az üzemi tárolóhelytől eltérő meglévő tárolóhelyet |

### <a name="deploy-using-publish-profile"></a>Üzembe helyezés a közzétételi profil használatával

Az alábbi példa egy Node. js-Webalkalmazás létrehozása és üzembe helyezése az Azure-ban a közzétételi profil használatával.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v1
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Üzembe helyezés az Azure egyszerű szolgáltatásával

Az alábbi példa a Node. js-webalkalmazások Azure-beli Azure-beli használatával történő létrehozásához és üzembe helyezéséhez használható minta munkafolyamat.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v1
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>Következő lépések

Megtalálhatja a GitHubon különböző adattárakba csoportosított műveleteit, amelyek mindegyike dokumentációt és példákat tartalmaz, amelyek segítséget nyújtanak a GitHub használatához a CI/CD-hez, és az alkalmazások üzembe helyezését az Azure-ban.

- [Azure-bejelentkezés](https://github.com/Azure/actions)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp tárolók számára](https://github.com/Azure/webapps-container-deploy)

- [Docker-bejelentkezés/kijelentkezés](https://github.com/Azure/docker-login)

- [Munkafolyamatokat kiváltó események](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s üzembe helyezése](https://github.com/Azure/k8s-deploy)

- [Kezdő munkafolyamatok](https://github.com/actions/starter-workflows)
