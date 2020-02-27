---
title: Egyéni bejövő traefik-vezérlő használata és HTTPS konfigurálása
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Ismerje meg, hogyan konfigurálhatja az Azure dev Spaces-t egyéni traefik bemenő vezérlő használatára, és hogyan konfigurálhatja a HTTPS-t az adott bejövő vezérlő használatával
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
ms.openlocfilehash: 9e0c726d97fc87a25d559ecc3478d3f85df4eeb8
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623159"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Egyéni bejövő traefik-vezérlő használata és HTTPS konfigurálása

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure dev Spaces-t egyéni traefik beáramlási vezérlő használatára. A cikk azt is bemutatja, hogyan konfigurálhatja az egyéni bejövő vezérlőt a HTTPS használatára.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes fiókot][azure-account-create].
* [Telepített Azure CLI][az-cli].
* [Azure Kubernetes Service (ak) fürt, amelyen engedélyezve van az Azure dev Spaces][qs-cli].
* a [kubectl][kubectl] telepítve van.
* A [Helm 3 telepítve van][helm-installed].
* [Egy egyéni tartomány][custom-domain] egy [DNS-zónával][dns-zone] , amely ugyanabban az erőforráscsoporthoz van, mint az AK-fürt.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Egyéni bejövő traefik-vezérlő konfigurálása

Kapcsolódjon a fürthöz a [kubectl][kubectl]és a Kubernetes parancssori ügyfél használatával. Ha `kubectl` szeretne konfigurálni a Kubernetes-fürthöz való kapcsolódáshoz, használja az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Adja hozzá a [hivatalos stabil Helm-tárházat][helm-stable-repo], amely tartalmazza a traefik beáramló vezérlő Helm diagramját.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Hozzon létre egy Kubernetes-névteret a traefik beáramló vezérlőhöz, és telepítse azt `helm`használatával.

> [!NOTE]
> Ha az AK-ban nincs engedélyezve a RBAC, távolítsa el a *--set RBAC. enabled = True* paramétert.

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> A fenti példa egy nyilvános végpontot hoz létre a bejövő vezérlőhöz. Ha ehelyett privát végpontot kell használnia a bejövő adatvezérlőhöz, adja hozzá a *--set Service. Megjegyzések kifejezést. " Service\\. Beta\\. kubernetes\\. IO/Azure-Load-Balancer-Internal "= true* paraméter a *Helm install* parancshoz.
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Ez a titkos végpont a virtuális hálózaton belül van, ahol az AK-beli fürtöt telepítették.

Szerezze be az IP-címet a traefik beáramló vezérlő szolgáltatáshoz a [kubectl Get][kubectl-get]használatával.

```console
kubectl get svc -n traefik --watch
```

A minta kimenet a *traefik* -névtérben lévő összes szolgáltatás IP-címeit jeleníti meg.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Adjon hozzá *egy* rekordot a DNS-zónához a traefik szolgáltatás külső IP-címével az [az Network DNS Record-set a Add-Record][az-network-dns-record-set-a-add-record]paranccsal.

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

A fenti példa *egy* rekordot vesz fel a *MY_CUSTOM_DOMAIN* DNS-zónába.

Ebben a cikkben az [Azure dev Spaces Bike Sharing Sample Application](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) használatával mutatjuk be az Azure dev Spaces szolgáltatást. Az alkalmazás klónozása a GitHubról, majd a címtárba való navigálása:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Nyissa meg a [Values. YAML][values-yaml] , és végezze el a következő frissítéseket:
* Cserélje le a *< REPLACE_ME_WITH_HOST_SUFFIX >* összes példányát a *traefik. MY_CUSTOM_DOMAIN* a tartományt a *MY_CUSTOM_DOMAINhoz*. 
* Cserélje le a *kubernetes.IO/ingress.Class: traefik-azds # dev Spaces-specifikus* with *kubernetes.IO/ingress.Class: traefik # Custom beáramló*. 

Az alábbi példa egy frissített `values.yaml` fájlt mutat be:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Mentse a módosításokat, és zárjuk be a fájlt.

Hozza létre a *fejlesztői* területet a minta alkalmazással `azds space select`használatával.

```console
azds space select -n dev -y
```

Telepítse a minta alkalmazást `helm install`használatával.

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

A fenti példa telepíti a minta alkalmazást a *fejlesztői* névtérbe.

Jelenítse meg az URL-eket a minta alkalmazás `azds list-uris`használatával való eléréséhez.

```console
azds list-uris
```

Az alábbi kimenet a `azds list-uris`URL-címekből mutatja be a példákat.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Nyissa meg a *bikesharingweb* szolgáltatást a `azds list-uris` parancs nyilvános URL-címének megnyitásával. A fenti példában a *bikesharingweb* szolgáltatás nyilvános URL-címe `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

> [!NOTE]
> Ha a *bikesharingweb* szolgáltatás helyett hibaüzenet jelenik meg, ellenőrizze, hogy a *kubernetes.IO/ingress.Class* jegyzetét és a gazdagépet **is** frissítette-e a *Values. YAML* fájlban.

A `azds space select` parancs használatával hozzon létre egy gyermek területet a *fejlesztői* területen, és sorolja fel az URL-címeket a gyermek fejlesztői terület eléréséhez.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

Az alábbi kimenetben a `azds list-uris` URL-címei láthatók a *azureuser1* gyermek fejlesztői területének alkalmazásához.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Nyissa meg a *bikesharingweb* szolgáltatást a *azureuser1* gyermekének fejlesztői területén, és nyissa meg a nyilvános URL-címet a `azds list-uris` parancsban. A fenti példában a *azureuser1* gyermek fejlesztői terület *bikesharingweb* szolgáltatásának nyilvános URL-címe `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>A traefik bejövő forgalom-vezérlő konfigurálása HTTPS használatára

A [tanúsítvány-kezelő][cert-manager] segítségével automatizálhatja a TLS-tanúsítvány felügyeletét, amikor konfigurálja a traefik bejövő adatvezérlőt a https használatára. A *certmanager* -diagram telepítéséhez használja a `helm`.

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Hozzon létre egy `letsencrypt-clusterissuer.yaml` fájlt, és frissítse az e-mail mezőt az e-mail-címével.

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
            class: traefik
```

> [!NOTE]
> Teszteléshez egy [átmeneti kiszolgáló][letsencrypt-staging-issuer] is használható a *ClusterIssuer*.

`letsencrypt-clusterissuer.yaml`alkalmazásához használja a `kubectl`.

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Frissítse a traefik a HTTPS használatára a `helm`használatával.

```console
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Frissítse a [Values. YAML][values-yaml] , és adja meg a *CERT-Manager* és a https használatának részleteit. Az alábbi példa egy frissített `values.yaml` fájlt mutat be:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

A minta alkalmazás frissítése `helm`használatával:

```console
helm upgrade bikesharing . --namespace dev --atomic
```

Navigáljon az alkalmazáshoz a *dev/azureuser1* , és figyelje meg, hogy a rendszer átirányítja a https használatára. Azt is figyelje meg, hogy az oldal betöltődik, de a böngésző bizonyos hibákat jelez. A böngésző konzoljának megnyitásakor a hiba a HTTP-erőforrások betöltésére tett HTTPS-oldalra vonatkozik. Például:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

A hiba kijavításához frissítse a [BikeSharingWeb/azds. YAML][azds-yaml] -t a *traefik* használata a *kubernetes.IO/ingress.Class* és az egyéni tartomány számára a *$ (hostSuffix)* értékre. Például:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

Frissítse a [BikeSharingWeb/Package. JSON][package-json] fájlt az *URL-* csomagra vonatkozó függőséggel.

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Frissítse a *getApiHostAsync* metódust a [BikeSharingWeb/Pages/Helpers. js][helpers-js] fájlban a https használatára:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

A frissített BikeSharingWeb szolgáltatás futtatásához navigáljon a `BikeSharingWeb` könyvtárba, és használja a `azds up`.

```console
cd ../BikeSharingWeb/
azds up
```

Navigáljon az alkalmazáshoz a *dev/azureuser1* , és figyelje meg, hogy a rendszer a HTTPS-t hibák nélkül használja.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogy az Azure dev Spaces hogyan segíti az összetettebb alkalmazások fejlesztését több tárolóban, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést, ha a kód különböző verzióival vagy ágaival dolgozik a különböző helyeken.

> [!div class="nextstepaction"]
> [Csoportmunka az Azure fejlesztői Spaces szolgáltatásban][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml