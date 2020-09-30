---
title: Az Azure arc-kompatibilis Kubernetes kapcsolatos gyakori problémák elhárítása (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Az arc-kompatibilis Kubernetes-fürtökkel kapcsolatos gyakori problémák elhárítása.
keywords: Kubernetes, arc, Azure, tárolók
ms.openlocfilehash: 4a8f4c652f1ab73e0b9979f77d7de5014c8d31a8
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540608"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>Azure arc-kompatibilis Kubernetes-hibaelhárítás (előzetes verzió)

Ez a dokumentum a kapcsolatokkal, engedélyekkel és ügynökökkel kapcsolatos gyakori hibaelhárítási forgatókönyveket tartalmaz.

## <a name="general-troubleshooting"></a>Általános hibaelhárítás

### <a name="azure-cli-set-up"></a>Azure CLI-beállítás
Az az connectedk8s vagy az k8sconfiguration CLI parancsok használata előtt gondoskodjon arról, hogy az az a megfelelő Azure-előfizetésen működjön.

```console
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure-arc ügynökök
Az Azure arc-kompatibilis Kubernetes összes ügynöke hüvelyként van telepítve a `azure-arc` névtérben. Normál működés esetén az összes hüvelynek futnia kell, és át kell haladnia az állapotuk ellenőrzésével.

Először ellenőrizze az Azure arc Helm kiadását:

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Ha a Helm kiadása nem található vagy hiányzik, próbálja újra bevezetni a fürtöt.

Ha a Helm kiadása jelen van, és `STATUS: deployed` meghatározza az ügynökök állapotát a használatával `kubectl` :

```console
$ kubectl -n azure-arc get deployments,pods
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

Az összes hüvelynek a következőnek kell lennie:, és legyen az `STATUS` `Running` vagy a `READY` `3/3` `2/2` . Naplók beolvasása és a visszaadott vagy a hüvelyek leírása `Error` `CrashLoopBackOff` . Ha az ilyen hüvelyek bármelyike állapota beragadt, annak `Pending` oka az lehet, hogy nincs elegendő erőforrás a fürtcsomópontokon. [A fürt vertikális Felskálázása](https://kubernetes.io/docs/tasks/administer-cluster/cluster-management/#resizing-a-cluster) ezeket a hüvelyeket az állapotba való áttérésre fogja kérni `Running` .

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Kubernetes-fürtök csatlakoztatása az Azure arc-hoz

A fürtök Azure-hoz való csatlakoztatásához az Azure-előfizetéshez és `cluster-admin` a célként megadott fürthöz való hozzáféréshez is hozzá kell férnie. Ha a fürt nem érhető el, vagy nem rendelkezik megfelelő engedélyekkel, az előkészítés sikertelen lesz.

### <a name="insufficient-cluster-permissions"></a>Nem elégségesek a fürt engedélyei

Ha a megadott kubeconfig-fájl nem rendelkezik megfelelő engedélyekkel az Azure arc-ügynökök telepítéséhez, az Azure CLI-parancs hibát ad vissza, amely a Kubernetes API meghívására tesz kísérletet.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

A fürt tulajdonosának olyan Kubernetes-felhasználót kell használnia, amely a fürt rendszergazdai engedélyeivel rendelkezik.

### <a name="installation-timeouts"></a>Telepítési időtúllépések

Az Azure arc-ügynök telepítéséhez tárolók készletét kell futtatni a célként megadott fürtön. Ha a fürt lassú internetkapcsolaton keresztül fut, a tároló rendszerképének lekérése hosszabb időt vehet igénybe, mint az Azure CLI időtúllépése.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helm-probléma

A Helm `v3.3.0-rc.1` verziójának olyan [hibája](https://github.com/helm/helm/pull/8527) van, ahol a Helm install/upgrade (amelyet a motorháztető alatt használ a connectedk8s CLI bővítménnyel) a következő hibához vezető összes Hook futtatását eredményezi:

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

A probléma megoldásához kövesse az alábbi lépéseket:

1. Törölje az Azure arc-kompatibilis Kubernetes-erőforrást a Azure Portal.
2. Futtassa a következő parancsokat a gépen:
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. Telepítse a Helm 3 [stabil verzióját](https://helm.sh/docs/intro/install/) a gépen a kiadásra jelölt verzió helyett.
4. Futtassa a `az connectedk8s connect` parancsot a megfelelő értékekkel, hogy csatlakozhasson a fürthöz az Azure-ív használatával.

## <a name="configuration-management"></a>Konfigurációkezelés

### <a name="general"></a>Általános kérdések
A verziókövetés konfigurációjával kapcsolatos problémák elhárítása érdekében futtassa az az commands with--debug kapcsolót.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>Verziókövetés konfigurációjának létrehozása
A Microsoft. Kubernetes/connectedCluster erőforrás közreműködői szerepköre szükséges és elegendő a Microsoft. KubernetesConfiguration/sourceControlConfiguration erőforrás létrehozásához.

### <a name="configuration-remains-pending"></a>A konfiguráció továbbra is fennáll `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>Figyelés

A tárolók Azure Monitor számára szükséges, hogy a Daemonset elemet privilegizált módban fussanak. Egy Canonical Charmed Kubernetes-fürt sikeres beállításához a figyeléshez futtassa a következő parancsot:

```console
juju config kubernetes-worker allow-privileged=true
```