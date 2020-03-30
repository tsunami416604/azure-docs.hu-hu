---
title: Az Azure Kubernetes-szolgáltatás (AKS) csomópontjainak automatikus javítása
description: Információ a csomópont automatikus javítási funkcióiról, valamint arról, hogy az AKS hogyan javítja a törött munkavégző csomópontokat.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284840"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Az Azure Kubernetes-szolgáltatás (AKS) csomópont automatikus javítása

Az AKS folyamatosan ellenőrzi a dolgozói csomópontok állapotát, és automatikus javítást hajt végre a csomópontokon, ha azok nem megfelelő állapotúvá válnak. Ez a dokumentáció azt ismerteti, hogy az Azure Kubernetes Service (AKS) hogyan figyeli a feldolgozói csomópontokat, és javítja a nem megfelelő állapotú feldolgozói csomópontokat.  A dokumentáció célja, hogy tájékoztassa az AKS-üzemeltetőket a csomópont-javítási funkciók működéséről. Azt is fontos megjegyezni, hogy az Azure platform [karbantartást végez a virtuális gépek,][vm-updates] amelyek problémákat tapasztal. Az AKS és az Azure együttműködik a fürtök szolgáltatáskimaradásainak minimalizálása érdekében.

> [!Important]
> A Csomópont automatikus javítási funkciója jelenleg nem támogatott a Windows Server csomópontkészletekben.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Hogyan ellenőrzi az AKS a nem megfelelő állapotú csomópontokat?

> [!Note]
> Az AKS javítási műveletet végez az **aks-remediátorral**rendelkező csomópontokon.

Az AKS szabályok segítségével határozza meg, hogy egy csomópont nem kifogástalan állapotú-e, és javításra szorul.AKS uses rules to determine if a node is a unhealthy state and needs repair. Az AKS a következő szabályok alapján állapítja meg, hogy szükség van-e automatikus javításra.

* A csomópont jelenti a **NotReady** állapotát 10 perces időkereten belül az egymást követő ellenőrzéseksorán
* A csomópont 10 percen belül nem jelent állapotot

Manuálisan ellenőrizheti a csomópontok állapotát a kubectl segítségével. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Az automatikus javítás működése

> [!Note]
> Az AKS javítási műveletet végez az **aks-remediátorral**rendelkező csomópontokon.

Ez a viselkedés a **virtuálisgép-méretezési készletekre van.**  Az automatikus javítás több lépést is tesz egy meghibásodott csomópont javításához.  Ha egy csomópont sérültnek minősül, az AKS több javítási lépést kísérel meg.  A lépések a következő sorrendben hajtják végre:

1. Miután a tároló futási ideje 10 percig nem válaszol, a sikertelen futásidejű szolgáltatások újraindulnak a csomóponton.
2. Ha a csomópont 10 percen belül nem készül el, a csomópont újraindul.
3. Ha a csomópont 30 percen belül nem készül el, a rendszer újra képezi a csomópontot.

> [!Note]
> Ha több csomópont nem kifogástalan, egyenként javítják őket

## <a name="next-steps"></a>További lépések

A [rendelkezésre állási zónák][availability-zones] segítségével növelheti a magas rendelkezésre állást az AKS-fürt számítási feladataival.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
