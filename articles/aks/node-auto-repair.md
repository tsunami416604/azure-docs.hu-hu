---
title: Az Azure Kubernetes Service (ak)-csomópontok automatikus javítása
description: Ismerje meg a csomópontok automatikus javításának funkcióit, valamint azt, hogy az AK Hogyan javítja a törött munkavégző csomópontokat.
services: container-service
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 91384461567634faabaaa1dd588d6e7ec6ece60e
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84735625"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes szolgáltatás (ak) csomópontjának automatikus javítása

Az AK folyamatosan ellenőrzi a munkavégző csomópontok állapotát, és a csomópontok automatikus javítását hajtja végre, ha azok nem megfelelő állapotba kerülnek. Ez a dokumentum a csomópontok automatikus javítási funkcióinak működéséről tájékoztatja a kezelőket. Az AK-s javításokon kívül az Azure-beli virtuálisgép-platform [karbantartási feladatokat hajt végre Virtual Machineson][vm-updates] is. Az AK-ban és az Azure-beli virtuális gépeken a fürtök szolgáltatásbeli fennakadások minimalizálására is használható.

> [!Important]
> A csomópont automatikus javítási funkciója jelenleg nem támogatott a Windows Server-csomópontok készletei esetében.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>A nem megfelelő állapotú csomópontok ellenőrzése az AK-ban

> [!Note]
> Az AK végrehajtja a javítási műveleteket a csomópontokon a következő felhasználói fiókkal: **AK-remediátor**.

Az AK szabályok segítségével határozza meg, hogy egy csomópont nem kifogástalan állapotú-e, és javításra szorul. Az AK a következő szabályok alapján határozza meg, hogy szükséges-e az automatikus javítás.

* A csomópont egy 10 perces időkereten belül jelentést **küld az** egymást követő ellenőrzéseken
* A csomópont 10 percen belül nem jelent állapotot.

A csomópontok állapotát manuálisan is megtekintheti a kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Az automatikus javítás működése

> [!Note]
> Az AK a javítási műveleteket kezdeményezi a felhasználói fiókhoz tartozó **AK-remediátortal**.

Alapértelmezés szerint az automatikus javítás a virtuálisgép-készlet **Virtual Machine Scale sets**-típusával rendelkező fürtök esetén támogatott. Ha egy csomópont nem Kifogástalan állapotra van meghatározva a fenti szabályok alapján, az AK 10 egymást követő, nem kifogástalan állapotú perc elteltével újraindítja a csomópontot. Ha a csomópontok állapota nem kifogástalan a kezdeti javítási művelet után, a további szervizeléseket az AK-mérnökök vizsgálják meg.
  
Ha egy állapot-ellenőrzés során több csomópont nem kifogástalan állapotú, akkor minden egyes csomópontot külön kell kijavítani, mielőtt egy másik javítás megkezdődik.

## <a name="next-steps"></a>További lépések

A [Availability Zones][availability-zones] használatával növelheti a magas rendelkezésre állást az AK-fürt számítási feladataival.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
