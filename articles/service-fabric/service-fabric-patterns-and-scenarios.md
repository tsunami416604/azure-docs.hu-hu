---
title: Az Azure Service Fabric-minták és forgatókönyvek |} A Microsoft Docs
description: Ajánlott eljárások és bevált és újra felhasználható minták tervezését, fejlesztését, és a Service Fabricben mikroszolgáltatásokat működnek.
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
ms.openlocfilehash: 3bd77891cc7508eeb1fee2152d37478c654a7e37
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44294703"
---
# <a name="service-fabric-patterns-and-scenarios"></a>A Service Fabric-minták és forgatókönyvek
Ha nagyméretű mikroszolgáltatás-alapú Azure Service Fabric segítségével készíthet, tanuljon a szakértőkkel, akik alapján terveztük és készítettük erre a platformra (PaaS) szolgáltatás. Ismerkedés a megfelelő architektúrát, és megismerheti, hogyan optimalizálhatja az erőforrásokat az alkalmazáshoz. A [Service Fabric-minták és gyakorlatok](https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344) tanfolyam a leggyakrabban a való életből vett ügyfelek által a Service Fabric-forgatókönyvek és alkalmazási területek kapcsolatban felmerülő kérdésekre.
 
Megtudhatja, hogyan lehet a Service Fabricben mikroszolgáltatásokat megtervezni, fejleszteni és működtetni, és ehhez ajánlott eljárásokat és kipróbált, újrahasznosítható mintákat is kap. A Service Fabric áttekintése, majd részletesen témakörök, amelyek fürt optimalizálása és a biztonság, az örökölt alkalmazások áttelepítése ipari méretekben, a üzemeltetési játékmotorokkal és egyéb IoT. Tekintse meg a különböző számítási feladatok folyamatos készregyártás, és akár részletes információkhoz juthat a Linux-támogatást és a tárolók. 

## <a name="introduction"></a>Bevezetés
Hogyan kiválasztásáról platform (PaaS) szolgáltatás infrastruktúrán szolgáltatás (IaaS), és megismerheti az ajánlott eljárásokat. Részletes információkkal szolgál a következő bevált alkalmazás tervezési alapelvek.

<table><tr><th>Videó</th><th>A PowerPoint-bemutatót</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=N2KwbbSGD_6405167344">
<img src="./media/service-fabric-patterns-and-scenarios/intro.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344">Bevezetés a Service Fabric</a></td></tr>
</table>

## <a name="cluster-planning-and-management"></a>Tervezés és a felügyeleti fürt
Útmutató kapacitásának megtervezése, a fürt optimalizálása és a fürt biztonsági, az Azure Service Fabric tekintse meg a.

<table><tr><th>Videó</th><th>A PowerPoint-bemutatót</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=cyDYZcSGD_2805167344">
<img src="./media/service-fabric-patterns-and-scenarios/cluster.png" WIDTH="360" HEIGHT="244">
</a></td><td> <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=E5B3nJSGD_805167344">Fürt megtervezése és kezelése</a></td></tr>
</table>

## <a name="hyper-scale-web"></a>Nagy kapacitású webes
Nagy kapacitású webes, beleértve a rendelkezésre állás és megbízhatóság, rendkívül nagy kapacitású és állapot-felügyeleti körül fogalmak áttekintése.

<table><tr><th>Videó</th><th>A PowerPoint-bemutatót</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=NgldAdSGD_405167344">
<img src="./media/service-fabric-patterns-and-scenarios/hyperscaleweb.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=CPMLBLSGD_7705167344">Nagy kapacitású webes</a></td></tr>
</table>

## <a name="iot"></a>IoT
Fedezze fel a dolgok Internete (IoT) az Azure Service Fabric, beleértve az Azure IoT folyamatok, a több-bérlős és az IoT ipari méretekben kontextusában.

<table><tr><th>Videó</th><th>A PowerPoint-bemutatót</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=naFUVeSGD_1505167344">
<img src="./media/service-fabric-patterns-and-scenarios/iot.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">IoT</a></td></tr>
</table>

## <a name="gaming"></a>Játékok
Tekintse meg a fordulókra osztott játékok készíthetők, interaktív játékok és futtató meglévő játékmotorokkal.

<table><tr><th>Videó</th><th>A PowerPoint-bemutatót</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=6wECzeSGD_3805167344">
<img src="./media/service-fabric-patterns-and-scenarios/gaming.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">Játékok</a></td></tr>
</table>

## <a name="continuous-delivery"></a>Folyamatos készregyártás
Ismerje meg a fogalommal, mint például a folyamatos integráció/folyamatos teljesítés a Azure folyamatok, a build és csomag/közzétételi munkafolyamat, a több környezet beállítása és a szolgáltatás csomagmegosztás.

<table><tr><th>Videó</th><th>A PowerPoint-bemutatót</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=78h5ofSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/cd.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=VlENvOSGD_105167344">Folyamatos készregyártás</a></td></tr>
</table>

## <a name="migration"></a>Migrálás
Ismerje meg az örökölt alkalmazások áttelepítése mellett egy felhőszolgáltatás-ről.

<table><tr><th>Videó</th><th>A PowerPoint-bemutatót</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=hd1cMgSGD_5605167344">
<img src="./media/service-fabric-patterns-and-scenarios/migration.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=GQAq4QSGD_8305167344">Áttelepítés</a></td></tr>
</table>

## <a name="containers-and-linux-support"></a>Tárolók és a Linux-támogatás
A választ a kérdésére lekérése "Miért tárolók?" További információ a Windows-tárolók, támogatja a Linux és a Linux-tárolók vezénylési előzetes. Emellett ismerje meg, hogyan telepítheti át a .NET Core alkalmazásokat a Linux.

<table><tr><th>Videó</th><th>A PowerPoint-bemutatót</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=V1ERJhSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/containers.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mlYsZRSGD_2105167344">Tárolók és a Linux-támogatás</a></td></tr>
</table>

## <a name="next-steps"></a>További lépések
Most, hogy megismerkedett a Service Fabric-minták és forgatókönyvek, további információ: kapcsolatos [fürtök létrehozása és felügyelete](service-fabric-deploy-anywhere.md), [Cloud Services alkalmazások migrálása a Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md), [beállítása a folyamatos teljesítés](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), és [tárolók üzembe helyezése](service-fabric-containers-overview.md).
