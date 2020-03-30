---
title: Az Azure Service Fabric architektúrája
description: Ez a cikk ismerteti a Service Fabric, egy elosztott rendszerplatform, amely méretezhető, megbízható és könnyen kezelhető alkalmazások at a felhőlétrehozásához használt architektúráját ismerteti.
services: service-fabric
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 972700dded1841994de9252b4aa4bbc8eaefeaf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76024713"
---
# <a name="service-fabric-architecture"></a>A Service Fabric-architektúra

A Service Fabric réteges alrendszerekből épül fel. Ezek az alrendszerek lehetővé teszik olyan alkalmazások írását, amelyek:

* Magas rendelkezésre állású
* Méretezhető
* Kezelhető
* Tesztelhető

Az alábbi ábrán a Service Fabric fő alrendszerei láthatók.

![A Service Fabric architektúrájának diagramja](media/service-fabric-architecture/service-fabric-architecture.png)

Egy elosztott rendszerben a fürt csomópontjai közötti biztonságos kommunikáció elengedhetetlen. A verem alapja a szállítási alrendszer, amely biztonságos kommunikációt biztosít a csomópontok között. Az átviteli alrendszer felett található az összevonási alrendszer, amely a különböző csomópontokat egyetlen entitásba (elnevezett fürtökbe) csoportosítja, hogy a Service Fabric észlelhesse a hibákat, végrehajthassa a vezető választást, és egységes útválasztást biztosítson. A megbízhatósági alrendszer, az összevonási alrendszer réteges, felelős a Service Fabric-szolgáltatások megbízhatóságáért olyan mechanizmusokon keresztül, mint a replikáció, az erőforrás-kezelés és a feladatátvétel. Az összevonási alrendszer a hosting és aktiválási alrendszer alapját is képezi, amely egyetlen csomóponton kezeli az alkalmazások életciklusát. A felügyeleti alrendszer kezeli az alkalmazások és szolgáltatások életciklusát. A testability alrendszer segítségével az alkalmazásfejlesztők tesztelhetik szolgáltatásaikat szimulált hibákkal az alkalmazások és szolgáltatások éles környezetben történő üzembe helyezése előtt és után. A Service Fabric lehetővé teszi a szolgáltatáshelyek feloldását a kommunikációs alrendszeren keresztül. A fejlesztők számára elérhető alkalmazásprogramozási modellek rétegesek ezeken az alrendszereken és az alkalmazásmodellen az eszközkészítés engedélyezéséhez.

## <a name="transport-subsystem"></a>Szállítási alrendszer

Az átviteli alrendszer pont-pont datagram kommunikációs csatornát valósít meg. Ez a csatorna a szolgáltatásháló-fürtökön belüli kommunikációra, valamint a szolgáltatásháló-fürt és az ügyfelek közötti kommunikációra szolgál. Támogatja az egyirányú és a kérelem-válasz kommunikációs mintákat, amelyek alapul biztosítják a műsorszórás és a csoportos küldés megvalósításához az összevonási rétegben. Az átviteli alrendszer x509-es tanúsítványokkal vagy Windows-biztonsággal biztosítja a kommunikációt. Ezt az alrendszert a Service Fabric belsőleg használja, és nem érhető el közvetlenül a fejlesztők számára az alkalmazásprogramozáshoz.

## <a name="federation-subsystem"></a>Összevonási alrendszer

Annak érdekében, hogy egy elosztott rendszerben lévő csomópontok egy készletét oktathassa, egységes en kell szemlélnie a rendszert. Az összevonási alrendszer a szállítási alrendszer által biztosított kommunikációs primitíveket használja, és a különböző csomópontokat egyetlen egyesített fürtbe varrja össze, amelyet meg tud indokolni. Biztosítja a többi alrendszer által szükséges elosztott rendszerprimitíveket – hibaészlelést, vezető választást és konzisztens útválasztást. Az összevonási alrendszer 128 bites jogkivonat-területtel rendelkező elosztott kivonattáblákra épül. Az alrendszer létrehoz egy gyűrűtopológiát a csomópontok felett, és a gyűrű minden csomópontja a jogkivonat-terület egy részhalmazát rendeli a tulajdonjoghoz. A hiba észleléséhez a réteg szívdobogáson és választottbírósági eljáráson alapuló lízingmechanizmust használ. Az összevonási alrendszer bonyolult illesztési és indulási protokollokkal is garantálja, hogy a jogkivonatnak csak egyetlen tulajdonosa létezik bármikor. Ez vezető választási és következetes útválasztási garanciákat biztosít.

## <a name="reliability-subsystem"></a>Megbízhatósági alrendszer

A megbízhatósági alrendszer biztosítja azt a mechanizmust , amely a *Replikátor*, *a Feladatátvétel-kezelő*és az *Erőforrás-elosztó*használatával magas rendelkezésre állásúvá teszi a Service Fabric szolgáltatás állapotát .

* A Replikátor biztosítja, hogy az elsődleges szolgáltatás replika állapotváltozásai automatikusan replikálódnak a másodlagos replikákra, fenntartva a szolgáltatás replikakészlet elsődleges és másodlagos replikái közötti konzisztenciát. A replikátor felelős a kvórum kezeléséért a replikakészlet replikái között. Együttműködik a feladatátvevő egységgel, hogy megkapja a replikálandó műveletek listáját, és az újrakonfigurációs ügynök biztosítja a replikakészlet konfigurációját. Ez a konfiguráció azt jelzi, hogy a műveleteket mely replikákat kell replikálni. A Service Fabric egy fabric replikátor nevű alapértelmezett replikátort biztosít, amelyet a programozási modell API-val a szolgáltatásállapot magas rendelkezésre állásúvá és megbízhatóvá tehet.
* A feladatátvételi kezelő biztosítja, hogy amikor csomópontokat adnak hozzá, vagy eltávolítják a fürtből, a terhelés automatikusan újra eloszlik a rendelkezésre álló csomópontok között. Ha a fürt egy csomópontja meghibásodik, a fürt automatikusan újrakonfigurálja a szolgáltatásreplikákat a rendelkezésre állás fenntartása érdekében.
* Az Erőforrás-kezelő a szolgáltatásreplikákat a fürt hibatartományai között helyezi el, és biztosítja, hogy az összes feladatátvevő egység működőképes legyen. Az erőforrás-kezelő is kiegyensúlyozza a szolgáltatás erőforrásait az alapul szolgáló megosztott fürtcsomópontok között az optimális egységes terheléselosztás elérése érdekében.

## <a name="management-subsystem"></a>Felügyeleti alrendszer

A felügyeleti alrendszer teljes körű szolgáltatást és alkalmazáséletciklus-kezelést biztosít. A PowerShell-parancsmagok és a felügyeleti API-k lehetővé teszik az alkalmazások üzembe helyezését, üzembe helyezését, javítását, frissítését és a kiépítést a rendelkezésre állás elvesztése nélkül. A felügyeleti alrendszer ezt a következő szolgáltatásokon keresztül hajtja végre.

* **Fürtkezelő**: Ez az elsődleges szolgáltatás, amely a megbízhatóságtól kezdve kommunikál a feladatátvételi kezelővel, és az alkalmazásokat a szolgáltatáselhelyezési megkötések alapján csomópontokra helyezi. A feladatátvételi alrendszerben lévő erőforrás-kezelő biztosítja, hogy a korlátozások soha ne törjenek. A fürtkezelő kezeli az alkalmazások életciklusát a kiépítéstől a de-provision-ig. Integrálja az állapotkezelővel annak érdekében, hogy az alkalmazások rendelkezésre állása ne vesszen el szemantikai állapotszempontjából a frissítések során.
* **Állapotkezelő**: Ez a szolgáltatás lehetővé teszi az alkalmazások, szolgáltatások és fürtentitások állapotfigyelését. A fürtentitások (például csomópontok, szolgáltatáspartíciók és replikák) jelenthetik az állapotadatokat, amelyeket ezután a központi állapottárolóba összesít. Ez az állapotinformáció a szolgáltatások és csomópontok általános időponthoz-időpontra vonatkozó állapotpillanatképét biztosítja a fürt több csomópontja között, lehetővé téve a szükséges korrekciós műveletek elvégzését. Az állapotfigyelő API-k lehetővé teszik az állapotfigyelő alrendszernek jelentett állapotesemények lekérdezését. Az állapotlekérdezési API-k az állapottárolóban vagy az adott fürtentitás összesített, értelmezett állapotadatait adják vissza.
* **Image Store**: Ez a szolgáltatás biztosítja az alkalmazás bináris fájljait. Ez a szolgáltatás egy egyszerű elosztott fájltárolót biztosít, ahová az alkalmazásokat feltöltik és letöltik.

## <a name="hosting-subsystem"></a>Üzemeltetési alrendszer

A fürtkezelő tájékoztatja a gazdakiszolgáló alrendszert (amely az egyes csomópontokon fut), hogy mely szolgáltatásokat kell kezelnie egy adott csomóponthoz. A gazdaalrendszer ezután kezeli az alkalmazás életciklusát az adott csomóponton. Együttműködik a megbízhatósággal és az állapot-összetevőkkel annak érdekében, hogy a replikák megfelelően legyenek elhelyezve és kifogástalanállapotban legyenek.

## <a name="communication-subsystem"></a>Kommunikációs alrendszer

Ez az alrendszer megbízható üzenetküldést biztosít a fürtön és a szolgáltatásfelderítésen belül az elnevezési szolgáltatáson keresztül. Az elnevezési szolgáltatás feloldja a szolgáltatásneveket a fürt egy helyére, és lehetővé teszi a felhasználók számára a szolgáltatásnevek és -tulajdonságok kezelését. Az elnevezési szolgáltatás használatával az ügyfelek biztonságosan kommunikálhatnak a fürt bármely csomócsomójával a szolgáltatásnév feloldásához és a szolgáltatás metaadatainak beolvasásához. Egy egyszerű névhívó ügyfél API használatával a Service Fabric felhasználói olyan szolgáltatásokat és ügyfeleket fejleszthetnek, amelyek képesek az aktuális hálózati hely feloldására a csomópont dinamizmusa vagy a fürt újraméretezése ellenére.

## <a name="testability-subsystem"></a>A testability alrendszer

A Testability egy olyan eszközcsomag, amelyet kifejezetten a Service Fabric-re épülő szolgáltatások tesztelésére terveztek. Az eszközök segítségével a fejlesztő könnyen indukálják értelmes hibák és fuss teszt forgatókönyvek gyakorolni és érvényesíteni a számos állam és átmenetek, hogy a szolgáltatás fogja tapasztalni az egész élettartama alatt, mindezt ellenőrzött és biztonságos módon. Testability is biztosít egy mechanizmust, hogy hosszabb tesztek futtatásához, amelyek iterálni a különböző lehetséges hibák elvesztése nélkül rendelkezésre állás. Ez biztosítja a teszt-in-production környezetben.
