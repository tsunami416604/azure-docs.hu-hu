---
title: Egyéni bejövő traefik-vezérlő használata és HTTPS konfigurálása
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Ismerje meg, hogyan konfigurálhatja az Azure dev Spaces-t egyéni traefik bemenő vezérlő használatára, és hogyan konfigurálhatja a HTTPS-t az adott bejövő vezérlő használatával
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: d19dc409f4d57a114b5937e6ce3718315e550a08
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763587"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Egyéni bejövő traefik-vezérlő használata és HTTPS konfigurálása

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure dev Spaces-t egyéni traefik beáramlási vezérlő használatára. A cikk azt is bemutatja, hogyan konfigurálhatja az egyéni bejövő vezérlőt a HTTPS használatára.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes fiókot][azure-account-create].
* Az [Azure CLI telepítve van][az-cli].
* [Azure Kubernetes szolgáltatás (ak) fürt az Azure dev Spaces-szel engedélyezve] [QS-CLI].
* a [kubectl][kubectl] telepítve van.
* A [Helm 3 telepítve van][helm-installed].
* Egy [DNS-zónával][dns-zone]rendelkező [egyéni tartomány][custom-domain] . Ez a cikk azt feltételezi, hogy az egyéni tartomány és a DNS-zóna ugyanabban az erőforráscsoporthoz van, mint az AK-fürt, de az egyéni tartományt és a DNS-zónát egy másik erőforráscsoporthoz is lehet használni.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Egyéni bejövő traefik-vezérlő konfigurálása

Kapcsolódjon a fürthöz a [kubectl][kubectl]és a Kubernetes parancssori ügyfél használatával. Az [aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Adja hozzá a [hivatalos stabil Helm-tárházat][helm-stable-repo], amely tartalmazza a traefik beáramló vezérlő Helm diagramját.

```console
helm repo add stable https://charts.helm.sh/stable
```

Hozzon létre egy Kubernetes-névteret a traefik beáramló vezérlőhöz, és telepítse azt a használatával `helm` .

> [!NOTE]
> Ha az AK-fürthöz nincs engedélyezve a Kubernetes RBAC, távolítsa el a *--set RBAC. enabled = True* paramétert.

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> A fenti példa egy nyilvános végpontot hoz létre a bejövő vezérlőhöz. Ha ehelyett privát végpontot kell használnia a bejövő adatvezérlőhöz, adja hozzá a *--set Service. Megjegyzések kifejezést. " Service \\ . Beta \\ . kubernetes \\ . IO/Azure-Load-Balancer-belső "= true* paraméter a *Helm install* parancshoz.
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

```azurecli
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
* Cserélje le a *<REPLACE_ME_WITH_HOST_SUFFIX>* összes példányát a *traefik. MY_CUSTOM_DOMAIN* a tartományt a *MY_CUSTOM_DOMAINhoz*. 
* Cserélje le a *kubernetes.IO/ingress.Class: traefik-azds # dev Spaces-specifikus* with *kubernetes.IO/ingress.Class: traefik # Custom beáramló*. 

Az alábbi példa egy frissített fájlt mutat be `values.yaml` :

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

Hozza létre a *fejlesztői* területet a minta alkalmazással a használatával `azds space select` .

```console
azds space select -n dev -y
```

Telepítse a minta alkalmazást a használatával `helm install` .

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

A fenti példa telepíti a minta alkalmazást a *fejlesztői* névtérbe.

Jelenítse meg az URL-címeket, amelyekkel elérheti a minta alkalmazást a használatával `azds list-uris` .

```console
azds list-uris
```

Az alábbi kimenet a példákban szereplő URL-címeket mutatja `azds list-uris` .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

A parancsból nyissa meg a nyilvános URL-címet a *bikesharingweb* szolgáltatáshoz `azds list-uris` . A fenti példában a *bikesharingweb* szolgáltatás nyilvános URL-címe a következő: `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` .

> [!NOTE]
> Ha a *bikesharingweb* szolgáltatás helyett hibaüzenet jelenik meg, ellenőrizze, hogy a *kubernetes.IO/ingress.Class* jegyzetét és a gazdagépet **is** frissítette-e a *Values. YAML* fájlban.

A `azds space select` parancs használatával hozzon létre egy gyermek területet a *dev* -ben, és sorolja fel az URL-címeket a gyermek fejlesztői terület eléréséhez.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

Az alábbi kimenet a példaként szolgáló URL-címeket mutatja be a `azds list-uris` *azureuser1* gyermek-fejlesztési térben található minta alkalmazás eléréséhez.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Nyissa meg a *bikesharingweb* szolgáltatást a *azureuser1* gyermekének fejlesztői területén, és nyissa meg a paranccsal a nyilvános URL-címet `azds list-uris` . A fenti példában a *bikesharingweb* szolgáltatás nyilvános URL-címe a *azureuser1* gyermekének fejlesztői területén `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` .

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>A traefik bejövő forgalom-vezérlő konfigurálása HTTPS használatára

A [tanúsítvány-kezelő][cert-manager] segítségével automatizálhatja a TLS-tanúsítvány felügyeletét, amikor konfigurálja a traefik bejövő adatvezérlőt a https használatára. `helm`A paranccsal telepítheti a *certmanager* -diagramot.

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

`kubectl`Az alkalmazással használható `letsencrypt-clusterissuer.yaml` .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Távolítsa el az előző *traefik* *ClusterRole* és *ClusterRoleBinding*, majd frissítse a traefik a https használatára a használatával `helm` .

> [!NOTE]
> Ha az AK-fürthöz nincs engedélyezve a Kubernetes RBAC, távolítsa el a *--set RBAC. enabled = True* paramétert.

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Szerezze be a traefik beáramló vezérlő szolgáltatás frissített IP-címét a [kubectl Get][kubectl-get]használatával.

```console
kubectl get svc -n traefik --watch
```

A minta kimenet a *traefik* -névtérben lévő összes szolgáltatás IP-címeit jeleníti meg.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Vegyen fel *egy rekordot a* DNS-zónába a traefik szolgáltatás új külső IP-címével az az [Network DNS Record-set a Record-Record][az-network-dns-record-set-a-add-record] paranccsal, és távolítsa el *az előző rekordot* az [az Network DNS Record-set a Remove-Record][az-network-dns-record-set-a-remove-record]paranccsal.

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

A fenti példa frissíti a *MY_CUSTOM_DOMAIN* DNS-zónában *lévő rekordot a* *PREVIOUS_EXTERNAL_IP* használatára.

Frissítse a [Values. YAML][values-yaml] , és adja meg a *CERT-Manager* és a https használatának részleteit. Az alábbi példa egy frissített fájlt mutat be `values.yaml` :

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

A minta alkalmazás frissítése a használatával `helm` :

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Navigáljon az alkalmazáshoz a *dev/azureuser1* , és figyelje meg, hogy a rendszer átirányítja a https használatára.

> [!IMPORTANT]
> Akár 30 percet is igénybe vehet, amíg a DNS-módosítások befejeződik, és a minta alkalmazása elérhetővé válik.

Azt is figyelje meg, hogy az oldal betöltődik, de a böngésző bizonyos hibákat jelez. A böngésző konzoljának megnyitásakor a hiba a HTTP-erőforrások betöltésére tett HTTPS-oldalra vonatkozik. Például:

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

A [BikeSharingWeb/package.js][package-json] frissítése az *URL-* csomagra vonatkozó függőséggel.

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Frissítse a *getApiHostAsync* metódust a [BikeSharingWeb/lib/helpers.jsban][helpers-js] a https használatára:

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

Navigáljon a `BikeSharingWeb` címtárhoz, és a használatával `azds up` futtassa a frissített BikeSharingWeb szolgáltatást.

```console
cd ../BikeSharingWeb/
azds up
```

Navigáljon az alkalmazáshoz a *dev/azureuser1* , és figyelje meg, hogy a rendszer a HTTPS-t hibák nélkül használja.

## <a name="next-steps"></a>További lépések

További információ az Azure dev Spaces működéséről.

> [!div class="nextstepaction"]
> [Az Azure Dev Spaces működése](../how-dev-spaces-works.md)


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-remove-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
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
