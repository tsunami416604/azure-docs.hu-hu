---
title: Bejövő vezérlő létrehozása belső hálózathoz az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan telepítheti és konfigurálhatja az NGINX beáramlási vezérlőt egy belső, magánhálózat Azure Kubernetes Service-(ak-) fürtben.
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 8c3eeaf2f9a92f1be9c691091d8e33d09a60b22d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595654"
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

Ez a cikk a Helm használatával telepíti az NGINX beáramló vezérlőt, a tanúsítvány-kezelőt és egy minta webalkalmazást. Meg kell adnia a Helm-t az AK-fürtön belül, és egy szolgáltatásfiókot kell használnia a Kormányrúdhoz. A Helm konfigurálásával és használatával kapcsolatos további információkért lásd: [alkalmazások telepítése az Azure Kubernetes szolgáltatásban (ak)][use-helm].

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

Most telepítse az *Nginx-* beléptetési diagramot a Helm szolgáltatással. Az előző lépésben létrehozott manifest-fájl használatához adja hozzá a `-f internal-ingress.yaml` paramétert. A hozzáadott redundancia érdekében az NGINX bejövő vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. Ahhoz, hogy teljes mértékben élvezhesse a bejövő vezérlő replikáit, győződjön meg arról, hogy az AK-fürt több csomópontja van.

A bejövő vezérlőt egy Linux-csomóponton is ütemezni kell. A Windows Server-csomópontok (az AK-ban jelenleg előzetes verzióban) nem futtathatják a bejövő vezérlőt. A csomópont-választó a `--set nodeSelector` paraméterrel adható meg, hogy a Kubernetes ütemező az NGINX bejövő adatkezelőt Linux-alapú csomóponton futtassa.

> [!TIP]
> A következő példa egy Kubernetes névteret hoz létre a bejövő erőforrások *– alapszintű*forgalomhoz. Szükség szerint adja meg a saját környezetének névterét. Ha az AK-fürt nincs engedélyezve RBAC, adja hozzá a `--set rbac.create=false` a Helm parancshoz.

> [!TIP]
> Ha engedélyezni szeretné az ügyfél- [forrás IP-megőrzését][client-source-ip] a fürtön lévő tárolók kéréseire, adja hozzá `--set controller.service.externalTrafficPolicy=Local` a Helm install parancshoz. Az ügyfél forrásának IP-címét a kérelem fejlécében tárolja a rendszer az *X által továbbított – esetében*. Ha olyan bejövő adatkezelőt használ, amelyen engedélyezve van az ügyfél forrásának IP-címe, az SSL-továbbítás nem fog működni.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Ha a Kubernetes terheléselosztó szolgáltatás létrejön az NGINX beáramló vezérlőhöz, a belső IP-cím hozzá van rendelve, ahogy az a következő példában látható:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Még nem jöttek létre Bejövő szabályok, így a belső IP-cím megkereséséhez az NGINX bejövő vezérlő alapértelmezett 404 lapja jelenik meg. A bejövő szabályok a következő lépésekben vannak konfigurálva.

## <a name="run-demo-applications"></a>Bemutató alkalmazások futtatása

A beáramló vezérlő működés közbeni megtekintéséhez futtasson két bemutató alkalmazást az AK-fürtben. Ebben a példában a Helm egy egyszerű "Hello World" alkalmazás két példányának üzembe helyezésére szolgál.

A minta Helm-diagramok telepítése előtt adja hozzá az Azure Samples-tárházat a Helm-környezethez az alábbiak szerint:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Hozza létre az első bemutató alkalmazást egy Helm-diagramból a következő paranccsal:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Most telepítse a bemutató alkalmazás egy második példányát. A második példány esetében meg kell adnia egy új címet, hogy a két alkalmazás vizuálisan eltérő legyen. Egyedi szolgáltatásnevet is megadhat:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Bejövő forgalom útvonalának létrehozása

Mindkét alkalmazás már fut a Kubernetes-fürtön. Az egyes alkalmazásokra irányuló forgalom irányításához hozzon létre egy Kubernetes-bejövő erőforrást. A bejövő erőforrás konfigurálja azokat a szabályokat, amelyek átirányítják a forgalmat a két alkalmazás egyikére.

A következő példában a `http://10.240.0.42/`re irányuló forgalmat a rendszer átirányítja a `aks-helloworld`nevű szolgáltatáshoz. A `http://10.240.0.42/hello-world-two` felé irányuló forgalmat a rendszer átirányítja a `ingress-demo` szolgáltatáshoz.

Hozzon létre egy `hello-world-ingress.yaml` nevű fájlt, és másolja a következő példában szereplő YAML.

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

Telepítse a `curl`t a pod használatával `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Most nyissa meg a Kubernetes bejövő adatkezelője címe `curl`használatával, például *http://10.240.0.42val* . Adja meg saját belső IP-címét, ha a beléptetési vezérlőt a cikk első lépésében telepítette.

```console
curl -L http://10.240.0.42
```

Nincs további elérési út megadva a címnek, így a bejövő vezérlő alapértelmezett értéke a */* útvonal. A rendszer az első bemutató alkalmazást adja vissza, ahogy az a következő tömörített példában is látható:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Most adja hozzá a */Hello-World-Two* elérési útját a címnek, például *http://10.240.0.42/hello-world-two* . A rendszer az egyéni címmel rendelkező második bemutató alkalmazást adja vissza, ahogy az az alábbi tömörített példában is látható:

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

Azt is megteheti, hogy egy részletesebb megközelítéssel törli a létrehozott egyéni erőforrásokat. Sorolja fel a Helm kiadásait az `helm list` paranccsal. Keresse meg az *Nginx-beáramló* és az *AK-HelloWorld*nevű diagramot az alábbi példában látható módon:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Törölje a kiadásokat a `helm delete` paranccsal. A következő példa törli az NGINX beáramló üzembe helyezését, valamint a két mintául szolgáló Hello World alkalmazást.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
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

## <a name="next-steps"></a>Következő lépések

Ez a cikk néhány külső összetevőt tartalmaz az ak-nak. Ha többet szeretne megtudni ezekről az összetevőkről, tekintse meg a következő Project-lapokat:

- [Helm parancssori felület][helm-cli]
- [NGINX bejövő adatkezelő][nginx-ingress]

További lehetőségek:

- [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Dinamikus nyilvános IP-címmel rendelkező bejövő vezérlő létrehozása, amely lehetővé teszi a titkosítást a TLS-tanúsítványok automatikus létrehozásához][aks-ingress-tls]
- [Statikus nyilvános IP-címmel rendelkező bejövő vezérlő létrehozása, amely lehetővé teszi a titkosítást a TLS-tanúsítványok automatikus létrehozásához][aks-ingress-static-tls]

<!-- LINKS - external -->
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