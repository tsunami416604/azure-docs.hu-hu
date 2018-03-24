---
title: Azure-példányokon tároló és a tároló vezénylési
description: Ismerje meg, a tároló orchestrators példányok használhatja az Azure tároló.
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/23/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 79dcb4be49791e84efa99b36b1c0a0d474a372f4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure-példányokon tároló és a tároló orchestrators

Kis méret és alkalmazás tájolás, miatt tárolók alkalmas mikroszolgáltatási-alapú architektúra és gyors kézbesítési környezetben. A feladat automatizálása és kezelése a tárolók és azok működésmódját nagy számú nevezik *vezénylési*. Népszerű tároló orchestrators Kubernetes, a DC/OS és a Docker Swarm tartalmazza.

Azure tároló példányok biztosít bizonyos vezénylési platformokról alapvető ütemezési képességeket. És nem terjed ki a nagyobb érték szolgáltatásokat, ezek a platformok adja meg, amíg Azure tároló példányok kiegészítő hozzájuk. Ez a cikk ismerteti az Azure-tároló példányok kezeli, majd hogyan teljes tároló orchestrators előfordulhat, hogy úgy körét.

## <a name="traditional-orchestration"></a>Hagyományos vezénylési

Vezénylési szabványos definícióját a következő feladatokból áll:

- **Ütemezés**: egy tároló lemezképet és az erőforrás kérelmet kap, található a tároló futtatására alkalmas gép.
- **Affinitás/Anti-affinity**: Adja meg, hogy tárolók készlete kell közelben egymástól (teljesítmény) vagy fusson elég távolságra (a rendelkezésre állás érdekében).
- **Állapotfigyelés**: nézze meg a tároló hibák, és automatikusan ütemezze újra őket.
- **Feladatátvételi**: nyomon követjük, hogy mi minden számítógépen fut, és ütemezze újra a sikertelen gépekről tárolók kifogástalan csomópontokat.
- **Skálázás**: hozzáadása vagy eltávolítása az igény szerinti egyeztetéshez, manuálisan vagy automatikusan tároló példányok.
- **Hálózati**: Adja meg egy fölérétegzett hálózatot összehangolása a tárolók több gazdagép gépnek kommunikálhassanak.
- **A szolgáltatásészlelés**: tárolók sikerült automatikusan észlelni az egymáshoz, még akkor is, mivel gazdagépeken között, és módosítsa az IP-címek engedélyezése.
- **Alkalmazásfrissítések koordinált**: alkalmazás állásidő elkerülése érdekében tároló frissítések kezelése, és a visszaállítás engedélyezése, ha valamilyen hiba.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Vezénylési Azure tároló osztályt: réteges megközelítésének

Azure tároló példányok lehetővé teszi, hogy a vezénylési, réteges megközelítésének biztosítása az ütemezés és a felügyeleti funkciók egyetlen tárolót, miközben lehetővé teszi az orchestrator platform utasítást a több tároló feladatokat futtatásához szükséges.

Az alkalmazás mögötti infrastruktúra tároló példányok Azure kezeli, mivel az orchestrator platform nem kell saját magát és egy megfelelő gazdagépet egyetlen tárolót futtatására megkereséséről vonatkoznak. A felhő rugalmassága biztosítja, hogy egy mindig elérhető legyen. Ehelyett az orchestrator a feladatokat, amelyek egyszerűbbé teszik a több tároló-architektúrák, beleértve a méretezés és koordinált frissítések fejlesztéséhez összpontosíthat.

## <a name="potential-scenarios"></a>A lehetséges forgatókönyvek

Azure-tároló példányaival orchestrator integrációs pedig továbbra is születő tervezzük, hogy néhány különböző környezetekben előfordulhat, hogy megjelenni:

### <a name="orchestration-of-container-instances-exclusively"></a>Vezénylési tároló kizárólag-példányok

Mivel gyorsan start, illetve a második kiszámlázni, kizárólag az Azure-tároló példányok alapján környezet kínál a leggyorsabban a kezdéshez és magas változó munkaterhelések kezelésére.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Tároló-példányok és a virtuális gépek a tárolók

Olyan hosszan futó, stabil munkaterhelések esetén tárolók dedikált virtuális gépek fürtben futó rendszerint olcsóbb, mint az azonos tárolók futó Azure tároló osztályt. Azonban a tároló példányok kiválóan alkalmas gyorsan kibontásával, és a teljes kapacitásának használata során váratlan vagy rövid élettartamú igényeiben jelentkező kezelésére szerződő kínálnak.

Ahelyett, hogy a fürtben lévő virtuális gépek száma kiterjesztése, akkor telepíti ezeket a gépeket alakzatot további tárolókat, az orchestrator is egyszerűen ütemezése Azure tároló példányát további tárolókat, és törölje őket, ha azok már nem szükséges.

## <a name="sample-implementation-azure-container-instances-connector-for-kubernetes"></a>Példa: Kubernetes Azure tároló példányok összekötő

Annak bemutatásához, hogyan integrálható tároló vezénylési platformok Azure tároló példányok, épület elindította azt egy [Kubernetes minta összekötő][aci-connector-k8s].

Az összekötő Kubernetes utánozza a [kubelet] [ kubelet-doc] korlátlan kapacitás csomópontként regisztrálása és létrehozásának terjesztéséhez [három munkaállomás-csoporttal] [ pod-doc] tároló csoportokként Azure tároló példányát.

Összekötők a többi orchestrators épülhet a hasonlóképpen integrálódó platform primitívek az orchestrator API hatványa egyesítenie a sebesség és a jellemzője az egyszerűség Azure tároló példányát tárolók kezelése.

> [!WARNING]
> A ACI összekötő a Kubernetes *kísérleti* és éles környezetben nem használható.

## <a name="next-steps"></a>További lépések

Az első tároló létrehozása az Azure-tároló példányok használata a [gyors üzembe helyezési útmutató](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/