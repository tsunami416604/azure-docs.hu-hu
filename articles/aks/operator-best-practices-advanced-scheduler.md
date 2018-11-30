---
title: Operátor ajánlott eljárások – speciális scheduler szolgáltatások az Azure Kubernetes-szolgáltatások (AKS)
description: Ismerje meg, a fürt operátor használatának ajánlott eljárásai elkerülésére és a tolerations, a csomópont választók és a kapcsolat, vagy a pod közötti kapcsolat és a affinitást speciális scheduler funkciókat az Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 3fa2183a5bb3239059c349e8417aeb52553829cf
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428983"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Gyakorlati tanácsok a speciális scheduler funkciók az Azure Kubernetes Service (AKS)

Kezelheti a fürtöket az Azure Kubernetes Service (AKS), gyakran kell elkülöníteni a teams és a számítási feladatokhoz. A Kubernetes-ütemező speciális szolgáltatásokat kínál, amelyekkel meghatározhatja, melyik podok ütemezhetők az egyes csomópontok, vagy hogyan az alkalmazások megfelelően is több pod a fürt elosztva. 

Ez – gyakorlati tanácsok cikk ütemezési összetevők adhatnak az operátoroknak fürt speciális Kubernetes összpontosít. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Használat elkerülésére, valamint milyen podok korlátozása tolerations ütemezett csomópontokon
> * Részesítik előnyben podok csomópont választók vagy csomópont-affinitásának futtathat az egyes csomópontok
> * Vegyes egymástól vagy a csoport együttesen podok közötti pod affinitás vagy affinitást megakadályozó beállítása

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Adja meg a dedikált csomópontok elkerülésére, valamint tolerations használatával

**Ajánlott eljárásokkal kapcsolatos útmutatás** – erőforrás-igényes alkalmazásokhoz, bejövő vezérlők, például adott csomópontjaihoz hozzáférést korlátozza. Tartsa elérhető megkövetelő számítási feladatokhoz csomópont erőforrásokat, és nem engedélyezi, hogy más számítási feladatok a csomóponton.

Az AKS-fürt létrehozásakor telepíthet csomópontok GPU-támogatással vagy egy nagy teljesítményű processzorokkal rendelkeznek. Ezek a csomópontok gyakran használják a nagyméretű adatfeldolgozási számítási feladatokhoz, például a machine learning (gépi tanulás) vagy a mesterséges intelligencia (AI). Mivel az ilyen típusú hardver általában egy költséges csomópont-erőforrást helyezhet üzembe, a számítási feladatok, amelyek ezeken a csomópontokon ütemezett korlátozza. Ehelyett érdemes elkülönítenie az egyes csomópontok bejövő szolgáltatások futtatásához, és más számítási feladatok megakadályozása a fürtben.

A Kubernetes a scheduler használatával elkerülésére, valamint tolerations milyen számítási feladatokat futtathat a csomópontok korlátozhatják.

* A **vágófelülettel** alkalmazott egy csomópont, amely jelzi, hogy csak adott podok ütemezett rajtuk.
* A **toleration** a rendszer ezután alkalmazza, amely lehetővé teszi, hogy podot *lekérés* egy csomópont mellékíz.

Amikor telepít egy pod egy AKS-fürtöt, Kubernetes csak ütemezi csomópontokon podok, ahol egy toleration igazítva van-e a mellékíz. Tegyük fel fel, hogy az AKS-fürt csomópontok grafikus processzort tartalmaz egy nodepool, támogatja. Név, például meghatározhat *gpu*, majd ütemezés értékét. Ha ez az érték *NoSchedule*, a Kubernetes-ütemező nem lehet ütemezni a csomóponton podok, ha a pod nem adja meg a megfelelő toleration.

```console
kubectl taint node aks-nodepool1 gpu:NoSchedule
```

A rendszer összes csomópontra alkalmazza a mellékíz majd egy toleration az meghatározott a pod szabvány, amely lehetővé teszi, hogy ütemezése a csomópontokon. Az alábbi példa meghatározza az `key: gpu` és `effect: NoSchedule` elviselni a alkalmazni a csomópontra az előző lépésben mellékíz:

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
  - key: "gpu"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
```

Ha a pod üzembe lett helyezve, például `kubectl apply -f gpu-toleration.yaml`, Kubernetes sikeresen ütemezhet a csomópontokon a pod a alkalmazni mellékíz együtt. A logikai elkülönítés lehetővé teszi egy fürtön belüli erőforrásokhoz való hozzáférés szabályozása.

Amikor alkalmazza elkerülésére, az alkalmazásfejlesztők és tulajdonosok, hogy azok tudjanak határozhat meg a szükséges tolerations telepítések együttműködve.

Elkerülésére, valamint tolerations kapcsolatos további információkért lásd: [elkerülésére, valamint tolerations alkalmazása][k8s-taints-tolerations].

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Vezérlő pod ütemezés csomópont választók-kapcsolat használatával

**Ajánlott eljárásokkal kapcsolatos útmutatás** – szabályozza a podok csomópont választók, csomópont-affinitásának használatával csomópontokon vezénylését vagy közötti pod affinitás. Ezek a beállítások lehetővé teszik a Kubernetes-ütemező logikailag számítási feladatok elkülönítésére, mint például a csomópont a hardverrel.

Elkerülésére, valamint tolerations logikailag elkülönítésére szolgálnak a rögzített küszöbérték - erőforrásokat a pod ugyan nem egy csomópont mellékíz, ha nincs-e ütemezve a csomóponton. Egy alternatív módszer, hogy használjon csomópont. A helyileg csatlakoztatott SSD-tárolás vagy jelentős mennyiségű memóriát, és megadhatja, a pod specifikációban egy csomópont-választó például címkét csomópont esetén. Kubernetes majd ezeket a hozzá tartozó csomópont podok ütemezi. Ellentétben tolerations anélkül, hogy a megfelelő csomópont választó podok ütemezhető címkézett csomópontokon. Ez a viselkedés lehetővé teszi, hogy a nem használt erőforrások felhasználását, a csomóponton, de biztosít, amelyek meghatározzák a hozzá tartozó csomópont-választó podok prioritás.

Nagy mennyiségű memóriával rendelkező vegyünk egy példát a csomópontok. Ezek a csomópontok adhat a rendszer nagy mennyiségű memóriát igénylő podok előnyben részesítése. Győződjön meg arról, hogy az erőforrások nem található tétlen, a is lehetővé teszik más podok futtatásához.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

A pod megadása majd hozzáadja a `nodeSelector` csomópont nastavit vlastnost meghatározásához egy csomópont-választó, amely megfelel a címkét:

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

Ezek a scheduler a beállítások használatakor az alkalmazásfejlesztők és tulajdonosok, hogy azok tudjanak a pod-specifikációknak megfelelően meghatározásához együttműködve.

Csomópont választók használatával kapcsolatos további információkért lásd: [csomóponthoz való hozzárendelése Podok][k8s-node-selector].

### <a name="node-affinity"></a>Csomópont-affinitásának

Egy csomópont-választó alapvető módja a podok hozzárendelése egy adott csomópont. Rugalmasabb elérhető használatával *csomópont-affinitásának*. A csomópont-affinitásának meghatározhatja, mi történik, ha a pod nem egyeztethető össze a csomópont. Is *megkövetelése* , hogy a Kubernetes-ütemező podot megegyezik a feliratozott gazdagépre. Is *inkább* egyezés, de a pod ütemezhető, egy másik gazdagép engedélyezése, ha nincs egyezés érhető el.

Az alábbi példa a csomópont-affinitásának beállítása *requiredDuringSchedulingIgnoredDuringExecution*. Ez a kapcsolat szükséges a Kubernetes ütemezés egy csomópont használata a hozzájuk tartozó címkéket. Ha nem csomópont nem érhető el, a pod azt várja meg, a folytatáshoz ütemezés. Ahhoz, hogy egy másik csomóponton ütemezni a pod, inkább az értéket és megadható *preferredDuringScheduledIgnoreDuringExecution*:

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

A *IgnoredDuringExecution* része a beállítás azt jelzi, hogy a csomópont címkék módosítása, ha a pod nem zárható ki a csomópontból. A Kubernetes-ütemező csak új podok lett ütemezve, nem a csomóponton már ütemezve podok a frissített csomópontoknak címkéket használ.

További információkért lásd: [affinitás és affinitást][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>A pod közötti kapcsolat és affinitást megakadályozó beállítása

A Kubernetes a Scheduler logikailag a számítási feladatok elkülönítésére az egyik utolsó megközelítés közötti pod affinitás vagy affinitást használ. A beállítások határozzák meg, hogy a podok *nem* ütemezhetők, amely rendelkezik egy meglévő megfelelő pod csomópont, vagy adott ezek *kell* ütemezhető. Alapértelmezés szerint a Kubernetes-ütemező próbál egy replikát, állítsa be a csomópontok között több podok ütemezése. E probléma részletesebb szabályozás határozhatja meg.

Egy jó példa egy webalkalmazás, Redis Cache-gyorsítótárhoz is használó. Podok affinitást szabályok segítségével kérés, hogy a Kubernetes-ütemező replikák elosztja a csomópontok között. Ezek közül ise affinitás szabályokat, győződjön meg arról, hogy egyes webes alkalmazás-összetevők ugyanazon a gazdagépen a megfelelő gyorsítótárként van ütemezve. Podok csomópontok közötti elosztása az alábbi példához hasonlóan néz ki:

| **1. csomópont** | **2. csomópont** | **3. csomópont** |
|------------|------------|------------|
| webalkalmazás-1   | webalkalmazás-2   | WebApp – 3   |
| gyorsítótár-1    | gyorsítótár-2    | gyorsítótár-3    |

Ebben a példában az összetettebb üzemelő használatát, mint a csomópont választók vagy csomópont-affinitásának. Az üzembe helyezési lehetőséget biztosít, hogyan Kubernetes ütemezi a podok csomópontokon felett ellenőrzést, és logikailag is erőforrások elkülönítése. Egy teljes példa a webalkalmazás Redis cache példát: [ugyanazon a csomóponton podok elhelyezése][k8s-pod-affinity].

## <a name="next-steps"></a>További lépések

Ez a cikk a Kubernetes-ütemező speciális szolgáltatások összpontosít. Az AKS-fürt műveletekkel kapcsolatos további információkért tekintse meg az alábbi gyakorlati tanácsokat:

* [Több-bérlős és a fürt elkülönítés][aks-best-practices-scheduler]
* [Kubernetes-ütemezési alapszintű funkciók][aks-best-practices-scheduler]
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
