---
title: Az Azure Kubernetes Service-ben támogatott Kubernetes-verzió
description: Megismerheti a Kubernetes-verzió támogatási szabályzata és életciklus-fürtök az Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 09/21/2018
ms.author: saudas
ms.openlocfilehash: 6b55825107ae8872b146b3ad4fde0ef4b917b71d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047575"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Támogatott az Azure Kubernetes Service (AKS) Kubernetes-verzió

A Kubernetes közösségi-kiadások alverziót nagyjából háromhavonta. Ezek a kiadásai új szolgáltatásait és fejlesztéseit. Javítás kiadások szolgálnak gyakoribb (néha hetente), és csak a alverzió fontos hibajavításokat tartalmaz. A patch-kiadásokkal biztonsági réseket, vagy az ügyfelek és a Kubernetes-alapú éles üzemben futó termékek sok érintő jelentős hibák javításait tartalmazza.

Alverzió elérhetővé az új Kubernetes [acs-engine] [ acs-engine] ismereteik alapján. Az AKS szolgáltatási szint célkitűzést (SLO) célok ad ki az AKS-fürtök alverzió vonatkoznak a kiadási stabilitását, 30 napon belül.

## <a name="kubernetes-version-support-policy"></a>Kubernetes-verzió támogatási szabályzata

Az AKS Kubernetes négy kisebb verzióit támogatja:

- Az aktuális alverzió, amely nyilvánosan felsőbb rétegbeli (n)
- Három előző alverziót. Minden egyes támogatott alverzió két stabil javítások is támogatja.

Például, ha az AKS vezet be *1.11.x* még ma, támogatást is biztosítunk *1.10.a* + *1.10.b*, *1.9.c*  +  *1.9d*, *1.8.e* + *1.8F* (ahol a betűkkel javítás kiadások olyan két legújabb stabil buildek).

Amikor egy új alverzió bemutatott, a legrégebbi kisebb verziója és a javítás kiadások támogatott kivezettük. az új alverzió és a következő használatból való kivonást egyaránt kiadása előtt 15 napon keresztül bejelentés az Azure frissítési csatornákon keresztül történik. Ha a fenti példában *1.11.x* van nyilvánosan, a kivont verziók a következők *1.7.g* + *1.7.h*.

Amikor telepít egy AKS-fürtöt a portálon vagy az Azure CLI-vel, a fürt értéke mindig a n-1 alverzió és a legújabb javítást. Például, ha támogatja az AKS *1.11.x*, *1.10.a* + *1.10.b*, *1.9.c* + *1.9 d* , *1.8.e* + *1.8F*, az alapértelmezett verzió új fürtök *1.10.b*.

## <a name="faq"></a>GYIK

**Mi történik, ha egy ügyfél frissíti a Kubernetes-fürthöz egy kisebb verziója nem támogatott?**

Ha a *n-4* verzióját, akkor kívül esnek a szolgáltatási szint Célkitűzésének. Ha sikeres n-3 az verzió n-4 a frissítést, majd Ön vissza a szolgáltatási szint Célkitűzésének. Példa:

- Ha az AKS-verziók a következők *1.10.a* + *1.10.b*, *1.9.c* + *1.9d*,  *1.8.e* + *1.8F* és a *1.7.g* vagy *1.7.h*, akkor a szolgáltatási szint Célkitűzésének kívül esnek.
- Ha a frissítés a *1.7.g* vagy *1.7.h* való *1.8.e* vagy *1.8.f* sikeres, a szolgáltatási szint Célkitűzésének vissza van.

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
[acs-engine]: https://github.com/Azure/acs-engine

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md