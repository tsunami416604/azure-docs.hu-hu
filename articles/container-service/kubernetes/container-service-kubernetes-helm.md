---
title: (ELAVULT) Az Azure-beli Kubernetes Helm-tárolók üzembe helyezése
description: A Helm csomagolás eszköz használatával helyezhet üzembe tárolókat a Kubernetes-fürtön az Azure Container Service-ben
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 05edbf40e8cd5f8edbdc8b74b540962b1a25c8de
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997887"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>(ELAVULT) A Kubernetes-fürtön a tárolók üzembe helyezése a Helm használatával

> [!TIP]
> Ez a cikk, amely a frissített verziót használja Azure Kubernetes Service-ben, lásd: [telepíthet alkalmazásokat a Helm használatával az Azure Kubernetes Service (AKS)](../../aks/kubernetes-helm.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Helm](https://github.com/kubernetes/helm/) egy nyílt forráskódú csomagolás eszköz, amely segít telepítése és a Kubernetes-alkalmazások életciklusának kezelését. Például az Apt-get paranccsal végzi, és a yum használatával Linux csomagkezelők hasonlóan Helm segítségével Kubernetes-diagramok, amelyek az előre konfigurált Kubernetes-erőforrások-csomagok kezelése. Ez a cikk bemutatja, hogyan használható a Helm, az Azure Container Service-ben üzembe helyezett egy Kubernetes-fürtön.

Helm két részből áll: 
* A **Helm CLI** a gépén helyileg vagy a felhőben futó ügyfél  

* **A tiller valóban** egy kiszolgáló, amely a Kubernetes-fürtön fut, és a Kubernetes-alkalmazások életciklusát felügyeli 
 
## <a name="prerequisites"></a>Előfeltételek

* [Kubernetes-fürt létrehozása](container-service-kubernetes-walkthrough.md) Azure Container Service-ben

* [Telepítse és konfigurálja `kubectl` ](../container-service-connect.md) helyi számítógépre

* [Telepítse a Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) helyi számítógépre

## <a name="helm-basics"></a>Helm alapjai 

A Kubernetes-fürt, hogy a tiller valóban telepítése és az alkalmazások üzembe helyezése kapcsolatos információk megtekintéséhez írja be a következő parancsot:

```bash
kubectl cluster-info 
```
![a kubectl fürt-info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Miután telepítette a Helm, telepítse a tiller valóban a Kubernetes-fürtön a következő parancs beírásával:

```bash
helm init --upgrade
```
Ha sikeresen befejeződik, az alábbihoz hasonló kimenet jelenik meg:

![A tiller valóban telepítése](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
A tárházban minden a Helm-diagramok rendelkezésre álló megtekintéséhez írja be a következő parancsot:

```bash 
helm search 
```

Az alábbihoz hasonló kimenet jelenik meg:

![Helm-keresés](./media/container-service-kubernetes-helm/helm-search.png)
 
A diagramok beolvasni a legújabb verzióra frissíti, írja be:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Az Nginx bejövő vezérlő diagram üzembe helyezése 
 
Telepíti az Nginx bejövő vezérlő diagramra, írja be az egyetlen paranccsal:

```bash
helm install stable/nginx-ingress 
```
![Bejövő forgalom vezérlő üzembe helyezése](./media/container-service-kubernetes-helm/nginx-ingress.png)

Ha `kubectl get svc` azon a fürtön futó összes szolgáltatás, tekintse meg, hogy egy IP-cím hozzá van rendelve a bejövőforgalom-vezérlőt. (A hozzárendelés folyamatban van, amíg megjelenik `<pending>`. Ez eltarthat néhány percig.) 

Után az IP-cím lesz kiosztva, keresse meg a külső IP-cím megjelenítéséhez a futó Nginx-háttérrendszer értékét. 
 
![Bejövő IP-cím](./media/container-service-kubernetes-helm/ingress-ip-address.png)


A fürtön telepíteni diagramok listájának megtekintéséhez írja be:

```bash
helm list 
```

A parancs rövidíthető `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>A MariaDB diagram és az ügyfél telepítése

Most már üzembe MariaDB diagram és az adatbázishoz való csatlakozáshoz MariaDB-ügyfelet.

A MariaDB diagram központi telepítéséhez írja be a következő parancsot:

```bash
helm install --name v1 stable/mariadb
```

ahol `--name` kiadásokban használható címkék.

> [!TIP]
> Ha nem sikerül a telepítés, futtassa `helm repo update` , és próbálkozzon újra.
>
 
 
A diagramok a fürtre telepített megtekintéséhez írja be:

```bash 
helm list
```
 
A fürtben futó központi telepítések megtekintéséhez írja be:

```bash
kubectl get deployments 
``` 
 
 
Végül futtassa a pod eléréséhez az ügyfél, írja be:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Az ügyfél csatlakozik, írja be a következő parancsot, és cserélje le `v1-mariadb` a központi telepítés nevére:

```bash
sudo mysql –h v1-mariadb
```
 
 
Standard SQL-parancsok segítségével most létrehozása az adatbázisok, táblák, stb. Ha például `Create DATABASE testdb1;` létrehoz egy üres adatbázist. 
 
 
 
## <a name="next-steps"></a>További lépések

* Kubernetes-diagramok kezelésével kapcsolatos további információkért lásd: a [dokumentáció Helm](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

