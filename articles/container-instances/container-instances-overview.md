---
title: Az Azure Container Instances áttekintése
description: Az Azure Container Instances ismertetése
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 03/29/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: e10d4046f543fbcc479421f4ca8f10221abc806f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="azure-container-instances"></a>Azure Container Instances

Egyre többen használják a tárolókat felhőalapú alkalmazások csomagolásához, üzembe helyezéséhez és felügyeletéhez. Az Azure Container Instances a tárolók Azure-ban való futtatásának leggyorsabb és legegyszerűbb módját nyújtja anélkül, hogy virtuális gépeket kellene kezelnie vagy magasabb szintű szolgáltatást kellene alkalmaznia.

Az Azure Container Instances ideális megoldás minden olyan forgatókönyv esetében, amely elkülönített tárolókban valósulhat meg, beleértve az egyszerű alkalmazásokat, a tevékenységek automatizálását és a buildfeladatokat. Az olyan forgatókönyvek esetében, amelyekhez teljes mértékű tárolóvezénylés szükséges, beleértve a több tárolón végzett szolgáltatásészlelést, az automatikus skálázást és az alkalmazások koordinált frissítését, az [Azure Container Service (AKS)](../aks/index.yml) használatát javasoljuk.

## <a name="fast-startup-times"></a>Rövid indítási idők

A tárolók jelentős előnyöket nyújtanak a virtuális gépekkel szemben az indítás terén. Az Azure Container Instances használatával másodpercek alatt elindíthat egy tárolót az Azure-ban anélkül, hogy virtuális gépeket kellene kiépítenie és kezelnie.

## <a name="public-ip-connectivity-and-dns-name"></a>Nyilvános IP-kapcsolat és DNS-név

Az Azure Container Instances használatával tárolóit közvetlenül is közzéteheti az interneten IP-cím és teljes tartománynév (FQDN) használatával. Tárolópéldány létrehozásakor egyéni DNS-névcímkét kell megadnia ahhoz, hogy az alkalmazás elérhető legyen a *customlabel*.*azureregion*.azurecontainer.io régióban.

## <a name="hypervisor-level-security"></a>Hipervizorszintű biztonság

Korábban a tárolók biztosítottak ugyan alkalmazásfüggőség-elkülönítési és erőforrás-szabályozási funkciót, azonban nem voltak eléggé megerősítve a rosszindulatú több-bérlős alkalmazásokhoz. Az Azure Container Instances használatakor az alkalmazások ugyanannyira el vannak különítve a tárolóban, mintha egy virtuális gépen futnának.

## <a name="custom-sizes"></a>Egyéni méretek

A tárolók általában egyetlen alkalmazás futtatására vannak optimalizálva, azonban ezeknek az alkalmazásoknak a pontos igényei nagyon is eltérőek lehetnek. Az Azure Container Instances optimális kihasználtságot biztosít, mivel pontosan megadható a processzormagok száma és a memória mennyisége. A fizetés az igényelt erőforrások alapján történik másodpercalapú elszámolás szerint, így tényleges igényeinek megfelelően optimalizálhatja költségeit a legkisebb részletekig.

## <a name="persistent-storage"></a>Állandó tárolók

Az állapotok Azure Container Instances használatával való lekéréséhez és megőrzéséhez az [Azure-fájlmegosztások közvetlen csatolásának](container-instances-mounting-azure-files-volume.md) lehetőségét kínáljuk.

## <a name="linux-and-windows-containers"></a>Linux- és Windows-tárolók

Az Azure Container Instances használatával a Windows- és a Linux-tárolókat ugyanazzal az API-val ütemezheti. Egyszerűen adja meg az operációs rendszer típusát a [tárolócsoportok](container-instances-container-groups.md) létrehozásakor.

Bizonyos funkciók jelenleg Linux-tárolókra vannak korlátozva. Arra törekszünk, hogy idővel az összes funkció elérhető legyen a Windows-tárolókon is. Az egyes platformok közötti aktuális eltérésekről az [Azure Container Instances-kvóták és -régiók rendelkezésre állása](container-instances-quotas.md) részben tájékozódhat.

## <a name="co-scheduled-groups"></a>Együttesen ütemezett csoportok

Az Azure Container Instances támogatja az olyan [több tárolóból álló csoportok](container-instances-container-groups.md) ütemezését, amelyek osztoznak a gazdagépen, a helyi hálózaton és a tárolón, valamint azonos az életciklusuk. Ennek segítéségével kombinálhatja a fő alkalmazástárolóját más, támogató szerepű tárolókkal, például naplózó oldalkocsikkal.

## <a name="next-steps"></a>További lépések

Próbáljon üzembe helyezni egy tárolót az Azure-ban egyetlen parancs használatával a rövid útmutatónk alapján:

> [!div class="nextstepaction"]
> [Az Azure Container Instances rövid útmutatója](container-instances-quickstart.md)
