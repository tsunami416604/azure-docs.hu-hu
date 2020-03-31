---
title: Tárolók létrehozása, tesztelése és üzembe helyezése az Azure Kubernetes szolgáltatásba a GitHub-műveletek használatával
description: Ismerje meg, hogyan használhatja a GitHub-műveleteket a tároló kubernetesi üzembe helyezéséhez
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595365"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub-műveletek a Kubernetes szolgáltatásra való üzembe helyezéshez

[A GitHub-műveletek](https://help.github.com/en/articles/about-github-actions) rugalmasságot biztosít egy automatizált szoftverfejlesztési életciklus-munkafolyamat létrehozásához. A Kubernetes-művelet [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) megkönnyíti az Azure Kubernetes-szolgáltatás fürtjein történő telepítéseket. A művelet beállítja a cél AKS-fürt környezetben, amely más műveletek, például [az azure/k8s-deploy,](https://github.com/Azure/k8s-deploy/tree/master) [azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master) stb.

A munkafolyamatot egy YAML (.yml) fájl `/.github/workflows/` határozza meg a tárház elérési útján. Ez a definíció tartalmazza a munkafolyamatot kifutó különböző lépéseket és paramétereket.

Az AKS-t célzó munkafolyamatok esetében a fájl három szakaszból ad ki:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | Bejelentkezés privát tárolórendszerleíró adatbázisba (ACR) |
|**Felépítés** | A & a tárolókép leküldése  |
|**Telepítés** | 1. Állítsa be a cél AKS-fürtöt |
| |2. Általános/docker-registry titkos kulcs létrehozása a Kubernetes-fürtben  |
||3. Üzembe helyezés a Kubernetes-fürtbe|

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az [Azure CLI-ben](https://docs.microsoft.com/cli/azure/)található [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) paranccsal hozhat létre [egyszerű szolgáltatást.](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) Ezt a parancsot az [Azure Cloud Shell](https://shell.azure.com/) használatával futtathatja az Azure Portalon, vagy a Try **it** gombra kattintva.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

A fenti parancsban cserélje le a helyőrzőket az előfizetés-azonosítóra és az erőforráscsoportra. A kimenet az erőforráshoz való hozzáférést biztosító szerepkör-hozzárendelési hitelesítő adatok. A parancsnak ehhez hasonló JSON-objektumot kell kiadnia.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Másolja ezt a JSON-objektumot, amellyel hitelesíthető a GitHubról.

## <a name="configure-the-github-secrets"></a>A GitHub-titkok konfigurálása

A titkos kulcsok konfigurálásához kövesse a lépéseket:

1. A [GitHubon](https://github.com/)keresse meg a tárházat, válassza a **Beállítások > titkos kulcsok > Új titok hozzáadása**lehetőséget.

    ![Titkok](media/kubernetes-action/secrets.png)

2. Illessze be a `az cli` fenti parancs tartalmát titkos változó értékeként. Például: `AZURE_CREDENTIALS`.

3. Hasonlóképpen adja meg a következő további titkos kulcsokat a tároló beállításjegyzék-hitelesítő adatait, és állítsa be őket a Docker bejelentkezési műveletben. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Látni fogja a titkokat, ahogy az alábbiakban látható, ha meghatározott.

    ![kubernetes-titkok](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Tárolórendszerkép létrehozása és üzembe helyezés az Azure Kubernetes-szolgáltatásfürtre

A tárolórendszerképek létrehozása és leküldése művelet teljében `Azure/docker-login@v1` történik. Egy tárolórendszerkép üzembe helyezéséhez az AKS,a `Azure/k8s-deploy@v1` műveletet kell használnia. Ennek a műveletnek öt paramétere van:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **Namespace** | (Nem kötelező) Válassza ki a cél Kubernetes névteret. Ha a névtér nincs megadva, a parancsok az alapértelmezett névtérben fognak futni | 
| **Rakományjegyzék** |  (Kötelező) A telepítéshez használt jegyzékfájl elérési útja |
| **Képek** | (Nem kötelező) A jegyzékfájlok helyettesítésére használt kép(ek) teljesen minősített erőforrás-URL-címe |
| **imagepullsecrets** | (Nem kötelező) A fürtön belül már beállított docker-registry titkos kulcs neve. Ezek a titkos nevek mindegyike hozzáadódik az imagePullSecrets mezőben a bemeneti jegyzékfájlokban található munkaterhelésekhez |
| **kubectl-változat** | (Nem kötelező) Telepíti a kubectl bináris verziójának egy adott verzióját |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Üzembe helyezés az Azure Kubernetes szolgáltatásfürtre

Teljes körű munkafolyamat tárolólemezképek létrehozásához és egy Azure Kubernetes-szolgáltatásfürtbe való üzembe helyezéshez.

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>További lépések

A GitHub különböző tárházaiban megtalálhatja a műveletek készletét, amelyek mindegyike dokumentációt és példákat tartalmaz a GitHub CI/CD-hez való használatában és az alkalmazások Azure-ba való üzembe helyezésében.

- [setup-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-set-kontextus](https://github.com/Azure/k8s-set-context)

- [aks-set-context](https://github.com/Azure/aks-set-context)

- [k8s-create-secret](https://github.com/Azure/k8s-create-secret)

- [k8s-telepítés](https://github.com/Azure/k8s-deploy)

- [webapps-container-deploy](https://github.com/Azure/webapps-container-deploy)

- [műveletek-munkafolyamat-minták](https://github.com/Azure/actions-workflow-samples)
