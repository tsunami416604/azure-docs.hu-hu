---
title: (ELAVULT) Tárolók üzembe helyezése helmtel az Azure Kubernetes-ben
description: A Helm csomagolási eszköz használatával tárolókat helyezhet üzembe egy Kubernetes-fürtön az Azure Container Service-ben
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a32c9fab3877a693d2df26571b9fae4aa7b4380c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271084"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>(ELAVULT) A Helm használatával tárolókat helyezhet üzembe egy Kubernetes-fürtön

> [!TIP]
> Az Azure Kubernetes szolgáltatást használó cikk frissített verziójáról az [Alkalmazások telepítése az Azure Kubernetes szolgáltatásban (AKS)](../../aks/kubernetes-helm.md)című témakörben olvashat.

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[A Helm](https://github.com/kubernetes/helm/) egy nyílt forráskódú csomagolási eszköz, amely segít a Kubernetes-alkalmazások életciklusának telepítésében és kezelésében. A Linux csomagkezelőkhöz hasonlóan, például az Apt-get és a Yum, a Helm a Kubernetes-diagramok kezelésére szolgál, amelyek előre konfigurált Kubernetes-erőforrások csomagjai. Ez a cikk bemutatja, hogyan dolgozhat a Helm egy Azure Container Service-ben telepített Kubernetes-fürtön.

A helm két összetevőből áll: 
* A **Helm CLI** egy olyan ügyfél, amely helyileg vagy a felhőben fut a gépen  

* **A Tiller** egy olyan kiszolgáló, amely a Kubernetes-fürtön fut, és kezeli a Kubernetes-alkalmazások életciklusát 
 
## <a name="prerequisites"></a>Előfeltételek

* [Kubernetes-fürt létrehozása](container-service-kubernetes-walkthrough.md) az Azure Container Service szolgáltatásban

* [Telepítés és `kubectl` konfigurálás](../container-service-connect.md) helyi számítógépen

* [A Helm telepítése](https://github.com/kubernetes/helm/blob/master/docs/install.md) helyi számítógépre

## <a name="helm-basics"></a>A helm alapjai 

A Kubernetes-fürttel kapcsolatos információk megtekintéséhez, amelyekbe a Talajtőt telepíti, és az alkalmazásokat telepíti, írja be a következő parancsot:

```bash
kubectl cluster-info 
```
![kubectl fürt-info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Miután telepítette a Helm-et, telepítse a Tiller-t a Kubernetes-fürtre a következő parancs beírásával:

```bash
helm init --upgrade
```
Ha sikeresen befejeződik, a következőhez hasonló kimenet jelenik meg:

![Kormányrúd beszerelése](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
A tárházban elérhető összes Helm-diagram megtekintéséhez írja be a következő parancsot:

```bash 
helm search 
```

A kimenet a következőhez hasonlóan jelenik meg:

![Helm keresés](./media/container-service-kubernetes-helm/helm-search.png)
 
Ha a legújabb verziók bekéseléséhez szeretné frissíteni a diagramokat, írja be a következőt:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Nginx bejövő vezérlők vezérlőjének központi telepítése 
 
Nginx bejövő vezérlődiagramjának telepítéséhez írjon be egyetlen parancsot:

```bash
helm install stable/nginx-ingress 
```
![Be- és visszaküldött vezérlő üzembe helyezése](./media/container-service-kubernetes-helm/nginx-ingress.png)

Ha a `kubectl get svc` fürtön futó összes szolgáltatás megtekintéséhez írja be, láthatja, hogy a be- és fürtvezérlőhöz IP-cím van rendelve. (Amíg a feladat folyamatban `<pending>`van, megjelenik a . Néhány percet vesz igénybe.) 

Az IP-cím hozzárendelése után keresse meg a külső IP-cím értékét, és tekintse meg a Nginx háttérrendszer futását. 
 
![Be- és visszakésel-vissza IP-cím](./media/container-service-kubernetes-helm/ingress-ip-address.png)


A fürtre telepített diagramok listájának megtekintéséhez írja be a következőt:

```bash
helm list 
```

A parancsot rövidítheti `helm ls`a parancsra.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>MariaDB-diagram és ügyfél telepítése

Most telepítsen egy MariaDB-diagramot és egy MariaDB-ügyfelet az adatbázishoz való csatlakozáshoz.

A MariaDB-diagram telepítéséhez írja be a következő parancsot:

```bash
helm install --name v1 stable/mariadb
```

hol `--name` van a címke használt kiadások.

> [!TIP]
> Ha az üzemelő `helm repo update` példány nem sikerül, futtassa, majd próbálkozzon újra.
>
 
 
A fürtön telepített összes diagram megtekintéséhez írja be a következőt:

```bash 
helm list
```
 
A fürtön futó összes központi telepítés megtekintéséhez írja be a következőt:

```bash
kubectl get deployments 
``` 
 
 
Végül, ha egy podot szeretne futtatni az ügyfél eléréséhez, írja be a következőt:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Az ügyfélhez való csatlakozáshoz írja `v1-mariadb` be a következő parancsot, amely a központi telepítés nevére lép:

```bash
sudo mysql –h v1-mariadb
```
 
 
Mostantól szabványos SQL-parancsokkal is létrehozhat adatbázisokat, táblákat stb. Például `Create DATABASE testdb1;` létrehoz egy üres adatbázist. 
 
 
 
## <a name="next-steps"></a>További lépések

* A Kubernetes-diagramok kezeléséről a [Helm dokumentációban](https://github.com/kubernetes/helm/blob/master/docs/index.md)olvashat bővebben. 

