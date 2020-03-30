---
title: Egyéni traefik inress vezérlő használata és https konfigurálása
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Megtudhatja, hogy miként konfigurálhatja az Azure Dev Spaces szolgáltatást egyéni traefik ingress vezérlő használatára, és hogyan konfigurálhatja a HTTPS protokollt a be- ésévárosi vezérlő használatával
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s
ms.openlocfilehash: fd11b3bbd3f90b75203084ff0753c1485d57a35b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155429"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Egyéni traefik inress vezérlő használata és https konfigurálása

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Dev Spaces egyéni traefik ingress vezérlő használatával. Ez a cikk azt is bemutatja, hogyan konfigurálhatja az egyéni be- ésresvezérlőt a HTTPS használatára.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes fiókot][azure-account-create].
* [Az Azure CLI telepítve van.][az-cli]
* [Az Azure Kubernetes-szolgáltatás (AKS) fürtje engedélyezve van az Azure Dev Spaces szolgáltatással.][qs-cli]
* [kubectl][kubectl] telepítve.
* [Helm 3 telepítve][helm-installed].
* [Egyéni tartomány][custom-domain] [DNS-zónával][dns-zone]. Ez a cikk feltételezi, hogy az egyéni tartomány és a DNS-zóna ugyanabban az erőforráscsoportban van, mint az AKS-fürt, de lehetőség van egyéni tartomány és DNS-zóna használatára egy másik erőforráscsoportban.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Egyéni traefik ingress vezérlő konfigurálása

Csatlakozzon a fürthöz [a kubectl][kubectl], a Kubernetes parancssori ügyfél használatával. Az [aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t azok használatára.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Adja hozzá a [hivatalos stabil Helm repository,][helm-stable-repo]amely tartalmazza a traefik ingress vezérlő Helm chart.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Hozzon létre egy Kubernetes névteret a traefik ingress vezérlőhöz, és telepítse azt a használatával. `helm`

> [!NOTE]
> Ha az AKS-fürtrése nincs engedélyezve az RBAC értéken, távolítsa el a *--set rbac.enabled=true* paramétert.

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> A fenti példa létrehoz egy nyilvános végpontot a be- és a be- és adékezelő vezérlő. Ha ehelyett privát végpontot kell használnia a be- és a be- és énemvezérlőhöz, adja hozzá a *--set service.annotations értéket." service\\\\.beta\\.kubernetes .io/azure-load-balancer-internal"=true* parameter to the helm *install* command.
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Ez a privát végpont elérhető a virtuális hálózaton belül, ahol az AKS-fürt telepítve van.

Szerezd meg az IP-címét a traefik ingress vezérlő szolgáltatás [segítségével kubectl kap.][kubectl-get]

```console
kubectl get svc -n traefik --watch
```

A minta kimenete a *traefik* névtérben lévő összes szolgáltatás IP-címét mutatja.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Adjon hozzá egy *A* rekordot a DNS-zónához a traefik szolgáltatás külső IP-címével az [network dns rekord-set a add-record][az-network-dns-record-set-a-add-record]használatával.

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

A fenti példa *ad* hozzá egy A rekordot a *MY_CUSTOM_DOMAIN* DNS-zónához.

Ebben a cikkben az [Azure Dev Spaces-ek kerékpármegosztásminta-alkalmazást](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) használja az Azure Dev Spaces használatával történő bemutatáshoz. Klónozza az alkalmazást a GitHubról, és navigáljon a könyvtárába:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Nyissa meg [a values.yaml fájlt,][values-yaml] és tegye a következő frissítéseket:
* Cserélje le *<REPLACE_ME_WITH_HOST_SUFFIX>* összes példányát *traefikre. MY_CUSTOM_DOMAIN* a tartományt *használja a MY_CUSTOM_DOMAIN.* 
* Cserélje ki *kubernetes.io/ingress.class: traefik-azds # Dev Spaces-specifikus* *kubernetes.io/ingress.class: traefik # Custom Ingress*. 

Az alábbiakban egy `values.yaml` példa egy frissített fájl:

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

Mentse a módosításokat, és zárja be a fájlt.

Hozza létre a *fejlesztési* területet `azds space select`a mintaalkalmazással a használatával.

```console
azds space select -n dev -y
```

Telepítse a mintaalkalmazást a használatával. `helm install`

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

A fenti példa telepíti a mintaalkalmazást a *fejlesztői* névtérbe.

A mintaalkalmazás eléréséhez a `azds list-uris`képernyőn megjelenő URL-címek megjelenítése a használatával.

```console
azds list-uris
```

Az alábbi kimenet a példa `azds list-uris`URL-címeit mutatja.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Keresse meg a *bikesharingweb* szolgáltatást a `azds list-uris` nyilvános URL megnyitásával a parancsból. A fenti példában a *bikesharingweb* szolgáltatás `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`nyilvános URL-címe .

> [!NOTE]
> Ha a *bikesharingweb* szolgáltatás helyett hibaoldalt lát, ellenőrizze, hogy **frissítette-e** a *kubernetes.io/ingress.class* kommentárt és a host-ot is a *values.yaml* fájlban.

A `azds space select` paranccsal hozzon létre egy gyermekterületet a *fejlesztési területen,* és sorolja fel az URL-címeket a gyermekfejlesztési terület eléréséhez.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

Az alábbi kimenet a példa `azds list-uris` URL-címeket jeleníti meg az *azureuser1* gyermekfejlesztési térben a mintaalkalmazás eléréséhez.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Keresse meg a *bikesharingweb* szolgáltatás az *azureuser1* gyermek fejlesztői `azds list-uris` tér megnyitásával a nyilvános URL-t a parancsból. A fenti példában az *azureuser1* gyermekfejlesztői térben a `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` *bikesharingweb* szolgáltatás nyilvános URL-címe a.

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Konfigurálja a traefik inress vezérlőt https használatára

A [cert-manager][cert-manager] segítségével automatizálhatja a TLS-tanúsítvány kezelését, amikor a traefik ingress vezérlőt HTTPS használatára konfigurálja. A `helm` *certmanager* diagram telepítéséhez használható.

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Hozzon `letsencrypt-clusterissuer.yaml` létre egy fájlt, és frissítse az e-mail mezőt az e-mail címével.

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
> A teszteléshez van egy [átmeneti kiszolgáló][letsencrypt-staging-issuer] is, amelyet a *ClusterIssuer*számára használhat.

Alkalmazásra `kubectl` `letsencrypt-clusterissuer.yaml`használható.

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Távolítsa el az előző *traefik* *ClusterRole* és *ClusterRoleBinding*tulajdonságot, majd frissítse a traefik-et a HTTPS használatára a használatával. `helm`

> [!NOTE]
> Ha az AKS-fürtrése nincs engedélyezve az RBAC értéken, távolítsa el a *--set rbac.enabled=true* paramétert.

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Szerezd meg a frissített IP-címét a traefik ingress vezérlő szolgáltatás [segítségével kubectl kap.][kubectl-get]

```console
kubectl get svc -n traefik --watch
```

A minta kimenete a *traefik* névtérben lévő összes szolgáltatás IP-címét mutatja.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

*Adjon* hozzá egy A rekordot a DNS-zónához a traefik szolgáltatás új külső IP-címével az [network dns rekord-set a add-record][az-network-dns-record-set-a-add-record] segítségével, és távolítsa el az előző *A* rekordot az az network [dns rekord-set a remove-record][az-network-dns-record-set-a-remove-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_NEW_EXTERNAL_IP

az network dns record-set a remove-record \
    --resource-group myResourceGroup \
    --zone-name  MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address PREVIOUS_EXTERNAL_IP
```

A fenti példa frissíti az *A* rekordot a *MY_CUSTOM_DOMAIN* DNS-zónában, hogy *PREVIOUS_EXTERNAL_IP.*

Frissítse [a values.yaml fájlt,][values-yaml] hogy tartalmazza a *cert-manager* és a HTTPS használatának részleteit. Az alábbiakban egy `values.yaml` példa egy frissített fájl:

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

Frissítse a mintaalkalmazást a következő használatával: `helm`

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Keresse meg a mintaalkalmazást a *fejlesztési/azureuser1* gyermektérben, és figyelje meg, hogy a rendszer átirányítja a HTTPS használatára.

> [!IMPORTANT]
> A DNS-módosítások végrehajtása és a mintaalkalmazás elérhetővé tétele 30 percet vagy többet is igénybe vehet.

Azt is észreveszi, hogy az oldal betöltődik, de a böngésző néhány hibát mutat. A böngészőkonzol megnyitása azt mutatja, hogy a hiba egy HTTP-erőforrásokat betöltő HTTPS-lapra vonatkozik. Példa:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

A hiba kijavításához frissítse [a BikeSharingWeb/azds.yaml fájlt,][azds-yaml] hogy *a traefik* et *használja kubernetes.io/ingress.class* és az egyéni tartományt *$(hostSuffix)* számára. Példa:

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

Frissítse [bikesharingweb/package.json][package-json] a függőség az *URL-csomagot.*

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Frissítse a *getApiHostAsync* metódust a [BikeSharingWeb/lib/helpers.js][helpers-js] fájlban https használatára:

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

Keresse meg `BikeSharingWeb` a `azds up` könyvtárat, és használja a frissített BikeSharingWeb szolgáltatás futtatásához.

```console
cd ../BikeSharingWeb/
azds up
```

Keresse meg a mintaalkalmazást a *fejlesztési/azureuser1* gyermektérben, és figyelje meg, hogy a rendszer átirányítja a HTTPS használatára hiba nélkül.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure Dev Spaces hogyan segít összetettebb alkalmazások fejlesztésében több tárolóközött, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést a kód különböző verzióival vagy ágaival való együttműködéssel különböző helyeken.

> [!div class="nextstepaction"]
> [Csapatfejlesztés az Azure Dev Spaces-ben][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-remove-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml