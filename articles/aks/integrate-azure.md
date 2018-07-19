---
title: Integrálás az Azure által kezelt szolgáltatásokkal az Open Service Broker for Azure (OSBA) használatával
description: Integrálás az Azure által kezelt szolgáltatásokkal az Open Service Broker for Azure (OSBA) használatával
services: container-service
author: sozercan
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 12/05/2017
ms.author: seozerca
ms.openlocfilehash: cebd98ec31ae6089c20952924c39ee240cb5d6a2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002698"
---
# <a name="integrate-with-azure-managed-services-using-open-service-broker-for-azure-osba"></a>Integrálás az Azure által kezelt szolgáltatásokkal az Open Service Broker for Azure (OSBA) használatával

A [Kubernetes szolgáltatáskatalógussal][kubernetes-service-catalog] együtt az Open Service Broker for Azure (OSBA) lehetővé teszi a fejlesztők számára, hogy az Azure által kezelt szolgáltatásokat használhassák a Kubernetesben. Ez az útmutató a Kubernetes szolgáltatáskatalógus, az Open Service Broker for Azure (OSBA) és olyan alkalmazások üzembe helyezésére összpontosít, amelyek a Kubernetes használatával vesznek igénybe Azure által kezelt szolgáltatásokat.

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés

* Azure CLI: [telepítheti helyileg][azure-cli-install], vagy használhatja az [Azure Cloud Shellben][azure-cloud-shell].

* Helm CLI 2.7+: [Telepítheti helyileg][helm-cli-install], vagy használhatja az [Azure Cloud Shellben][azure-cloud-shell].

* Engedély egyszerű szolgáltatás létrehozásához Azure-előfizetése Közreműködő szerepkörében

* Egy meglévő Azure Kubernetes Service- (AKS-) fürt. Ha egy AKS-fürtre van szüksége, kövesse az [AKS-fürt létrehozása][create-aks-cluster] című rövid útmutatót.

## <a name="install-service-catalog"></a>Szolgáltatáskatalógus telepítése

Első lépésként egy Helm-diagram használatával telepítse a szolgáltatáskatalógust a Kubernetes-fürtbe. Frissítse a Tiller-példányát (Helm-kiszolgáló) a fürtben az alábbi módon:

```azurecli-interactive
helm init --upgrade
```

Ezután adja hozzá a szolgáltatáskatalógust a Helm-adattárhoz:

```azurecli-interactive
helm repo add svc-cat https://svc-catalog-charts.storage.googleapis.com
```

Végül telepítse a szolgáltatáskatalógust a Helm-diagrammal. Ha a fürtjén engedélyezve van az RBAC, futtassa ezt a parancsot.

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set controllerManager.healthcheck.enabled=false
```

Ha a fürtjén nincs engedélyezve az RBAC, futtassa ezt a parancsot.

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set rbacEnable=false --set controllerManager.healthcheck.enabled=false
```

Miután futtatta a Helm-diagramot, ellenőrizze, hogy a `servicecatalog` megjelenik az alábbi parancs kimenetében:

```azurecli-interactive
kubectl get apiservice
```

Például az alábbi kimenethez hasonlónak kell megjelennie (itt csonkolva látható):

```
NAME                                 AGE
v1.                                  10m
v1.authentication.k8s.io             10m
...
v1beta1.servicecatalog.k8s.io        34s
v1beta1.storage.k8s.io               10
```

## <a name="install-open-service-broker-for-azure"></a>Az Open Service Broker for Azure telepítése

A következő lépés az [Open Service Broker for Azure][open-service-broker-azure] telepítése, amely tartalmazza az Azure által kezelt szolgáltatások katalógusát. Az elérhető Azure-szolgáltatások között megtalálható például az Azure Database for PostgreSQL, az Azure Database for MySQL és az Azure SQL Database.

Kezdésnek adja hozzá az Open Service Broker for Azure Helm-adattárát:

```azurecli-interactive
helm repo add azure https://kubernetescharts.blob.core.windows.net/azure
```

Hozzon létre egy [egyszerű szolgáltatást][create-service-principal] az alábbi Azure CLI-paranccsal:

```azurecli-interactive
az ad sp create-for-rbac
```

A kimenet az alábbihoz hasonló lesz. Jegyezze fel az `appId`, a `password`, és a `tenant` értékét, mert a következő lépésben használni fogja őket.

```JSON
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

Állítsa be az alábbi környezeti változókat az előbbi értékekkel:

```azurecli-interactive
AZURE_CLIENT_ID=<appId>
AZURE_CLIENT_SECRET=<password>
AZURE_TENANT_ID=<tenant>
```

Most vegye elő Azure-előfizetési azonosítóját:

```azurecli-interactive
az account show --query id --output tsv
```

Majd állítsa be az alábbi környezeti változót az előbbi értékkel:

```azurecli-interactive
AZURE_SUBSCRIPTION_ID=[your Azure subscription ID from above]
```

Miután feltöltötte értékkel ezeket a környezeti változókat, hajtsa végre az alábbi parancsot az Open Service Broker for Azure a Helm-diagram használatával történő telepítéséhez:

```azurecli-interactive
helm install azure/open-service-broker-azure --name osba --namespace osba \
    --set azure.subscriptionId=$AZURE_SUBSCRIPTION_ID \
    --set azure.tenantId=$AZURE_TENANT_ID \
    --set azure.clientId=$AZURE_CLIENT_ID \
    --set azure.clientSecret=$AZURE_CLIENT_SECRET
```

Ha az OSBA üzembe helyezése befejeződött, telepítse a [szolgáltatáskatalógus CLI-jét][service-catalog-cli]. Ez egy könnyen használható parancssori felület, amellyel lekérdezhetők többek között a szolgáltatásközvetítők, a szolgáltatásosztályok és a szolgáltatáscsomagok.

Hajtsa végre az alábbi parancsokat a szolgáltatáskatalógus parancssori felületi binárisának telepítéséhez:

```azurecli-interactive
curl -sLO https://servicecatalogcli.blob.core.windows.net/cli/latest/$(uname -s)/$(uname -m)/svcat
chmod +x ./svcat
```

Ezután listázza ki a telepített szolgáltatásközvetítőket:

```azurecli-interactive
./svcat get brokers
```

A következőhöz hasonló kimenetnek kell megjelennie:

```
  NAME                               URL                                STATUS
+------+--------------------------------------------------------------+--------+
  osba   http://osba-open-service-broker-azure.osba.svc.cluster.local   Ready
```

Ezt követően listázza ki az elérhető szolgáltatásosztályokat. A megjelenített szolgáltatásosztályok azok az elérhető, az Azure által kezelt szolgáltatások, amelyek kioszthatóak az Open Service Broker for Azure-on keresztül.

```azurecli-interactive
./svcat get classes
```

Végül listázza ki az elérhető szolgáltatáscsomagokat. A szolgáltatáscsomagok az Azure által kezelt szolgáltatások szolgáltatási szintjei. Például az Azure Database for MySQL esetében a csomagok a `basic50` alapszintű, 50 adatbázis-tranzakciós egységtől (DTU) a `standard800` standard szintű, 800 adatbázis-tranzakciós egységig terjednek.

```azurecli-interactive
./svcat get plans
```

## <a name="install-wordpress-from-helm-chart-using-azure-database-for-mysql"></a>A WordPress telepítése Helm-diagramból az Azure Database for MySQL használatával

Ebben a lépésben a Helm használatával telepítünk egy frissített Helm-diagramot a WordPress számára. A diagram egy külső Azure Database for MySQL-példányt helyez üzembe, amelyet a WordPress használhat. Ez eltarthat pár percig.

```azurecli-interactive
helm install azure/wordpress --name wordpress --namespace wordpress --set resources.requests.cpu=0
```

Ha meg szeretne győződni arról, hogy a telepítés a megfelelő erőforrásokat osztotta ki, listázza ki a telepített szolgáltatáspéldányokat és kötéseket:

```azurecli-interactive
./svcat get instances -n wordpress
./svcat get bindings -n wordpress
```

Listázza ki a telepített titkos kulcsokat:

```azurecli-interactive
kubectl get secrets -n wordpress -o yaml
```

## <a name="next-steps"></a>További lépések

A cikk útmutatásait követve üzembe helyezte a szolgáltatáskatalógust egy Azure Kubernetes Service- (AKS-) fürtön. Az Open Service Broker for Azure használatával üzembe helyezett egy WordPress-példányt, amely az Azure által kezelt szolgáltatásokat használja, ebben az esetben az Azure Database for MySQL-t.

További frissített, OSBA-alapú Helm-diagramok eléréséhez tekintse meg az [Azure/helm-charts][helm-charts] adattárat. Ha szeretné saját, az OSBA-val működő diagramjait létrehozni, tekintse meg az [új diagram létrehozását][helm-create-new-chart] ismertető részt.

<!-- LINKS - external -->
[helm-charts]: https://github.com/Azure/helm-charts
[helm-cli-install]: kubernetes-helm.md#install-helm-cli
[helm-create-new-chart]: https://github.com/Azure/helm-charts#creating-a-new-chart
[kubernetes-service-catalog]: https://github.com/kubernetes-incubator/service-catalog
[open-service-broker-azure]: https://github.com/Azure/open-service-broker-azure
[service-catalog-cli]: https://github.com/Azure/service-catalog-cli

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[create-aks-cluster]: ./kubernetes-walkthrough.md
[create-service-principal]: ./kubernetes-service-principal.md
