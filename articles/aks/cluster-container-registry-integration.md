---
title: Azure Container Registry integrálása az Azure Kubernetes szolgáltatással
description: Ismerje meg, hogyan integrálható az Azure Kubernetes szolgáltatás (ak) a Azure Container Registry (ACR) használatával
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: mlearned
ms.openlocfilehash: 1f07581be8fc416f8aae5eec1460ca3d33bda8f9
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114236"
---
# <a name="preview---authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Előzetes verzió – hitelesítés a Azure Container Registry az Azure Kubernetes Service-ben

Ha Azure Container Registryt (ACR) használ az Azure Kubernetes szolgáltatással (ak), akkor hitelesítési mechanizmust kell létrehoznia. Ez a cikk a két Azure-szolgáltatás közötti hitelesítés ajánlott konfigurációit részletezi.

Beállíthatja az AK-t az ACR-integrációra néhány egyszerű parancsban az Azure CLI-vel.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További részletekért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok](support-policies.md)
> * [Azure-támogatás – gyakori kérdések](faq.md)

## <a name="before-you-begin"></a>Előkészületek

A következőkkel kell rendelkeznie:

* Az **Azure** -előfizetéshez tartozó tulajdonosi vagy **Azure-fiók rendszergazdai** szerepköre
* Szüksége lesz az Azure CLI 2.0.70 vagy újabb verziójára, valamint az AK-előnézet 0.4.8-bővítményre
* Szüksége van az ügyfélre [telepített Docker](https://docs.docker.com/install/) -ra, és hozzá kell férnie a [Docker hub](https://hub.docker.com/) -hoz

## <a name="install-latest-aks-cli-preview-extension"></a>A legújabb AK CLI előnézet bővítmény telepítése

Szüksége lesz az **AK-előnézet 0.4.8-** bővítményre vagy újabb verzióra.

```azurecli
az extension remove --name aks-preview 
az extension add -y --name aks-preview
```

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Új AK-fürt létrehozása ACR-integrációval

Itt állíthatja be az AK-t és az ACR-integrációt az AK-fürt kezdeti létrehozása során.  Annak engedélyezéséhez, hogy egy AK-fürt együttműködjön az ACR-szel, egy Azure Active Directory **egyszerű szolgáltatásnevet** használ. A következő CLI-parancs létrehoz egy ACR-t az Ön által megadott erőforráscsoporthoz, és konfigurálja az egyszerű szolgáltatásnév megfelelő **ACRPull** -szerepkörét. Ha az *ACR-Name* nem létezik az Ön által megadott erőforráscsoporthoz, automatikusan létrejön egy alapértelmezett ACR- `aks<resource-group>acr` név.  Adja meg az alábbi paraméterek érvényes értékeit.  A szögletes zárójelek paraméterei nem kötelezőek.
```azurecli
az login
az aks create -n myAKSCluster -g myResourceGroup --enable-acr [--acr <acr-name-or-resource-id>]
```
\* * Az ACR erőforrás-azonosító formátuma a következő: 

/Subscriptions/< előfizetés-d >/resourceGroups/< Resource-Group-Name >/providers/Microsoft.ContainerRegistry/registries/<name> 
  
Ez a lépés több percet is igénybe vehet.

## <a name="create-acr-integration-for-existing-aks-clusters"></a>ACR-integráció létrehozása meglévő AK-fürtökhöz

Az alábbihoz tartozó **ACR-Name** vagy **ACR-Resource-id** érvényes értékek megadásával integrálhat egy meglévő, meglévő "ACR" típusú fürtöket.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --enable-acr --acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --enable-acr --acr <acr-resource-id>
```

## <a name="log-in-to-your-acr"></a>Jelentkezzen be az ACR-be

A következő parancs használatával jelentkezzen be az ACR-be.  Cserélje le <acrname> a paramétert az ACR-névre.  Az alapértelmezett érték például az **ak < a-Resource-group > ACR**.

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
