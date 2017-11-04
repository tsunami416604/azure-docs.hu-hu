---
title: "Rövid útmutató – Azure Kubernetes-fürt létrehozása Linux rendszeren | Microsoft Docs"
description: "Ismerje meg, a Linux-tárolók Kubernetes fürt létrehozása az Azure parancssori felülettel AKS a gyorsan."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 89d469b330644b8f5b82a343ea4408d5b8d10b12
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Az Azure-tároló szolgáltatás (AKS) fürt központi telepítése

A gyors üzembe helyezés egy AKS fürtre van telepítve az Azure parancssori felület használatával. Egy előtér-webkiszolgáló és a Redis példánya több tároló alkalmazást majd futtassa a fürtön. Miután végeztünk ezzel, az alkalmazás elérhető lesz az interneten.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-vote.png)

A gyors üzembe helyezés azt feltételezi, hogy alapszinten megértse, Kubernetes fogalmak Kubernetes tekintse meg a részletes információk a [Kubernetes dokumentáció]( https://kubernetes.io/docs/home/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Rendszererőforrásokra telepíti, és a parancssori felület helyileg, a gyors üzembe helyezés megköveteli, hogy futnak-e az Azure parancssori felület 2.0.20 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>Azure-előfizetése AKS előzetes engedélyezése
AKS jelenleg előzetes verzióban érhető, amíg a szolgáltatás jelzőt előfizetéséhez új fürtök létrehozása szükséges. Ez a szolgáltatás tetszőleges számú előfizetések, amelyek használni szeretné a kérheti. Használja a `az provider register` parancs futtatásával regisztrálja a AKS szolgáltatót:

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

A regisztrálás után most már készen áll a AKS Kubernetes fürt létrehozása.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik.

Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroup* a a *westus2* helyét.

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Kimenet:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westus2",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>AKS fürt létrehozása

Az alábbi példakód létrehozza a fürt nevű *myK8sCluster* egy csomóponthoz.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8sCluster --agent-count 1 --generate-ssh-keys
```

Néhány perc múlva befejeződik a parancs végrehajtása, és visszaadja a fürttel kapcsolatos adatokat JSON formátumban.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Kubernetes-fürtök kezeléséhez használja a [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) eszközt, a Kubernetes parancssori ügyfelét.

Azure Cloud rendszerhéj használata, kubectl már telepítve van. Ha szeretné helyileg telepíteni, futtassa a következő parancsot.


```azurecli
az aks install-cli
```

A következő parancsot a Kubernetes fürthöz való kapcsolódás kubectl konfigurálásához. Ebben a lépésben a rendszer hitelesítő adatokat tölt le, és konfigurálja a Kubernetes parancssori felületét azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myK8sCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

Kimenet:

```
NAME                          STATUS    ROLES     AGE       VERSION
k8s-myk8scluster-36346190-0   Ready     agent     2m        v1.7.7
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Kubernetes-jegyzékfájl meghatározza a fürt célállapotát, például azt, hogy milyen tárolórendszerképeknek kell futniuk. Ebben a példában egy jegyzékfájlt használunk az Azure Vote alkalmazás futtatásához szükséges összes objektum létrehozásához.

Hozzon létre egy fájlt `azure-vote.yml` és másolja azt a következő YAM-kóddal. Ha az Azure Cloud Shellben dolgozik, ez a fájl a vi vagy a Nano segítségével hozható létre, ugyanúgy, mint egy virtuális vagy fizikai rendszeren.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:redis-v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Az alkalmazást a [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) paranccsal futtathatja.

```azurecli-interactive
kubectl create -f azure-vote.yml
```

Kimenet:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a rendszer létrehoz egy [Kubernetes-szolgáltatást](https://kubernetes.io/docs/concepts/services-networking/service/), amely közzéteszi az alkalmazás-előteret az interneten. A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) parancsot a `--watch` argumentummal.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Kezdetben az *azure-vote-front* szolgáltatás *EXTERNAL-IP* értéke *pending* állapotú.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Egyszer a *külső IP-* cím megváltozott *függőben lévő* való egy *IP-cím*, használjon `CTRL-C` kubectl figyelési megszakításához.

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Most a külső IP-címre léphet az Azure Vote alkalmazás megtekintéséhez.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Fürt törlése
Ha a fürtre már nincs szükség, az [az group delete](/cli/azure/group#delete) paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>A kód letöltése

A gyors üzembe helyezés, az előre létrehozott tároló képek használták a Kubernetes központi telepítés létrehozásához. A kapcsolódó alkalmazáskód, Docker-fájl és Kubernetes-jegyzékfájl a GitHubon érhetőek el.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Kubernetes-fürtöt és azon egy többtárolós alkalmazást helyezett üzembe.

További tudnivalók AKS, és végezze el a teljes kód látható, a központi telepítés példája, továbbra is a Kubernetes fürt oktatóanyag.

> [!div class="nextstepaction"]
> [Egy AKS fürt kezelése](./tutorial-kubernetes-prepare-app.md)
