---
title: Statikus IP-címmel rendelkező HTTP-be- és áthálózaton (AKS) hozzon létre egy HTTP-be- és be- és címvezérlőt
description: Ismerje meg, hogyan telepíthet és konfigurálhat egy statikus nyilvános IP-címmel rendelkező NGINX bejövő forgalom vezérlőt egy Azure Kubernetes-fürt (AKS) fürtben.
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 10422595b85c71020225df694778e6b8ae7e0185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191350"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Statikus nyilvános IP-címmel rendelkező bejövő forgalomvezérlő létrehozása az Azure Kubernetes-szolgáltatásban (AKS)

A be- ésvisszaszolgáltatás vezérlője egy szoftver, amely fordított proxyt, konfigurálható forgalom-útválasztást és TLS-végződtetést biztosít a Kubernetes-szolgáltatásokszámára. Kubernetes inress erőforrások konfigurálására használják a be- és telepítési szabályok és útvonalak az egyes Kubernetes-szolgáltatások. A bejövő forgalom vezérlő és a bejövő forgalom szabályok használatával egyetlen IP-cím használható a forgalom irányításához több szolgáltatás egy Kubernetes-fürtben.

Ez a cikk bemutatja, hogyan telepítheti az [NGINX bejövő forgalom vezérlőegy][nginx-ingress] Azure Kubernetes-fürt (AKS) fürtben. A be- és előtér-vezérlő statikus nyilvános IP-címmel van konfigurálva. A [cert-manager][cert-manager] projekt segítségével automatikusan generálja és [konfigurálja A tanúsítványok titkosítása.][lets-encrypt] Végül két alkalmazás fut az AKS-fürtben, amelyek mindegyike egyetlen IP-címen érhető el.

További lehetőségek:

- [Egyszerű be- és átszivárgási vezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazásútválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Saját TLS-tanúsítványokat használó be- és hanghálózati vezérlő létrehozása][aks-ingress-own-tls]
- [Bejövő forgalom vezérlő létrehozása, amely a Let's Encrypt segítségével automatikusan létrehozza a TLS-tanúsítványokat dinamikus nyilvános IP-címmel][aks-ingress-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Ez a cikk a Helm segítségével telepíti az NGINX bejövő adatkezelőt, a cert-kezelőt és egy mintawebalkalmazást. Győződjön meg arról, hogy a Helm legújabb kiadását használja. A frissítéssel kapcsolatos tudnivalókat a [Helm telepítési dokumentumokban találja.][helm-install] A Helm konfigurálásáról és használatáról további információt az [Alkalmazások telepítése helmtel az Azure Kubernetes szolgáltatásban (AKS) című témakörben][use-helm]talál.

Ez a cikk azt is megköveteli, hogy az Azure CLI 2.0.64-es vagy újabb verzióját futtassa. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Be- és be- és átakat meghatározó vezérlő létrehozása

Alapértelmezés szerint egy NGINX bejövő kapcsolat vezérlő jön létre egy új nyilvános IP-cím hozzárendelés. Ez a nyilvános IP-cím csak statikus a be- ésabanyúló vezérlő élettartama, és elvész, ha a vezérlő törlődik, és újra létre. Az NGINX bejövő kapcsolat vezérlőjének általános konfigurációs követelménye, hogy az NGINX bejövő kapcsolat vezérlőjének egy meglévő statikus nyilvános IP-címet biztosítson. A statikus nyilvános IP-cím megmarad, ha a be- éséjegy-vezérlő törlődik. Ez a megközelítés lehetővé teszi a meglévő DNS-rekordok és hálózati konfigurációk egységes módon használatát az alkalmazások teljes életciklusa során.

Ha statikus nyilvános IP-címet kell létrehoznia, először az aKS [show][az-aks-show] paranccsal kapja meg az AKS-fürt erőforráscsoportnevét:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Ezután hozzon létre egy nyilvános IP-címet a *statikus* foglalási módszerrel az [az-hálózati nyilvános ip create][az-network-public-ip-create] paranccsal. A következő példa létrehoz egy *myAKSPublicIP* nevű nyilvános IP-címet az előző lépésben kapott AKS-fürterőforrás-csoportban:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

Most telepítsd a *nginx-ingress táblázatot* Helm-el. Adja `--set controller.service.loadBalancerIP` hozzá a paramétert, és adja meg az előző lépésben létrehozott saját nyilvános IP-címet. A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. A bejövő adatkezelő replikáinak teljes körű kihasználása érdekében győződjön meg arról, hogy az AKS-fürtben egynél több csomópont található.

A bejövő forgalmi vezérlőt egy Linux-csomóponton is ütemezni kell. A Windows Server-csomópontok (jelenleg előzetes verzióban az AKS) nem futtatható a bejövő adatbeviteli vezérlő. A csomópont-választó `--set nodeSelector` paraméterrel történő meghatározása arra utasítja a Kubernetes ütemezőt, hogy az NGINX bejövő vezérlőt Linux-alapú csomóponton futtassa.

> [!TIP]
> A következő példa létrehoz egy Kubernetes névteret a *be- és alapvető*nevű be- és erőforráshoz. Szükség szerint adjon meg egy névteret a saját környezetéhez. Ha az AKS-fürt nincs engedélyezve az RBAC-on, adja hozzá `--set rbac.create=false` a Helm parancsokhoz.

> [!TIP]
> Ha engedélyezni szeretné [az ügyfélforrás IP-címének megőrzését][client-source-ip] `--set controller.service.externalTrafficPolicy=Local` a fürtben lévő tárolókra vonatkozó kérelmekhez, adja hozzá a Helm telepítési parancshoz. Az ügyfélforrás *IP-címe az X-Forwarded-For*csoportban található kérelemfejlécben található. Ha egy olyan be- ésfeleltetót használ, amelyen az ügyfélforrás IP-megőrzése engedélyezve van, az SSL-áthaladás nem fog működni.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
```

Amikor a Kubernetes terheléselosztó szolgáltatás jön létre az NGINX bejövő forgalom vezérlőhöz, a statikus IP-cím hozzá van rendelve, ahogy az a következő példa kimenetben látható:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
nginx-ingress-controller                    LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
nginx-ingress-default-backend               ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Még nem jött létre bejövő házirend, így az NGINX bejövő adatószabályzó alapértelmezett 404-es lapja jelenik meg, ha a nyilvános IP-címet keresi. A bejövő támadások szabályai a következő lépésekben vannak konfigurálva.

## <a name="configure-a-dns-name"></a>DNS-név konfigurálása

A HTTPS-tanúsítványok megfelelő működéséhez konfiguráljon egy teljes tartománynat a be- ésécsvezérlő IP-címéhez. Frissítse a következő parancsfájlt a belső kapcsolatvezérlő IP-címével és egy egyedi névvel, amelyet a teljes tartománynévhez szeretne használni:

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

A be- ésres-vezérlő most már elérhető a teljes tartományon keresztül.

## <a name="install-cert-manager"></a>Tanúsítványkezelő telepítése

Az NGINX bejövőforgalom-vezérlő támogatja a TLS-megszakítást. A HTTPS-tanúsítványok lekérésének és konfigurálásának számos módja van. Ez a cikk bemutatja a [cert-manager][cert-manager], amely automatikus Lehetővé teszi a tanúsítványok [konfigurálása][lets-encrypt] és felügyeleti funkciók titkosítását.

> [!NOTE]
> Ez a `staging` cikk a Let's Encrypt környezetét használja. Éles környezetben, `letsencrypt-prod` használja, és `https://acme-v02.api.letsencrypt.org/directory` az erőforrás-definíciók és a Helm diagram telepítésekor.

A cert-manager vezérlő RBAC-kompatibilis fürtben történő `helm install` telepítéséhez használja a következő parancsot:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.13.0 \
  jetstack/cert-manager
```

A cert-manager konfigurációjáról a [cert-manager projektben][cert-manager]talál további információt.

## <a name="create-a-ca-cluster-issuer"></a>Hitelesítéshálózat-fürt kibocsátójának létrehozása

A tanúsítványok kiállítása előtt a tanúsítványkezelőnek [kiállítói][cert-manager-issuer] vagy [fürtkiállítói erőforrásra][cert-manager-cluster-issuer] van szüksége. Ezek a Kubernetes-erőforrások azonosak a funkcionalitásban, `Issuer` `ClusterIssuer` azonban egyetlen névtérben működnek, és minden névterületen működnek. További információt a [cert-manager kibocsátó][cert-manager-issuer] dokumentációjában talál.

Hozzon létre egy fürtkibocsátót, például `cluster-issuer.yaml`a következő példajegyzéket használja. Frissítse az e-mail címet a szervezet től érvényes címmel:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
```

A kibocsátó létrehozásához használja `kubectl apply -f cluster-issuer.yaml` a parancsot.

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Bemutatóalkalmazások futtatása

Egy be- és éresvezérlő és egy tanúsítványkezelési megoldás lett konfigurálva. Most futtasson két bemutató alkalmazást az AKS-fürtben. Ebben a példában a Helm egy egyszerű "Hello world" alkalmazás két példányának üzembe helyezésére szolgál.

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
helm install aks-helloworld-2 azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Be- és befelé vezető útvonal létrehozása

Mindkét alkalmazás most fut a Kubernetes-fürtön, azonban egy `ClusterIP`típusú szolgáltatással vannak konfigurálva. Mint ilyen, az alkalmazások nem érhetők el az internetről. Ha nyilvánosan elérhetővé szeretné tenni őket, hozzon létre egy Kubernetes-be- és erőforrást. A be- és elő-visszaforrása konfigurálja azokat a szabályokat, amelyek a forgalmat a két alkalmazás egyikéhez irányítják.

A következő példában a `https://demo-aks-ingress.eastus.cloudapp.azure.com/` címhez vezető forgalom `aks-helloworld`a nevű szolgáltatáshoz kerül. A címre `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` irányuló forgalom a `ingress-demo` szolgáltatáshoz kerül. Frissítse az *állomásokat* és az *állomást* az előző lépésben létrehozott DNS-névre.

Hozzon létre `hello-world-ingress.yaml` egy elnevezett fájlt, és másolja a következő példa YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
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

Hozza létre a be- `kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic` éséidézőerőforrást a paranccsal.

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Tanúsítványobjektum létrehozása

Ezután létre kell hozni egy tanúsítvány-erőforrást. A tanúsítvány-erőforrás határozza meg a kívánt X.509 tanúsítványt. További információt a [tanúsítványkezelő tanúsítványai című témakörben talál.][cert-manager-certificates]

A Cert-manager valószínűleg automatikusan létrehozott egy tanúsítványobjektumot a bejövő alátét használatával, amely a 2.2-es v0.2.-es jelleg óta automatikusan telepítve van a cert-managerrel. További információt a [be- és achimdokumentációban][ingress-shim]talál.

A tanúsítvány sikeres létrehozásának ellenőrzéséhez `kubectl describe certificate tls-secret --namespace ingress-basic` használja a parancsot.

Ha a tanúsítványt kiállították, a kimenet a következőhöz hasonló lesz:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Ha további tanúsítvány-erőforrást kell létrehoznia, ezt a következő példajegyzékfájltal teheti meg. Frissítse a *dnsNames* és *tartományokat* az előző lépésben létrehozott DNS-névre. Ha csak belső be- ésresvezérlőt használ, adja meg a szolgáltatás belső DNS-nevét.

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

A tanúsítvány-erőforrás létrehozásához `kubectl apply -f certificates.yaml` használja a parancsot.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>A be- és visszaszolgáltatás konfigurációjának tesztelése

Nyisson meg egy webböngészőt a Kubernetes befektvezérlőjének teljes tartománynevében, például *https://demo-aks-ingress.eastus.cloudapp.azure.com*.

Ahogy ezek `letsencrypt-staging`a példák használják, a böngésző nem bízik a kiadott SSL-tanúsítvánnyal. Fogadja el a figyelmeztetési kérdést az alkalmazás folytatásához. A tanúsítvány adatai azt mutatják, hogy ezt a *Hamis LE Intermediate X1* tanúsítványt a Let's Encrypt adta ki. Ez a hamis `cert-manager` tanúsítvány azt jelzi, hogy helyesen dolgozta fel a kérelmet, és tanúsítványt kapott a szolgáltatótól:

![Átmeneti tanúsítvány titkosítása](media/ingress/staging-certificate.png)

Ha a Let's Encrypt `prod` (Let's Encrypt) helyett a tegyük a titkosítás helyett `staging`használható megbízható tanúsítványt módosítja, ahogy az a következő példában látható:

![Tanúsítvány titkosítása](media/ingress/certificate.png)

A demo alkalmazás jelenik meg a böngészőben:

![Alkalmazás példa egy](media/ingress/app-one.png)

Most adja hozzá a */hello-world-2* elérési utat *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*a teljes tartománynnához, például . A második demo alkalmazás az egyéni cím jelenik meg:

![Alkalmazás példa két](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a cikk a Helm segítségével telepítse a be- és telepítési összetevőket, tanúsítványokat és mintaalkalmazásokat. Helm-diagram telepítésekor számos Kubernetes-erőforrás jön létre. Ezek az erőforrások podokat, üzemelő példányokat és szolgáltatásokat tartalmaznak. Az erőforrások törléséhez törölheti a teljes mintanévteret vagy az egyes erőforrásokat.

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

Másik lehetőségként egy részletesebb megközelítés az egyes létrehozott erőforrások törlése. Először távolítsa el a tanúsítvány erőforrásait:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Most sorolja fel a `helm list` Helm-kiadásokat a paranccsal. Keresse meg a *nginx-inress*, *cert-manager*és *aks-helloworld*nevű diagramokat, ahogy az a következő példa kimenetben látható:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-11 15:02:21.51172346   deployed        aks-helloworld-0.1.0
aks-helloworld-2        ingress-basic   1               2020-01-11 15:03:10.533465598  deployed        aks-helloworld-0.1.0
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic    1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Törölje a kiadásokat `helm uninstall` a paranccsal. A következő példa törli az NGINX bejövő telepítés, tanúsítványkezelő, és a két minta AKS hello world alkalmazások.

```
$ helm uninstall aks-helloworld aks-helloworld-2 nginx-ingress cert-manager -n ingress-basic

release "aks-helloworld" deleted
release "aks-helloworld-2" deleted
release "nginx-ingress" deleted
release "cert-manager" deleted
```

Ezután távolítsa el az AKS hello world alkalmazás Helm repo-ját:

```console
helm repo remove azure-samples
```

Törölje magát a névteret. Használja `kubectl delete` a parancsot, és adja meg a névtér nevét:

```console
kubectl delete namespace ingress-basic
```

Végül távolítsa el a statikus nyilvános IP-címet a be- és a be- és a be- és adhatósági vezérlőhöz. Adja meg *a cikk* első lépésében kapott MC_ fürterőforrás-csoport nevét, például *a MC_myResourceGroup_myAKSCluster_eastus:*

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>További lépések

Ez a cikk az AKS néhány külső összetevőjét is tartalmazta. Ezekről az összetevőkről a következő projektlapokon olvashat bővebben:

- [Kormányrúd CLI][helm-cli]
- [NGINX bejövő adatvezérlő][nginx-ingress]
- [cert-menedzser][cert-manager]

További lehetőségek:

- [Egyszerű be- és átszivárgási vezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazásútválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Belső, magánhálózati és IP-címet használó be- és áthálózaton belüli adatélmény-vezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó be- és hanghálózati vezérlő létrehozása][aks-ingress-own-tls]
- [Dinamikus nyilvános IP-című bejövő forgalom vezérlő létrehozása és a TLS-tanúsítványok automatikus létrehozásához szükséges Titkosítás konfigurálása][aks-ingress-tls]

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
