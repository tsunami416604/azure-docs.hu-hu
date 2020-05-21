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
ms.openlocfilehash: 1527f8d4ca06c2deaf4ce18b73bfdb515dcadc63
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725584"
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

Az összes hüvelynek a következőnek kell lennie:, és legyen az `STATUS` `Running` vagy a `READY` `3/3` `2/2` . Naplók beolvasása és a visszaadott vagy a hüvelyek leírása `Error` `CrashLoopBackOff` .

## <a name="unable-to-connect-my-kubernetes-cluster-to-azure"></a>Nem lehet csatlakozni a Kubernetes-fürthöz az Azure-hoz

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

There was a problem with connect-agent deployment. Please run 'kubectl -n azure-arc logs -l app.kubernetes.io/component=connect-agent -c connect-agent' to debug the error.
```

## <a name="configuration-management"></a>Konfigurációkezelés

### <a name="general"></a>Általános kérdések
A verziókövetés konfigurációjával kapcsolatos problémák elhárítása érdekében futtassa az az commands with--debug kapcsolót.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>Verziókövetés konfigurációjának létrehozása
A Microsoft. Kubernetes/connectedCluster erőforrás közreműködői szerepköre szükséges és elegendő a Microsoft. KubernetesConfiguration/sourceControlConfiguration erőforrás létrehozásához.

### <a name="configuration-remains-pending"></a>A konfiguráció továbbra is fennáll`Pending`

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
