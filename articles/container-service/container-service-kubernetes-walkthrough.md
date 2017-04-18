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
ms.date: 04/05/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 5c529ae41b42d276d37e6103305e33ed04694e18
ms.lasthandoff: 04/07/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Első lépések a Kubernetes-fürtökkel a Container Service-ben


Ez a forgatókönyv bemutatja, hogyan használhatók az Azure CLI 2.0 parancsok Kubernetes-fürt létrehozása az Azure Container Service-ben. Ezután a `kubectl` parancssori eszközzel elkezdheti a fürtben lévő tárolók használatát.

A következő kép egy Container Service-fürt architektúráját mutatja be, amely egy főkiszolgálóval és két ügynökkel rendelkezik. A főcsomópont a Kubernetes REST API-t szolgálja ki. Az ügyfélcsomópontok egy Azure-beli rendelkezésre állási csoportba vannak besorolva, és a tárolókat futtatják. Minden virtuális gép ugyanazon a privát virtuális hálózaton található, és teljes mértékben elérhetők egymás számára.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="prerequisites"></a>Előfeltételek
Az útmutatás feltételezi, hogy telepítette és beállította a következőt: [Azure CLI 2.0](/cli/azure/install-az-cli2). 

A parancspéldákban feltételeztük, hogy az Azure CLI a Linux és macOS rendszerben általános Bash-rendszerhéjból fut. Ha Windows-ügyfélen futtatja az Azure CLI-t, a parancssori felülettől függően néhány szintaktikai eltérés lehet a parancsprogramokban és a fájlokban. 

## <a name="create-your-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Itt rövid rendszerhéjparancsokat talál a fürt Azure CLI 2.0-val történő létrehozásához. 

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
A fürt létrehozásához először létre kell hoznia egy erőforráscsoportot egy adott helyen. Futtasson az alábbihoz hasonló parancsokat:

```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Fürt létrehozása
Ha rendelkezik erőforráscsoporttal, létrehozhat benne egy fürtöt. Az alábbi példában a `--generate-ssh-keys` kapcsoló létrehozza a szükséges SSH nyilvános és titkos kulcsfájlokat az üzembe helyezéshez, ha azok még nem léteztek már az alapértelmezett `~/.ssh/` könyvtárban. 

Ez a parancs automatikusan létrehozza a [Azure Active Directory szolgáltatásnevet](container-service-kubernetes-service-principal.md), amelyet a Kubernetes-fürt használ az Azure-ban.

```azurecli
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX --generate-ssh-keys
```


Pár perc múlva befejeződik a parancs végrehajtása, és rendelkeznie kell egy működő Kubernetes-fürttel.

### <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürthöz csatlakozhat a számítógépéről a Kubernetes [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/) nevű parancssori ügyfelével. 

Ha a `kubectl` még nincs telepítve, a következővel telepítheti:

```azurecli
sudo az acs kubernetes install-cli
```
> [!TIP]
> Alapértelmezés szerint ez a parancs telepíti a `kubectl` bináris fájlt a `/usr/local/bin/kubectl` könyvtárba Linux vagy macOS rendszeren, vagy a `C:\Program Files (x86)\kubectl.exe` mappába Windows rendszeren. Másik telepítési útvonal megadásához használja az `--install-location` paramétert.
>

A `kubectl` telepítését követően győződjön meg arról, hogy a könyvtár elérhető a rendszerútvonalon, vagy adja hozzá az elérési úthoz. 


Ezután az alábbi parancs futtatásával töltheti le a fő Kubernetes-fürt konfigurációját a `~/.kube/config` fájlba:

```azurecli
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

A `kubectl` telepítésének és beállításának további lehetőségei: [Csatlakozás Azure Container Service-fürthöz](container-service-connect.md).

Most már készen áll arra, hogy hozzáférjen a fürthöz a gépéről. Próbálja meg futtatni a következőt:

```bash
kubectl get nodes
```

Ellenőrizze, hogy látja-e a fürtben található gépek listáját.

## <a name="create-your-first-kubernetes-service"></a>Az első Kubernetes-szolgáltatás létrehozása

Az oktatóanyag végére elsajátíthatja a következőket:
* Dockler-alkalmazások üzembe helyezése és elérhetővé tétele
* a `kubectl exec` használata parancsok futtatására egy tárolóban, 
* hozzáférés a Kubernetes-irányítópulthoz

### <a name="start-a-simple-container"></a>Egyszerű tároló indítása
Az egyszerű tárolókat (ebben az esetben az Nginx webkiszolgálót) a következővel futtathatja:

```bash
kubectl run nginx --image nginx
```

Ez a parancs elindítja az Nginx Docker-tárolót az egyik csomópont valamelyik podján.

A futó tároló megtekintéséhez futtassa a következőt:

```bash
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>A szolgáltatás elérhetővé tétele
A szolgáltatás elérhetővé tételéhez egy `LoadBalancer` típusú Kubernetes-`Service` létrehozása szükséges:

```bash
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

A parancs hatására a Kubernetes létrehoz egy nyilvános IP-címmel rendelkező Azure Load Balancer-szabályt. A rendszer néhány percen belül propagálja a módosítást a terheléselosztóra. További információ: [Tárolók terheléselosztása Kubernetes-fürtön az Azure Container Service-ben](container-service-kubernetes-load-balancing.md).

Ha látni szeretné, ahogy a szolgáltatás `pending` állapotról átvált egy külső IP-cím megjelenítésére, futtassa a következő parancsot:

```bash
watch 'kubectl get svc'
```

  ![A függőben állapotról külső IP-címre való váltás megtekintését ábrázoló kép](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Ha már látja a külső IP-címet, megnyithatja a böngészőben:

  ![Az Nginx keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Böngészés a Kubernetes felhasználói felületén
A Kubernetes webes felületének megtekintéséhez a következőt használhatja:

```bash
kubectl proxy
```
Ez a parancs egyszerű hitelesített proxyt futtat a localhoston, amellyel megtekintheti a [http://localhost:8001/ui](http://localhost:8001/ui) helyen futó Kubernetes webes felhasználói felületét. További információ: [A Kubernetes webes felhasználói felületének használata az Azure Container Service-szel](container-service-kubernetes-ui.md).

![Kubernetes-irányítópult képe](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Távoli munkamenetek a tárolókon belül
A Kubernetes lehetővé teszi a parancsok futtatását a fürtben futó távoli Docker-tárolókban.

```bash
# Get the name of your nginx pods
kubectl get pods
```

A podnév használatával távoli parancsokat futtathat a podján.  Példa:

```bash
kubectl exec <pod name> date
```

Az `-it` jelölőkkel teljesen interaktív munkamenetet indíthat:

```bash
kubectl exec <pod name> -it bash
```

![Távoli munkamenet tárolón belül](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>Következő lépések

A Kubernetes-fürt használatának további módjaiért tekintse meg a következő forrásanyagokat:

* [Kubernetes Bootcamp](https://katacoda.com/embed/kubernetes-bootcamp/1/) – bemutatja, hogyan lehet üzembe helyezni, méretezni és frissíteni a tárolóalapú alkalmazásokat, illetve hibakeresést végezni rajtuk.
* [Kubernetes felhasználói útmutató](http://kubernetes.io/docs/user-guide/) – információkat tartalmaz a programok meglévő Kubernetes-fürtökben való futtatásáról.
* [Kubernetes-példák](https://github.com/kubernetes/kubernetes/tree/master/examples) – példákkal szolgál a valódi alkalmazások Kubernetes használatával való futtatására.

