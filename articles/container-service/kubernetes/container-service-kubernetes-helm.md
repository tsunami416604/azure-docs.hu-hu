---
title: Az Azure Kubernetes Helm a tároló üzembe helyezése
description: Az Azure Tárolószolgáltatásban Kubernetes fürtön tároló üzembe helyezése a Helm csomagolás eszközzel
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 882e785968f94473e80c7a14e5a68498add37735
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>Helm segítségével tároló Kubernetes fürt üzembe helyezése

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

[Helm](https://github.com/kubernetes/helm/) nyílt forráskódú csomagolás eszköz, amely segít telepíteni, és Kubernetes alkalmazások életciklusának kezelését. Linux-csomag kezelői például Apt-get és Yum hasonló, Helm segítségével Kubernetes diagramok, amelyek az előre konfigurált Kubernetes erőforrások-csomagok kezelése. Ez a cikk bemutatja, hogyan Helm dolgozni az Azure Tárolószolgáltatásban telepített Kubernetes fürtön.

Helm két részből áll: 
* A **Helm CLI** helyileg vagy a felhőben a számítógépen futó ügyfél  

* **Kormányrúd** , amely a Kubernetes fürtön fut, és a Kubernetes alkalmazások életciklusát felügyeli 
 
## <a name="prerequisites"></a>Előfeltételek

* [Hozzon létre egy Kubernetes fürtöt](container-service-kubernetes-walkthrough.md) az Azure Tárolószolgáltatásban

* [Telepítse és konfigurálja `kubectl` ](../container-service-connect.md) a helyi számítógépen

* [Telepítse a Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) a helyi számítógépen

## <a name="helm-basics"></a>Helm alapjai 

A Kubernetes fürt, hogy kormányrúd telepítése, és az alkalmazások telepítésével kapcsolatos információk megtekintéséhez írja be a következő parancsot:

```bash
kubectl cluster-info 
```
![kubectl fürt-adatai](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Miután telepítette a Helm, kormányrúd a fürtön telepíteni a Kubernetes a következő parancs beírásával:

```bash
helm init --upgrade
```
Sikeresen befejeződött, a következőhöz hasonló kimenetet láthat:

![Kormányrúd telepítése](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
A tárházban összes rendelkezésre álló Helm diagramok megtekintéséhez írja be a következő parancsot:

```bash 
helm search 
```

A következőhöz hasonló kimenetnek jelenik meg:

![Helm keresése](./media/container-service-kubernetes-helm/helm-search.png)
 
A diagramok első a legújabb verziók frissítése, írja be:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Egy Nginx érkező vezérlő diagram telepítése 
 
Egy Nginx érkező vezérlő diagram telepítéséhez írja be egy parancs:

```bash
helm install stable/nginx-ingress 
```
![Érkező tartományvezérlő központi telepítése](./media/container-service-kubernetes-helm/nginx-ingress.png)

Ha `kubectl get svc` minden, a fürtön futó szolgáltatások megtekintése, láthatja, hogy egy IP-címet a érkező tartományvezérlő van rendelve. (A hozzárendelés folyamatban van, amíg megjelenik `<pending>`. Igénybe vehet néhány percet.) 

Az IP-cím után cím hozzá van rendelve, keresse meg a futó Nginx háttér megtekintéséhez a külső IP-cím értékét. 
 
![Érkező IP-cím](./media/container-service-kubernetes-helm/ingress-ip-address.png)


A fürtön telepítve diagramok listájának megtekintéséhez írja be:

```bash
helm list 
```

A parancs futtatásával rövidíthető `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>A MariaDB diagram és az ügyfél telepítése

Mostantól telepítheti MariaDB diagram és az adatbázishoz való kapcsolódáshoz MariaDB ügyfél.

A MariaDB diagram telepítéséhez írja be a következő parancsot:

```bash
helm install --name v1 stable/mariadb
```

Ha `--name` kiadásokban használt címke.

> [!TIP]
> Ha nem sikerül a telepítés, futtassa `helm repo update` , és próbálkozzon újra.
>
 
 
A fürt telepített összes diagramok megtekintéséhez írja be:

```bash 
helm list
```
 
A fürtben futó összes központi telepítést, írja be:

```bash
kubectl get deployments 
``` 
 
 
Végezetül futtatásához egy pod az ügyfél eléréséhez írja be:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Az ügyfél csatlakozik, írja be a következő parancsot, cseréje `v1-mariadb` nevű, a központi telepítés:

```bash
sudo mysql –h v1-mariadb
```
 
 
Használhatja a szokásos SQL-parancsok létrehozása az adatbázisok, táblák, stb. Például `Create DATABASE testdb1;` létrehoz egy üres adatbázist. 
 
 
 
## <a name="next-steps"></a>További lépések

* Kubernetes diagramok kezelésével kapcsolatos további információkért tekintse meg a [Helm dokumentáció](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

