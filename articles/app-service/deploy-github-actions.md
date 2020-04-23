---
title: Ci/CD konfigurálása GitHub-műveletekkel
description: Ismerje meg, hogyan telepítheti a kódot az Azure App Service-be egy CI/CD-folyamatból a GitHub-műveletekkel. Testreszabhatja a buildelési feladatokat, és összetett központi telepítéseket hajthat végre.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 57ca5b0880d4b027e33bc0d01fc6225eb886029b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084991"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Üzembe helyezés az App Service-be a GitHub-műveletek használatával

[A GitHub-műveletek](https://help.github.com/en/articles/about-github-actions) rugalmasságot biztosít egy automatizált szoftverfejlesztési életciklus-munkafolyamat létrehozásához. Az Azure App Service-műveletek a GitHub, automatizálhatja a munkafolyamat üzembe helyezéséhez [az Azure App Service](overview.md) a GitHub-műveletek használatával.

> [!IMPORTANT]
> A GitHub-műveletek jelenleg béta verzióban vannak. Először [regisztrálnia kell, hogy csatlakozzon az előzetes verzióhoz a](https://github.com/features/actions) GitHub-fiókjával.
> 

A munkafolyamatot egy YAML (.yml) fájl `/.github/workflows/` határozza meg a tárház elérési útján. Ez a definíció tartalmazza a munkafolyamatot kifutó különböző lépéseket és paramétereket.

Az Azure App Service-munkafolyamatok esetében a fájl három szakaszból áll:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Egyszerű szolgáltatás definiálása <br /> 2. Hozzon létre egy GitHub-titkot |
|**Felépítés** | 1. A környezet beállítása <br /> 2. A webalkalmazás létrehozása |
|**Telepítés** | 1. A webalkalmazás telepítése |

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az [Azure CLI-ben](https://docs.microsoft.com/cli/azure/)található [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) paranccsal hozhat létre [egyszerű szolgáltatást.](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) Ezt a parancsot az [Azure Cloud Shell](https://shell.azure.com/) használatával futtathatja az Azure Portalon, vagy a Try **it** gombra kattintva.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

Ebben a példában cserélje le az erőforrás helyőrzőit az előfizetés-azonosítóra, az erőforráscsoport nevére és az alkalmazás nevére. A kimenet a szerepkör-hozzárendelési hitelesítő adatok, amelyek hozzáférést biztosítanak az App Service-alkalmazáshoz. Másolja ezt a JSON-objektumot, amellyel hitelesíthető a GitHubról.

> [!NOTE]
> Nem kell egyszerű szolgáltatást létrehoznia, ha úgy dönt, hogy közzétételi profilt használ a hitelesítéshez.

> [!IMPORTANT]
> Mindig jó gyakorlat a minimális hozzáférés megadása. Ez az oka annak, hogy az előző példában szereplő hatókör az adott App Service-alkalmazásra korlátozódik, és nem a teljes erőforráscsoportra.

## <a name="configure-the-github-secret"></a>A GitHub-titok konfigurálása

Alkalmazásszintű hitelesítő adatokat is használhat, azaz közzéteheti a profilt a központi telepítéshez. A titkos adatik beállításához kövesse a lépéseket:

1. Töltse le az App Service-alkalmazás közzétételi profilját a portálról a **Közzétételi profil** használatával.

2. A [GitHubon](https://github.com/)keresse meg a tárházat, válassza a **Beállítások > a Titkok lehetőséget, > új titkos kulcsot adjon hozzá**

    ![Titkok](media/app-service-github-actions/secrets.png)

3. Illessze be a letöltött közzétételi profilfájl tartalmát a titkos adattárba.

4. Most a munkafolyamat-fájlban `.github/workflows/workflow.yml` az ágban: `publish-profile` cserélje ki a titkos kulcsot a központi Azure Web App-művelet bemenete.
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. A titok az alábbiakban látható, ha egyszer definiált.

    ![Titkok](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>A környezet beállítása

A környezet beállítása a telepítési műveletek egyikével végezhető el.

|**Nyelv**  |**Telepítési művelet**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

A következő példák a munkafolyamat nak azt a részét mutatják be, amely a különböző támogatott nyelvek környezetét állítja be:

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

## <a name="build-the-web-app"></a>A webalkalmazás létrehozása

Ez az Azure App Service által támogatott nyelvek nyelvétés nyelvek, ez a szakasz kell a szabványos build lépéseit az egyes nyelvek.

Az alábbi példák a munkafolyamat nak azt a részét mutatják be, amely a webalkalmazást a különböző támogatott nyelveken építi.

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

A kód telepítése egy App Service-alkalmazásban, használja a `azure/webapps-deploy@v2` műveletet. Ennek a műveletnek négy paramétere van:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **alkalmazásneve** | (Kötelező) Az App Service-alkalmazás neve | 
| **közzétételi profil** | (Nem kötelező) Profilfájl tartalmának közzététele webes központi telepítés titkos fájlokkal |
| **Csomag** | (Nem kötelező) A csomag vagy mappa elérési útja. *.zip, *.war, *.jar vagy egy mappát telepíteni |
| **bővítőhely neve** | (Nem kötelező) Adjon meg egy meglévő bővítőhelyet a termelési bővítőhely-en kívül |

### <a name="deploy-using-publish-profile"></a>Telepítés a Közzétételi profil használatával

Az alábbiakban a minta munkafolyamat létrehozásához és üzembe helyezéséhez node.js alkalmazást az Azure-ban közzétételi profil használatával.

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

### <a name="deploy-using-azure-service-principal"></a>Üzembe helyezés az Egyszerű Azure szolgáltatáshasználatával

Az alábbiakban a minta-munkafolyamat létrehozása és üzembe helyezése node.js alkalmazást az Azure-ban egy Azure-szolgáltatás egyszerű használatával.

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

A GitHubon különböző adattárakba csoportosítva megtalálhatja a műveletek készletét, amelyek mindegyike dokumentációt és példákat tartalmaz a GitHub CI/CD-hez való használatának és az alkalmazások Azure-ba való üzembe helyezéséhez.

- [Az Azure-ban üzembe helyezhető műveletek munkafolyamata](https://github.com/Azure/actions-workflow-samples)

- [Azure-bejelentkezés](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp tárolókhoz](https://github.com/Azure/webapps-container-deploy)

- [Docker bejelentkezés/kijelentkezés](https://github.com/Azure/docker-login)

- [Munkafolyamatokat kiváltó események](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s telepíteni](https://github.com/Azure/k8s-deploy)

- [Kezdő munkafolyamatok](https://github.com/actions/starter-workflows)
