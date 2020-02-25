---
title: Egyéni tároló CI/CD a GitHub-műveletekből
description: Megtudhatja, hogyan helyezhet üzembe egyéni linuxos tárolót a GitHub-műveletek használatával App Service egy CI/CD-folyamatból.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: f32ea2ae0be66259ff153c24bfd10e179fddbbe5
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77559028"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Egyéni tároló üzembe helyezése a GitHub-műveletek használatával App Service

A [GitHub-műveletek](https://help.github.com/en/articles/about-github-actions) révén rugalmasan hozhat létre automatizált szoftverfejlesztési életciklus-munkafolyamatot. A [tárolók Azure app Service műveletével](https://github.com/Azure/webapps-container-deploy)automatizálhatja a munkafolyamatot úgy, hogy [Egyéni tárolóként](https://azure.microsoft.com/services/app-service/containers/) telepítse az alkalmazásokat a GitHub-műveletek használatával app Service.

> [!IMPORTANT]
> A GitHub-műveletek jelenleg béta verzióban vannak. Először [regisztrálnia kell, hogy csatlakozzon az előzetes](https://github.com/features/actions) verzióhoz a GitHub-fiók használatával.
> 

A munkafolyamatot egy YAML-fájl (. YML) határozza meg a tárház `/.github/workflows/` útvonalán. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

Azure App Service tároló munkafolyamathoz a fájl három szakaszt tartalmaz:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Adjon meg egy szolgáltatásnevet. <br /> 2. hozzon létre egy GitHub-titkot. |
|**Build** | 1. Állítsa be a környezetet. <br /> 2. hozza létre a tároló rendszerképét. |
|**Üzembe helyezés** | 1. Telepítse a tároló lemezképét. |

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

[Egyszerű szolgáltatásnév](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) létrehozásához használja az az [ad SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot az [Azure CLI](https://docs.microsoft.com/cli/azure/)-ben. Ezt a parancsot a Azure Portal [Azure Cloud Shell](https://shell.azure.com/) használatával vagy a **kipróbálás** gombra kattintva futtathatja.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
  # Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

A kimenet egy JSON-objektum, amely a szerepkör-hozzárendelés hitelesítő adatait tartalmazza, amelyek hozzáférést biztosítanak a App Service alkalmazáshoz az alábbihoz hasonló módon. Másolja ezt a JSON-objektumot a GitHubról történő hitelesítéshez.

 ```azurecli 
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

5. A titkokat az alább látható módon tekintheti meg.

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

A lemezkép App Serviceban lévő egyéni tárolóba való üzembe helyezéséhez használja a `azure/webapps-container-deploy@v1` műveletet. Ehhez a művelethez öt paraméter tartozik:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **alkalmazás neve** | Szükséges A App Service alkalmazás neve | 
| **tárolóhely neve** | Választható Adja meg az üzemi tárolóhelytől eltérő meglévő tárolóhelyet |
| **képek** | Szükséges Adja meg a teljes tároló rendszerkép (ek) nevét. Például: "myregistry.azurecr.io/nginx:latest" vagy "Python: 3.7.2-Alpine/". Többtárolós alkalmazások esetén több tároló-rendszerkép is megadható (több sorba tagolt) |
| **konfigurációs fájl** | Választható A Docker-levélírás fájljának elérési útja. Teljes elérési útnak kell lennie, vagy az alapértelmezett munkakönyvtárhoz viszonyítva kell lennie. Többtárolós alkalmazások esetén szükséges. |
| **tároló – parancs** | Választható Adja meg az indítási parancsot. Pl.: DotNet-Futtatás vagy DotNet filename. dll |

Az alábbi példa a Node. js-alkalmazások felépítésére és üzembe helyezésére szolgáló minta munkafolyamat a App Serviceban található egyéni tárolóba.

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

- [Azure-bejelentkezés](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp tárolók számára](https://github.com/Azure/webapps-container-deploy)

- [Docker-bejelentkezés/kijelentkezés](https://github.com/Azure/docker-login)

- [Munkafolyamatokat kiváltó események](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s üzembe helyezése](https://github.com/Azure/k8s-deploy)

- [Starter CI-munkafolyamatok](https://github.com/actions/starter-workflows)

- [Az Azure-ba üzembe helyezhető alapszintű munkafolyamatok](https://github.com/Azure/actions-workflow-samples)
