---
title: Hozzon létre egy HTTP bejövőforgalom-vezérlőjéhez statikus IP-címet az Azure Kubernetes Service (AKS)
description: Ismerje meg, telepítése és a egy nginx-et bejövőforgalom-vezérlőt konfigurálja egy statikus nyilvános IP-cím az Azure Kubernetes Service (AKS)-fürtben.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 8b05a770c58e1d796e2658fe5a3bd5aa1808825c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54469991"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Hozzon létre egy bejövőforgalom-vezérlőt egy statikus nyilvános IP-cím az Azure Kubernetes Service (AKS)

Bejövőforgalom-vezérlőjéhez olyan szoftver, amely biztosítja a fordított proxy, konfigurálható forgalom-útválasztást és a TLS-lezárást biztosít Kubernetes-szolgáltatás. Kubernetes bejövő erőforrások segítségével konfigurálhatja a bejövő szabályok és útvonalak a Kubernetes-szolgáltatás. A bejövőforgalom-vezérlőt, és a bejövő szabályok használatával az egyetlen IP-cím irányíthatja a forgalmat több szolgáltatást a Kubernetes-fürtben használható.

Ez a cikk bemutatja, hogyan helyezhet üzembe a [NGINX bejövőforgalom-vezérlőjéhez] [ nginx-ingress] Azure Kubernetes Service (AKS)-fürtben. A bejövőforgalom-vezérlőt egy statikus nyilvános IP-címmel van konfigurálva. A [cert-kezelő] [ cert-manager] projekt automatikus létrehozásához és konfigurálásához használatos [hozzunk titkosítása] [ lets-encrypt] tanúsítványokat. Végül a két alkalmazás az AKS-fürtöt, amelyek mindegyike érhető el egyetlen IP-címen keresztül a futnak.

További lehetőségek:

- [Hozzon létre egy alapszintű bejövőforgalom-vezérlőjéhez külső hálózatok közötti kapcsolatokkal][aks-ingress-basic]
- [A HTTP-kérelem útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Hozzon létre egy saját TLS-tanúsítványokat használ a bejövőforgalom-vezérlőt][aks-ingress-own-tls]
- [Hozzon létre egy bejövőforgalom-vezérlőt használó hozzunk titkosítása automatikus létrehozására szolgáló dinamikus nyilvános IP-cím a TLS-tanúsítványok][aks-ingress-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk a Helm használatával az NGINX bejövőforgalom-vezérlőt, a tanúsítvány-kezelő és a egy mintául szolgáló webalkalmazás telepítése. Szüksége lesz a Helm belül az AKS-fürt inicializálva, és a tiller valóban szolgáltatásfiók használatával. Győződjön meg arról, hogy a Helm legújabb kiadását használja. Frissítési utasításokért lásd: a [Helm telepítése docs][helm-install]. Konfigurálása és a Helm használatával további információkért lásd: [telepíthet alkalmazásokat a Helm használatával az Azure Kubernetes Service (AKS)][use-helm].

Ez a cikk is szükséges, hogy futnak-e az Azure CLI 2.0.41 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Hozzon létre egy bejövőforgalom-vezérlőt

Alapértelmezés szerint egy nginx-et bejövőforgalom-vezérlőjéhez létrejön egy új nyilvános IP-cím hozzárendelése. A nyilvános IP-cím a bejövőforgalom-vezérlőjéhez élettartamát a csak statikus, és megszakad, ha a vezérlő törlődik, és újból létrehozza. Általános konfigurációs követelmény, hogy az NGINX bejövőforgalom-vezérlőt egy meglévő statikus nyilvános IP-címet. Statikus nyilvános IP-cím marad, abban az esetben, ha a bejövőforgalom-vezérlőjéhez törlődik. Ez a megközelítés lehetővé teszi, hogy az alkalmazások életciklusa során következetesen meglévő DNS-rekordok és a hálózati konfigurációt.

Hozzon létre egy statikus nyilvános IP-cím van szüksége, ha először kérje le az erőforráscsoport neve, az AKS-fürtöt a [az aks show] [ az-aks-show] parancsot:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Ezután hozzon létre egy nyilvános IP-címet a *statikus* kiosztási módszer használatával a [az network public-ip létrehozása] [ az-network-public-ip-create] parancsot. Az alábbi példa létrehoz egy nyilvános IP-címet *myAKSPublicIP* az aks-ben a fürt az előző lépésben lekért erőforráscsoportot:

```azurecli
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Már üzembe helyezheti a *nginx-belépő* Helm-diagramot. Adja hozzá a `--set controller.service.loadBalancerIP` paramétert, és adja meg saját nyilvános IP-címet az előző lépésben létrehozott. Hozzáadott redundancia céljából két replika az NGINX bejövő vezérlők telepítik a `--set controller.replicaCount` paraméter. Teljes körűen kihasználhatják a bejövőforgalom-vezérlőjéhez replikáin fut, ellenőrizze, egynél több csomópont szerepel az AKS-fürt.

> [!TIP]
> Az alábbi példák a bejövőforgalom-vezérlőt, és a tanúsítványok telepítése a `kube-system` névtér. Megadhat egy másik névtér a saját környezetben, ha szükséges. Ezenkívül ha az AKS-fürt nem RBAC engedélyezve, vegye fel `--set rbac.create=false` parancsok.

```console
helm install stable/nginx-ingress \
    --namespace kube-system \
    --set controller.service.loadBalancerIP="40.121.63.72"  \
    --set controller.replicaCount=2
```

A terheléselosztó Kubernetes szolgáltatás az NGINX bejövőforgalom-vezérlőjéhez hoz létre, ha a statikus IP-cím van hozzárendelve, az alábbi példa kimenetében látható módon:

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
dinky-panda-nginx-ingress-controller        LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
dinky-panda-nginx-ingress-default-backend   ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Bejövő szabályok már hozott létre, így az NGINX bejövőforgalom-vezérlőjéhez tartozó alapértelmezett 404-es lap is megjelenik, miután felkereste a nyilvános IP-cím. Bejövő szabályok a következő lépések vannak konfigurálva.

## <a name="configure-a-dns-name"></a>Konfigurálja a DNS-név

A HTTPS-tanúsítványok megfelelően működjön konfigurálja a bejövő forgalom vezérlő IP-cím teljes Tartománynevet. Frissítse a bejövőforgalom-vezérlőt, és a egy egyedi nevet, amely a teljes Tartománynevet használni kívánt IP-címét az alábbi parancsfájlt:

```console
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

A bejövőforgalom-vezérlőjéhez most már a teljes tartománynév keresztül érhető el.

## <a name="install-cert-manager"></a>Tanúsítvány-kezelőjének telepítése

Az NGINX bejövőforgalom-vezérlőjéhez támogatja a TLS megszüntetése. Számos módon lekérésére és tanúsítványok konfigurálása HTTPS használatára. Használatát mutatja be ez a cikk [cert-kezelő][cert-manager], amely biztosítja az automatikus [titkosítása lehetővé teszi, hogy] [ lets-encrypt] tanúsítvány generálása és felügyeleti funkciókat.

> [!NOTE]
> Ez a cikk a `staging` környezetet hozzunk titkosítása. Használja az éles környezetekben, `letsencrypt-prod` és `https://acme-v02.api.letsencrypt.org/directory` az erőforrás-definíciókban, és a Helm-diagram telepítésekor.

Az RBAC-t fürtben a tanúsítvány-kezelő tartományvezérlő telepítéséhez használja a következő `helm install` parancsot. Újra, ha szükséges, módosítsa `--namespace` valami nem *kube rendszer*:

```console
helm install stable/cert-manager \
  --namespace kube-system \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Ha a fürt nem engedélyezett az RBAC, ehelyett a következő paranccsal:

```console
helm install stable/cert-manager \
  --namespace kube-system \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

A tanúsítvány-kezelő konfigurációjának további információkért lásd: a [cert-kezelő projekt][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Hozzon létre egy fürtöt hitelesítésszolgáltató-kiállítói

Mielőtt adhatók ki tanúsítványok, tanúsítvány-manager csak egy [kibocsátó] [ cert-manager-issuer] vagy [ClusterIssuer] [ cert-manager-cluster-issuer] erőforrás. Ezek a Kubernetes-erőforrást megegyeznek a funkciót, azonban `Issuer` működik egy egységes névtérben, és `ClusterIssuer` összes névtér működik. További információkért lásd: a [cert-kezelő kibocsátó] [ cert-manager-issuer] dokumentációját.

Például hozzon létre egy fürtöt az issuer `cluster-issuer.yaml`, a következő példa jegyzék használatával. Frissítse az e-mail-cím az érvényes cím a szervezet:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
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

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="create-a-certificate-object"></a>Tanúsítvány-objektum létrehozása

Ezután egy tanúsítvány erőforrást kell létrehozni. A tanúsítvány erőforrás határozza meg a kívánt X.509-tanúsítvány. További információkért lásd: [cert-kezelő tanúsítványok][cert-manager-certificates].

Hozzon létre például a tanúsítvány erőforrás `certificates.yaml`, az a következő példa jegyzékfájlt. Frissítés a *dnsNames* és *tartományok* az előző lépésben létrehozott DNS-nevével. Ha egy belső bejövőforgalom-vezérlőt használ, adja meg a szolgáltatás belső DNS-nevét.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
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

certificate.certmanager.k8s.io/tls-secret created
```

Győződjön meg arról, hogy a tanúsítvány sikeresen létrejött-e, használja a `kubectl describe certificate tls-secret` parancsot.

Ha a tanúsítványt, a következőhöz hasonló kimenetet fog látni:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

## <a name="run-demo-applications"></a>Bemutató alkalmazások futtatása

Bejövőforgalom-vezérlőt, és a egy tanúsítvány-kezelési megoldás konfigurálva. Most tegyük a futtatási két bemutató az AKS-fürt az alkalmazásokat. Ebben a példában a Helm szolgál egy egyszerű "Hello world" alkalmazás két példány üzembe helyezéséhez.

A minta Helm-diagramok telepítése előtt vegye fel az Azure-minták tárhelyet a Helm-környezetben a következő:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Az első bemutató alkalmazás létrehozása egy Helm-diagramot a következő paranccsal:

```console
helm install azure-samples/aks-helloworld
```

Egy második példányt a bemutató alkalmazás telepítése. A második példány, adja meg az új címet, hogy a két alkalmazás vizuálisan elkülönülnek. Is adjon meg egy egyedi szolgáltatásnevet:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Bejövő útvonal létrehozása

Mindkét alkalmazás most már futtat egy Kubernetes-fürtöt, azonban egy szolgáltatással típusú konfigurálják `ClusterIP`. Mint ilyen az alkalmazás nem az interneten. Ahhoz, hogy azok nyilvánosan elérhető, hozzon létre egy Kubernetes bejövő erőforrást. A bejövő forgalom erőforrás konfigurálja a szabályokat, amelyek a két alkalmazás egyik irányíthatja a forgalmat.

A következő példában a forgalmat a címre `https://demo-aks-ingress.eastus.cloudapp.azure.com/` irányítja a rendszer a szolgáltatás nevű `aks-helloworld`. A cím forgalmát `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` irányítja a rendszer a `ingress-demo` szolgáltatás. Frissítés a *gazdagépek* és *gazdagép* az előző lépésben létrehozott DNS-nevével.

Hozzon létre egy fájlt `hello-world-ingress.yaml` , és másolja az alábbi példában YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Létrehozhatja a bejövő forgalom erőforrás a `kubectl apply -f hello-world-ingress.yaml` parancsot.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>A konfiguráció teszteléséhez a bejövő forgalom

Például a Kubernetes bejövő tartományvezérlő teljes Tartománynevét egy webböngészőben nyissa meg *https://demo-aks-ingress.eastus.cloudapp.azure.com*.

Mivel ezek a példák a `letsencrypt-staging`, a böngésző által kiállított SSL-tanúsítvány nem megbízható. Fogadja el a figyelmeztetést, az alkalmazás továbbra is. Ez a tanúsítvány adatait jeleníti meg *hamis LE köztes X1* tanúsítványt most titkosítása. Ez a tanúsítvány hamis azt jelzi, hogy `cert-manager` képesek megfelelően feldolgozni a kérelmet, és a egy tanúsítványt kapott a szolgáltató:

![Most a átmeneti tanúsítvány titkosítása](media/ingress/staging-certificate.png)

Ha módosítja a hozzunk titkosítására használandó `prod` helyett `staging`, megbízható által kibocsátott tanúsítványt most titkosítására szolgál, az alábbi példában látható módon:

![Most a tanúsítvány titkosítása](media/ingress/certificate.png)

A webböngésző a bemutató alkalmazás látható:

![Egy példa az alkalmazások](media/ingress/app-one.png)

Most adja hozzá a */hello-world-two* elérési útját a teljes Tartománynevét, például *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. A második bemutató alkalmazás és az egyéni cím jelenik meg:

![Példa az alkalmazások két](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a cikk Helm használja a bejövő forgalom összetevők, a tanúsítványok és a mintaalkalmazások telepítésére. Amikor telepít egy Helm-diagram, egy Kubernetes-erőforrások száma jönnek létre. Ezeket az erőforrásokat podok, központi telepítések és szolgáltatásokat tartalmazza. Karbantartása, először távolítsa el a tanúsítvány erőforrások:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Most már listázása a Helm-kiadások, az a `helm list` parancsot. Keresse meg a diagramok nevű *nginx-bejövő*, *cert-kezelő*, és *aks-helloworld*, ahogy az alábbi példa kimenetében látható:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
waxen-hamster           1           Tue Oct 16 17:44:28 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
alliterating-peacock    1           Tue Oct 16 18:03:11 2018    DEPLOYED    cert-manager-v0.3.4     v0.3.2      kube-system
mollified-armadillo     1           Tue Oct 16 18:04:53 2018    DEPLOYED    aks-helloworld-0.1.0                default
wondering-clam          1           Tue Oct 16 18:04:56 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

A kiadások, és törölje a `helm delete` parancsot. Az alábbi példában az NGINX bejövő üzembe helyezés, a Tanúsítványkezelő és a két minta AKS hello world alkalmazás törlése.

```
$ helm delete waxen-hamster alliterating-peacock mollified-armadillo wondering-clam

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Ezután távolítsa el az AKS hello world alkalmazás a Helm-adattárat:

```console
helm repo remove azure-samples
```

A bejövő útvonal, amely átirányítja a forgalmat a mintaalkalmazások eltávolítása:

```console
kubectl delete -f hello-world-ingress.yaml
```

Végezetül távolítsa el a bejövőforgalom-vezérlőjéhez létrehozott statikus nyilvános IP-címet. Adja meg a *MC_* fürt erőforráscsoport neve például kapott ebben a cikkben, az első lépésben *MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>További lépések

Ez a cikk tartalmaz néhány külső összetevők az aks-ben. Ezek az összetevők kapcsolatos további információkért tekintse meg a következő projekt oldalakat:

- [Helm CLI][helm-cli]
- [Az NGINX bejövőforgalom-vezérlőt][nginx-ingress]
- [cert-manager][cert-manager]

További lehetőségek:

- [Hozzon létre egy alapszintű bejövőforgalom-vezérlőjéhez külső hálózatok közötti kapcsolatokkal][aks-ingress-basic]
- [A HTTP-kérelem útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Hozzon létre egy bejövőforgalom-vezérlőt, amely egy belső, saját hálózat és IP-cím][aks-ingress-internal]
- [Hozzon létre egy saját TLS-tanúsítványokat használ a bejövőforgalom-vezérlőt][aks-ingress-own-tls]
- [Hozzon létre egy bejövőforgalom-vezérlőjéhez dinamikus nyilvános IP-cím, és nézzük titkosítása automatikusan létrehozni a TLS-tanúsítványok konfigurálása][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
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
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
