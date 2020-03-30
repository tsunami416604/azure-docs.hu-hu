---
title: Tárolópéldányok és tároló vezénylése
description: Ismerje meg, hogy az Azure-tárolópéldányok hogyan lépnek kapcsolatba a tárolóvezőkkel.
ms.topic: article
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: f3f8693d1a9a12e7c35d126ab3e3ca53448e5e40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533661"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container-példányok és tárolóvező

Kis méretük és alkalmazásorientáltságuk miatt a tárolók kiválóan alkalmasak agilis kézbesítési környezetek és mikroszolgáltatás-alapú architektúrák számára. A nagyszámú tároló automatizálásának és kezelésének feladata, valamint az interakciójuk a *vezénylés.* Népszerű konténer orchestrators közé Kubernetes, DC/OS és Docker Swarm.

Az Azure Container Instances a vezénylési platformok néhány alapvető ütemezési képességet biztosít. És bár nem terjed ki a nagyobb értékű szolgáltatásokat, amelyeket ezek a platformok nyújtanak, az Azure Container Instances kiegészítheti őket. Ez a cikk ismerteti, hogy az Azure Container instances milyen hatókört kezel, és hogyan teljes tároló vezénylők kölcsönhatásba léphetvele.

## <a name="traditional-orchestration"></a>Hagyományos vezénylés

A vezénylés szabványos definíciója a következő feladatokat tartalmazza:

- **Ütemezés:** Adott egy tárolórendszerképet és egy erőforrás-kérelmet, keresse meg a megfelelő gépet, amelyen a tároló futtatásához.
- **Affinitás/anti-affinitás**: Adja meg, hogy a tárolók készlete egymás közelében fusson (a teljesítmény érdekében) vagy kellően távol (a rendelkezésre állás érdekében).
- **Állapotfigyelés:** Figyelje a tárolóhibákat, és automatikusan ütemezze át őket.
- **Feladatátvétel:** Nyomon követheti, hogy mi fut az egyes gépeken, és ütemezze át a tárolókat a sikertelen gépekről az egészséges csomópontokra.
- **Méretezés**: Tárolópéldányok hozzáadása vagy eltávolítása az igényeknek megfelelően, manuálisan vagy automatikusan.
- **Hálózat:** Átfedési hálózat biztosítása a tárolók koordinálására, hogy több gazdagépen kommunikálhassanak.
- **Szolgáltatásfelderítés:** Engedélyezze, hogy a tárolók automatikusan megtalálják egymást, még akkor is, ha a gazdagépek között mozognak és IP-címeket váltanak.
- **Koordinált alkalmazásfrissítések:** A tárolófrissítések kezelése az alkalmazások állásidejének elkerülése érdekében, és engedélyezze a visszaállítást, ha valami elromlik.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Vezénylés az Azure Container-példányokkal: Rétegzett megközelítés

Az Azure Container Instances lehetővé teszi a többrétegű megközelítés vezénylési, biztosítva az összes ütemezési és felügyeleti képességek futtatásához szükséges egyetlen tároló, miközben lehetővé teszi az orchestrator platformok kezelésére többtárolós feladatok at.

Mivel a tárolópéldányok alapul szolgáló infrastruktúráját az Azure kezeli, az orchestrator platformnak nem kell aggódnia, hogy megtalálja a megfelelő gazdagépet, amelyen egyetlen tárolót futtathat. A felhő rugalmassága biztosítja, hogy az egyik mindig elérhető legyen. Ehelyett az orchestrator összpontosíthat a feladatok, amelyek egyszerűsítik a többtárolós architektúrák, beleértve a méretezés és a koordinált frissítéseket.

## <a name="scenarios"></a>Forgatókönyvek

Bár az Azure Container Instances-szel való vezénylőintegráció még mindig kialakulóban van, arra számítunk, hogy néhány különböző környezet jelenik meg:

### <a name="orchestration-of-container-instances-exclusively"></a>Kizárólag tárolópéldányok vezénylése

Mivel gyorsan indulnak, és a számlázás a második, egy környezet kizárólag az Azure Container Instances kínálja a leggyorsabb módja az első lépésekhez, és a rendkívül változó számítási feladatok kezelésére.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Tárolópéldányok és tárolók kombinációja a virtuális gépekben

Hosszú ideig futó, stabil számítási feladatok esetén a dedikált virtuális gépek fürtjében lévő tárolók vezénylése általában olcsóbb, mint az Azure Container Instances azonos tárolók futtatása. A tárolópéldányok azonban nagyszerű megoldást kínálnak a teljes kapacitás gyors bővítésére és összehúzódására a használat váratlan vagy rövid élettartamú kiugrásainak kezeléséhez.

Ahelyett, hogy a fürtben lévő virtuális gépek számát, majd további tárolókat telepítene ezekre a gépekre, az orchestrator egyszerűen ütemezheti a további tárolókat az Azure Container Instances-ben, és törölheti őket, ha már nem Szükséges.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Mintamegvalósítás: virtuális csomópontok az Azure Kubernetes szolgáltatáshoz (AKS)

Az [Azure Kubernetes-szolgáltatás](../aks/intro-kubernetes.md) (AKS) fürtben az alkalmazás-munkaterhelések gyors méretezéséhez használhatja az Azure Container Instances-ben dinamikusan létrehozott *virtuális csomópontokat.* A virtuális csomópontok lehetővé teszik a hálózati kommunikációt az ACI-ban futó podok és az AKS-fürt között. 

A virtuális csomópontok jelenleg támogatják a Linux-tárolópéldányokat. Ismerkedés a virtuális csomópontokkal az [Azure CLI](https://go.microsoft.com/fwlink/?linkid=2047538) vagy az [Azure Portal használatával.](https://go.microsoft.com/fwlink/?linkid=2047545)

A virtuális csomópontok a nyílt forráskódú [Virtual Kubelet][aci-connector-k8s] segítségével utánozzák a Kubernetes [kubelet-et][kubelet-doc] korlátlan kapacitású csomópontként történő regisztrációval. A Virtual Kubelet elküldi a [podok][pod-doc] létrehozása tárolócsoportok ként az Azure Container instances.

Tekintse meg a [Virtual Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) projekt további példákat a Kubernetes API kiszolgáló nélküli tárolóplatformokra való kiterjesztésére.

## <a name="next-steps"></a>További lépések

Hozza létre az első tárolót az Azure Container Instances segítségével a [rövid útmutató](container-instances-quickstart.md)használatával.

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
