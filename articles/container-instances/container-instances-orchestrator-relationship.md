---
title: Azure Container Instances és tárolók összehangolása
description: Ismerje meg, hogyan működnek együtt az Azure Container instances a Container-szervezővel.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 044b74e1a8683c6beb0220c1cf9fb97403286a95
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972253"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container Instances és Container-szervezők

A kis méret és az alkalmazási irányultság miatt a tárolók jól illeszkednek az agilis kézbesítési környezetekhez és a Service-alapú architektúrához. A nagy mennyiségű tároló automatizálásának és kezelésének, valamint a velük való interakciónak a feladatát a rendszer hangolja. A népszerű Container-szervezők közé tartozik a Kubernetes, a DC/OS és a Docker Swarm.

A Azure Container Instances a koordináló platformok alapszintű ütemezési funkcióit biztosítja. Noha nem fedi le a platform által biztosított magasabb értékű szolgáltatásokat, Azure Container Instances is kiegészítheti őket. Ez a cikk ismerteti a Azure Container Instances-kezelők hatókörét, valamint azt, hogy a teljes tárolók hogyan kezelhetik azt.

## <a name="traditional-orchestration"></a>Hagyományos előkészítés

A koordinálás szabványos definíciója a következő feladatokat tartalmazza:

- **Ütemezés**: Egy tároló-rendszerkép és egy erőforrás-kérelem alapján keresse meg a megfelelő gépet, amelyen futtatni szeretné a tárolót.
- **Affinitás/affinitás**: Adja meg, hogy a tárolók készletének a közelében kell futnia (a teljesítményhez) vagy a megfelelő távolban (a rendelkezésre álláshoz).
- **Állapot figyelése**: Figyelje meg a tárolók hibáit, és automatikusan ütemezze őket.
- **Feladatátvétel**: Tartsa nyomon, hogy mi fut az egyes gépeken, és ütemezze át a tárolókat a meghibásodott gépekről a kifogástalan állapotú csomópontokra.
- **Méretezés**: Adja hozzá vagy távolítsa el a Container instances-t az igényeknek megfelelően manuálisan vagy automatikusan.
- **Hálózatkezelés**: Adjon meg egy átfedésben lévő hálózatot a tárolók koordinálásához több gazdagépen keresztüli kommunikációhoz.
- **Szolgáltatás felderítése**: Lehetővé teszi, hogy a tárolók automatikusan megtalálják egymást, még akkor is, ha a gazdagépek között mozognak, és az IP-címeket módosítják
- **Összehangolt alkalmazások frissítése**: Kezelheti a tárolók frissítéseit, hogy elkerülje az alkalmazás leállását, és engedélyezze a visszaállítást, ha hiba történik.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Előkészítés Azure Container Instancesekkel: Rétegzett megközelítés

A Azure Container Instances lehetővé teszi egy rétegen belüli megközelítés használatát, amely biztosítja az összes ütemezési és felügyeleti képességet, amely egyetlen tároló futtatásához szükséges, miközben lehetővé teszi a Orchestrator platformok számára a több tárolós feladatok kezelését.

Mivel a Container instances mögöttes infrastruktúráját az Azure felügyeli, a Orchestrator platformnak nincs szüksége arra, hogy megkeresse a megfelelő gazdagépet, amelyre egyetlen tárolót szeretne futtatni. A felhő rugalmassága biztosítja, hogy az egyik mindig elérhető legyen. Ehelyett a Orchestrator olyan feladatokra koncentrálhat, amelyek leegyszerűsítik a többtárolós architektúrák fejlesztését, beleértve a skálázást és az összehangolt frissítéseket is.

## <a name="scenarios"></a>Forgatókönyvek

Habár a Orchestrator-integráció Azure Container Instances még mindig születik, várható, hogy néhány különböző környezet fog megjelenni:

### <a name="orchestration-of-container-instances-exclusively"></a>Kizárólag a Container instances összehangolása

Mivel a gyors kezdés és a számlázás a második módszer, a kizárólag Azure Container Instanceson alapuló környezet a lehető leggyorsabb módszert kínálja a kezdéshez és a nagy teljesítményű számítási feladatok kezeléséhez.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>A Container instances és a tárolók kombinációja Virtual Machines

A hosszan futó, stabil számítási feladatokhoz, a tárolók dedikált virtuális gépek fürtjében való összehangolása általában olcsóbb, mint a Azure Container Instanceskal azonos tárolók futtatása. A Container instances azonban nagyszerű megoldást kínál arra, hogy gyorsan bővítse és megossza a teljes kapacitást a használatban lévő váratlan vagy rövid élettartamú tüskék kezeléséhez.

A fürtben lévő virtuális gépek számának méretezése helyett, majd további tárolók üzembe helyezése a gépeken a Orchestrator egyszerűen ütemezhetik a további tárolókat Azure Container Instancesban, és törölheti őket, ha már nem szükséges.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Példa a megvalósításra: virtuális csomópontok az Azure Kubernetes szolgáltatáshoz (ak)

Az alkalmazások számítási feladatainak gyors méretezéséhez egy [Azure Kubernetes szolgáltatásbeli](../aks/intro-kubernetes.md) (ak-beli) fürtben a Azure Container instances dinamikusan létrehozott *virtuális csomópontokat* használhatja. A virtuális csomópontok lehetővé teszik a hálózati kommunikációt az ACI-ban és az AK-fürtben futó hüvelyek között. 

A virtuális csomópontok jelenleg támogatják a Linux Container instances-t. A virtuális csomópontok használatának első lépései az [Azure CLI](https://go.microsoft.com/fwlink/?linkid=2047538) vagy a [Azure Portal](https://go.microsoft.com/fwlink/?linkid=2047545)használatával.

A virtuális csomópontok a nyílt forráskódú [virtuális Kubelet][aci-connector-k8s] használatával utánozzák a Kubernetes- [Kubelet][kubelet-doc] úgy, hogy korlátlan kapacitással rendelkező csomópontként regisztrálják őket. A virtuális Kubelet elküldi a hüvelyek [][pod-doc] létrehozását a Azure Container Instancesban.

A Kubernetes API kiszolgáló nélküli tároló platformokra való kiterjesztésével kapcsolatos további példákért tekintse meg a [Virtual Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) projektet.

## <a name="next-steps"></a>További lépések

Hozza létre az első tárolóját a Azure Container Instances a gyors [üzembe helyezési útmutató](container-instances-quickstart.md)segítségével.

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
