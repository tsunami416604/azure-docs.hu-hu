---
title: Azure tároló szolgáltatás (AKS) fürt érkező konfigurálása
description: Telepítse és konfigurálja az NGINX érkező vezérlőhöz egy Azure tároló szolgáltatás (AKS) fürt.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d56b27a040420d049f567ac0de9289b1e72f3ea9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="https-ingress-on-azure-container-service-aks"></a>Azure tárolószolgáltatás (AKS) HTTPS érkező

Az érkező vezérlőhöz egy adott szoftver, amely fordított proxy, konfigurálható a forgalom útválasztásához megszüntetése, és a TLS Kubernetes szolgáltatásokhoz. Kubernetes érkező erőforrások érkező szabályok és az egyes Kubernetes szolgáltatások útvonalak konfigurálására szolgálnak. Az érkező vezérlőhöz és érkező szabályokat használ, egy külső cím forgalom irányítására vonatkozik több szolgáltatásra Kubernetes fürtben használható.

Ez a dokumentum végigvezeti egy minta központi telepítése a [NGINX érkező vezérlő] [ nginx-ingress] Azure tároló szolgáltatás (AKS) fürtben. Emellett a [KUBE-LEGO] [ kube-lego] projekt használt automatikus létrehozása és konfigurálása [most titkosítása] [ lets-encrypt] tanúsítványokat. Végül több alkalmazást futtathatók a AKS fürtben, amelyek érhető el egyetlen címen keresztül.

## <a name="prerequisite"></a>Előfeltétel

Helm parancssori felület telepítése – tekintse meg a Helm CLI [dokumentáció] [ helm-cli] telepítési utasításokat.

## <a name="install-an-ingress-controller"></a>Egy érkező controller telepítése

A NGINX érkező tartományvezérlő telepítése Helm használatával. Tekintse meg a NGINX érkező vezérlő [dokumentáció] [ nginx-ingress] részletes telepítési információkat.

A diagram tárház frissítése.

```console
helm repo update
```

A NGINX érkező üzembe. Ebben a példában a tartományvezérlőre telepíti a `kube-system` névtér, ez az Ön által választott egy névteret kell módosítani.

```
helm install stable/nginx-ingress --namespace kube-system
```

A telepítés során az Azure nyilvános IP-címet a érkező tartományvezérlő jön létre. A nyilvános IP-cím beszerzéséhez használja a kubectl get szolgáltatás parancsot. Az IP-cím hozzá kell rendelni a szolgáltatás egy ideig is eltarthat.

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   13.82.238.45   80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>         80/TCP                       20m
```

Nincs érkező szabály létrejött, ha a nyilvános IP-címét, mert a rendszer irányítja az NGINX érkező tartományvezérlők alapértelmezett 404-es lapra.

![Alapértelmezett NGINX háttér](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>DNS-nevét konfigurálja

Mivel a HTTPS-tanúsítványok használata esetén kell egy teljes tartománynevet a érkező vezérlők IP-cím konfigurálása. Ebben a példában az Azure teljes Tartománynevet hozza létre az Azure parancssori felület. A parancsfájl frissíteni az IP-címe a érkező tartományvezérlő és a nevét, amely a teljes Tartománynevet használni szeretné.

```
#!/bin/bash

# Public IP address
IP="52.224.125.195"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get resource group and public ip name
RESOURCEGROUP=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[resourceGroup]" --output tsv)
PIPNAME=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[name]" --output tsv)

# Update public ip address with dns name
az network public-ip update --resource-group $RESOURCEGROUP --name  $PIPNAME --dns-name $DNSNAME
```

A bejövő adatok tartományvezérlő teljesen minősített Tartománynevét keresztül érhető el kell.

## <a name="install-kube-lego"></a>KUBE-LEGO telepítése

A NGINX érkező vezérlő támogatja a TLS-záráshoz. Számos módon lekérdezéséhez és tanúsítványok konfigurálása a HTTPS-hez, amíg ez a dokumentum bemutatja használatával [KUBE-LEGO][kube-lego], amely biztosítja az automatikus [lehetővé teszi, hogy titkosítani] [ lets-encrypt] tanúsítvány létrehozása és kezelése funkciót.

A KUBE-LEGO tartományvezérlő telepítéséhez a következő paranccsal Helm telepítése. Frissítse az e-mail cím a szervezet közül.

```
helm install stable/kube-lego \
  --set config.LEGO_EMAIL=user@contoso.com \
  --set config.LEGO_URL=https://acme-v01.api.letsencrypt.org/directory
```

KUBE-LEGO konfigurációs további információkért tekintse meg a [KUBE-LEGO dokumentáció][kube-lego].

## <a name="run-application"></a>Alkalmazás futtatása

Ezen a ponton az érkező vezérlőhöz és a tanúsítvány-kezelési megoldás vannak konfigurálva. Most futtassa az egyes alkalmazások a AKS fürtön.

Ehhez a példához Helm futtatására szolgál egy egyszerű hello world alkalmazásról több példányát.

Mielőtt futtatná az alkalmazást, adja hozzá az Azure-minták Helm tárház a fejlesztői rendszeren.

```
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

 A AKS hello world diagram futtassa a következő parancsot:

```
helm install azure-samples/aks-helloworld
```

Most már a hello world alkalmazásról második példányának a telepítéséhez.

A második példány adja meg az új címet, hogy a két alkalmazás vizuálisan különbözőek. Meg kell adnia egy egyedi nevét is. Ezek a beállítások a következő parancs látható.

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Érkező útvonal létrehozása

Mindkét alkalmazás használhatók a Kubernetes fürtben futó azonban be lett állítva egy, a következő típusú szolgáltatást `ClusterIP`. Az alkalmazások, az internetről nem érhetők el. Ahhoz, hogy az elérhető, hozzon létre egy Kubernetes érkező erőforrást. A bejövő adatok erőforrás konfigurálja a szabályokat, amelyek irányíthatja a forgalmat egy, a két alkalmazás.

Adjon meg egy fájlnevet `hello-world-ingress.yaml` és a következő YAM másolja.

Jegyezze fel, hogy a forgalmat a cím `https://demo-aks-ingress.eastus.cloudapp.azure.com/` nevű szolgáltatás irányítása `aks-helloworld`. A cím felé irányuló forgalom `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` annak biztosítására, hogy a `ingress-demo` szolgáltatás.

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/tls-acme: "true"
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

A bejövő adatok erőforrás létrehozása a `kubectl apply` parancsot.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>A bejövő adatok konfigurációjának tesztelése

Keresse meg a Kubernetes érkező tartományvezérlő teljes Tartománynevét, a hello world alkalmazásról kell megjelennie.

![Egy alkalmazás – példa](media/ingress/app-one.png)

Most keresse meg a érkező vezérlő teljes Tartománynevét a `/hello-world-two` elérési útja, a hello world alkalmazásról az egyéni címmel kell megjelennie.

![A két alkalmazás – példa](media/ingress/app-two.png)

Figyelje meg, hogy a kapcsolat titkosított, és most titkosítása által kiállított tanúsítványt használt is.

![Lehetővé teszi, hogy a tanúsítvány titkosításához](media/ingress/certificate.png)

## <a name="next-steps"></a>További lépések

További információ a jelen dokumentumban bemutatott szoftver.

- [Helm parancssori felület][helm-cli]
- [NGINX érkező vezérlő][nginx-ingress]
- [KUBE-LEGO][kube-lego]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/en-us/azure/aks/kubernetes-helm#install-helm-cli
[kube-lego]: https://github.com/jetstack/kube-lego
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
