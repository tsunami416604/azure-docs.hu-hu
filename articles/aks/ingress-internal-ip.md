---
title: Bejövő forgalom irányító létrehozása belső hálózathoz az Azure Kubernetes szolgáltatásban (AKS)
description: Ismerje meg, hogyan telepíthet és konfigurálhat egy NGINX bejövő forgalom vezérlőt egy Azure Kubernetes-fürt (AKS) fürtbelső, magánhálózatához.
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 8c3eeaf2f9a92f1be9c691091d8e33d09a60b22d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595654"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Bejövő forgalom vezérlő létrehozása egy belső virtuális hálózathoz az Azure Kubernetes szolgáltatásban (AKS)

A be- ésvisszaszolgáltatás vezérlője egy szoftver, amely fordított proxyt, konfigurálható forgalom-útválasztást és TLS-végződtetést biztosít a Kubernetes-szolgáltatásokszámára. Kubernetes inress erőforrások konfigurálására használják a be- és telepítési szabályok és útvonalak az egyes Kubernetes-szolgáltatások. A bejövő forgalom vezérlő és a bejövő forgalom szabályok használatával egyetlen IP-cím használható a forgalom irányításához több szolgáltatás egy Kubernetes-fürtben.

Ez a cikk bemutatja, hogyan telepítheti az [NGINX bejövő forgalom vezérlőegy][nginx-ingress] Azure Kubernetes-fürt (AKS) fürtben. A belső virtuális hálózatés IP-cím a belső, privát virtuális hálózaton és IP-címen van konfigurálva. Külső hozzáférés nem engedélyezett. Ezután két alkalmazás fut az AKS-fürtben, amelyek mindegyike elérhető az egyetlen IP-címen keresztül.

További lehetőségek:

- [Egyszerű be- és átszivárgási vezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazásútválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Saját TLS-tanúsítványokat használó be- és hanghálózati vezérlő létrehozása][aks-ingress-own-tls]
- Bejövő forgalom vezérlő létrehozása, amely a Let's Encrypt segítségével automatikusan generáltTLS-tanúsítványokat [dinamikus nyilvános IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk a Helm segítségével telepíti az NGINX bejövő adatkezelőt, a cert-kezelőt és egy mintawebalkalmazást. Helm inicializálva kell rendelkeznie az AKS-fürtön belül, és egy service-fiókot kell használnia a Tiller számára. A Helm konfigurálásáról és használatáról további információt az [Alkalmazások telepítése helmtel az Azure Kubernetes szolgáltatásban (AKS) című témakörben][use-helm]talál.

Ez a cikk azt is megköveteli, hogy az Azure CLI 2.0.64-es vagy újabb verzióját futtassa. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Be- és be- és átakat meghatározó vezérlő létrehozása

Alapértelmezés szerint egy NGINX bejövő kapcsolat vezérlő jön létre egy dinamikus nyilvános IP-cím hozzárendelés. A belső, magánhálózat és IP-cím használata általános konfigurációs követelmény. Ez a megközelítés lehetővé teszi, hogy korlátozza a szolgáltatásokhoz való hozzáférést a belső felhasználók, külső hozzáférés nélkül.

Hozzon létre egy *belső-ingress.yaml* nevű fájlt a következő példajegyzékfájl használatával. Ez a példa *10.240.0.42-t* rendel a *loadBalancerIP* erőforráshoz. Adja meg saját belső IP-címét a be- és énekre vezérlővel való használatra. Győződjön meg arról, hogy ez az IP-cím még nincs használatban a virtuális hálózaton belül.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Most telepítsd a *nginx-ingress táblázatot* Helm-el. Az előző lépésben létrehozott jegyzékfájl használatához `-f internal-ingress.yaml` adja hozzá a paramétert. A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. A bejövő adatkezelő replikáinak teljes körű kihasználása érdekében győződjön meg arról, hogy az AKS-fürtben egynél több csomópont található.

A bejövő forgalmi vezérlőt egy Linux-csomóponton is ütemezni kell. A Windows Server-csomópontok (jelenleg előzetes verzióban az AKS) nem futtatható a bejövő adatbeviteli vezérlő. A csomópont-választó `--set nodeSelector` paraméterrel történő meghatározása arra utasítja a Kubernetes ütemezőt, hogy az NGINX bejövő vezérlőt Linux-alapú csomóponton futtassa.

> [!TIP]
> A következő példa létrehoz egy Kubernetes névteret a *be- és alapvető*nevű be- és erőforráshoz. Szükség szerint adjon meg egy névteret a saját környezetéhez. Ha az AKS-fürt nincs engedélyezve az RBAC-on, adja hozzá `--set rbac.create=false` a Helm parancsokhoz.

> [!TIP]
> Ha engedélyezni szeretné [az ügyfélforrás IP-címének megőrzését][client-source-ip] `--set controller.service.externalTrafficPolicy=Local` a fürtben lévő tárolókra vonatkozó kérelmekhez, adja hozzá a Helm telepítési parancshoz. Az ügyfélforrás *IP-címe az X-Forwarded-For*csoportban található kérelemfejlécben található. Ha egy olyan be- ésfeleltetót használ, amelyen az ügyfélforrás IP-megőrzése engedélyezve van, az SSL-áthaladás nem fog működni.

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

Amikor a Kubernetes terheléselosztó szolgáltatás jön létre az NGINX bejövő forgalom vezérlő, a belső IP-cím van rendelve, ahogy az a következő példa kimenet:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Még nem jött létre bejövő házirend, így az NGINX bejövő adatószabályzó alapértelmezett 404-es lapja jelenik meg, ha a belső IP-címet keresi. A bejövő támadások szabályai a következő lépésekben vannak konfigurálva.

## <a name="run-demo-applications"></a>Bemutatóalkalmazások futtatása

A bejövő tevékenység vezérlő működés közben, futtasson két bemutató alkalmazások at a AKS-fürtben. Ebben a példában a Helm egy egyszerű "Hello world" alkalmazás két példányának üzembe helyezésére szolgál.

A minta Helm-diagramok telepítése előtt adja hozzá az Azure-minták tárházát a Helm-környezethez az alábbiak szerint:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Hozza létre az első bemutató alkalmazást egy Helm diagramból a következő paranccsal:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Most telepítse a demo alkalmazás második példányát. A második példányhoz új címet kell megadnia, hogy a két alkalmazás vizuálisan elkülönüljön. Egyedi szolgáltatásnevet is megadhat:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Be- és befelé vezető útvonal létrehozása

Mindkét alkalmazás most fut a Kubernetes-fürtön. Az egyes alkalmazásokhoz való forgalom irányításához hozzon létre egy Kubernetes-be- és erőforrást. A be- és elő-visszaforrása konfigurálja azokat a szabályokat, amelyek a forgalmat a két alkalmazás egyikéhez irányítják.

A következő példában a `http://10.240.0.42/` címhez vezető forgalom `aks-helloworld`a nevű szolgáltatáshoz kerül. A címre `http://10.240.0.42/hello-world-two` irányuló forgalom a `ingress-demo` szolgáltatáshoz kerül.

Hozzon létre `hello-world-ingress.yaml` egy elnevezett fájlt, és másolja a következő példa YAML.

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

Hozza létre a be- `kubectl apply -f hello-world-ingress.yaml` éséidézőerőforrást a paranccsal.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>A be- és vissza- és visszarepülés vezérlőjének tesztelése

A be- ésakozóvezérlő útvonalainak teszteléséhez keresse meg a két alkalmazást egy webes ügyféllel. Szükség esetén gyorsan tesztelheti ezt a csak belső funkciót az AKS-fürt egyik podjából. Hozzon létre egy tesztpodot, és csatoljon hozzá egy terminálmunkamenetet:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Telepítse `curl` a pod `apt-get`segítségével:

```console
apt-get update && apt-get install -y curl
```

Most pedig a használatával `curl`érheti el a Kubernetes *http://10.240.0.42*belépővezérlő címét, például . Adja meg a saját belső IP-címet megadott, amikor üzembe helyezte a bejövő kapcsolat vezérlőa a cikk első lépésében.

```console
curl -L http://10.240.0.42
```

Nincs további elérési út a címhez, így a be- */* ésakozóvezérlő alapértelmezés szerint az útvonal. Az első bemutató alkalmazás visszaad, amint az a következő tömörített példa kimenet:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Most add hozzá *a /hello-world-2* *http://10.240.0.42/hello-world-two*elérési utat a címhez, például . A második demó alkalmazás az egyéni címet adja vissza, amint az a következő tömörített példa kimenet:

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

Ez a cikk helm a be- és a be- és mintaalkalmazások telepítéséhez használt. Helm-diagram telepítésekor számos Kubernetes-erőforrás jön létre. Ezek az erőforrások podokat, üzemelő példányokat és szolgáltatásokat tartalmaznak. Az erőforrások törléséhez törölheti a teljes mintanévteret vagy az egyes erőforrásokat.

### <a name="delete-the-sample-namespace-and-all-resources"></a>A mintanévtér és az összes erőforrás törlése

A teljes mintanévtér törléséhez használja a `kubectl delete` parancsot, és adja meg a névtér nevét. A névtérben lévő összes erőforrás törlődik.

```console
kubectl delete namespace ingress-basic
```

Ezután távolítsa el az AKS hello world alkalmazás Helm repo-ját:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Erőforrások törlése egyenként

Másik lehetőségként egy részletesebb megközelítés az egyes létrehozott erőforrások törlése. Sorolja fel a `helm list` Helm-kiadásokat a paranccsal. Keresse meg a *nginx-inress* és *aks-helloworld*nevű diagramokat, ahogy az a következő példa kimenetben látható:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Törölje a kiadásokat `helm delete` a paranccsal. A következő példa törli az NGINX bejövő környezet, és a két minta AKS hello world alkalmazások.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
```

Ezután távolítsa el az AKS hello world alkalmazás Helm repo-ját:

```console
helm repo remove azure-samples
```

Távolítsa el a mintaalkalmazásokba irányuló forgalmat irányító be- és áttérési útvonalat:

```console
kubectl delete -f hello-world-ingress.yaml
```

Végül törölheti magát a névteret. Használja `kubectl delete` a parancsot, és adja meg a névtér nevét:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>További lépések

Ez a cikk az AKS néhány külső összetevőjét is tartalmazta. Ezekről az összetevőkről a következő projektlapokon olvashat bővebben:

- [Kormányrúd CLI][helm-cli]
- [NGINX bejövő adatvezérlő][nginx-ingress]

További lehetőségek:

- [Egyszerű be- és átszivárgási vezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazásútválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Dinamikus nyilvános IP-című bejövő forgalom vezérlő létrehozása és a TLS-tanúsítványok automatikus létrehozásához szükséges Titkosítás konfigurálása][aks-ingress-tls]
- [Bejövő forgalom vezérlő létrehozása statikus nyilvános IP-címmel, és a TLS-tanúsítványok automatikus létrehozásához konfigurálása A titkosítás konfigurálása][aks-ingress-static-tls]

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