---
title: Az Azure Kubernetes Service-ben támogatott Kubernetes-verzió
description: Megismerheti a Kubernetes-verzió támogatási szabályzata és életciklus-fürtök az Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 02/15/2018
ms.author: saudas
ms.openlocfilehash: 37a9712749a1575f81086d28ad461a665bef36d9
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313449"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Támogatott az Azure Kubernetes Service (AKS) Kubernetes-verzió

A Kubernetes-közösség körülbelül háromhavonta ad ki alverziókat. Ezek a kiadások új funkciókat és fejlesztéseket tartalmaznak. A javítások kiadásai gyakoribbak (esetenként heti rendszerességűek), és csak az alverziók kritikus hibáinak javítására szolgálnak. A patch-kiadásokkal biztonsági réseket, vagy az ügyfelek és a Kubernetes-alapú éles üzemben futó termékek sok érintő jelentős hibák javításait tartalmazza.

Alverzió elérhetővé az új Kubernetes [aks-motor] [ aks-engine] ismereteik alapján. The AKS szolgáltatási szintjének célkitűzése (SLO) az AKS-fürtök alverzióinak kiadása 30 napon belül. Ez a kiadás stabilitásától függően változhat.

## <a name="kubernetes-version-support-policy"></a>A Kubernetes verziótámogatási szabályzata

Az AKS a Kubernetes négy alverzióját támogatja:

- Az aktuális alverzió, amely nyilvánosan felsőbb rétegbeli (n)
- Három korábbi alverzió. Mindegyik támogatott alverzió két stabil javítást támogat.

Például, ha az AKS vezet be *1.12.x* még ma, támogatást is biztosítunk *1.11.a* + *1.11.b*, *1.10.c*  +  *1.10d*, *1.9.e* + *1.9F* (ahol a betűkkel javítás kiadások olyan két legújabb stabil buildek).

Új alverzió bemutatásakor a legrégebbi alverziót és a legrégebbi javításokat kivezetjük. az új alverzió és a következő használatból való kivonást egyaránt kiadása előtt 15 napon keresztül egy közlemény keresztül jön létre a [Azure frissítési csatornákkal][azure-update-channel]. Ha a fenti példában *1.12.x* van nyilvánosan, a kivont verziók a következők *1.8.g* + *1.8.h*.

Ha egy AKS-fürtöt üzembe helyez a portálon vagy az Azure CLI-vel, az mindig az n-1. alverzióra, illetve a legújabb javításra lesz beállítva. Például, ha támogatja az AKS *1.12.x*, *1.11.a* + *1.11.b*, *1.10.c*  +   *1.10d*, *1.9.e* + *1.9F*, az alapértelmezett verzió új fürtök *1.10.b*.

## <a name="list-currently-supported-versions"></a>Jelenleg a támogatott verziók listája

Ismerje meg, hogy mely verziói érhetők el jelenleg az előfizetés és a régiót, használja a [az aks get-verziók] [ az-aks-get-versions] parancsot. Az alábbi példa felsorolja az elérhető Kubernetes-verzió esetében a *EastUS* régió:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

A kimenet hasonlít a következő példának, amely bemutatja, hogy a Kubernetes-verziót *1.12.5* a legfrissebb verzió érhető el:

```
KubernetesVersion    Upgrades
-------------------  -----------------------
1.12.5               None available
1.12.4               1.12.5
1.11.7               1.12.4, 1.12.5
1.11.6               1.11.7, 1.12.4, 1.12.5
1.10.12              1.11.6, 1.11.7
1.10.9               1.10.12, 1.11.6, 1.11.7
1.9.11               1.10.9, 1.10.12
1.9.10               1.9.11, 1.10.9, 1.10.12
```

## <a name="faq"></a>GYIK

**Mi történik, ha egy ügyfél frissíti a Kubernetes-fürthöz egy kisebb verziója nem támogatott?**

Ha a *n-4* verzióját, akkor kívül esnek a szolgáltatási szint Célkitűzésének. Ha sikeres n-3 az verzió n-4 a frissítést, majd Ön vissza a szolgáltatási szint Célkitűzésének. Példa:

- Ha az AKS-verziók a következők *1.12.x*, *1.11.a* + *1.11.b*, *1.10.c*  +  *1.10d*, és *1.9.e* + *1.9F* és a *1.8.g* vagy *1.8.h*, akkor a szolgáltatási szint Célkitűzésének kívül esnek.
- Ha a frissítés a *1.8.g* vagy *1.8.h* való *1.9.e* vagy *1.9.f* sikeres, a szolgáltatási szint Célkitűzésének vissza van.

Frissítés-nél régebbi verziók *n-4* nem támogatottak. Ezekben az esetekben javasoljuk, hogy ügyfeleink új AKS-fürtök létrehozásának és ismételt üzembe helyezése számítási feladataik.

**Mi történik, ha egy ügyfél méretezhető alverzió, amely nem támogatott a Kubernetes-fürt?**

Az AKS által nem támogatott alverziót, a méretezés, vagy továbbra is problémamentesen működjenek.

**Egy ügyfél maradhat egy Kubernetes-verziót a végtelen?**

Igen. Azonban a fürt nem szerepel az AKS által támogatott verziókra, ha a fürtön kívül esik az AKS slo-t. Az Azure automatikusan frissítse a fürtöt, vagy törölje.

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
