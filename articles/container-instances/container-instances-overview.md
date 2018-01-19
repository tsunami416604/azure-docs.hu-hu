---
title: "Az Azure Container Instances áttekintése"
description: "Az Azure Container Instances ismertetése"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 01e539856adbdcf02dc4e49087a3ab71b328db5a
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="azure-container-instances"></a>Azure Container Instances

Egyre többen használják a tárolókat felhőalapú alkalmazások csomagolásához, üzembe helyezéséhez és felügyeletéhez. Az Azure Container Instances a tárolók Azure-ban való futtatásának leggyorsabb és legegyszerűbb módját nyújtja anélkül, hogy virtuális gépeket kellene kiépítenie vagy magasabb szintű szolgáltatást kellene alkalmaznia.

Az Azure Container Instances ideális megoldás minden olyan forgatókönyv esetében, amely elkülönített tárolókban valósulhat meg, beleértve az egyszerű alkalmazásokat, a tevékenységek automatizálását és a buildfeladatokat. Az olyan forgatókönyvek esetében, amelyekhez teljes mértékű tárolóvezénylés szükséges, beleértve a több tárolón végzett szolgáltatásészlelést, az automatikus skálázást és az alkalmazások koordinált frissítését, az [Azure Container Service (AKS)](../aks/index.yml) használatát javasoljuk.

## <a name="fast-startup-times"></a>Rövid indítási idők

A tárolók jelentős előnyöket nyújtanak a virtuális gépekkel szemben az indítás terén. Az Azure Container Instanceszel másodpercek alatt elindíthat egy tárolót az Azure-ban anélkül, hogy virtuális gépeket kellene kiépítenie és kezelnie.

## <a name="hypervisor-level-security"></a>Hipervizorszintű biztonság

Korábban a tárolók biztosítottak ugyan alkalmazásfüggőség-elkülönítési és erőforrás-szabályozási funkciót, azonban nem voltak eléggé megerősítve a rosszindulatú több-bérlős alkalmazásokhoz. Az Azure Container Instances használatakor az alkalmazások ugyanannyira el vannak különítve a tárolóban, mintha egy virtuális gépen futnának.

## <a name="custom-sizes"></a>Egyéni méretek

A tárolók általában egyetlen alkalmazás futtatására vannak optimalizálva, azonban ezeknek az alkalmazásoknak a pontos igényei nagyon is eltérőek lehetnek. Az Azure Container Instanceszel pontosan annyi processzormagot és memóriát igényelhet, amennyire szüksége van. A fizetés az igényelt erőforrások alapján történik másodpercalapú elszámolás szerint, így az igényeinek megfelelően optimalizálhatja költségeit a legkisebb részletekig.

## <a name="public-ip-connectivity"></a>Nyilvános IP-kapcsolat

Az Azure Container Serviceszel tárolóit közvetlenül is közzéteheti az interneten nyilvános IP-cím használatával. A későbbiekben bővíteni fogjuk a hálózati képességeket, hogy azok magukban foglalják az integrációt a virtuális hálózatokkal, a terheléselosztókkal és az Azure hálózati infrastruktúra egyéb alapvető részeivel.

## <a name="persistent-storage"></a>Állandó tárolók

Az állapotok az Azure Container Instanceszel való lekéréséhez és megőrzéséhez az [Azure-fájlmegosztások közvetlen csatolásának](container-instances-mounting-azure-files-volume.md) lehetőségét kínáljuk.

## <a name="linux-and-windows-containers"></a>Linux- és Windows-tárolók

Az Azure Container Instances használatával a Windows- és a Linux-tárolókat ugyanazzal az API-val ütemezheti. Egyszerűen adja meg az operációs rendszer típusát a [tárolócsoportok](container-instances-container-groups.md) létrehozásakor.

Bizonyos funkciók jelenleg Linux-tárolókra vannak korlátozva. Arra törekszünk, hogy idővel az összes funkció elérhető legyen a Windows-tárolókon is. Az egyes platformok közötti aktuális eltérésekről a [Azure Container Instances-kvóták és -régiók rendelkezésre állása](container-instances-quotas.md) részben tájékozódhat.

## <a name="co-scheduled-groups"></a>Együttesen ütemezett csoportok

Az Azure Container Instances támogatja az olyan [több tárolóból álló csoportok](container-instances-container-groups.md) ütemezését, amelyek osztoznak a gazdagépen, a helyi hálózaton és a tárolón, valamint azonos az életciklusuk. Ennek segítéségével kombinálhatja a fő alkalmazását más, támogató szerepű, például naplózó alkalmazásokkal.

## <a name="next-steps"></a>További lépések

Próbáljon üzembe helyezni egy tárolót az Azure-ban egyetlen parancs használatával a [rövid útmutatóink](container-instances-quickstart.md) segítségével.