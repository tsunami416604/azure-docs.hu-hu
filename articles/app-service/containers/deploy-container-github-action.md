---
title: A tároló üzembe helyezése CI/CD-folyamatból GitHub-műveletekkel – Azure App Service | Microsoft Docs
description: Ismerje meg, hogyan helyezhetők üzembe a tárolók a GitHub-műveletek használatával App Service
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
ms.date: 10/09/2019
ms.author: jafreebe
ms.openlocfilehash: 2341eba2c24c06d654c9d2eeda96788d168fe27c
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809811"
---
# <a name="github-actions-for-deploying-to-web-app-for-containers"></a>A Web App for Containers üzembe helyezéséhez szükséges GitHub-műveletek

A [GitHub-műveletek](https://help.github.com/en/articles/about-github-actions) révén rugalmasan hozhat létre automatizált szoftverfejlesztési életciklus-munkafolyamatot. A GitHubhoz készült Azure App Service műveletekkel automatizálhatja a munkafolyamatot, hogy a GitHub-műveletekkel üzembe helyezzen [Azure-Web Apps a tárolók számára](https://azure.microsoft.com/services/app-service/containers/) .

> [!IMPORTANT]
> A GitHub-műveletek jelenleg béta verzióban vannak. Először [regisztrálnia kell, hogy csatlakozzon az előzetes](https://github.com/features/actions) verzióhoz a GitHub-fiók használatával.
> 

A munkafolyamatot egy YAML-fájl (. YML) határozza meg a tárház `/.github/workflows/` útvonalán. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

Az Azure webalkalmazás-tároló munkafolyamata esetében a fájl három szakaszt tartalmaz:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. egyszerű szolgáltatásnév megadása <br /> 2. GitHub-titok létrehozása |
|**Építeni** | 1. a környezet beállítása <br /> 2. a tároló rendszerképének összeállítása |
|**Üzembe helyezés** | 1. a tároló lemezképének üzembe helyezése |

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

Az alábbi példa felhasználói szintű hitelesítő adatokat használ, például az Azure egyszerű szolgáltatását az üzembe helyezéshez. Kövesse a titkos kód konfigurálásához szükséges lépéseket:

1. A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása** elemet.

2. Illessze be az alábbi `az cli` parancs tartalmát a titkos változó értékeként. Például: `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Most az ág munkafolyamat-fájljában: `.github/workflows/workflow.yml` cserélje le az Azure-beli bejelentkezési művelet titkát a titkos kulcsra.

4. Hasonlóképpen adja meg a következő további titkokat a tároló beállításjegyzékbeli hitelesítő adataihoz, és állítsa be őket a Docker bejelentkezési műveletben. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. A titkokat az alább látható módon fogja látni.

    ![tároló titkai](../media/app-service-github-actions/app-service-secrets-container.png)

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
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
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

## <a name="deploy-to-web-app-container"></a>Üzembe helyezés a webalkalmazás-tárolóban

Ha a lemezképet egy webalkalmazás-tárolóba szeretné telepíteni, akkor a `Azure/appservice-actions/webapp@master` műveletet kell használnia. Ehhez a művelethez 5 paraméter tartozik:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **alkalmazás neve** | Szükséges Az Azure-webalkalmazás neve | 
| **tárolóhely neve** | Választható Adja meg az üzemi tárolóhelytől eltérő meglévő tárolóhelyet |
| **képek** | Szükséges Adja meg a teljes tároló rendszerkép (ek) nevét. Például: "myregistry.azurecr.io/nginx:latest" vagy "Python: 3.7.2-Alpine/". Többtárolós forgatókönyv esetén több tároló KépNeve is biztosítható (többsoros elválasztva) |
| **konfigurációs fájl** | Választható A Docker-levélírás fájljának elérési útja. Teljes elérési útnak kell lennie, vagy az alapértelmezett munkakönyvtárhoz viszonyítva kell lennie. Több tárolós forgatókönyv esetén szükséges |
| **tároló – parancs** | Választható Adja meg a Start up parancsot. Pl.: DotNet-Futtatás vagy DotNet filename. dll |

Az alábbi példa a Node. js-webalkalmazások Azure Web App-tárolóba való felépítésére és üzembe helyezésére szolgáló minta munkafolyamat.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
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

## <a name="next-steps"></a>Következő lépések

Megtalálhatja a GitHubon különböző adattárakba csoportosított műveleteit, amelyek mindegyike dokumentációt és példákat tartalmaz, amelyek segítséget nyújtanak a GitHub használatához a CI/CD-hez, és az alkalmazások üzembe helyezését az Azure-ban.

- [Azure-bejelentkezés](https://github.com/Azure/actions)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp tárolók számára](https://github.com/Azure/webapps-container-deploy)

- [Docker-bejelentkezés/kijelentkezés](https://github.com/Azure/docker-login)

- [Munkafolyamatokat kiváltó események](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s üzembe helyezése](https://github.com/Azure/k8s-deploy)

- [Kezdő munkafolyamatok](https://github.com/actions/starter-workflows)
