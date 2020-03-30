---
title: A CoreDNS testreszabása az Azure Kubernetes szolgáltatáshoz (AKS)
description: Megtudhatja, hogy miként szabhatja testre a CoreDNS-t altartományok hozzáadásához vagy egyéni DNS-végpontok bővítéséhez az Azure Kubernetes Szolgáltatás (AKS) használatával
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595824"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>A CoreDNS testreszabása Azure Kubernetes Service-szel

Az Azure Kubernetes Service (AKS) a [CoreDNS-projektet][coredns] használja a fürt DNS-kezeléséhez és felbontásához az összes *1.12.x* és magasabb fürttel. Korábban a kube-dns projektet használták. Ez a kube-dns projekt most elavult. A CoreDNS testreszabásáról és a Kubernetes szolgáltatásról további információt a [hivatalos upstream dokumentációban][corednsk8s]talál.

Mivel az AKS felügyelt szolgáltatás, nem módosíthatja a CoreDNS *(CoreFile) fő konfigurációját.* Ehelyett egy Kubernetes *ConfigMap* használatával felülbírálhatja az alapértelmezett beállításokat. Az alapértelmezett AKS CoreDNS ConfigMaps `kubectl get configmaps --namespace=kube-system coredns -o yaml` megtekintéséhez használja a parancsot.

Ez a cikk bemutatja, hogyan használhatja a ConfigMaps-ot a CoreDNS alapvető testreszabási lehetőségeihez az AKS-ben. Ez a megközelítés különbözik a CoreDNS konfigurálásától más környezetekben, például a CoreFile használatával. Ellenőrizze a Futtatott CoreDNS verzióját, mert a konfigurációs értékek változhatnak a verziók között.

> [!NOTE]
> `kube-dns`különböző [testreszabási lehetőségeket][kubednsblog] kínált egy Kubernetes konfigurációs térképen keresztül. CoreDNS **nem** visszafelé kompatibilis kube-dns. A korábban használt testreszabásokat frissíteni kell a CoreDNS-sel való használathoz.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

## <a name="what-is-supportedunsupported"></a>Mi támogatott/nem támogatott

Minden beépített CoreDNS plugin támogatott. Nincs bővítmény/harmadik fél től származó bővítmény.

## <a name="rewrite-dns"></a>DNS újraírása

Az egyik forgatókönyv, hogy végre on-the-fly DNS-név átírása. A következő példában `<domain to be written>` cserélje le a saját teljesen minősített tartománynevét. Hozzon létre `corednsms.yaml` egy nevű fájlt, és illessze be a következő példakonfigurációt:

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
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Hozza létre a ConfigMap-et a [kubectl apply configmap][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f corednsms.yaml
```

A testreszabások alkalmazásának ellenőrzéséhez használja a [kubectl get configmaps-ot,][kubectl-get] és adja meg a *beszerzett egyedi* ConfigMap-et:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Most kényszerítse a CoreDNS-t a ConfigMap újratöltésére. A [kubectl delete pod][kubectl delete] parancs nem destruktív, és nem okoz leállási időt. A `kube-dns` podok törlődnek, és a Kubernetes ütemező, majd újra létrehozza őket. Ezek az új podok tartalmazzák a Változás a TTL érték.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> A fenti parancs helyes. Miközben változunk, `coredns`a telepítés **kube-dns** néven van.

## <a name="custom-forward-server"></a>Egyéni továbbító kiszolgáló

Ha meg kell adnia egy továbbítási kiszolgálót a hálózati forgalomhoz, létrehozhat egy ConfigMap-et a DNS testreszabásához. A következő példában `forward` frissítse a nevet és a címet a saját környezetére vonatkozó értékekkel. Hozzon létre `corednsms.yaml` egy nevű fájlt, és illessze be a következő példakonfigurációt:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Az előző példákhoz hasonló módon hozza létre a ConfigMap-et a [kubectl apply configmap][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét. Ezután kényszerítse a CoreDNS-t a ConfigMap újratöltésére a [kubectl delete pod][kubectl delete] használatával a Kubernetes ütemező számára, hogy újra létrehozza őket:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Egyéni tartományok használata

Előfordulhat, hogy olyan egyéni tartományokat szeretne konfigurálni, amelyek csak belsőleg oldhatók fel. Előfordulhat például, hogy fel szeretné oldani a *puglife.local*egyéni tartományt, amely nem érvényes legfelső szintű tartomány. Egyéni tartomány, a ConfigMap nélkül az AKS-fürt nem tudja feloldani a címet.

A következő példában frissítse az egyéni tartományt és az IP-címet, hogy a forgalmat a saját környezetére vonatkozó értékekkel irányítsa. Hozzon létre `corednsms.yaml` egy nevű fájlt, és illessze be a következő példakonfigurációt:

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
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Az előző példákhoz hasonló módon hozza létre a ConfigMap-et a [kubectl apply configmap][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét. Ezután kényszerítse a CoreDNS-t a ConfigMap újratöltésére a [kubectl delete pod][kubectl delete] használatával a Kubernetes ütemező számára, hogy újra létrehozza őket:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Csonktartományok

A CoreDNS a helyettes tartományok konfigurálására is használható. A következő példában frissítse az egyéni tartományokat és IP-címeket a saját környezetére vonatkozó értékekkel. Hozzon létre `corednsms.yaml` egy nevű fájlt, és illessze be a következő példakonfigurációt:

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
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Az előző példákhoz hasonló módon hozza létre a ConfigMap-et a [kubectl apply configmap][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét. Ezután kényszerítse a CoreDNS-t a ConfigMap újratöltésére a [kubectl delete pod][kubectl delete] használatával a Kubernetes ütemező számára, hogy újra létrehozza őket:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Hosts plugin

Mivel az összes beépített plugin támogatott, ez azt jelenti, hogy a CoreDNS [Hosts][coredns hosts] bővítmény is testreszabható:

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

## <a name="enable-logging-for-dns-query-debugging"></a>A DNS-lekérdezés hibakeresésének naplózásának engedélyezése 

A DNS-lekérdezés naplózásának engedélyezéséhez alkalmazza a következő konfigurációt a kisajátítandó ConfigMap alkalmazásban:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: |
        log
```

## <a name="next-steps"></a>További lépések

Ez a cikk néhány példa forgatókönyvet mutatott be a CoreDNS testreszabásához. A CoreDNS projekttel kapcsolatos információkért tekintse meg [a CoreDNS upstream projekt oldalát.][coredns]

Ha többet szeretne megtudni a törzshálózati fogalmakról, olvassa el [a Hálózati fogalmak az AKS alkalmazásban című témakört.][concepts-network]

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
