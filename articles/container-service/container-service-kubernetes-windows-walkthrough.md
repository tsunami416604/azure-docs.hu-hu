---
title: "Rövid útmutató – Azure-beli Kubernetes-fürt Windows rendszeren | Microsoft Docs"
description: "Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Kubernetes-fürtöt Windows-tárolókhoz az Azure Container Service szolgáltatásban az Azure parancssori felületével."
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 929a4dec638da9488dd0b43fd123ed0cce77bcf3
ms.contentlocale: hu-hu
ms.lasthandoff: 06/20/2017


---

<a id="deploy-kubernetes-cluster-for-windows-containers" class="xliff"></a>

# Kubernetes-fürt üzembe helyezése Windows-tárolókhoz

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Az útmutató leírja, hogyan használható az Azure parancssori felülete egy [Kubernetes](https://kubernetes.io/docs/home/)-fürt [Azure Container Service](container-service-intro.md) szolgáltatásban való üzembe helyezéséhez. Miután üzembe helyezte a fürtöt, a Kubernetes `kubectl` parancssori eszközével kapcsolódhat hozzá, és üzembe helyezheti az első Windows-tárolóját.

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!NOTE]
> Az Azure Container Service szolgáltatásban a Kubernetesen működő Windows-tárolók támogatása előzetes verzióban érhető el. 
>

<a id="log-in-to-azure" class="xliff"></a>

## Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/#login) paranccsal, és kövesse a képernyőn látható utasításokat.

```azurecli-interactive 
az login
```

<a id="create-a-resource-group" class="xliff"></a>

## Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport egy logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

<a id="create-kubernetes-cluster" class="xliff"></a>

## Kubernetes-fürt létrehozása
Kubernetes-fürt létrehozása az Azure Container Service szolgáltatásban az [az acs create](/cli/azure/acs#create) parancs használatával. 

Az alábbi példa egy *myK8sCluster* nevű fürtöt hoz létre egy Linux rendszerű főcsomóponttal és két Windows rendszerű ügynökcsomóponttal. A példa létrehozza a Linux-főcsomóponthoz való kapcsolódáshoz szükséges SSH-kulcsokat. A példa az *azureuser* rendszergazdanevet és a *myPassword12* jelszót használja a Windows-csomópontokon. Az értékeket módosítsa a környezetének megfelelően. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

Pár perc alatt lefut a parancs, és megjeleníti az üzembe helyezésre vonatkozó információkat.

<a id="install-kubectl" class="xliff"></a>

## A kubectl telepítése

Ha a csatlakozni kíván a Kubernetes-fürthöz az ügyfélszámítógépről, ehhez a Kubernetes [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/) nevű parancssori ügyfelét használhatja. 

Az Azure CloudShell használata esetén a `kubectl` már telepítve van. Ha helyileg szeretné telepíteni, használja az [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) parancsot.

Az Azure parancssori felületén használható alábbi példa telepíti a rendszeren a `kubectl` parancssori ügyfelet. Windows rendszerben rendszergazdaként futtassa ezt a parancsot.

```azurecli-interactive 
az acs kubernetes install-cli
```


<a id="connect-with-kubectl" class="xliff"></a>

## Kapcsolódás a kubectl parancssori ügyfélhez

Az [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) parancs futtatásával konfigurálja a `kubectl` ügyfelet úgy, hogy a saját Kubernetes-fürthöz kapcsolódjon. Az alábbi példa letölti a Kubernetes-fürthöz tartozó fürtkonfigurációt.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

A számítógép és a fürt kapcsolódását a következő parancs futtatásával ellenőrizheti:

```azurecli-interactive
kubectl get nodes
```

A `kubectl` felsorolja a fő- és ügynökcsomópontokat.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```

<a id="deploy-a-windows-iis-container" class="xliff"></a>

## Windows IIS-tároló üzembe helyezése

Docker-tárolót futtathat egy olyan Kubernetes-*podon* belül, amely egy vagy több tárolót tartalmaz. 

Ez az egyszerű példa egy JSON-fájl segítségével határoz meg egy Microsoft Internet Information Server- (IIS-) tárolót, majd a `kubctl apply` paranccsal létrehozza a podot. 

Hozzon létre egy `iis.json` nevű helyi fájlt, és illessze be a fájlba a következő szöveget. A fájl arra utasítja a Kubernetest, hogy futtassa az IIS-t a Windows Server 2016 Nano Serveren egy [Docker Hubról](https://hub.docker.com/r/nanoserver/iis/) származó nyilvános tárolólemezkép segítségével. A tároló a 80-as portot használja, de kezdetben csak a fürthálózaton belül érhető el.

 ```JSON
 {
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "iis",
    "labels": {
      "name": "iis"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "iis",
        "image": "nanoserver/iis",
        "ports": [
          {
          "containerPort": 80
          }
        ]
      }
    ],
    "nodeSelector": {
     "beta.kubernetes.io/os": "windows"
     }
   }
 }
 ```

A pod indításához írja be a következőt:
  
```azurecli-interactive
kubectl apply -f iis.json
```  

Az üzembe helyezés nyomon követéséhez írja be a következőt:
  
```azurecli-interactive
kubectl get pods
```

A pod üzembe helyezése közben az állapot: `ContainerCreating`. Néhány percet igénybe vehet, amíg a tároló eléri a `Running` állapotot.

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

<a id="view-the-iis-welcome-page" class="xliff"></a>

## Az IIS kezdőlapjának megtekintése

A pod nyilvános IP-címmel történő közzétételéhez írja be a következő parancsot:

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

Ezzel a paranccsal a Kubernetes létrehoz egy szolgáltatást és egy nyilvános IP-címmel rendelkező [Azure Load Balancer-szabályt](container-service-kubernetes-load-balancing.md) a szolgáltatás számára. 

Futtassa az alábbi parancsot a szolgáltatás állapotának megtekintéséhez.

```azurecli-interactive
kubectl get svc
```

Az IP-cím először a `pending` állapotban jelenik meg. Az `iis` pod külső IP-címének beállítása néhány perc múlva elkészül:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

A külső IP-cím alapján tetszőleges webböngészővel rákereshet az alapértelmezés szerinti IIS-kezdőlapra:

![Az IIS keresését ábrázoló kép](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


<a id="delete-cluster" class="xliff"></a>

## A fürt törlése
Ha a fürtre már nincs szükség, az [az group delete](/cli/azure/group#delete) paranccsal eltávolítható az erőforráscsoport, a tárolószolgáltatás és az összes kapcsolódó erőforrás.

```azurecli-interactive 
az group delete --name myResourceGroup
```


<a id="next-steps" class="xliff"></a>

## Következő lépések

A rövid útmutató segítségével üzembe helyezett egy Kubernetes-fürtöt, kapcsolatot hozott létre a `kubectl` parancssori ügyféllel, és üzembe helyezett egy IIS-tárolóval rendelkező podot. Ha többet szeretne megtudni az Azure Container Service szolgáltatásról, tekintse meg a Kubernetes-oktatóanyagot.

> [!div class="nextstepaction"]
> [ACS-alapú Kubernetes-fürt kezelése](./container-service-tutorial-kubernetes-prepare-app.md)

