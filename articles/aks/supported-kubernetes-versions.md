---
title: Támogatott Kubernetes-verziók az Azure Kubernetes Service-ben
description: Ismerje meg az Azure Kubernetes Service-ben (ak) lévő fürtök Kubernetes-verziójának támogatási szabályzatát és életciklusát
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 625b44ae3f8a1d5d474d980693d92840b1317f09
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425765"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Támogatott Kubernetes-verziók az Azure Kubernetes szolgáltatásban (ak)

A Kubernetes-Közösség nagyjából három havonta kibocsátja a kisebb verziókat. Ezek a kiadások új funkciókat és fejlesztési lehetőségeket tartalmaznak. A javítási kiadások gyakoribbak (esetenként hetente), és csak a kritikus hibajavítások számára készültek egy kisebb verzióban. Ezek a javítások a biztonsági rések vagy a nagy mennyiségű, éles környezetben futó, Kubernetes alapuló termékkel kapcsolatos hibákat érintik.

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

A felhasználóknak törekedniük kell arra, hogy futtassák a futtatott alverzió legújabb patch kiadását, például ha az üzemi fürt a *1.12.14* -on található, és a *1.12.15* az *1,12* sorozat legújabb elérhető javítási verziója, akkor a *1.12.15* -re kell frissítenie, amint meg tudja győződni arról, hogy a fürt teljes mértékben kijavított és támogatott.

## <a name="kubernetes-version-support-policy"></a>Kubernetes-verzió támogatási szabályzata

Az AK támogatja a Kubernetes három kisebb verzióját:

* Az AK-ban megjelent aktuális másodlagos verzió (N)
* Két korábbi másodlagos verzió. Mindegyik támogatott alverzió két stabil javítást is támogat.

Ez az úgynevezett "N-2": (N (legújabb kiadás) – 2 (másodlagos verzió)).

Ha például a (z) a (z), a (z *)* a következő verziókhoz nyújt támogatást:

Új alverzió    |    Támogatott verziók listája
-----------------    |    ----------------------
1.15. a               |    1.15. a, 1.15. b, 1.14. c, 1.14. d, 1.13. e, 1.13. f

Ahol a ". Letter" a javítási verziókra jellemző.

A verziók változásaival és elvárásaival kapcsolatos tájékoztatásért tekintse meg az alábbi "kommunikáció" című szakaszt.

Új alverzió bevezetését követően a legrégebbi alverzió és a javítások támogatott kiadásai elavultak és törlődnek. Ha például az aktuálisan támogatott verziók listája a következő:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

És az AK 1,16-es kiadása. *, ez azt jelenti, hogy a 1,13.* a verziók (az összes 1,13-es verzió) el lesznek távolítva, és nem támogatottak.

> [!NOTE]
> Vegye figyelembe, hogy ha az ügyfelek nem támogatott Kubernetes-verziót futtatnak, a rendszer a fürt támogatásának kérelmezése után a frissítésre kéri őket. A nem támogatott Kubernetes-kiadásokat futtató fürtöket az [AK-támogatási szabályzatok](https://docs.microsoft.com/azure/aks/support-policies)nem tartalmazzák.

A fenti alverzión kívül az AK támogatja az adott alverzióhoz tartozó két legújabb **javítási** kiadást. Például a következő támogatott verziók miatt:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Ha a felsőbb rétegbeli Kubernetes kiadta a 1.15.3 és a 1.14.6, és az AK a javítási verziókat, a legrégebbi javítási verziók elavultak és törlődnek, és a támogatott verziók listája a következő lesz:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Kommunikáció

* A Kubernetes új **másodlagos** verzióihoz
  * Az összes felhasználó nyilvánosan értesítést kap az új verzióról, és a rendszer eltávolítja a verziót.
  * Egy új javítási verzió kiadásakor a rendszer a legrégebbi javítást is eltávolítja egyszerre.
  * Az ügyfelek a nyilvános értesítés dátumától számított **30 napon belül** frissíthetnek egy támogatott alverzióra.
* A Kubernetes új **javítócsomag** -verzióihoz
  * Az összes felhasználó értesítést kap az új javítási verzió kiadásáról, és a legújabb patch kiadásra frissít.
  * A felhasználók **30 napig** frissíthetnek újabb, támogatott javítási kiadásra a legrégebbi eltávolítása előtt.

Az AK az általánosan elérhető verzióként határozza meg a "kiadott verziót", amely minden SLO/minőségi szolgáltatásbeli mérésben engedélyezve van, és minden régióban elérhető. Az AK is támogathatja a explicit módon címkézett és az előzetes verzióra vonatkozó használati feltételek hatálya alá tartozó előzetes verziót.

#### <a name="notification-channels-for-aks-changes"></a>Értesítési csatornák az AK-beli változásokhoz

Az AK rendszeres szolgáltatási frissítéseket tesz közzé, amelyek összegzik az új Kubernetes-verziókat, a szolgáltatások változásait és a [githubon](https://github.com/Azure/AKS/releases)közzétett szolgáltatásban megjelent összetevő-frissítéseket.

Ezeket a módosításokat az összes ügyfélre a felügyelt szolgáltatás részeként kínált rendszeres karbantartás részeként kell átirányítani, néhány esetben pedig explicit frissítésre van szükség, míg mások nem igényelnek semmilyen műveletet.

Az értesítések küldése a következőkkel is elküldhető:

* [AK kibocsátási megjegyzései](https://aka.ms/aks/releasenotes)
* Az Azure Portal értesítései
* [Azure frissítési csatorna][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Támogatott verziók házirend-kivételei

Az AK fenntartja a jogot arra, hogy új/meglévő verziókat adjon hozzá vagy távolítson el, amelyek egy vagy több kritikus termeléssel kapcsolatos hibával vagy biztonsági problémával rendelkeznek, előzetes értesítés nélkül.

Előfordulhat, hogy a rendszer kihagyja az adott javítási kiadásokat, vagy a hiba vagy a biztonsági probléma súlyossága függvényében felgyorsítja a bevezetést.

### <a name="azure-portal-and-cli-default-versions"></a>Azure Portal és CLI alapértelmezett verziói

Ha AK-fürtöt telepít a portálon vagy az Azure CLI-vel, a fürt alapértelmezett értéke az N-1 alverzió és a legújabb javítás. Ha például az KABA a következőt támogatja: *1.15. a*, *1.15. b*, *1.14. c*, *1.14. d*, *1.13. e*és *1.13. f*, a kiválasztott alapértelmezett verzió: *1,14. c*.

Az AK alapértelmezett értéke az N-1, amely alapértelmezés szerint az ügyfelek számára ismert, stabil és javított verziót biztosít.

## <a name="list-currently-supported-versions"></a>Jelenleg támogatott verziók listázása

Ha szeretné megtudni, hogy az előfizetéshez és a régióhoz jelenleg milyen verziók érhetők el, használja az az [AK Get-Versions][az-aks-get-versions] parancsot. A következő példa a *EastUS* régió elérhető Kubernetes-verzióit sorolja fel:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>GYIK

**Mi történik, ha egy ügyfél olyan Kubernetes-fürtöt frissít, amely nem támogatott másodlagos verzióval?**

Ha az *n-3* verziót használ, a rendszer a támogatási szolgálaton kívülre kerül, és a frissítésre kéri. Ha az n-3 verzióról n-2-ra való frissítés sikeres, akkor már a támogatási szabályzatok között van. Például:

- Ha a legrégebbi támogatott AK-verzió a *1.13. a* , és a *1.12. b* vagy régebbi verziója van, akkor a támogatáson kívül van.
- Ha a *1.12. b* verzióról *1.13* -re való frissítés sikeres, akkor a támogatási szabályzatunk keretében visszakerül.

Az *N-2* támogatott ablaknál régebbi verzióra történő frissítés nem támogatott. Ilyen esetekben azt javasoljuk, hogy az ügyfelek új AK-fürtöket hozzanak létre, és a támogatott ablakban lévő verziókkal újra üzembe helyezik a munkaterheléseket.

**Mit jelent a "támogatáson kívül" kifejezés**

A "támogatáson kívül" kifejezés azt jelenti, hogy a futtatott verzió a támogatott verziók listáján kívül esik, és a rendszer arra kéri, hogy a támogatás kérelmezése után frissítse a fürtöt egy támogatott verzióra. Emellett az AK nem hajt végre semmilyen futtatókörnyezetet vagy egyéb garanciát a támogatott verziók listáján kívüli fürtökre.

**Mi történik, ha egy ügyfél olyan Kubernetes-fürtöt méretezi, amelynek alverziója nem támogatott?**

Az AK által nem támogatott alverziók esetén a méretezés be-vagy kikapcsolásának továbbra is működnie kell, de erősen ajánlott frissíteni, hogy a fürt vissza tudja állítani a támogatást.

**Az ügyfél örökre Kubernetes-verzióban maradhat?**

Igen. Ha azonban a fürt nem az AK által támogatott verziók egyikén található, a fürt kívül esik az AK-támogatási szabályzatok közül. Az Azure nem frissíti automatikusan a fürtöt, vagy nem törli azt.

**A vezérlő síkja milyen verziójú támogatást nyújt, ha a csomópont-készlet nem szerepel a támogatott AK-verziók egyikében sem?**

A vezérlési síkon az összes csomópont-készlet verziójának egy ablakában kell lennie. A vezérlő síkja vagy a csomópont-készletek frissítésével kapcsolatos részletekért látogasson el a [Node-készletek frissítésének](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)dokumentációjában.

## <a name="next-steps"></a>Következő lépések

A fürt frissítésével kapcsolatos további információkért lásd: [Azure Kubernetes Service (ak) fürt frissítése][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
