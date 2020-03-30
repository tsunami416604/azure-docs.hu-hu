---
title: Egyéni tároló CI/CD a GitHub-műveletekből
description: Ismerje meg, hogyan használhatja a GitHub-műveleteket az egyéni Linux-tároló üzembe helyezéséhez az App Service-be egy CI/CD-folyamatból.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246974"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Egyéni tároló üzembe helyezése az App Service-be a GitHub-műveletek használatával

[A GitHub-műveletek](https://help.github.com/en/articles/about-github-actions) rugalmasságot biztosít egy automatizált szoftverfejlesztési életciklus-munkafolyamat létrehozásához. Az [Azure App Service-művelet a tárolókhoz](https://github.com/Azure/webapps-container-deploy)segítségével automatizálhatja a munkafolyamatot, hogy az alkalmazások [egyéni tárolókként](https://azure.microsoft.com/services/app-service/containers/) az App Service-ben a GitHub-műveletek használatával.

> [!IMPORTANT]
> A GitHub-műveletek jelenleg béta verzióban vannak. Először [regisztrálnia kell, hogy csatlakozzon az előzetes verzióhoz a](https://github.com/features/actions) GitHub-fiókjával.
> 

A munkafolyamatot egy YAML (.yml) fájl `/.github/workflows/` határozza meg a tárház elérési útján. Ez a definíció tartalmazza a munkafolyamatot kifutó különböző lépéseket és paramétereket.

Az Azure App Service-tároló munkafolyamatok esetén a fájl három szakaszból áll:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Egyszerű szolgáltatás definiálása. <br /> 2. Hozzon létre egy GitHub-titkot. |
|**Felépítés** | 1. Állítsa be a környezetet. <br /> 2. Építsd meg a tárolólemezképet. |
|**Telepítés** | 1. Telepítse a tárolólemezképet. |

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az [Azure CLI-ben](https://docs.microsoft.com/cli/azure/)található [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) paranccsal hozhat létre [egyszerű szolgáltatást.](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) Ezt a parancsot az [Azure Cloud Shell](https://shell.azure.com/) használatával futtathatja az Azure Portalon, vagy a Try **it** gombra kattintva.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

A kimenet egy JSON-objektum a szerepkör-hozzárendelési hitelesítő adatokkal, amelyek hozzáférést biztosítanak az App Service-alkalmazás hoz az alábbihoz hasonló. Másolja ezt a JSON-objektumot a GitHubról való hitelesítéshez.

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
> Mindig jó gyakorlat a minimális hozzáférés megadása. Korlátozhatja a hatókört a fenti Az CLI-parancsban az adott App Service-alkalmazásra és az Azure Container Registry-re, ahol a tárolórendszerképek et lelökik.

## <a name="configure-the-github-secret"></a>A GitHub-titok konfigurálása

Az alábbi példa felhasználói szintű hitelesítő adatokat használ, azaz az Azure Service Principal szolgáltatást a központi telepítéshez. A titkos adatik beállításához kövesse a lépéseket:

1. A [GitHubon](https://github.com/)keresse meg a tárházat, válassza a **Beállítások > a Titkok lehetőséget, > új titkos kulcsot adjon hozzá**

2. Illessze be az `az cli` alábbi parancs tartalmát titkos változó értékeként. Például: `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Most a munkafolyamat-fájlban `.github/workflows/workflow.yml` a fiókban: cserélje le a titkos Azure bejelentkezési művelet a titkos.

4. Hasonlóképpen adja meg a következő további titkos kulcsokat a tároló beállításjegyzék-hitelesítő adatait, és állítsa be őket a Docker bejelentkezési műveletben. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Látod a titkokat, ahogy az alábbiakban látható, ha meghatározott.

    ![konténer titkai](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>A tárolólemezkép létrehozása

A következő példa a docker-rendszerképet felépítő munkafolyamat egy részét mutatja be.

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

## <a name="deploy-to-an-app-service-container"></a>Üzembe helyezés egy App Service-tárolóba

A lemezkép üzembe helyezéséhez egy egyéni `azure/webapps-container-deploy@v1` tárolóban az App Service-ben, használja a műveletet. Ennek a műveletnek öt paramétere van:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **alkalmazásneve** | (Kötelező) Az App Service-alkalmazás neve | 
| **bővítőhely neve** | (Nem kötelező) Adjon meg egy meglévő bővítőhelyet a termelési bővítőhely-en kívül |
| **Képek** | (Kötelező) Adja meg a teljesen minősített tárolókép(ek) nevét. Például "myregistry.azurecr.io/nginx:latest" vagy "python:3.7.2-alpine/". Többtárolós alkalmazás esetén több tárolóképnév is megadható (többsoros elválasztás) |
| **konfigurációs fájl** | (Nem kötelező) A Docker-Compose fájl elérési útja. Teljesen minősített elérési útnak vagy az alapértelmezett munkakönyvtárhoz viszonyított elérési útnak kell lennie. Többtárolós alkalmazásokesetén szükséges. |
| **tároló-parancs** | (Nem kötelező) Adja meg az indítási parancsot. Az ex-nek. dotnet futtatás vagy dotnet fájlnév.dll |

Az alábbiakban a minta munkafolyamat létrehozása és üzembe helyezése node.js alkalmazást egy egyéni tárolóaz App Service-ben.

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

A GitHubon különböző adattárakba csoportosítva megtalálhatja a műveletek készletét, amelyek mindegyike dokumentációt és példákat tartalmaz a GitHub CI/CD-hez való használatának és az alkalmazások Azure-ba való üzembe helyezéséhez.

- [Azure-bejelentkezés](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp tárolókhoz](https://github.com/Azure/webapps-container-deploy)

- [Docker bejelentkezés/kijelentkezés](https://github.com/Azure/docker-login)

- [Munkafolyamatokat kiváltó események](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s telepíteni](https://github.com/Azure/k8s-deploy)

- [Kezdő CI-munkafolyamatok](https://github.com/actions/starter-workflows)

- [Az Azure-ban üzembe helyezhető kezdő munkafolyamatok](https://github.com/Azure/actions-workflow-samples)
