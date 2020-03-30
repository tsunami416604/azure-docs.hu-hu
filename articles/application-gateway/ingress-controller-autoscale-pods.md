---
title: Automatikus skálázású AKS-podok az Azure Application Gateway-metrikákkal
description: Ez a cikk az AKS-háttérpodok méretezésével kapcsolatos útmutatást nyújt az Application Gateway-metrikák és az Azure Kubernetes metrikaadapter használatával
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1169ed0e9a2b970ee0e30d73ea20c87001b62786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239448"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Az AKS-podok automatikus skálázása az Application Gateway metrikáival (béta)

A bejövő forgalom növekedésével elengedhetetlen, hogy az alkalmazások igény alapján felskálázható.

A következő oktatóanyagban bemutatjuk, hogyan használhatja az Application Gateway metrikáját `AvgRequestCountPerHealthyHost` az alkalmazás méretezéséhez. `AvgRequestCountPerHealthyHost`egy adott háttérkészletbe és háttér-HTTP-beállításkombinációba küldött átlagos kérelmeket méri.

A következő két összetevőt fogjuk használni:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter)- A metrikaadaptert az Application Gateway metrikák nak a metrikakiszolgálón keresztül történő elérhetővé tjük. Az Azure Kubernetes metric adapter egy nyílt forráskódú projekt az Azure-ban, hasonló az Application Gateway ingress controller. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler)- HpA használatával fogjuk használni az Application Gateway metrikákat, és a méretezésközponti telepítést.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Az Azure Kubernetes mérőszámadapterének beállítása

1. Először létrehozunk egy Azure AAD `Monitoring Reader` egyszerű szolgáltatást, és hozzárendeljük a hozzáférést az Application Gateway erőforráscsoportján keresztül. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Most telepítjük a [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) fent létrehozott AAD szolgáltatásnév használatával.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Létrehozunk egy `ExternalMetric` erőforrást `appgw-request-count-metric`a nevével. Ez az erőforrás arra utasítja a metrika adaptert, hogy tegye elérhetővé `AvgRequestCountPerHealthyHost` az erőforráscsoportban lévő `myApplicationGateway` `myResourceGroup` erőforrás metrikáját. A `filter` mező segítségével egy adott háttérkészletet és háttérHTTP-beállítást célozhat meg az Application Gateway-ben.

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

Most már kérhet egy kérést a metrikakiszolgálón, hogy lássa, az új metrika elérhetővé tesz-e:
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

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Az új metrika használata a központi telepítés méretezéséhez

Amint képesek vagyunk a metrikakiszolgálón keresztül elérhetővé `appgw-request-count-metric` tenni, készen állunk a [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) céltelepítés felskálázására.

A következő példában egy mintatelepítést fogunk megcélozni. `aspnet` Mi lesz bővíteni podok, amikor `appgw-request-count-metric` > 200 `10` podonként legfeljebb egy podok.

Cserélje le a céltelepítési nevet, és alkalmazza a következő automatikus méretezési konfigurációt:
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

Tesztelje a beállításokat egy terhelésteszt-eszközzel, például apache paddal:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>További lépések
- [**A be- és ac.**](ingress-controller-troubleshoot.md)