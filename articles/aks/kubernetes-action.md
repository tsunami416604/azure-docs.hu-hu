---
title: Tárolók létrehozása, tesztelése és üzembe helyezése az Azure Kubernetes Service-ben GitHub-műveletek használatával
description: Ismerje meg, hogyan helyezhet üzembe a tárolót a GitHub-műveletek használatával a Kubernetes
services: container-service
author: azooinmyluggage
ms.service: container-service
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: cc2d6df952b2e0aa9b9f4d4e1dcb4859a5bb3790
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130530"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>A Kubernetes szolgáltatás üzembe helyezéséhez szükséges GitHub-műveletek

A [GitHub-műveletek](https://help.github.com/en/articles/about-github-actions) révén rugalmasan hozhat létre automatizált szoftverfejlesztési életciklus-munkafolyamatot. A Kubernetes művelet [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) megkönnyíti az Azure Kubernetes Service-fürtök üzembe helyezését. A művelet beállítja a cél AK-alapú fürt kontextusát, amelyet más műveletek, például az [Azure/k8s-Deploy](https://github.com/Azure/k8s-deploy/tree/master), az [Azure/k8s-Create-Secret](https://github.com/Azure/k8s-create-secret/tree/master) stb. használhatnak, vagy futtathatnak bármilyen kubectl-parancsot.

> [!IMPORTANT]
> A GitHub-műveletek jelenleg béta verzióban vannak. Először [regisztrálnia kell, hogy csatlakozzon az előzetes](https://github.com/features/actions) verzióhoz a GitHub-fiók használatával.
> 

A munkafolyamatot egy YAML-fájl (. YML) határozza meg a tárház `/.github/workflows/` útvonalán. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

Az AK-t tartalmazó munkafolyamatok esetében a fájl három szakaszt tartalmaz:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | Bejelentkezés a Private Container registrybe (ACR) |
|**Build** | Build & a tároló rendszerképének leküldése  |
|**Üzembe helyezés** | 1. a cél AK-fürt beállítása |
| |2. hozzon létre egy általános/Docker-beállításjegyzékbeli titkot a Kubernetes-fürtben  |
||3. üzembe helyezés a Kubernetes-fürtön|

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

[Egyszerű szolgáltatásnév](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) létrehozásához használja az az [ad SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot az [Azure CLI](https://docs.microsoft.com/cli/azure/)-ben. Ezt a parancsot a Azure Portal [Azure Cloud Shell](https://shell.azure.com/) használatával vagy a **kipróbálás** gombra kattintva futtathatja.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

A fenti parancsban cserélje le a helyőrzőket az előfizetés-azonosítójával és az erőforráscsoporthoz. A kimenet a szerepkör-hozzárendelés hitelesítő adatai, amelyek hozzáférést biztosítanak az erőforráshoz. A parancsnak a következőhöz hasonló JSON-objektumot kell kiadnia.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Másolja ezt a JSON-objektumot, amelyet a GitHubról történő hitelesítéshez használhat.

## <a name="configure-the-github-secrets"></a>A GitHub-titkok konfigurálása

A titkok konfigurálásához kövesse a következő lépéseket:

1. A [githubon](https://github.com/)tallózással keresse meg a tárházat, válassza a **beállítások > titkok > új titok hozzáadása**lehetőséget.

    ![titkok](media/kubernetes-action/secrets.png)

2. Illessze be a fenti `az cli` parancs tartalmát a titkos változó értékeként. Például: `AZURE_CREDENTIALS`.

3. Hasonlóképpen adja meg a következő további titkokat a tároló beállításjegyzékbeli hitelesítő adataihoz, és állítsa be őket a Docker bejelentkezési műveletben. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. A titkokat az alább látható módon fogja látni.

    ![kubernetes – titkok](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Tároló lemezképének létrehozása és üzembe helyezése az Azure Kubernetes Service-fürtön

A tároló lemezképek felépítése és leküldése `Azure/docker-login@v1` művelettel történik. Ha a Container-lemezképet AK-ra szeretné telepíteni, akkor a `Azure/k8s-deploy@v1` műveletet kell használnia. Ehhez a művelethez öt paraméter tartozik:

| **A paraméter**  | **Magyarázat**  |
|---------|---------|
| **namespace** | Választható Válassza ki a cél Kubernetes-névteret. Ha a névtér nincs megadva, a parancsok az alapértelmezett névtérben fognak futni. | 
| **jegyzékek** |  Szükséges A jegyzékfájl elérési útja, amelyet a rendszer az üzembe helyezéshez fog használni |
| **képek** | Választható A jegyzékfájlok helyettesítéséhez használni kívánt rendszerkép (ek) teljes erőforrás-URL-címe |
| **imagepullsecrets** | Választható Egy olyan Docker-beállításjegyzékbeli titok neve, amely már be van állítva a fürtön belül. Az egyes titkos nevek a imagePullSecrets mezőben találhatók a bemeneti jegyzékfájlban található munkaterhelések esetében. |
| **kubectl – verzió** | Választható A kubectl bináris fájl egy adott verzióját telepíti. |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Üzembe helyezés az Azure Kubernetes Service-fürtben

Végpontok közötti munkafolyamat a tároló lemezképek létrehozásához és az Azure Kubernetes Service-fürtön való üzembe helyezéshez.

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

A GitHubon különböző adattárakban találhatja meg a műveleteket, melyek mindegyike dokumentációt és példákat tartalmaz, amelyek segítséget nyújtanak a GitHub a CI/CD-hez való használatához és az alkalmazások Azure-beli üzembe helyezéséhez.

- [telepítő – kubectl](https://github.com/Azure/setup-kubectl)

- [k8s – környezet beállítása](https://github.com/Azure/k8s-set-context)

- [AK-set-Context](https://github.com/Azure/aks-set-context)

- [k8s – titkos kód létrehozása](https://github.com/Azure/k8s-create-secret)

- [k8s – üzembe helyezés](https://github.com/Azure/k8s-deploy)

- [webapps – tároló – üzembe helyezés](https://github.com/Azure/webapps-container-deploy)

- [műveletek – munkafolyamat – minták](https://github.com/Azure/actions-workflow-samples)
