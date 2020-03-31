---
title: Alkalmazási forgatókönyvek és tervezés
description: A felhőalapú alkalmazások kategóriáinak áttekintése a Service Fabricben. Az állapotalapú és állapotmentes szolgáltatásokat használó alkalmazástervezést ismerteti.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: sfrev
ms.openlocfilehash: 0aeb8ab2923915befdd11f96025687be3b3c4ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76024735"
---
# <a name="service-fabric-application-scenarios"></a>A Service Fabric alkalmazási forgatókönyvei

Az Azure Service Fabric megbízható és rugalmas platformot kínál, ahol számos típusú üzleti alkalmazást és szolgáltatást írhat és futtathat. Ezek az alkalmazások és mikroszolgáltatások lehetnek állapot nélküliek vagy állapotalapúak, és a hatékonyság maximalizálása érdekében erőforrás-kiegyensúlyozottak a virtuális gépek között.

A Service Fabric egyedi architektúrája lehetővé teszi, hogy közel valós idejű adatelemzést, memórián belüli számítást, párhuzamos tranzakciókat és eseményfeldolgozást hajtson végre az alkalmazásokban. A változó erőforrásigényektől függően könnyedén skálázhatja az alkalmazásokat.

Az alkalmazások létrehozásához vezető tervezési útmutatásért olvassa el [a Mikroszolgáltatások architektúráját az Azure Service Fabricen,](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) és ajánlott eljárásokat az [alkalmazástervezéshez a Service Fabric használatával.](service-fabric-best-practices-applications.md)

Fontolja meg a Service Fabric platform használatát a következő típusú alkalmazásokhoz:

* **Adatgyűjtés, feldolgozás és IoT:** Service Fabric kezeli a nagy méretű és alacsony késés az állapotalapú szolgáltatásokon keresztül. Ez segíthet az adatok feldolgozásában több millió eszközön, ahol az eszköz és a számítás adatai közös elhelyezkedésűek.

    Azok az ügyfelek, akik IoT-szolgáltatásokat építettek a Service Fabric használatával, a [Honeywell,](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure) [a PCL Construction,](https://customers.microsoft.com/story/pcl-construction-professional-services-azure) [a Crestron, a](https://customers.microsoft.com/story/crestron-partner-professional-services-azure) [BMW,](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/)a [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)és a [Mesh Systems.](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)

* **Játék- és munkamenet-alapú interaktív alkalmazások:** A Service Fabric akkor hasznos, ha az alkalmazás alacsony késleltetésű olvasási és írási műveleteket igényel, például online játékokban vagy azonnali üzenetküldésben. A Service Fabric lehetővé teszi, hogy ezeket az interaktív, állapotalapú alkalmazásokat anélkül, hogy hozzon létre egy külön tárolóvagy gyorsítótár. Látogasson el [az Azure játékmegoldásaiba,](https://azure.microsoft.com/solutions/gaming/) ahol tervezési útmutatást talál a [Service Fabric játékszolgáltatásokban való használatával kapcsolatos útmutatásért.](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    Azok az ügyfelek, akik játékszolgáltatásokat építettek, közétartozik [a Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) és a [Digamore.](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/) Az interaktív munkameneteket fejlesztő ügyfelek közé tartozik [a Honeywell és a Hololens.](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **Adatelemzés és munkafolyamat-feldolgozás:** Olyan alkalmazások, amelyeknek megbízhatóan fel kell dolgozniuk az eseményeket vagy adatfolyamokat, a Service Fabric optimalizált olvasási és írási műveleteinek előnyeit. A Service Fabric is támogatja az alkalmazásfeldolgozási folyamatok, ahol az eredmények megbízhatónak kell lennie, és továbbítja a következő feldolgozási szakaszban veszteség nélkül. Ezek a folyamatok tranzakciós és pénzügyi rendszereket tartalmaznak, ahol az adatok konzisztenciája és a számítási garanciák alapvető fontosságúak.

    Az üzleti munkafolyamat-szolgáltatásokat kialakító ügyfelek közé tartozik a [Zeiss Group](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [a Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)és a [Société General](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Számítási adatok:** Service Fabric lehetővé teszi, hogy állapotalapú alkalmazások, amelyek intenzív adatszámítás. A Service Fabric lehetővé teszi a feldolgozás (számítás) és az alkalmazások ban lévő adatok közös elhelyezését. 

   Általában, ha az alkalmazás hozzáférést igényel az adatokhoz, a külső adat-gyorsítótárhoz vagy tárolási réteghez társított hálózati késés korlátozza a számítási időt. Állapotalapú Service Fabric-szolgáltatások kiküszöbölik ezt a késést, lehetővé téve az optimalizált olvasási és írási műveletek.

   Vegyünk például egy olyan alkalmazást, amely közel valós idejű javaslatválasztást hajt végre az ügyfelek számára, 100 ezredmásodpercnél rövidebb oda-vissza időszükséglettel. A Késés és a teljesítmény jellemzői a Service Fabric-szolgáltatások biztosít a felhasználó számára, összehasonlítva a szabványos megvalósítási modell, amelynek a szükséges adatokat a távtárolóból kell letölteni. A rendszer jobban reagál, mivel az ajánlás kiválasztásának kiszámítása az adatokkal és a szabályokkal együtt található.

    Azok az ügyfelek, akik számítási szolgáltatásokat építettek, közétartozik [a Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) és az [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Magas rendelkezésre állású szolgáltatások:** A Service Fabric gyors feladatátvételt biztosít több másodlagos szolgáltatás replikák létrehozásával. Ha egy csomópont, folyamat vagy egyéni szolgáltatás hardver vagy más hiba miatt megszakad, a másodlagos replikák egyike egy elsődleges replikává lép, minimális szolgáltatásvesztéssel.

* **Méretezhető szolgáltatások:** Az egyes szolgáltatások particionálhatók, így az állapot a fürtön keresztül horizontálisan skálázható. Az egyes szolgáltatások menet közben is létrehozhatók és eltávolíthatók. A szolgáltatások horizontális felskálázhatók néhány csomóponton néhány példányról több ezer példányra számos csomóponton, majd szükség szerint újra méretezheti őket. A Service Fabric segítségével ezeket a szolgáltatásokat, és kezelheti a teljes életciklusok.

## <a name="application-design-case-studies"></a>Alkalmazástervezési esettanulmányok

Esettanulmányok, amelyek bemutatják, hogyan használják a Service Fabric-et az alkalmazások tervezéséhez, közzé vannak téve az [Ügyféltörténeteken](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) és [a mikroszolgáltatásokon](https://azure.microsoft.com/solutions/microservice-applications/) az Azure-webhelyeken.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Állapotnélküli és állapotalapú mikroszolgáltatásokból álló alkalmazások tervezése

Alkalmazások létrehozása az Azure Cloud Services feldolgozói szerepkörök egy példa egy állapotmentes szolgáltatás. Ezzel szemben az állapotalapú mikroszolgáltatások a kérésen és a válaszon túl is fenntartják mérvadó állapotukat. Ez a funkció az állapot magas rendelkezésre állását és konzisztenciáját biztosítja egyszerű API-kon keresztül, amelyek replikációval támogatott tranzakciós garanciákat nyújtanak.

A Service Fabric állapotalapú szolgáltatásai magas rendelkezésre állást biztosítanak minden típusú alkalmazásszámára, nem csak az adatbázisokhoz és más adattárakhoz. Ez egy természetes progresszió. Az alkalmazások már áthelyezték a tisztán relációs adatbázisok at a magas rendelkezésre állás érdekében a NoSQL adatbázisokba. Most maguk az alkalmazások is rendelkezhetnek a "forró" állapotés az adatok kezelése bennük további teljesítménynövekedés feláldozása nélkül megbízhatóság, a következetesség vagy a rendelkezésre állás.

Mikroszolgáltatásokból álló alkalmazások létrehozásakor általában állapotnélküli webalkalmazások (például ASP.NET és Node.js) kombinációjával rendelkezik, amelyek állapotnélküli és állapotalapú üzleti középső rétegbeli szolgáltatásokra szólítanak fel. Az alkalmazások és szolgáltatások mind ugyanabban a Service Fabric-fürtben vannak telepítve a Service Fabric központi telepítési parancsain keresztül. Ezek a szolgáltatások a méretezés, a megbízhatóság és az erőforrás-használat tekintetében függetlenek. Ez a függetlenség javítja az agilitást és a rugalmasságot a fejlesztés és az életciklus-menedzsment terén.

Állapotalapú mikroszolgáltatások egyszerűsíti az alkalmazás tervek, mert megszünteti a további várólisták és gyorsítótárak, amelyek hagyományosan szükséges a tisztán állapotmentes alkalmazások rendelkezésre állási és késési követelményeinek. Mivel az állapotalapú szolgáltatások magas rendelkezésre állással és alacsony késésű, kevesebb részletet kell kezelni az alkalmazásban.

Az alábbi ábrák bemutatják a különbség et egy állapotnélküli és egy állapottalan alkalmazás tervezése között. A [Reliable Services](service-fabric-reliable-services-introduction.md) és a [Reliable Actors](service-fabric-reliable-actors-introduction.md) programozási modellek kihasználásával az állapotalapú szolgáltatások csökkentik az alkalmazások összetettségét, miközben nagy átviteli feszültséget és alacsony késleltetést biztosítanak.

Íme egy példa alkalmazás, amely állapotmentes szolgáltatásokat használ: ![Állapotmentes szolgáltatásokat használó alkalmazás][Image1]

Íme egy példa alkalmazás, amely állapotalapú szolgáltatásokat használ: ![Állapotalapú szolgáltatásokat használó alkalmazás][Image2]

## <a name="next-steps"></a>További lépések

* Kezdje el építeni az állapotnélküli és állapotalapú szolgáltatásokat a Service Fabric [megbízható szolgáltatások](service-fabric-reliable-services-quick-start.md) és a [Reliable Actors](service-fabric-reliable-actors-get-started.md) programozási modellekkel.
* Az Azure Architektárház ban útmutatást talál az [Azure-beli mikroszolgáltatások létrehozásához.](https://docs.microsoft.com/azure/architecture/microservices/)
* Nyissa meg az [Azure Service Fabric-alkalmazást és a fürtajánlott eljárásokat](service-fabric-best-practices-overview.md) az alkalmazástervezési útmutatáshoz.

* Lásd még:
  * [A mikroszolgáltatások ismertetése](service-fabric-overview-microservices.md)
  * [Szolgáltatásállapot ának meghatározása és kezelése](service-fabric-concepts-state.md)
  * [A szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
  * [Szolgáltatások méretezése](service-fabric-concepts-scalability.md)
  * [Particionálási szolgáltatások](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
