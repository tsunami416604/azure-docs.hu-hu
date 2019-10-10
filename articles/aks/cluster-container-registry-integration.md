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
ms.openlocfilehash: d9d432c073872e7bb7f3562979e78989faea65eb
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241091"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Hitelesítés Azure Container Registry az Azure Kubernetes szolgáltatással

Ha Azure Container Registryt (ACR) használ az Azure Kubernetes szolgáltatással (ak), akkor hitelesítési mechanizmust kell létrehoznia. Ez a cikk a két Azure-szolgáltatás közötti hitelesítés ajánlott konfigurációit részletezi.

Beállíthatja az AK-t az ACR-integrációra néhány egyszerű parancsban az Azure CLI-vel.

## <a name="before-you-begin"></a>Előzetes teendők

A következőkkel kell rendelkeznie:

* Az **Azure-előfizetéshez** tartozó **tulajdonosi** vagy **Azure-fiók rendszergazdai** szerepköre
* Szüksége lesz az Azure CLI 2.0.73 vagy újabb verziójára is
* Szüksége van az ügyfélre [telepített Docker](https://docs.docker.com/install/) -ra, és hozzá kell férnie a [Docker hub](https://hub.docker.com/) -hoz

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Új AK-fürt létrehozása ACR-integrációval

Itt állíthatja be az AK-t és az ACR-integrációt az AK-fürt kezdeti létrehozása során.  Annak engedélyezéséhez, hogy egy AK-fürt együttműködjön az ACR-szel, egy Azure Active Directory **egyszerű szolgáltatásnevet** használ. A következő CLI-parancs lehetővé teszi egy meglévő ACR engedélyezését az előfizetésében, és konfigurálja az egyszerű szolgáltatásnév megfelelő **ACRPull** -szerepkörét. Adja meg az alábbi paraméterek érvényes értékeit.  A szögletes zárójelek paraméterei nem kötelezőek.
```azurecli
az login
az acr create -n myContainerRegistry -g myContainerRegistryResourceGroup --sku basic [in case you do not have an existing ACR]
az aks create -n myAKSCluster -g myResourceGroup --attach-acr <acr-name-or-resource-id>
```
**Az ACR erőforrás-azonosító formátuma a következő:** 

/Subscriptions/\<subscription-ID @ no__t-1/resourceGroups/\<resource-Group-Name @ no__t-3/Providers/Microsoft. ContainerRegistry/nyilvántartós/\<name @ no__t-5 
  
Ez a lépés több percet is igénybe vehet.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>ACR-integráció konfigurálása meglévő AK-fürtökhöz

Az alábbihoz tartozó **ACR-Name** vagy **ACR-Resource-id** érvényes értékek megadásával integrálhat egy meglévő, meglévő "ACR" típusú fürtöket.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Az ACR és az AK-fürt közötti integrációt is eltávolíthatja a következőkkel
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```


## <a name="log-in-to-your-acr"></a>Jelentkezzen be az ACR-be

A következő parancs használatával jelentkezzen be az ACR-be.  Cserélje le a <acrname> paramétert az ACR-névre.  Az alapértelmezett érték például az **ak < a-Resource-group > ACR**.

```azurecli
az acr login -n <acrName>
```

## <a name="pull-an-image-from-docker-hub-and-push-to-your-acr"></a>Egy rendszerkép lekérése a Docker hub-ról, és leküldése az ACR-nek

Húzzon egy rendszerképet a Docker hub-ról, címkézze fel a képet, és küldje el az ACR-nek.

```console
acrloginservername=$(az acr show -n <acrname> -g <myResourceGroup> --query loginServer -o tsv)
docker pull nginx
```

```
$ docker tag nginx $acrloginservername/nginx:v1
$ docker push $acrloginservername/nginx:v1

The push refers to repository [someacr1.azurecr.io/nginx]
fe6a7a3b3f27: Pushed
d0673244f7d4: Pushed
d8a33133e477: Pushed
v1: digest: sha256:dc85890ba9763fe38b178b337d4ccc802874afe3c02e6c98c304f65b08af958f size: 948
```

## <a name="update-the-state-and-verify-pods"></a>Állapot frissítése és a hüvelyek ellenőrzése

A telepítés ellenőrzéséhez hajtsa végre az alábbi lépéseket.

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Tekintse meg a YAML fájlt, és szerkessze a rendszerkép tulajdonságot úgy, hogy lecseréli az értéket az ACR bejelentkezési kiszolgálójára, a képre és a címkére.

```
$ cat acr-nginx.yaml

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

$ kubectl apply -f acr-nginx.yaml
$ kubectl get pods

You should have two running pods.

NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
