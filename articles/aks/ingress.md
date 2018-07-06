---
title: Konfigurálja a bejövő forgalom az Azure Kubernetes Service (AKS)-fürt
description: Telepítse és konfigurálja az NGINX bejövőforgalom-vezérlőt az Azure Kubernetes Service (AKS)-fürtben.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/25/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bd223e9eebac495d7336c618b831528505c30959
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857395"
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (AKS) a HTTPS-bejövő

Bejövőforgalom-vezérlőjéhez olyan szoftver, amely biztosítja a fordított proxy, konfigurálható forgalom-útválasztást és a TLS-lezárást biztosít Kubernetes-szolgáltatás. Kubernetes bejövő erőforrások segítségével konfigurálhatja a bejövő szabályok és útvonalak a Kubernetes-szolgáltatás. A bejövőforgalom-vezérlőt, és a bejövő szabályok használatával az egyetlen külső címet irányíthatja a forgalmat több szolgáltatást a Kubernetes-fürtben használható.

Ez a dokumentum végigvezeti a minta üzembe helyezése a [NGINX bejövőforgalom-vezérlőjéhez] [ nginx-ingress] Azure Kubernetes Service (AKS)-fürtben. Ezenkívül a [cert-kezelő] [ cert-manager] projekt automatikus létrehozásához és konfigurálásához használatos [hozzunk titkosítása] [ lets-encrypt] tanúsítványokat. Végül az AKS-fürtöt, amelyek mindegyike érhető el egyetlen címet keresztül több alkalmazások is futnak.

## <a name="install-an-ingress-controller"></a>Egy belépő controller telepítése

Helm használatával telepítheti az NGINX bejövőforgalom-vezérlőt. Tekintse meg az NGINX bejövőforgalom-vezérlőjéhez [dokumentáció] [ nginx-ingress] telepítés részletes információt.

Ebben a példában a tartományvezérlőre telepíti a `kube-system` névteret, a választott névtérhez módosítható. Ha az AKS-fürt nem RBAC engedélyezve, vegye fel `--set rbac.create=false` a parancshoz. További információkért lásd: a [nginx-belépő diagram][nginx-ingress].

```bash
helm install stable/nginx-ingress --namespace kube-system
```

A telepítés során az Azure nyilvános IP-cím a bejövőforgalom-vezérlőjéhez jön létre. Nyilvános IP-címének lekéréséhez használja a kubectl get service parancsot. Eltarthat egy kis ideig kell a szolgáltatáshoz rendelt IP-cím.

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Nincs bejövő szabály létrejött, miután felkereste a nyilvános IP-cím, mert Ön az NGINX bejövő tartományvezérlők alapértelmezett 404-es oldal legyenek irányítva.

![Alapértelmezett NGINX-háttérrendszer](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>Konfigurálja a DNS-név

Mivel a HTTPS-tanúsítványok használata esetén kell a bejövő forgalom vezérlők IP-cím FQDN nevének beállítása. Ebben a példában az Azure teljes Tartománynevet az Azure CLI használatával jön létre. Frissítse a parancsfájlt a bejövőforgalom-vezérlőt, és a nevét, amely a teljes Tartománynevet a használni kívánt IP-címét.

```bash
#!/bin/bash

# Public IP address
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with dns name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

A bejövőforgalom-vezérlőjéhez most az FQDN-n keresztül elérhető kell lennie.

## <a name="install-cert-manager"></a>Tanúsítvány-kezelőjének telepítése

Az NGINX bejövőforgalom-vezérlőjéhez támogatja a TLS megszüntetése. Többféleképpen is lekérésére és tanúsítványok konfigurálása HTTPS használatára, amíg ez a dokumentum használatát mutatja be [cert-kezelő][cert-manager], amely biztosítja az automatikus [lehetővé teszi, hogy titkosítani] [ lets-encrypt] tanúsítvány létrehozása és kezelése funkciót.

A tanúsítvány-kezelő tartományvezérlő telepítéséhez használja a következő parancs a Helm install.

```bash
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-prod --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Ha a fürt nem RBAC engedélyezve van, használja ezt a parancsot.

```bash
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-prod \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

A tanúsítvány-kezelő konfigurációjának további információkért lásd: a [cert-kezelő projekt][cert-manager].

## <a name="create-ca-cluster-issuer"></a>Hitelesítésszolgáltató-kiállítói fürt létrehozása

Mielőtt adhatók ki tanúsítványok, tanúsítvány-manager csak egy [kibocsátó] [ cert-manager-issuer] vagy [ClusterIssuer] [ cert-manager-cluster-issuer] erőforrás. Az erőforrások megegyeznek a funkciót azonban `Issuer` működik egy egységes névtérben ahol `ClusterIssuer` összes névtér működik. További információkért lásd: a [cert-kezelő kibocsátó] [ cert-manager-issuer] dokumentációját.

Hozzon létre egy fürtöt kibocsátó használatával a következő jegyzékfájlt. Az e-mail-cím frissítése egy érvényes címet a szervezet.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-prod
    http01: {}
```

## <a name="create-certificate-object"></a>Tanúsítvány-objektum létrehozása

Ezután egy tanúsítvány erőforrást kell létrehozni. A tanúsítvány erőforrás határozza meg a kívánt X.509-tanúsítvány. További információkért tekintse meg, [cert-kezelő tanúsítványok][cert-manager-certificates].

A tanúsítvány-erőforrás létrehozása a következő jegyzékfájl.

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
    name: letsencrypt-prod
    kind: ClusterIssuer
```

## <a name="run-application"></a>Alkalmazás futtatása

Ezen a ponton bejövőforgalom-vezérlőt, és a egy tanúsítvány-kezelési megoldás vannak konfigurálva. Most futtassa az AKS-fürt néhány alkalmazásokat.

Ebben a példában a Helm segítségével egy egyszerű hello world alkalmazás több példányának futtatása.

Mielőtt futtatná az alkalmazást, adja hozzá a fejlesztői rendszeren az Azure-minták Helm-adattárhoz.

```bash
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Az AKS hello world diagram futtassa a következő paranccsal:

```bash
helm install azure-samples/aks-helloworld
```

Egy második példányt a hello world alkalmazás telepítése.

A második példány új cím megadásához, hogy a két alkalmazás vizuálisan elkülönülnek. Ön emellett adjon meg egy egyedi nevet. Ezeket a konfigurációkat az alábbi parancsban látható.

```bash
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Bejövő útvonal létrehozása

Mindkét alkalmazás van most a Kubernetes-fürtön futó azonban konfigurált egy szolgáltatás típusa az `ClusterIP`. Mint ilyen az alkalmazások nem érhetők el az internetről. Annak érdekében, hogy elérhetővé őket, hozzon létre egy Kubernetes bejövő erőforrást. A bejövő forgalom erőforrás konfigurálja a szabályokat, amelyek a két alkalmazás egyik irányíthatja a forgalmat.

Hozzon létre egy fájlt `hello-world-ingress.yaml` másolja be a következő yaml-kódot.

Jegyezze fel, hogy a forgalom a címre `https://demo-aks-ingress.eastus.cloudapp.azure.com/` irányítja a rendszer a szolgáltatás nevű `aks-helloworld`. A cím forgalmát `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` irányítja a rendszer a `ingress-demo` szolgáltatás.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
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

A bejövő forgalom erőforrás létrehozása a `kubectl apply` parancsot.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>A konfiguráció teszteléséhez a bejövő forgalom

Keresse meg a teljes Tartománynevét, a Kubernetes bejövőforgalom-vezérlőt, a hello world alkalmazás kell megjelennie.

![Egy példa az alkalmazások](media/ingress/app-one.png)

Most már teljes Tartománynevét a bejövőforgalom-vezérlőt, keresse meg a `/hello-world-two` elérési utat, a hello world alkalmazás és az egyéni cím kell megjelennie.

![Példa az alkalmazások két](media/ingress/app-two.png)

Szintén figyelje meg, hogy a kapcsolat titkosítva van, és nézzük titkosítása által kibocsátott tanúsítványt használja, hogy.

![Lehetővé teszi, hogy a tanúsítvány titkosítása](media/ingress/certificate.png)

## <a name="next-steps"></a>További lépések

További információ az ebben a dokumentumban bemutatott szoftver.

- [Helm CLI][helm-cli]
- [Az NGINX bejövőforgalom-vezérlőt][nginx-ingress]
- [tanúsítvány-kezelő][cert-manager]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
