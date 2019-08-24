---
title: Támogatott Kubernetes-verziók az Azure Kubernetes Service-ben
description: Ismerje meg az Azure Kubernetes Service-ben (ak) lévő fürtök Kubernetes-verziójának támogatási szabályzatát és életciklusát
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: 0e26b9b5aee61f399a39c032cdbd2084884538c9
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982714"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Támogatott Kubernetes-verziók az Azure Kubernetes szolgáltatásban (ak)

A Kubernetes-közösség körülbelül háromhavonta ad ki alverziókat. Ezek a kiadások új funkciókat és fejlesztéseket tartalmaznak. A javítások kiadásai gyakoribbak (esetenként heti rendszerességűek), és csak az alverziók kritikus hibáinak javítására szolgálnak. Ezek a javítások a biztonsági rések vagy a nagy mennyiségű, éles környezetben futó, Kubernetes alapuló termékkel kapcsolatos hibákat érintik.

Az AK célja, hogy az új Kubernetes-verziókat egy felsőbb rétegbeli kiadástól számított 30 napon belül hitelesítse és bocsássa ki, a kiadás stabilitásának függvényében.

## <a name="kubernetes-versions"></a>Kubernetes-verziók

A Kubernetes a szabványos [szemantikai verziószámozási](https://semver.org/) sémát használja. Ez azt jelenti, hogy a Kubernetes minden verziója a következő számozási sémát követi:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

A verzióban lévő egyes számok általános kompatibilitást jeleznek az előző verzióval:

* A főverziók akkor változnak, ha a nem kompatibilis API-változások vagy a visszamenőleges kompatibilitás megszakad.
* Az alverziók akkor változnak, ha a funkcionalitás változása visszamenőlegesen kompatibilis a többi másodlagos kiadással.
* A javítási verziók megváltoznak, ha visszamenőlegesen kompatibilis hibajavítás történik.

Általánosságban elmondható, hogy a felhasználóknak futtatniuk kell a futtatott alverzió legújabb javítását, például ha az üzemi fürt a *1.12.14* -on található, és a *1.12.15* az *1,12* sorozat legújabb elérhető javítási verziója. , a *1.12.15* -re kell frissítenie, amint biztos lehet abban, hogy a fürt teljes mértékben kijavított és támogatott.

## <a name="kubernetes-version-support-policy"></a>A Kubernetes verziótámogatási szabályzata

Az AKS a Kubernetes négy alverzióját támogatja:

* Az AK-ban megjelent aktuális másodlagos verzió (N)
* Három korábbi alverzió. Mindegyik támogatott alverzió két stabil javítást támogat.

Ez az úgynevezett "N-3" – (N (legújabb kiadás) – 3 (másodlagos verzió)).

Ha például az AK a *1.13. x-et* mutatja be, a következő verziókhoz nyújt támogatást:

Új alverzió    |    Támogatott verziók listája
-----------------    |    ----------------------
1.13.x               |    1.12. a, 1.12. b, 1.11. a, 1.11. b, 1.10. a, 1.10. b

Ahol az "x" és a ". a" és a ". b" is jellemző patch-verziók.

A verziók változásaival és elvárásaival kapcsolatos tájékoztatásért tekintse meg az alábbi "kommunikáció" című szakaszt.

Új alverzió bevezetését követően a legrégebbi alverzió és a javítások támogatott kiadásai elavultak és törlődnek. Például ha a jelenlegi támogatott verziók listája a következő:

```
1.12.a
1.12.b
1.11.a
1.11.b
1.10.a
1.10.b
1.9.a
1.9.b
```

A és az AK a 1.13. x-et is kibocsátja, ez azt jelenti, hogy az 1,9. x verzió (az összes 1,9-es verzió) el lesz távolítva, és nem támogatott

> [!NOTE]
> Vegye figyelembe, hogy ha az ügyfelek nem támogatott Kubernetes-verziót futtatnak, a rendszer a fürt támogatásának kérelmezése után a frissítésre kéri őket. A nem támogatott Kubernetes-kiadásokat futtató fürtöket az [AK-támogatási szabályzatok](https://docs.microsoft.com/azure/aks/support-policies)nem tartalmazzák.


A fenti alverzión kívül az AK támogatja az adott alverzióhoz tartozó két legújabb *patch** kiadást. Például a következő támogatott verziók miatt:

```
Current Supported Version List
------------------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5
```

Ha a felsőbb rétegbeli Kubernetes kiadta a 1.12.3 és a 1.11.6, és az AK a javítási verziókat, a legrégebbi javítási verziók elavultak és törlődnek, és a támogatott verziók listája a következő lesz:

```
New Supported Version List
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*
```

> [!NOTE]
> Az ügyfeleknek nem kell rögzíteniük a fürt létrehozását, a CI-t vagy más automatizált feladatokat az adott javítási kiadásokhoz. 

### <a name="communications"></a>Kommunikáció

* A Kubernetes új **másodlagos** verzióihoz
  * Az összes felhasználó nyilvánosan értesítést kap az új verzióról, és a rendszer eltávolítja a verziót.
  * Egy új javítási verzió kiadásakor a rendszer a legrégebbi javítást is eltávolítja egyszerre.
  * Az ügyfeleknek a nyilvános értesítés dátumától számítva **60 nap elteltével** frissíteniük kell egy támogatott alverzióra.
* A Kubernetes új **javítócsomag** -verzióihoz
  * Az összes felhasználó értesítést kap az új javítási verzió kiadásáról, és a legújabb patch kiadásra frissít.
  * A felhasználók **30 napig** frissíthetnek újabb, támogatott javítási kiadásra. A felhasználók **30 napon belül** frissítenek egy támogatott patch kiadásra a legrégebbi eltávolítása előtt.

Az AK a "kiadott" állapotot általános rendelkezésre állásként határozza meg, amely minden SLO/minőségi szolgáltatásbeli mérésben engedélyezve van, és minden régióban elérhető.

> [!NOTE]
> Az ügyfelek értesítést kapnak a Kubernetes-verziókról és az elavulás alól, ha egy alverzió elavult/eltávolított felhasználók 60 nap múlva frissülnek egy támogatott kiadásra. A javítási kiadások esetében az ügyfelek 30 napot kapnak a támogatott kiadásokra való frissítéshez.

Az értesítések küldése a következőkön keresztül történik:

* [AK kibocsátási megjegyzései](https://aka.ms/aks/releasenotes)
* Értesítések Azure Portal
* [Azure frissítési csatorna][azure-update-channel]

### <a name="policy-exceptions"></a>Házirend-kivételek

Az AK fenntartja a jogot arra, hogy új/meglévő verziókat adjon hozzá vagy távolítson el, amelyek egy vagy több kritikus termeléssel kapcsolatos hibával vagy biztonsági problémával rendelkeznek, előzetes értesítés nélkül.

Előfordulhat, hogy a rendszer kihagyja az adott javítási kiadásokat, vagy a hiba vagy a biztonsági probléma súlyossága függvényében felgyorsítja a bevezetést.

### <a name="azure-portal-and-cli-default-versions"></a>Azure Portal és CLI alapértelmezett verziói

Ha AK-fürtöt telepít a portálon vagy az Azure CLI-vel, a fürt mindig az N-1 alverzióra és a legújabb javításra van beállítva. Ha például az AK támogatja a *1.13. x-et*, *1.12. a* + 1.12.*b*, *1.11. a* + *1.11. b*, *1.10. a* + *1.10 b*, az új fürtök alapértelmezett verziója *1.12. b* .

Az AK alapértelmezett értéke N-1 (Minor. latestPatch, például 1.12. b), így az ügyfelek számára az ismert, a stabil és a javított verzió alapértelmezés szerint elérhető.

## <a name="list-currently-supported-versions"></a>Jelenleg támogatott verziók listázása

Ha szeretné megtudni, hogy az előfizetéshez és a régióhoz jelenleg milyen verziók érhetők el, használja az az [AK Get-Versions][az-aks-get-versions] parancsot. A következő példa a *EastUS* régió elérhető Kubernetes-verzióit sorolja fel:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

A kimenet a következő példához hasonlóan jelenik meg, amely azt mutatja, hogy a Kubernetes verziója *1.14.6* a legújabb elérhető verzió:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.14.6               None available
1.14.5               1.14.6
1.13.10              1.14.5, 1.14.6
1.13.9               1.13.10, 1.14.5, 1.14.6
1.12.8               1.13.9, 1.13.10
1.12.7               1.12.8, 1.13.9, 1.13.10
1.11.10              1.12.7, 1.12.8
1.11.9               1.11.10, 1.12.7, 1.12.8
1.10.13              1.11.9, 1.11.10
1.10.12              1.10.13, 1.11.9, 1.11.10
```

## <a name="faq"></a>GYIK

**Mi történik, ha egy ügyfél olyan Kubernetes-fürtöt frissít, amely nem támogatott másodlagos verzióval?**

Ha az *n-4* verziót használ, a rendszer nem támogatja a támogatást, és a rendszer a frissítésre kéri. Ha az n-4 verzióról az n-3-ra való frissítés sikeres, akkor már a támogatási szabályzatok között van. Példa:

- Ha a támogatott AK-verziók a következők: *1.13. x*, *1.12. a* + *1.12. b*, *1.11. c* + *1.11 d*, és *1.10. e* + *1.10 f* és Ön *1,9. g* vagy *1,9. h*, a támogatáson kívül van.
- Ha a frissítés *1,9. g* vagy *1,9. h* és *1.10. e* között vagy *1.10. f* sikeres, a támogatási szabályzatokon belül visszakerül.

Az *n-4-* nél régebbi verzióra történő frissítés nem támogatott. Ilyen esetekben javasoljuk, hogy az ügyfelek új AK-fürtöket hozzanak létre, és újra üzembe helyezzük a munkaterheléseket.

**Mit jelent a "nem támogatott" kifejezés**

A "támogatáson kívül" kifejezés azt jelenti, hogy a futtatott verzió a támogatott verziók listáján kívül esik, és a rendszer arra kéri, hogy a támogatás kérelmezése után frissítse a fürtöt egy támogatott verzióra. Emellett az AK nem hajt végre semmilyen futtatókörnyezetet vagy egyéb garanciát a támogatott verziók listáján kívüli fürtökre.

**Mi történik, ha egy ügyfél olyan Kubernetes-fürtöt méretezi, amelynek alverziója nem támogatott?**

Az AK által nem támogatott alverziók esetén a méretezés be-és kilépésével továbbra is problémák nélkül dolgozhat.

**Az ügyfél örökre Kubernetes-verzióban maradhat?**

Igen. Ha azonban a fürt nem az AK által támogatott verziók egyikén található, a fürt kívül esik az AK-támogatási szabályzatok közül. Az Azure nem frissíti automatikusan a fürtöt, vagy nem törli azt.

**Milyen verziót támogat a főkiszolgáló, ha az ügynök-fürt nem szerepel a támogatott AK-verziók egyikében sem?**

A rendszer automatikusan frissíti a főkiszolgálót a legújabb támogatott verzióra.

## <a name="next-steps"></a>További lépések

A fürt frissítésével kapcsolatos további információkért lásd: [Azure Kubernetes Service (ak) fürt frissítése][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
