---
title: Belső hálózaton belüli bejövő vezérlő
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan telepítheti és konfigurálhatja az NGINX beáramlási vezérlőt egy belső, magánhálózat Azure Kubernetes Service-(ak-) fürtben.
services: container-service
ms.topic: article
ms.date: 04/27/2020
ms.openlocfilehash: 749c9904244dd702e41a63e0266c5ff6b1344261
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561947"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Bejövő vezérlő létrehozása belső virtuális hálózathoz az Azure Kubernetes szolgáltatásban (ak)

A bejövő vezérlő egy olyan szoftver, amely fordított proxyt, konfigurálható forgalmi útválasztást és TLS-megszakítást biztosít a Kubernetes-szolgáltatásokhoz. A Kubernetes bejövő erőforrásai az egyes Kubernetes-szolgáltatások bejövő szabályainak és útvonalának konfigurálására szolgálnak. A bejövő és a bejövő forgalomra vonatkozó szabályok használatával egyetlen IP-cím segítségével átirányíthatja a forgalmat több szolgáltatásba egy Kubernetes-fürtben.

Ez a cikk bemutatja, hogyan helyezheti üzembe az NGINX beléptetési [vezérlőt][nginx-ingress] egy Azure Kubernetes-szolgáltatási (ak-) fürtben. A bejövő vezérlő belső, magánhálózati virtuális hálózaton és IP-címen van konfigurálva. Nincs engedélyezve külső hozzáférés. Ezután két alkalmazás fut az AK-fürtben, amelyek mindegyike elérhető egyetlen IP-címen keresztül.

További lehetőségek:

- [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Saját TLS-tanúsítványokat használó bejövő adatkezelő létrehozása][aks-ingress-own-tls]
- Hozzon létre egy olyan bejövő vezérlőt, amely lehetővé teszi a titkosítást [egy dinamikus nyilvános IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel rendelkező][aks-ingress-static-tls] TLS-tanúsítványok automatikus létrehozásához

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk a [Helm 3][helm] használatával telepíti az NGINX beáramló vezérlőt és a tanúsítvány-kezelőt. A Helm konfigurálásával és használatával kapcsolatos további információkért lásd: [alkalmazások telepítése az Azure Kubernetes szolgáltatásban (ak)][use-helm].

Ehhez a cikkhez az Azure CLI 2.0.64 vagy újabb verzióját is futtatnia kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Bejövő adatkezelő létrehozása

Alapértelmezés szerint az NGINX bejövő vezérlő dinamikus nyilvános IP-cím-hozzárendeléssel jön létre. Az általános konfigurációs követelmény belső, magánhálózati és IP-cím használata. Ez a megközelítés lehetővé teszi a szolgáltatásokhoz való hozzáférés korlátozását belső felhasználók számára, külső hozzáférés nélkül.

Hozzon létre egy *belső Behatolású. YAML* nevű fájlt a következő példa jegyzékfájl használatával. Ez a példa *10.240.0.42* rendel a *loadBalancerIP* -erőforráshoz. Adja meg saját belső IP-címét a bejövő adatkezelővel való használatra. Győződjön meg arról, hogy ez az IP-cím még nincs használatban a virtuális hálózaton belül.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Most telepítse az *Nginx-* beléptetési diagramot a Helm szolgáltatással. Az előző lépésben létrehozott manifest-fájl használatához adja hozzá a `-f internal-ingress.yaml` paramétert. A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. Ahhoz, hogy teljes mértékben élvezhesse a bejövő vezérlő replikáit, győződjön meg arról, hogy az AK-fürt több csomópontja van.

A bejövő forgalmi vezérlőt egy Linux-csomóponton is ütemezni kell. Windows Server-csomópontok nem futtathatják a bejövő forgalmi vezérlőt. A csomópont-választó `--set nodeSelector` paraméterrel történő meghatározása arra utasítja a Kubernetes ütemezőt, hogy az NGINX bejövő vezérlőt Linux-alapú csomóponton futtassa.

> [!TIP]
> A következő példa egy Kubernetes névteret hoz létre a bejövő erőforrások *– alapszintű*forgalomhoz. Szükség szerint adja meg a saját környezetének névterét. Ha az AK-fürt nincs engedélyezve RBAC, adja `--set rbac.create=false` hozzá a parancsot a Helm parancshoz.

> [!TIP]
> Ha engedélyezni szeretné az [ügyfél forrásának IP-megőrzését][client-source-ip] a fürtben lévő tárolók kéréseire, adja `--set controller.service.externalTrafficPolicy=Local` hozzá a parancsot a Helm install parancshoz. Az ügyfél forrásának IP-címét a kérelem fejlécében tárolja a rendszer az *X által továbbított – esetében*. Ha az ügyfél-forrás IP-megtartást engedélyező bejövő vezérlőt használ, a TLS-áteresztő nem fog működni.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Ha a Kubernetes terheléselosztó szolgáltatás létrejön az NGINX beáramló vezérlőhöz, a belső IP-cím hozzá van rendelve, ahogy az a következő példában látható:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    10.240.0.42   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Még nem jöttek létre Bejövő szabályok, így a belső IP-cím megkereséséhez az NGINX bejövő vezérlő alapértelmezett 404 lapja jelenik meg. A bejövő szabályok a következő lépésekben vannak konfigurálva.

## <a name="run-demo-applications"></a>Bemutató alkalmazások futtatása

A beáramló vezérlő működés közbeni megtekintéséhez futtasson két bemutató alkalmazást az AK-fürtben. Ebben a példában egy egyszerű `kubectl apply` *Helló World* -alkalmazás két példányának üzembe helyezésére használható.

Hozzon létre egy *AK-HelloWorld. YAML* fájlt, és másolja a következő PÉLDÁBAN szereplő YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

Hozzon létre egy *beáramló-bemutató. YAML* fájlt, és másolja a következő PÉLDÁBAN szereplő YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Futtassa a két bemutató alkalmazást a `kubectl apply`használatával:

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Bejövő forgalom útvonalának létrehozása

Mindkét alkalmazás már fut a Kubernetes-fürtön. Az egyes alkalmazásokra irányuló forgalom irányításához hozzon létre egy Kubernetes-bejövő erőforrást. A bejövő erőforrás konfigurálja azokat a szabályokat, amelyek átirányítják a forgalmat a két alkalmazás egyikére.

A következő példában a címnek `http://10.240.0.42/` való adatforgalom a nevű `aks-helloworld`szolgáltatáshoz lesz irányítva. A `http://10.240.0.42/hello-world-two` `ingress-demo` rendszer átirányítja a címnek a szolgáltatás felé irányuló forgalmat.

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
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Hozza létre a bejövő erőforrásokat a `kubectl apply -f hello-world-ingress.yaml` parancs használatával.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>A bejövő vezérlő tesztelése

A beáramló vezérlő útvonalának teszteléséhez keresse meg a két alkalmazást webes ügyféllel. Ha szükséges, gyorsan tesztelheti ezt a csak belső funkciókat az AK-fürtön található Pod-ból. Hozzon létre egy teszt Pod-t, és csatoljon hozzá egy terminál-munkamenetet:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Telepítés `curl` a pod használatával `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Most nyissa meg a Kubernetes-bejövő adatkezelő- `curl`vezérlő címeit *http://10.240.0.42*a használatával, például:. Adja meg saját belső IP-címét, ha a beléptetési vezérlőt a cikk első lépésében telepítette.

```console
curl -L http://10.240.0.42
```

Nincs további elérési út megadva a címnek, így a bejövő vezérlő alapértelmezett értéke az */* útvonal. A rendszer az első bemutató alkalmazást adja vissza, ahogy az a következő tömörített példában is látható:

```
$ curl -L http://10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Most adja hozzá a */Hello-World-Two* elérési útját a *http://10.240.0.42/hello-world-two*címnek, például:. A rendszer az egyéni címmel rendelkező második bemutató alkalmazást adja vissza, ahogy az az alábbi tömörített példában is látható:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a cikk a beáramló összetevők telepítésére szolgál. Amikor központilag telepít egy Helm-diagramot, a rendszer számos Kubernetes-erőforrást hoz létre. Ezek az erőforrások a hüvelyek, a központi telepítések és a szolgáltatások részét képezik. Ezen erőforrások törléséhez törölheti a teljes minta névteret vagy az egyes erőforrásokat.

### <a name="delete-the-sample-namespace-and-all-resources"></a>A minta névtér és az összes erőforrás törlése

A teljes minta névtér törléséhez használja a `kubectl delete` parancsot, és adja meg a névtér nevét. A rendszer törli a névtérben lévő összes erőforrást.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Erőforrások egyenkénti törlése

Azt is megteheti, hogy egy részletesebb megközelítéssel törli a létrehozott egyéni erőforrásokat. Sorolja fel az `helm list` paranccsal a Helm kiadásait. Keresse meg az *Nginx-beáramló* és az *AK-HelloWorld*nevű diagramot az alábbi példában látható módon:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Távolítsa el a kiadásokat a `helm uninstall` paranccsal. Az alábbi példa eltávolítja az NGINX bejövő forgalom üzembe helyezését.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Ezután távolítsa el a két minta alkalmazást:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
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

- [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Dinamikus nyilvános IP-címmel rendelkező bejövő vezérlő létrehozása, amely lehetővé teszi a titkosítást a TLS-tanúsítványok automatikus létrehozásához][aks-ingress-tls]
- [Statikus nyilvános IP-címmel rendelkező bejövő vezérlő létrehozása, amely lehetővé teszi a titkosítást a TLS-tanúsítványok automatikus létrehozásához][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers