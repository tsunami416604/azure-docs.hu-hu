---
title: Azure arc-kompatibilis Kubernetes-fürt összekapcsolása (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure arc-kompatibilis Kubernetes-fürt összekapcsolása az Azure arc szolgáltatással
keywords: Kubernetes, arc, Azure, K8s, tárolók
ms.custom: references_regions
ms.openlocfilehash: 2c5e697f3dd67087582118fb6a6e083feecf549f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050091"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Azure arc-kompatibilis Kubernetes-fürt összekapcsolása (előzetes verzió)

Ez a dokumentum a Cloud Native Computing Foundation (CNCF) tanúsítvánnyal rendelkező Kubernetes-fürtök csatlakoztatásának folyamatát ismerteti, például: AK-motor az Azure-ban, AK-motor az Azure Stack hub, a GKE, a EKS és a VMware vSphere-fürt számára az Azure arc.

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy az alábbi követelmények állnak készen:

* Kubernetes-fürt, amely működik. Ha nem rendelkezik meglévő Kubernetes-fürttel, a következő útmutatók egyikével hozhat létre egy tesztelési fürtöt:
  * Kubernetes-fürt létrehozása a [Kubernetes használatával a Docker-ben (fajta)](https://kind.sigs.k8s.io/)
  * Kubernetes-fürt létrehozása a Docker használatával [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) vagy [Windows rendszerhez](https://docs.docker.com/docker-for-windows/#kubernetes)
* Szüksége lesz egy kubeconfig-fájlra a fürt és a fürt rendszergazdai szerepkörének eléréséhez a fürtön az arc-kompatibilis Kubernetes-ügynökök telepítéséhez.
* A (z) és a (z) paranccsal használt felhasználónak vagy szolgáltatásnak " `az login` `az connectedk8s connect` READ" és "Write" engedélyekkel kell rendelkeznie a "Microsoft. Kubernetes/connectedclusters" erőforrástípus számára. A "Kubernetes-fürt – Azure arc bevezetése" szerepkör rendelkezik ezekkel az engedélyekkel, és a felhasználó vagy az egyszerű szolgáltatás szerepkör-hozzárendeléseihez is használható.
* A connectedk8s-bővítmény használatával a fürt bevezetéséhez a Helm 3 szükséges. [Telepítse a Helm 3 legújabb kiadását](https://helm.sh/docs/intro/install) , hogy megfeleljen ennek a követelménynek.
* Az Azure CLI-hez készült Kubernetes CLI-bővítmények telepítéséhez az Azure CLI 2.3-s verziója szükséges. [Telepítse az Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) -t vagy frissítsen a legújabb verzióra, és győződjön meg arról, hogy az Azure CLI 2.3-as vagy újabb verziója van.
* Az arc-kompatibilis Kubernetes CLI-bővítményeinek telepítése:
  
  Telepítse a `connectedk8s` bővítményt, amely segít a Kubernetes-fürtök az Azure-hoz való összekapcsolásában:
  
  ```console
  az extension add --name connectedk8s
  ```
  
  A `k8sconfiguration` bővítmény telepítése:
  
  ```console
  az extension add --name k8sconfiguration
  ```
  
  Ha később szeretné frissíteni ezeket a bővítményeket, futtassa a következő parancsokat:
  
  ```console
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Támogatott régiók

* USA keleti régiója
* Nyugat-Európa

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Az Azure arc-ügynökök a következő protokollok/portok/kimenő URL-címek működéséhez szükségesek.

* TCP a 443-es porton – >`https://:443`
* TCP a 9418-es porton – >`git://:9418`

| Végpont (DNS)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Ahhoz szükséges, hogy az ügynök csatlakozhasson az Azure-hoz, és regisztrálja a fürtöt                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Adatsík végpontja az ügynök számára az állapot leküldéséhez és a konfigurációs adatok beolvasásához                                      |
| `https://docker.io`                                                                                            | A tároló lemezképének lekéréséhez szükséges                                                                                         |
| `https://github.com`, git://github.com                                                                         | Például a GitOps repók a GitHubon futnak. A konfigurációs ügynöknek a megadott git-végponthoz való kapcsolódásra van szüksége. |
| `https://login.microsoftonline.com`                                                                            | Azure Resource Manager tokenek beolvasásához és frissítéséhez szükséges                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | A tároló lemezképének lekéréséhez szükséges az Azure arc-ügynökökhöz                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Regisztrálja a két szolgáltatót az Azure arc-kompatibilis Kubernetes:

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

A regisztráció egy aszinkron folyamat. A regisztráció körülbelül 10 percet is igénybe vehet. A következő parancsokkal figyelheti a regisztrációs folyamatot:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Egy erőforráscsoport használatával tárolhatja a fürt metaadatait.

Először hozzon létre egy erőforráscsoportot a csatlakoztatott fürterőforrás tárolásához.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Kimeneti**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Fürt összekötése

Ezután a Kubernetes-fürtöt az Azure-ba fogjuk kapcsolni. A munkafolyamat a `az connectedk8s connect` következő:

1. A Kubernetes-fürthöz való csatlakozás ellenőrzése: a, a vagy a használatával `KUBECONFIG` `~/.kube/config``--kube-config`
1. Azure arc-ügynökök üzembe helyezése a Kubernetes a Helm 3 használatával a `azure-arc` névtérben

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**Kimeneti**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Csatlakoztatott fürt ellenőrzése

A csatlakoztatott fürtök listázása:

```console
az connectedk8s list -g AzureArcTest -o table
```

**Kimeneti**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Ezt az erőforrást a [Azure Portal](https://portal.azure.com/)is megtekintheti. Miután megnyitotta a portált a böngészőben, navigáljon az erőforráscsoporthoz és az Azure arc-kompatibilis Kubernetes-erőforráshoz a parancs korábbi részében használt erőforrás neve és erőforráscsoport-név bemenete alapján `az connectedk8s connect` .

> [!NOTE]
> A fürt bevezetését követően 5 – 10 percet vesz igénybe, hogy a fürt metaadatai (a fürt verziója, az ügynök verziója, a csomópontok száma) az Azure arc-kompatibilis Kubernetes-erőforrás áttekintés lapján legyenek felszínre Azure Portal.

Az Azure arc-kompatibilis Kubernetes üzembe helyez néhány operátort a `azure-arc` névtérben. Ezeket a központi telepítéseket és hüvelyeket itt tekintheti meg:

```console
kubectl -n azure-arc get deployments,pods
```

**Kimeneti**

```console
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

## <a name="azure-arc-agents-for-kubernetes"></a>Azure arc-ügynökök a Kubernetes

Az Azure arc-kompatibilis Kubernetes néhány ügynököt (operátort) tartalmaz, amelyek a névtérben üzembe helyezett fürtön futnak `azure-arc` .

* `deployment.apps/config-agent`: a fürtön alkalmazott forrás-vezérlési konfigurációs erőforrások, valamint a frissítések megfelelőségi állapotának figyeli a csatlakoztatott fürtöt
* `deployment.apps/controller-manager`: az operátorok és az Azure arc-összetevők közötti interakciók
* `deployment.apps/metrics-agent`: más ív-ügynökök metrikáinak gyűjtésével biztosíthatja, hogy ezek az ügynökök optimális teljesítményt mutassanak
* `deployment.apps/cluster-metadata-operator`: a fürt metaadatait – a fürt verzióját, a csomópontok darabszámát és az Azure arc-ügynök verzióját gyűjti.
* `deployment.apps/resource-sync-agent`: szinkronizálja a fent említett fürt metaadatait az Azure-ba
* `deployment.apps/clusteridentityoperator`: Az Azure arc-kompatibilis Kubernetes jelenleg támogatja a rendszerhez rendelt identitást. a clusteridentityoperator fenntartja a más ügynökök által az Azure-nal folytatott kommunikációhoz használt felügyelt szolgáltatás-identitás (MSI) tanúsítványát.
* `deployment.apps/flux-logs-agent`: naplók gyűjtése a verziókövetés konfigurációjának részeként üzembe helyezett Flux-kezelők számára

## <a name="delete-a-connected-cluster"></a>Csatlakoztatott fürt törlése

`Microsoft.Kubernetes/connectedcluster`Az Azure CLI vagy a Azure Portal használatával törölheti az erőforrásokat.


* **Törlés az Azure CLI használatával**: az Azure arc-kompatibilis Kubernetes-erőforrás törlésének elindításához a következő Azure CLI-parancs használható.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Ez a parancs eltávolítja az `Microsoft.Kubernetes/connectedCluster` erőforrást és az összes kapcsolódó `sourcecontrolconfiguration` erőforrást az Azure-ban. Az Azure CLI a Helm uninstall használatával távolítja el a fürtön futó ügynököket is.

* **Törlés Azure Portal**: az Azure arc-kompatibilis Kubernetes-erőforrás törlése a Azure Portal törli az `Microsoft.Kubernetes/connectedcluster` erőforrást és a hozzá tartozó összes `sourcecontrolconfiguration` erőforrást az Azure-ban, de nem törli a fürtön futó ügynököket. A fürtön futó ügynökök törléséhez futtassa a következő parancsot.

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>További lépések

* [GitOps használata csatlakoztatott fürtben](./use-gitops-connected-cluster.md)
* [A fürt konfigurációjának szabályozása Azure Policy használatával](./use-azure-policy.md)
