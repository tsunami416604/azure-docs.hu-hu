---
title: Az Azure Kubernetes Service-ben támogatott Kubernetes-verzió
description: Megismerheti a Kubernetes-verzió támogatási szabályzata és életciklus-fürtök az Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: 2d555908007f4e43a38b6d0eff909ef5050878ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069675"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Támogatott az Azure Kubernetes Service (AKS) Kubernetes-verzió

A Kubernetes-közösség körülbelül háromhavonta ad ki alverziókat. Ezek a kiadások új funkciókat és fejlesztéseket tartalmaznak. A javítások kiadásai gyakoribbak (esetenként heti rendszerességűek), és csak az alverziók kritikus hibáinak javítására szolgálnak. A patch-kiadásokkal biztonsági réseket, vagy az ügyfelek és a Kubernetes-alapú éles üzemben futó termékek sok érintő jelentős hibák javításait tartalmazza.

Az AKS célja, hogy igazolja, és a kiadás Kubernetes-verzió új felső kiadási, a kiadás stabilitását vonatkoznak számított 30 napon belül.

## <a name="kubernetes-versions"></a>Kubernetes-verzió

Kubernetes használja a standard [Szemantikus verziószámozást](https://semver.org/) verziószámozási séma. Ez azt jelenti, hogy a Kubernetes minden verziója a számozási sémát követi:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
  1.13.7
```

A verzió található egyes számok azt jelzi, hogy általános kompatibilitási korábbi verziójával:

* Főbb verziók módosítása, ha a nem kompatibilis API megváltozik, vagy a visszamenőleges kompatibilitás valószínűleg megszakadt.
* Alverziót funkció végrehajtott módosítások a többi másodlagos kiadásokhoz visszafelé kompatibilis módosítása
* Javítás verziója visszafelé kompatibilis hibajavítások módosítás történik.

Általánosságban véve a felhasználók endeavor kell futtatni az alverzió futnak, például ha az éles fürtöt a legújabb javítást kiadása *1.13.6* és *1.13.7* a legújabb elérhető javítás elérhető verzió a *1.13* sorozat, frissítsen a *1.13.7* , amint tud a fürt teljes mértékben javítva, valamint támogatott.

## <a name="kubernetes-version-support-policy"></a>A Kubernetes verziótámogatási szabályzata

Az AKS a Kubernetes négy alverzióját támogatja:

* Az aktuális alverzió, amely akkor szabadul fel, az AKS (N)
* Három korábbi alverzió. Mindegyik támogatott alverzió két stabil javítást támogat.

Ez az úgynevezett "N-3"-(N (a legújabb kiadás) – 3 (alverziót)).

Például, ha az AKS vezet be *1.13.x* még ma, a következő verziók támogatást is magukban:

Új alverzió támogatott listája
-----------------        ----------------------
1.13.x 1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b

Ha "x" és "sémaszintjén" és ".b" reprezentatív javítási verzió.

Tekintse meg az alábbi "kommunikációs" kommunikációs megváltozik a verzió és elvárásainak kapcsolatos további információk.

Amikor bevezetett egy új alverzió, a legrégebbi kisebb verziója és a javítás kiadások támogatott elavult, és eltávolítja. Például, ha a jelenleg támogatott verzió lista van:

<a name="supported-version-list"></a>Támogatott verzió lista
----------------------
1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b, 1.9.a, 1.9.b

És az AKS-kiadások 1.13.x, ez azt jelenti, hogy a 1.9.x verziók (az összes 1.9 verzió) törlődni fog, és nem támogatott.

> [!NOTE]
> Ne felejtse el, hogy ügyfeleink futnak a Kubernetes-verzió nem támogatott, ha azok ki kell töltenie a fürt támogatása kérésekor frissítése. Nem támogatott Kubernetes-kiadások rendszert futtató fürtöket nem tartoznak a [AKS támogatja a szabályzatok](https://docs.microsoft.com/azure/aks/support-policies).


A fentiek mellett a alverziót AKS támogatja a két legutóbbi *javítás** egy adott alverzió kiadásaiban. Például adja a következő támogatott verziók:

<a name="supported-version-list"></a>Támogatott verzió lista
----------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5

Ha a felsőbb rétegbeli Kubernetes kiadott 1.12.3 és 1.11.6 és AKS-kiadások azok javítási verzió, a legrégebbi javítás verziója elavult, és eltávolítja és az támogatott verziójú lista válik:

<a name="supported-version-list"></a>Támogatott verzió lista
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*

### <a name="communications"></a>Kommunikáció

* Az új **kisebb** Kubernetes verzióját
  * Összes felhasználó értesítést kap az új verziót, és milyen verziójú törlődni fog.
  * A verzióját futtató ügyfelek **eltávolítandó** értesítést kap arról, hogy **60 napig** frissítése egy támogatott verziójára (például kisebb verzió).
* Az új **javítás** Kubernetes verzióját
  * Összes felhasználó értesítést kap az új javítási verzió felszabadítását, és frissítse a legújabb javítást.
  * Felhasználók **30 napig** frissítése egy újabb, a támogatott javítás kiadásra. Felhasználók **30 napig** frissítése egy támogatott javítás kiadásra a legrégebbi eltávolítása előtt.

Az AKS határozza meg, "" néven kiadott általános rendelkezésre állás érdekében engedélyezve van az összes SLO / minőségben szolgáltatás mérések, és minden régióban elérhető.

> [!NOTE]
> Ügyfelek arról kubernetes verziókban és kivonások, ha egy kisebb verziója elavult/eltávolított felhasználók kapnak 60 napig frissítése egy támogatott verziójára. Javítás kiadásokban esetén ügyfelek kapják 30 napig frissítése egy támogatott verziójára.

Értesítések küldése keresztül:

* [Az AKS kibocsátási megjegyzései](https://aka.ms/aks/releasenotes)
* Az Azure portal értesítések
* [Az Azure frissítési csatorna][azure-update-channel]

### <a name="policy-exceptions"></a>Szabályzat kivételei

Az AKS fenntartja a jogot hozzáadni vagy eltávolítani szeretné, hogy legalább egy kritikus fontosságú éles érintő hibák vagy biztonsági problémák előzetes értesítés nélkül azonosított új vagy meglévő verziók.

Adott javítás verziókban előfordulhat, hogy kihagyja, vagy a programhiba vagy biztonsági probléma súlyosságától függően gyorsított bevezetés.

### <a name="azure-portal-and-cli-default-versions"></a>Az Azure portal és CLI alapértelmezett verzió

Amikor telepít egy AKS-fürtöt a portálon vagy az Azure CLI-vel, a fürt értéke mindig a N-1 alverzió és a legújabb javítást. Például, ha támogatja az AKS *1.13.x*, *1.12.a* + *1.12.b*, *1.11.a*  +   *1.11.b*, *1.10.a* + *1.10b*, az alapértelmezett verzió új fürtök *1.12.b*.

Az AKS N-1 (minor.latestPatch, például 1.12.b) ismert, stabil biztosít az ügyfeleknek az alapértelmezett, és javítani a verzió alapértelmezés szerint.

## <a name="list-currently-supported-versions"></a>Jelenleg a támogatott verziók listája

Ismerje meg, hogy mely verziói érhetők el jelenleg az előfizetés és a régiót, használja a [az aks get-verziók] [ az-aks-get-versions] parancsot. Az alábbi példa felsorolja az elérhető Kubernetes-verzió esetében a *EastUS* régió:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

A kimenet hasonlít a következő példának, amely bemutatja, hogy a Kubernetes-verziót *1.13.5* a legfrissebb verzió érhető el:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.13.5               None available
1.12.7               1.13.5
1.12.6               1.12.7, 1.13.5
1.11.9               1.12.6, 1.12.7
1.11.8               1.11.9, 1.12.6, 1.12.7
1.10.13              1.11.8, 1.11.9
1.10.12              1.10.13, 1.11.8, 1.11.9
```

## <a name="faq"></a>GYIK

**Mi történik, ha egy ügyfél frissíti a Kubernetes-fürthöz egy kisebb verziója nem támogatott?**

Ha a *n-4* verzió, Ön kívüli támogatási, és meg kell adnia frissítése. Ha a n-3 n-4 verzióra történő frissítés sikeres volt, áll a támogatási házirendeken belül. Példa:

- Ha az AKS-verziók a következők *1.13.x*, *1.12.a* + *1.12.b*, *1.11.c*  +  *1.11d*, és *1.10.e* + *1.10F* és a *1.9.g* vagy *1.9.h*, kívüli támogatás áll.
- Ha a frissítés a *1.9.g* vagy *1.9.h* való *1.10.e* vagy *1.10.f* sikeres, visszatér a saját támogatási irányelveik belül.

Frissítés-nél régebbi verziók *n-4* nem támogatottak. Ezekben az esetekben javasoljuk, hogy ügyfeleink új AKS-fürtök létrehozásának és ismételt üzembe helyezése számítási feladataik.

**Mit jelent az "kívüli támogatás"**

"Kívüli támogatás" azt jelenti, hogy a támogatott verziók listája kívül esik a verzióját használja, és meg kell adnia egy támogatott verziójára a fürt frissítésének támogatása kérésekor. Ezenkívül az AKS bármely futásidejű vagy elérhetővé más garanciák fürtök kívül esik a támogatott verziók listája.

**Mi történik, ha egy ügyfél méretezhető alverzió, amely nem támogatott a Kubernetes-fürt?**

Az AKS által nem támogatott alverziót, a méretezés, vagy továbbra is problémamentesen működjenek.

**Egy ügyfél maradhat egy Kubernetes-verziót a végtelen?**

Igen. Azonban a fürt nem szerepel az AKS által támogatott verziókra, ha a fürtön kívül esik az AKS támogatási házirendjeit. Az Azure automatikusan frissítse a fürtöt, vagy törölje.

**Melyik verziót támogatja a master, ha az ügynököt a fürt nem szerepel a támogatott AKS-verziók valamelyikét?**

A fő automatikusan frissül, és a legújabb támogatott verzióját.

## <a name="next-steps"></a>További lépések

A fürt frissítésével kapcsolatos információkért lásd: [Azure Kubernetes Service (AKS)-fürt frissítése][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
