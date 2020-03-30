---
title: Operátori gyakorlati tanácsok – Alapvető ütemező funkciók az Azure Kubernetes-szolgáltatásokban (AKS)
description: Ismerje meg a fürtoperátor okainak gyakorlati tanácsait az alapvető ütemező funkciók, például az erőforráskvóták és a pod megszakítási költségvetések használatával az Azure Kubernetes szolgáltatásban (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cccc476a944b28d24c53a947e434d465c94f94ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126572"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes-szolgáltatás (AKS) alapvető ütemező funkcióinak gyakorlati tanácsok

A fürtök kezelése az Azure Kubernetes-szolgáltatás (AKS) kezelése során gyakran kell elkülöníteni a csapatokat és a számítási feladatokat. A Kubernetes-ütemező olyan funkciókat biztosít, amelyek lehetővé teszik a számítási erőforrások elosztásának szabályozását, vagy a karbantartási események hatásának korlátozását.

Ez az ajánlott eljárások cikk a Fürt-üzemeltetők alapvető Kubernetes ütemezési szolgáltatásaira összpontosít. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Erőforráskvóták használata meghatározott mennyiségű erőforrás biztosításához csapatoknak vagy munkaterheléseknek
> * Korlátozza az ütemezett karbantartás hatását a pod megszakítási költségvetések használatával
> * Hiányzó pod erőforrás-kérelmek és `kube-advisor` korlátozások ellenőrzése az eszközzel

## <a name="enforce-resource-quotas"></a>Erőforráskvóták kényszerítése

**Ajánlott eljárásokra vonatkozó útmutatás** – Erőforráskvóták tervezése és alkalmazása a névtér szintjén. Ha a podok nem határoznak meg erőforrás-kérelmeket és korlátokat, utasítsa el a központi telepítést. Figyelje az erőforrás-használatot, és szükség szerint módosítsa a kvótákat.

Az erőforrás-kérelmek és korlátok a pod specifikációjába kerülnek. Ezeket a korlátokat a Kubernetes-ütemező használja a központi telepítés idején egy elérhető csomópont megkereséséhez a fürtben. Ezek a korlátok és kérések az egyes pod okán működnek. Az értékek meghatározásáról további információt a [Pod erőforrás-kérések és -korlátok megadása című][resource-limits] témakörben talál.

Ha erőforrásokat szeretne lefoglalni és korlátozni egy fejlesztői csoportban vagy projektben, *erőforráskvótákat kell használnia.* Ezek a kvóták névtéren vannak definiálva, és a kvóták beállítására használhatók a következő képpen:

* **Számítási erőforrások**, például a PROCESSZOR és a memória, vagy gpu-k.
* **A tárolási erőforrások**tartalmazzák az adott tárolóosztály köteteinek teljes számát vagy lemezterület-mennyiségét.
* **Az objektumok száma**, például a titkos kulcsok, szolgáltatások vagy munkahelyek maximális száma hozható létre.

A Kubernetes nem véglegesíti túl az erőforrásokat. Miután az erőforrás-kérelmek vagy korlátok összesített összege túllépi a hozzárendelt kvótát, további telepítések nem sikeresek.

Erőforráskvóták definiálásakor a névtérben létrehozott összes podnak korlátokat vagy kéréseket kell megadnia a pod specifikációiban. Ha nem adják meg ezeket az értékeket, elutasíthatja a központi telepítést. Ehelyett beállíthatja a [névtér alapértelmezett kéréseit és korlátait.][configure-default-quotas]

A következő példa: *dev-app-team-quotas.yaml* nevű YAML-jegyzékfájl összesen *10* processzort, *20G* memóriát és *10* podot állít be:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Ez az erőforráskvóta a névtér megadásával alkalmazható, például *a fejlesztői alkalmazások:*

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Együttműködve az alkalmazásfejlesztőkkel és -tulajdonosokkal az igényeik megértéséhez és a megfelelő erőforráskvóták alkalmazásához.

A rendelkezésre álló erőforrás-objektumokról, hatókörökről és prioritásokról a [Kubernetes erőforráskvótái][k8s-resource-quotas]című témakörben talál további információt.

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Pod megszakítási költségvetések használatával a rendelkezésre állás megtervezése

**Ajánlott eljárások –** Az alkalmazások rendelkezésre állásának fenntartása érdekében határozza pod megszakítási költségvetések (PDF) győződjön meg arról, hogy a fürtben elérhető minimális számú podok.

Két zavaró esemény okozza a hüvelyek eltávolítását:

* *A nem önkéntes megszakítások* olyan események, amelyek kívül esnek a fürtüzemeltető vagy az alkalmazás tulajdonosának tipikus ellenőrzésén.
  * Ezek a nem szándékos zavarok közé tartozik a hardverhiba a fizikai gépen, a kernel pánik, vagy a csomópont VM törlése
* *Az önkéntes megszakítások* a fürt üzemeltetője vagy az alkalmazás tulajdonosa által kért események.
  * Ezek az önkéntes megszakítások közé tartozik a fürtfrissítések, egy frissített központi telepítési sablon, vagy véletlenül egy pod törlése.

A nem önkéntes megszakítások lehet csökkenteni a podok több replikák egy központi telepítésben használatával. Több csomópont futtatása az AKS-fürtben is segít ezeket a nem önkéntes megszakítások. Önkéntes megszakítások esetén a Kubernetes *pod megszakítási költségvetéseket* biztosít, amelyek lehetővé teszik a fürtoperátor számára, hogy meghatározza a minimálisan elérhető vagy maximálisan elérhető erőforrásszámot. Ezek a pod megszakítási költségvetések segítségével megtervezheti, hogyan üzemelő példányok vagy replikakészletek reagálnak, ha egy önkéntes megszakítási esemény bekövetkezik.

Ha egy fürtet frissíteni kell, vagy egy központi telepítési sablont frissíteni kell, a Kubernetes-ütemező gondoskodik arról, hogy további podok vannak ütemezve más csomópontokon, mielőtt az önkéntes megszakítási események folytatódhatnak. Az ütemező megvárja, mielőtt egy csomópont újraindul, amíg a megadott számú podok sikeresen ütemezve a fürt más csomópontjain.

Nézzünk egy példát egy replika készlet öt podok, amelyek az NGINX fut. A kópiakészletben lévő hüvelyek hozzá vannak rendelve a címkét. `app: nginx-frontend` Egy önkéntes megszakítási esemény, például a fürt frissítése során győződjön meg arról, hogy legalább három podok továbbra is futnak. A *PodDisruptionBudget* objektum következő YAML-jegyzékfájlja határozza meg a következő követelményeket:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

Egy százalékot is megadhat, például *60%-ot,* amely lehetővé teszi a replikakészlet automatikus kompenzálását a podok számának növelése.

A kópiakészletben legfeljebb több nem elérhető példányt adhat meg. Ismét a maximálisan nem elérhető podok százalékos aránya is megadható. A következő pod megszakítási költségvetés YAML-jegyzékfájl határozza meg, hogy a replikakészletben legfeljebb két pod nem érhető el:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

Miután a pod megszakítási költségvetés definiálva van, hozza létre az AKS-fürtben, mint bármely más Kubernetes objektum:

```console
kubectl apply -f nginx-pdb.yaml
```

Együttműködve az alkalmazásfejlesztők kel és a tulajdonosok, hogy megértsék az igényeiket, és alkalmazza a megfelelő pod megszakítási költségvetések.

A pod megszakítási költségvetések használatával kapcsolatos további információkért [lásd: Az alkalmazás megszakítási költségkeretének megadása.][k8s-pdbs]

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Rendszeresen ellenőrizze a kube-advisor fürtproblémáit

**Ajánlott eljárások –** Rendszeresen futtassa a nyílt forráskódú eszköz legújabb verzióját `kube-advisor` a fürtben felmerülő problémák észleléséhez. Ha erőforráskvótákat alkalmaz egy meglévő AKS-fürtön, először futtassa `kube-advisor` azokat a podokat, amelyek nem rendelkeznek erőforrás-kérelmekkel és korlátokkal.

A [kube-advisor][kube-advisor] eszköz egy társított AKS nyílt forráskódú projekt, amely megvizsgálja a Kubernetes-fürtöt, és jelentéseket készít a talált problémákról. Egy hasznos ellenőrzés, hogy azonosítsa a podok, amelyek nem rendelkeznek erőforrás-kérelmek és korlátok érvényben.

A kube-advisor eszköz jelentést tehet az erőforrás-kérelemről, és a PodSpecs for Windows alkalmazások, valamint a Linux-alkalmazások hiányzó korlátairól, de maga a kube-advisor eszköz egy Linux podon kell ütemezve. Ütemezheti a pod egy adott operációs rendszerrel rendelkező csomópontkészleten a pod konfigurációjában lévő [csomópontválasztó][k8s-node-selector] használatával.

Egy AKS-fürt, amely több fejlesztői csapatok és alkalmazások, nehéz lehet nyomon követni podok nélkül ezeket az erőforrás-kérelmeket és korlátokat beállítva. Ajánlott eljárásként rendszeresen `kube-advisor` futtassa az AKS-fürtökön, különösen akkor, ha nem rendel erőforráskvótákat a névterekhez.

## <a name="next-steps"></a>További lépések

Ez a cikk a Kubernetes ütemező alapvető szolgáltatásaira összpontosított. Az AKS fürtműveleteiről az alábbi gyakorlati tanácsokban talál további információt:

* [Több-bérlős működés és fürtelkülönítés][aks-best-practices-cluster-isolation]
* [Speciális Kubernetes ütemező funkciók][aks-best-practices-advanced-scheduler]
* [Hitelesítés és engedélyezés][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
