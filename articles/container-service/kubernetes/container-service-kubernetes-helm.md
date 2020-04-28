---
title: ELAVULT Tárolók üzembe helyezése az Azure Kubernetes Helm szolgáltatásával
description: Tárolók üzembe helyezése Kubernetes-fürtön a Helm Packaging eszköz használatával Azure Container Service
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a32c9fab3877a693d2df26571b9fae4aa7b4380c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76271084"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>ELAVULT Tárolók üzembe helyezése Kubernetes-fürtön a Helm használatával

> [!TIP]
> Az Azure Kubernetes szolgáltatást használó cikk frissített verziója: [alkalmazások telepítése az Azure Kubernetes Service-ben (ak)](../../aks/kubernetes-helm.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

A [Helm](https://github.com/kubernetes/helm/) egy nyílt forráskódú csomagolási eszköz, amely segítséget nyújt a Kubernetes-alkalmazások életciklusának telepítéséhez és kezeléséhez. A Linux csomagkezelő hasonló, például az apt-get és a yum, a Helm a Kubernetes-diagramok kezelésére szolgál, amelyek előre konfigurált Kubernetes-erőforrások csomagjai. Ebből a cikkből megtudhatja, hogyan dolgozhat a Helmtel a Azure Container Service-ben üzembe helyezett Kubernetes-fürtökön.

A Helm két összetevőből áll: 
* A **Helm CLI** egy olyan ügyfél, amely helyileg vagy a felhőben fut a gépen  

* A **kormányrúd** egy olyan kiszolgáló, amely a Kubernetes-fürtön fut, és felügyeli a Kubernetes-alkalmazások életciklusát 
 
## <a name="prerequisites"></a>Előfeltételek

* [Kubernetes-fürt létrehozása](container-service-kubernetes-walkthrough.md) Azure Container Serviceban

* [Telepítés és konfigurálás `kubectl` ](../container-service-connect.md) helyi számítógépen

* A [Helm telepítése](https://github.com/kubernetes/helm/blob/master/docs/install.md) helyi számítógépre

## <a name="helm-basics"></a>A Helm alapjai 

A következő parancs beírásával tekintheti meg a Kubernetes-fürtre vonatkozó információkat, amelyeknek a telepítését és az alkalmazások telepítését végzi:

```bash
kubectl cluster-info 
```
![kubectl-fürt – információ](./media/container-service-kubernetes-helm/clusterinfo.png)
 
A Helm telepítését követően telepítse a következő parancsot a Kubernetes-fürtön:

```bash
helm init --upgrade
```
Ha sikeresen befejeződik, a következőhöz hasonló kimenet jelenik meg:

![Kormányrúd telepítése](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Az adattár összes elérhető Helm-diagramjának megtekintéséhez írja be a következő parancsot:

```bash 
helm search 
```

Az alábbihoz hasonló kimenet jelenik meg:

![Helm-keresés](./media/container-service-kubernetes-helm/helm-search.png)
 
Ha szeretné frissíteni a diagramokat a legújabb verziók beszerzéséhez, írja be a következőt:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Nginx bejövő adatkezelői diagram üzembe helyezése 
 
Egy Nginx bejövő adatkezelő diagramjának üzembe helyezéséhez írjon be egyetlen parancsot:

```bash
helm install stable/nginx-ingress 
```
![Bejövő adatkezelő üzembe helyezése](./media/container-service-kubernetes-helm/nginx-ingress.png)

Ha a fürtön futó összes szolgáltatás megtekintését írja be `kubectl get svc` , láthatja, hogy az IP-cím hozzá van rendelve a bejövő vezérlőhöz. (Amíg a hozzárendelés folyamatban van, megjelenik a következő `<pending>`:. A művelet elvégzése néhány percet vesz igénybe.) 

Az IP-cím hozzárendelése után navigáljon a külső IP-cím értékéhez, és ellenőrizze, hogy fut-e az Nginx-háttér. 
 
![Bejövő IP-cím](./media/container-service-kubernetes-helm/ingress-ip-address.png)


A fürtre telepített diagramok listájának megtekintéséhez írja be a következőt:

```bash
helm list 
```

A parancsot rövidítheti a következőre: `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>MariaDB-diagram és-ügyfél üzembe helyezése

Most helyezzen üzembe egy MariaDB-diagramot és egy MariaDB-ügyfelet az adatbázishoz való kapcsolódáshoz.

A MariaDB diagram telepítéséhez írja be a következő parancsot:

```bash
helm install --name v1 stable/mariadb
```

ahol `--name` a a kiadásokhoz használt címke.

> [!TIP]
> Ha a telepítés sikertelen, futtassa `helm repo update` a parancsot, és próbálkozzon újra.
>
 
 
A fürtön üzembe helyezett összes diagram megtekintéséhez írja be a következőt:

```bash 
helm list
```
 
A fürtön futó összes központi telepítés megtekintéséhez írja be a következőt:

```bash
kubectl get deployments 
``` 
 
 
Végül, ha egy Pod-t szeretne futtatni az ügyfél eléréséhez, írja be a következőt:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Az ügyfélhez való kapcsolódáshoz írja be a következő parancsot, `v1-mariadb` és cserélje le a kifejezést az üzemelő példány nevére:

```bash
sudo mysql –h v1-mariadb
```
 
 
Mostantól szabványos SQL-parancsokkal is létrehozhat adatbázisokat, táblázatokat stb. Például `Create DATABASE testdb1;` létrehoz egy üres adatbázist. 
 
 
 
## <a name="next-steps"></a>További lépések

* A Kubernetes-diagramok kezelésével kapcsolatos további információkért tekintse meg a [Helm dokumentációját](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

