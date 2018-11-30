---
title: Ajánlott eljárások operátor - fürt elkülönítés az Azure Kubernetes-szolgáltatások (AKS)
description: A fürt operátor ajánlott eljárások az elkülönítés az Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 3a4b62fb16745a3b226bda6c0574812278a34456
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428731"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a fürt elkülönítés az Azure Kubernetes Service (AKS)

Kezelheti a fürtöket az Azure Kubernetes Service (AKS), gyakran kell elkülöníteni a teams és a számítási feladatokhoz. Az AKS, hogyan futtathat több-bérlős fürtöket és erőforrások elkülönítése rugalmasságot biztosít. A Kubernetesben befektetéseit maximalizálhatja, több-bérlős és elkülönítési funkciókhoz kell kell értelmezni és végrehajtani.

Ajánlott eljárások a cikkben a fürt operátorok elkülönítési összpontosít. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Több-bérlős fürtök és az erőforrások elkülönítését tervezése
> * Logikai és fizikai elkülönítését az AKS-fürtök használata

## <a name="design-clusters-for-multi-tenancy"></a>Több-bérlős tervezési-fürtök

Kubernetes biztosít funkciókat, amelyekkel logikailag elkülönítheti a különböző csapatok és a számítási feladatokat ugyanazon fürt. A cél legyen, adja meg a legalacsonyabb jogosultságok, a hatókörön belüli egyes csapat által igényelt erőforrások száma. A [Namespace] [ k8s-namespaces] a Kubernetes létrehoz egy logikai elkülönítési határt. További kubernetes funkciók és az elkülönítési és több-bérlős kapcsolatos szempontok közé tartozik a következő területeken:

* **Ütemezés** beletartozik a erőforráskvóták és pod megszakítás költségvetése alapszintű funkciókat. Ezek a funkciók kapcsolatos további információkért lásd: [ajánlott eljárásai az aks-ben alapszintű ütemezési funkciókat][aks-best-practices-scheduler].
  * Speciális ütemezési szolgáltatásai a elkerülésére és a tolerations, a csomópont választók, és a csomópont- és pod kapcsolat vagy a affinitást. Ezek a funkciók kapcsolatos további információkért lásd: [ajánlott eljárások az aks-ben a scheduler speciális funkciók][aks-best-practices-advanced-scheduler].
* **Hálózatkezelés** is magában foglalja a hálózati házirendeket a podok és a forgalom szabályozásához.
* **Hitelesítés és engedélyezés** tartalmazzák a felhasználót a szerepköralapú hozzáférés-vezérlés (RBAC) és a Azure Active Directory (AD) integrációt, a pod identitások és a titkos kulcsok Azure Key vaultban. Ezek a funkciók kapcsolatos további információkért lásd: [gyakorlati tanácsok a hitelesítés és engedélyezés az aks-ben][aks-best-practices-identity].
* **Tárolók** pod biztonsági házirendek, pod biztonsági környezetben, vizsgálati lemezképek és a biztonsági réseket modulok. Alkalmazás motor vagy Seccompot (biztonságos Computing) használata a tároló hozzáférésének korlátozása az alapul szolgáló csomópont is szükséges.

## <a name="logically-isolate-clusters"></a>Fürtök logikailag elkülönítése

**Ajánlott eljárásokkal kapcsolatos útmutatás** -csapatok és projektek külön logikai elkülönítés használatával. Próbálja meg minimálisra csökkenteni a fizikai üzembe AKS-fürtök számát csapatok vagy alkalmazások elkülönítése.

Logikai elkülönítés egy AKS-fürt több számítási feladatokhoz, csapatok és környezetek esetében használható. Kubernetes [névterek] [ k8s-namespaces] számítási feladatok és erőforrások a logikai elkülönítési határt alkotnak.

![Logikai elkülönítéssel egy Kubernetes-fürt az aks-ben](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Fürtök logikai elkülönítése általában a fizikailag elkülönített fürtök, mint a magasabb pod sűrűségű nyújt. Nincs a fürtben üresjárati kevesebb számítási kapacitás, amely helyezkedik el. A Kubernetes-fürt méretező kombinálva, a csomópontok számát a felfelé vagy lefelé találkozik a növekvő igények szerint skálázhatja. Az ajánlott eljárásokat írják le az automatikus skálázás lehetővé teszi a Futtatás csak a szükséges csomópontok számát, és minimálisra csökkenti a költségeket.

## <a name="physically-isolate-clusters"></a>Fizikailag a fürtök elkülönítése

**Ajánlott eljárásokkal kapcsolatos útmutatás** -minimalizálja a fizikai elkülönítését az egyes külön csapat vagy az alkalmazás központi telepítésének használatát. Ehelyett használjon *logikai* elkülönítés, az előző szakaszban leírt módon.

Fürt elkülönítési általánosan használt megközelítése, hogy fizikailag elkülönülő AKS-fürt. Elkülönítés ebben a modellben csapatok vagy a számítási feladatok vannak hozzárendelve a saját AKS-fürtöt. Ezt a módszert gyakran például a számítási feladatok vagy csoportok elkülönítése a legegyszerűbben úgy tűnik, de további felügyeleti és pénzügyi terhelést. Mostantól ezek több fürt karbantartása kell, és külön-külön hozzáférést és engedélyeket kell. Az egyes csomópontok is díjköteles.

![Fizikai elkülönítése egyéni Kubernetes-fürtök az aks-ben](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fizikailag különálló fürtöket egy alacsony pod sűrűségű általában rendelkeznek. Minden egyes csapat vagy a számítási feladatok rendelkezik saját AKS-fürtöt, a fürt legtöbbször túlterhelt, a számítási erőforrásokat. Podok kis számú gyakran előfordul, a csomópontokon van ütemezve. A csomópontok nem használt kapacitás más csapatokkal az alkalmazáshoz vagy szolgáltatáshoz fejlesztési nem használható. Ezek az erőforrások felesleges járulnak hozzá a további költségek a fizikailag különálló fürtöket.

## <a name="next-steps"></a>További lépések

Ez a cikk a fürt elkülönítési összpontosít. Az AKS-fürt műveletekkel kapcsolatos további információkért tekintse meg az alábbi gyakorlati tanácsokat:

* [Kubernetes-ütemezési alapszintű funkciók][aks-best-practices-scheduler]
* [Kubernetes-ütemező speciális funkciók][aks-best-practices-advanced-scheduler]
* [Hitelesítés és engedélyezés][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
