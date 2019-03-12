---
title: Mi az Azure Container Instances?
description: Az Azure Container Instances szolgáltatás elkülönített-tárolókat futtathat az Azure-ban, virtuális gépek kezelése nélkül, és a egy magasabb szintű orchestrator kellene a leggyorsabb és legegyszerűbb megoldást kínál.
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 11/30/2018
ms.author: seanmck
ms.custom: seodec18, mvc
ms.openlocfilehash: d7c63503d0e1c142dfc1ef685453b93e24ec52b3
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730385"
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

Nagy számítási igényű feladatok például machine learning, az Azure Container Instances ütemezheti a Linux-tárolók használata NVIDIA Tesla [GPU-erőforrások](container-instances-gpu.md) (előzetes verzió).

## <a name="persistent-storage"></a>Állandó tárolók

Az állapotok az Azure Container Instanceszel való lekéréséhez és megőrzéséhez az [Azure-fájlmegosztások közvetlen csatolásának](container-instances-mounting-azure-files-volume.md) lehetőségét kínáljuk.

## <a name="linux-and-windows-containers"></a>Linux- és Windows-tárolók

Az Azure Container Instances használatával a Windows- és a Linux-tárolókat ugyanazzal az API-val ütemezheti. Egyszerűen adja meg az operációs rendszer típusát a [tárolócsoportok](container-instances-container-groups.md) létrehozásakor.

Néhány funkció jelenleg csak Linux-tárolók:

* Több tároló tárolócsoportonként
* Kötet csatlakoztatási ([Azure Files](container-instances-volume-azure-files.md), [emptyDir](container-instances-volume-emptydir.md), [GitRepo](container-instances-volume-gitrepo.md), [titkos](container-instances-volume-secret.md))
* [Virtuális hálózat üzembe helyezési](container-instances-vnet.md) (előzetes verzió)
* [GPU-erőforrások](container-instances-gpu.md) (előzetes verzió)

Az Azure Container Instances támogatja a Windows Server 2016 lemezképek hosszú távú karbantartási csatorna (LTSC) alapján. A Windows féléves csatorna (SAC) egyes kiadásai, például az 1709 és az 1803 nem támogatottak.

## <a name="co-scheduled-groups"></a>Együttesen ütemezett csoportok

Az Azure Container Instances támogatja az olyan [több tárolóból álló csoportok](container-instances-container-groups.md) ütemezését, amelyek osztoznak a gazdagépen, a helyi hálózaton és a tárolón, valamint azonos az életciklusuk. Ennek segítéségével kombinálhatja a fő alkalmazástárolóját más, támogató szerepű tárolókkal, például naplózó oldalkocsikkal.

## <a name="virtual-network-deployment-preview"></a>Virtuális hálózat üzembe helyezése (előzetes verzió)

Ez a jelenleg előzetes verzióban lévő Azure Container Instances-funkció lehetővé teszi [tárolópéldányok üzembe helyezését egy Azure virtuális hálózatban](container-instances-vnet.md). Ha a tárolópéldányokat a virtuális hálózat egyik alhálózatában helyezi üzembe, biztonságosan tudnak majd kommunikálni a virtuális hálózat más erőforrásaival, így a helyszíniekkel is (a [VPN Gateway-en](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy az [ExpressRoute-on](../expressroute/expressroute-introduction.md) keresztül).

> [!IMPORTANT]
> Azure Container Instances bizonyos funkciói a következők előzetes verzióban érhető el, és néhány [korlátozások érvényesek a](container-instances-vnet.md#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. Ezek a szolgáltatások egyes funkcióit általánosan (elérhetővé tétel GA) előtt módosíthatja.

## <a name="next-steps"></a>További lépések

Próbáljon üzembe helyezni egy tárolót az Azure-ban egyetlen parancs használatával a rövid útmutatónk alapján:

> [!div class="nextstepaction"]
> [Az Azure Container Instances rövid útmutatója](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
