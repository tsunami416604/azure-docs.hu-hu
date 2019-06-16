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
ms.openlocfilehash: 6563d6e7c454f44e1a70d725191e56d3f90315c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052599"
---
# <a name="service-fabric-application-scenarios"></a>A Service Fabric-alkalmazás-forgatókönyvek
Az Azure Service Fabric egy megbízható és rugalmas platform, amelyen írása és futtatása a számos különböző típusú üzleti alkalmazásokat és szolgáltatásokat kínál. Ezek az alkalmazások és a mikroszolgáltatások lehet állapot nélküli vagy állapotalapú, és a maximális hatékonyság a virtuális gép között zajlik a erőforrás kiegyensúlyozott. 

A Service fabric egyedi architektúra lehetővé teszi, hogy közel valós idejű adatelemzés, a memóriabeli számítási, a párhuzamos tranzakciókat és a eseményfeldolgozás az alkalmazásokban. Egyszerűen méretezhető az alkalmazások felfelé és lefelé (nagyon bejövő vagy kimenő), a változó erőforrás követelményeitől függően.

Tervezési útmutató az alkalmazások létrehozására, olvassa el a [Mikroszolgáltatási architektúra az Azure Service Fabricban](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) és [ajánlott eljárások használatával a Service Fabric alkalmazás-tervezés](service-fabric-best-practices-applications.md).

Vegye figyelembe a következő típusú alkalmazások számára a Service Fabric platformot használnak:

* **Adatok gyűjtése, feldolgozása és IoT**: A Service Fabric nagy méretű kezeli, és közel valós idejű, az állapotalapú szolgáltatások révén rendelkezik. Megkönnyíti a több millió eszköz, ahol az adatokat az eszköz és a számítási közös elhelyezésű adatok feldolgozásához.

    Ügyfelek, akik a Service Fabric készült IoT-szolgáltatások közé tartozik [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [Megoldástervezője](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), és [rendszerek háló](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Játékok és a munkamenet-alapú interaktív alkalmazások**: A Service Fabric akkor hasznos, ha az alkalmazás által kért alacsony késésű olvasási és írási, mint például az online játékok vagy azonnali üzenetküldéshez. A Service Fabric lehetővé teszi, hogy ezek interaktív, állapotalapú alkalmazások készítését egy külön tároló vagy gyorsítótár létrehozása nélkül. Látogasson el [Azure játékmegoldásainak megoldások](https://azure.microsoft.com/solutions/gaming/) tervezési útmutatást [Service Fabric használatával a játékszolgáltatások](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

    Ügyfelek, akik készült-e szolgáltatások közé tartozik [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) és [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Ügyfelek, akik készült interaktív munkamenet tartalmaznak [Honeywell a Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Data analytics és a munkafolyamat-feldolgozáshoz**: Alkalmazások, amelyek megbízhatóan feldolgozni eseményeket vagy adatokat profitál az optimalizált olvasási és írási műveletek a Service Fabricben az adatfolyamot. A Service Fabric application feldolgozási folyamatok, ahol az eredményeket a megbízható és átadott be a következő feldolgozási feladat adatveszteség nélküli kell is támogatja. Ezek a folyamatok például a következők tranzakciós és pénzügyi rendszerek, ahol adatok konzisztencia és a számítás garanciák nélkülözhetetlenek.

    Ügyfelek, akik létrehozott üzleti munkafolyamatot szolgáltatások közé tartozik [Zeiss csoport](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [kvórum üzleti megoldások](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric), és [Société általános](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Az adatok számítási**: A Service Fabric lehetővé teszi, amelyeket a adatok nagy számításigényű számítási állapotalapú alkalmazások. A Service Fabric lehetővé teszi, hogy a közös elhelyezést, (számítási) és az alkalmazásokban. 

   Normális esetben az alkalmazás az adatokhoz való hozzáférésre van szüksége, ha egy külső tároló vagy gyorsítótár adatrétegbeli társított hálózati késés korlátozza a számítási idő. Állapotalapú Service Fabric-szolgáltatások számára, hogy a késés, több optimalizált olvas és ír. 
   
   Például érdemes lehet olyan alkalmazás, amely közel valós idejű javaslat beállításokat 100-nál kevesebb idő ezredmásodpercben üzenetváltási idő követelmény élvező hajt végre. A Service Fabric-szolgáltatások késés és a teljesítmény jellemzői válaszol-e felhasználói élményt nyújtson a felhasználó, a standard végrehajtási modell, hogy a szükséges adatok lehívása távoli tárolóról képest. A rendszer nem rugalmasabb ügyfélkapcsolatok kialakítását, mert a számítási javaslat kijelölés közös elhelyezésű adatok és szabályok.

    Ügyfelek, akik készült-e számítási szolgáltatások közé tartozik [Solidsoft válasz](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) és [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Magas rendelkezésre állású szolgáltatások**: A Service Fabric biztosítja, hogy a gyors feladatátvétel a másodlagos szolgáltatás több replika hozza létre. Ha hardveres vagy más hiba miatt leáll egy csomópont, folyamat vagy egy adott szolgáltatás, a másodlagos replikára lett előléptetve, egy minimális szolgáltatáskimaradást elsődleges replikán.

* **Méretezhető szolgáltatásokkal**: Egyes szolgáltatások particionálható, így-állapot működéséhez a fürtszinten horizontálisan felskálázott. Egyes szolgáltatások is létrehozható és menet közben eltávolítva. Horizontális felskálázás szolgáltatás közül néhány példány néhány csomóponton el több ezer példány sok csomóponton, és majd skálázhatja őket igény szerint újra. A Service Fabric használatával a szolgáltatások kiépítése és felügyelete teljes életciklusa.

## <a name="application-design-case-studies"></a>Alkalmazás tervezési esettanulmányok
Ügyféleset-tanulmányok, amelyek megmutatják, hogy a Service Fabric alkalmazások tervezéséhez használható a közzétett a [sikertörténetei](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) és [az Azure Mikroszolgáltatások](https://azure.microsoft.com/solutions/microservice-applications/) helyek.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Állapot nélküli és állapotalapú mikroszolgáltatások álló alkalmazások tervezése
Az Azure Cloud Services feldolgozói szerepkörei alkalmazásokat, amelyek állapotmentes szolgáltatások. Ezzel szemben az állapotalapú mikroszolgáltatások karbantartása a kérés- és a nem mérvadó állapotuk. Ez a funkció magas rendelkezésre állás és konzisztencia egyszerű API-k segítségével, amelyek alapját a replikációs tranzakciós garanciákat biztosítanak a állapotának. 

A Service Fabric állapotalapú szolgáltatások használata magas rendelkezésre állás minden típusú alkalmazásokat, nem csak adatbázisok és más adattárakban. Ez a természetes metódushívásainak. Alkalmazások már áthelyezte, pusztán a relációs adatbázisok az NoSQL-adatbázisok magas rendelkezésre állását. Maguk az alkalmazások most már az "Forró" állapot és az azokhoz tartozó kezelt további teljesítménynövekedést a megbízhatóság, a konzisztencia, vagy a rendelkezésre állási feláldozása nélkül adatokat is rendelkezik.

Ha a mikroszolgáltatások álló alkalmazásokat fejleszt, általában akkor állapot nélküli webes alkalmazások (például az ASP.NET- és Node.js) kombinációja be állapot nélküli és állapotalapú üzleti középső rétegbeli szolgáltatásokat hívó. Az alkalmazások és szolgáltatások üzembe helyezett az ugyanazon a Service Fabric-fürthöz a Service Fabric üzembe helyezési parancsok segítségével. Ezen szolgáltatások mindegyike a független méretezési, a megbízhatóság és az erőforrás-használat tekintetében. Ez a függetlenség növeli a rugalmasságot és rugalmasságot biztosít a fejlesztési és életciklus-kezelés.

Állapotalapú mikroszolgáltatások egyszerűsítése alkalmazás tervek, mert azok eltávolítása a további várólisták és a rendelkezésre állás és a késés igényeinek tisztán állapot nélküli alkalmazások hagyományosan szükséges gyorsítótárak van szükség. Állapotalapú szolgáltatások magas rendelkezésre állás és a közel valós idejű rendelkezik, mert nincsenek kevesebb részleteit az alkalmazás kezeléséhez. 

Az alábbi ábrák bemutatják az állapotmentes alkalmazások, a másik az állapot-nyilvántartó tervezése közötti különbségeket. Előnyeit kihasználva a [Reliable Services](service-fabric-reliable-services-introduction.md) és [Reliable Actors](service-fabric-reliable-actors-introduction.md) programozási modelleket, állapotalapú szolgáltatások csökkenthető alkalmazás elérése a nagy átviteli sebességű és kis késése mellett.

A következő állapotmentes szolgáltatások használó mintaalkalmazás: ![Állapotmentes szolgáltatások használó alkalmazás][Image1]

Itt látható az állapotalapú szolgáltatások használó mintaalkalmazás: ![Állapotmentes szolgáltatások használó alkalmazás][Image2]

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [minták és forgatókönyvek](service-fabric-patterns-and-scenarios.md).

* Ismerkedés a Service fabric állapot nélküli és állapotalapú szolgáltatások létrehozásával [Reliable Services](service-fabric-reliable-services-quick-start.md) és [Reliable Actors](service-fabric-reliable-actors-get-started.md) programozási modelljeivel.
* Keresse meg az Azure Architecture Centert kapcsolatos útmutató a [mikroszolgáltatások készítése az Azure-ban](https://docs.microsoft.com/azure/architecture/microservices/).
* Lépjen a [gyakorlati tanácsok az Azure Service Fabric-alkalmazás és fürt](service-fabric-best-practices-overview.md) alkalmazás tervezési útmutatóját.

* A következő témakörökben is talál:
  * [Tudnivalók a microservicesről](service-fabric-overview-microservices.md)
  * [Definiálása és kezelése](service-fabric-concepts-state.md)
  * [Service Fabric-szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
  * [A Service Fabric-szolgáltatások méretezése](service-fabric-concepts-scalability.md)
  * [A Service Fabric services particionálása](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
