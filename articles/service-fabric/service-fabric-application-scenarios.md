---
title: Alkalmazáshasználati helyzetek és tervezés |} A Microsoft Docs
description: Kategóriák felhőalapú alkalmazások a Service Fabric áttekintése. A cikk ismerteti, állapotalapú és állapotmentes szolgáltatások használó alkalmazás-tervezés.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: f7ad295eb30d257cd195ae02d5a5b1d85de76bda
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228499"
---
# <a name="service-fabric-application-scenarios"></a>A Service Fabric-alkalmazás-forgatókönyvek
Az Azure Service Fabric egy megbízható és rugalmas platform, amely lehetővé teszi, hogy írása és futtatása a számos különböző típusú üzleti alkalmazásokat és szolgáltatásokat kínál. Ezek az alkalmazások és a mikroszolgáltatások lehet állapot nélküli vagy állapotalapú, és azok erőforrás kiegyensúlyozott a maximális hatékonyság a virtuális gépek között. A Service fabric egyedi architektúra lehetővé teszi, hogy közel valós idejű adatelemzés, a memóriabeli számítási, a párhuzamos tranzakciókat és a eseményfeldolgozás az alkalmazásokban. Egyszerűen méretezhető az alkalmazások felfelé és lefelé (nagyon bejövő vagy kimenő), a változó erőforrás követelményeitől függően.

Tervezési útmutató az alkalmazások létrehozására, olvassa el a [mikroszolgáltatási architektúra az Azure Service Fabricban](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) és [ajánlott eljárások használatával a Service Fabric alkalmazás-tervezés](service-fabric-best-practices-applications.md)

A Service Fabric platformot ideális megoldás a következő típusú alkalmazásokat:

* **Adatok gyűjtése, feldolgozása és IoT**: Service Fabric nagy méretű kezeli, és közel valós idejű, az állapotalapú szolgáltatások révén van, mivel ideális a több millió eszköz adatok feldolgozása az adatokat az eszköz és a számítási amelyeknél ugyanarra a szalagra.

    Ügyfelek, akik készült Service Fabric használatával IoT-szolgáltatások közé tartozik [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [Megoldástervezője](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), és [rendszerek háló](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Játékok és a munkamenet-alapú interaktív alkalmazások**: A Service Fabric ideális, ha az alkalmazás által kért alacsony késleltetésű olvasási és írási, mint például az online játékok vagy azonnali üzenetküldés. A Service Fabric lehetővé teszi, hogy ezek interaktív, állapotalapú alkalmazások készítését egy külön tároló vagy gyorsítótár létrehozása nélkül. Látogasson el [Azure játékmegoldásainak megoldások](https://azure.microsoft.com/solutions/gaming/) tervezési útmutatást [Service Fabric használatával a játékszolgáltatások](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    Ügyfelek, akik készült-e szolgáltatások közé tartozik [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) és [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Ügyfelek, akik készült interaktív munkamenet tartalmaznak [a Hololens Honeywell](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **Data analytics és a munkafolyamat-feldolgozáshoz**: Fel kell dolgoznia megbízhatóan események vagy adatstreamek is profitál az optimalizált olvasási és írási műveletek a Service Fabricben az alkalmazásokat. Továbbá a Service Fabric támogatja a kérelem feldolgozási folyamatok, ahol az eredményeket a megbízható és átadott be a következő feldolgozási feladat adatveszteség nélküli kell. Ezek közé tartozik a tranzakciós és pénzügyi rendszerek, ahol adatok konzisztencia és a számítás garanciák nélkülözhetetlenek.

    Ügyfelek, akik létrehozott üzleti munkafolyamatot szolgáltatások közé tartozik [Zeiss csoport](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) és [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)

* **Az adatok számítási**: A Service Fabric lehetővé teszi a számítási igényű állapotalapú alkalmazások készítését. A Service Fabric lehetővé teszi, hogy a közös helye (számítási) és az alkalmazásokban. Normális esetben az alkalmazás az adatokhoz való hozzáférésre van szüksége, ha egy külső tároló vagy gyorsítótár adatrétegbeli társított hálózati késés korlátozza a számítási idő. Állapotalapú Service Fabric-szolgáltatások a rendszer megsemmisíti a késés, több optimalizált engedélyezése olvasási és írási. Például érdemes lehet olyan alkalmazás, amely közel valós idejű javaslat beállításokat 100-nál kevesebb idő ezredmásodpercben üzenetváltási idő követelmény élvező hajt végre. A Service Fabric-szolgáltatások (ahol a kijelölt javaslat számítási az közös elhelyezésű adatok és szabályok) késés és a teljesítmény jellemzői rugalmas élményt nyújt a felhasználónak, a standard végrehajtási modell a képest a szükséges adatok lehívása távoli tárolóról kellene.

    Ügyfelek, akik készült-e számítási szolgáltatások közé tartozik [Solidsoft válasz](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) és [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)

* **Magas rendelkezésre állású szolgáltatások**: A Service Fabric biztosítja, hogy a gyors feladatátvétel a másodlagos szolgáltatás több replika hozza létre. Ha hardveres vagy más hiba miatt leáll egy csomópont, folyamat vagy egy adott szolgáltatás, a másodlagos replikára lett előléptetve, egy minimális szolgáltatáskimaradást elsődleges replikán.

* **Méretezhető szolgáltatásokkal**: Egyes szolgáltatások particionálható, így-állapot működéséhez a fürtszinten horizontálisan felskálázott. Emellett az egyes szolgáltatások létrehozhatók és menet közben eltávolítva. A szolgáltatások gyorsabban és egyszerűbben lehetnek néhány példány néhány csomópontokon horizontálisan felskálázott el több ezer példány sok csomóponton, és ezután skálázva ismét erőforrás igényeitől függően. A Service Fabric használatával a szolgáltatások kiépítése és felügyelete teljes életciklusa.

## <a name="application-design-case-studies"></a>Alkalmazás tervezési esettanulmányok
Ügyféleset-tanulmányok megjelenítése a Service Fabric alkalmazások használatáról számos közzétett a [sikertörténetei](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) és a [mikroszolgáltatás-alapú megoldások hely](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Mikroszolgáltatásokból álló, állapot nélküli és állapotalapú alkalmazások tervezése
Az Azure-Felhőszolgáltatás munkavégző szerepköreit alkalmazásokat, amelyek állapotmentes szolgáltatások. Ezzel szemben az állapotalapú mikroszolgáltatások karbantartása a kérés- és a nem mérvadó állapotuk. Ez a funkció magas rendelkezésre állás és konzisztencia egyszerű API-k segítségével, amelyek alapját a replikációs tranzakciós garanciákat biztosítanak a állapotának. A Service Fabric állapotalapú szolgáltatások biztosításával mindenki számára a magas rendelkezésre állás érdekében minden típusú alkalmazásokat, nem csak adatbázisok és más adattárakban kerülnének. Ez a természetes metódushívásainak. Alkalmazások már áthelyezte, pusztán a relációs adatbázisok az NoSQL-adatbázisok magas rendelkezésre állását. Maguk az alkalmazások most már az "Forró" állapot és az azokhoz tartozó kezelt további teljesítménynövekedést a megbízhatóság, a konzisztencia, vagy a rendelkezésre állási feláldozása nélkül adatokat is rendelkezik.

Mikroszolgáltatások álló alkalmazások létrehozását, általában rendelkezik állapot nélküli webes alkalmazások (az ASP.NET, Node.js stb.) kombinációjából hívja meg az alakzatot állapot nélküli és állapotalapú üzleti középső rétegű szolgáltatások, az összes üzembe helyezve az ugyanazon a Service Fabric fürt használatával a Service Fabric-telepítési parancsokat. Ezen szolgáltatások mindegyike a független méretezési, a megbízhatóság és az erőforrás-használat, nagy mértékben javítva a rugalmasságot és rugalmasságot biztosít a fejlesztési és életciklus-kezelés tekintetében.

Állapotalapú mikroszolgáltatások egyszerűsítése alkalmazás tervek, mert azok eltávolítása a további várólisták és a rendelkezésre állás és a késés igényeinek tisztán állapot nélküli alkalmazások hagyományosan szükséges gyorsítótárak van szükség. Állapotalapú szolgáltatások természetes módon van a magas rendelkezésre állást és kis késésű, mivel kevesebb mozgó részből áll az alkalmazás egészére kezelése. Az alábbi ábrák bemutatják tervezése az állapotmentes alkalmazások, a másik az állapot-nyilvántartó közötti különbségek. Előnyeit kihasználva a [Reliable Services](service-fabric-reliable-services-introduction.md) és [Reliable Actors](service-fabric-reliable-actors-introduction.md) programozási modelleket, állapotalapú szolgáltatások csökkenthető alkalmazás elérése a nagy átviteli sebességű és kis késése mellett.

## <a name="an-application-built-using-stateless-services"></a>Egy alkalmazás állapotmentes szolgáltatások használatával
![Alkalmazás állapotmentes szolgáltatás használatával][Image1]

## <a name="an-application-built-using-stateful-services"></a>Egy alkalmazás, állapotalapú szolgáltatások használatával
![Alkalmazás állapotmentes szolgáltatás használatával][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések

* További információ [ügyféleset-tanulmányok](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc)
* Tudjon meg többet [minták és forgatókönyvek](service-fabric-patterns-and-scenarios.md)

* Ismerkedés a Service fabric állapot nélküli és állapotalapú szolgáltatások létrehozásával [a reliable services](service-fabric-reliable-services-quick-start.md) és [a reliable actors](service-fabric-reliable-actors-get-started.md) programozási modelljeivel.
* Keresse meg az Azure architecture Centert kapcsolatos útmutató a [mikroszolgáltatások készítése az Azure-ban](https://docs.microsoft.com/azure/architecture/microservices/)
* Lépjen a [gyakorlati tanácsok az Azure Service Fabric-alkalmazás és fürt](service-fabric-best-practices-overview.md) alkalmazás tervezési útmutatóját.

* A következő témakörökben is talál:
  * [Tudnivalók a microservicesről](service-fabric-overview-microservices.md)
  * [Definiálása és kezelése](service-fabric-concepts-state.md)
  * [Service Fabric-szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
  * [A Service Fabric-szolgáltatások méretezése](service-fabric-concepts-scalability.md)
  * [A Service Fabric services particionálása](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
