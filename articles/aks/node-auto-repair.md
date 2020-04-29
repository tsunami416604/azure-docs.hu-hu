---
title: Az Azure Kubernetes Service (ak)-csomópontok automatikus javítása
description: Ismerje meg a csomópontok automatikus javításának funkcióit, valamint azt, hogy az AK Hogyan javítja a törött munkavégző csomópontokat.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80284840"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes szolgáltatás (ak) csomópontjának automatikus javítása

Az AK folyamatosan ellenőrzi a munkavégző csomópontok állapotát, és a csomópontok automatikus javítását hajtja végre, ha azok nem megfelelő állapotba kerülnek. Ez a dokumentáció azt ismerteti, hogyan figyeli az Azure Kubernetes Service (ak) a feldolgozó csomópontokat, és javítsa a nem megfelelő állapotú munkavégző csomópontokat.  A dokumentáció az AK-operátorok tájékoztatása a csomópont-javítási funkciók működéséről. Azt is fontos megjegyezni, hogy az Azure platform [karbantartási feladatokat hajt végre Virtual Machines][vm-updates] , amelyek problémákat tapasztalnak. Az AK és az Azure együttműködik a fürtök szolgáltatásbeli zavarainak minimalizálásával.

> [!Important]
> A csomópont automatikus javítási funkciója jelenleg nem támogatott a Windows Server-csomópontok készletei esetében.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>A nem megfelelő állapotú csomópontok ellenőrzése az AK-ban

> [!Note]
> Az AK végrehajtja a javítási műveleteket a csomópontokon a következő felhasználói fiókkal: **AK-remediátor**.

Az AK szabályok használatával határozza meg, hogy egy csomópont nem kifogástalan állapotú-e, és javításra szorul. Az AK a következő szabályok alapján határozza meg, hogy szükséges-e az automatikus javítás.

* A csomópont egy 10 perces időkereten belül jelentést **küld az** egymást követő ellenőrzéseken
* A csomópont 10 percen belül nem jelent állapotot.

A csomópontok állapotát manuálisan is megtekintheti a kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Az automatikus javítás működése

> [!Note]
> Az AK végrehajtja a javítási műveleteket a csomópontokon a következő felhasználói fiókkal: **AK-remediátor**.

Ez a viselkedés a **Virtual Machine Scale sets**.  Az automatikus javítás több lépést is végrehajt a hibás csomópontok kijavításához.  Ha egy csomópont állapota sérült, az AK több szervizelési lépést próbál meg felvenni.  A lépéseket a következő sorrendben hajtja végre:

1. Ha a tároló futtatókörnyezete 10 percen belül nem válaszol, a rendszer újraindítja a feladatátvételi szolgáltatást a csomóponton.
2. Ha a csomópont 10 percen belül nem áll készen, a csomópont újraindul.
3. Ha a csomópont 30 percen belül nem áll készen, a csomópont újra rendszerképbe kerül.

> [!Note]
> Ha több csomópont nem kifogástalan állapotú, akkor a rendszer egy

## <a name="next-steps"></a>További lépések

A [Availability Zones][availability-zones] használatával növelheti a magas rendelkezésre állást az AK-fürt számítási feladataival.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
