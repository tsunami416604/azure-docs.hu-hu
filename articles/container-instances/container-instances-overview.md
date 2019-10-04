---
title: Mi az Azure Container Instances?
description: A Azure Container Instances szolgáltatás az elkülönített tárolók Azure-ban való futtatásának leggyorsabb és legegyszerűbb módját kínálja anélkül, hogy virtuális gépeket kellene kezelnie, és nem kell magasabb szintű Orchestrator alkalmaznia.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 04/25/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: a07eda709c813e68bc3e4f08b6aab3f93dec65a6
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325745"
---
# <a name="what-is-azure-container-instances"></a>Mi az Azure Container Instances?

Egyre többen használják a tárolókat felhőalapú alkalmazások csomagolásához, üzembe helyezéséhez és felügyeletéhez. Az Azure Container Instances a tárolók Azure-ban való futtatásának leggyorsabb és legegyszerűbb módját nyújtja anélkül, hogy virtuális gépeket kellene kezelnie vagy magasabb szintű szolgáltatást kellene alkalmaznia.

Az Azure Container Instances ideális megoldás minden olyan forgatókönyv esetében, amely elkülönített tárolókban valósulhat meg, beleértve az egyszerű alkalmazásokat, a tevékenységek automatizálását és a buildfeladatokat. Az olyan forgatókönyvek esetében, amelyekhez teljes mértékű tárolóvezénylés szükséges, beleértve a több tárolón végzett szolgáltatásészlelést, az automatikus skálázást és az alkalmazások koordinált frissítését, az [Azure Kubernetes Service (AKS)](../aks/index.yml) használatát javasoljuk.

## <a name="fast-startup-times"></a>Rövid indítási idők

A tárolók jelentős előnyöket nyújtanak a virtuális gépekkel (VM-ekkel) szemben az indítás terén. Az Azure Container Instanceszel másodpercek alatt elindíthat egy tárolót az Azure-ban anélkül, hogy virtuális gépeket kellene kiépítenie és kezelnie.

## <a name="public-ip-connectivity-and-dns-name"></a>Nyilvános IP-kapcsolat és DNS-név

Az Azure Container Instances használatával tárolóit közvetlenül is közzéteheti az interneten IP-cím és teljes tartománynév (FQDN) használatával. Tárolópéldány létrehozásakor egyéni DNS-névcímkét kell megadnia ahhoz, hogy az alkalmazás elérhető legyen a *customlabel*.*azureregion*.azurecontainer.io régióban.

## <a name="hypervisor-level-security"></a>Hipervizorszintű biztonság

Korábban a tárolók biztosítottak ugyan alkalmazásfüggőség-elkülönítési és erőforrás-szabályozási funkciót, azonban nem voltak eléggé megerősítve a rosszindulatú több-bérlős alkalmazásokhoz. Az Azure Container Instances használatakor az alkalmazások ugyanannyira el vannak különítve a tárolóban, mintha egy virtuális gépen futnának.

## <a name="custom-sizes"></a>Egyéni méretek

A tárolók általában egyetlen alkalmazás futtatására vannak optimalizálva, azonban ezeknek az alkalmazásoknak a pontos igényei nagyon is eltérőek lehetnek. Az Azure Container Instances optimális kihasználtságot biztosít, mivel pontosan megadható a processzormagok száma és a memória mennyisége. A fizetés az igényelt erőforrások alapján történik másodpercalapú elszámolás szerint, így tényleges igényeinek megfelelően optimalizálhatja költségeit a legkisebb részletekig.

A nagy számítási igényű feladatok, például a gépi tanulás esetében a Azure Container Instances a Linux-tárolókat az NVIDIA Tesla [GPU-erőforrások](container-instances-gpu.md) (előzetes verzió) használatára is képes ütemezni.

## <a name="persistent-storage"></a>Állandó tárolók

Az állapotok az Azure Container Instanceszel való lekéréséhez és megőrzéséhez az [Azure-fájlmegosztások közvetlen csatolásának](container-instances-mounting-azure-files-volume.md) lehetőségét kínáljuk.

## <a name="linux-and-windows-containers"></a>Linux- és Windows-tárolók

Az Azure Container Instances használatával a Windows- és a Linux-tárolókat ugyanazzal az API-val ütemezheti. Egyszerűen adja meg az operációs rendszer típusát a [tárolócsoportok](container-instances-container-groups.md) létrehozásakor.

Egyes funkciók jelenleg csak Linux-tárolók:

* Több tároló tárolócsoportonként
* Kötet csatlakoztatása ([Azure Files](container-instances-volume-azure-files.md), [emptyDir](container-instances-volume-emptydir.md), [gitrepo típusú](container-instances-volume-gitrepo.md), [Secret](container-instances-volume-secret.md))
* [Erőforrás-használati metrikák](container-instances-monitor.md) Azure monitor
* [Virtuális hálózat központi telepítése](container-instances-vnet.md) előnézet
* [GPU-erőforrások](container-instances-gpu.md) előnézet

A Windows-tárolók központi telepítése esetén a lemezképeket a gyakori [Windows alapképek](container-instances-faq.md#what-windows-base-os-images-are-supported)alapján kell használni.

> [!NOTE]
> A Windows Server 2019-alapú rendszerképek használata a Azure Container Instances előzetes verzióban érhető el.

## <a name="co-scheduled-groups"></a>Együttesen ütemezett csoportok

Az Azure Container Instances támogatja az olyan [több tárolóból álló csoportok](container-instances-container-groups.md) ütemezését, amelyek osztoznak a gazdagépen, a helyi hálózaton és a tárolón, valamint azonos az életciklusuk. Ennek segítéségével kombinálhatja a fő alkalmazástárolóját más, támogató szerepű tárolókkal, például naplózó oldalkocsikkal.

## <a name="virtual-network-deployment-preview"></a>Virtuális hálózat üzembe helyezése (előzetes verzió)

Ez a jelenleg előzetes verzióban lévő Azure Container Instances-funkció lehetővé teszi [tárolópéldányok üzembe helyezését egy Azure virtuális hálózatban](container-instances-vnet.md). Ha a tárolópéldányokat a virtuális hálózat egyik alhálózatában helyezi üzembe, biztonságosan tudnak majd kommunikálni a virtuális hálózat más erőforrásaival, így a helyszíniekkel is (a [VPN Gateway-en](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy az [ExpressRoute-on](../expressroute/expressroute-introduction.md) keresztül).

> [!IMPORTANT]
> A Azure Container Instances bizonyos funkciói előzetes verzióban érhetők el, és bizonyos [korlátozások érvényesek](container-instances-vnet.md#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A funkciók bizonyos aspektusai megváltozhatnak az általánosan elérhetővé válás előtt.

## <a name="next-steps"></a>További lépések

Próbáljon üzembe helyezni egy tárolót az Azure-ban egyetlen parancs használatával a rövid útmutatónk alapján:

> [!div class="nextstepaction"]
> [Az Azure Container Instances rövid útmutatója](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
