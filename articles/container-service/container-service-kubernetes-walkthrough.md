---
title: "Kubernetes-fürt gyors üzembe helyezése az Azure-ban | Microsoft Docs"
description: "Kubernetes-fürt üzembe helyezése az Azure Container Service-ben és az első lépések"
services: container-service
documentationcenter: 
author: anhowe
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
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 7ed8fb75f057d5a7cfde5436e72e8fec52d07156
ms.openlocfilehash: f3b2fc301bf7083f192c0ec872c4e032472eef97


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Microsoft Azure Container Service Engine – Kubernetes-bemutató

## <a name="prerequisites"></a>Előfeltételek
A bemutató feltételezi, hogy telepítette az [azure-cli parancssori eszközt](https://github.com/azure/azure-cli#installation), és létrehozta a [nyilvános SSH-kulcsot](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) a `~/.ssh/id_rsa.pub` helyen.

## <a name="overview"></a>Áttekintés

Az alábbi utasítások létrehoznak egy Kubernetes-fürtöt egy fő- és két feldolgozó csomóponttal.
A főcsomópont a Kubernetes REST API-t szolgálja ki.  A feldolgozó csomópontok egy Azure rendelkezésre állási csoportba vannak besorolva, és a tárolókat futtatják. Minden virtuális gép ugyanabban a magánhálózati VNET-ben található, és teljes mértékben elérhetők egymás számára.

> [!NOTE]
> A Kubernetes támogatása az Azure Container Service-ben jelenleg előzetes verzióban van.
>

A következő kép egy tárolószolgáltatási fürt architektúráját mutatja be, amely egy főkiszolgálóval és két ügynökkel rendelkezik:

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="creating-your-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
A fürt létrehozásához először létre kell hoznia egy erőforráscsoportot egy adott helyen. Ezt a következővel teheti meg:
```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Fürt létrehozása
Ha rendelkezik erőforráscsoporttal, a következővel hozhat létre egy fürtöt a csoporton belül:
```console
DNS_PREFIX=some-unique-value
SERVICE_NAME=any-acs-service-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$SERVICE_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> Az azure-cli feltölti a `~/.ssh/id_rsa.pub` fájlt a linuxos virtuális gépekre.
>

A parancs végrehajtása után a Kubernetes-fürttel működik.

### <a name="configure-kubectl"></a>A kubectl konfigurálása
A `kubectl` a Kubernetes parancssori ügyfele.  Ha még nem telepítette, a következővel telepítheti:

```console
az acs kubernetes install-cli
```

A `kubectl` telepítése után az alábbi parancs futtatásával töltheti le a fő Kubernetes-fürt konfigurációját a ~/.kube/config fájlba
```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$SERVICE_NAME
```

Most már készen áll arra, hogy hozzáférjen a fürthöz a gépéről. Próbálja meg futtatni a következőt:
```console
kubectl get nodes
```

Ellenőrizze, hogy látja-e a fürtben található gépeket.

## <a name="create-your-first-kubernetes-service"></a>Az első Kubernetes-szolgáltatás létrehozása

Az oktatóanyag végére elsajátíthatja a következőket:
 * Dockler-alkalmazások üzembe helyezése és elérhetővé tétele,
 * a `kubectl exec` használata parancsok futtatására egy tárolóban, 
 * és hozzáférés a Kubernetes-irányítópulthoz.

### <a name="start-a-simple-container"></a>Egyszerű tároló indítása
Az egyszerű tárolókat (ebben az esetben az `nginx` webkiszolgálót) a következővel futtathatja:

```console
kubectl run nginx --image nginx
```

Ez a parancs elindítja az nginx Docker-tárolót az egyik csomópont valamelyik podján.

A
```console
kubectl get pods
```

futtatásával megtekintheti a futó tárolót.

### <a name="expose-the-service-to-the-world"></a>A szolgáltatás elérhetővé tétele
A szolgáltatás elérhetővé tételéhez  egy `LoadBalancer` típusú Kubernetes-`Service` létrehozására lesz szükség:

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

A Kubernetes létrehoz egy nyilvános IP-címmel rendelkező Azure Load Balancert. A rendszer 2-3 percen belül propagálja a módosítást a terheléselosztóra.

Ha látni szeretné, ahogy a szolgáltatás „függőben” állapotról átvált külső IP-cím típusra:
```console
watch 'kubectl get svc'
```

  ![A függőben állapotról külső IP-címre való váltás megtekintését ábrázoló kép](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Ha már látja a külső IP-címet, megkeresheti a böngészőben:

  ![Az nginx keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Böngészés a Kubernetes felhasználói felületén
A Kubernetes webes felületének megtekintéséhez a következőt használhatja:

```console
kubectl proxy
```
Ez egy egyszerű hitelesített proxyt futtat a localhoston, amellyel megtekintheti a [Kubernetes felhasználói felületét](http://localhost:8001/ui)

![Kubernetes-irányítópult képe](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Távoli munkamenetek a tárolókon belül
A Kubernetes lehetővé teszi a parancsok futtatását a fürtben futó távoli Docker-tárolókban.

```console
# Get the name of your nginx pod
kubectl get pods
```

A podnév használatával távoli parancsokat futtathat a podján.  Példa:
```console
kubectl exec nginx-701339712-retbj date
```

Az `-it` jelölőkkel teljesen interaktív munkamenetet indíthat:

```console
kubectl exec nginx-701339712-retbj -it bash
```

![A pod IP-címére mutató curl képe](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)


## <a name="details"></a>Részletek
### <a name="installing-the-kubectl-configuration-file"></a>A kubectl konfigurációs fájljának telepítése
Az `az acs kubernetes get-credentials` parancs futtatásakor a rendszer a kezdőkönyvtár ~/.kube/config mappájában tárolta a távelérés kube konfigurációs fájlját.

Ha bármikor le kell töltenie közvetlenül, Linuxon és OS X-en az `ssh`-t, Windowson pedig a `Putty`t használhatja:

#### <a name="windows"></a>Windows
A pscp [puttyból](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) való használatához  ellenőrizze, hogy a tanúsítványa közzé van-e téve a [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant) eszközön keresztül:
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```

#### <a name="os-x-or-linux"></a>OS X vagy Linux:
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
## <a name="learning-more"></a>További információk

### <a name="azure-container-service"></a>Azure Container Service

1. [Az Azure Container Service dokumentációja](https://azure.microsoft.com/en-us/documentation/services/container-service/)
2. [Az Azure Container Service nyílt forráskódú motorja](https://github.com/azure/acs-engine)

### <a name="kubernetes-community-documentation"></a>A Kubernetes közösségi dokumentációja

1. [Kubernetes Bootcamp](https://katacoda.com/embed/kubernetes-bootcamp/1/) – bemutatja, hogyan lehet üzembe helyezni, méretezni és frissíteni a tárolóalapú alkalmazásokat, illetve hibakeresést végezni rajtuk.
2. [Kubernetes felhasználói útmutató](http://kubernetes.io/docs/user-guide/) – információkat tartalmaz a programok meglévő Kubernetes-fürtökben való futtatásáról.
3. [Kubernetes-példák](https://github.com/kubernetes/kubernetes/tree/master/examples) – számos példával szolgál a valódi alkalmazások Kubernetes használatával való futtatására.



<!--HONumber=Jan17_HO4-->


