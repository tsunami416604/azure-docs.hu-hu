---
title: "Rövid útmutató – Azure Kubernetes-fürt létrehozása Linux rendszeren | Microsoft Docs"
description: "Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre az Azure CLI segítségével Kubernetes-fürtöt Linux-tárolók esetén az Azure Container Service-ben."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: 3be2079d205d6bfd4c796e5f6abcd7ac5fe595a2
ms.contentlocale: hu-hu
ms.lasthandoff: 07/19/2017

---

# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>Kubernetes-fürt üzembe helyezése Linux-tárolók esetén

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Az útmutatóból megtudhatja, hogyan használható az Azure CLI [Kubernetes](https://kubernetes.io/docs/home/)-fürtök [Azure Container Service-ben](container-service-intro.md) történő üzembe helyezésére. Miután üzembe helyezte a fürtöt, a Kubernetes `kubectl` parancssori eszközével kapcsolódhat hozzá, és hozhatja létre az első Linux-tárolóját.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Kubernetes-fürt létrehozása
Hozzon létre egy Kubernetes-fürtöt az Azure Container Service-ben az [az acs create](/cli/azure/acs#create) paranccsal. 

Az alábbi példában egy *myK8sCluster* nevű fürtöt hozhat létre egy Linux rendszerű főcsomóponttal és két Linux rendszerű ügynökcsomóponttal. Ebben a példában SSH-kulcsokat hozhat létre, ha még nem léteznek ilyenek az alapértelmezett helyeken. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. Frissítse a fürt nevét a környezetnek megfelelően. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --generate-ssh-keys 
```

Pár perc múlva befejeződik a parancs futtatása, és megjelennek az üzembe helyezésre vonatkozó információk.

## <a name="install-kubectl"></a>A kubectl telepítése

Ha a csatlakozni kíván a Kubernetes-fürthöz az ügyfélszámítógépről, ehhez a Kubernetes [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/) nevű parancssori ügyfelét használhatja. 

Az Azure CloudShell használata esetén a `kubectl` már telepítve van. Ha helyileg szeretné telepíteni, használja az [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) parancsot.

Az alábbi Azure CLI-példában a `kubectl` rendszerre való telepítése történik. Ha macOS vagy Linux rendszeren használja az Azure CLI-t, elképzelhető, hogy a parancsot a `sudo` használatával kell futtatnia.

```azurecli-interactive 
az acs kubernetes install-cli 
```

## <a name="connect-with-kubectl"></a>Kapcsolódás a kubectl segítségével

Az [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) parancs futtatásával konfigurálja a `kubectl` ügyfelet úgy, hogy a saját Kubernetes-fürthöz kapcsolódjon. Az alábbi példában a Kubernetes-fürthöz tartozó fürtkonfiguráció letöltése történik.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

A számítógép és a fürt közötti kapcsolat állapotát az alábbi parancs futtatásával ellenőrizheti:

```azurecli-interactive
kubectl get nodes
```

A `kubectl` listázza a fő- és ügynökcsomópontokat.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```


## <a name="deploy-an-nginx-container"></a>NGINX-tároló üzembe helyezése

Docker-tárolót futtathat olyan Kubernetes-*podokban*, amelyek legalább egy tárolót tartalmaznak. 

Az alábbi parancs elindítja az NGINX Docker-tárolót az egyik csomópont valamelyik Kubernetes-podján. Ebben az esetben a tároló rendszerképből lekért NGINX-webkiszolgálót futtat a [Docker Hub](https://hub.docker.com/_/nginx/) szolgáltatásban.

```azurecli-interactive
kubectl run nginx --image nginx
```
A tároló futtatási állapotának ellenőrzéséhez futtassa az alábbi parancsot:

```azurecli-interactive
kubectl get pods
```

## <a name="view-the-nginx-welcome-page"></a>Az NGINX kezdőlapjának megtekintése
Az NGINX-kiszolgáló nyilvános IP-címmel történő közzétételéhez írja be az alábbi parancsot:

```azurecli-interactive
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Ezzel a paranccsal a Kubernetes létrehoz egy szolgáltatást és egy nyilvános IP-címmel rendelkező [Azure Load Balancer-szabályt](container-service-kubernetes-load-balancing.md) a szolgáltatás számára. 

Futtassa az alábbi parancsot a szolgáltatás állapotának megtekintéséhez.

```azurecli-interactive
kubectl get svc
```

Az IP-cím először a következőképpen jelenik meg: `pending`. Néhány perc múlva megtörténik a szolgáltatás külső IP-címének beállítása:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
nginx        10.0.111.25    52.179.3.96     80/TCP         22m
```

Az alapértelmezés szerinti NGINX-kezdőlapot tetszőleges böngészővel tekintheti meg a külső IP-címen:

![Az Nginx keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


## <a name="delete-cluster"></a>Fürt törlése
Ha a fürtre már nincs szükség, az [az group delete](/cli/azure/group#delete) paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Következő lépések

A rövid útmutató segítségével üzembe helyezett egy Kubernetes-fürtöt, kapcsolatot hozott létre a `kubectl` használatával, és üzembe helyezett egy NGINX-tárolóval rendelkező podot. Az Azure Container Service-ről további információt a Kubernetes-fürtöket ismertető oktatóanyagban talál.

> [!div class="nextstepaction"]
> [ACS Kubernetes-fürtök kezelése](./container-service-tutorial-kubernetes-prepare-app.md)

