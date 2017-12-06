---
title: "Azure-tárolót példányok és tároló Vezénylési"
description: "Azure-tároló példányok és tároló orchestrators együttműködését ismertetése"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 07/24/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a51e746c501cca0521972b09d145439348d1d22d
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure-példányokon tároló és a tároló orchestrators

Kis méret és alkalmazás tájolás, miatt tárolók alkalmas mikroszolgáltatási-alapú architektúra és gyors kézbesítési környezetben. A feladat automatizálása és kezelése a tárolók és azok működésmódját nagy számú nevezik *vezénylési*. Népszerű tároló orchestrators például Kubernetes, a DC/OS és a Docker Swarm, amelyek érhetők el a [Azure Tárolószolgáltatás](https://docs.microsoft.com/azure/container-service/).

Azure tároló példányok biztosít néhány alapvető ütemezési funkciót, az orchestration platformokról, de nem vonatkozik a nagyobb érték szolgáltatásokat, hogy ezek a platformok adja meg, és ténylegesen kiegészítő velük. Ez a cikk ismerteti a hatókör Azure tároló példányok kezeli, és hogyan teljes tároló orchestrators lehet használni.

## <a name="traditional-orchestration"></a>Hagyományos vezénylési

Vezénylési szabványos definícióját a következő feladatokból áll:

- **Ütemezés**: egy tároló lemezképet és az erőforrás kérelmet kap, található a tároló futtatására alkalmas gép.
- **Affinitás/Anti-affinity**: Adja meg, hogy tárolók készlete kell közelben egymástól (teljesítmény) vagy fusson elég távolságra (a rendelkezésre állás érdekében).
- **Állapotfigyelés**: nézze meg a tároló hibák, és automatikusan ütemezze újra őket.
- **Feladatátvételi**: nyomon követjük, hogy mi minden számítógépen fut, és ütemezze újra a sikertelen gépekről tárolók kifogástalan csomópontokat.
- **Skálázás**: hozzáadása vagy eltávolítása az igény szerinti egyeztetéshez, manuálisan vagy automatikusan tároló példányok.
- **Hálózati**: Adja meg egy fölérétegzett hálózatot összehangolása a tárolók több gazdagép gépnek kommunikálhassanak.
- **A szolgáltatásészlelés**: tárolók sikerült automatikusan észlelni az egymáshoz akár gazdagépeken között, és módosítsa az IP-címek engedélyezése.
- **Alkalmazásfrissítések koordinált**: alkalmazás állásidő elkerülése és visszaállítási engedélyezése, ha valamilyen hiba tároló frissítések kezelése.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Vezénylési Azure tároló osztályt: réteges megközelítésének

Azure tároló példányok lehetővé teszi, hogy a vezénylési, réteges megközelítésének biztosítása az ütemezés és a felügyeleti funkciók egyetlen tárolót, miközben lehetővé teszi az orchestrator platform utasítást a több tároló feladatokat futtatásához szükséges.

Az összes tároló-példányok az alkalmazás mögötti infrastruktúra Azure kezeli, mivel az orchestrator platform nem kell saját magát és egy megfelelő gazdagépet egyetlen tárolót futtatására megkereséséről vonatkoznak. A felhő rugalmassága biztosítja, hogy egy mindig elérhető legyen. Ehelyett az orchestrator a feladatokat, amelyek egyszerűbbé teszik a több tároló-architektúrák, beleértve a méretezés és koordinált frissítések fejlesztéséhez összpontosíthat.



## <a name="potential-scenarios"></a>A lehetséges forgatókönyvek

Azure-tároló példányaival orchestrator integrációs pedig továbbra is születő tervezzük, hogy néhány különböző környezetekben előfordulhat, hogy megjelenni:

### <a name="orchestration-of-container-instances-exclusively"></a>Vezénylési tároló példányok kizárólag

Mivel gyorsan start, illetve a második kiszámlázni, kizárólag az Azure-tároló példányok alapján környezet kínál a leggyorsabban a kezdéshez és magas változó munkaterhelések kezelésére.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Tároló-példányok és a virtuális gépek a tárolók

Olyan hosszan futó, stabil munkaterhelések esetén tárolók dedikált virtuális gépek fürtben futó lesz olcsóbb, mint az azonos tárolók futtató tároló osztályt. Azonban a tároló példányok kiválóan alkalmas gyorsan kibontásával, és a teljes kapacitásának használata során váratlan vagy rövid élettartamú igényeiben jelentkező kezelésére szerződő kínálnak. Ahelyett, hogy a fürtben lévő virtuális gépek száma kiterjesztése, majd telepíti ezeket a gépeket alakzatot további tárolókat, az orchestrator egyszerűen ütemezhető a tároló példányok használatával további tárolókat és törölje őket, ha azok már nincs szükség.

## <a name="sample-implementation-azure-container-instances-connector-for-kubernetes"></a>Példa: Kubernetes Azure tároló példányok összekötő

Annak bemutatásához, hogyan integrálható tároló vezénylési platformok Azure tároló példányok, épület elindította azt egy [Kubernetes minta összekötő][aci-connector-k8s].

Az összekötő Kubernetes utánozza a [kubelet] [ kubelet-doc] korlátlan kapacitás csomópontként regisztrálása és létrehozásának terjesztéséhez [három munkaállomás-csoporttal] [ pod-doc] tároló csoportokként Azure tároló példányát.

<!-- ![ACI Connector for Kubernetes][aci-connector-k8s-gif] -->

Az egyéb orchestrators összekötők épülhet, amely az orchestrator API hatványa egyesítenie a sebesség és egyszerű Azure tároló példányát tárolók kezelése a platform primitívek hasonló módon integrálva.

> [!WARNING]
> A ACI összekötő a Kubernetes *kísérleti* és éles környezetben nem használható.

## <a name="next-steps"></a>Következő lépések

Az első tároló létrehozása az Azure-tároló példányok használata a [gyors üzembe helyezési útmutató](container-instances-quickstart.md).

<!-- IMAGES -->
[aci-connector-k8s-gif]: ./media/container-instances-orchestrator-relationship/aci-connector-k8s.gif

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/azure/aci-connector-k8s
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/