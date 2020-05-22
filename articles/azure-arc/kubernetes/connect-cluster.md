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
ms.openlocfilehash: 690955f0e7d18f9a784b4c9a2fa1733442cb70dc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780024"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Azure arc-kompatibilis Kubernetes-fürt összekapcsolása (előzetes verzió)

Kubernetes-fürt összekötése az Azure-ív használatával. 

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy az alábbi követelmények állnak készen:

* Kubernetes-fürt, amely működik és fut
* Szüksége lesz a kubeconfig és a fürt rendszergazdai hozzáférésére. 
* A (z) és a (z) paranccsal használt felhasználónak vagy szolgáltatásnak " `az login` `az connectedk8s connect` READ" és "Write" engedélyekkel kell rendelkeznie a "Microsoft. Kubernetes/connectedclusters" erőforrástípus számára. Az "Azure-Kubernetes bevezetéséhez" szerepkör, amelynek az engedélyei használhatók az Azure CLI-vel való bevezetéshez használt felhasználó vagy szolgáltatásnév szerepkör-hozzárendeléseihez.
* A *connectedk8s* -és *k8sconfiguration* -bővítmények legújabb verziója

## <a name="supported-regions"></a>Támogatott régiók

* USA keleti régiója
* Nyugat-Európa

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Az Azure arc-ügynökök a következő protokollok/portok/kimenő URL-címek működéséhez szükségesek.

* TCP a 443-es porton – >`https://:443`
* TCP a 9418-es porton – >`git://:9418`

| Végpont (DNS)                                                                                               | Leírás                                                                                                                 |
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
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.Kubernetes'

az provider register --namespace Microsoft.KubernetesConfiguration
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.KubernetesConfiguration'
```

A regisztráció egy aszinkron folyamat. A regisztráció körülbelül 10 percet is igénybe vehet. A következő parancsokkal figyelheti a regisztrációs folyamatot:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="install-azure-cli-extensions"></a>Azure CLI-bővítmények telepítése

Telepítse a `connectedk8s` bővítményt, amely segít a Kubernetes-fürtök az Azure-hoz való összekapcsolásában:

```console
az extension add --name connectedk8s
```

A `k8sconfiguration` bővítmény telepítése:

```console
az extension add --name k8sconfiguration
```

Futtassa a következő parancsokat a bővítmények legújabb verzióra való frissítéséhez.

```console
az extension update --name connectedk8s
az extension update --name k8sconfiguration
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
az connectedk8s list -g AzureArcTest
```

**Kimeneti**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

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
* `deployment.apps/cluster-metadata-operator`: a fürt metaadatainak gyűjtése – a fürt verziószáma, a csomópontok száma és az ív ügynök verziója
* `deployment.apps/resource-sync-agent`: szinkronizálja a fent említett fürt metaadatait az Azure-ba
* `deployment.apps/clusteridentityoperator`: karbantartja a más ügynökök által az Azure-nal való kommunikációhoz használt felügyelt szolgáltatás-identitás (MSI) tanúsítványát.
* `deployment.apps/flux-logs-agent`: naplók gyűjtése a verziókövetés konfigurációjának részeként üzembe helyezett Flux-kezelők számára

## <a name="delete-a-connected-cluster"></a>Csatlakoztatott fürt törlése

`Microsoft.Kubernetes/connectedcluster`Az Azure CLI vagy a Azure Portal használatával törölheti az erőforrásokat.

Az Azure CLI `az connectedk8s delete` -parancs eltávolítja az `Microsoft.Kubernetes/connectedCluster` erőforrást az Azure-ban. Az Azure CLI törli az összes kapcsolódó `sourcecontrolconfiguration` erőforrást az Azure-ban. Az Azure CLI a Helm uninstall használatával távolítja el az ügynököket a fürtben.

A Azure Portal törli az `Microsoft.Kubernetes/connectedcluster` erőforrást az Azure-ban, és törli az összes kapcsolódó `sourcecontrolconfiguration` erőforrást az Azure-ban.

Ha el szeretné távolítani a fürtben lévő ügynököket, a vagy a-t kell futtatnia `az connectedk8s delete` `helm uninstall azurearcfork8s` .

## <a name="next-steps"></a>További lépések

* [GitOps használata csatlakoztatott fürtben](./use-gitops-connected-cluster.md)
* [A fürt konfigurációjának szabályozása Azure Policy használatával](./use-azure-policy.md)