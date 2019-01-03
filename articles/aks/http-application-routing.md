---
title: HTTP útválasztási bővítmény az Azure Kubernetes Service (AKS)
description: A HTTP-alkalmazás útválasztási bővítmények használatát az Azure Kubernetes Service (AKS).
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/25/2018
ms.author: laevenso
ms.openlocfilehash: 0bca7281c390388bd860219fb6f2eacb96b99df0
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742388"
---
# <a name="http-application-routing"></a>HTTP-alkalmazások útválasztása

A HTTP-kérelem útválasztási megoldás megkönnyíti az Azure Kubernetes Service (AKS) fürtön üzembe helyezett alkalmazások eléréséhez. A megoldás engedélyezésekor a Bejövőforgalom-vezérlőt az AKS-fürt azt konfigurálja. Alkalmazások vannak telepítve, mint a megoldás létrehoz végpontjainak nyilvánosan elérhető-e DNS-neveit is.

Ha a bővítmény engedélyezve van, az előfizetésben hoz létre egy DNS-zónát. További információ a DNS-költség: [DNS díjszabási][dns-pricing].

> [!CAUTION]
> A HTTP-kérelem útválasztási bővítmény célja, hogy gyorsan létrehozhat egy bejövőforgalom-vezérlőt és az alkalmazások eléréséhez. Ez a bővítmény nem ajánlott éles környezetben való használatra. Támogatja az éles használatra kész bejövő üzemelő példánya, amely tartalmazza a replikák és a TLS, lásd: [hozzon létre egy HTTPS bejövőforgalom-vezérlőjéhez](https://docs.microsoft.com/azure/aks/ingress-tls).

## <a name="http-routing-solution-overview"></a>HTTP-útválasztási megoldás áttekintése

A bővítmény üzembe helyezi a két összetevőt: egy [Kubernetes Bejövőforgalom-vezérlőjéhez] [ ingress] és a egy [külső DNS-] [ external-dns] vezérlő.

- **Bejövőforgalom-vezérlőjéhez**: A Bejövőforgalom-vezérlőjéhez típusú terheléselosztó Kubernetes szolgáltatás használatával közvetlenül csatlakozik az interneten. A Bejövőforgalom-vezérlőjéhez figyeli, és implementálja [Kubernetes bejövő erőforrások][ingress-resource], alkalmazás-végpontokra irányuló útvonalakat hoz létre, amely.
- **Külső DNS-vezérlő**: Kubernetes bejövő erőforrások figyeli, és létrehozza a DNS A rekordokat a fürtre jellemző DNS-zónában.

## <a name="deploy-http-routing-cli"></a>Helyezze üzembe a HTTP-útválasztás: parancssori felület

A HTTP-kérelem útválasztási bővítmény engedélyezhető az Azure CLI-vel egy AKS-fürt üzembe helyezésekor. Ehhez használja a [az aks létrehozása] [ az-aks-create] parancsot a `--enable-addons` argumentum.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

HTTP-útválasztás a meglévő AKS fürt használatával is engedélyezheti a [az aks enable-bővítmények] [ az-aks-enable-addons] parancsot. HTTP-útválasztás a meglévő fürt engedélyezéséhez vegye fel a `--addons` paramétert, és adja meg *http_application_routing* az alábbi példában látható módon:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

Miután a fürt üzembe helyezve, vagy frissíteni, használja a [az aks show] [ az-aks-show] parancs használatával kérje le a DNS-zóna nevét. Ez a név szükséges alkalmazások üzembe helyezésére az AKS-fürtöt.

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table

Result
-----------------------------------------------------
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>Helyezze üzembe a HTTP-útválasztás: Portál

A HTTP-kérelem útválasztási bővítmény engedélyezhető az Azure Portalon keresztül, egy AKS-fürt üzembe helyezésekor.

![A HTTP-útválasztási szolgáltatás engedélyezése](media/http-routing/create.png)

Miután a fürt üzembe lesz helyezve, az automatikusan létrehozott AKS erőforráscsoport keresse meg, és válassza ki a DNS-zóna. Jegyezze fel a DNS-zóna nevét. Ez a név szükséges alkalmazások üzembe helyezésére az AKS-fürtöt.

![A DNS-zóna neve beolvasása](media/http-routing/dns.png)

## <a name="use-http-routing"></a>Használja a HTTP-Útválasztás

A HTTP útválasztási megoldást csak indítható el, amely a következőképpen vannak feliratozva bejövő erőforrásokon:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Hozzon létre egy fájlt **minták – http-kérelem-routing.yaml** másolja be a következő yaml-kódot. 43. sorban, frissítse `<CLUSTER_SPECIFIC_DNS_ZONE>` Ez a cikk az előző lépésben gyűjtött és a DNS-zóna nevét.


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: party-clippy
spec:
  template:
    metadata:
      labels:
        app: party-clippy
    spec:
      containers:
      - image: r.j3ss.co/party-clippy
        name: party-clippy
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        tty: true
        command: ["party-clippy"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: party-clippy
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: party-clippy
  type: ClusterIP
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: party-clippy
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: party-clippy.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: party-clippy
          servicePort: 80
        path: /
```

Használja a [a kubectl a alkalmazni] [ kubectl-apply] parancsot az erőforrások létrehozásához.

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

A cURL vagy a böngésző használatával navigáljon az állomásnév-minták – http-kérelem-routing.yaml fájl gazdagép szakaszában megadott. Az alkalmazás előtt az interneten keresztül elérhető legfeljebb egy percig is eltarthat.

```bash
$ curl party-clippy.471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io

 _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/

```

## <a name="remove-http-routing"></a>Távolítsa el a HTTP-Útválasztás

A HTTP-útválasztási megoldás távolíthatja el az Azure CLI használatával. Ehhez futtassa a következő parancsot, és cserélje le az AKS-fürt és az erőforrás csoport neve.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

A HTTP-kérelem útválasztási bővítmény le van tiltva, egy Kubernetes-erőforrást a fürt maradhatnak. Ilyen erőforrások többek között *configMaps* és *titkok*, és hozza létre a rendszer a *kube rendszer* névtér. A tiszta fürt fenntartása, előfordulhat, hogy el kívánja távolítani ezeket az erőforrásokat.

Keressen *bővítmény-http-kérelem-útválasztási* az alábbi erőforrások [kubectl get] [ kubectl-get] parancsokat:

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

Az alábbi példa kimenetében látható, hogy a törölni kívánt configMaps:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Erőforrások törléséhez használja a [kubectl törlése] [ kubectl-delete] parancsot. Adja meg az erőforrás típusa, az erőforrás nevét és a névtér. A következő példa törli az előző configmaps egyikét:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Ismételje meg az előző `kubectl delete` lépést az összes *bővítmény-http-kérelem-útválasztási* maradt a fürtben lévő erőforrásokat.

## <a name="troubleshoot"></a>Hibaelhárítás

Használja a [kubectl naplók] [ kubectl-logs] paranccsal tekintheti meg az alkalmazásnaplókat a külső DNS-alkalmazáshoz. A naplók kell győződjön meg arról, hogy az A és a txt típusú DNS-rekord sikeresen létrejöttek-e.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Ezeket a rekordokat a DNS-zóna erőforrásai az Azure Portalon is láthatók.

![A DNS-rekordok beolvasása](media/http-routing/clippy.png)

Használja a [kubectl naplók] [ kubectl-logs] parancsot az alkalmazásnaplókat az Nginx Bejövőforgalom-vezérlőjéhez tartozó megtekintéséhez. Ellenőrizze a naplókat a `CREATE` egy bejövő erőforrás és a vezérlő a töltse be újra. Az összes HTTP-tevékenységet a rendszer naplózza.

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"party-clippy", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/party-clippy
W0426 21:51:57.908771       9 controller.go:775] service default/party-clippy does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-party-clippy-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Távolítsa el a jelen cikkben létrehozott kapcsolódó Kubernetes-objektumokat.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>További lépések

Az aks-ben egy biztonságos HTTPS Bejövőforgalom-vezérlőjéhez telepítésével kapcsolatos információkért lásd: [HTTPS bejövő forgalom az Azure Kubernetes Service (AKS)][ingress-https].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons


<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
