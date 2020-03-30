---
title: Az Azure Kubernetes Service-ben támogatott Kubernetes-verziók
description: Ismerje meg a Kubernetes-verzió támogatási szabályzatát és a fürtök életciklusát az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593444"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service-ben (AKS) támogatott Kubernetes-verziók

A Kubernetes közösség nagyjából háromhavonta ad ki alverziókat. Ezek a kiadások új funkciókat és fejlesztéseket tartalmaznak. A javítási kiadások gyakoribbak (néha hetente is), és csak az alverzió kritikus hibajavításaihoz szolgálnak. Ezek a javítási kiadások biztonsági réseket vagy a Kubernetes-en alapuló éles környezetben futó termékek nagy számát érintő súlyos hibákat tartalmaznak.

Az AKS célja, hogy az új Kubernetes-verziókat a felső áramú kiadástól számított 30 napon belül hitelesítse és kiadja, a kiadás stabilitását követően.

## <a name="kubernetes-versions"></a>Kubernetes verziók

A Kubernetes a szabványos [szemantikai verziószámozási](https://semver.org/) verziószámozási sémát használja. Ez azt jelenti, hogy a Kubernetes minden verziója ezt a számozási sémát követi:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

A verzió ban minden szám általános kompatibilitást jelez az előző verzióval:

* A főverziók akkor változnak, ha az API-módosítások vagy a visszamenőleges kompatibilitás megszakadhat.
* Az alverziók megváltoznak, ha olyan funkciómódosításokat hajtvégre, amelyek visszafelé kompatibilisek a többi kisebb kiadással.
* A javításverziók megváltoznak, ha visszafelé kompatibilis hibajavításokat tesznek.

A felhasználóknak arra kell törekedniük, hogy futtassák az általuk futtatott alverzió legújabb javítóverzióját, például ha az éles fürt *az 1.12.14-es* és az *1.12.15-ös* verzión található az *1.12-es* sorozathoz elérhető legújabb javítási verzió, akkor frissítenie kell *az 1.12.15-ös* verzióra, amint biztosítani tudja, hogy a fürt teljes mértékben javításra és támogatottra legyen.

## <a name="kubernetes-version-support-policy"></a>Kubernetes verziótámogatási házirend

Az AKS a Kubernetes három alverzióját támogatja:

* Az AKS (N) rendszerben kiadott aktuális alverzió
* Két korábbi alverzió. Minden támogatott alverzió két stabil javítást is támogat.

Ez az úgynevezett "N-2": (N (Legújabb kiadás) - 2 (alverzió)).

Ha például az AKS ma bemutatja az *1.15.a-t,* a következő verziókhoz nyújt unk támogatást:

Új alverzió    |    Támogatott verziólista
-----------------    |    ----------------------
1.15.a.               |    1.15.a, 1.15.b, 1.14.c, 1.14.d. 1.13.e, 1.13.f

Ahol a ".letter" a patch verziókat reprezentálja.

A verzióváltozásokra és elvárásokra vonatkozó kommunikációval kapcsolatos részleteket lásd alább a "Kommunikáció" című témakörben.

Új alverzió bevezetésekor a legrégebbi támogatott alverzió és a javítási kiadások elavultak és törlődnek. Ha például az aktuális támogatott verziólista:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

És az AKS kiadja az 1.16-ot. *, ez azt jelenti, hogy az 1.13.* verziók (mind 1,13 verzió) eltávolításra kerül, és ki vannak a támogatásból.

> [!NOTE]
> Kérjük, vegye figyelembe, hogy ha az ügyfelek nem támogatott Kubernetes-verziót futtatnak, a rendszer kérni fogja őket a frissítésre, amikor a fürt támogatását kérik. A nem támogatott Kubernetes-kiadásokat futtató fürtökre nem vonatkoznak az [AKS támogatási házirendjei.](https://docs.microsoft.com/azure/aks/support-policies)

Az alverziókon a fentieken kívül az AKS támogatja egy adott alverzió két legújabb **patch** kiadását is. Például a következő támogatott verziókat tekintve:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Ha az upstream Kubernetes kiadta az 1.15.3 és az 1.14.6-os verziót, és az AKS kiadja ezeket a javítóverziókat, a legrégebbi javításverziók elavulttá válnak, és a támogatott verziólista a következő lesz:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Kommunikáció

* A **minor** Kubernetes új alverzióihoz
  * Minden felhasználó nyilvánosan értesítést kap az új verzióról, és arról, hogy melyik verzió lesz eltávolítva.
  * Új javítószám megjelenésekor a legrégebbi javítási kiadás egyszerre törlődik.
  * Az ügyfeleknek a nyilvános értesítés dátumától számított **30 nap** áll a támogatott alverziókiadásra való frissítésre.
* A Kubernetes új **patch** verzióihoz
  * Minden felhasználó értesítést kap az új javítás verzió kiadása, és frissíteni a legújabb javítás kiadása.
  * A felhasználóknak **30 nap** áll a frissítésre egy újabb, támogatott javítási kiadásra, mielőtt a legrégebbieket eltávolítanák.

Az AKS a "kiadott verziót" az általánosan elérhető verzióként határozza meg, amely minden SLO / Szolgáltatás minőségének mérésében engedélyezve van, és minden régióban elérhető. Az AKS támogathatja az előzetes verziókat is, amelyek kifejezetten meg vannak címkézve, és amelyekre az előzetes verzióra vonatkozó feltételek vonatkoznak.

#### <a name="notification-channels-for-aks-changes"></a>Értesítési csatornák az AKS-módosításokhoz

Az AKS rendszeres szolgáltatásfrissítéseket tesz közzé, amelyek összefoglalják a [GitHubon](https://github.com/Azure/AKS/releases)a szolgáltatásban kiadott új Kubernetes-verziókat, szolgáltatásmódosításokat és összetevő-frissítéseket.

Ezeket a módosításokat a rendszer a felügyelt szolgáltatás részeként kínált rendszeres karbantartás részeként minden ügyfél számára elvezeti, míg mások nem igényelnek műveletet.

Az értesítéseket a következő kontinitin keresztül is küldik:

* [AKS kibocsátási megjegyzések](https://aka.ms/aks/releasenotes)
* Az Azure Portal értesítései
* [Azure frissítési csatorna][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Támogatott verziók házirend-kivételei

Az AKS fenntartja a jogot, hogy előzetes értesítés nélkül olyan új/meglévő verziókat adjon hozzá vagy távolítson el, amelyekről megállapították, hogy egy vagy több kritikus éles környezet hatással van a hibákra vagy a biztonsági problémákra.

A hiba vagy a biztonsági probléma súlyosságától függően előfordulhat, hogy bizonyos javításokat kihagy, vagy felgyorsítja a bevezetést.

### <a name="azure-portal-and-cli-default-versions"></a>Az Azure Portal és a CLI alapértelmezett verziói

Amikor egy AKS-fürta portálon vagy az Azure CLI-vel üzembe helyezésekor a fürt alapértelmezés szerint az N-1 alverzióés a legújabb javítás. Ha például az AKS támogatja az *1.15.a*, *1.15.b*, *1.14.c*, *1.14.d*, *1.13.e*és *1.13.f*, az alapértelmezett verzió *1.14.c*.

Az AKS az N-1 alapértelmezett beállítását választja, hogy alapértelmezés szerint ismert, stabil és javított verziót biztosítson az ügyfeleknek.

## <a name="list-currently-supported-versions"></a>A jelenleg támogatott verziók listája

Ha meg szeretné tudni, hogy jelenleg mely verziók érhetők el az előfizetéshez és a régióhoz, használja az [aks get-versions][az-aks-get-versions] parancsot. A következő példa az *EastUS* régió elérhető Kubernetes-verzióit sorolja fel:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>GYIK

**Mi történik, ha egy ügyfél frissíti a Kubernetes-fürt egy alverzió, amely nem támogatott?**

Ha az *n-3 verzió,* akkor kívül támogatja, és meg kell kérni, hogy frissítse. Ha az n-3-as verzióról az n-2-re való frissítés sikeres, akkor most már a támogatási irányelveinkben van. Példa:

- Ha a legrégebbi támogatott AKS-verzió *1.13.a,* és ön *1.12.b* vagy régebbi, akkor kívül esik a támogatáson.
- Ha az *1.12.b-ről* *1.13.a* vagy magasabb verzióra történő frissítés sikeres, akkor visszakell lépnie a támogatási irányelveinkben.

Az *N-2* támogatott ablakánál régebbi verziókra való frissítés nem támogatott. Ilyen esetekben azt javasoljuk, hogy az ügyfelek új AKS-fürtöket hozzanak létre, és a támogatott ablakverzióival újratelepítsék a számítási feladataikat.

**Mit jelent a "Támogatáson kívüli"**

A "Külső támogatás" azt jelenti, hogy a futtatott verzió kívül esik a támogatott verziók listáján, és a rendszer megkéri, hogy a támogatás kérésekor frissítse a fürtöt egy támogatott verzióra. Emellett az AKS nem vállal futásidejű vagy egyéb garanciát a támogatott verziók listáján kívüli fürtökre.

**Mi történik, ha egy ügyfél egy Kubernetes-fürt ötös verzióval skálázódik, amely nem támogatott?**

Az AKS által nem támogatott alverziók esetében a be- és kiskálázásnak továbbra is működnie kell, de erősen ajánlott frissíteni a fürt támogatásba való visszahozása érdekében.

**Maradhat-e egy ügyfél örökre a Kubernetes verzióján?**

Igen. Ha azonban a fürt nem az AKS által támogatott verziók egyikén található, a fürt kívül van az AKS támogatási házirendjeiből. Az Azure nem frissíti automatikusan a fürtöt, és nem törli azt.

**Melyik verziót támogatja a vezérlősík, ha a csomópontkészlet nem a támogatott AKS-verziók egyikében található?**

A vezérlősíknak az összes csomópontkészlet verzióinak ablakán belül kell lennie. A vezérlősík vagy csomópontkészletek frissítésével kapcsolatos részletekért látogasson el a [csomópontkészletek frissítésével kapcsolatos dokumentációba.](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)

## <a name="next-steps"></a>További lépések

A fürt frissítésével kapcsolatos további tudnivalókért olvassa el [az Azure Kubernetes-szolgáltatás (AKS) fürt frissítése című témakört.][aks-upgrade]

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
