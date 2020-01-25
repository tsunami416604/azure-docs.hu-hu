---
title: AK-hüvelyek autoskálázása Azure Application Gateway metrikákkal
description: Ez a cikk útmutatást nyújt az AK háttérbeli hüvelyek Application Gateway mérőszámok és az Azure Kubernetes metrikus adapter használatával történő méretezéséhez
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: b98ab8d3c4d03115ea689b4dfd3d8dee753f019d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715077"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>AK-hüvelyek autoskálázása Application Gateway metrikák (bétaverzió) használatával

Ahogy a bejövő forgalom megnövekszik, elengedhetetlen az alkalmazások vertikális felskálázása az igények alapján.

Az alábbi oktatóanyagban azt ismertetjük, hogyan használható a Application Gateway `AvgRequestCountPerHealthyHost` metrikája az alkalmazás vertikális felskálázásához. `AvgRequestCountPerHealthyHost` az adott háttérrendszer-készletre és a háttérbeli HTTP-beállítási kombinációra küldött kérelmek átlagos kérelmét méri.

A következő két összetevőt fogjuk használni:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) – a metrikai adapter használatával tesz elérhetővé Application Gateway metrikákat a metrika-kiszolgálón. Az Azure Kubernetes metrika-adapter egy nyílt forráskódú projekt az Azure-ban, amely a Application Gateway beáramló vezérlőhöz hasonlóan működik. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) – a HPA használatával Application Gateway metrikákat használhat, és megcélozhatja a skálázási üzembe helyezést.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Az Azure Kubernetes metrikus adapter beállítása

1. Először létre kell hozni egy Azure HRE egyszerű szolgáltatásnevet, és hozzá kell rendelnie `Monitoring Reader` hozzáférést Application Gateway erőforráscsoporthoz. 

    ```bash
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Most a fent létrehozott HRE egyszerű szolgáltatásnév használatával telepítjük a [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) .

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Létre fogunk hozni egy `appgw-request-count-metric`nevű `ExternalMetric`-erőforrást. Ez az erőforrás utasítja a metrikai adaptert, hogy `myResourceGroup` erőforráscsoport `myApplicationGateway` erőforrásának `AvgRequestCountPerHealthyHost` metrikáját elérhetővé tegye. A `filter` mező használatával megcélozhat egy adott háttérbeli készletet és a háttérbeli HTTP-beállítást a Application Gateway.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

Mostantól kérheti a metrika-kiszolgálónak, hogy ellenőrizze, hogy az új metrika elérhető-e:
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Az új metrika használata az üzemelő példány vertikális felskálázásához

Ha a metrika-kiszolgálón keresztül elérhetővé `appgw-request-count-metric`, készen állunk a [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) használatára a cél üzembe helyezésének vertikális felskálázásához.

A következő példában egy minta üzembe helyezési `aspnet`fogunk megcélozni. A hüvelyek felskálázása akkor történik, amikor a `appgw-request-count-metric` > 200/Pod akár Max `10` Hüvelyig.

Cserélje le a cél központi telepítési nevét, és alkalmazza a következő automatikus méretezési konfigurációt:
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Tesztelje a telepítőt egy terhelési teszt eszközzel, például az Apache pad használatával:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Következő lépések
- A [**beáramló vezérlővel kapcsolatos problémák elhárítása**](ingress-controller-troubleshoot.md): a bejövő adatkezelővel kapcsolatos problémák elhárítása.