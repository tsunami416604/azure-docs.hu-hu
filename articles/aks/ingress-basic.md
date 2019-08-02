---
title: Alapszintű bejövő adatkezelő létrehozása az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan telepíthet és konfigurálhat egy alapszintű NGINX beáramlási vezérlőt egy Azure Kubernetes-szolgáltatási (ak-) fürtben.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 1e5c3aa7ed4ec990dba07fb24830fae243141ad5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "67615581"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Bejövő adatvezérlő létrehozása az Azure Kubernetes szolgáltatásban (ak)

A bejövő vezérlő egy olyan szoftver, amely fordított proxyt, konfigurálható forgalmi útválasztást és TLS-megszakítást biztosít a Kubernetes-szolgáltatásokhoz. A Kubernetes bejövő erőforrásai az egyes Kubernetes-szolgáltatások bejövő szabályainak és útvonalának konfigurálására szolgálnak. A bejövő és a bejövő forgalomra vonatkozó szabályok használatával egyetlen IP-cím segítségével átirányíthatja a forgalmat több szolgáltatásba egy Kubernetes-fürtben.

Ez a cikk bemutatja, hogyan helyezheti üzembe az [NGINX][nginx-ingress] beléptetési vezérlőt egy Azure Kubernetes-szolgáltatási (ak-) fürtben. Ezután két alkalmazás fut az AK-fürtben, amelyek mindegyike elérhető egyetlen IP-címen keresztül.

További lehetőségek:

- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Belső, privát hálózatot és IP-címet használó bejövő adatforgalom-vezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó bejövő adatkezelő létrehozása][aks-ingress-own-tls]
- Hozzon létre egy olyan bejövő vezérlőt, amely lehetővé teszi a titkosítást [egy dinamikus nyilvános IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel rendelkező][aks-ingress-static-tls] TLS-tanúsítványok automatikus létrehozásához

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk a Helm használatával telepíti az NGINX beáramló vezérlőt, a tanúsítvány-kezelőt és egy minta webalkalmazást. Meg kell adnia a Helm-t az AK-fürtön belül, és egy szolgáltatásfiókot kell használnia a Kormányrúdhoz. A Helm konfigurálásával és használatával kapcsolatos további információkért lásd: [alkalmazások telepítése az Azure Kubernetes szolgáltatásban (ak)][use-helm].

Ehhez a cikkhez az Azure CLI 2.0.64 vagy újabb verzióját is futtatnia kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Bejövő adatkezelő létrehozása

A bejövő adatkezelő létrehozásához használja `Helm` az *Nginx-behatolások*telepítését. A hozzáadott redundancia érdekében az NGINX bejövő adatkezelők két replikája van telepítve a `--set controller.replicaCount` paraméterrel. Ahhoz, hogy teljes mértékben élvezhesse a bejövő vezérlő replikáit, győződjön meg arról, hogy az AK-fürt több csomópontja van.

A bejövő vezérlőt egy Linux-csomóponton is ütemezni kell. A Windows Server-csomópontok (az AK-ban jelenleg előzetes verzióban) nem futtathatják a bejövő vezérlőt. A (z) `--set nodeSelector` paraméter használatával adja meg a csomópont-választót a Kubernetes Scheduler számára, hogy a Linux-alapú csomóponton futtassa az NGINX beáramló vezérlőt.

> [!TIP]
> A következő példa egy Kubernetes névteret hoz létre a bejövő erőforrások *–* alapszintű forgalomhoz. Szükség szerint adja meg a saját környezetének névterét. Ha az AK-fürt nincs engedélyezve RBAC, adja `--set rbac.create=false` hozzá a parancsot a Helm parancshoz.

> [!TIP]
> Ha engedélyezni szeretné az [ügyfél forrásának IP-megőrzését][client-source-ip] a fürtben lévő tárolók kéréseire, adja `--set controller.service.externalTrafficPolicy=Local` hozzá a parancsot a Helm install parancshoz. Az ügyfél forrásának IP-címét a kérelem fejlécében tárolja a rendszer az *X által továbbított – esetében*. Ha olyan bejövő adatkezelőt használ, amelyen engedélyezve van az ügyfél forrásának IP-címe, az SSL-továbbítás nem fog működni.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Ha a Kubernetes terheléselosztó szolgáltatás létrejön az NGINX beáramló vezérlőhöz, a rendszer egy dinamikus nyilvános IP-címet rendel hozzá, ahogy az a következő példában látható:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                                 TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
aspiring-labradoodle-nginx-ingress-controller        LoadBalancer   10.0.61.144    40.117.74.8   80:30386/TCP,443:32276/TCP   6m2s
aspiring-labradoodle-nginx-ingress-default-backend   ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
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

A következő példában a címnek `http://40.117.74.8/` való adatforgalom a nevű `aks-helloworld`szolgáltatáshoz lesz irányítva. A `http://40.117.74.8/hello-world-two` `ingress-demo` rendszer átirányítja a címnek a szolgáltatás felé irányuló forgalmat.

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

A bejövő vezérlő útvonalának teszteléséhez tallózzon a két alkalmazás között. Nyisson meg egy webböngészőt az NGINX beáramló vezérlő IP-címéhez, *http://40.117.74.8* például:. Az első bemutató alkalmazás megjelenik a böngészőben, ahogy az a következő példában látható:

![Első alkalmazás, amely a bejövő vezérlő mögött fut](media/ingress-basic/app-one.png)

Most adja hozzá a */Hello-World-Two* elérési útját az IP- *http://40.117.74.8/hello-world-two* címhez, például:. Megjelenik a második bemutató alkalmazás az egyéni címmel:

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

Azt is megteheti, hogy egy részletesebb megközelítéssel törli a létrehozott egyéni erőforrásokat. Sorolja fel `helm list` az paranccsal a Helm kiadásait. Keresse meg az *Nginx-* beáramló és az *AK-HelloWorld*nevű diagramot az alábbi példában látható módon:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
aspiring-labradoodle    1           Wed Mar 27 19:55:37 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      ingress-basic
esteemed-koala          1           Wed Mar 27 19:59:18 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
wonderful-puma          1           Wed Mar 27 19:59:07 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
```

Törölje a kiadásokat a `helm delete` paranccsal. A következő példa törli az NGINX beáramló üzembe helyezését, valamint a két mintául szolgáló Hello World alkalmazást.

```
$ helm delete aspiring-labradoodle esteemed-koala wonderful-puma

release "aspiring-labradoodle" deleted
release "esteemed-koala" deleted
release "wonderful-puma" deleted
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
