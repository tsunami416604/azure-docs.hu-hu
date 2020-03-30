---
title: A Linkerd telepítése az Azure Kubernetes szolgáltatásban (AKS)
description: Megtudhatja, hogy miként telepítheti és használhatja a Linkerd szolgáltatást egy Azure Kubernetes-szolgáltatás (AKS) fürtben lévő szolgáltatásháló létrehozásához
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 419b61527b68299c82dec4f2f5da6b0220859cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593723"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>A Linkerd telepítése az Azure Kubernetes szolgáltatásban (AKS)

[A Linkerd][linkerd-github] egy nyílt forráskódú szolgáltatásháló és [cncf inkubátorprojekt.][linkerd-cncf] A Linkerd egy ultrakönnyű szolgáltatásháló, amely olyan funkciókat kínál, mint a forgalomkezelés, a szolgáltatás identitása és biztonsága, a megbízhatóság és a megfigyelhetőség. A Linkerdről további információt a [Linkerd GYIK][linkerd-faq] és a [Linkerd Architecture][linkerd-architecture] hivatalos dokumentációjában talál.

Ez a cikk bemutatja, hogyan kell telepíteni linkerd. A Linkerd `linkerd` ügyfél bináris telepítve van az ügyfélgépre, és a Linkerd összetevők egy Kubernetes-fürtbe vannak telepítve az AKS-en.

> [!NOTE]
> Ezek az utasítások hivatkozás `stable-2.6.0`Linkerd változat .
>
> A Linkerd `stable-2.6.x` futtatható Kubernetes `1.13+`verziók . További stabil és peremhálózati Linkerd verziókat találhat a [GitHub - Linkerd Releases oldalon.][linkerd-github-releases]

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A Linkerd linkerd ügyfél bináris fájljának letöltése és telepítése
> * A Linkerd telepítése AKS-re
> * A Linkerd telepítésének ellenőrzése
> * Az irányítópult elérése
> * A Linkerd eltávolítása az AKS-ből

## <a name="before-you-begin"></a>Előkészületek

A cikkben ismertetett lépések feltételezik, hogy létrehozott egy AKS-fürtöt (Kubernetes `1.13` és `kubectl` újabb, az RBAC engedélyezve), és kapcsolatot létesített a fürttel. Ha ezekhez az elemekhez segítségre van szüksége, olvassa el az [AKS rövid útmutatóját.][aks-quickstart]

Minden Linkerd podot linuxos csomópontokon kell futtatni - ez a beállítás az alapértelmezett az alább részletezett telepítési módszerben, és nem igényel további konfigurációt.

Ez a cikk a Linkerd telepítési útmutatót több különálló lépésre bontja el. Az eredmény ugyanaz a szerkezet, mint a hivatalos Linkerd első lépések [útmutatást][linkerd-getting-started].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>A Linkerd telepítése AKS-re

A Linkerd telepítése előtt a telepítés előtti ellenőrzéseket futtatjuk annak megállapítására, hogy a vezérlősík telepíthető-e az AKS-fürtre:

```console
linkerd check --pre
```

Az alábbiakhoz hasonló tetszés szerint az AKS-fürt a Linkerd érvényes telepítési célpontja:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

Most itt az ideje, hogy telepítse a Linkerd alkatrészeket. A `linkerd` és `kubectl` a bináris fájlok segítségével telepítse a Linkerd összetevőket az AKS-fürtbe. A `linkerd` program automatikusan létrehoz egy névteret, és az összetevők telepítve lesznek a névtérben.

```console
linkerd install | kubectl apply -f -
```

A Linkerd számos objektumot telepít. A fenti parancs kimenetének listájában `linkerd install` jelenik meg. A Linkerd-összetevők üzembe helyezése a fürtkörnyezettől függően körülbelül 1 percet vesz igénybe.

Ezen a ponton telepítette a Linkerd-et az AKS-fürthöz. Annak érdekében, hogy a Linkerd sikeres telepítése sikeres legyen, térjünk át a következő szakaszra [a Linkerd telepítésének ellenőrzése.](#validate-the-linkerd-installation)

## <a name="validate-the-linkerd-installation"></a>A Linkerd telepítésének ellenőrzése

Ellenőrizze, hogy az erőforrások sikeresen létrejöttek-e. Használja a [kubectl get svc][kubectl-get] és [kubectl get pod][kubectl-get] parancsokat a `linkerd` névtér lekérdezéséhez, ahol a Linkerd összetevők telepítve voltak a `linkerd install` parancs:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

A következő példa kimeneti jeleníti meg a szolgáltatások és a podok (linuxos csomópontokon ütemezve), amelyek most már fut:

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

A Linkerd parancsot `linkerd` ad az ügyfél bináris fájljának segítségével annak ellenőrzésére, hogy a Linkerd vezérlősík telepítése és konfigurálása sikeresen megtörtént-e.

```console
linkerd check
```

A telepítés sikerességedet jelezve a következőkhöz hasonló tetszést kell látnia:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>Az irányítópult elérése

A Linkerd egy irányítópultot tartalmaz, amely betekintést nyújt a szolgáltatáshálóba és a munkaterhelésekbe. Az irányítópult eléréséhez `linkerd dashboard` használja a parancsot. Ez a parancs [kubectl port-forward-t][kubectl-port-forward] használ, hogy biztonságos kapcsolatot hozzon létre az ügyfélgép és az AKS-fürt megfelelő podjai között. Ezután automatikusan megnyitja az irányítópultot az alapértelmezett böngészőben.

```console
linkerd dashboard
```

A parancs port-forward-ot is létrehoz, és visszaad egy hivatkozást a Grafana irányítópultokhoz.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>A Linkerd eltávolítása az AKS-ből

> [!WARNING]
> A Linkerd törlése egy futó rendszerből a szolgáltatások közötti forgalommal kapcsolatos problémákat okozhat. A folytatás előtt győződjön meg arról, hogy rendelkezik arról, hogy a rendszer továbbra is megfelelően működjön a Linkerd nélkül.

Először el kell távolítania az adatsík proxyit. Távolítsa el az automatikus [proxy-injektálási jegyzeteket][linkerd-automatic-proxy-injection] a számítási feladatok névtereiből, és terjesítse el a számítási feladatok központi telepítését. A számítási feladatok nak már nem kell rendelkezniük az adatsík-összetevőkhöz.

Végül távolítsa el a vezérlősíkot az alábbiak szerint:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>További lépések

A Linkerd további telepítési és konfigurációs lehetőségeinek megismeréséhez olvassa el a Linkerd alábbi hivatalos útmutatóját:

- [Linkerd - Helm telepítés][linkerd-install-with-helm]
- [Linkerd - Többlépcsős telepítés a szerepkör-jogosultságok kielégítésére][linkerd-multi-stage-installation]

További forgatókönyveket is követhet a következő használatával:

- [Linkerd emojivoto demó][linkerd-demo-emojivoto]
- [Linkerd könyvek demo][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
