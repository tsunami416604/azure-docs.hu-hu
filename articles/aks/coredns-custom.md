---
title: CoreDNS testreszabása az Azure Kubernetes Service (AKS)
description: Ismerje meg, hogyan CoreDNS altartományok hozzáadásához, vagy kiterjesztheti az Azure Kubernetes Service (AKS) használatával egyéni DNS-végpontok testreszabása
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 03/15/2019
ms.author: jnoller
ms.openlocfilehash: 9c67902f8a6c10552ea60fed145afc24f82b01a1
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65594288"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Az Azure Kubernetes Service CoreDNS testreszabása

Az Azure Kubernetes Service (AKS) használ a [CoreDNS] [ coredns] fürt DNS-kezelés és a megoldás az összes projektet *1.12.x* és nagyobb fürtök. Korábban a kube-dns projekt lett megadva. Ez a kube-dns-projekt már elavult. CoreDNS testreszabási és a Kubernetes kapcsolatos további információkért lásd: a [felsőbb rétegbeli dokumentációs][corednsk8s].

Mivel AKS egy felügyelt szolgáltatás, CoreDNS fő konfigurációja nem módosítható (egy *alapfájl*). Ehelyett használja a Kubernetes *ConfigMap* felülbírálhatja az alapértelmezett beállításokat. Az alapértelmezett AKS CoreDNS ConfigMaps megtekintéséhez használja a `kubectl get configmaps coredns -o yaml` parancsot.

Ez a cikk bemutatja, hogyan ConfigMaps használandó testreszabási lehetőségeket CoreDNS az aks-ben.

> [!NOTE]
> `kube-dns` ajánlott különböző [testreszabási lehetőségek] [ kubednsblog] Kubernetes konfigurációs térkép-n keresztül. CoreDNS van **nem** visszamenőleg kompatibilis a kube-dns. Korábban már használt testreszabások CoreDNS való használatra kell frissíteni.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg az AKS gyors [az Azure CLI-vel] [az aks-rövid-cli] vagy [az Azure Portalon] [az aks – rövid útmutató – portal].

## <a name="what-is-supportedunsupported"></a>Mi az a támogatott/nem támogatott

Az összes beépített CoreDNS beépülő modulok támogatottak. Nincs add-a vagy harmadik féltől származó beépülő modulok támogatottak.

## <a name="rewrite-dns"></a>DNS újraírása

Rendelkezik egy forgatókönyvet, hogy hajtsa végre a működés közbeni DNS neve újraírások. A következő példában cserélje le a `<domain to be written>` a saját teljesen minősített tartománynevét. Hozzon létre egy fájlt `corednsms.json` , és illessze be a következő példa konfiguráció:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        proxy .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Létrehozhatja a ConfigMap a [a kubectl a alkalmazni configmap] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl neve:

```console
kubectl apply -f corednsms.json
```

A testreszabások alkalmazása ellenőrzéséhez használja a [kubectl get configmaps] [ kubectl-get] , és adja meg a *coredns – egyéni* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Most már kényszerített újbóli betöltéséhez a ConfigMap CoreDNS. A [kubectl törlése pod] [ kubectl delete] parancs nem romboló, és nem leállás következik. A `kube-dns` podok törlődnek, és a Kubernetes-ütemező azt majd újra létrehozza őket. Ezek új podok az élettartam értéke tartalmaz.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> A fenti parancs nem megfelelő. Bár azt épp módosított `coredns`, az üzembe helyezés alatt áll a **kube-dns** nevét.

## <a name="custom-proxy-server"></a>Egyéni proxy server

Adja meg a hálózati forgalmat egy proxykiszolgáló van szüksége, ha egy ConfigMap DNS testre is létrehozhat. Frissítse a következő példában a `proxy` nevét és címét azokra az értékekre a saját környezetének. Hozzon létre egy fájlt `corednsms.json` , és illessze be a következő példa konfiguráció:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        proxy foo.com 1.1.1.1
    }
```

Az előző példához hasonlóan hozzon létre a ConfigMap a [a kubectl a alkalmazni configmap] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzék nevét. Ezután kényszerítése CoreDNS a ConfigMap használatával frissítse a [kubectl pod törlése] [ kubectl delete] a Kubernetes a Scheduler hoznia őket:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Egyéni tartományok használata

Érdemes az egyéni tartományok megoldható csak belső használatra konfigurálásához. Például előfordulhat, hogy szeretné feloldani az egyéni tartomány *puglife.local*, de nem egy érvényes legfelső szintű tartományt. Egyéni tartomány ConfigMap nélkül az AKS-fürt nem oldható fel a címet.

A következő példában frissítse az egyéni tartomány és az IP-cím az értékeket a saját környezetben való közvetlen forgalom. Hozzon létre egy fájlt `corednsms.json` , és illessze be a következő példa konfiguráció:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        proxy . 192.11.0.1  # this is my test/dev DNS server
    }
```

Az előző példához hasonlóan hozzon létre a ConfigMap a [a kubectl a alkalmazni configmap] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzék nevét. Ezután kényszerítése CoreDNS a ConfigMap használatával frissítse a [kubectl pod törlése] [ kubectl delete] a Kubernetes a Scheduler hoznia őket:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="stub-domains"></a>A helyettes tartományok

CoreDNS is használható a helyettes tartományok konfigurálása. A következő példában frissítse az egyéni tartományok és az IP-címek az értékeket a saját környezetének. Hozzon létre egy fájlt `corednsms.json` , és illessze be a következő példa konfiguráció:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        proxy . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        proxy . 2.3.4.5
    }

```

Az előző példához hasonlóan hozzon létre a ConfigMap a [a kubectl a alkalmazni configmap] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzék nevét. Ezután kényszerítése CoreDNS a ConfigMap használatával frissítse a [kubectl pod törlése] [ kubectl delete] a Kubernetes a Scheduler hoznia őket:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Gazdagépek beépülő modul

Az összes beépített beépülő modulok támogatottak, ez azt jelenti, hogy a CoreDNS [gazdagépek] [ coredns hosts] beépülő modul érhető el, valamint testre:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatta, néhány példa forgatókönyvet CoreDNS testreszabása. Információk a CoreDNS projektre: [a felsőbb rétegbeli CoreDNS lapot][coredns].

Hálózati alapfogalmakat kapcsolatos további információkért lásd: [fogalmak az aks-ben az alkalmazások hálózati][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - external -->
[concepts-network]: concepts-network.md
