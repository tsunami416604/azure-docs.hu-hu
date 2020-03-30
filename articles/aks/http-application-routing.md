---
title: HTTP-alkalmazásútválasztási bővítmény az Azure Kubernetes szolgáltatáson (AKS)
description: Használja a HTTP-alkalmazás útválasztási bővítményaz Azure Kubernetes szolgáltatás (AKS).
services: container-service
author: lachie83
ms.topic: article
ms.date: 08/06/2019
ms.author: laevenso
ms.openlocfilehash: 5794e46f10abec1ceef4c79a881f7f353e729634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595688"
---
# <a name="http-application-routing"></a>HTTP-alkalmazások útválasztása

A HTTP-alkalmazásútválasztási megoldás megkönnyíti az Azure Kubernetes-fürt (AKS) fürtre telepített alkalmazások elérését. Ha a megoldás engedélyezve van, konfigurálja a [bejövő kapcsolat vezérlőjét](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) az AKS-fürtben. Az alkalmazások telepítése során a megoldás nyilvánosan elérhető DNS-neveket is létrehoz az alkalmazásvégpontokhoz.

Ha a bővítmény engedélyezve van, létrehoz egy DNS-zónát az előfizetésében. A DNS-költségekről további információt a [DNS-díjszabás][dns-pricing]című témakörben talál.

> [!CAUTION]
> A HTTP-alkalmazás útválasztási bővítményt úgy tervezték, hogy gyorsan létrehozhat egy bejövő kapcsolatvezérlőt, és hozzáférhessen az alkalmazásokhoz. Ez a bővítmény nem ajánlott éles használatra. A több replikát és TLS-támogatást tartalmazó éles környezethasználatra kész környezettelepítésekről a [HTTPS-be- és áttétkezelő létrehozása című](https://docs.microsoft.com/azure/aks/ingress-tls)témakörben talál.

## <a name="http-routing-solution-overview"></a>HTTP útválasztási megoldás – áttekintés

A bővítmény két összetevőt telepít: egy [Kubernetes Ingress vezérlőt][ingress] és egy [külső DNS-vezérlőt.][external-dns]

- **Be- és be- és visszatöltési vezérlő:** A be- és écses vezérlő loadbalancer típusú Kubernetes-szolgáltatás használatával elérhető az interneten. A be- ésécs-vezérlő figyeli és megvalósítja a [Kubernetes ingress erőforrásokat,][ingress-resource]amelyek útvonalakat hoznak létre az alkalmazásvégpontokhoz.
- **Külső DNS-vezérlő:** Figyeli a Kubernetes Ingress erőforrásokat, és létrehozza a DNS A rekordokat a fürtspecifikus DNS-zónában.

## <a name="deploy-http-routing-cli"></a>HTTP-útválasztás telepítése: CLI

A HTTP-alkalmazás útválasztási bővítmény engedélyezhető az Azure CLI-vel egy AKS-fürt telepítésekor. Ehhez használja az [aks create][az-aks-create] parancsot `--enable-addons` az argumentummal.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Ha több bővítményt szeretne engedélyezni, adja meg őket vesszővel tagolt listaként. A HTTP-alkalmazások útválasztásának és figyelésének engedélyezéséhez például használja a formátumot. `--enable-addons http_application_routing,monitoring`

A HTTP-útválasztást egy meglévő AKS-fürtön is engedélyezheti az [aks enable-addons][az-aks-enable-addons] paranccsal. A HTTP-útválasztás engedélyezéséhez egy `--addons` meglévő fürtön adja hozzá a paramétert, és adja meg *http_application_routing* az alábbi példában látható módon:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

A fürt telepítése vagy frissítése után az [az aks show][az-aks-show] paranccsal olvassa be a DNS-zóna nevét. Ez a név szükséges az alkalmazások AKS-fürtbe való telepítéséhez.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table
```

Eredmény

9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io


## <a name="deploy-http-routing-portal"></a>HTTP-útválasztás telepítése: Portál

A HTTP-alkalmazás útválasztási bővítmény az Azure Portalon keresztül engedélyezhető Egy AKS-fürt telepítésekor.

![A HTTP-útválasztási szolgáltatás engedélyezése](media/http-routing/create.png)

A fürt telepítése után keresse meg az automatikusan létrehozott AKS erőforráscsoportot, és válassza ki a DNS-zónát. Jegyezze fel a DNS-zóna nevét. Ez a név szükséges az alkalmazások AKS-fürtbe való telepítéséhez.

![A DNS-zóna nevének beszereznie](media/http-routing/dns.png)

## <a name="use-http-routing"></a>HTTP-útválasztás használata

A HTTP-alkalmazásútválasztási megoldás csak a következő megjegyzésekkel ellátható bejövő erőforrásokon aktiválható:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Hozzon létre egy **samples-http-application-routing.yaml** nevű fájlt, és másolja a következő YAML-fájlba. A 43-as `<CLUSTER_SPECIFIC_DNS_ZONE>` sorban frissítse a cikk előző lépésében összegyűjtött DNS-zónanévvel.


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

Használja a [kubectl apply parancsot][kubectl-apply] az erőforrások létrehozásához.

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

A cURL vagy a böngésző segítségével keresse meg a samples-http-application-routing.yaml fájl gazdaszakaszában megadott állomásnevet. Az alkalmazás akár egy percet is igénybe vehet, mielőtt elérhető véválik az interneten keresztül.

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

## <a name="remove-http-routing"></a>HTTP-útválasztás eltávolítása

A HTTP útválasztási megoldás eltávolítható az Azure CLI használatával. Ehhez futtassa a következő parancsot, helyettesítse az AKS-fürt és az erőforráscsoport nevét.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

Ha a HTTP-alkalmazás útválasztási bővítménye le van tiltva, egyes Kubernetes-erőforrások maradhatnak a fürtben. Ezek az erőforrások közé tartozik *a configMaps* és *a titkos kulcsok,* és jönnek létre a *kube-rendszer* névtérben. A tiszta fürt karbantartásához célszerű eltávolítani ezeket az erőforrásokat.

Keresse *meg az addon-http-application-routing* erőforrásokat a következő [kubectl get][kubectl-get] parancsok használatával:

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

A következő példa kimeneti jeleníti meg configMaps, hogy törölni kell:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Az erőforrások törléséhez használja a [kubectl delete parancsot.][kubectl-delete] Adja meg az erőforrás típusát, az erőforrás nevét és a névteret. A következő példa törli az előző konfigurációs térképek egyikét:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Ismételje meg `kubectl delete` az előző lépést a fürtben maradt összes *addon-http-application-routing* erőforrás esetében.

## <a name="troubleshoot"></a>Hibaelhárítás

A [kubectl logs][kubectl-logs] paranccsal megtekintheti a külső DNS-alkalmazás alkalmazásnaplóit. A naplóknak meg kell erősíteniük, hogy az A és TXT DNS-rekord sikeresen létrejött.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Ezek a rekordok is látható a DNS-zóna erőforrás az Azure Portalon.

![A DNS-rekordok beszerezése](media/http-routing/clippy.png)

A [Kubectl logs][kubectl-logs] paranccsal tekintse meg a Nginx ingress vezérlő alkalmazásnaplók. A naplók nak `CREATE` meg kell erősítenie a be- és a vezérlő újratöltését. Minden HTTP-tevékenység naplózva van.

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

Távolítsa el a cikkben létrehozott társított Kubernetes-objektumokat.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>További lépések

A HTTPS-vel védett bejövő forgalom vezérlőjének a KS-ben való telepítéséről a [HTTPS-bekapcsolás az Azure Kubernetes szolgáltatás (AKS)][ingress-https]című témakörben talál további információt.

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
