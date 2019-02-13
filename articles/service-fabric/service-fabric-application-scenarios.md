---
title: Alkalmazáshasználati helyzetek és tervezés |} A Microsoft Docs
description: Kategóriák felhőalapú alkalmazások a Service Fabric áttekintése. A cikk ismerteti, állapotalapú és állapotmentes szolgáltatások használó alkalmazás-tervezés.
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
ms.openlocfilehash: a84d8fead42f8314decc144f01d8de1dac30bbdf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199476"
---
# <a name="service-fabric-application-scenarios"></a>A Service Fabric-alkalmazás-forgatókönyvek
Az Azure Service Fabric egy megbízható és rugalmas platform, amely lehetővé teszi, hogy írása és futtatása a számos különböző típusú üzleti alkalmazásokat és szolgáltatásokat kínál. Ezek az alkalmazások és a mikroszolgáltatások lehet állapot nélküli vagy állapotalapú, és azok erőforrás kiegyensúlyozott a maximális hatékonyság a virtuális gépek között. A Service fabric egyedi architektúra lehetővé teszi, hogy közel valós idejű adatelemzés, a memóriabeli számítási, a párhuzamos tranzakciókat és a eseményfeldolgozás az alkalmazásokban. Egyszerűen méretezhető az alkalmazások felfelé és lefelé (nagyon bejövő vagy kimenő), a változó erőforrás követelményeitől függően.

Az Azure-ban a Service Fabric platformot ideális megoldást biztosít az alkalmazások az alábbi kategóriákban:

* **Magas rendelkezésre állású szolgáltatások**: Service Fabric-szolgáltatások biztosítanak a gyors feladatátvétel több szolgáltatás másodlagos replikák létrehozásával. Ha hardveres vagy más hiba miatt leáll egy csomópont, folyamat vagy egy adott szolgáltatás, a másodlagos replikára lett előléptetve, egy minimális szolgáltatáskimaradást elsődleges replikán.
* **Méretezhető szolgáltatásokkal**: Egyes szolgáltatások particionálható, így-állapot működéséhez a fürtszinten horizontálisan felskálázott. Emellett az egyes szolgáltatások létrehozhatók és menet közben eltávolítva. Szolgáltatások gyorsan és egyszerűen horizontálisan felskálázott néhány példányokról néhány csomópontján el több ezer példány sok csomóponton, és ezután skálázva ismét erőforrás igényeitől függően. A Service Fabric használhatja ezeket a szolgáltatásokat és azok teljes életciklusának kezeléséről.
* **A gyorsítótárazó adatok számítási**: A Service Fabric lehetővé teszi a build adatokat, bemeneti és kimeneti és számítási teljesítményt igénylő állapotalapú alkalmazások. A Service Fabric lehetővé teszi, hogy az Elhelyezés feldolgozást (számítást) és az adatok az alkalmazásokban. Normális esetben az alkalmazás az adatokhoz való hozzáférésre van szüksége, ha van egy külső tároló vagy gyorsítótár adatrétegbeli társított hálózati késés. Az állapotalapú Service Fabric-szolgáltatások a késés eltávolításáig, további nagy teljesítményű engedélyezése olvasási és írási. Például tegyük fel, hogy rendelkezik-e olyan alkalmazás, amely közel valós idejű javaslat beállításokat 100-nál kevesebb idő ezredmásodpercben üzenetváltási idő követelmény élvező hajt végre. A Service Fabric-szolgáltatások (ahol a kijelölt javaslat számítási közös elhelyezésű az adatok és a szabályok) késés és a teljesítmény jellemzői rugalmas élményt nyújt a felhasználónak, a standard végrehajtási modell a képest a szükséges adatok lehívása távoli tárolóról kellene.  
* **Munkamenet-alapú interaktív alkalmazások**: A Service Fabric akkor hasznos, ha az Ön alkalmazásai, például az online játékok vagy azonnali üzenetküldés, alacsony késleltetésű olvasási és írási igényelnek. A Service Fabric lehetővé teszi, hogy ezek interaktív, állapotalapú alkalmazások készítését egy külön tároló vagy gyorsítótár, amely szükséges az állapot nélküli alkalmazások létrehozása nélkül. (Ez növeli a késés és potenciálisan vezet be adatkonzisztenciával kapcsolatos problémák.).
* **A Data analytics és a munkafolyamatok**: A gyors olvasások és írások Service fabric engedélyezze az alkalmazásokat, amelyek megbízhatóan feldolgozni eseményeket vagy adatfolyamokat. A Service Fabric emellett lehetővé teszi az alkalmazásokat, amelyek ismertetik a feldolgozási folyamatokat, ahol az eredményeket a megbízható és átadott be a következő feldolgozási feladat adatveszteség nélküli kell. Ezek közé tartozik a tranzakciós és pénzügyi rendszerek, ahol adatok konzisztencia és a számítás garanciák nélkülözhetetlenek.
* **Adatgyűjtés,-feldolgozás és az IoT**: Service Fabric nagy méretű kezeli, és közel valós idejű, az állapotalapú szolgáltatások révén van, mivel ideális a több millió eszköz adatok feldolgozása az adatokat az eszköz és a számítási amelyeknél közös elhelyezésű.
Úgy találtuk, hogy számos olyan ügyfelek, akik készült IoT-rendszerekben, beleértve a Service Fabric használatával [BMW](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/24/service-fabric-customer-profile-bmw-technology-corporation/), [Schneider Electric](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/05/service-fabric-customer-profile-schneider-electric/) és [háló rendszerek](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/20/service-fabric-customer-profile-mesh-systems/).

## <a name="application-design-case-studies"></a>Alkalmazás tervezési esettanulmányok
A bemutató a Service Fabric alkalmazások használatáról esettanulmányok számos közzétett a [Service Fabric-csapat blogja](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/) és a [mikroszolgáltatás-alapú megoldások hely](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Mikroszolgáltatásokból álló, állapot nélküli és állapotalapú alkalmazások tervezése
Az Azure-Felhőszolgáltatás munkavégző szerepköreit alkalmazásokat, amelyek állapotmentes szolgáltatások. Ezzel szemben az állapotalapú mikroszolgáltatások karbantartása a kérés- és a nem mérvadó állapotuk. Ez biztosítja a magas rendelkezésre állás és konzisztencia az állapot egyszerű API-k segítségével, amelyek alapját a replikációs tranzakciós garanciákat biztosítanak. A Service Fabric állapotalapú szolgáltatások biztosításával mindenki számára a magas rendelkezésre állás érdekében minden típusú alkalmazásokat, nem csak adatbázisok és más adattárakban kerülnének. Ez a természetes metódushívásainak. Alkalmazások már áthelyezte, pusztán a relációs adatbázisok az NoSQL-adatbázisok magas rendelkezésre állását. Maguk az alkalmazások most már az "Forró" állapot és az azokhoz tartozó kezelt további teljesítménynövekedést a megbízhatóság, a konzisztencia, vagy a rendelkezésre állási feláldozása nélkül adatokat is rendelkezik.

Mikroszolgáltatások álló alkalmazások létrehozását, általában rendelkezik állapot nélküli webes alkalmazások (az ASP.NET, Node.js stb.) kombinációjából hívja meg az alakzatot állapot nélküli és állapotalapú üzleti középső rétegű szolgáltatások, az összes üzembe helyezve az ugyanazon a Service Fabric fürt használatával a Service Fabric-telepítési parancsokat. Ezen szolgáltatások mindegyike a független méretezési, a megbízhatóság és az Erőforrás kihasználtsága, nagy mértékben javítva a rugalmasságot az alkalmazásfejlesztés és életciklus-kezelés tekintetében.

Állapotalapú mikroszolgáltatások egyszerűsítése alkalmazás tervek, mert azok eltávolítása a további várólisták és a rendelkezésre állás és a késés igényeinek tisztán állapot nélküli alkalmazások hagyományosan szükséges gyorsítótárak van szükség. Mivel az állapotalapú szolgáltatások természetes módon magas rendelkezésre állású és kis késésű, ez azt jelenti, hogy az alkalmazás egészére kezelése kevesebb mozgó részből áll. Az alábbi ábrák bemutatják tervezése az állapotmentes alkalmazások, a másik az állapot-nyilvántartó közötti különbségek. Előnyeit kihasználva a [Reliable Services](service-fabric-reliable-services-introduction.md) és [Reliable Actors](service-fabric-reliable-actors-introduction.md) programozási modelleket, állapotalapú szolgáltatások csökkenthető alkalmazás elérése a nagy átviteli sebességű és kis késése mellett.

## <a name="an-application-built-using-stateless-services"></a>Egy alkalmazás állapotmentes szolgáltatások használatával
![Alkalmazás állapotmentes szolgáltatás használatával][Image1]

## <a name="an-application-built-using-stateful-services"></a>Egy alkalmazás, állapotalapú szolgáltatások használatával
![Alkalmazás állapotmentes szolgáltatás használatával][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések

* További információ [ügyféleset-tanulmányok](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/)
* Tudjon meg többet [minták és forgatókönyvek](service-fabric-patterns-and-scenarios.md)

* Ismerkedés a Service fabric állapot nélküli és állapotalapú szolgáltatások létrehozásával [a reliable services](service-fabric-reliable-services-quick-start.md) és [a reliable actors](service-fabric-reliable-actors-get-started.md) programozási modelljeivel.
* A következő témakörökben is talál:
  * [Tudnivalók a microservicesről](service-fabric-overview-microservices.md)
  * [Definiálása és kezelése](service-fabric-concepts-state.md)
  * [Service Fabric-szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
  * [A Service Fabric-szolgáltatások méretezése](service-fabric-concepts-scalability.md)
  * [A Service Fabric services particionálása](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
