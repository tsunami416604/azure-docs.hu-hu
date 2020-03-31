---
title: Operátori gyakorlati tanácsok – Fürtelkülönítés az Azure Kubernetes-szolgáltatásokban (AKS)
description: Ismerje meg a fürtüzemeltető ajánlott elkülönítési gyakorlati az Azure Kubernetes szolgáltatás (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 228b856d5c5ffa2bfac7df12094667e02f797690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594855"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a fürtelkülönítéshez az Azure Kubernetes-szolgáltatásban (AKS)

A fürtök kezelése az Azure Kubernetes-szolgáltatás (AKS) kezelése során gyakran kell elkülöníteni a csapatokat és a számítási feladatokat. Az AKS rugalmasságot biztosít a több-bérlős fürtök futtatásában és az erőforrások elkülönítésében. A Kubernetes-be történő befektetés maximalizálása érdekében ezeket a több-bérlős és elkülönítési funkciókat meg kell érteni és végre kell hajtani.

Ez az ajánlott eljárások cikk a fürtoperátorok elkülönítésére összpontosít. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Több-bérlős fürtök tervezése és az erőforrások elkülönítése
> * Logikai vagy fizikai elkülönítés használata az AKS-fürtökben

## <a name="design-clusters-for-multi-tenancy"></a>Fürtök tervezése többbérleti szerződéshez

A Kubernetes lehetővé teszi az egyazon fürtön található csapatok és számítási feladatok logikai elkülönítését. Arra kell törekedni, hogy az egyes munkacsoportok igényei alapján a lehető legkevesebb jogosultság legyen kiosztva. A Kubernetes [névtere][k8s-namespaces] logikai elkülönítési határt hoz létre. A Kubernetes további funkciói és az elkülönítéssel és a több-bérlős foglalásokkal kapcsolatos szempontok a következő területek:

* **Az ütemezés** magában foglalja az alapvető funkciók, például az erőforráskvóták és a pod megszakítási költségvetések használatát. Ezekről a szolgáltatásokról az [AKS alapvető ütemezőszolgáltatásaival kapcsolatos gyakorlati tanácsok][aks-best-practices-scheduler]című témakörben talál további információt.
  * A fejlettebb ütemező funkciók közé tartoznak a taints és a tolerations, csomópont választók, valamint a csomópont és a pod affinitás vagy affinitás. Ezekről a szolgáltatásokról az [AKS speciális ütemezőszolgáltatásainak gyakorlati tanácsai című témakörben][aks-best-practices-advanced-scheduler]talál további információt.
* **A hálózatkezelés** magában foglalja a hálózati házirendek használatát a podok be- és kiáramlásának szabályozására.
* **A hitelesítés és az engedélyezés** közé tartozik a szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure Active Directory (AD) integráció, pod identitások és titkos kulcsok az Azure Key Vault. Ezekről a szolgáltatásokról az [AKS-hitelesítéssel és engedélyezéssel kapcsolatos gyakorlati tanácsok][aks-best-practices-identity]című témakörben talál további információt.
* **A tárolók** közé tartozik a pod biztonsági házirendek, pod biztonsági környezetek, a rendszerképek és a biztonsági rések vizsgálata. Azt is jelenti, app armor vagy Seccomp (Secure Computing) korlátozza konténer hozzáférést a mögöttes csomópont.

## <a name="logically-isolate-clusters"></a>A fürtök logikai elkülönítése

**Ajánlott eljárásokra vonatkozó útmutatás** – Logikai elkülönítés használatával különcsoportok és projektek. Próbálja meg minimalizálni a csapatok vagy alkalmazások elkülönítésére telepített fizikai AKS-fürtök számát.

A logikai elkülönítés esetén egyetlen AKS-fürt több számítási feladathoz, csapathoz vagy környezethez is használható. Kubernetes [névterek][k8s-namespaces] alkotják a számítási feladatok és erőforrások logikai elkülönítési határát.

![Kubernetes-fürt logikai elkülönítése az AKS-ben](media/operator-best-practices-cluster-isolation/logical-isolation.png)

A fürtök logikai elkülönítése általában nagyobb pod sűrűséget biztosít, mint a fizikailag elkülönített fürtök. Kevesebb számítási kapacitás van, amely tétlenül ül a fürtben. A Kubernetes-fürt automatikus skálázóval kombinálva skálázhatja a csomópontok számát fel- vagy leaz igényeknek megfelelően. Ez az ajánlott eljárás az automatikus skálázás lehetővé teszi, hogy csak a szükséges csomópontok számát futtatja, és minimalizálja a költségeket.

Kubernetes környezetek, az AKS vagy máshol, nem teljesen biztonságos az ellenséges több-bérlős használat. Több-bérlős környezetben több bérlő dolgozik egy közös, megosztott infrastruktúrán. Ennek eredményeképpen, ha az összes bérlő nem megbízható, további tervezést kell végeznie, hogy elkerülje, hogy egy bérlő befolyásolja egy másik biztonságát és szolgáltatását. További biztonsági funkciók, például *pod biztonsági házirend* és a részletes szerepkör-alapú hozzáférés-vezérlés (RBAC) a csomópontok megnehezítik a biztonsági réseket. Azonban a valódi biztonság ellenséges több-bérlős számítási feladatok futtatásakor, a hipervizor az egyetlen biztonsági szint, amelyben meg kell bíznia. A Kubernetes biztonsági tartománya a teljes fürtlesz, nem egy adott csomópont. Az ilyen típusú ellenséges több-bérlős számítási feladatok, fizikailag elkülönített fürtök használata kell használnia.

## <a name="physically-isolate-clusters"></a>Fürtök fizikai elkülönítése

**Ajánlott eljárásokra vonatkozó útmutatás** – Minimalizálja a fizikai elkülönítés használatát minden egyes csoport vagy alkalmazás központi telepítése esetén. Ehelyett használja *a logikai* elkülönítést, ahogy azt az előző szakaszban tárgyaltuk.

A fürtelkülönítés gyakori megközelítése a fizikailag különálló AKS-fürtök használata. Ebben az elkülönítési modellben a csapatok vagy a számítási feladatok saját AKS-fürthöz vannak rendelve. Ez a megközelítés gyakran úgy néz ki, mint a legegyszerűbb módja a számítási feladatok vagy csapatok elkülönítésének, de további felügyeleti és pénzügyi többletterhelést ad hozzá. Most már fenn kell tartania ezeket a több fürtöt, és külön-külön kell biztosítania a hozzáférést és az engedélyek hozzárendelését. Az egyes csomópontokért is számlázunk.

![Az egyes Kubernetes-fürtök fizikai elkülönítése az AKS-ben](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fizikailag elkülönített fürtök általában alacsony pod sűrűsége. Mivel minden csapat vagy számítási feladatok saját AKS-fürttel rendelkeznek, a fürt gyakran túlvannak kiépítve számítási erőforrásokkal. Gyakran előfordul, hogy néhány podok vannak ütemezve ezeken a csomópontokon. A csomópontok kihasználatlan kapacitása nem használható más csapatok által fejlesztés alatt álló alkalmazásokhoz vagy szolgáltatásokhoz. Ezek a felesleges erőforrások hozzájárulnak a fizikailag különálló fürtök többletköltségeihez.

## <a name="next-steps"></a>További lépések

Ez a cikk a fürtelkülönítésre összpontosított. Az AKS fürtműveleteiről az alábbi gyakorlati tanácsokban talál további információt:

* [Egyszerű Kubernetes ütemező funkciók][aks-best-practices-scheduler]
* [Speciális Kubernetes ütemező funkciók][aks-best-practices-advanced-scheduler]
* [Hitelesítés és engedélyezés][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
