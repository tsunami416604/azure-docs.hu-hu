---
title: Gyakorlati tanácsok az ütemező szolgáltatásaihoz
titleSuffix: Azure Kubernetes Service
description: Ismerje meg a fürtoperátor okainak gyakorlati tanácsait a speciális ütemező funkciók, például a betakarások és a tűrések, a csomópontválasztók és -affinitás, illetve a podok közötti affinitás és affinitás használatával kapcsolatban az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: d0d13a699d2559c6b4360c807721e0b748959382
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617529"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes-szolgáltatás (AKS) speciális ütemező funkcióinak gyakorlati tanácsok

A fürtök kezelése az Azure Kubernetes-szolgáltatás (AKS) kezelése során gyakran kell elkülöníteni a csapatokat és a számítási feladatokat. A Kubernetes-ütemező speciális funkciókat biztosít, amelyek segítségével szabályozhatja, hogy mely podok ütemezhetők bizonyos csomópontokon, vagy hogyan lehet a többpodos alkalmazásokat megfelelően elosztani a fürtön. 

Ez az ajánlott eljárások ról szóló cikk a fürtfelelősök speciális Kubernetes ütemezési szolgáltatásaira összpontosít. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A taints és a tolerations segítségével korlátozhatja, hogy milyen podok ütemezhetők a csomópontokon
> * Előnyben részesíti a podok futtatásához bizonyos csomópontok csomópont választók vagy csomópont affinitás
> * Szétválasztva vagy csoportosítva podok inter-pod affinitással vagy affinitással

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Dedikált csomópontok biztosítása taints és tolerations használatával

**Ajánlott eljárásokra vonatkozó útmutatás** – Az erőforrás-igényes alkalmazások, például a be- éselőrendszer-vezérlők hozzáférésének korlátozása adott csomópontokra. A csomópont-erőforrásokat elérhetővé kell tenni az őket igénylő munkaterhelések számára, és nem engedélyezi a csomópontokon lévő más számítási feladatok ütemezését.

Az AKS-fürt létrehozásakor gpu-támogatással vagy nagyszámú nagy teljesítményű processzorral telepíthet csomópontokat. Ezeket a csomópontokat gyakran használják nagy adatfeldolgozási számítási feladatokhoz, például a gépi tanuláshoz (ML) vagy a mesterséges intelligenciához (AI). Mivel az ilyen típusú hardver általában egy költséges csomópont erőforrás üzembe helyezéséhez, korlátozza a számítási feladatok, amelyek ezeken a csomópontokon ütemezhető. Ehelyett előfordulhat, hogy a fürt egyes csomópontjait a bejövő szolgáltatások futtatására és más számítási feladatok megelőzésére kívánja fordítani.

Ez a támogatás a különböző csomópontok több csomópont-készletek használatával érhető el. Az AKS-fürt egy vagy több csomópontkészletet biztosít.

A Kubernetes-ütemező használhat taints és tűrések korlátozhatja, milyen számítási feladatok futtathatók a csomópontokon.

* Egy **taint** egy csomópontra van alkalmazva, amely azt jelzi, hogy csak adott podok ütemezhetők rájuk.
* Ezután **egy tolerálást** alkalmaznak egy podra, amely lehetővé teszi számukra, hogy *tolerálják* a csomópont romlottságát.

Amikor egy podot telepít egy AKS-fürtre, a Kubernetes csak azokat a csomópontokat ütemezi, ahol a tűrés a taint-hez igazodik. Tegyük fel például, hogy rendelkezik egy csomópontkészlettel az AKS-fürtben a GPU-támogatással rendelkező csomópontok számára. Meg kell adnia a nevet, például *a GPU-t,* majd egy értéket az ütemezéshez. Ha ezt az értéket NoSchedule értékre *állítja,* a Kubernetes-ütemező nem ütemezheti a podokat a csomóponton, ha a pod nem határozza meg a megfelelő tűrést.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

A csomópontokra alkalmazott romlottság esetén ezután meg kell határoznia egy tűrést a pod specifikációjában, amely lehetővé teszi a csomópontok ütemezését. A következő példa `sku: gpu` az `effect: NoSchedule` előző lépésben a csomópontra alkalmazott és tolerálható tűrőt határozza meg:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Ha ez a pod telepítve `kubectl apply -f gpu-toleration.yaml`van, például a használata, Kubernetes sikeresen ütemezheti a pod a csomópontokon a taint alkalmazott. Ez a logikai elkülönítés lehetővé teszi a fürtön belüli erőforrásokhoz való hozzáférés szabályozását.

Ha taints alkalmazásakor együttműködik az alkalmazásfejlesztőkkel és -tulajdonosokkal, hogy lehetővé tegyék számukra a szükséges tűrések meghatározását a központi telepítésekben.

A taintokról és a tűrésekről további információt a [taints és tolerations alkalmazása][k8s-taints-tolerations]című témakörben talál.

Az AKS több csomópontkészletének használatáról az [AKS-ben lévő fürt több csomópontkészletének létrehozása és kezelése][use-multiple-node-pools]című témakörben talál további információt.

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>A taints és tolerations viselkedése az AKS-ben

Amikor frissít egy csomópontkészletet az AKS-ben, a taints és a tolerations egy meghatározott mintát követ, ahogy azok új csomópontokra vannak alkalmazva:

- **A virtuálisgép-méretezési csoportokat használó alapértelmezett fürtök**
  - Tegyük fel, hogy kétcsomópontos fürtje van - *csomópont1* és *2*csomópont . A csomópontkészlet frissítése.
  - Két további csomópont jön létre, *a 3-* as és a *4-es csomópont,* és a taint-ok kerülnek továbbadhatók.
  - Az eredeti *csomópont1* és *node2* törlődik.

- **Virtuálisgép-méretezési csoport támogatása nélkül lévő fürtök**
  - Ismét tegyük fel, hogy van egy kétcsomópontos fürtje - *csomópont1* és *csomópont2*. A frissítés során létrejön egy további csomópont (*csomópont3*).
  - A *csomópont 1-es* taint-jai a *3-as csomópontra*lesznek alkalmazva, majd a *csomópont1* törlődik.
  - Egy másik új csomópont jön létre (a név *: node1*, mivel az előző *csomópont1* törölve lett), és a *csomópont2* taints az új *csomópontra1.* Ezután a *csomópont2* törlődik.
  - Lényegében *node1* lesz *node3*, és *node2* lesz *node1*.

Az AKS-ben lévő csomópontkészlet méretezésekor a taints és a tűrések nem szándékosan vihetők át.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Vezérlőpod ütemezése csomópontválasztók és affinitás használatával

**Ajánlott eljárások –** a csomópontokon lévő podok ütemezésének szabályozása csomópontválasztók, csomópont-affinitás vagy podok közötti affinitás használatával. Ezek a beállítások lehetővé teszik a Kubernetes-ütemező számára a számítási feladatok logikai elkülönítését, például a csomópont hardverei szerint.

A taints és a taszlerációk logikailag elkülönítésére használják az erőforrásokat egy kemény cut-off - ha a pod nem tolerálja a csomópont romlottság, ez nem van ütemezve a csomóponton. Egy másik megközelítés a csomópont-választók használata. Címkéket címkéket, például a helyileg csatlakoztatott SSD-tároló vagy nagy mennyiségű memória jelzésére, majd a pod specifikációjában csomópontválasztót határozhat meg. Kubernetes majd ütemezi ezeket a podokat egy megfelelő csomóponton. A tűrésekkel ellentétben a megfelelő csomópontválasztó nélküli podok ütemezhetők a címkézett csomópontokon. Ez a viselkedés lehetővé teszi a csomópontok nem használt erőforrásainak felhasználását, de elsőbbséget biztosít a megfelelő csomópontválasztót meghatározó podok számára.

Nézzünk egy példát a nagy mennyiségű memóriával rendelkező csomópontokra. Ezek a csomópontok előnyben részesíthetik a nagy mennyiségű memóriát kérő podokat. Győződjön meg arról, hogy az erőforrások nem tétlenül, azt is lehetővé teszi, hogy más podok futtatásához.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

A pod specifikációja `nodeSelector` ezután hozzáadja a tulajdonságot egy csomópontválasztó definiálásához, amely megfelel a csomóponton beállított címkekészletnek:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
    nodeSelector:
      hardware: highmem
```

Ha ezeket az ütemező beállításokat használja, az alkalmazásfejlesztőkkel és -tulajdonosokkal együttműködve lehetővé teszi számukra a podspecifikációk helyes meghatározását.

A csomópontválasztók használatáról a [Podok hozzárendelése csomópontokhoz][k8s-node-selector]című témakörben talál további információt.

### <a name="node-affinity"></a>Csomópont-affinitás

A csomópont-választó egy alapvető módja a podok hozzárendelésének egy adott csomóponthoz. Nagyobb rugalmasság érhető el a *csomópont-affinitással.* A csomópont-affinitással megadhatja, hogy mi történik, ha a pod nem egyeztethető egy csomón. *Megkövetelheti,* hogy a Kubernetes-ütemező egy podot egy címkézett állomással egyezzen meg. Vagy *inkább* egy egyezést, de lehetővé teszi, hogy a pod ütemezve egy másik gazdagép, ha nem egyezés érhető el.

A következő példa a csomópont-affinitást *a szükségesre állítja: Az Ütemezésfigyelmen kívülavégrehajtás során.* Ez az affinitás megköveteli, hogy a Kubernetes-ütemezés egy megfelelő címkével rendelkező csomópontot használjon. Ha nincs elérhető csomópont, a podnak meg kell várnia az ütemezés folytatását. Ahhoz, hogy a pod ütemezve egy másik csomóponton, akkor ehelyett állítsa be az értéket *preferáltDuringSchedulingIgnoreDuringExecution*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

A *setting IgnoreDuringExecution* része azt jelzi, hogy ha a csomópont címkéi megváltoznak, a pod nem szabad kizárni a csomópontról. A Kubernetes-ütemező csak az ütemezett új podok frissített csomópontcímkéit használja, a csomópontokon már ütemezett podokat nem.

További információ: [Affinity and anti-affinity][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Inter-pod affinitás és anti-affinitás

A Kubernetes-ütemező logikailag elkülöníti a számítási feladatok egy utolsó megközelítése a podok közötti affinitás vagy affinitás használatával. A beállítások határozzák meg, hogy a podok *nem* kell ütemezni egy csomóponton, amely rendelkezik egy meglévő egyező pod, vagy hogy ütemezni *kell* őket. Alapértelmezés szerint a Kubernetes-ütemező több podot próbál ütemezni egy replikakészletben a csomópontok között. A viselkedés körül pontosabb szabályokat határozhat meg.

Egy jó példa egy olyan webalkalmazás, amely egy Azure-gyorsítótárat is használ a Redis számára. Pod affinitáselleni szabályok használatával kérheti, hogy a Kubernetes-ütemező replikák at osztja el a csomópontok között. Ezután használhatja affinitási szabályok győződjön meg arról, hogy minden webalkalmazás-összetevő ütemezve ugyanazon az állomáson, mint a megfelelő gyorsítótár. A podok csomópontok közötti eloszlása a következő példához hasonlóan néz ki:

| **1. csomópont** | **2. csomópont** | **3. csomópont** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| gyorsítótár-1    | gyorsítótár-2    | gyorsítótár-3    |

Ez a példa egy összetettebb központi telepítés, mint a csomópont-választók vagy csomópont-affinitás használata. A központi telepítés segítségével szabályozhatja, hogy a Kubernetes hogyan ütemezi a podokat a csomópontokon, és logikailag elkülönítheti az erőforrásokat. A teljes példa erre a webalkalmazás az Azure Cache for Redis példa, lásd: [Közösen keresse podok ugyanazon a csomóponton.][k8s-pod-affinity]

## <a name="next-steps"></a>További lépések

Ez a cikk a Kubernetes-ütemező speciális szolgáltatásaira összpontosított. Az AKS fürtműveleteiről az alábbi gyakorlati tanácsokban talál további információt:

* [Több-bérlős működés és fürtelkülönítés][aks-best-practices-scheduler]
* [Egyszerű Kubernetes ütemező funkciók][aks-best-practices-scheduler]
* [Hitelesítés és engedélyezés][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
