---
title: Tárolók létrehozása, tesztelése és üzembe helyezése az Azure Kubernetes Service-ben GitHub-műveletek használatával
description: Ismerje meg, hogyan helyezhet üzembe a tárolót a GitHub-műveletek használatával a Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 7743a3a8d6e77affd6229b648ab79b5b2f07a0af
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564100"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>A Kubernetes szolgáltatás üzembe helyezéséhez szükséges GitHub-műveletek

A [GitHub-műveletek](https://help.github.com/en/articles/about-github-actions) révén rugalmasan hozhat létre automatizált szoftverfejlesztési életciklus-munkafolyamatot. A Kubernetes művelet [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) megkönnyíti az Azure Kubernetes Service-fürtök üzembe helyezését. A művelet beállítja a cél AK-alapú fürt kontextusát, amelyet más műveletek, például az [Azure/k8s-Deploy](https://github.com/Azure/k8s-deploy/tree/master), az [Azure/k8s-Create-Secret](https://github.com/Azure/k8s-create-secret/tree/master) stb. használhatnak, vagy futtathatnak bármilyen kubectl-parancsot.

A munkafolyamatot egy YAML-(. YML) fájl határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

Az AK-t tartalmazó munkafolyamatok esetében a fájl három szakaszt tartalmaz:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | Bejelentkezés a Private Container registrybe (ACR) |
|**Létrehozás** | Build & a tároló rendszerképének leküldése  |
|**Telepítés** | 1. a cél AK-fürt beállítása |
| |2. hozzon létre egy általános/Docker-beállításjegyzékbeli titkot a Kubernetes-fürtben  |
||3. üzembe helyezés a Kubernetes-fürtön|

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

[Egyszerű szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) létrehozásához használja az az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot az [Azure CLI](/cli/azure/)-ben. Ezt a parancsot a Azure Portal [Azure Cloud Shell](https://shell.azure.com/) használatával vagy a **kipróbálás** gombra kattintva futtathatja.

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

    ![Képernyőfelvétel: az új titkos kulcs hozzáadása egy adattárhoz.](media/kubernetes-action/secrets.png)

2. Illessze be a fenti parancs tartalmát a `az cli` titkos változó értékére. Például: `AZURE_CREDENTIALS`.

3. Hasonlóképpen adja meg a következő további titkokat a tároló beállításjegyzékbeli hitelesítő adataihoz, és állítsa be őket a Docker bejelentkezési műveletben. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. A titkokat az alább látható módon fogja látni.

    ![Képernyőfelvétel: egy adattár meglévő titkait jeleníti meg.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Tároló lemezképének létrehozása és üzembe helyezése az Azure Kubernetes Service-fürtön

A tároló lemezképek felépítésének és leküldésének lépései a művelet használatával hajthatók végre `Azure/docker-login@v1` . Ha a Container-lemezképet AK-ba szeretné telepíteni, akkor a műveletet kell használnia `Azure/k8s-deploy@v1` . Ehhez a művelethez öt paraméter tartozik:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **névtér** | Választható Válassza ki a cél Kubernetes-névteret. Ha a névtér nincs megadva, a parancsok az alapértelmezett névtérben fognak futni. | 
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
