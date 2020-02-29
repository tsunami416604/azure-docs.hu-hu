---
title: Azure Container Registry integrálása az Azure Kubernetes szolgáltatással
description: Ismerje meg, hogyan integrálható az Azure Kubernetes szolgáltatás (ak) a Azure Container Registry (ACR) használatával
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 5d8b45137ff82db6b23b5bf31eb3e8063de343bb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191333"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Hitelesítés Azure Container Registry az Azure Kubernetes szolgáltatással

Ha Azure Container Registryt (ACR) használ az Azure Kubernetes szolgáltatással (ak), akkor hitelesítési mechanizmust kell létrehoznia. Ez a cikk példákat tartalmaz a két Azure-szolgáltatás közötti hitelesítés konfigurálására.

Beállíthatja az AK-t az ACR-integrációra néhány egyszerű parancsban az Azure CLI-vel.

## <a name="before-you-begin"></a>Előkészületek

A példákhoz a következők szükségesek:

* Az **Azure-előfizetéshez** tartozó **tulajdonosi** vagy **Azure-fiók rendszergazdai** szerepköre
* Azure CLI-verzió 2.0.73 vagy újabb verziója

Ha el szeretné kerülni a **tulajdonosi** vagy az **Azure-fiók rendszergazdai** szerepkörének megkövetelését, manuálisan is konfigurálhat egy egyszerű szolgáltatásnevet, vagy használhat egy meglévő egyszerű szolgáltatásnevet az ACR-ből való hitelesítéshez. További információkért tekintse meg az [ACR-hitelesítés egyszerű szolgáltatásokkal](../container-registry/container-registry-auth-service-principal.md) vagy a [Kubernetes egy lekéréses titokkal történő hitelesítését](../container-registry/container-registry-auth-kubernetes.md)ismertető témakört.

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

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>` 

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Ez a lépés több percet is igénybe vehet.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>ACR-integráció konfigurálása meglévő AK-fürtökhöz

Az alábbihoz tartozó **ACR-Name** vagy **ACR-Resource-id** érvényes értékek megadásával integrálhat egy meglévő, meglévő "ACR" típusú fürtöket.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```
vagy,
```
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Az ACR és az AK-fürt közötti integrációt is eltávolíthatja a következőkkel
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```
or
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
