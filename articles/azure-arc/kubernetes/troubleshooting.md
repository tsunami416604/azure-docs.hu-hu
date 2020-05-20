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
ms.openlocfilehash: e0583fdeaa0819ec961e87ae89ee3aa7592d1f79
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680705"
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
$ kubectl -n azure-arc get deploy,pods
NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/config-agent         1/1     1            1           53s
deployment.apps/connect-agent        1/1     1            1           53s
deployment.apps/controller-manager   1/1     1            1           53s
deployment.apps/metrics-agent        1/1     1            1           53s

NAME                                      READY   STATUS    RESTARTS   AGE
pod/config-agent-74cf758b5f-cxnhs         2/2     Running   0          53s
pod/connect-agent-bc6b9ff5d-dzkvf         2/2     Running   0          53s
pod/controller-manager-7cf95d5d77-wv5cw   2/2     Running   0          53s
pod/metrics-agent-c77c9dfc7-45n5r         1/1     Running   0          53s
```

Az összes hüvelynek a következőnek kell lennie:, és legyen az `STATUS` `Running` vagy a `READY` `2/2` `1/1` . Naplók beolvasása és a visszaadott vagy a hüvelyek leírása `Error` `CrashLoopBackOff` .

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

### <a name="incorrect-or-expired-onboarding-credentials"></a>Helytelen vagy lejárt bevezetési hitelesítő adatok

```console
$ kubectl -n azure-arc get deploy,pod
NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/config-agent         1/1     1            1           8m11s
deployment.apps/connect-agent        0/1     1            0           8m11s
deployment.apps/controller-manager   1/1     1            1           8m11s
deployment.apps/metrics-agent        1/1     1            1           8m11s

NAME                                      READY   STATUS             RESTARTS   AGE
pod/config-agent-74cf758b5f-d7qz9         2/2     Running            0          8m11s
pod/connect-agent-bc6b9ff5d-sd9fb         1/2     CrashLoopBackOff   6          8m11s
pod/controller-manager-7cf95d5d77-qlsvs   2/2     Running            0          8m11s
pod/metrics-agent-c77c9dfc7-lp2rf         1/1     Running            1          8m11s
```

A összekapcsolási ügynök minden olyan hibát naplóz, amely az Azure-val és a helyi Kubernetes API-kiszolgálóval kommunikál szabványos Pod-naplókként. A naplók beolvasása a `kubectl` hibakereséshez.

```console
$ kubectl -n azure-arc logs -l app.kubernetes.io/component=connect-agent -c connect-agent
2020/04/07 20:52:50 Environment validation :success
2020/04/07 20:52:50 Kubernetes API server access validation :success
2020/04/07 20:52:51 Azure Subscription access token :error :http request failed. Authentication Token URL:https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47/oauth2/token Authentication Token Body:grant_type=client_credentials&client_id=82195c37-7497-458c-b643-f4a3d0a64190&client_secret=9814c84e-59d7-49fc-bef6-17b717d2f5a8&resource=https%3A%2F%2Fmanagement.azure.com%2F ErrorInfo: Response:{"error":"invalid_client","error_description":"AADSTS7000215: Invalid client secret is provided.\r\nTrace ID: b179b7db-c957-4917-a1b6-66fab2042a00\r\nCorrelation ID: 4cfc9c81-660f-4a1a-ba0b-87db205c5461\r\nTimestamp: 2020-04-07 20:52:51Z","error_codes":[7000215],"timestamp":"2020-04-07 20:52:51Z","trace_id":"b179b7db-c957-4917-a1b6-66fab2042a00","correlation_id":"4cfc9c81-660f-4a1a-ba0b-87db205c5461","error_uri":"https://login.microsoftonline.com/error?code=7000215"} HTTPReturnCode:401
```

Érvénytelen ügyfél-hitelesítő adatok kijavításához ellenőrizze, hogy helyesek-e a client_id és a titkos kulcs:

```console
$ kubectl -n azure-arc get cm/azure-clusterconfig -o yaml
  AZURE_CLIENT_ID: 82195c37-7497-458c-b643-f4a3d0a64190
  AZURE_RESOURCE_GROUP: AzureArc
  AZURE_RESOURCE_NAME: AzureArcCluster
```

### <a name="expired-credentials"></a>Lejárt hitelesítő adatok

Az egyszerű szolgáltatásnév hitelesítő adatai miatt a kapcsolódási ügynök hibát naplóz `AADSTS7000222: The provided client secret keys are expired` .

```console
$ kubectl -n azure-arc logs -l app.kubernetes.io/component=connect-agent -c connect-agent
2020/04/13 19:49:19 Environment validation :success
2020/04/13 19:49:19 Kubernetes API server access validation :success
2020/04/13 19:49:19 Azure Subscription access token :error :http request failed. Authentication Token URL:https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47/oauth2/token Authentication Token Body:grant_type=client_credentials&client_id=82195c37-7497-458c-b643-f4a3d0a64190&client_secret=9814c84e-59d7-49fc-bef6-17b717d2f5a8&resource=https%3A%2F%2Fmanagement.azure.com%2F ErrorInfo: Response:{"error":"invalid_client","error_description":"AADSTS7000222: The provided client secret keys are expired.\r\nTrace ID: 69ade0e5-f089-4a9d-b55d-9089e07f6300\r\nCorrelation ID: 10057011-6143-4e87-ad4a-c8256cf0e353\r\nTimestamp: 2020-04-13 19:49:19Z","error_codes":[7000222],"timestamp":"2020-04-13 19:49:19Z","trace_id":"69ade0e5-f089-4a9d-b55d-9089e07f6300","correlation_id":"10057011-6143-4e87-ad4a-c8256cf0e353"} HTTPReturnCode:401
```

Előfordulhat, hogy a lejárt hitelesítő adatok alaphelyzetbe állnak a használatával `az ad sp credential reset` .

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
