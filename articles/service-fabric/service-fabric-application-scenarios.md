---
title: Az alkalmazás-forgatókönyvek és kialakítása |} Microsoft Docs
description: A Service Fabric felhőalkalmazásokhoz kategóriáinak áttekintése. Állapot nélküli és állapotalapú alkalmazások és szolgáltatások használó alkalmazás tervét, és ismerteti.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/02/2017
ms.author: mfussell
ms.openlocfilehash: c0a9b24704a91d6a6893937b4ee03765fb05f092
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207536"
---
# <a name="service-fabric-application-scenarios"></a>A Service Fabric-alkalmazás-forgatókönyvek
Az Azure Service Fabric biztosít a megbízható és rugalmas platform, amely lehetővé teszi, és számos üzleti alkalmazások és szolgáltatások futtatásához. Ezek az alkalmazások és mikroszolgáltatások létrehozására lehet állapot nélküli és állapotalapú, és erőforrás terhelésű hatékonyságának maximalizálása a virtuális gépek között. A Service Fabric egyedi architektúra lehetővé teszi a közel valós idejű adatelemzés, a memórián belüli számítási, a párhuzamos tranzakciókat és a esemény feldolgozását az alkalmazásokban. Könnyedén méretezhető az alkalmazások felfelé vagy lefelé (valóban bejövő vagy kimenő), a változó erőforrás követelményeitől függően.

Az Azure Service Fabric-platformról ideális megoldás az alkalmazások az alábbi kategóriákban:

* **Magas rendelkezésre állású szolgáltatások**: a Service Fabric szolgáltatásokat biztosítanak az gyors több másodlagos szolgáltatás replikák létrehozásával. Egy csomópont, a folyamat vagy a szolgáltatás hardver- vagy egyéb hiba miatt nem működik, ha a másodlagos replikák egyik szolgáltatás minimális szintű kiesése elsődleges másodpéldányt van előléptetve.
* **Méretezhető szolgáltatások**: egyéni szolgáltatások lehet particionálni, így-állapot működéséhez a fürtön terjeszthető ki. Emellett az egyes szolgáltatások hozható létre és parancsprogramok eltávolítása. Szolgáltatások gyorsan és egyszerűen horizontálisan néhány csomópontján néhány példányokból több ezer példányok sok csomópontján, és majd méretezve, az ebben az esetben erőforrás igényeitől függően. A Service Fabric segítségével ezeket a szolgáltatásokat, és a teljes életciklusának kezelését.
* **A következő nem statikus adatok számítási**: a Service Fabric lehetővé teszi, hogy adatokat, elkészítéséhez bemeneti/kimeneti és állapotalapú alkalmazások számítási. A Service Fabric lehetővé teszi, hogy a közös elhelyezés feldolgozás (számítás) és az adatok az alkalmazások. Általában az alkalmazás által kért adatokhoz való hozzáférést, ha van egy külső adatforráshoz gyorsítótár vagy tárolási réteget társított hálózati késés. Állapotalapú Service Fabric-szolgáltatásokkal a késés nem szűnik, beolvassa és további performant engedélyezése. Tegyük fel például például, hogy rendelkezik-e olyan alkalmazás, amely közel valós idejű ajánlott beállításokat, kevesebb mint 100 milliomod másodperc körbejárási időt követelményt rendelkező ügyfelek esetén hajt végre. A késleltetés és a teljesítmény jellemzőkkel (ahol a számítási javaslat kijelölés közös elhelyezésű az adatok és a szabályok) a Service Fabric-szolgáltatás rugalmas élményt nyújt a felhasználó szabványos implementációjától modelljét képest a szükséges adatok beolvasása a távtároló kellene.  
* **Munkamenet-alapú interaktív alkalmazások**: a Service Fabric akkor hasznos, ha az alkalmazások, például az online játékok vagy csevegőüzenetben, kis késleltetésű olvasása és írása szükséges. A Service Fabric külön áruházból vagy az állapot nélküli alkalmazások szükség szerint a gyorsítótár létrehozása nélkül ezeket interaktív, az állapotalapú alkalmazások létrehozását teszi lehetővé. (Ez növeli a késés és potenciálisan vezet be konzisztenciabeli problémákat.).
* **Adatelemzés és munkafolyamatok**: A gyors olvasást és az írás a Service Fabric lehetővé teszik az alkalmazások számára események vagy adatstreamek kell megbízhatóan feldolgozni. A Service Fabric azt is lehetővé teszi, hogy a feldolgozási folyamatok, ahol eredményeket kell a megbízható és továbbítása a következő feldolgozási szint adatvesztés nélkül átadott leíró alkalmazások. Ezek közé tartozik a tranzakciós és pénzügyi rendszerek, ahol adatok konzisztencia- és számítási garanciák nélkülözhetetlenek.
* **Az adatgyűjtés, feldolgozása és az IoT**: mivel a Service Fabric nagy méretű kezeli, és rendelkezik az állapot-nyilvántartó szolgáltatásokon keresztül az alacsony késleltetés, ideális adatok feldolgozása a eszközök millióira ahol az eszköz és a számítási adatai egy helyen találhatók.
Úgy találtuk, hogy létrehozta a buildet használ, beleértve a Service Fabric IoT rendszerek több felhasználókat [BMW](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/24/service-fabric-customer-profile-bmw-technology-corporation/), [Schneider elektromos](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/05/service-fabric-customer-profile-schneider-electric/) és [háló rendszerek](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/20/service-fabric-customer-profile-mesh-systems/).

## <a name="application-design-case-studies"></a>Alkalmazás tervezési esettanulmányok
Megjeleníti a Service Fabric alkalmazások használatáról esettanulmányok számos közzé a [Service Fabric csapat blogja](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/) és a [mikroszolgáltatások megoldások hely](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Az állapotmentes és állapotalapú mikroszolgáltatások álló alkalmazások
Alkalmazások az Azure Cloud Service feldolgozói szerepkörök az állapotmentes szolgáltatások egy példája. Állapot-nyilvántartó mikroszolgáltatások ellentétben a kérelem és a válaszában mérvadó állapotukra karbantartása. Ez biztosítja a magas rendelkezésre állás és a tranzakciós replikáció által támogatott garanciákat nyújt egyszerű API-k segítségével a szerinti konzisztencia. A Service Fabric állapotalapú szolgáltatások democratize magas rendelkezésre állású, minden típusú alkalmazásokat, nem csak adatbázisok és egyéb adattárakhoz állapotba kerülne. Ez a természetes előmenetel. Alkalmazások már a NoSQL-adatbázisok magas rendelkezésre állású tisztán relációs adatbázisok az került át. Maguk az alkalmazások most a "Forró" állapot és a rajtuk kezelt további teljesítménynövekedés érhető a megbízhatóságot, konzisztencia vagy rendelkezésre állási feláldozása nélkül is rendelkezhetnek.

Ha a mikroszolgáltatások álló alkalmazások létrehozásához, általában akkor az állapot nélküli web apps (ASP.NET, Node.js, stb.) kombinációjából hívja meg az állapotmentes és állapotalapú üzleti középső rétegbeli szolgáltatások telepítését, az összes helyezhető üzembe a azonos a Service Fabric fürt használatával a Service Fabric telepítési parancsok. Ezek a szolgáltatások egy független méretezés, a megbízhatóság és az erőforrás-használat, fejlesztési és életciklus-kezelés agilitásának nagy mértékben javítva tekintetében.

Állapot-nyilvántartó mikroszolgáltatások egyszerűbbé teszik alkalmazás tervek, mert azok távolítsa el a további várólisták és a rendelkezésre állás és a késési követelménynek tisztán állapot nélküli alkalmazások hagyományosan szükséges gyorsítótárak van szükség. Mivel az állapotalapú szolgáltatások természetes magas rendelkezésre állású és alacsony késést, ez azt jelenti, hogy vannak-e az alkalmazás egészének kezeléséhez kevesebb mozgó elemeket. Az alábbi ábrák bemutatják, állapot nélküli alkalmazások, a másik állapotalapú tervezése közötti különbséget. Kihasználásával a [Reliable Services](service-fabric-reliable-services-introduction.md) és [Reliable Actors](service-fabric-reliable-actors-introduction.md) programozási modellek, állapotalapú szolgáltatások alkalmazás bonyolultságának csökkentése nagyobb teljesítményt és alacsony késést elérése közben.

## <a name="an-application-built-using-stateless-services"></a>Egy alkalmazás állapotmentes szolgáltatások segítségével
![Állapot nélküli szolgáltatást használó alkalmazások][Image1]

## <a name="an-application-built-using-stateful-services"></a>Egy alkalmazás állapotalapú szolgáltatások segítségével
![Állapot nélküli szolgáltatást használó alkalmazások][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések

* Figyelésére [ügyfél esettanulmányok](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=qDJnf86yC_5206218965
)
* További információ a [ügyfél esettanulmányok](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/)
* További információ [mintákat és forgatókönyvek](service-fabric-patterns-and-scenarios.md)

* Bevezetés a Service Fabric az állapotmentes és állapotalapú szolgáltatással építése [megbízható szolgáltatások](service-fabric-reliable-services-quick-start.md) és [megbízható szereplője](service-fabric-reliable-actors-get-started.md) programozási modellek.
* Tekintse meg a következő témaköröket is:
  * [További részletek a mikroszolgáltatások](service-fabric-overview-microservices.md)
  * [Meghatározhatja és kezelheti a szolgáltatás állapota](service-fabric-concepts-state.md)
  * [A Service Fabric-szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
  * [Skála Service Fabric-szolgáltatások](service-fabric-concepts-scalability.md)
  * [Partíció Service Fabric-szolgáltatások](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
