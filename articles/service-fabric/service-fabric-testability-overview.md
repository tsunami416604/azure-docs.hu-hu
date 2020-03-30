---
title: Hibaelemzési szolgáltatás – áttekintés
description: Ez a cikk ismerteti a Service Fabric hibaelemzési szolgáltatás a hibák indukálása és a szolgáltatások tesztelési forgatókönyvek futtatásához.
author: anmolah
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: d5c770a4d823ebe9b2700b081c407c54dd1d18a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465565"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Bevezetés a hibaelemzési szolgáltatásba
A Hibaelemzési szolgáltatás a Microsoft Azure Service Fabric-re épülő szolgáltatások tesztelésére szolgál. A Fault Analysis Service segítségével érdemi hibákat hozhat ki, és teljes tesztforgatókönyveket futtathat az alkalmazásokon. Ezek a hibák és forgatókönyvek gyakorolják és érvényesítik a számos állapotot és átmenetet, amelyeket egy szolgáltatás az egész élettartama alatt tapasztal, mindezt ellenőrzött, biztonságos és következetes módon.

A műveletek azok az egyes hibák, amelyek egy szolgáltatást a teszteléshez céloznak. A szolgáltatásfejlesztő használhatja ezeket építőelemként bonyolult forgatókönyvek írásához. Példa:

* Indítsa újra a csomópontot, hogy szimulálja a számos olyan helyzetben, amikor egy gép vagy virtuális gép újraindul.
* Az állapotalapú szolgáltatás kópiájának áthelyezése a terheléselosztás, a feladatátvétel vagy az alkalmazásfrissítés szimulálása érdekében.
* Kvórumveszteség meghívása egy állapotalapú szolgáltatás on hozzon létre egy olyan helyzetet, ahol írási műveletek nem folytatható, mert nincs elég "back-up" vagy "másodlagos" replikák az új adatok fogadásához.
* Adatvesztés meghívása egy állapotalapú szolgáltatás olyan helyzetet hoz létre, amelyben az összes memóriában állapot teljesen törlődik.

A forgatókönyvek összetett műveletek, amelyek egy vagy több műveletből állnak. A Hibaelemzési szolgáltatás két beépített teljes forgatókönyvet biztosít:

* Káosz forgatókönyv
* Feladatátvételi forgatókönyv

## <a name="testing-as-a-service"></a>Tesztelés szolgáltatásként
A Fault Analysis Service egy Service Fabric rendszerszolgáltatás, amely automatikusan elindul egy Service Fabric-fürttel. Ez a szolgáltatás a hibabefecskendezés, a tesztforgatókönyv-végrehajtás és az állapotelemzés gazdagépeként működik. 

![Hibaelemző szolgáltatás][0]

Hibaművelet vagy tesztforgatókönyv indításakor a rendszer parancsot küld a hibaelemzési szolgáltatásnak a hibaművelet vagy a tesztforgatókönyv futtatásához. A hibaelemzési szolgáltatás állapotalapú, így megbízhatóan futtathatja a hibákat és forgatókönyveket, és érvényesítheti az eredményeket. Például egy hosszú ideig futó tesztforgatókönyv megbízhatóan hajthatja végre a Hibaelemzési szolgáltatás. És mivel a tesztek végrehajtása a fürtön belül történik, a szolgáltatás megvizsgálhatja a fürt és a szolgáltatások állapotát, hogy részletesebb információkat nyújtson a hibákról.

## <a name="testing-distributed-systems"></a>Elosztott rendszerek tesztelése
A Service Fabric jelentősen megkönnyíti az elosztott méretezhető alkalmazások írását és kezelését. A hibaelemzési szolgáltatás hasonlóan megkönnyíti az elosztott alkalmazások tesztelését. A tesztelés során három fő problémát kell megoldani:

1. Szimulálása/generálása hibák, amelyek valós helyzetekben előforduló hibák: A Service Fabric egyik fontos szempontja, hogy lehetővé teszi az elosztott alkalmazások helyreállítása a különböző hibák. Azonban annak teszteléséhez, hogy az alkalmazás képes-e helyreállítani ezeket a hibákat, szükségünk van egy mechanizmus szimulálására/generálására ezeket a valós hibákat egy ellenőrzött tesztkörnyezetben.
1. A korrelált hibák: A rendszer alapvető hibái, például a hálózati és a géphibák, könnyen előállíthatók. Az egyes hibák kölcsönhatásai következtében a valós világban bekövetkező forgatókönyvek létrehozása nem triviális.
1. Egységes felhasználói élmény a fejlesztés és üzembe helyezés különböző szintjein: Sok olyan hibabefecskendező rendszer létezik, amely különböző típusú hibákat képes megtenni. Azonban a tapasztalat az összes ilyen gyenge, ha mozog egy dobozos fejlesztői forgatókönyvek, hogy ugyanazokat a teszteket nagy tesztkörnyezetekben, hogy használja őket a tesztek éles környezetben.

Bár számos mechanizmus van a problémák megoldására, egy olyan rendszer, amely ugyanezt teszi a szükséges garanciákkal - egészen az egydobozos fejlesztői környezettől az éles fürtökben való teszteléshez - hiányzik. A Hibaelemzési szolgáltatás segítségével az alkalmazásfejlesztők az üzleti logikájuk tesztelésére összpontosíthatnak. A Hibaelemzési szolgáltatás biztosítja a szolgáltatás és az alapul szolgáló elosztott rendszer közötti interakció teszteléséhez szükséges összes lehetőséget.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Valós hibaforgatókönyvek szimulálása/létrehozása
Az elosztott rendszer meghibásodások elleni robusztusságának teszteléséhez szükségünk van egy mechanizmusra a hibák létrehozásához. Míg elméletben egy hiba, mint egy csomópont lefelé úgy tűnik, egyszerű, elkezdi üti az azonos konzisztencia problémák, amelyek a Service Fabric próbál megoldani. Ha például le szeretnénk állítani egy csomópontot, a szükséges munkafolyamat a következő:

1. Az ügyféltől adjon ki egy leállítási csomópontkérelmet.
1. Küldje el a kérelmet a megfelelő csomópontra.
   
    a. Ha a csomópont nem található, akkor nem sikerül.
   
    b. Ha a csomópont megtalálható, csak akkor adja vissza, ha a csomópont le van állítva.

A hiba tesztelési szempontból történő ellenőrzéséhez a tesztnek tudnia kell, hogy a hiba kihirdetése esetén a hiba ténylegesen megtörténik. A Service Fabric által nyújtott garancia az, hogy vagy a csomópont leáll, vagy már leállt, amikor a parancs elérte a csomópontot. Mindkét esetben a tesztnek képesnek kell lennie arra, hogy helyesen indokolja meg az állapotot, és sikeres vagy sikertelen legyen az érvényesítéssorán. A Service Fabric-en kívül megvalósított rendszer ugyanazokat a hibákat okozhat számos hálózati, hardver- és szoftverproblémát, ami megakadályozná az előző garanciák biztosítását. A korábban megadott problémák jelenlétében a Service Fabric újrakonfigurálja a fürtállapotát a problémák megkerülése érdekében, és így a hibaelemzési szolgáltatás továbbra is képes lesz a megfelelő garanciákat adni.

### <a name="generating-required-events-and-scenarios"></a>Szükséges események és forgatókönyvek létrehozása
Míg szimulálja a valós kudarc következetesen nehéz kezdeni, a képesség, hogy létrehoz korrelált hibák még keményebb. Adatvesztés történik például egy állapotalapú, megőrzött szolgáltatásban, amikor a következő dolgok történnek:

1. A replikák nak csak egy írási kvóruma van felzárkózva a replikációhoz. Az összes másodlagos replikák lemaradnak az elsődleges.
1. Az írási kvórum leáll, mert a replikák leállnak (egy kódcsomag vagy csomópont miatt.
1. Az írási kvórum nem tud biztonsági másolatot készíteni, mert a replikák adatai elvesznek (lemezsérülés vagy a gép újraimagingja miatt).

Ezek a korrelált hibák nem történik meg a valós világban, de nem olyan gyakran, mint az egyéni kudarcok. Az ilyen forgatókönyvek tesztelése az éles környezetben való bekövetkezésük előtt kritikus fontosságú. Még ennél is fontosabb az a képesség, hogy szimulálja ezeket a forgatókönyveket éles számítási feladatok ellenőrzött körülmények között (a nap közepén az összes mérnök a fedélzeten). Ez sokkal jobb, mint ha először történne meg a gyártásban hajnali 2-kor.

### <a name="unified-experience-across-different-environments"></a>Egységes élmény a különböző környezetekben
A gyakorlat hagyományosan az volt, hogy hozzon létre három különböző tapasztalatok, az egyik a fejlesztési környezet, az egyik a vizsgálatok, és egy termelési. A modell a következő volt:

1. A fejlesztési környezetben készítsen állapotátmeneteket, amelyek lehetővé teszik az egyes módszerek egységes tesztjeit.
1. A tesztkörnyezetben hibákat hozhat létre, amelyek lehetővé teszik a különböző hibaforgatókönyveket lehetővé tévő végpontok között végzett teszteket.
1. Tartsa meg az éles környezetet érintetlenül, hogy megelőzze a nem természetes hibákat, és biztosítsa, hogy rendkívül gyors emberi válasz legyen a meghibásodásra.

A Service Fabric, a Fault Analysis Service, azt javasoljuk, hogy fordítsa meg ezt, és ugyanazt a módszert használja a fejlesztői környezettől az éles környezetig. Ezt kétféleképpen lehet elérni:

1. Ellenőrzött hibák kiváltásához használja a Fault Analysis Service API-kat egy egydobozos környezetből egészen az éles fürtökig.
1. Ha a fürtnek olyan lázat szeretne adni, amely a hibák automatikus indukcióját okozza, használja a Hibaelemzési szolgáltatást automatikus hibák létrehozásához. A hibák konfiguráción keresztüli szabályozása lehetővé teszi, hogy ugyanazt a szolgáltatást különböző környezetekben eltérően teszteljék.

A Service Fabric, bár a hibák mértéke eltérő lenne a különböző környezetekben, a tényleges mechanizmusok azonosak lennének. Ez lehetővé teszi egy sokkal gyorsabb kód-to-deployment folyamat, és a szolgáltatások tesztelésének lehetősége valós terhelések alatt.

## <a name="using-the-fault-analysis-service"></a>A hibaelemző szolgáltatás használata
**C #**

A Fault Analysis Service szolgáltatásai a System.Fabric névtérben találhatók a Microsoft.ServiceFabric NuGet csomagban. A Hibaelemzési szolgáltatás szolgáltatásainak használatához adja meg a nuget csomagot referenciaként a projektben.

**Powershell**

A PowerShell használatához telepítenie kell a Service Fabric SDK-t. Az SDK telepítése után a ServiceFabric PowerShell-modul automatikusan betöltődik a használatához.

## <a name="next-steps"></a>További lépések
A valóban felhőszintű szolgáltatások létrehozásához elengedhetetlen, hogy a telepítés előtt és után is biztosítsa, hogy a szolgáltatások ellenálljanak a valós hibáknak. A mai szolgáltatási világban nagyon fontos az a képesség, hogy gyorsan újítson és a kódot gyorsan a termelésbe helyezze át. A Hibaelemzési Szolgáltatás segít a szolgáltatásfejlesztőknek pontosan ezt megtenni.

Kezdje el tesztelni az alkalmazásokat és a szolgáltatásokat a beépített [tesztforgatókönyvek](service-fabric-testability-scenarios.md)használatával, vagy saját tesztforgatókönyveket a Hibaelemzési szolgáltatás által biztosított [hibaműveletek](service-fabric-testability-actions.md) használatával.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
