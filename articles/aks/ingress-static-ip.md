---
title: Statikus IP-címmel rendelkező HTTP-bejövő adatvezérlő létrehozása az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan telepíthet és konfigurálhat egy, az Azure Kubernetes Service (ak) fürtben statikus nyilvános IP-címmel rendelkező NGINX bejövő adatforgalom-vezérlőt.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 7e390ed1151c45ca9a325b1795a8fbcad74cdfdb
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74194734"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Statikus nyilvános IP-címmel rendelkező bejövő vezérlő létrehozása az Azure Kubernetes szolgáltatásban (ak)

A bejövő vezérlő egy olyan szoftver, amely fordított proxyt, konfigurálható forgalmi útválasztást és TLS-megszakítást biztosít a Kubernetes-szolgáltatásokhoz. A Kubernetes bejövő erőforrásai az egyes Kubernetes-szolgáltatások bejövő szabályainak és útvonalának konfigurálására szolgálnak. A bejövő és a bejövő forgalomra vonatkozó szabályok használatával egyetlen IP-cím segítségével átirányíthatja a forgalmat több szolgáltatásba egy Kubernetes-fürtben.

Ez a cikk bemutatja, hogyan helyezheti üzembe az NGINX beléptetési [vezérlőt][nginx-ingress] egy Azure Kubernetes-szolgáltatási (ak-) fürtben. A bejövő vezérlő statikus nyilvános IP-címmel van konfigurálva. A Certificate [-Manager][cert-manager] projekttel automatikusan hozhatja meg és állíthatja be a tanúsítványok [titkosítását][lets-encrypt] . Végül két alkalmazás fut az AK-fürtben, amelyek mindegyike egyetlen IP-címen érhető el.

További lehetőségek:

- [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Saját TLS-tanúsítványokat használó bejövő adatkezelő létrehozása][aks-ingress-own-tls]
- [Hozzon létre egy adatforgalom-vezérlőt, amely a let titkosítást használja a dinamikus nyilvános IP-címmel rendelkező TLS-tanúsítványok automatikus létrehozásához][aks-ingress-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Ez a cikk a Helm használatával telepíti az NGINX beáramló vezérlőt, a tanúsítvány-kezelőt és egy minta webalkalmazást. Meg kell adnia a Helm-t az AK-fürtön belül, és egy szolgáltatásfiókot kell használnia a Kormányrúdhoz. Győződjön meg arról, hogy a Helm legújabb kiadását használja. A frissítési utasításokért tekintse meg a [Helm install docs][helm-install]című témakört. A Helm konfigurálásával és használatával kapcsolatos további információkért lásd: [alkalmazások telepítése az Azure Kubernetes szolgáltatásban (ak)][use-helm].

Ehhez a cikkhez az Azure CLI 2.0.64 vagy újabb verzióját is futtatnia kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Bejövő adatkezelő létrehozása

Alapértelmezés szerint egy új nyilvános IP-cím-hozzárendeléssel jön létre az NGINX bejövő adatkezelője. Ez a nyilvános IP-cím csak a beáramló vezérlő élettartamára statikus, és a vezérlő törlése és újbóli létrehozása esetén elvész. Gyakori konfigurációs követelmény, hogy az NGINX bejövő vezérlő egy meglévő statikus nyilvános IP-címet adjon meg. A statikus nyilvános IP-cím akkor is megmarad, ha a bejövő vezérlőt törölték. Ez a módszer lehetővé teszi a meglévő DNS-rekordok és hálózati konfigurációk egységes módon történő használatát az alkalmazások életciklusa során.

Ha létre kell hoznia egy statikus nyilvános IP-címet, először le kell kérnie az AK-fürt erőforráscsoport-nevét az az [AK show][az-aks-show] paranccsal:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Ezután hozzon létre egy nyilvános IP-címet a *statikus* kiosztási módszerrel az az [Network Public-IP Create][az-network-public-ip-create] parancs használatával. A következő példa egy *myAKSPublicIP* nevű nyilvános IP-címet hoz létre az alábbi lépésben beszerzett AK fürterőforrás-csoportban:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

Most telepítse az *Nginx-* beléptetési diagramot a Helm szolgáltatással. Adja hozzá a `--set controller.service.loadBalancerIP` paramétert, és adja meg az előző lépésben létrehozott saját nyilvános IP-címet. A hozzáadott redundancia érdekében az NGINX bejövő vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. Ahhoz, hogy teljes mértékben élvezhesse a bejövő vezérlő replikáit, győződjön meg arról, hogy az AK-fürt több csomópontja van.

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
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
```

Ha a Kubernetes terheléselosztó szolgáltatás létrejön az NGINX beáramló vezérlőhöz, a statikus IP-cím hozzá van rendelve, ahogy az a következő példában látható:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
dinky-panda-nginx-ingress-controller        LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
dinky-panda-nginx-ingress-default-backend   ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Még nem jöttek létre Bejövő szabályok, így az NGINX bejövő vezérlő alapértelmezett 404 lapja jelenik meg, ha megkeresi a nyilvános IP-címet. A bejövő szabályok a következő lépésekben vannak konfigurálva.

## <a name="configure-a-dns-name"></a>DNS-név konfigurálása

Ahhoz, hogy a HTTPS-tanúsítványok megfelelően működjenek, állítson be egy teljes tartománynevet a bejövő vezérlő IP-címéhez. Frissítse a következő parancsfájlt a bejövő vezérlő IP-címével és egy egyedi névvel, amelyet a teljes tartománynévhez használni szeretne:

```azurecli-interactive
#!/bin/bash

# Public IP address of your ingress controller
IP="40.121.63.72"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

A bejövő vezérlő mostantól elérhető a teljes tartománynévvel.

## <a name="install-cert-manager"></a>Tanúsítvány telepítése – kezelő

Az NGINX bejövő adatkezelője támogatja a TLS-megszakítást. A HTTPS-tanúsítványok lekérésének és konfigurálásának számos módja van. Ez a cikk a [CERT-Manager][cert-manager]használatát mutatja be, amely lehetővé teszi az automatikus titkosítást a tanúsítvány-létrehozási és-kezelési funkciók [titkosításához][lets-encrypt] .

> [!NOTE]
> Ez a cikk a `staging` környezetet használja a titkosításhoz. Éles környezetben az erőforrás-definíciók és a Helm-diagram telepítésekor használja az `letsencrypt-prod` és a `https://acme-v02.api.letsencrypt.org/directory`.

Ha a tanúsítvány-kezelő vezérlőt egy RBAC-kompatibilis fürtön szeretné telepíteni, használja a következő `helm install` parancsot:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.11/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.11.0 \
  jetstack/cert-manager
```

A tanúsítvány-kezelő konfigurálásával kapcsolatos további információkért tekintse meg a [CERT-Manager projektet][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>HITELESÍTÉSSZOLGÁLTATÓI fürt kiállítójának létrehozása

A tanúsítványok kiállítása előtt a tanúsítvány-kezelőnek [kiállítói][cert-manager-issuer] vagy [ClusterIssuer][cert-manager-cluster-issuer] erőforrásra van szüksége. Ezek a Kubernetes-erőforrások azonosak a funkcionalitással, azonban `Issuer` egyetlen névtérben működnek, és `ClusterIssuer` az összes névtérben működik. További információt a [tanúsítvány-kezelő kiállítói][cert-manager-issuer] dokumentációjában talál.

Hozzon létre egy fürt kiállítóját, például `cluster-issuer.yaml`a következő példa jegyzékfájl használatával. Frissítse az e-mail-címet a szervezete érvényes címével:

```yaml
apiVersion: cert-manager.io/v1alpha2
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

A kibocsátó létrehozásához használja a `kubectl apply -f cluster-issuer.yaml` parancsot.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.cert-manager.io/letsencrypt-staging created
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

Mindkét alkalmazás már fut a Kubernetes-fürtön, azonban `ClusterIP`típusú szolgáltatással vannak konfigurálva. Így az alkalmazások nem érhetők el az internetről. A nyilvánosan elérhetővé tételéhez hozzon létre egy Kubernetes-bejövő erőforrást. A bejövő erőforrás konfigurálja azokat a szabályokat, amelyek átirányítják a forgalmat a két alkalmazás egyikére.

A következő példában a `https://demo-aks-ingress.eastus.cloudapp.azure.com/`re irányuló forgalmat a rendszer átirányítja a `aks-helloworld`nevű szolgáltatáshoz. A `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` felé irányuló forgalmat a rendszer átirányítja a `ingress-demo` szolgáltatáshoz. Frissítse a *gazdagépeket és a* *gazdagépet* az előző lépésben létrehozott DNS-névre.

Hozzon létre egy `hello-world-ingress.yaml` nevű fájlt, és másolja a következő példában szereplő YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
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

A CERT-Manager valószínűleg automatikusan létrehozott egy tanúsítvány-objektumot a bejövő forgalom-alátét használatával, amelyet a rendszer automatikusan telepít a CERT-Manager szolgáltatással a v 0.2.2 óta. További információkért lásd a bejövő adatokról szóló [dokumentációt][ingress-shim].

A tanúsítvány sikeres létrehozásának ellenőrzéséhez használja a `kubectl describe certificate tls-secret --namespace ingress-basic` parancsot.

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

Ha további tanúsítvány-erőforrást kell létrehoznia, ezt az alábbi példával teheti meg. Frissítse a *dnsNames* és a *tartományokat* az előző lépésben létrehozott DNS-névre. Ha csak belső bejövő vezérlőt használ, adja meg a szolgáltatás belső DNS-nevét.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
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

A tanúsítvány-erőforrás létrehozásához használja a `kubectl apply -f certificates.yaml` parancsot.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>A behatolási konfiguráció tesztelése

Nyisson meg egy webböngészőt a Kubernetes bejövő adatkezelője teljes tartománynevére, például *https://demo-aks-ingress.eastus.cloudapp.azure.com* .

Mivel ezek a példák a `letsencrypt-staging`használják, a kiállított SSL-tanúsítvány nem megbízható a böngésző számára. Fogadja el az alkalmazás folytatásához szükséges figyelmeztető üzenetet. A tanúsítvány adatai azt mutatják be, hogy ezt a *hamis le közbenső X1* -tanúsítványt a titkosítva állítja ki. Ez a hamis tanúsítvány azt jelzi, `cert-manager` megfelelően feldolgozotta a kérést, és a szolgáltatótól kapott tanúsítványt:

![Az átmeneti tanúsítvány titkosítása](media/ingress/staging-certificate.png)

Ha úgy módosítja a titkosítást, hogy `staging`helyett `prod` használjon, a titkosításra kiállított megbízható tanúsítványt a rendszer a következő példában látható módon használja:

![A tanúsítvány titkosítása](media/ingress/certificate.png)

A bemutató alkalmazás a böngészőben jelenik meg:

![Példa egy alkalmazásra](media/ingress/app-one.png)

Most adja hozzá a */Hello-World-Two* elérési útját a teljes tartománynévhez, például *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* . Az egyéni címmel rendelkező második bemutató alkalmazás a következőképpen jelenik meg:

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

Most sorolja fel a Helm kiadásait a `helm list` paranccsal. Keresse meg az *Nginx-beáramló*, a *CERT-Manager*és az *AK-HelloWorld*nevű diagramot az alábbi példában látható módon:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
waxen-hamster           1           Wed Mar  6 23:16:00 2019    DEPLOYED    nginx-ingress-1.3.1   0.22.0        kube-system
alliterating-peacock    1           Wed Mar  6 23:17:37 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
mollified-armadillo     1           Wed Mar  6 23:26:04 2019    DEPLOYED    aks-helloworld-0.1.0                default
wondering-clam          1           Wed Mar  6 23:26:07 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

Törölje a kiadásokat a `helm delete` paranccsal. A következő példa törli az NGINX beáramló üzembe helyezését, a Tanúsítványkezelőt és a két mintául szolgáló Hello World alkalmazást.

```
$ helm delete waxen-hamster alliterating-peacock mollified-armadillo wondering-clam

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Ezt követően távolítsa el a Helm-tárházat az AK Hello World alkalmazáshoz:

```console
helm repo remove azure-samples
```

Távolítsa el a bejövő forgalom útvonalát, amely irányítja a forgalmat a mintául szolgáló alkalmazásokba:

```console
kubectl delete -f hello-world-ingress.yaml
```

Törölje a saját maga névterét. Használja a `kubectl delete` parancsot, és adja meg a névtér nevét:

```console
kubectl delete namespace ingress-basic
```

Végezetül távolítsa el a bejövő vezérlőhöz létrehozott statikus nyilvános IP-címet. Adja meg *MC_* fürterőforrás-csoportjának nevét a cikk első lépéseként, például *MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>További lépések

Ez a cikk néhány külső összetevőt tartalmaz az ak-nak. Ha többet szeretne megtudni ezekről az összetevőkről, tekintse meg a következő Project-lapokat:

- [Helm parancssori felület][helm-cli]
- [NGINX bejövő adatkezelő][nginx-ingress]
- [tanúsítvány-kezelő][cert-manager]

További lehetőségek:

- [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Belső, privát hálózatot és IP-címet használó bejövő adatforgalom-vezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó bejövő adatkezelő létrehozása][aks-ingress-own-tls]
- [Dinamikus nyilvános IP-címmel rendelkező bejövő vezérlő létrehozása, amely lehetővé teszi a titkosítást a TLS-tanúsítványok automatikus létrehozásához][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
