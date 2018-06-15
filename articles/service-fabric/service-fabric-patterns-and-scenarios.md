---
title: Az Azure Service Fabric mintákat és forgatókönyvek |} Microsoft Docs
description: Gyakorlati tanácsokat és bizonyítása, újrahasznosítható minták megtervezésére, fejleszthet, és a Service Fabric a mikroszolgáltatások működnek.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: d5aa75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 5c2cd38001a9645011bbab84cff3b9366aa64b1d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206140"
---
# <a name="service-fabric-patterns-and-scenarios"></a>A Service Fabric mintákat és forgatókönyvek
Ha felépítése az Azure Service Fabric használatával a nagyméretű mikroszolgáltatások tartózkodik, ismerje meg, a szakértők, akik erre a platformra beépített szolgáltatásként (PaaS), és a. Ismerkedés a megfelelő architektúra, és majd megtudhatja, hogyan optimalizálható az erőforrások az alkalmazáshoz. A [Service Fabric minták és gyakorlatok](https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344) működés során a leggyakrabban ismételt valós ügyfelek Service Fabric forgatókönyvek és alkalmazási területek kérdésekre.
 
Megtudhatja, hogyan lehet a Service Fabricben mikroszolgáltatásokat megtervezni, fejleszteni és működtetni, és ehhez ajánlott eljárásokat és kipróbált, újrahasznosítható mintákat is kap. A Service Fabric áttekintést kaphat, és ezután alaposabban tanulmányozhatja a mély témakörök, amelyek közé tartozik a fürt optimalizálása és a biztonsági, örökölt alkalmazások áttelepítése léptékű játék motorok és egyéb üzemeltetési IoT. Nézze meg a különböző munkaterhelések folyamatos kézbesítési, és a Linux-támogatás és a tárolók a részleteket még beolvasása. 

## <a name="introduction"></a>Bevezetés
Gyakorlati tanácsok vizsgálatát, és Sajátítsa el kiválasztására vonatkozó platform (PaaS) szolgáltatás keresztül infrastruktúra (IaaS) szolgáltatás. Ismerje meg a részleteket a következő bevált alkalmazás tervezési alapelvek.

<table><tr><th>Videó</th><th>PowerPoint fedélzet</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=N2KwbbSGD_6405167344">
<img src="./media/service-fabric-patterns-and-scenarios/intro.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344">A Service Fabric bemutatása</a></td></tr>
</table>

## <a name="cluster-planning-and-management"></a>Tervezési és a felügyeleti fürtön
További tudnivalók a kapacitástervezés, a fürt optimalizálása és a fürt biztonsági, az Azure Service Fabric nézze meg a.

<table><tr><th>Videó</th><th>PowerPoint fedélzet</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=cyDYZcSGD_2805167344">
<img src="./media/service-fabric-patterns-and-scenarios/cluster.png" WIDTH="360" HEIGHT="244">
</a></td><td> <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=E5B3nJSGD_805167344">Fürt tervezési és kezelése</a></td></tr>
</table>

## <a name="hyper-scale-web"></a>Hyper-skálázási webes
Körül kapacitású webalkalmazás rendelkezésre állásának és megbízhatóságának, kapacitású és állapotkezelés fogalmak áttekintése.

<table><tr><th>Videó</th><th>PowerPoint fedélzet</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=NgldAdSGD_405167344">
<img src="./media/service-fabric-patterns-and-scenarios/hyperscaleweb.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=CPMLBLSGD_7705167344">Hyper-skálázási webes</a></td></tr>
</table>

## <a name="iot"></a>IoT
Megismerkedhet a az eszközök internetes hálózatát (IoT) Azure Service Fabric, beleértve az Azure IoT folyamat, a több-bérlős és a IoT léptékű környezetében.

<table><tr><th>Videó</th><th>PowerPoint fedélzet</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=naFUVeSGD_1505167344">
<img src="./media/service-fabric-patterns-and-scenarios/iot.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">IoT</a></td></tr>
</table>

## <a name="gaming"></a>Játékok
Nézze meg a kapcsolja játékokat, interaktív játékok és meglévő játék motorok üzemeltetéséhez.

<table><tr><th>Videó</th><th>PowerPoint fedélzet</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=6wECzeSGD_3805167344">
<img src="./media/service-fabric-patterns-and-scenarios/gaming.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">Játék</a></td></tr>
</table>

## <a name="continuous-delivery"></a>Folyamatos készregyártás
Megismerkedhet a fogalmakat, beleértve a Visual Studio Team Services, a munkafolyamat build/csomag/közzététele, a több környezet beállítása és a szolgáltatás csomagmegosztás folyamatos integráció/folyamatos kézbesítési.

<table><tr><th>Videó</th><th>PowerPoint fedélzet</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=78h5ofSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/cd.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=VlENvOSGD_105167344">Folyamatos kézbesítés</a></td></tr>
</table>

## <a name="migration"></a>Migrálás
Ismerje meg az áttelepítés egy felhőalapú szolgáltatás, az örökölt alkalmazások áttelepítése mellett.

<table><tr><th>Videó</th><th>PowerPoint fedélzet</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=hd1cMgSGD_5605167344">
<img src="./media/service-fabric-patterns-and-scenarios/migration.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=GQAq4QSGD_8305167344">Áttelepítés</a></td></tr>
</table>

## <a name="containers-and-linux-support"></a>Tárolók és a Linux-támogatás
A válasz a kérdésre "Miért tárolók?" További tudnivalók a Windows-tárolók, támogatja a Linux és Linux tárolók vezénylési előzetes verzióját. Valamint megtudhatja, hogyan telepíthetők át a .NET Core alkalmazások Linux.

<table><tr><th>Videó</th><th>PowerPoint fedélzet</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=V1ERJhSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/containers.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mlYsZRSGD_2105167344">Tárolók és a Linux-támogatás</a></td></tr>
</table>

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Service Fabric mintákat és forgatókönyvek, további tudnivalókat arról, hogy hogyan [létrehozása és kezelése a fürtök](service-fabric-deploy-anywhere.md), [Felhőszolgáltatások alkalmazásokat telepíthet át a Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md), [folyamatos kézbesítési beállítása](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), és [tároló üzembe helyezése](service-fabric-containers-overview.md).
