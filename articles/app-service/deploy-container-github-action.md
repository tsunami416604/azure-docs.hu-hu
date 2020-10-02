---
title: Egyéni tároló CI/CD a GitHub-műveletekből
description: Megtudhatja, hogyan helyezhet üzembe egyéni linuxos tárolót a GitHub-műveletek használatával App Service egy CI/CD-folyamatból.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 59bfdbf2d78497bc253f466e94fd69367a85070d
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631783"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Egyéni tároló üzembe helyezése a GitHub-műveletek használatával App Service

A [GitHub-műveletek](https://help.github.com/en/articles/about-github-actions) révén rugalmasan hozhat létre automatizált szoftverfejlesztői munkafolyamatokat. Az [Azure web Deploy művelettel](https://github.com/Azure/webapps-deploy)automatizálhatja a munkafolyamatot, hogy egyéni tárolókat helyezzen üzembe [app Service](overview.md) a GitHub-műveletek használatával.

A munkafolyamatot egy YAML-(. YML) fájl határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamat különböző lépéseit és paramétereit tartalmazza.

Azure App Service tároló munkafolyamathoz a fájl három szakaszt tartalmaz:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. az egyszerű szolgáltatásnév vagy a közzétételi profil beolvasása. <br /> 2. hozzon létre egy GitHub-titkot. |
|**Létrehozás** | 1. hozza létre a környezetet. <br /> 2. hozza létre a tároló rendszerképét. |
|**Telepítés** | 1. Telepítse a tároló lemezképét. |

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Egy GitHub-fiók. Ha még nem rendelkezik ilyennel, regisztráljon [ingyenesen](https://github.com/join).  
- Egy működő tároló-beállításjegyzék és Azure App Service-alkalmazás tárolók számára. Ez a példa Azure Container Registry használ. 
    - [Megtudhatja, hogyan hozhat létre egy tárolóval Node.js alkalmazást a Docker használatával, leküldheti a tároló lemezképét egy beállításjegyzékbe, majd üzembe helyezheti a rendszerképet Azure App Service](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01)

## <a name="generate-deployment-credentials"></a>Központi telepítési hitelesítő adatok előállítása

Az Azure App Services a GitHub-műveletekhez való hitelesítésének ajánlott módja a közzétételi profil. A hitelesítést egy egyszerű szolgáltatással is elvégezheti, de a folyamat további lépéseket igényel. 

Mentse a közzétételi profil hitelesítő adatait vagy egyszerű szolgáltatásnevet [GitHub-titokként](https://docs.github.com/en/actions/reference/encrypted-secrets) az Azure-beli hitelesítéshez. A titkos kulcsot a munkafolyamaton belül érheti el. 

# <a name="publish-profile"></a>[Profil közzététele](#tab/publish-profile)

A közzétételi profil egy alkalmazás szintű hitelesítő adat. A közzétételi profil beállítása GitHub-titokként. 

1. Nyissa meg az App Service-t a Azure Portal. 

1. Az **Áttekintés** lapon válassza a **közzétételi profil beolvasása**elemet.

1. Mentse a letöltött fájlt. A fájl tartalmát a GitHub-titok létrehozásához fogja használni.

# <a name="service-principal"></a>[Egyszerű szolgáltatásnév](#tab/service-principal)

Az [Azure CLI](/cli/azure/)-ben létrehozhat egy [egyszerű szolgáltatást](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) az az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) parancs használatával. Futtassa ezt a parancsot [Azure Cloud Shell](https://shell.azure.com/) a Azure Portalban, vagy kattintson a **TRY IT (kipróbálás** ) gombra.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

A példában cserélje le a helyőrzőket az előfizetés-AZONOSÍTÓra, az erőforráscsoport nevére és az alkalmazás nevére. A kimenet egy JSON-objektum, amelynek a szerepkör-hozzárendelési hitelesítő adatai biztosítják a hozzáférést a App Service alkalmazáshoz. A JSON-objektum másolása később.

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

---

## <a name="configure-the-github-secret"></a>A GitHub-titok konfigurálása

A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása**lehetőséget.

Illessze be a JSON-kimenet tartalmát a titkos változó értékeként. Adja meg a titkot a nevet, például: `AZURE_CREDENTIALS` .

Amikor később konfigurálja a munkafolyamat-fájlt, az `creds` Azure bejelentkezési művelethez tartozó titkos kulcsot használja. Például:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="configure-the-github-secret-for-authentication"></a>A GitHub-titok konfigurálása a hitelesítéshez

# <a name="publish-profile"></a>[Profil közzététele](#tab/publish-profile)

A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása**lehetőséget.

Az [alkalmazás szintű hitelesítő adatok](#generate-deployment-credentials)használatához illessze be a letöltött közzétételi profil tartalmát a titkos kulcs érték mezőjébe. Nevezze el a titkot `AZURE_WEBAPP_PUBLISH_PROFILE` .

A GitHub-munkafolyamatok konfigurálásakor használja az `AZURE_WEBAPP_PUBLISH_PROFILE` Azure-webalkalmazás üzembe helyezése műveletet. Például:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Egyszerű szolgáltatásnév](#tab/service-principal)

A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása**lehetőséget.

[Felhasználói szintű hitelesítő adatok](#generate-deployment-credentials)használatához illessze be a teljes JSON-kimenetet az Azure CLI-parancsból a titkos kulcs érték mezőjébe. Adja meg a titkot a nevet, például: `AZURE_CREDENTIALS` .

Amikor később konfigurálja a munkafolyamat-fájlt, az `creds` Azure bejelentkezési művelethez tartozó titkos kulcsot használja. Például:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>GitHub-titkok konfigurálása a beállításjegyzékhez

Adja meg a Docker bejelentkezési művelettel használni kívánt titkokat. 

1. Nyissa meg a tárolót a Azure Portal vagy a Docker-ben, és másolja a felhasználónevet és a jelszót. 

2. Adjon meg egy új titkot a nevű beállításjegyzékbeli felhasználónévhez `REGISTRY_USERNAME` . 

3. Adjon meg egy új titkot a nevű beállításjegyzék-jelszóhoz `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>A tároló rendszerképének összeállítása

Az alábbi példa egy Node.JS Docker-rendszerképet felépítő munkafolyamat egy részét mutatja be. A [Docker-bejelentkezés](https://github.com/azure/docker-login) használatával jelentkezzen be egy privát tároló-beállításjegyzékbe. Ez a példa Azure Container Registry használ, de ugyanez a művelet más beállításjegyzékek esetében is működik. 

# <a name="publish-profile"></a>[Profil közzététele](#tab/publish-profile)

Ebből a példából megtudhatja, hogyan hozhat létre Node.JS Docker-rendszerképet egy közzétételi profil használatával a hitelesítéshez.


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

A [Docker-bejelentkezés](https://github.com/azure/docker-login) használatával egyszerre több tároló-beállításjegyzékbe is bejelentkezhet. Ez a példa két új GitHub-titkot tartalmaz a docker.io-vel történő hitelesítéshez.

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```
# <a name="service-principal"></a>[Egyszerű szolgáltatásnév](#tab/service-principal)

Ez a példa bemutatja, hogyan hozhat létre Node.JS Docker-rendszerképet egy egyszerű szolgáltatásnév használatával a hitelesítéshez. 

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master

    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}   
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}  
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}      
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="deploy-to-an-app-service-container"></a>Üzembe helyezés App Service tárolón

Ha a lemezképet a App Serviceban lévő egyéni tárolóba szeretné telepíteni, használja a `azure/webapps-deploy@v2` műveletet. Ehhez a művelethez öt paraméter tartozik:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **alkalmazás neve** | Szükséges A App Service alkalmazás neve | 
| **közzétételi profil** | Választható Profil fájl tartalmának közzététele a web Deploy Secrets szolgáltatásban |
| **képek** | Teljesen minősített tároló-rendszerkép (ek) neve. Például: "myregistry.azurecr.io/nginx:latest" vagy "Python: 3.7.2-Alpine/". Többtárolós forgatókönyv esetén több tároló KépNeve is biztosítható (többsoros elválasztva) |
| **tárolóhely neve** | Választható Adja meg az üzemi tárolóhelytől eltérő meglévő tárolóhelyet |
| **konfigurációs fájl** | Választható A Docker-levélírás fájljának elérési útja |

# <a name="publish-profile"></a>[Profil közzététele](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[Egyszerű szolgáltatásnév](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>További lépések

Megtalálhatja a GitHubon különböző adattárakba csoportosított műveleteit, amelyek mindegyike dokumentációt és példákat tartalmaz, amelyek segítséget nyújtanak a GitHub használatához a CI/CD-hez, és az alkalmazások üzembe helyezését az Azure-ban.

- [Az Azure-ba telepítendő műveletek munkafolyamatai](https://github.com/Azure/actions-workflow-samples)

- [Azure-bejelentkezés](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Docker-bejelentkezés/kijelentkezés](https://github.com/Azure/docker-login)

- [Munkafolyamatokat kiváltó események](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s üzembe helyezése](https://github.com/Azure/k8s-deploy)

- [Kezdő munkafolyamatok](https://github.com/actions/starter-workflows)