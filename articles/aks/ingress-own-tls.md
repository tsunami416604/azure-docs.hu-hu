---
title: Saját TLS-tanúsítványok használata az Azure Kubernetes-szolgáltatás (AKS) fürtjével való be- és visszaszállításhoz
description: Megtudhatja, hogyan telepíthet és konfigurálhat egy NGINX bejövő forgalom vezérlőt, amely saját tanúsítványokat használ egy Azure Kubernetes-fürtben (AKS) fürtben.
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: e567f5384cdd1e40ea67284713a29a92ee87af7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595501"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>HTTPS bejövőforgalom-vezérlő létrehozása, és saját TLS-tanúsítványok használata az Azure Kubernetes Service-ben (AKS)

A be- ésvisszaszolgáltatás vezérlője egy szoftver, amely fordított proxyt, konfigurálható forgalom-útválasztást és TLS-végződtetést biztosít a Kubernetes-szolgáltatásokszámára. Kubernetes inress erőforrások konfigurálására használják a be- és telepítési szabályok és útvonalak az egyes Kubernetes-szolgáltatások. A bejövő forgalom vezérlő és a bejövő forgalom szabályok használatával egyetlen IP-cím használható a forgalom irányításához több szolgáltatás egy Kubernetes-fürtben.

Ez a cikk bemutatja, hogyan telepítheti az [NGINX bejövő forgalom vezérlőegy][nginx-ingress] Azure Kubernetes-fürt (AKS) fürtben. Saját tanúsítványokat hoz létre, és hozzon létre egy Kubernetes titkos kulcsot a be- ésévisztő útvonalhoz. Végül két alkalmazás fut az AKS-fürtben, amelyek mindegyike egyetlen IP-címen érhető el.

További lehetőségek:

- [Egyszerű be- és átszivárgási vezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazásútválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Belső, magánhálózati és IP-címet használó be- és áthálózaton belüli adatélmény-vezérlő létrehozása][aks-ingress-internal]
- Bejövő forgalom vezérlő létrehozása, amely a Let's Encrypt segítségével automatikusan generáltTLS-tanúsítványokat [dinamikus nyilvános IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk a Helm segítségével telepíti az NGINX bejövő adatkezelő t és egy minta webalkalmazást. Helm inicializálva kell rendelkeznie az AKS-fürtön belül, és egy service-fiókot kell használnia a Tiller számára. Győződjön meg arról, hogy a Helm legújabb kiadását használja. A frissítéssel kapcsolatos tudnivalókat a [Helm telepítési dokumentumokban találja.][helm-install] A Helm konfigurálásáról és használatáról további információt az [Alkalmazások telepítése helmtel az Azure Kubernetes szolgáltatásban (AKS) című témakörben][use-helm]talál.

Ez a cikk azt is megköveteli, hogy az Azure CLI 2.0.64-es vagy újabb verzióját futtassa. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Be- és be- és átakat meghatározó vezérlő létrehozása

A bejövő kapcsolat vezérlőjének `Helm` létrehozásához használja a *nginx-inress telepítését.* A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. A bejövő adatkezelő replikáinak teljes körű kihasználása érdekében győződjön meg arról, hogy az AKS-fürtben egynél több csomópont található.

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
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

A telepítés során egy Azure nyilvános IP-cím jön létre a be- és a be- és a di. Ez a nyilvános IP-cím statikus a be- és a be- ésaszvezérlő élettartama. Ha törli a be- éséjegyvezérlőt, a nyilvános IP-címhozzárendelés elvész. Ha ezután létrehoz egy további be- éséi vezérlőt, egy új nyilvános IP-cím lesz hozzárendelve. Ha meg szeretné őrizni a nyilvános IP-cím használatát, létrehozhat [egy statikus nyilvános IP-címmel rendelkező be- és visszafértő-vezérlőt.][aks-ingress-static-tls]

A nyilvános IP-cím leéséhez használja a `kubectl get service` parancsot. Az IP-cím hozzárendelése néhány percet vesz igénybe.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Jegyezze fel ezt a nyilvános IP-címet, ahogy az az utolsó lépésben a központi telepítés teszteléséhez használt.

Még nem hoztak létre be- és be- és bejárási szabályokat. Ha a nyilvános IP-címet keresi, megjelenik az NGINX bejövő adatvezérlő alapértelmezett 404-es oldala.

## <a name="generate-tls-certificates"></a>TLS-tanúsítványok létrehozása

Ebben a cikkben hozzunk létre egy `openssl`önaláírt tanúsítványt a segítségével. Éles használatra megbízható, aláírt tanúsítványt kell kérnie egy szolgáltatón vagy a saját hitelesítésszolgáltatóján keresztül. A következő lépésben létrehoz egy Kubernetes *titkos* kulcsot az OpenSSL által létrehozott TLS-tanúsítvánnyal és személyes kulccsal.

A következő példa egy 2048 bites RSA X509 tanúsítványt hoz létre, amely 365 napig érvényes *az aks-ingress-tls.crt*névre. A személyes kulcsfájl neve *aks-ingress-tls.key*. A Kubernetes TLS-titok mindkét fájlt igényli.

Ez a cikk a *demo.azure.com* téma közös nevével működik, és nem kell módosítani. Éles használatra adja meg a saját `-subj` szervezeti értékeit a paraméterhez:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Kubernetes titkos kódjának létrehozása a TLS-tanúsítványhoz

Annak engedélyezéséhez, hogy a Kubernetes használhassa a TLS-tanúsítványt és a személyes kulcsot a be- és a be- és écses vezérlőhöz, hozzon létre és használjon titkos kulcsot. A titkos kulcs egyszer van definiálva, és az előző lépésben létrehozott tanúsítványt és kulcsfájlt használja. Ezután hivatkozik erre a titkos kulcsot, amikor meghatározza a be- ésresútvonalakat.

A következő példa létrehoz egy titkos nevet *aks-ingress-tls:*

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Bemutatóalkalmazások futtatása

A be- és visszaszolgáltatás vezérlője és a tanúsítvány titkos titkára konfigurálva van. Most futtasson két bemutató alkalmazást az AKS-fürtben. Ebben a példában a Helm egy egyszerű "Hello world" alkalmazás két példányának üzembe helyezésére szolgál.

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

Mindkét alkalmazás most fut a Kubernetes-fürtön, azonban egy `ClusterIP`típusú szolgáltatással vannak konfigurálva. Mint ilyen, az alkalmazások nem érhetők el az internetről. Ha nyilvánosan elérhetővé szeretné tenni őket, hozzon létre egy Kubernetes-be- és erőforrást. A be- és elő-visszaforrása konfigurálja azokat a szabályokat, amelyek a forgalmat a két alkalmazás egyikéhez irányítják.

A következő példában a `https://demo.azure.com/` címhez vezető forgalom `aks-helloworld`a nevű szolgáltatáshoz kerül. A címre `https://demo.azure.com/hello-world-two` irányuló forgalom a `ingress-demo` szolgáltatáshoz kerül. Ebben a cikkben nem kell módosítania ezeket a bemutató állomásneveket. Éles használatra adja meg a tanúsítványkérelem és a létrehozási folyamat részeként megadott neveket.

> [!TIP]
> Ha a tanúsítványigénylési folyamat során megadott állomásnév, a KN-név nem egyezik meg a bejövő forgalom útvonalán definiált állomással, akkor a bejövő forgalom vezérlője egy *Kubernetes ingress controller hamis tanúsítványfigyelmeztetést* jelenít meg. Győződjön meg arról, hogy a tanúsítvány és a be- és be- és be- és útvonalállomásnevek egyeznek.

A *tls* szakasz megmondja a befelé irányuló útvonalat, hogy a titkos nevű *aks-ingress-tls* a gazdagép *demo.azure.com.* Éles környezetben adja meg a saját állomáscímét.

Hozzon létre `hello-world-ingress.yaml` egy elnevezett fájlt, és másolja a következő példa YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
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

## <a name="test-the-ingress-configuration"></a>A be- és visszaszolgáltatás konfigurációjának tesztelése

A tanúsítványok teszteléséhez *demo.azure.com* a mi `curl` hamis demo.azure.com gazdagép, használja, és adja meg a *--feloldási* paraméter. Ez a paraméter lehetővé teszi a *demo.azure.com* név hozzárendelését a be- éséjegyvezérlő nyilvános IP-címéhez. Adja meg a saját bejövő kapcsolatvezérlőnyilvános IP-címét, ahogy az a következő példában látható:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Nincs további elérési út a címhez, így a be- */* ésakozóvezérlő alapértelmezés szerint az útvonal. Az első bemutató alkalmazás visszaad, amint az a következő tömörített példa kimenet:

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

A *parancsban* `curl` szereplő -v paraméter részletes információkat ad ki, beleértve a kapott TLS-tanúsítványt is. A göndör kimenet felénél ellenőrizheti, hogy a saját TLS-tanúsítványát használta-e. A *-k* paraméter továbbra is betölti az oldalt, annak ellenére, hogy önaláírt tanúsítványt használunk. A következő példa azt mutatja, hogy a *kibocsátó: CN=demo.azure.com; O=aks-ingress-tls* tanúsítványt használtak:

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Most add hozzá *a /hello-world-2* `https://demo.azure.com/hello-world-two`elérési utat a címhez, például . A második demó alkalmazás az egyéni címet adja vissza, amint az a következő tömörített példa kimenet:

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a cikk helm a be- és a be- és mintaalkalmazások telepítéséhez használt. Helm-diagram telepítésekor számos Kubernetes-erőforrás jön létre. Ezek az erőforrások podok, üzembe helyezések és szolgáltatások. Az erőforrások törléséhez törölheti a teljes mintanévteret vagy az egyes erőforrásokat.

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

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Törölje a kiadásokat `helm delete` a paranccsal. A következő példa törli az NGINX bejövő környezet és a két minta AKS hello world alkalmazások.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Ezután távolítsa el az AKS hello world alkalmazás Helm repo-ját:

```console
helm repo remove azure-samples
```

Távolítsa el a mintaalkalmazásokba irányuló forgalmat irányító be- és áttérési útvonalat:

```console
kubectl delete -f hello-world-ingress.yaml
```

A tanúsítvány titkos törlése:

```console
kubectl delete secret aks-ingress-tls
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
- [Belső, magánhálózati és IP-címet használó be- és áthálózaton belüli adatélmény-vezérlő létrehozása][aks-ingress-internal]
- Bejövő forgalom vezérlő létrehozása, amely a Let's Encrypt segítségével automatikusan generáltTLS-tanúsítványokat [dinamikus nyilvános IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
