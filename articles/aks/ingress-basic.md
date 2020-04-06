---
title: Be- és be- és átakat meghatározó vezérlő létrehozása
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan telepíthet és konfigurálhat egy alapvető NGINX bejövő forgalom vezérlőt egy Azure Kubernetes-fürtben (AKS) fürtben.
services: container-service
ms.topic: article
ms.date: 12/20/2019
ms.openlocfilehash: 8b9e4537d6dd771697accc1f9fbdc3b6e2584863
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668537"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Bejövő forgalom vezérlő létrehozása az Azure Kubernetes szolgáltatásban (AKS)

A be- ésvisszaszolgáltatás vezérlője egy szoftver, amely fordított proxyt, konfigurálható forgalom-útválasztást és TLS-végződtetést biztosít a Kubernetes-szolgáltatásokszámára. Kubernetes inress erőforrások konfigurálására használják a be- és telepítési szabályok és útvonalak az egyes Kubernetes-szolgáltatások. A bejövő forgalom vezérlő és a bejövő forgalom szabályok használatával egyetlen IP-cím használható a forgalom irányításához több szolgáltatás egy Kubernetes-fürtben.

Ez a cikk bemutatja, hogyan telepítheti az [NGINX bejövő forgalom vezérlőegy][nginx-ingress] Azure Kubernetes-fürt (AKS) fürtben. Ezután két alkalmazás fut az AKS-fürtben, amelyek mindegyike elérhető az egyetlen IP-címen keresztül.

További lehetőségek:

- [A HTTP-alkalmazásútválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Belső, magánhálózati és IP-címet használó be- és áthálózaton belüli adatélmény-vezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó be- és hanghálózati vezérlő létrehozása][aks-ingress-own-tls]
- Bejövő forgalom vezérlő létrehozása, amely a Let's Encrypt segítségével automatikusan generáltTLS-tanúsítványokat [dinamikus nyilvános IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk a Helm segítségével telepíti az NGINX bejövő adatkezelő t és egy minta webalkalmazást.

Ez a cikk azt is megköveteli, hogy az Azure CLI 2.0.64-es vagy újabb verzióját futtassa. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Be- és be- és átakat meghatározó vezérlő létrehozása

A bejövő kapcsolat vezérlőjének létrehozásához a Helm segítségével telepítse a *nginx-inthess -t.* A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. A bejövő adatkezelő replikáinak teljes körű kihasználása érdekében győződjön meg arról, hogy az AKS-fürtben egynél több csomópont található.

A bejövő forgalmi vezérlőt egy Linux-csomóponton is ütemezni kell. A Windows Server-csomópontok (jelenleg előzetes verzióban az AKS) nem futtatható a bejövő adatbeviteli vezérlő. A csomópont-választó `--set nodeSelector` paraméterrel történő meghatározása arra utasítja a Kubernetes ütemezőt, hogy az NGINX bejövő vezérlőt Linux-alapú csomóponton futtassa.

> [!TIP]
> A következő példa létrehoz egy Kubernetes névteret a *be- és alapvető*nevű be- és erőforráshoz. Szükség szerint adjon meg egy névteret a saját környezetéhez.

> [!TIP]
> Ha engedélyezni szeretné [az ügyfélforrás IP-címének megőrzését][client-source-ip] `--set controller.service.externalTrafficPolicy=Local` a fürtben lévő tárolókra vonatkozó kérelmekhez, adja hozzá a Helm telepítési parancshoz. Az ügyfélforrás *IP-címe az X-Forwarded-For*csoportban található kérelemfejlécben található. Ha egy olyan be- ésfeleltetót használ, amelyen az ügyfélforrás IP-megőrzése engedélyezve van, az SSL-áthaladás nem fog működni.

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

Amikor a Kubernetes terheléselosztó szolgáltatás jön létre az NGINX bejövő forgalom vezérlőhöz, dinamikus nyilvános IP-cím van hozzárendelve, amint az a következő példa kimenetben látható:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    EXTERNAL_IP   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Még nem jött létre bejövő házirend, így az NGINX bejövő adatószabályzó alapértelmezett 404-es lapja jelenik meg, ha a belső IP-címet keresi. A bejövő támadások szabályai a következő lépésekben vannak konfigurálva.

## <a name="run-demo-applications"></a>Bemutatóalkalmazások futtatása

A bejövő tevékenység vezérlő működés közben, futtasson két bemutató alkalmazások at a AKS-fürtben. Ebben a példában a Helm egy egyszerű *Hello world* alkalmazás két példányának üzembe helyezésére szolgál.

A minta Helm-diagramok telepítése előtt adja hozzá az Azure-minták tárházát a Helm-környezethez az alábbiak szerint:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Hozza létre az első bemutató alkalmazást egy Helm diagramból a következő paranccsal:

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Most telepítse a demo alkalmazás második példányát. A második példányhoz új címet kell megadnia, hogy a két alkalmazás vizuálisan elkülönüljön. Egyedi szolgáltatásnevet is megadhat:

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Be- és befelé vezető útvonal létrehozása

Mindkét alkalmazás most fut a Kubernetes-fürtön. Az egyes alkalmazásokhoz való forgalom irányításához hozzon létre egy Kubernetes-be- és erőforrást. A be- és elő-visszaforrása konfigurálja azokat a szabályokat, amelyek a forgalmat a két alkalmazás egyikéhez irányítják.

A következő példában a *EXTERNAL_IP* irányuló forgalom `aks-helloworld`a nevű szolgáltatáshoz kerül. A *EXTERNAL_IP/hello-world-2-be* irányuló `aks-helloworld-two` forgalom a szolgáltatáshoz van irányítva. A *EXTERNAL_IP/static* for forgalmat a `aks-helloworld` statikus eszközöknek nevezett szolgáltatásra irányítja a rendszer.

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

Hozza létre a be- `kubectl apply -f hello-world-ingress.yaml` éséidézőerőforrást a paranccsal.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
ingress.extensions/hello-world-ingress-static created
```

## <a name="test-the-ingress-controller"></a>A be- és vissza- és visszarepülés vezérlőjének tesztelése

A be- ésakozóvezérlő útvonalainak teszteléséhez keresse meg a két alkalmazást. Nyisson meg egy webböngészőt az NGINX bejövő adatvezérlő IP-címére, például *a EXTERNAL_IP.* Az első bemutató alkalmazás megjelenik a webböngészőben, amint az a következő példában látható:

![A be- ésacsoport-vezérlő mögött futó első alkalmazás](media/ingress-basic/app-one.png)

Most adja hozzá a */hello-world-2* elérési utat az IP-címhez, például *EXTERNAL_IP/hello-world-two*. A második demo alkalmazás az egyéni cím jelenik meg:

![A be- és a működésvezérlő mögött futó második alkalmazás](media/ingress-basic/app-two.png)

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
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-06 19:57:00.131576 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-06 19:57:10.971365 -0600 CST    deployed        aks-helloworld-0.1.0               
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Törölje a kiadásokat `helm delete` a paranccsal. A következő példa törli az NGINX bejövő környezet, és a két minta AKS hello world alkalmazások.

```
$ helm delete aks-helloworld aks-helloworld-two nginx-ingress --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "nginx-ingress" uninstalled
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

- [A HTTP-alkalmazásútválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Belső, magánhálózati és IP-címet használó be- és áthálózaton belüli adatélmény-vezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó be- és hanghálózati vezérlő létrehozása][aks-ingress-own-tls]
- Bejövő forgalom vezérlő létrehozása, amely a Let's Encrypt segítségével automatikusan generáltTLS-tanúsítványokat [dinamikus nyilvános IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel][aks-ingress-static-tls]

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
