---
title: A saját TLS-tanúsítványokat használnak a bejövő forgalom az Azure Kubernetes Service (AKS)-fürt
description: Ismerje meg, hogyan telepítse és konfigurálja az NGINX bejövőforgalom-vezérlőt, amely a saját tanúsítványokat használ az Azure Kubernetes Service (AKS)-fürtben.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/23/2018
ms.author: iainfou
ms.openlocfilehash: 4e3f2f33cfffeacbcbeccc4f17f55b7d0e1a985c
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50098027"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Hozzon létre egy HTTPS bejövőforgalom-vezérlőt, és a saját TLS-tanúsítványok használata az Azure Kubernetes Service (AKS)

Bejövőforgalom-vezérlőjéhez olyan szoftver, amely biztosítja a fordított proxy, konfigurálható forgalom-útválasztást és a TLS-lezárást biztosít Kubernetes-szolgáltatás. Kubernetes bejövő erőforrások segítségével konfigurálhatja a bejövő szabályok és útvonalak a Kubernetes-szolgáltatás. A bejövőforgalom-vezérlőt, és a bejövő szabályok használatával az egyetlen IP-cím irányíthatja a forgalmat több szolgáltatást a Kubernetes-fürtben használható.

Ez a cikk bemutatja, hogyan helyezhet üzembe a [NGINX bejövőforgalom-vezérlőjéhez] [ nginx-ingress] Azure Kubernetes Service (AKS)-fürtben. A saját tanúsítványok előállításához, és hozzon létre egy Kubernetes titkos használatra a bejövő forgalom irányítása. Végül a két alkalmazás az AKS-fürtöt, amelyek mindegyike érhető el egyetlen IP-címen keresztül a futnak.

További lehetőségek:

- [Hozzon létre egy alapszintű bejövőforgalom-vezérlőjéhez külső hálózatok közötti kapcsolatokkal][aks-ingress-basic]
- [A HTTP-kérelem útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Hozzon létre egy bejövőforgalom-vezérlőt, amely egy belső, saját hálózat és IP-cím][aks-ingress-internal]
- Hozzon létre egy bejövőforgalom-vezérlőt használó hozzunk titkosítása automatikusan létrehozni a TLS-tanúsítványok [dinamikus nyilvános IP-címmel rendelkező] [ aks-ingress-tls] vagy [egy statikus nyilvános IP-címmel][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk az NGINX bejövőforgalom-vezérlőt, és a egy mintául szolgáló webalkalmazás telepítése Helm használ. Szüksége lesz a Helm belül az AKS-fürt inicializálva, és a tiller valóban szolgáltatásfiók használatával. Győződjön meg arról, hogy a Helm legújabb kiadását használja. Frissítési utasításokért lásd: a [Helm telepítése docs][helm-install]. Konfigurálása és a Helm használatával további információkért lásd: [telepíthet alkalmazásokat a Helm használatával az Azure Kubernetes Service (AKS)][use-helm].

Ez a cikk is szükséges, hogy futnak-e az Azure CLI 2.0.41 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Hozzon létre egy bejövőforgalom-vezérlőt

A bejövőforgalom-vezérlőjéhez létrehozásához használja `Helm` telepítéséhez *nginx-belépő*. Hozzáadott redundancia céljából két replika az NGINX bejövő vezérlők telepítik a `--set controller.replicaCount` paraméter. Teljes körűen kihasználhatják a bejövőforgalom-vezérlőjéhez replikáin fut, ellenőrizze, egynél több csomópont szerepel az AKS-fürt.

> [!TIP]
> A következő példa telepíti a bejövőforgalom-vezérlőt az `kube-system` névtér. Megadhat egy másik névtér a saját környezetben, ha szükséges. Ha az AKS-fürt nem RBAC engedélyezve, vegye fel `--set rbac.create=false` parancsok.

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2
```

A telepítés során az Azure nyilvános IP-cím a bejövőforgalom-vezérlőjéhez jön létre. A nyilvános IP-cím statikus-élettartamát a bejövőforgalom-vezérlőjéhez tartozó. Ha törli a bejövőforgalom-vezérlőt, a nyilvános IP-cím hozzárendelése elvész. Ezután hozzon létre egy további bejövőforgalom-vezérlőt, ha egy új nyilvános IP-cím van hozzárendelve. Ha szeretné megőrizni a nyilvános IP-cím használatát, akkor ehelyett [hozzon létre egy statikus nyilvános IP-cím bejövőforgalom-vezérlőjéhez][aks-ingress-static-tls].

Nyilvános IP-címének lekéréséhez használja a `kubectl get service` parancsot. Az IP-cím hozzá kell rendelni a szolgáltatás pár percet vesz igénybe.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Jegyezze fel a nyilvános IP-cím, az utolsó lépésben a telepítés tesztelésére használt.

Bejövő szabályok még hozták létre. Keresse meg a nyilvános IP-címre, ha az NGINX bejövőforgalom-vezérlőjéhez tartozó alapértelmezett 404-es oldal jelenik meg.

## <a name="generate-tls-certificates"></a>A TLS-tanúsítványok létrehozása

Ebben a cikkben hozzunk létre egy önaláírt tanúsítványt az `openssl`. Éles környezetben keresztül egy szolgáltató vagy a saját hitelesítésszolgáltató (CA) egy megbízható és aláírt tanúsítványt igényeljen. A következő lépésben létrehozhat egy Kubernetes *titkos* a TLS-tanúsítvány és OpenSSL által előállított titkos kulcsot.

Az alábbi példa létrehoz egy X 509 tanúsítvány nevű 365 napig érvényes 2048-bites RSA *aks-belépő-tls.crt*. A titkos kulcs fájlját nevű *aks-belépő-tls.key*. A TLS Kubernetes titkos kulcs szükséges mindkét fájlt.

Ez a cikk működik együtt a *demo.azure.com* tulajdonos köznapi nevének és nem kell módosítani. Üzemi használatra, adja meg a saját szervezeti értékeit a `-subj` paramétert:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Kubernetes titkos kódjának a TLS-tanúsítvány létrehozása

Ahhoz, hogy a Kubernetest, hogy a TLS-tanúsítványt és titkos kulcsot használja a bejövőforgalom-vezérlőt, akkor létrehozhat és használhat egy titkos kulcsot. A titkos kulcs többször van definiálva, és a tanúsítvány és az előző lépésben létrehozott kulcs fájlját használja. A titkos kód ezután bejövő útvonal meghatározása során hivatkozhat.

Az alábbi példa létrehoz egy titkos név *aks-belépő-tls*:

```console
kubectl create secret tls aks-ingress-tls \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Bemutató alkalmazások futtatása

Bejövőforgalom-vezérlőt, és a egy titkos kulcsot, a tanúsítvány konfigurálva. Most tegyük a futtatási két bemutató az AKS-fürt az alkalmazásokat. Ebben a példában a Helm szolgál egy egyszerű "Hello world" alkalmazás két példány üzembe helyezéséhez.

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

A következő példában a forgalmat a címre `https://demo.azure.com/` irányítja a rendszer a szolgáltatás nevű `aks-helloworld`. A cím forgalmát `https://demo.azure.com/hello-world-two` irányítja a rendszer a `ingress-demo` szolgáltatás. Ebben a cikkben nem kell módosítani ezeket bemutató állomásneveket. Adja meg a nevét, a kérelem és a generáció folyamat részeként megadott éles környezetben.

> [!TIP]
> Ha a tanúsítvány kérést folyamat során a CN-neve, a megadott állomásnév nem felel meg a gazdagép, a bejövő útvonal, akkor bejövő vezérlő megjeleníti a meghatározott egy *Kubernetes bejövő vezérlő hamis tanúsítvány*. Ellenőrizze, hogy a tanúsítvány a bejövő és kimenő útválasztási állomás neve egyezik.

A *tls* szakaszban arról tájékoztatja a bejövő útvonal használatához nevű titkos *aks-belépő-tls* a gazdagép *demo.azure.com*. Éles környezetben újra, adja meg a saját gazdagép címét.

Hozzon létre egy fájlt `hello-world-ingress.yaml` , és másolja az alábbi példában YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
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

A tanúsítványokat a hamis teszteléséhez *demo.azure.com* gazdagép, használja a `curl` , és adja meg a *--megoldásához* paraméter. Ez a paraméter lehetővé teszi a térkép a *demo.azure.com* neve a bejövőforgalom-vezérlőjéhez nyilvános IP-címet. Adja meg a saját bejövőforgalom-vezérlőt, nyilvános IP-címét, az alábbi példában látható módon:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Nincsenek további elérési út lett megadva a címét, ezért a bejövőforgalom-vezérlőt az alapértelmezett a */* útvonalat. Az első bemutató alkalmazás adja vissza, a következő sűrített példához kimenetben látható módon:

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

A *- v* paramétert a `curl` paranccsal kiadathatja részletes információkat, beleértve a kapott TLS-tanúsítványt. Félúton keresztül a curl-kimenet, ellenőrizheti, hogy a TLS-tanúsítvány lett megadva. A *-k* paraméter továbbra is fennáll, az oldal betöltése, annak ellenére, hogy önaláírt tanúsítványt használunk. Az alábbi példa azt mutatja, hogy a *kibocsátó: CN=demo.azure.com; O a tls bejövő aks =* tanúsítvány lett megadva:

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

Adjon hozzá */hello-world-two* elérési útját a címet, például *https://demo.azure.com/hello-world-two*. A második bemutató alkalmazás és az egyéni cím ad vissza, a következő sűrített példához kimenetben látható módon:

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

Ez a cikk a bejövő forgalom összetevők és a mintaalkalmazások telepítése Helm használja. Amikor telepít egy Helm-diagram, egy Kubernetes-erőforrások száma jönnek létre. Ilyen erőforrások többek között a podok, központi telepítések és szolgáltatásokat. Karbantartása, először a Helm-kiadások, a listában a `helm list` parancsot. Keresse meg a diagramok nevű *nginx-bejövő* és *aks-helloworld*, ahogy az alábbi példa kimenetében látható:

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

A kiadások, és törölje a `helm delete` parancsot. Az alábbi példában az NGINX bejövő üzembe helyezés és a két minta AKS hello world alkalmazás törlése.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Ezután távolítsa el az AKS hello world alkalmazás a Helm-adattárat:

```console
helm repo remove azure-samples
```

A bejövő útvonal, amely átirányítja a forgalmat a mintaalkalmazások eltávolítása:

```console
kubectl delete -f hello-world-ingress.yaml
```

Végezetül távolítsa el a tanúsítvány titkos kulcs:

```console
kubectl delete secret aks-ingress-tls
```

## <a name="next-steps"></a>További lépések

Ez a cikk tartalmaz néhány külső összetevők az aks-ben. Ezek az összetevők kapcsolatos további információkért tekintse meg a következő projekt oldalakat:

- [Helm CLI][helm-cli]
- [Az NGINX bejövőforgalom-vezérlőt][nginx-ingress]

További lehetőségek:

- [Hozzon létre egy alapszintű bejövőforgalom-vezérlőjéhez külső hálózatok közötti kapcsolatokkal][aks-ingress-basic]
- [A HTTP-kérelem útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Hozzon létre egy bejövőforgalom-vezérlőt, amely egy belső, saját hálózat és IP-cím][aks-ingress-internal]
- Hozzon létre egy bejövőforgalom-vezérlőt használó hozzunk titkosítása automatikusan létrehozni a TLS-tanúsítványok [dinamikus nyilvános IP-címmel rendelkező] [ aks-ingress-tls] vagy [egy statikus nyilvános IP-címmel][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
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
