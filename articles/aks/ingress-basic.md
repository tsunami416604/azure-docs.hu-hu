---
title: Hozzon létre egy alapszintű bejövőforgalom-vezérlőt az Azure Kubernetes Service (AKS)
description: Megtudhatja, hogyan telepítése és konfigurálása egy alapszintű NGINX bejövőforgalom-vezérlőt az Azure Kubernetes Service (AKS)-fürtben.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 3ae7a3193e0a4bacc64524f477b6c179ead20b6b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357259"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Hozzon létre egy bejövőforgalom-vezérlőt az Azure Kubernetes Service (AKS)

Bejövőforgalom-vezérlőjéhez olyan szoftver, amely biztosítja a fordított proxy, konfigurálható forgalom-útválasztást és a TLS-lezárást biztosít Kubernetes-szolgáltatás. Kubernetes bejövő erőforrások segítségével konfigurálhatja a bejövő szabályok és útvonalak a Kubernetes-szolgáltatás. A bejövőforgalom-vezérlőt, és a bejövő szabályok használatával az egyetlen IP-cím irányíthatja a forgalmat több szolgáltatást a Kubernetes-fürtben használható.

Ez a cikk bemutatja, hogyan helyezhet üzembe a [NGINX bejövőforgalom-vezérlőjéhez] [ nginx-ingress] Azure Kubernetes Service (AKS)-fürtben. Két alkalmazás futtatása a az AKS-fürtöt, amelyek mindegyike érhető el az egyetlen IP-címen keresztül.

További lehetőségek:

- [A HTTP-kérelem útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Hozzon létre egy bejövőforgalom-vezérlőt, amely egy belső, saját hálózat és IP-cím][aks-ingress-internal]
- [Hozzon létre egy bejövőforgalom-vezérlőjéhez dinamikus nyilvános IP-cím, és nézzük titkosítása automatikusan létrehozni a TLS-tanúsítványok konfigurálása][aks-ingress-tls]
- [Hozzon létre bejövőforgalom-vezérlőjéhez statikus nyilvános IP-címet, és nézzük titkosítása automatikusan létrehozni a TLS-tanúsítványok konfigurálása][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk a Helm használatával az NGINX bejövőforgalom-vezérlőt, a tanúsítvány-kezelő és a egy mintául szolgáló webalkalmazás telepítése. Szüksége lesz a Helm belül az AKS-fürt inicializálva, és a tiller valóban szolgáltatásfiók használatával. Konfigurálása és a Helm használatával további információkért lásd: [telepíthet alkalmazásokat a Helm használatával az Azure Kubernetes Service (AKS)][use-helm].

Ez a cikk is szükséges, hogy futnak-e az Azure CLI 2.0.41 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Hozzon létre egy bejövőforgalom-vezérlőt

A bejövőforgalom-vezérlőjéhez létrehozásához használja `Helm` telepítéséhez *nginx-belépő*.

> [!TIP]
> A következő példa telepíti a bejövőforgalom-vezérlőt az `kube-system` névtér. Megadhat egy másik névtér a saját környezetben, ha szükséges. Ha az AKS-fürt nem RBAC engedélyezve, vegye fel `--set rbac.create=false` parancsok.

```console
helm install stable/nginx-ingress --namespace kube-system
```

A terheléselosztó Kubernetes szolgáltatás az NGINX bejövőforgalom-vezérlőjéhez hoz létre, amikor a dinamikus nyilvános IP-cím van hozzárendelve, az alábbi példa kimenetében látható módon:

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                         TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
masked-otter-nginx-ingress-controller        LoadBalancer   10.0.92.99    40.117.74.8   80:31077/TCP,443:32592/TCP   7m
masked-otter-nginx-ingress-default-backend   ClusterIP      10.0.46.106   <none>        80/TCP                       7m
```

Bejövő szabályok már hozott létre, így az NGINX bejövőforgalom-vezérlőjéhez tartozó alapértelmezett 404-es lap is megjelenik, ha a belső IP-címe. Bejövő szabályok a következő lépések vannak konfigurálva.

## <a name="run-demo-applications"></a>Bemutató alkalmazások futtatása

Szeretné látni működés közben bejövőforgalom-vezérlőt, futtassunk két bemutató alkalmazás az AKS-fürt található. Ebben a példában a Helm szolgál egy egyszerű "Hello world" alkalmazás két példány üzembe helyezéséhez.

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

Mindkét alkalmazás most már a Kubernetes-fürtöt futtat. Irányíthatja a forgalmat mindegyik alkalmazás, hozzon létre egy Kubernetes bejövő erőforrást. A bejövő forgalom erőforrás konfigurálja a szabályokat, amelyek a két alkalmazás egyik irányíthatja a forgalmat.

A következő példában a forgalmat a címre `http://40.117.74.8/` irányítja a rendszer a szolgáltatás nevű `aks-helloworld`. A cím forgalmát `http://40.117.74.8/hello-world-two` irányítja a rendszer a `ingress-demo` szolgáltatás.

Hozzon létre egy fájlt `hello-world-ingress.yaml` , és másolja az alábbi példában YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
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

## <a name="test-the-ingress-controller"></a>A bejövőforgalom-vezérlőjéhez tesztelése

Tesztelheti a útvonalait a bejövőforgalom-vezérlőt, keresse meg a két alkalmazás. Például az NGINX bejövőforgalom-vezérlőt, IP-címét egy webböngészőben nyissa meg *http://40.117.74.8*. Az első bemutató alkalmazás megjelenik a webböngészőben az alábbi példában látható módon:

![Első alkalmazását a bejövőforgalom-vezérlőjéhez mögött fut.](media/ingress-basic/app-one.png)

Most adja hozzá a */hello-world-two* elérési útját az IP-cím, például *http://40.117.74.8/hello-world-two*. A második bemutató alkalmazás és az egyéni cím jelenik meg:

![A bejövőforgalom-vezérlőjéhez mögött futó második alkalmazás](media/ingress-basic/app-two.png)

## <a name="next-steps"></a>További lépések

Ez a cikk tartalmaz néhány külső összetevők az aks-ben. Ezek az összetevők kapcsolatos további információkért tekintse meg a következő projekt oldalakat:

- [Helm CLI][helm-cli]
- [Az NGINX bejövőforgalom-vezérlőt][nginx-ingress]

További lehetőségek:

- [A HTTP-kérelem útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Hozzon létre egy bejövőforgalom-vezérlőt, amely egy belső, saját hálózat és IP-cím][aks-ingress-internal]
- [Hozzon létre egy bejövőforgalom-vezérlőjéhez dinamikus nyilvános IP-cím, és nézzük titkosítása automatikusan létrehozni a TLS-tanúsítványok konfigurálása][aks-ingress-tls]
- [Hozzon létre bejövőforgalom-vezérlőjéhez statikus nyilvános IP-címet, és nézzük titkosítása automatikusan létrehozni a TLS-tanúsítványok konfigurálása][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
