---
title: Azure Container Registry integrálása az Azure Kubernetes szolgáltatással
description: Ismerje meg, hogyan integrálható az Azure Kubernetes szolgáltatás (ak) a Azure Container Registry (ACR) használatával
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 09/17/2018
ms.author: mlearned
ms.openlocfilehash: ba52cac4ebe923b7217550ed90948d908d8daf7f
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73900669"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Hitelesítés Azure Container Registry az Azure Kubernetes szolgáltatással

Ha Azure Container Registryt (ACR) használ az Azure Kubernetes szolgáltatással (ak), akkor hitelesítési mechanizmust kell létrehoznia. Ez a cikk példákat tartalmaz a két Azure-szolgáltatás közötti hitelesítés konfigurálására.

Beállíthatja az AK-t az ACR-integrációra néhány egyszerű parancsban az Azure CLI-vel.

## <a name="before-you-begin"></a>Előkészületek

A példákhoz a következők szükségesek:

* Az **Azure-előfizetéshez** tartozó **tulajdonosi** vagy **Azure-fiók rendszergazdai** szerepköre
* Azure CLI-verzió 2.0.73 vagy újabb verziója

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Új AK-fürt létrehozása ACR-integrációval

Itt állíthatja be az AK-t és az ACR-integrációt az AK-fürt kezdeti létrehozása során.  Annak engedélyezéséhez, hogy egy AK-fürt együttműködjön az ACR-szel, egy Azure Active Directory **egyszerű szolgáltatásnevet** használ. A következő CLI-parancs lehetővé teszi egy meglévő ACR engedélyezését az előfizetésében, és konfigurálja az egyszerű szolgáltatásnév megfelelő **ACRPull** -szerepkörét. Adja meg az alábbi paraméterek érvényes értékeit. 
```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR

```
Alternatív megoldásként megadhatja az ACR nevét egy ACR erőforrás-AZONOSÍTÓval, amelynek formátuma a következő:

/Subscriptions/\<előfizetés-azonosító\>/resourceGroups/\<erőforrás-csoport neve\>/providers/Microsoft.ContainerRegistry/registries/\<neve\> 
 
```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Ez a lépés több percet is igénybe vehet.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>ACR-integráció konfigurálása meglévő AK-fürtökhöz

Az alábbihoz tartozó **ACR-Name** vagy **ACR-Resource-id** érvényes értékek megadásával integrálhat egy meglévő, meglévő "ACR" típusú fürtöket.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```
vagy
```
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Az ACR és az AK-fürt közötti integrációt is eltávolíthatja a következőkkel
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```
vagy
```
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>ACR-& AK használata

### <a name="import-an-image-into-your-acr"></a>Rendszerkép importálása az ACR-be

Importáljon egy rendszerképet a Docker hub-ből az ACR-be a következő parancs futtatásával:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>A minta rendszerképének üzembe helyezése ACR-ből AK-ra

Győződjön meg arról, hogy megfelelő AK-beli hitelesítő adatokkal rendelkezik

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Hozzon létre egy **ACR-Nginx. YAML** nevű fájlt, amely a következőket tartalmazza:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80
```

Ezután futtassa ezt az üzembe helyezést az AK-fürtben:
```
kubectl apply -f acr-nginx.yaml
```

A központi telepítést a futtatásával figyelheti:
```
kubectl get pods
```
Két futó hüvelynek kell lennie.
```
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
