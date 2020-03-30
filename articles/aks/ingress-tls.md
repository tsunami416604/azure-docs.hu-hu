---
title: HTTPS-be- és beszállítás létrehozása az Azure Kubernetes-szolgáltatás (AKS) fürtjével
description: Megtudhatja, hogyan telepíthet és konfigurálhat egy NGINX bejövő forgalom vezérlőt, amely a Let's Encrypt for automatic TLS tanúsítványgenerálást használja egy Azure Kubernetes-szolgáltatás (AKS) fürtben.
services: container-service
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 6f497ee3edd5ee831c091a5a50629df81673acea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191316"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>HTTPS-es adatforgalom-vezérlő létrehozása az Azure Kubernetes szolgáltatáson (AKS)

A be- ésvisszaszolgáltatás vezérlője egy szoftver, amely fordított proxyt, konfigurálható forgalom-útválasztást és TLS-végződtetést biztosít a Kubernetes-szolgáltatásokszámára. Kubernetes inress erőforrások konfigurálására használják a be- és telepítési szabályok és útvonalak az egyes Kubernetes-szolgáltatások. A bejövő forgalom vezérlő és a bejövő forgalom szabályok használatával egyetlen IP-cím használható a forgalom irányításához több szolgáltatás egy Kubernetes-fürtben.

Ez a cikk bemutatja, hogyan telepítheti az [NGINX bejövő forgalom vezérlőegy][nginx-ingress] Azure Kubernetes-fürt (AKS) fürtben. A [cert-manager][cert-manager] projekt segítségével automatikusan generálja és [konfigurálja A tanúsítványok titkosítása.][lets-encrypt] Végül két alkalmazás fut az AKS-fürtben, amelyek mindegyike egyetlen IP-címen érhető el.

További lehetőségek:

- [Egyszerű be- és átszivárgási vezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazásútválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Belső, magánhálózati és IP-címet használó be- és áthálózaton belüli adatélmény-vezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó be- és hanghálózati vezérlő létrehozása][aks-ingress-own-tls]
- [Bejövő forgalom vezérlő létrehozása, amely a Let's Encrypt segítségével automatikusan létrehozza a TLS-tanúsítványokat statikus nyilvános IP-címmel][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Ez a cikk azt is feltételezi, hogy van [egy egyéni tartománya,][custom-domain] amelynek [DNS-zónája][dns-zone] ugyanabban az erőforráscsoportban található, mint az AKS-fürt.

Ez a cikk a Helm segítségével telepíti az NGINX bejövő adatkezelőt, a cert-kezelőt és egy mintawebalkalmazást. Győződjön meg arról, hogy a Helm legújabb kiadását használja. A frissítéssel kapcsolatos tudnivalókat a [Helm telepítési dokumentumokban találja.][helm-install] A Helm konfigurálásáról és használatáról további információt az [Alkalmazások telepítése helmtel az Azure Kubernetes szolgáltatásban (AKS) című témakörben][use-helm]talál.

Ez a cikk azt is megköveteli, hogy az Azure CLI 2.0.64-es vagy újabb verzióját futtassa. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Be- és be- és átakat meghatározó vezérlő létrehozása

A bejövő kapcsolat vezérlőjének létrehozásához használja a parancsot a `helm` *nginx-inress telepítéséhez.* A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. A bejövő adatkezelő replikáinak teljes körű kihasználása érdekében győződjön meg arról, hogy az AKS-fürtben egynél több csomópont található.

A bejövő forgalmi vezérlőt egy Linux-csomóponton is ütemezni kell. A Windows Server-csomópontok (jelenleg előzetes verzióban az AKS) nem futtatható a bejövő adatbeviteli vezérlő. A csomópont-választó `--set nodeSelector` paraméterrel történő meghatározása arra utasítja a Kubernetes ütemezőt, hogy az NGINX bejövő vezérlőt Linux-alapú csomóponton futtassa.

> [!TIP]
> A következő példa létrehoz egy Kubernetes névteret a *be- és alapvető*nevű be- és erőforráshoz. Szükség szerint adjon meg egy névteret a saját környezetéhez.

> [!TIP]
> Ha engedélyezni szeretné [az ügyfélforrás IP-címének megőrzését][client-source-ip] `--set controller.service.externalTrafficPolicy=Local` a fürtben lévő tárolókra vonatkozó kérelmekhez, adja hozzá a Helm telepítési parancshoz. Az ügyfélforrás *IP-címe az X-Forwarded-For*csoportban található kérelemfejlécben található. Ha egy olyan be- ésfeleltetót használ, amelyen az ügyfélforrás IP-megőrzése engedélyezve van, az SSL-áthaladás nem fog működni.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repo
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

A telepítés során egy Azure nyilvános IP-cím jön létre a be- és a be- és a di. Ez a nyilvános IP-cím statikus a be- és a be- ésaszvezérlő élettartama. Ha törli a be- éséjegyvezérlőt, a nyilvános IP-címhozzárendelés elvész. Ha ezután létrehoz egy további be- éséi vezérlőt, egy új nyilvános IP-cím lesz hozzárendelve. Ha meg szeretné őrizni a nyilvános IP-cím használatát, létrehozhat [egy statikus nyilvános IP-címmel rendelkező be- és visszafértő-vezérlőt.][aks-ingress-static-tls]

A nyilvános IP-cím leéséhez használja a `kubectl get service` parancsot. Az IP-cím hozzárendelése néhány percet vesz igénybe.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
nginx-ingress-controller                         LoadBalancer   10.0.182.160   MY_EXTERNAL_IP  80:30920/TCP,443:30426/TCP   20m
nginx-ingress-default-backend                    ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Még nem hoztak létre be- és be- és bejárási szabályokat. Ha a nyilvános IP-címet keresi, megjelenik az NGINX bejövő adatvezérlő alapértelmezett 404-es oldala.

## <a name="add-an-a-record-to-your-dns-zone"></a>A rekord hozzáadása a DNS-zónához

Adjon hozzá egy *A* rekordot a DNS-zónához az NGINX szolgáltatás külső IP-címével az [hálózati dns rekord-készlet-készlet-rekord][az-network-dns-record-set-a-add-record]használatával.

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Szükség esetén egyéni tartomány helyett beállíthatja a be- éségy/egy ip-cím teljes tartománynév-tartományt. Vegye figyelembe, hogy ez a minta egy Bash shell.
> 
> ```azurecli-interactive
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>Tanúsítványkezelő telepítése

Az NGINX bejövőforgalom-vezérlő támogatja a TLS-megszakítást. A HTTPS-tanúsítványok lekérésének és konfigurálásának számos módja van. Ez a cikk bemutatja a [cert-manager][cert-manager], amely automatikus Lehetővé teszi a tanúsítványok [konfigurálása][lets-encrypt] és felügyeleti funkciók titkosítását.

A cert-manager vezérlő telepítése:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the ingress-basic namespace to disable resource validation
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
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
```

A kibocsátó létrehozásához használja `kubectl apply` a parancsot.

```console
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

## <a name="run-demo-applications"></a>Bemutatóalkalmazások futtatása

Egy be- és éresvezérlő és egy tanúsítványkezelési megoldás lett konfigurálva. Most futtasson két bemutató alkalmazást az AKS-fürtben. Ebben a példában a Helm egy egyszerű *Hello world* alkalmazás két példányának üzembe helyezésére szolgál.

A minta Helm-diagramok telepítése előtt adja hozzá az Azure-minták tárház a Helm környezetben.

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Hozzon létre egy *aks-helloworld* nevű bemutatóalkalmazást az *azure-samples/aks-helloworld* Helm diagram használatával.

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Hozzon létre egy második példányt az *aks-helloworld-two*nevű bemutatóalkalmazásból. Adjon meg egy új címet és egyedi szolgáltatásnevet, hogy a két alkalmazás vizuálisan elkülönüljön a *--set*használatával.

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Be- és befelé vezető útvonal létrehozása

Mindkét alkalmazás most fut a Kubernetes-fürtön. Azonban ők konfigurálva egy típusú `ClusterIP` szolgáltatás, és nem érhető el az internetről. Ha nyilvánosan elérhetővé szeretné tenni őket, hozzon létre egy Kubernetes-be- és erőforrást. A be- és elő-visszaforrása konfigurálja azokat a szabályokat, amelyek a forgalmat a két alkalmazás egyikéhez irányítják.

A következő példában a címre irányuló forgalom *hello-world-ingress. MY_CUSTOM_DOMAIN* az *aks-helloworld* szolgáltatáshoz van irányítva. Forgalom a *címre hello-world-ingress. MY_CUSTOM_DOMAIN/hello-world-two* az *aks-helloworld-2* szolgáltatáshoz van irányítva. Forgalom *a világ-be-be-ress. MY_CUSTOM_DOMAIN/static* a statikus eszközök *aks-helloworld* nevű szolgáltatáshoz van irányítva.

Hozzon létre `hello-world-ingress.yaml` egy nevető fájlt az alábbi Példa YAML használatával. Frissítse az *állomásokat* és az *állomást* az előző lépésben létrehozott DNS-névre.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
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
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

Hozza létre a be- `kubectl apply` éséidézőerőforrást a paranccsal.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Tanúsítványobjektum létrehozásának ellenőrzése

Ezután létre kell hozni egy tanúsítvány-erőforrást. A tanúsítvány-erőforrás határozza meg a kívánt X.509 tanúsítványt. További információt a [tanúsítványkezelő tanúsítványai című témakörben talál.][cert-manager-certificates] A Cert-manager automatikusan létrehozott egy tanúsítványobjektumot a bejövő shim használatával, amely automatikusan üzembe kerül a cert-managerrel a v0.2.2 óta. További információt a [be- és achimdokumentációban][ingress-shim]talál.

A tanúsítvány sikeres létrehozásának ellenőrzéséhez `kubectl get certificate --namespace ingress-basic` használja a parancsot, és ellenőrizze, hogy *a READY* *igaz parancs több*percig is eltarthat.

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>A be- és visszaszolgáltatás konfigurációjának tesztelése

Nyisson meg egy webböngészőt a *hello-world-inress számára. MY_CUSTOM_DOMAIN* a Kubernetes be- és visszahálózaton. Figyelje meg, hogy átirányítja a HTTPS használatára, és a tanúsítvány megbízható, és a bemutató alkalmazás megjelenik a webböngészőben. Adja hozzá a */hello-world-two* elérési utat, és figyelje meg a második demó alkalmazást az egyéni címmel.

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

Másik lehetőségként egy részletesebb megközelítés az egyes létrehozott erőforrások törlése. Először távolítsa el a fürtkiállító erőforrásait:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Sorolja fel a `helm list` Helm-kiadásokat a paranccsal. Keresse meg a *nginx-inress* és *aks-helloworld*nevű diagramokat, ahogy az a következő példa kimenetben látható:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-15 10:24:32.054871 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-15 10:24:37.671667 -0600 CST    deployed        aks-helloworld-0.1.0               
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Törölje a kiadásokat `helm delete` a paranccsal. A következő példa törli az NGINX bejövő környezet, és a két minta AKS hello world alkalmazások.

```
$ helm uninstall aks-helloworld aks-helloworld-two cert-manager nginx --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "cert-manager" uninstalled
release "nginx" uninstalled
```

Ezután távolítsa el az AKS hello world alkalmazás Helm repo-ját:

```console
helm repo remove azure-samples
```

Távolítsa el a mintaalkalmazásokba irányuló forgalmat irányító be- és áttérési útvonalat:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Végül törölheti magát a névteret. Használja `kubectl delete` a parancsot, és adja meg a névtér nevét:

```console
kubectl delete namespace ingress-basic
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
- [Bejövő forgalom vezérlő létrehozása, amely a Let's Encrypt segítségével automatikusan létrehozza a TLS-tanúsítványokat statikus nyilvános IP-címmel][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
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
