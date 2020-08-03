---
title: Kiszolgáló nélküli tárolók az Azure-ban
description: A Azure Container Instances szolgáltatás az elkülönített tárolók Azure-ban való futtatásának leggyorsabb és legegyszerűbb módját kínálja anélkül, hogy virtuális gépeket kellene kezelnie, és nem kell magasabb szintű Orchestrator alkalmaznia.
ms.topic: overview
ms.date: 07/28/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 2871aabe4d81cfb1441e9c74c8fa24e4e906d3b9
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498684"
---
# <a name="what-is-azure-container-instances"></a>Mi az az Azure Container Instances?

Egyre többen használják a tárolókat felhőalapú alkalmazások csomagolásához, üzembe helyezéséhez és felügyeletéhez. Az Azure Container Instances a tárolók Azure-ban való futtatásának leggyorsabb és legegyszerűbb módját nyújtja anélkül, hogy virtuális gépeket kellene kezelnie vagy magasabb szintű szolgáltatást kellene alkalmaznia.

Az Azure Container Instances ideális megoldás minden olyan forgatókönyv esetében, amely elkülönített tárolókban valósulhat meg, beleértve az egyszerű alkalmazásokat, a tevékenységek automatizálását és a buildfeladatokat. Olyan forgatókönyvek esetén, ahol teljes tároló-előkészítésre van szükség, beleértve a szolgáltatások felderítését több tárolóban, az automatikus skálázást és az alkalmazások koordinált frissítését, javasoljuk, hogy az [Azure Kubernetes Service (ak) szolgáltatást](../aks/index.yml).

## <a name="fast-startup-times"></a>Rövid indítási idők

A tárolók jelentős előnyöket nyújtanak a virtuális gépekkel (VM-ekkel) szemben az indítás terén. Az Azure Container Instanceszel másodpercek alatt elindíthat egy tárolót az Azure-ban anélkül, hogy virtuális gépeket kellene kiépítenie és kezelnie.

Linux-vagy Windows-tárolói rendszerképeket hozhat a Docker hub, egy privát [Azure Container Registry](../container-registry/index.yml)vagy más felhőalapú Docker-beállításjegyzékből. Azure Container Instances gyorsítótárba helyezi az egyes általános operációsrendszer-lemezképeket, így gyorsíthatja az egyéni alkalmazások lemezképének üzembe helyezését.

> [!NOTE]
> Jelenleg nem telepíthet lemezképet helyszíni beállításjegyzékből Azure Container Instancesba.

## <a name="container-access"></a>Tároló-hozzáférés

Azure Container Instances lehetővé teszi, hogy a tároló csoportok közvetlenül az interneten legyenek kitéve IP-címmel és teljes tartománynévvel (FQDN). Tárolópéldány létrehozásakor egyéni DNS-névcímkét kell megadnia ahhoz, hogy az alkalmazás elérhető legyen a *customlabel*.*azureregion*.azurecontainer.io régióban.

A Azure Container Instances támogatja egy parancs futtatását egy futó tárolóban azáltal, hogy egy interaktív rendszerhéj segítségével segíti az alkalmazások fejlesztését és hibaelhárítását. A hozzáférés HTTPS-kapcsolaton keresztül történik, a TLS használatával biztosítva az ügyfélkapcsolatokat.

> [!IMPORTANT]
> 2020. január 13-án Azure Container Instances a TLS 1,2 használatához a kiszolgálók és alkalmazások összes biztonságos kapcsolata szükséges. A TLS 1,0 és 1,1 támogatása megszűnik.

## <a name="hypervisor-level-security"></a>Hipervizorszintű biztonság

Korábban a tárolók biztosítottak ugyan alkalmazásfüggőség-elkülönítési és erőforrás-szabályozási funkciót, azonban nem voltak eléggé megerősítve a rosszindulatú több-bérlős alkalmazásokhoz. Az Azure Container Instances használatakor az alkalmazások ugyanannyira el vannak különítve a tárolóban, mintha egy virtuális gépen futnának.


## <a name="custom-sizes"></a>Egyéni méretek

A tárolók általában egyetlen alkalmazás futtatására vannak optimalizálva, azonban ezeknek az alkalmazásoknak a pontos igényei nagyon is eltérőek lehetnek. Az Azure Container Instances optimális kihasználtságot biztosít, mivel pontosan megadható a processzormagok száma és a memória mennyisége. A fizetés az igényelt erőforrások alapján történik másodpercalapú elszámolás szerint, így tényleges igényeinek megfelelően optimalizálhatja költségeit a legkisebb részletekig.

A nagy számítási igényű feladatok, például a gépi tanulás esetében a Azure Container Instances a Linux-tárolókat az NVIDIA Tesla [GPU-erőforrások](container-instances-gpu.md) (előzetes verzió) használatára is képes ütemezni.

## <a name="persistent-storage"></a>Állandó tárolók

Az Azure Container Instances-nal való lekéréséhez és megtartásához közvetlen csatlakoztatást biztosítunk az Azure Storage által támogatott [Azure Files-megosztásokhoz](./container-instances-volume-azure-files.md) .

## <a name="linux-and-windows-containers"></a>Linux- és Windows-tárolók

Az Azure Container Instances használatával a Windows- és a Linux-tárolókat ugyanazzal az API-val ütemezheti. Egyszerűen adja meg az operációs rendszer típusát a [tárolócsoportok](container-instances-container-groups.md) létrehozásakor.

Egyes funkciók jelenleg csak Linux-tárolók:

* Több tároló tárolócsoportonként
* Kötet csatlakoztatása ([Azure Files](container-instances-volume-azure-files.md), [emptyDir](container-instances-volume-emptydir.md), [gitrepo típusú](container-instances-volume-gitrepo.md), [Secret](container-instances-volume-secret.md))
* [Erőforrás-használati metrikák](container-instances-monitor.md) Azure monitor
* [Virtuális hálózat központi telepítése](container-instances-vnet.md)
* [GPU-erőforrások](container-instances-gpu.md) (előzetes verzió)

A Windows-tárolók központi telepítése esetén a lemezképeket a gyakori [Windows alapképek](container-instances-faq.md#what-windows-base-os-images-are-supported)alapján kell használni.

> [!NOTE]
> A Windows Server 2019-alapú rendszerképek használata a Azure Container Instances előzetes verzióban érhető el.

## <a name="co-scheduled-groups"></a>Együttesen ütemezett csoportok

Az Azure Container Instances támogatja az olyan [több tárolóból álló csoportok](container-instances-container-groups.md) ütemezését, amelyek osztoznak a gazdagépen, a helyi hálózaton és a tárolón, valamint azonos az életciklusuk. Ennek segítéségével kombinálhatja a fő alkalmazástárolóját más, támogató szerepű tárolókkal, például naplózó oldalkocsikkal.

## <a name="virtual-network-deployment"></a>Virtuális hálózat központi telepítése

Azure Container Instances lehetővé teszi [a Container instances üzembe helyezését egy Azure-beli virtuális hálózatban](container-instances-vnet.md). A virtuális hálózatban lévő alhálózatba való üzembe helyezéskor a tároló példányai biztonságosan kommunikálhatnak a virtuális hálózatban lévő más erőforrásokkal, beleértve a helyszíni ( [VPN-átjárón](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [ExpressRoute](../expressroute/expressroute-introduction.md)) keresztül elérhető erőforrásokat is.

## <a name="next-steps"></a>További lépések

Próbáljon üzembe helyezni egy tárolót az Azure-ban egyetlen parancs használatával a rövid útmutatónk alapján:

> [!div class="nextstepaction"]
> [Az Azure Container Instances rövid útmutatója](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
