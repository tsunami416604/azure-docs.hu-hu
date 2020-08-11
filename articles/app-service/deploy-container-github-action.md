---
title: Egyéni tároló CI/CD a GitHub-műveletekből
description: Megtudhatja, hogyan helyezhet üzembe egyéni linuxos tárolót a GitHub-műveletek használatával App Service egy CI/CD-folyamatból.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 21019917f37ad95dc15056daa51b3d9e53ec06fa
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88083115"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Egyéni tároló üzembe helyezése a GitHub-műveletek használatával App Service

A [GitHub-műveletek](https://help.github.com/en/articles/about-github-actions) révén rugalmasan hozhat létre automatizált szoftverfejlesztési életciklus-munkafolyamatot. A [tárolók Azure app Service műveletével](https://github.com/Azure/webapps-container-deploy)automatizálhatja a munkafolyamatot, hogy egyéni tárolókat helyezzen üzembe [app Service](overview.md) GitHub-műveletek használatával.

> [!IMPORTANT]
> A GitHub-műveletek jelenleg béta verzióban vannak. Először [regisztrálnia kell, hogy csatlakozzon az előzetes](https://github.com/features/actions) verzióhoz a GitHub-fiók használatával.
> 

A munkafolyamatot egy YAML-(. YML) fájl határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

Azure App Service tároló munkafolyamathoz a fájl három szakaszt tartalmaz:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Adjon meg egy szolgáltatásnevet. <br /> 2. hozzon létre egy GitHub-titkot. |
|**Létrehozás** | 1. Állítsa be a környezetet. <br /> 2. hozza létre a tároló rendszerképét. |
|**Telepítés** | 1. Telepítse a tároló lemezképét. |

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

[Egyszerű szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) létrehozásához használja az az [ad SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot az [Azure CLI](https://docs.microsoft.com/cli/azure/)-ben. Ezt a parancsot a Azure Portal [Azure Cloud Shell](https://shell.azure.com/) használatával vagy a **kipróbálás** gombra kattintva futtathatja.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

A fenti példában cserélje le a helyőrzőket az előfizetés-AZONOSÍTÓra és az erőforráscsoport nevére. A kimenet egy JSON-objektum, amely a szerepkör-hozzárendelés hitelesítő adatait tartalmazza, amelyek hozzáférést biztosítanak a App Service alkalmazáshoz az alábbihoz hasonló módon. A JSON-objektum másolása később.

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
> Mindig jó gyakorlat a minimális hozzáférés megadására. A fenti CLI-parancs hatókörét korlátozhatja az adott App Service alkalmazásra, illetve arra a Azure Container Registryra, ahová a rendszer leküldi a tároló lemezképeit.

## <a name="configure-the-github-secret"></a>A GitHub-titok konfigurálása

A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása**lehetőséget.

Illessze be a JSON-kimenet tartalmát az [egyszerű szolgáltatásnév létrehozása](#create-a-service-principal) a titkos változó értékeként. Adja meg a titkot a nevet, például: `AZURE_CREDENTIALS` .

Amikor később konfigurálja a munkafolyamat-fájlt, az `creds` Azure bejelentkezési művelethez tartozó titkos kulcsot használja. Például:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

Hasonlóképpen adja meg a következő további titkokat a tároló beállításjegyzékbeli hitelesítő adataihoz, és állítsa be őket a Docker bejelentkezési műveletben.

- REGISTRY_USERNAME
- REGISTRY_PASSWORD

## <a name="build-the-container-image"></a>A tároló rendszerképének összeállítása

Az alábbi példa a Docker-rendszerképet Build munkafolyamat egy részét mutatja be.

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
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>Üzembe helyezés App Service tárolón

Ha a lemezképet a App Serviceban lévő egyéni tárolóba szeretné telepíteni, használja a `azure/webapps-container-deploy@v1` műveletet. Ehhez a művelethez öt paraméter tartozik:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **alkalmazás neve** | Szükséges A App Service alkalmazás neve | 
| **tárolóhely neve** | Választható Adja meg az üzemi tárolóhelytől eltérő meglévő tárolóhelyet |
| **képek** | Szükséges Adja meg a teljes tároló rendszerkép (ek) nevét. Például: "myregistry.azurecr.io/nginx:latest" vagy "Python: 3.7.2-Alpine/". Többtárolós alkalmazások esetén több tároló-rendszerkép is megadható (több sorba tagolt) |
| **konfigurációs fájl** | Választható A Docker-levélírás fájljának elérési útja. Teljes elérési útnak kell lennie, vagy az alapértelmezett munkakönyvtárhoz viszonyítva kell lennie. Többtárolós alkalmazások esetén szükséges. |
| **tároló – parancs** | Választható Adja meg az indítási parancsot. Pl.: DotNet-Futtatás vagy DotNet filename.dll |

Az alábbi példa egy Node.js-alkalmazás létrehozása és üzembe helyezése a App Service egyéni tárolójában. Figyelje meg, hogy a `creds` bemenet a `AZURE_CREDENTIALS` korábban létrehozott titokra hivatkozik.

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
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>További lépések

Megtalálhatja a GitHubon különböző adattárakba csoportosított műveleteit, amelyek mindegyike dokumentációt és példákat tartalmaz, amelyek segítséget nyújtanak a GitHub használatához a CI/CD-hez, és az alkalmazások üzembe helyezését az Azure-ban.

- [Az Azure-ba telepítendő műveletek munkafolyamatai](https://github.com/Azure/actions-workflow-samples)

- [Azure-bejelentkezés](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp tárolók számára](https://github.com/Azure/webapps-container-deploy)

- [Docker-bejelentkezés/kijelentkezés](https://github.com/Azure/docker-login)

- [Munkafolyamatokat kiváltó események](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s üzembe helyezése](https://github.com/Azure/k8s-deploy)

- [Kezdő munkafolyamatok](https://github.com/actions/starter-workflows)
