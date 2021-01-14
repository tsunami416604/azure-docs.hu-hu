---
title: Azure Defender a IoT architektúrához
description: Ismerje meg az Azure Defender IoT architektúra-és információáramlási folyamatát.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/13/2021
ms.author: shhazam
ms.openlocfilehash: f3239434a7ba737bc9323bc4f383afd794800db1
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201475"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender a IoT architektúrához

Ez a cikk a Defender IoT-megoldás működési rendszerarchitektúráját ismerteti. A IoT készült Azure Defender két lehetőséget kínál a környezet igényeinek, az ügynök nélküli megoldásnak a szervezeteknek és az eszközök építői számára.

## <a name="agentless-solution-for-organizations"></a>Ügynök nélküli megoldás szervezeteknek
### <a name="defender-for-iot-components"></a>Defender IoT-összetevőkhöz

A IoT Defender az Azure-felhőhöz és a helyszíni összetevőkhöz egyaránt csatlakozik. A megoldás nagy és földrajzilag elosztott környezetekben, több távoli hellyel való méretezhetőségre lett tervezve. Ez a megoldás lehetővé teszi a többrétegű elosztott architektúra használatát ország, régió, üzleti egység vagy zóna szerint. 

A IoT készült Azure Defender a következő összetevőket tartalmazza: 

**Felhőben csatlakoztatott üzemelő példányok**

- Azure Defender IoT-érzékelő virtuális géphez vagy készülékhez
- Azure Portal a felhőalapú felügyelethez és az Azure Sentinel-integrációhoz
- Helyszíni felügyeleti konzol helyi felügyelethez
- Beágyazott biztonsági ügynök (nem kötelező)

**Air-gapped (offline) üzemelő példányok**

- Azure Defender IoT-érzékelő virtuális géphez vagy készülékhez
- Helyszíni felügyeleti konzol a helyi hely kezeléséhez

:::image type="content" source="./media/architecture/defender-iot-security-architecture-v3.png" alt-text="A Defender IoT-hez készült architektúrája.":::

### <a name="azure-defender-for-iot-sensors"></a>Azure Defender IoT-érzékelőkhöz

A Defender for IoT érzékelők felfedik és folyamatosan figyelik a hálózati eszközöket. Az érzékelők összegyűjtik az INTERNETKAPCSOLATtal rendelkező hálózati forgalmat passzív (ügynök nélküli) figyeléssel a IoT és az OT-eszközökön. 
 
A IoT és az OT-hálózatok számára készült, az ügynök nélküli technológia részletesen betekintést nyújt a IoT és az OT-ra, hogy percek alatt a hálózathoz kapcsolódjon. A hálózatra és a hálózati eszközökre a nem invazív, a Network Traffic Analysis (NTA) megközelítés miatt nulla teljesítménnyel kapcsolatos hatással van. 
 
A szabadalmaztatott, a IoT és az OT-Aware viselkedés-elemzés és a 7. rétegbeli mély csomag vizsgálata (DPI) lehetővé teszi a hagyományos aláírás-alapú megoldások elemzését, hogy a rendellenes vagy jogosulatlan tevékenységek alapján azonnal észlelje a speciális IoT és a fenyegetéseket (például a fájl nélküli kártevőket). 
  
A IoT érzékelőkhöz tartozó Defender egy SPAN porthoz vagy egy hálózati KOPPINTÁShoz csatlakozik, és azonnal megkezdi a DPI-t a IoT és az OT hálózati forgalomban. 
 
Az adatgyűjtés,-feldolgozás,-elemzés és-riasztás közvetlenül az érzékelőn történik. Ez ideális megoldás az alacsony sávszélességű vagy nagy késésű kapcsolattal rendelkező helyekhez, mivel csak a metaadatok a felügyeleti konzolra kerülnek át.

Az érzékelő öt analitikai felismerő motort tartalmaz. A motorok a valós idejű és előre rögzített forgalom elemzése alapján indítják el a riasztásokat. A következő motorok érhetők el: 

#### <a name="protocol-violation-detection-engine"></a>Protokoll megsértésének észlelési motorja
A protokoll-megsértés észlelési motorja azonosítja az ICS protokoll specifikációit sértő csomagméret és mezőértékek használatát, például: Modbus kivételt, és egy elavult hibakód-riasztás kezdeményezését.

#### <a name="policy-violation-detection-engine"></a>Házirend-megsértés észlelési motorja
A gépi tanulás segítségével a szabályzat megsértése észlelési motor riasztást küld a felhasználóktól az alapkonfiguráció viselkedése alól, például az adott kódok jogosulatlan használatát, az adott objektumokhoz való hozzáférést vagy az eszköz konfigurációjának módosításait. Például: a DeltaV szoftver verziója megváltozott, a nem engedélyezett PLC programozási riasztások. Pontosabban, a szabályzat megsértésének motorja az állapotok és átmenetek determinisztikus-sorozatából modellezi az ICS-hálózatokat – egy ipari, véges állapotú modellezés (IFSM) nevű szabadalmaztatott eljárás használatával. A szabályzat megsértésének észlelési motorja az INTERNETKAPCSOLATtal rendelkező hálózatok alapkonfigurációját hozza létre, így a platform rövidebb tanulási időszakot igényel, hogy kiépítse a hálózatot, mint az általános matematikai megközelítéseket vagy az elemzéseket, amelyeket eredetileg az IT-hálózatok helyett fejlesztettek ki.

#### <a name="industrial-malware-detection-engine"></a>Ipari kártevő-észlelési motor
Az ipari kártevő-észlelési motor az ismert kártevők, például a Conficker, a Black Energy, a Havex, a WannaCry, a NotPetya és a Triton jelenlétét jelző viselkedéseket azonosítja. 

#### <a name="anomaly-detection-engine"></a>Rendellenesség-észlelési motor
Az anomáliák észlelési motorja észleli a gépek közötti szokatlan kommunikációt és viselkedést. Az ICS-hálózatok az állapotok és átmenetek determinisztikus való modellezésével a platform rövidebb tanulási időszakot igényel, mint az általános matematikai megközelítések vagy az eredetileg az OT-nál nem használt elemzések. Emellett a hibák gyors észlelését is észleli, minimális téves pozitív értékekkel. A rendellenesség-észlelési motor riasztásai közé tartozik a túlzott SMB-bejelentkezési kísérletek száma, és a PLC-vizsgálat riasztásokat észlelt.

#### <a name="operational-incident-detection"></a>Működési incidensek észlelése
A működési incidensek észlelése olyan működési problémákat észlel, mint például az átmeneti kapcsolat, amely jelzi a berendezések meghibásodásának korai jeleit. Előfordulhat például, hogy az eszköz le van választva (nem válaszol), és a Siemens S7 stop PLC paranccsal riasztásokat küld.

### <a name="management-consoles"></a>Felügyeleti konzolok
Az Azure Defender a hibrid környezetekben való IoT két felügyeleti portálon keresztül valósítható meg: 
- Érzékelő konzol
- A helyszíni felügyeleti konzol
- Azure Portal

### <a name="sensor-console"></a>Érzékelő konzol
Az érzékelők észlelései az érzékelő konzolján jelennek meg, ahol megtekinthetők, megtekinthetők és elemezhetők egy hálózati Térkép, az eszközök leltára, valamint a jelentések széles köre, például a kockázatértékelési jelentések, az adatbányászati lekérdezések és a támadási vektorok. A-konzol segítségével megtekintheti és kezelheti az érzékelő-motorok által észlelt fenyegetéseket, továbbíthatja az információkat a partneri rendszereknek, kezelheti a felhasználókat és egyéb műveleteket is.

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="Defender a IoT-érzékelő konzolján":::

### <a name="on-premises-management-console"></a>Helyszíni felügyeleti konzol
A helyszíni felügyeleti konzol lehetővé teszi, hogy a Security Operations Center (SOC) operátorai több érzékelőből származó riasztásokat kezelnek és elemezzenek egyetlen irányítópulton, és átfogó képet adjanak az OT-hálózatok állapotáról.

Ez az architektúra átfogó, egységes nézetet biztosít a hálózatról a SOC szinten, az optimalizált riasztások kezelését, valamint az operatív hálózatok biztonságának szabályozását, így biztosítva, hogy a döntéshozatal és a kockázatkezelés hibátlan maradjon.

A több-bérlős, a monitorozási, az adatelemzési és a központosított érzékelő-távvezérlés mellett a felügyeleti konzol további rendszerkarbantartási eszközöket (például riasztások kizárása) és a távoli berendezések teljes körű testreszabott jelentéskészítési funkcióit biztosítja. Ez a skálázható architektúra a helyi felügyeletet is támogatja a helyek szintjén, a zóna szintjén, valamint a globális felügyeletet a SOC-n belül.

A felügyeleti konzol a magas rendelkezésre állású konfigurációhoz helyezhető üzembe, amely egy biztonsági mentési konzolt biztosít, amely rendszeresen fogadja a helyreállításhoz szükséges összes konfigurációs fájl biztonsági másolatait. Ha az elsődleges konzol meghibásodik, a helyi hely felügyeleti berendezései automatikusan átveszik a feladatokat a biztonsági mentési konzollal a rendelkezésre állás megszakítás nélkül történő fenntartása érdekében.

A SOC-munkafolyamatokkal szorosan integrálva és a könyvek futtatásával egyszerűen rangsorolhatja a kockázatcsökkentő tevékenységeket és a fenyegetések közötti összefüggéseket.

- Holisztikus – a bonyolultság csökkentése egyetlen egységesített platformmal az eszközkezelés, a kockázatkezelés és a sebezhetőségek kezelése terén, valamint a fenyegetések figyelése az incidensek megválaszolásával.

- Összesítés és korreláció – az összes helyről összegyűjtött adatok és riasztások megjelenítése, összesítése és elemzése.

- Az összes érzékelő vezérlése – egyetlen helyről konfigurálhatja és figyelheti az összes érzékelőt.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="Kezelheti az összes riasztást és információt.":::

### <a name="azure-portal"></a>Azure Portal

Az Azure-beli IoT-portál Defender az alábbiakkal segíti Önt:

- Megoldási berendezések vásárlása

- Szoftver telepítése és frissítése
- Érzékelők bevezetése az Azure-ba
- Veszélyforrások elleni intelligencia-csomagok frissítése

## <a name="agent-based-solution-for-device-builders"></a>Ügynökalapú megoldás eszközkészítők számára

### <a name="embedded-security-agent-built-in-mode"></a>Beágyazott biztonsági ügynök: beépített üzemmód

A **beépített** módban a Defender for IoT engedélyezve van, ha úgy dönt, hogy bekapcsolja az IoT hub **biztonsági** beállítását. A valós idejű monitorozást, ajánlásokat és riasztásokat kínáló beépített üzemmód egylépéses eszköz-láthatóságot és páratlan biztonságot nyújt. A beépített módban nem szükséges az ügynök telepítése semmilyen eszközön, és a naplózott tevékenységekben a speciális elemzések segítségével elemezheti és védetté teheti a terepi eszközt és az IoT hubot.

### <a name="embedded-security-agent-enhanced-mode"></a>Beágyazott biztonsági ügynök: bővített mód

**Bővített** módban, miután bekapcsolta az IoT hub **biztonsági** beállítását, és telepíti a Defendert a IoT-eszközökön az eszközökön, az ügynökök összegyűjtik, összesítik és elemzik a nyers biztonsági eseményeket az eszközökről. A nyers biztonsági események közé tartozhatnak az IP-kapcsolatok, a folyamatok létrehozása, a felhasználói bejelentkezések és egyéb fontos információk. A IoT-ügynökök védelmezője az események összesítését is kezeli, így elkerülhető a hálózati átviteli sebesség. Az ügynökök nagy mértékben testreszabhatók, így adott feladatokhoz is használhatók, például csak a leggyorsabb SLA-ban, vagy a kiterjedt biztonsági információk és környezetek nagyobb szegmensekre történő elküldésével, a magasabb szolgáltatási költségek elkerülésével.

Az eszközök ügynökei és más alkalmazásai az **Azure Send Security Message SDK** használatával biztonsági adatokat küldhetnek az Azure IoT hub-ba. Az IoT hub beolvassa ezeket az információkat, és továbbítja a Defender for IoT szolgáltatásnak.

Ha a Defender for IoT szolgáltatás engedélyezve van, a továbbított adatokon felül a IoT hub az összes belső adatmennyiségét is elküldi a IoT Defender által végzett elemzéshez. Ezek az adategységek a Felhőbeli műveleti naplókat, az eszköz identitásait és a hub konfigurációját tartalmazzák. Mindezen információk segítenek létrehozni a Defendert a IoT Analytics-folyamathoz.

A IoT Analytics-folyamat Defender a Microsoft és a Microsoft partnerei által használt különböző forrásokból származó további veszélyforrások elleni intelligenciát is megkapja. A IoT teljes elemzési folyamatának védelmezője a szolgáltatásban végzett összes ügyfél-konfigurációval működik (például az egyéni riasztások és a biztonsági üzenet küldése SDK használata).

Az elemzési folyamat használatával a Defender for IoT az összes adatfolyamot ötvözi a gyakorlatban használható javaslatok és riasztások létrehozásához. A folyamat a biztonsági kutatók és szakértők által létrehozott egyéni szabályokat, valamint a szabványos eszköz viselkedésével és a kockázatelemzéstől való eltérést kereső gépi tanulási modelleket is tartalmaz.

A IoT ajánlásai és értesítései (analitikai folyamat kimenete) az egyes ügyfelek Log Analytics munkaterületére íródnak. A munkaterületen található nyers események, valamint a riasztások és javaslatok segítségével a gyanús tevékenységek pontos részleteivel részletesen elmerülhetnek a felderítéssel kapcsolatos vizsgálatok és lekérdezések.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="A Micro Agent architektúrája.":::

## <a name="see-also"></a>Lásd még

[Defender for IoT – gyakori kérdések](resources-frequently-asked-questions.md)

[Rendszer-előfeltételek](quickstart-system-prerequisites.md)
