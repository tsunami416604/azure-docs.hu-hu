---
title: Operátor ajánlott eljárások – alapszintű ütemezési funkciókat az Azure Kubernetes-szolgáltatások (AKS)
description: Ismerje meg a fürt operátor használatának ajánlott eljárásai erőforráskvóták alapszintű ütemezési funkciókat és megszakítás költségvetése Azure Kubernetes Service (AKS) pod
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 8233330973946e552e36a85a11bdbbfb06c739f0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58178135"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások az alapszintű ütemezési funkciókat az Azure Kubernetes Service (AKS)

Kezelheti a fürtöket az Azure Kubernetes Service (AKS), gyakran kell elkülöníteni a teams és a számítási feladatokhoz. A Kubernetes-ütemező lehetővé teszik a számítási erőforrások elosztását, vagy korlátozza a karbantartási események hatásait szolgáltatásokat biztosítja.

Ez – gyakorlati tanácsok cikk ütemezési összetevők az operátorok a fürt alapvető Kubernetes összpontosít. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Erőforráskvóták használatával biztosíthat a csapatok vagy a számítási erőforrások rögzített méretű
> * Korlátozhatják a pod megszakítás költségvetése ütemezett karbantartás
> * Ellenőrizze a hiányzó erőforrás-kérelmek pod és korlátozások használatával a `kube-advisor` eszköz

## <a name="enforce-resource-quotas"></a>Erőforrás-kvóták érvényesítése

**Ajánlott eljárásokkal kapcsolatos útmutatás** – csomag és a névterek szintjén erőforráskvóták a alkalmazni. Podok erőforrás-kérelmek és a korlátok nem ad meg, ha elutasítja a központi telepítést. Erőforrás-használat figyelése, és szükség szerint állítsa be a kvóták.

Erőforrás-kérelmek és a korlátok a pod-specifikáció kerülnek. Ezek a korlátok használják a Kubernetes-ütemező központi telepítéskor az elérhető csomópont található a fürt. Ezek a korlátozások és kérések az egyes pod szintjén működik. Ezek az értékek definiálása kapcsolatos további információkért lásd: [definiálása pod erőforrás-kérelmek és korlátozások][resource-limits]

Foglaljon le, és korlátozhatja a források között egy fejlesztői csapat vagy egy projekt lehetőséget biztosíthat, használjon *erőforráskvóták*. Kvóta névtér meg vannak adva, és kvóták beállítása az alábbi módon használható:

* **A számítási erőforrásokat**, például CPU és memória vagy gpu-kkal.
* **Tárolási erőforrások**, kötetek száma vagy egy adott tárolási osztály lemezterületet tartalmaz.
* **Objektumok száma**, titkos kulcsok maximális számát, például szolgáltatások vagy feladatok hozható létre.

Kubernetes erőforrások nem szükségesnél. Miután a teljes erőforrás-kérelmek vagy korlátok továbbítja a hozzárendelt kvótát, nincs további telepítések sikeres.

Erőforráskvóták határozza meg, ha a névtérben létrehozott összes podok korlátok, és a kérelmeket a saját pod előírásainak kell megadnia. Ha ezek nem adnak ezeket az értékeket, a központi telepítés is elutasítja. Ehelyett [konfigurálása alapértelmezett kérelmek és a egy névtér korlátai][configure-default-quotas].

A következő példa YAML jegyzékfájl nevű *dev-alkalmazás-csapat-quotas.yaml* összesen rögzített korlátja beállítja *10* processzorok, *20Gi* memóriát, és *10*podok:

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

A névtér megadásával az erőforrás-kvótát alkalmazható *dev-alkalmazások*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Működik az alkalmazásfejlesztők és tulajdonosok igényeiknek és a megfelelő erőforrás kvóták érvényesek.

További információ az elérhető erőforrás-objektumok, hatókörök és prioritások: [erőforráskvóták a Kubernetesben][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>A rendelkezésre állást pod megszakítás költségvetése használatával

**Ajánlott eljárásokkal kapcsolatos útmutatás** – az alkalmazások rendelkezésre állásának meghatározása a Pod megszakítás költségvetése (PDBs), győződjön meg arról, hogy a minimálisan szükséges podok érhetők el a fürt.

Nincsenek podok eltávolítani okozó két azokat a káros eseményeket:

* *Kötelező fennakadások* a fürt üzemeltető tipikus befolyásolható esemény vagy az alkalmazás tulajdonosa.
  * Ezek kötelező megszakításait tartalmazzák a fizikai gép, kernelpánikot vagy a törlés, a virtuális gép csomópont hardverhiba
* *Önkéntes fennakadások* olyan események, a fürt operátor által kért vagy az alkalmazás tulajdonosa.
  * Ezek önkéntes fennakadások közé tartozik a fürtfrissítések, egy frissített telepítési sablont vagy véletlenül törli a podot.

A kötelező fennakadások enyhíthetők több a podok-replika használatával. Az AKS-fürtöt több csomóponton futó is segít a ezek kötelező megszakadását. Önkéntes megszakadása, a Kubernetes biztosít *pod megszakítás költségvetése* , amelyek lehetővé teszik, hogy a fürt operátor egy minimális érhető el, vagy a maximális használható erőforrások száma határozza meg. A pod megszakítás költségvetés segítségével tervezheti meg, hogyan rendszerbe állításához vagy replikakészletekhez reagálnak önkéntes megszakítás esemény bekövetkeztekor.

Ha a fürt frissítve lesz, vagy frissíteni a központi telepítési sablont, a Kubernetes-ütemező lehetővé teszi, hogy további podok naponkénti gyakoriságra vannak a többi csomóponton az önkéntes megszakítás események folytatása előtt. Az ütemező várakozzon, mielőtt a csomópont újraindul mindaddig, amíg a megadott számú podok ütemezése sikeresen a fürt többi csomópontjára.

Lássunk egy példát egy nginx-et futtató öt podok replikakészlethez. A replika a podok kell beállítani, mert hozzá rendelve a címke `app: nginx-frontend`. Önkéntes megszakítás események esetén például a fürt frissítése érdemes ellenőrizze, hogy legalább három podok továbbra is futnak. A következő yaml-kódot a jegyzékfájl egy *PodDisruptionBudget* objektuma határozza meg, ezek a követelmények:

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

Azt is megadhatja, százalékként, például *60 %-os*, amely lehetővé teszi, hogy a replika automatikusan kompenzálni állítsa be a podok számát vertikális felskálázása.

Nem érhető el példányok maximális száma a replikakészlethez adhatja meg. Újra a maximális nem érhető el podok százalék is lehet definiálni. A következő pod megszakítás költségvetés YAML jegyzékfájl meghatározza, hogy legfeljebb két podok a replika beállítása nem érhető el:

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

A pod megszakítás költségvetést van definiálva, miután létrehozta az AKS-fürt a csakúgy, mint más Kubernetes-objektum:

```console
kubectl apply -f nginx-pdb.yaml
```

Az alkalmazásfejlesztők és a tulajdonosok, saját igényeinek megfelelő megértéséhez, és alkalmazza a megfelelő pod megszakítás költségvetések dolgozhat.

Pod megszakítás költségvetése használatával kapcsolatos további információkért lásd: [adja meg az alkalmazás megszakítás költségvetési][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Rendszeresen ellenőrzi a kube-advisor fürt problémái

**Ajánlott eljárásokkal kapcsolatos útmutatás** – rendszeresen, a legújabb verzióját futtató `kube-advisor` nyílt forráskódú eszköz, a hibák észleléséhez a fürtben. Ha egy meglévő AKS-fürtre erőforráskvóták alkalmaz, futtassa `kube-advisor` először, amelyek nem rendelkeznek, erőforrás-kérelmek és a meghatározott podok kereséséhez.

A [kube-advisor] [ kube-advisor] eszköz egy társított AKS nyílt forráskódú projekt, amely megvizsgálja egy Kubernetes-fürtöt, és jelentést készít a talált problémákat. Egy hasznos, ellenőrizze, hogy azonosítsa a podok nem rendelkező erőforrás-kérelmek és korlátozások érvényben.

Az AKS-fürt, amelyen több fejlesztői csapatok és alkalmazásokat azok visszakövetését, hogy podok nélkül ezeket az erőforrás-kérelmek, és korlátozza a készlet. Ajánlott eljárásként rendszeresen futtassa `kube-advisor` a AKS-fürtökön, különösen akkor, ha nem rendel hozzá erőforráskvóták névtér használatára korlátozzuk.

## <a name="next-steps"></a>További lépések

Ez a cikk a Kubernetes-ütemezési alapszintű funkcióit összpontosít. Az AKS-fürt műveletekkel kapcsolatos további információkért tekintse meg az alábbi gyakorlati tanácsokat:

* [Több-bérlős és a fürt elkülönítés][aks-best-practices-cluster-isolation]
* [Kubernetes-ütemező speciális funkciók][aks-best-practices-advanced-scheduler]
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
