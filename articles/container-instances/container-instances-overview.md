---
title: "Az Azure Container Instances áttekintése"
description: "Az Azure Container Instances ismertetése"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 07/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 98feed192f21cbc2bf9197b6d32646f50ddbd0c5
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-instances"></a>Azure Container Instances

Egyre többen használják a tárolókat felhőalapú alkalmazások csomagolásához, üzembe helyezéséhez és felügyeletéhez. Az Azure Container Instances a tárolók Azure-ban való futtatásának leggyorsabb és legegyszerűbb módját nyújtja anélkül, hogy virtuális gépeket kellene kiépítenie vagy magasabb szintű szolgáltatást kellene alkalmaznia.

Az Azure Container Instances ideális megoldás minden olyan forgatókönyv esetében, amely elkülönített tárolókban valósulhat meg, beleértve az egyszerű alkalmazásokat, a tevékenységek automatizálását és a buildfeladatokat. Az olyan forgatókönyvek esetében, amelyekhez teljes mértékű tárolóvezénylés szükséges, beleértve a több tárolón végzett szolgáltatásészlelést, az automatikus skálázást és az alkalmazások koordinált frissítését, az [Azure Container Service](https://docs.microsoft.com/azure/container-service/) használatát javasoljuk.

## <a name="fast-startup-times"></a>Rövid indítási idők

A tárolók jelentős előnyöket nyújtanak a virtuális gépekkel szemben az indítás terén. Az Azure Container Instanceszel másodpercek alatt elindíthat egy tárolót az Azure-ban anélkül, hogy virtuális gépeket kellene kiépítenie és kezelnie.

## <a name="hypervisor-level-security"></a>Hipervizorszintű biztonság

Korábban a tárolók biztosítottak ugyan alkalmazásfüggőség-elkülönítési és erőforrás-szabályozási funkciót, azonban nem voltak eléggé megerősítve a rosszindulatú több-bérlős alkalmazásokhoz. Az Azure Container Instances használatakor az alkalmazások ugyanannyira el vannak különítve a tárolóban, mintha egy virtuális gépen futnának.

## <a name="custom-sizes"></a>Egyéni méretek

A tárolók általában egyetlen alkalmazás futtatására vannak optimalizálva, azonban ezeknek az alkalmazásoknak a pontos igényei nagyon is eltérőek lehetnek. Az Azure Container Instanceszel pontosan annyi processzormagot és memóriát igényelhet, amennyire szüksége van. A fizetés az igényelt erőforrások alapján történik másodpercalapú elszámolás szerint, így az igényeinek megfelelően optimalizálhatja költségeit a legkisebb részletekig.

## <a name="public-ip-connectivity"></a>Nyilvános IP-kapcsolat

Az Azure Container Serviceszel tárolóit közvetlenül is közzéteheti az interneten nyilvános IP-cím használatával. A későbbiekben bővíteni fogjuk a hálózati képességeket, hogy azok magukban foglalják az integrációt a virtuális hálózatokkal, a terheléselosztókkal és az Azure hálózati infrastruktúra egyéb alapvető részeivel.

## <a name="persistent-storage"></a>Állandó tárolók

Az állapotok az Azure Container Instanceszel való lekéréséhez és megőrzéséhez az Azure-fájlmegosztások közvetlen csatolásának lehetőségét kínáljuk.

## <a name="linux-and-windows-containers"></a>Linux- és Windows-tárolók

Az Azure Container Instances használatával a Windows- és a Linux-tárolókat ugyanazzal az API-val ütemezheti. Egyszerűen határozza meg az alap operációs rendszer típusát, és minden más ugyanúgy fog működni.

## <a name="co-scheduled-groups"></a>Együttesen ütemezett csoportok

Az Azure Container Instances támogatja az olyan több tárolóból álló csoportok ütemezését, amelyek osztoznak a gazdagépen, a helyi hálózaton és a tárolón, valamint azonos az életciklusuk. Ennek segítéségével kombinálhatja a fő alkalmazását más, támogató szerepű, például naplózó alkalmazásokkal.

## <a name="next-steps"></a>Következő lépések

Próbáljon üzembe helyezni egy tárolót az Azure-ban egyetlen parancs használatával a [rövid útmutatóink](container-instances-quickstart.md) segítségével.