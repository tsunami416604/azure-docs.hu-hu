---
title: Bejövő adatkezelő létrehozása
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan telepíthet és konfigurálhat egy alapszintű NGINX beáramlási vezérlőt egy Azure Kubernetes-szolgáltatási (ak-) fürtben.
services: container-service
ms.topic: article
ms.date: 12/20/2019
ms.openlocfilehash: 689cfe4c94ba83b4c0b8815fd7b0438da32f6018
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82145961"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Bejövő adatvezérlő létrehozása az Azure Kubernetes szolgáltatásban (ak)

A bejövő vezérlő egy olyan szoftver, amely fordított proxyt, konfigurálható forgalmi útválasztást és TLS-megszakítást biztosít a Kubernetes-szolgáltatásokhoz. A Kubernetes bejövő erőforrásai az egyes Kubernetes-szolgáltatások bejövő szabályainak és útvonalának konfigurálására szolgálnak. A bejövő és a bejövő forgalomra vonatkozó szabályok használatával egyetlen IP-cím segítségével átirányíthatja a forgalmat több szolgáltatásba egy Kubernetes-fürtben.

Ez a cikk bemutatja, hogyan helyezheti üzembe az NGINX beléptetési [vezérlőt][nginx-ingress] egy Azure Kubernetes-szolgáltatási (ak-) fürtben. Ezután két alkalmazás fut az AK-fürtben, amelyek mindegyike elérhető egyetlen IP-címen keresztül.

További lehetőségek:

- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Belső, privát hálózatot és IP-címet használó bejövő adatforgalom-vezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó bejövő adatkezelő létrehozása][aks-ingress-own-tls]
- Hozzon létre egy olyan bejövő vezérlőt, amely lehetővé teszi a titkosítást [egy dinamikus nyilvános IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel rendelkező][aks-ingress-static-tls] TLS-tanúsítványok automatikus létrehozásához

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk a [Helm 3][helm] használatával telepíti az NGINX beáramló vezérlőt és egy minta webalkalmazást.

Ehhez a cikkhez az Azure CLI 2.0.64 vagy újabb verzióját is futtatnia kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Bejövő adatkezelő létrehozása

A bejövő vezérlő létrehozásához használja a Helmt az *Nginx-behatolások*telepítéséhez. A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. Ahhoz, hogy teljes mértékben élvezhesse a bejövő vezérlő replikáit, győződjön meg arról, hogy az AK-fürt több csomópontja van.

A bejövő forgalmi vezérlőt egy Linux-csomóponton is ütemezni kell. A Windows Server-csomópontok (az AK-ban jelenleg előzetes verzióban) nem futtathatják a bejövő vezérlőt. A csomópont-választó `--set nodeSelector` paraméterrel történő meghatározása arra utasítja a Kubernetes ütemezőt, hogy az NGINX bejövő vezérlőt Linux-alapú csomóponton futtassa.

> [!TIP]
> A következő példa egy Kubernetes névteret hoz létre a bejövő erőforrások *– alapszintű*forgalomhoz. Szükség szerint adja meg a saját környezetének névterét.

> [!TIP]
> Ha engedélyezni szeretné az [ügyfél forrásának IP-megőrzését][client-source-ip] a fürtben lévő tárolók kéréseire, adja `--set controller.service.externalTrafficPolicy=Local` hozzá a parancsot a Helm install parancshoz. Az ügyfél forrásának IP-címét a kérelem fejlécében tárolja a rendszer az *X által továbbított – esetében*. Ha az ügyfél-forrás IP-megtartást engedélyező bejövő vezérlőt használ, a TLS-áteresztő nem fog működni.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Ha a Kubernetes terheléselosztó szolgáltatás létrejön az NGINX beáramló vezérlőhöz, a rendszer egy dinamikus nyilvános IP-címet rendel hozzá, ahogy az a következő példában látható:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    EXTERNAL_IP   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Még nem jöttek létre Bejövő szabályok, így a belső IP-cím megkereséséhez az NGINX bejövő vezérlő alapértelmezett 404 lapja jelenik meg. A bejövő szabályok a következő lépésekben vannak konfigurálva.

## <a name="run-demo-applications"></a>Bemutató alkalmazások futtatása

A beáramló vezérlő működés közbeni megtekintéséhez futtasson két bemutató alkalmazást az AK-fürtben. Ebben a példában a Helm egy egyszerű *Hello World* -alkalmazás két példányának üzembe helyezésére szolgál.

A minta Helm-diagramok telepítése előtt adja hozzá az Azure Samples-tárházat a Helm-környezethez az alábbiak szerint:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Hozza létre az első bemutató alkalmazást egy Helm-diagramból a következő paranccsal:

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Most telepítse a bemutató alkalmazás egy második példányát. A második példány esetében meg kell adnia egy új címet, hogy a két alkalmazás vizuálisan eltérő legyen. Egyedi szolgáltatásnevet is megadhat:

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Bejövő forgalom útvonalának létrehozása

Mindkét alkalmazás már fut a Kubernetes-fürtön. Az egyes alkalmazásokra irányuló forgalom irányításához hozzon létre egy Kubernetes-bejövő erőforrást. A bejövő erőforrás konfigurálja azokat a szabályokat, amelyek átirányítják a forgalmat a két alkalmazás egyikére.

A következő példában a *EXTERNAL_IP* felé irányuló forgalmat a rendszer átirányítja a `aks-helloworld`nevű szolgáltatásba. *EXTERNAL_IP/Hello-World-Two* irányuló forgalmat a `aks-helloworld-two` szolgáltatás irányítja át. *EXTERNAL_IP/statikus* irányuló forgalmat a rendszer a statikus eszközökhöz `aks-helloworld` tartozó nevű szolgáltatáshoz irányítja.

Hozzon létre egy `hello-world-ingress.yaml` nevű fájlt, és másolja a következő példában YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

Hozza létre a bejövő erőforrásokat a `kubectl apply -f hello-world-ingress.yaml` parancs használatával.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
ingress.extensions/hello-world-ingress-static created
```

## <a name="test-the-ingress-controller"></a>A bejövő vezérlő tesztelése

A bejövő vezérlő útvonalának teszteléséhez tallózzon a két alkalmazás között. Nyisson meg egy webböngészőt az NGINX beáramló vezérlő IP-címéhez, például *EXTERNAL_IP*. Az első bemutató alkalmazás megjelenik a böngészőben, ahogy az a következő példában látható:

![Első alkalmazás, amely a bejövő vezérlő mögött fut](media/ingress-basic/app-one.png)

Most adja hozzá a */Hello-World-Two* elérési útját az IP-címhez, például *EXTERNAL_IP/Hello-World-Two*. Megjelenik a második bemutató alkalmazás az egyéni címmel:

![A bejövő vezérlő mögött futó második alkalmazás](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a cikk a beáramló összetevők és a minta alkalmazások telepítésére szolgál. Amikor központilag telepít egy Helm-diagramot, a rendszer számos Kubernetes-erőforrást hoz létre. Ezek az erőforrások a hüvelyek, a központi telepítések és a szolgáltatások részét képezik. Ezen erőforrások törléséhez törölheti a teljes minta névteret vagy az egyes erőforrásokat.

### <a name="delete-the-sample-namespace-and-all-resources"></a>A minta névtér és az összes erőforrás törlése

A teljes minta névtér törléséhez használja a `kubectl delete` parancsot, és adja meg a névtér nevét. A rendszer törli a névtérben lévő összes erőforrást.

```console
kubectl delete namespace ingress-basic
```

Ezután távolítsa el a Helm-tárházat az AK Hello World alkalmazáshoz:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Erőforrások egyenkénti törlése

Azt is megteheti, hogy egy részletesebb megközelítéssel törli a létrehozott egyéni erőforrásokat. Sorolja fel az `helm list` paranccsal a Helm kiadásait. Keresse meg az *Nginx-beáramló* és az *AK-HelloWorld*nevű diagramot az alábbi példában látható módon:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-06 19:57:00.131576 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-06 19:57:10.971365 -0600 CST    deployed        aks-helloworld-0.1.0               
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Törölje a kiadásokat a `helm delete` paranccsal. A következő példa törli az NGINX beáramló üzembe helyezését, valamint a két mintául szolgáló Hello World alkalmazást.

```
$ helm delete aks-helloworld aks-helloworld-two nginx-ingress --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "nginx-ingress" uninstalled
```

Ezt követően távolítsa el a Helm-tárházat az AK Hello World alkalmazáshoz:

```console
helm repo remove azure-samples
```

Távolítsa el a bejövő forgalom útvonalát, amely irányítja a forgalmat a mintául szolgáló alkalmazásokba:

```console
kubectl delete -f hello-world-ingress.yaml
```

Végezetül törölheti saját maga is a névteret. Használja a `kubectl delete` parancsot, és adja meg a névtér nevét:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>További lépések

Ez a cikk néhány külső összetevőt tartalmaz az ak-nak. Ha többet szeretne megtudni ezekről az összetevőkről, tekintse meg a következő Project-lapokat:

- [Helm parancssori felület][helm-cli]
- [NGINX bejövő adatkezelő][nginx-ingress]

További lehetőségek:

- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Belső, privát hálózatot és IP-címet használó bejövő adatforgalom-vezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó bejövő adatkezelő létrehozása][aks-ingress-own-tls]
- Hozzon létre egy olyan bejövő vezérlőt, amely lehetővé teszi a titkosítást [egy dinamikus nyilvános IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel rendelkező][aks-ingress-static-tls] TLS-tanúsítványok automatikus létrehozásához

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
