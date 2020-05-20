---
title: Az GitOps és a Helm használata az Azure arc-kompatibilis fürtkonfiguráció (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Az GitOps és a Helm használata az Azure arc-kompatibilis fürtkonfiguráció (előzetes verzió)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, arc, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: 9cd1169c7a622da0e4be3900f94dc31fc99e762d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664157"
---
# <a name="use-gitops-with-helm-for-an-azure-arc-enabled-cluster-configuration-preview"></a>Az GitOps és a Helm használata az Azure arc-kompatibilis fürtkonfiguráció (előzetes verzió)

A Helm egy nyílt forráskódú csomagolási eszköz, amely segítséget nyújt a Kubernetes-alkalmazások életciklusának telepítéséhez és kezeléséhez. A Linux-csomagkezelő, például az APT és a yum hasonlóan a Helm a Kubernetes-diagramok kezelésére szolgál, amelyek előre konfigurált Kubernetes-erőforrások csomagjai.

Ez a cikk bemutatja, hogyan konfigurálhatja és használhatja a Helm-t az Azure arc-kompatibilis Kubernetes.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy rendelkezik egy meglévő Azure arc-kompatibilis Kubernetes csatlakoztatott fürttel. Ha csatlakoztatott fürtre van szüksége, tekintse meg a [fürt csatlakoztatása](./connect-cluster.md)rövid útmutató című témakört.

Először állítsa be az oktatóanyag során használt környezeti változókat. Szüksége lesz az erőforráscsoport nevére és a fürt nevére a csatlakoztatott fürthöz.

```bash
export RESOURCE_GROUP=<Resource_Group_Name>
export CLUSTER_NAME=<ClusterName>
```

## <a name="verify-your-cluster-is-enabled-with-arc"></a>Ellenőrizze, hogy a fürt engedélyezve van-e az arc

```bash
az connectedk8s list -g $RESOURCE_GROUP -o table
```

Kimenet:
```bash
Name           Location    ResourceGroup
-------------  ----------  ---------------
arc-helm-demo  eastus      k8s-clusters
```

## <a name="overview-of-using-helm-with-azure-arc-enabled-kubernetes"></a>A Helm használatának áttekintése az Azure arc-kompatibilis Kubernetes

 A Helm operátor a Helm chart kiadásait automatizáló adatáramlási bővítményt biztosít. A diagram kiadása egy HelmRelease nevű egyéni Kubernetes-erőforráson keresztül van leírva. A Flux szinkronizálja ezeket az erőforrásokat a git-ből a fürtbe, és a Helm operátor biztosítja, hogy a Helm-diagramok az erőforrásokban megadott módon legyenek közzétéve.

 Az alábbiakban egy példa git-tárház struktúrát fogunk használni ebben az oktatóanyagban:

```bash
├── charts
│   └── azure-vote
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── prod
        └── azure-vote-app.yaml
```

A git-tárházban két címtár található, amelyek közül az egyik egy Helm-diagramot, egy pedig a releases config fájlt tartalmazza. A `releases/prod` könyvtárban a `azure-vote-app.yaml` tartalmazza az alábbi HelmRelease-konfigurációt:

```bash
 apiVersion: helm.fluxcd.io/v1
 kind: HelmRelease
 metadata:
   name: azure-vote-app
   namespace: prod
   annotations:
 spec:
   releaseName: azure-vote-app
   chart:
     git: https://github.com/Azure/arc-helm-demo
     path: charts/azure-vote
     ref: master
   values:
     image:
       repository: dstrebel/azurevote
       tag: v1
     replicaCount: 3
```

A Helm kiadásának konfigurációja a következő mezőket tartalmazza:

- `metadata.name`kötelező, és követnie kell a Kubernetes elnevezési konvencióit
- `metadata.namespace`nem kötelező, és meghatározza, hogy a rendszer hol hozza létre a kiadást.
- `spec.releaseName`nem kötelező, és ha nincs megadva, akkor a kiadás neve $namespace-$name
- `spec.chart.path`a diagramot tartalmazó könyvtár, amely az adattár gyökeréhez képest van megadva
- `spec.values`a felhasználó a diagram alapértelmezett paramétereinek értékeit is testreszabja

A HelmRelease spec. Values paraméterben megadott beállítások felülbírálják a diagram forrásának Values. YAML megadott beállításait.

A HelmRelease kapcsolatos további információkért tekintse meg a hivatalos [Helm-kezelő dokumentációját](https://docs.fluxcd.io/projects/helm-operator/en/1.0.0-rc9/references/helmrelease-custom-resource.html)

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

Az Azure CLI-bővítményének használatával `k8sconfiguration` kapcsolja össze a csatlakoztatott fürtöt a példában szereplő git-adattárral. Ennek a konfigurációnak a nevét `azure-voting-app` és a Flux-operátor üzembe helyezését a névtérben fogjuk megadni `prod` .

```bash
az k8sconfiguration create --name azure-voting-app --resource-group  $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --operator-instance-name azure-voting-app --operator-namespace prod --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --operator-params='--git-readonly --git-path=releases/prod' --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Konfigurációs paraméterek

A konfiguráció létrehozásának testreszabásához további tudnivalókat a [használni kívánt paraméterekről](./use-gitops-connected-cluster.md#additional-parameters)olvashat.


## <a name="validate-the-configuration"></a>A konfiguráció ellenőrzése

Az Azure CLI használatával ellenőrizze, hogy a `sourceControlConfiguration` sikeresen létrejött-e.

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

Vegye figyelembe, hogy az `sourceControlConfiguration` erőforrás a megfelelőségi állapottal, az üzenetekkel és a hibakeresési információkkal frissül.

**Kimeneti**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Compliant",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "azure-vote-app",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "prod",
  "operatorParams": "--git-readonly --git-path=releases/prod",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Alkalmazás érvényesítése

Most ellenőrizzük, hogy az alkalmazás a szolgáltatás IP-címének beszerzésével működik-e.

```bash
kubectl get svc/azure-vote-front -n prod
```

**Kimeneti**

```bash
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.14.161   52.186.160.216   80:30372/TCP   4d22h
```

Keresse meg a fenti kimenetből a külső IP-címet, és nyissa meg egy böngészőben.

## <a name="next-steps"></a>További lépések

- [A fürt konfigurációjának szabályozása Azure Policy használatával](./use-azure-policy.md)
