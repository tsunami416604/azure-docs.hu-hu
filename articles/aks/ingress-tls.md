---
title: HTTPS-bejövő forgalom létrehozása az Azure Kubernetes Service (ak) fürttel
description: Megtudhatja, hogyan telepíthet és konfigurálhat egy olyan NGINX beáramlási vezérlőt, amely egy Azure Kubernetes-szolgáltatási (ak-) fürtön használja az automatikus TLS-tanúsítvány létrehozását.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 30f25ad9152bc722b54a834ef0ed037ac1666014
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "67615293"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>HTTPS bejövő adatkezelő létrehozása az Azure Kubernetes szolgáltatásban (ak)

A bejövő vezérlő egy olyan szoftver, amely fordított proxyt, konfigurálható forgalmi útválasztást és TLS-megszakítást biztosít a Kubernetes-szolgáltatásokhoz. A Kubernetes bejövő erőforrásai az egyes Kubernetes-szolgáltatások bejövő szabályainak és útvonalának konfigurálására szolgálnak. A bejövő és a bejövő forgalomra vonatkozó szabályok használatával egyetlen IP-cím segítségével átirányíthatja a forgalmat több szolgáltatásba egy Kubernetes-fürtben.

Ez a cikk bemutatja, hogyan helyezheti üzembe az [NGINX][nginx-ingress] beléptetési vezérlőt egy Azure Kubernetes-szolgáltatási (ak-) fürtben. A Certificate [-Manager][cert-manager] projekttel automatikusan hozhatja [meg][lets-encrypt] és állíthatja be a tanúsítványok titkosítását. Végül két alkalmazás fut az AK-fürtben, amelyek mindegyike egyetlen IP-címen érhető el.

További lehetőségek:

- [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Belső, privát hálózatot és IP-címet használó bejövő adatforgalom-vezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó bejövő adatkezelő létrehozása][aks-ingress-own-tls]
- [Hozzon létre egy olyan bejövő adatkezelőt, amely a let titkosítást használja a statikus nyilvános IP-címmel rendelkező TLS-tanúsítványok automatikus létrehozásához][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Ez a cikk a Helm használatával telepíti az NGINX beáramló vezérlőt, a tanúsítvány-kezelőt és egy minta webalkalmazást. Meg kell adnia a Helm-t az AK-fürtön belül, és egy szolgáltatásfiókot kell használnia a Kormányrúdhoz. Győződjön meg arról, hogy a Helm legújabb kiadását használja. A frissítési utasításokért tekintse meg a [Helm install docs][helm-install]című témakört. A Helm konfigurálásával és használatával kapcsolatos további információkért lásd: [alkalmazások telepítése az Azure Kubernetes szolgáltatásban (ak)][use-helm].

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

A telepítés során létrejön egy Azure nyilvános IP-cím a beléptetési vezérlőhöz. Ez a nyilvános IP-cím statikus a bejövő vezérlő élettartama szempontjából. Ha törli a beáramló vezérlőt, a nyilvános IP-cím hozzárendelés elvész. Ha ezután létrehoz egy további bejövő vezérlőt, a rendszer egy új nyilvános IP-címet rendel hozzá. Ha meg szeretné őrizni a nyilvános IP-cím használatát, helyette [létrehozhat egy statikus nyilvános IP-címmel rendelkező bejövő vezérlőt][aks-ingress-static-tls]is.

A nyilvános IP-cím lekéréséhez használja `kubectl get service` az parancsot. Néhány percet vesz igénybe, hogy az IP-cím hozzá legyen rendelve a szolgáltatáshoz.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
billowing-kitten-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
billowing-kitten-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Még nem jöttek létre Bejövő szabályok. Ha megkeresi a nyilvános IP-címet, megjelenik az NGINX bejövő vezérlő alapértelmezett 404 lapja.

## <a name="configure-a-dns-name"></a>DNS-név konfigurálása

Ahhoz, hogy a HTTPS-tanúsítványok megfelelően működjenek, állítson be egy teljes tartománynevet a bejövő vezérlő IP-címéhez. Frissítse a következő parancsfájlt a bejövő vezérlő IP-címével és egy egyedi névvel, amelyet a teljes tartománynévhez használni szeretne:

```azurecli-interactive
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

A bejövő vezérlő mostantól elérhető a teljes tartománynévvel.

## <a name="install-cert-manager"></a>Tanúsítvány telepítése – kezelő

Az NGINX bejövő adatkezelője támogatja a TLS-megszakítást. A HTTPS-tanúsítványok lekérésének és konfigurálásának számos módja van. Ez a cikk a [CERT-Manager][cert-manager]használatát mutatja be, amely lehetővé [teszi][lets-encrypt] az automatikus titkosítást a tanúsítvány-létrehozási és-kezelési funkciók titkosításához.

> [!NOTE]
> Ez a cikk a `staging` környezetet használja a titkosításhoz. Éles környezetben az erőforrás-definíciókban `https://acme-v02.api.letsencrypt.org/directory` és a Helm-diagram telepítésekor használja `letsencrypt-prod` a és a-t.

Ha a tanúsítvány-kezelő vezérlőt egy RBAC-kompatibilis fürtön szeretné telepíteni, használja `helm install` a következő parancsot:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.8.0 \
  jetstack/cert-manager
```

A tanúsítvány-kezelő konfigurálásával kapcsolatos további információkért tekintse meg a [CERT-Manager projektet][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>HITELESÍTÉSSZOLGÁLTATÓI fürt kiállítójának létrehozása

A tanúsítványok kiállítása előtt a tanúsítvány-kezelőnek [kiállítói][cert-manager-issuer] vagy [ClusterIssuer][cert-manager-cluster-issuer] erőforrásra van szüksége. Ezek a Kubernetes-erőforrások azonosak a funkcionalitással, azonban `Issuer` egyetlen névtérben működnek, és `ClusterIssuer` az összes névtérben működnek. További információt a [tanúsítvány-kezelő kiállítói][cert-manager-issuer] dokumentációjában talál.

Hozzon létre egy fürt kiállítóját, `cluster-issuer.yaml`például:, a következő példa jegyzékfájl használatával. Frissítse az e-mail-címet a szervezete érvényes címével:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: ingress-basic
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

A kiállító létrehozásához használja az `kubectl apply -f cluster-issuer.yaml` parancsot.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Bemutató alkalmazások futtatása

Be van állítva egy bejövő vezérlő és egy tanúsítványkezelő megoldás. Most futtasson két bemutató alkalmazást az AK-fürtben. Ebben a példában a Helm egy egyszerű "Hello World" alkalmazás két példányának üzembe helyezésére szolgál.

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

Mindkét alkalmazás már fut a Kubernetes-fürtön, azonban egy típusú `ClusterIP`szolgáltatással vannak konfigurálva. Így az alkalmazások nem érhetők el az internetről. A nyilvánosan elérhetővé tételéhez hozzon létre egy Kubernetes-bejövő erőforrást. A bejövő erőforrás konfigurálja azokat a szabályokat, amelyek átirányítják a forgalmat a két alkalmazás egyikére.

A következő példában a címnek `https://demo-aks-ingress.eastus.cloudapp.azure.com/` való adatforgalom a nevű `aks-helloworld`szolgáltatáshoz lesz irányítva. A `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` `ingress-demo` rendszer átirányítja a címnek a szolgáltatás felé irányuló forgalmat. Frissítse a *gazdagépeket és a* *gazdagépet* az előző lépésben létrehozott DNS-névre.

Hozzon létre egy `hello-world-ingress.yaml` nevű fájlt, és másolja a következő példában YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
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

Hozza létre a bejövő erőforrásokat a `kubectl apply -f hello-world-ingress.yaml` parancs használatával.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Tanúsítvány-objektum létrehozása

Ezután létre kell hozni egy tanúsítvány-erőforrást. A tanúsítvány erőforrása határozza meg a kívánt X. 509 tanúsítványt. További információ: [CERT-Manager-tanúsítványok][cert-manager-certificates].

A CERT-Manager valószínűleg automatikusan létrehozott egy tanúsítvány-objektumot a bejövő forgalom-alátét használatával, amelyet a rendszer automatikusan telepít a CERT-Manager szolgáltatással a v 0.2.2 óta. További információkért lásd a [bejövő][ingress-shim]adatokról szóló dokumentációt.

A tanúsítvány sikeres létrehozásának ellenőrzéséhez használja az `kubectl describe certificate tls-secret --namespace ingress-basic` parancsot.

Ha a tanúsítvány ki lett állítva, a következőhöz hasonló kimenet jelenik meg:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Ha további tanúsítvány-erőforrást kell létrehoznia, ezt az alábbi példával teheti meg. Frissítse a *dnsNames* és a tartományokat az előző lépésben létrehozott DNS-névre. Ha csak belső bejövő vezérlőt használ, adja meg a szolgáltatás belső DNS-nevét.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret-staging
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

A tanúsítvány-erőforrás létrehozásához használja az `kubectl apply -f certificates.yaml` parancsot.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret-staging created
```

## <a name="test-the-ingress-configuration"></a>A behatolási konfiguráció tesztelése

Nyisson meg egy webböngészőt a Kubernetes bejövő adatkezelője teljes tartománynevére, *https://demo-aks-ingress.eastus.cloudapp.azure.com* például:.

A példákban `letsencrypt-staging`a kiállított SSL-tanúsítványt a böngésző nem tartja megbízhatónak. Fogadja el az alkalmazás folytatásához szükséges figyelmeztető üzenetet. A tanúsítvány adatai azt mutatják be, hogy ezt a *hamis le közbenső X1* -tanúsítványt a titkosítva állítja ki. Ez a hamis tanúsítvány `cert-manager` azt jelzi, hogy megfelelően feldolgozta a kérést, és a szolgáltatótól kapott tanúsítványt:

![Az átmeneti tanúsítvány titkosítása](media/ingress/staging-certificate.png)

Ha úgy módosítja a titkosítást `prod` `staging`, hogy a rendszer helyett a titkosítást használja, az alábbi példában látható módon a titkosított tanúsítvány is használatos.

![A tanúsítvány titkosítása](media/ingress/certificate.png)

A bemutató alkalmazás a böngészőben jelenik meg:

![Példa egy alkalmazásra](media/ingress/app-one.png)

Most adja hozzá a */Hello-World-Two* elérési útját a teljes *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* tartománynévhez, például:. Az egyéni címmel rendelkező második bemutató alkalmazás a következőképpen jelenik meg:

![Példa két alkalmazásra](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a cikk a beáramlási összetevők, a tanúsítványok és a minta alkalmazások telepítéséhez használható. Amikor központilag telepít egy Helm-diagramot, a rendszer számos Kubernetes-erőforrást hoz létre. Ezek az erőforrások a hüvelyek, a központi telepítések és a szolgáltatások részét képezik. Ezen erőforrások törléséhez törölheti a teljes minta névteret vagy az egyes erőforrásokat.

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

Azt is megteheti, hogy egy részletesebb megközelítéssel törli a létrehozott egyéni erőforrásokat. Először távolítsa el a tanúsítvány erőforrásait:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Most sorolja fel a Helm kiadásait `helm list` a paranccsal. Keresse meg az *Nginx-* beáramló, a *CERT-Manager*és az *AK-HelloWorld*nevű diagramot az alábbi példában látható módon:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
billowing-kitten        1           Wed Mar  6 19:37:43 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      kube-system
loitering-waterbuffalo  1           Wed Mar  6 20:25:01 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
flabby-deer             1           Wed Mar  6 20:27:54 2019    DEPLOYED    aks-helloworld-0.1.0                default
linting-echidna         1           Wed Mar  6 20:27:59 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

Törölje a kiadásokat a `helm delete` paranccsal. A következő példa törli az NGINX beáramló üzembe helyezését, a Tanúsítványkezelőt és a két mintául szolgáló Hello World alkalmazást.

```
$ helm delete billowing-kitten loitering-waterbuffalo flabby-deer linting-echidna

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Ezt követően távolítsa el a Helm-tárházat az AK Hello World alkalmazáshoz:

```console
helm repo remove azure-samples
```

Törölje a saját maga névterét. Használja a `kubectl delete` parancsot, és adja meg a névtér nevét:

```console
kubectl delete namespace ingress-basic
```

Végezetül távolítsa el a bejövő forgalom útvonalát, amely a minta alkalmazásokhoz irányítja a forgalmat:

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>További lépések

Ez a cikk néhány külső összetevőt tartalmaz az ak-nak. Ha többet szeretne megtudni ezekről az összetevőkről, tekintse meg a következő Project-lapokat:

- [Helm parancssori felület][helm-cli]
- [NGINX bejövő adatkezelő][nginx-ingress]
- [cert-manager][cert-manager]

További lehetőségek:

- [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Belső, privát hálózatot és IP-címet használó bejövő adatforgalom-vezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó bejövő adatkezelő létrehozása][aks-ingress-own-tls]
- [Hozzon létre egy olyan bejövő adatkezelőt, amely a let titkosítást használja a statikus nyilvános IP-címmel rendelkező TLS-tanúsítványok automatikus létrehozásához][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
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
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli