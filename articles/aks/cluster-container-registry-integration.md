---
title: Az Azure Container Registry integrálása az Azure Kubernetes szolgáltatással
description: Ismerje meg, hogyan integrálható az Azure Kubernetes-szolgáltatás (AKS) az Azure Container Registry (ACR) szolgáltatással
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 514cc25e1959145c65fe60cd3054cec4ed28f44d
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617423"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Azure Container Registryvel történő hitelesítés az Azure Kubernetes Service-ből (AKS)

Ha az Azure Kubernetes-szolgáltatással (AKS) azure Container Registry (ACR) szolgáltatást használja, létre kell hozni egy hitelesítési mechanizmust. Ez a cikk példákat tartalmaz a két Azure-szolgáltatás közötti hitelesítés konfigurálása. 

Az AKS-t az ACR-integrációhoz néhány egyszerű parancsban állíthatja be az Azure CLI-vel. Ez az integráció az AcrPull szerepkört az AKS-fürthöz társított egyszerű szolgáltatáshoz rendeli.

## <a name="before-you-begin"></a>Előkészületek

Ezek a példák a következőket igénylik:

* **Tulajdonos** vagy **Azure-fiók rendszergazdai** szerepköre az **Azure-előfizetésben**
* Az Azure CLI 2.0.73-as vagy újabb verziója

Annak elkerülése érdekében, **hogy egy tulajdonos** vagy az **Azure-fiók rendszergazdai** szerepkör, manuálisan konfigurálhatja a szolgáltatásnévi manuálisan, vagy egy meglévő egyszerű szolgáltatás hitelesítéséhez ACR az AKS-ből. További információ: [ACR-hitelesítés egyszerű szolgáltatásokkal](../container-registry/container-registry-auth-service-principal.md) vagy [Hitelesítés a Kubernetes-ből lekéréses titkos adatokkal.](../container-registry/container-registry-auth-kubernetes.md)

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Új AKS-fürt létrehozása ACR-integrációval

Az AKS- és ACR-integrációt az AKS-fürt kezdeti létrehozása során állíthatja be.  Annak engedélyezéséhez, hogy egy AKS-fürt kommunikáljon az ACR-rel, egy Azure Active **Directory-szolgáltatásegyszerű** szolgáltatást használ. A következő CLI parancs lehetővé teszi egy meglévő ACR engedélyezését az előfizetésben, és konfigurálja a megfelelő **ACRPull** szerepkört az egyszerű szolgáltatáshoz. Adja meg az alábbi paraméterek érvényes értékeit.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
$MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Másik lehetőségként megadhatja az ACR-nevet egy ACR erőforrásazonosító val, amely a következő formátummal rendelkezik:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>` 

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Ez a lépés több percig is eltarthat.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>ACR-integráció konfigurálása meglévő AKS-fürtökhöz

Integráljon egy meglévő ACR-t a meglévő AKS-fürtökkel az **acr-name** vagy **acr-resource-id** érvényes értékeinek biztosításával az alábbiak szerint.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```

Vagy

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Az ACR és az AKS-fürt közötti integrációt a következő

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```

vagy

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Az ACR & aKS-sel való együttműködés

### <a name="import-an-image-into-your-acr"></a>Kép importálása az ACR-be

Importáljon egy lemezképet a docker hubról az ACR-be az alábbi futtatásával:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>A mintakép telepítése az ACR-ről az AKS-re

Annak ellenőrzése, hogy rendelkezik-e a megfelelő AKS-hitelesítő adatokkal

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Hozzon létre egy **acr-nginx.yaml** nevű fájlt, amely a következőket tartalmazza:

```yaml
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

Ezután futtassa ezt a központi telepítést az AKS-fürtben:

```console
kubectl apply -f acr-nginx.yaml
```

A központi telepítést a következő futtatásával figyelheti:

```console
kubectl get pods
```

Két futókabinod kellene.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
