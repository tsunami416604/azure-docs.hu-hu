---
title: CI/CD konfigurálása GitHub-műveletekkel
description: Megtudhatja, hogyan helyezheti üzembe a kódot Azure App Service egy CI/CD-folyamatból a GitHub-műveletekkel. Testreszabhatja a felépítési feladatokat, és összetett központi telepítéseket hajthat végre.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python
ms.openlocfilehash: 54e4ce409eb9f2a6bedd7861b3e268311f886b49
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273245"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Üzembe helyezés a App Service GitHub-műveletek használatával

A [GitHub-műveletek](https://help.github.com/en/articles/about-github-actions) révén rugalmasan hozhat létre automatizált szoftverfejlesztési életciklus-munkafolyamatot. A GitHub Azure App Service műveleteivel automatizálhatja a munkafolyamatot, hogy a GitHub-műveletek használatával [Azure app Service](overview.md) üzembe helyezését.

> [!IMPORTANT]
> A GitHub-műveletek jelenleg béta verzióban vannak. Először [regisztrálnia kell, hogy csatlakozzon az előzetes](https://github.com/features/actions) verzióhoz a GitHub-fiók használatával.
> 

A munkafolyamatot egy YAML-(. YML) fájl határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

## <a name="use-the-deployment-center"></a>A központi telepítési központ használata

Gyorsan megkezdheti a GitHub-műveleteket a App Service Deployment Center használatával. Ez automatikusan létrehozza a munkafolyamat-fájlt az alkalmazás verem alapján, és véglegesíti azt a GitHub-tárházban a megfelelő könyvtárban.

1. Navigáljon a webapphoz az Azure Portalon
1. A bal oldalon kattintson a **központi telepítési központ** elemre.
1. A **folyamatos üzembe helyezés (CI/CD)** alatt válassza a **GitHub** elemet.
1. Ezután válassza a **GitHub-műveletek** elemet.
1. A legördülő lista használatával kiválaszthatja a GitHub-tárházat, az ág és az alkalmazás veremét
    - Ha a kiválasztott ág védett, továbbra is hozzáadhatja a munkafolyamat-fájlt. A folytatás előtt tekintse át a fiókirodák védelmét.
1. Az utolsó képernyőn áttekintheti a beállításokat, és megtekintheti a tárházban véglegesíteni kívánt munkafolyamat-fájlt. Ha a beállítások helyesek, kattintson a **Befejezés** gombra.

Ezzel véglegesíti a munkafolyamat-fájlt a tárházban. Az alkalmazás létrehozásához és üzembe helyezéséhez szükséges munkafolyamat azonnal elindul.

## <a name="add-the-workflow-manually"></a>Manuálisan adja hozzá a munkafolyamatot

Azure App Service munkafolyamathoz a fájl három szakaszt tartalmaz:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Adjon meg egy szolgáltatásnevet. <br /> 2. hozzon létre egy GitHub-titkot. |
|**Létrehozás** | 1. Állítsa be a környezetet. <br /> 2. hozza létre a webalkalmazást. |
|**Telepítés** | 1. Telepítse a webalkalmazást. |

## <a name="generate-deployment-credentials"></a>Központi telepítési hitelesítő adatok előállítása

# <a name="user-level-credentials"></a>[Felhasználói szintű hitelesítő adatok](#tab/userlevel)

[Egyszerű szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) létrehozásához használja az az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot az [Azure CLI](/cli/azure/)-ben. Ezt a parancsot a Azure Portal [Azure Cloud Shell](https://shell.azure.com/) használatával vagy a **kipróbálás** gombra kattintva futtathatja.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

A fenti példában cserélje le a helyőrzőket az előfizetés-AZONOSÍTÓra, az erőforráscsoport nevére és az alkalmazás nevére. A kimenet egy JSON-objektum, amely a szerepkör-hozzárendelés hitelesítő adatait tartalmazza, amelyek hozzáférést biztosítanak a App Service alkalmazáshoz az alábbihoz hasonló módon. A JSON-objektum másolása később.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Mindig jó gyakorlat a minimális hozzáférés megadására. Az előző példában szereplő hatókör az adott App Service alkalmazásra korlátozódik, nem a teljes erőforráscsoporthoz.

# <a name="app-level-credentials"></a>[Alkalmazás szintű hitelesítő adatok](#tab/applevel)

Az alkalmazás-szintű hitelesítő adatok az alkalmazás közzétételi profiljának használatával használhatók. Nyissa meg az alkalmazás felügyeleti lapját a portálon. Az **Áttekintés** lapon kattintson a **közzétételi profil beolvasása** lehetőségre.

Később szüksége lesz a fájl tartalmára.

---

## <a name="configure-the-github-secret"></a>A GitHub-titok konfigurálása

# <a name="user-level-credentials"></a>[Felhasználói szintű hitelesítő adatok](#tab/userlevel)

A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása**lehetőséget.

[Felhasználói szintű hitelesítő adatok](#generate-deployment-credentials)használatához illessze be a teljes JSON-kimenetet az Azure CLI-parancsból a titkos kulcs érték mezőjébe. Adja meg a titkot a nevet, például: `AZURE_CREDENTIALS` .

Amikor később konfigurálja a munkafolyamat-fájlt, az `creds` Azure bejelentkezési művelethez tartozó titkos kulcsot használja. Példa:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

# <a name="app-level-credentials"></a>[Alkalmazás szintű hitelesítő adatok](#tab/applevel)

A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása**lehetőséget.

Az [alkalmazás szintű hitelesítő adatok](#generate-deployment-credentials)használatához illessze be a letöltött közzétételi profil tartalmát a titkos kulcs érték mezőjébe. Adja meg a titkot a nevet, például: `azureWebAppPublishProfile` .

Ha később konfigurálja a munkafolyamat-fájlt, az `publish-profile` Azure-webalkalmazás üzembe helyezése művelet titkát kell használnia. Példa:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

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

# <a name="user-level-credentials"></a>[Felhasználói szintű hitelesítő adatok](#tab/userlevel)

Az alábbi példa egy Azure-szolgáltatásnév használatával Node.js alkalmazás Azure-beli létrehozásához és üzembe helyezéséhez használható minta munkafolyamat. Figyelje meg, hogy a `creds` bemenet a `AZURE_CREDENTIALS` korábban létrehozott titokra hivatkozik.

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

# <a name="app-level-credentials"></a>[Alkalmazás szintű hitelesítő adatok](#tab/applevel)

Az alábbi példa egy Node.js alkalmazás Azure-ba való felépítésének és üzembe helyezésének folyamata az alkalmazás közzétételi profiljának használatával. Figyelje meg, hogy a `publish-profile` bemenet a `azureWebAppPublishProfile` korábban létrehozott titokra hivatkozik.

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

---

## <a name="next-steps"></a>Következő lépések

Megtalálhatja a GitHubon különböző adattárakba csoportosított műveleteit, amelyek mindegyike dokumentációt és példákat tartalmaz, amelyek segítséget nyújtanak a GitHub használatához a CI/CD-hez, és az alkalmazások üzembe helyezését az Azure-ban.

- [Az Azure-ba telepítendő műveletek munkafolyamatai](https://github.com/Azure/actions-workflow-samples)

- [Azure-bejelentkezés](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp tárolók számára](https://github.com/Azure/webapps-container-deploy)

- [Docker-bejelentkezés/kijelentkezés](https://github.com/Azure/docker-login)

- [Munkafolyamatokat kiváltó események](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s üzembe helyezése](https://github.com/Azure/k8s-deploy)

- [Kezdő munkafolyamatok](https://github.com/actions/starter-workflows)