---
title: CI/CD konfigurálása GitHub-műveletekkel
description: Megtudhatja, hogyan helyezheti üzembe a kódot Azure App Service egy CI/CD-folyamatból a GitHub-műveletekkel. Testreszabhatja a felépítési feladatokat, és összetett központi telepítéseket hajthat végre.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: tracking-python
ms.openlocfilehash: b40da0c8746bc63a99394027b61d777a611727e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84559590"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Üzembe helyezés a App Service GitHub-műveletek használatával

A [GitHub-műveletek](https://help.github.com/en/articles/about-github-actions) révén rugalmasan hozhat létre automatizált szoftverfejlesztési életciklus-munkafolyamatot. A GitHub Azure App Service műveleteivel automatizálhatja a munkafolyamatot, hogy a GitHub-műveletek használatával [Azure app Service](overview.md) üzembe helyezését.

> [!IMPORTANT]
> A GitHub-műveletek jelenleg béta verzióban vannak. Először [regisztrálnia kell, hogy csatlakozzon az előzetes](https://github.com/features/actions) verzióhoz a GitHub-fiók használatával.
> 

A munkafolyamatot egy YAML-(. YML) fájl határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

Azure App Service munkafolyamathoz a fájl három szakaszt tartalmaz:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. egyszerű szolgáltatásnév megadása <br /> 2. GitHub-titok létrehozása |
|**Felépítés** | 1. a környezet beállítása <br /> 2. a webalkalmazás összeállítása |
|**Telepítés** | 1. a webalkalmazás üzembe helyezése |

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

[Egyszerű szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) létrehozásához használja az az [ad SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot az [Azure CLI](https://docs.microsoft.com/cli/azure/)-ben. Ezt a parancsot a Azure Portal [Azure Cloud Shell](https://shell.azure.com/) használatával vagy a **kipróbálás** gombra kattintva futtathatja.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

Ebben a példában az erőforrásban található helyőrzőket cserélje le az előfizetés-AZONOSÍTÓra, az erőforráscsoport nevére és az alkalmazás nevére. A kimenet a szerepkör-hozzárendelés hitelesítő adatai, amelyek hozzáférést biztosítanak a App Service alkalmazáshoz. Másolja ezt a JSON-objektumot, amelyet a GitHubról történő hitelesítéshez használhat.

> [!NOTE]
> Nem kell létrehoznia egy egyszerű szolgáltatásnevet, ha úgy dönt, hogy közzétételi profilt használ a hitelesítéshez.

> [!IMPORTANT]
> Mindig jó gyakorlat a minimális hozzáférés megadására. Ezért az előző példában szereplő hatókör az adott App Service alkalmazásra korlátozódik, és nem a teljes erőforráscsoporthoz.

## <a name="configure-the-github-secret"></a>A GitHub-titok konfigurálása

Használhatja az alkalmazás szintű hitelesítő adatokat is, például a közzétételi profilt az üzembe helyezéshez. Kövesse a titkos kód konfigurálásához szükséges lépéseket:

1. Töltse le a App Service alkalmazás közzétételi profilját a portálon a **közzétételi profil beolvasása** lehetőség használatával.

2. A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása** elemet.

    ![titkok](media/app-service-github-actions/secrets.png)

3. Illessze be a letöltött közzétételi profil tartalmát a titok Value mezőjébe.

4. Most az ág munkafolyamat-fájljában: `.github/workflows/workflow.yml` cserélje le az `publish-profile` Azure-webalkalmazás üzembe helyezése művelethez tartozó titkos kulcsot.
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. A titkos kulcsot az alább látható módon láthatja.

    ![titkok](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>A környezet beállítása

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

Ez a Azure App Service által támogatott nyelvtől és nyelvtől függ. a szakasznak az egyes nyelvek standard Build lépéseinek kell lennie.

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
## <a name="deploy-to-app-service"></a>Üzembe helyezés az App Service-ben

A kód App Service alkalmazásba való üzembe helyezéséhez használja a `azure/webapps-deploy@v2` műveletet. A műveletnek négy paramétere van:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **alkalmazás neve** | Szükséges A App Service alkalmazás neve | 
| **közzétételi profil** | Választható Profil fájl tartalmának közzététele a web Deploy Secrets szolgáltatásban |
| **csomag** | Választható Csomag vagy mappa elérési útja. *. zip, *. War, *. jar vagy üzembe helyezni kívánt mappa |
| **tárolóhely neve** | Választható Adja meg az üzemi tárolóhelytől eltérő meglévő tárolóhelyet |

### <a name="deploy-using-publish-profile"></a>Üzembe helyezés a közzétételi profil használatával

Alább látható egy Node.js-alkalmazás Azure-ban való létrehozásához és üzembe helyezéséhez szükséges minta munkafolyamat a közzétételi profil használatával.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
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
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Üzembe helyezés az Azure egyszerű szolgáltatásával

Az alábbi példa egy Azure-szolgáltatásnév használatával Node.js alkalmazás Azure-beli létrehozásához és üzembe helyezéséhez használható minta munkafolyamat.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
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
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>További lépések

Megtalálhatja a GitHubon különböző adattárakba csoportosított műveleteit, amelyek mindegyike dokumentációt és példákat tartalmaz, amelyek segítséget nyújtanak a GitHub használatához a CI/CD-hez, és az alkalmazások üzembe helyezését az Azure-ban.

- [Az Azure-ba telepítendő műveletek munkafolyamata](https://github.com/Azure/actions-workflow-samples)

- [Azure-bejelentkezés](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp tárolók számára](https://github.com/Azure/webapps-container-deploy)

- [Docker-bejelentkezés/kijelentkezés](https://github.com/Azure/docker-login)

- [Munkafolyamatokat kiváltó események](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s üzembe helyezése](https://github.com/Azure/k8s-deploy)

- [Kezdő munkafolyamatok](https://github.com/actions/starter-workflows)
