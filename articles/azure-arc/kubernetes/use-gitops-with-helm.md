---
title: Helm-diagramok üzembe helyezése GitOps használatával az arc-kompatibilis Kubernetes-fürtön (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Az GitOps és a Helm használata az Azure arc-kompatibilis fürtkonfiguráció (előzetes verzió)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, arc, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: eea81d458ac6631c4a023134b3198e4cdb04526e
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541611"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Helm-diagramok üzembe helyezése GitOps használatával az arc-kompatibilis Kubernetes-fürtön (előzetes verzió)

A Helm egy nyílt forráskódú csomagolási eszköz, amely segítséget nyújt a Kubernetes-alkalmazások életciklusának telepítéséhez és kezeléséhez. A Linux-csomagkezelő, például az APT és a yum hasonlóan a Helm a Kubernetes-diagramok kezelésére szolgál, amelyek előre konfigurált Kubernetes-erőforrások csomagjai.

Ez a cikk bemutatja, hogyan konfigurálhatja és használhatja a Helm-t az Azure arc-kompatibilis Kubernetes.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy rendelkezik egy meglévő Azure arc-kompatibilis Kubernetes csatlakoztatott fürttel. Ha csatlakoztatott fürtre van szüksége, tekintse meg a [fürt csatlakoztatása](./connect-cluster.md)rövid útmutató című témakört.

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>A GitOps és a Helm használatának áttekintése az Azure arc-kompatibilis Kubernetes

 A Helm operátor a Helm chart kiadásait automatizáló adatáramlási bővítményt biztosít. A diagram kiadása egy HelmRelease nevű egyéni Kubernetes-erőforráson keresztül van leírva. A Flux szinkronizálja ezeket az erőforrásokat a git-ből a fürtbe, és a Helm operátor biztosítja, hogy a Helm-diagramok az erőforrásokban megadott módon legyenek közzétéve.

 A dokumentumban használt [példa adattár](https://github.com/Azure/arc-helm-demo) a következő módon van strukturálva:

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

A git-tárházban két címtár található, amelyek közül az egyik egy Helm-diagramot, egy pedig a releases config fájlt tartalmazza. A `releases` címtárban a `app.yaml` tartalmazza az alábbi HelmRelease-konfigurációt:

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

A Helm kiadásának konfigurációja a következő mezőket tartalmazza:

- `metadata.name` kötelező, és követnie kell a Kubernetes elnevezési konvencióit
- `metadata.namespace` nem kötelező, és meghatározza, hogy a rendszer hol hozza létre a kiadást.
- `spec.releaseName` nem kötelező, és ha nincs megadva, akkor a kiadás neve $namespace-$name
- `spec.chart.path` a diagramot tartalmazó könyvtár, amely az adattár gyökeréhez képest van megadva
- `spec.values` a felhasználó a diagram alapértelmezett paramétereinek értékeit is testreszabja

A HelmRelease spec. Values paraméterben megadott beállítások felülbírálják a diagram forrásának Values. YAML megadott beállításait.

A HelmRelease kapcsolatos további információkért tekintse meg a hivatalos [Helm-kezelő dokumentációját](https://docs.fluxcd.io/projects/helm-operator/en/stable/)

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

Az Azure CLI-bővítményének használatával `k8sconfiguration` kapcsolja össze a csatlakoztatott fürtöt a példában szereplő git-adattárral. Ennek a konfigurációnak a nevét `azure-arc-sample` és a Flux-operátor üzembe helyezését a névtérben fogjuk megadni `arc-k8s-demo` .

```console
az k8sconfiguration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Konfigurációs paraméterek

A konfiguráció létrehozásának testreszabásához további tudnivalókat a [használni kívánt paraméterekről](./use-gitops-connected-cluster.md#additional-parameters)olvashat.

## <a name="validate-the-configuration"></a>A konfiguráció ellenőrzése

Az Azure CLI használatával ellenőrizze, hogy a `sourceControlConfiguration` sikeresen létrejött-e.

```console
az k8sconfiguration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Az `sourceControlConfiguration` erőforrás a megfelelőségi állapottal, az üzenetekkel és a hibakeresési információkkal frissül.

**Kimeneti**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "0.6.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Alkalmazás érvényesítése

Futtassa az alábbi parancsot, és navigáljon a `localhost:8080` böngészőjében, és ellenőrizze, hogy fut-e az alkalmazás.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>További lépések

- [A fürt konfigurációjának szabályozása Azure Policy használatával](./use-azure-policy.md)
