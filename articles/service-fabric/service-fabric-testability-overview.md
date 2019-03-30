---
title: Tartalék elemzési szolgáltatás áttekintése |} A Microsoft Docs
description: Ez a cikk ismerteti a Service Fabric Fault Analysis Service hogy hibák és tesztelési forgatókönyvek futtat a szolgáltatásokat.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: chackdan
editor: vturecek
ms.assetid: 1f064276-293a-4989-a513-e0d0b9fdf703
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: 3581550779b2387515b4f300d211b4e0a894edc7
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662353"
---
# <a name="introduction-to-the-fault-analysis-service"></a>A Fault Analysis Service bemutatása
A Fault Analysis Service tesztelése a Microsoft Azure Service Fabric beépített szolgáltatás lett tervezve. A Fault Analysis Service is idéz elő a jelentéssel bíró hibák és a teljes teszt forgatókönyvek az alkalmazások futtatásához. Ezek a hibák és a forgatókönyvek gyakorolja, és ellenőrizze a számos állapotok és átmenetek, amely egy szolgáltatás teljes élettartamuk, az összes olyan ellenőrzött, megbízható és konzisztens módon fog tapasztalni.

A műveletek az egyes hibák célzó vizsgálja, hogy a szolgáltatás olyan. Egy szolgáltatás fejlesztői használhatja ezeket az adatokat építőelemeket összetett forgatókönyvek írni. Példa:

* Indítsa újra a szimulálásához helyzetekben, ahol egy gépet vagy virtuális gép újraindítása bármennyi csomópontot.
* Helyezze át egy replikát, az állapotalapú szolgáltatás szimulálása a terheléselosztást, a feladatátvétel vagy az alkalmazás frissítését.
* Az állapotalapú szolgáltatások egy olyan helyzetet, ahol nem lehet folytatni az írási műveletek, mert nincs elég új adatokat fogadni "biztonsági mentése" vagy "másodlagos" replika létrehozása a kvórum elvesztése meghívása.
* Adatvesztés előidézése a létrehozása olyan helyzet, amelyben minden a memóriában teljesen törli az állapotalapú szolgáltatások.

Forgatókönyvek összetett operations mikroszolgáltatásokból álló, egy vagy több műveletet is. A Fault Analysis Service két beépített teljes forgatókönyv biztosítja:

* A Chaos forgatókönyv
* Feladatátvételi forgatókönyv

## <a name="testing-as-a-service"></a>Szolgáltatás tesztelése
A Fault Analysis Service egy Service Fabric-szolgáltatás, amely a Service Fabric-fürt automatikusan elindul. Ez a szolgáltatás a gazdagép hibabeszúrás, teszt forgatókönyv végrehajtási és egészségügyi elemzés funkcionál. 

![Fault Analysis Service][0]

Tartalék művelet vagy tesztkörnyezetben a forgatókönyv elindításakor a tartalék művelet vagy tesztkörnyezetben forgatókönyvet futtatni egy parancsot küldött Fault Analysis Service. A Fault Analysis Service az állapot-nyilvántartó, így is megbízhatóan hibák és forgatókönyvek futtatása vagy eredmény ellenőrzése. Ha például egy hosszú ideig futó tesztkörnyezet megbízhatóan által hajtható végre Fault Analysis Service. És tesztek végrehajtása van a fürtben, mivel a szolgáltatás megvizsgálhatja a fürt és a hibákkal kapcsolatos részletesebb információkat biztosít a szolgáltatások állapotát.

## <a name="testing-distributed-systems"></a>Az elosztott rendszerek tesztelése
A feladat írása, és jelentősen egyszerűbb elosztott méretezhető alkalmazások kezelése a Service Fabric gondoskodik. A Fault Analysis Service lehetővé teszi egy hasonló módon egyszerűbb elosztott alkalmazás tesztelése. Nincsenek három fő problémák tesztelése során kell megoldani:

1. Szimuláló/létrehozásakor fellépő hibákat a valós életből vett példák: A fontos szempontja a Service Fabric egyik célja, hogy lehetővé teszi az elosztott alkalmazások számára a különféle hibák helyreállításához. Azonban, hogy ezek a hibák helyreállíthatók-e az alkalmazás teszteléséhez kell egy mechanizmust, ezeket egy kontrollált tesztkörnyezetben végezze a való életből vett hibák szimulálása/létrehozni.
1. Korrelált hibák képességét: A rendszer, például hálózati hibák és a gép hibák, alapvető hibák is egyszerűen külön-külön előállításához. Nem triviális generálása jelentős számos forgatókönyv esetében, amely akkor fordulhat elő a való világból interakciókat ezeket az egyes hibák miatt.
1. Egységes felhasználói élményt között különböző szintjeit fejlesztéséhez és üzembe helyezéséhez: Nincsenek tartalék injektálási számos rendszer, amelyeket a különböző típusú hibák. Azonban a felhasználói felület az ezek mindegyikét, gyenge, beépített fejlesztői forgatókönyveket, a ugyanazokat a teszteket futó nagy tesztelési környezetekben, éles környezetben tesztek az áthelyezése esetén.

Bár vannak e problémák, a rendszer, amely megegyezik a szükséges garanciákkal – egészen a beépített fejlesztői környezet tesztelése az éles fürtök számos mechanizmusok hiányzik. A Fault Analysis Service a fejlesztőket az alkalmazás tesztelése a saját üzleti logikája összpontosít. A Fault Analysis Service biztosítja az összes szükséges a szolgáltatás az alapjául szolgáló elosztott rendszer közötti teszteléséhez.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Hiba a való életből vett forgatókönyv szimuláló/generálása
Teszt megbízhatóságát, egy elosztott rendszer meghibásodásokkal szemben, hibák létrehozása egy olyan mechanizmust kell. Bár elméletben létrehozása egy csomópont le egyszerű, úgy tűnik, hiba elindítja lenyomásával ugyanazokat a konzisztencia problémák, amelyek a Service Fabric próbál megoldani. Például, ha azt szeretné, hogy állítsa le a csomópontot, a szükséges munkafolyamat a következő:

1. Az ügyféltől adjon ki egy leállítási csomópont kérelmet.
1. A kérés küldése a helyes csomópont.
   
    a. Ha a csomópont nem található, sikertelennek kell lennie.
   
    b. Ha a csomóponton található, kell visszaadnia csak ha a csomópont leáll.

Ellenőrizze a hiba a teszt szempontjából, a teszt tudnia kell, hogy ezt a hibát okozta van, a hiba valójában nem történik. A csomópont a garantálja, hogy a Service Fabric biztosítja, hogy mindkét lép, vagy le a parancs elérésekor a csomópont már volt. Mindkét esetben a teszt megfelelően az okból az állapotával kapcsolatos információkat és sikeres legyen, vagy megfelelően az érvényesítés sikertelen képesnek kell lennie. A rendszer a Service Fabric ehhez ugyanazokat a hibák kívül végrehajtott sikerült találati számos hálózati hardveres és szoftverekkel kapcsolatos problémák, amely megakadályozná a fenti garanciát. Zajok mellett a problémákat, mielőtt hangsúlyoztuk a Service Fabric újra fogja konfigurálni a problémák megoldása a fürt állapota, és ezért a Fault Analysis Service továbbra is képesek lesznek garanciák helyes készletével biztosíthat.

### <a name="generating-required-events-and-scenarios"></a>Szükséges események és a forgatókönyvek létrehozása
Bár egy való életből vett hiba szimulálása következetesen legégetőbb a következővel kell kezdődnie, még szigorúbb arra, hogy hozzon létre a kapcsolódó hibák. Például egy adatvesztés történik az alkalmazásban az állapotalapú állandó szolgáltatások a következő dolog történik:

1. Csak a replikák írási kvórum a replikáció is szerepelnek. A másodlagos replikák késés jelentkezhet az elsődleges.
1. Az írási kvórum a replikákat a kódcsomag vagy leáll csomópont) (miatti leállítása előtt miatt leáll.
1. Az írási kvórum nem visszatérhet az adatok a replikák (miatt a lemez sérülésére vagy a gép rendszerképének alaphelyzetbe állítása) megszakadása miatt.

Ezek a hibák korrelált fordulhat elő, a való világból, habár nem gyakran egyes hibák. Arra, hogy az ilyen feladatokhoz szükséges tesztelni, mielőtt éles környezetben. Még ennél is fontosabbak rendszer azon képessége, ezek szimulálhatja a termelési számítási feladatokhoz, ellenőrzött körülmények között (közepén összes mérnökök, meg kell oldani a nap). Ez sokkal jobb, mint 2:00 órakor éles környezetben először fordulhat elő, ha

### <a name="unified-experience-across-different-environments"></a>Egységes felhasználói élményt a különböző környezetek között
Az eljárás volt hozhat létre három más-más részhalmazához élményt, egy fejlesztési környezetben, egy a tesztek, és egy éles környezetben. A modell a következő volt:

1. A fejlesztési környezetben hozhat létre, amelyek lehetővé teszik az egyes módszerek egységteszteket állapotváltozási adat áramlik.
1. A tesztelési környezetben előállítani a sikertelen különféle hiba eseteket, amelyek teljes körű teszteket.
1. Tartsa meg az éles környezet eredeti nem természetes hibák elkerülése érdekében, és győződjön meg arról, hogy nincs-e hiba rendkívül gyors emberi választ.

A Service Fabric segítségével a Fault Analysis Service azt vannak felajánlott fejlesztői környezetből módszert az éles környezetben, és kapcsolja be ezt a. Ennek érdekében két módja van:

1. Ellenőrzött hibák idéz elő, használja a tartalék Analysis Service API-k egy beépített környezetből egészen az éles fürtökben.
1. A fürt egy elleni, a hibák automatikus indukciós okozó biztosíthat, a Fault Analysis Service segítségével hozza létre a hibák automatikus. Konfigurálással hibaarányának szabályozása lehetővé teszi a vizsgálandó eltérően a különböző környezetekben ugyanazt a szolgáltatást.

A Service Fabric azonban a hibák a méretezési csoport lehet különböző, a különböző környezetekben a tényleges mechanizmusok lehet azonos. Ez lehetővé teszi egy sokkal gyorsabb kódot,-üzembe helyezési folyamatot és a szolgáltatások valós terhelések tesztelhetik.

## <a name="using-the-fault-analysis-service"></a>A Fault Analysis Service használatával
**C#**

Tartalék Analysis Service-szolgáltatások rendszer Microsoft.ServiceFabric NuGet-csomag a System.fabric – névtérben. A Fault Analysis Service-szolgáltatások használatához közé tartozik a nuget-csomagot a projekt hivatkozásként.

**PowerShell**

Ha a PowerShell segítségével, telepítenie kell a Service Fabric SDK. Az SDK telepítése után a ServiceFabric PowerShell-modul az automatikus használatra betölteni.

## <a name="next-steps"></a>További lépések
Valóban felhőléptékű-szolgáltatások létrehozását, rendkívül fontos annak érdekében, mielőtt és üzembe helyezés után is, hogy a szolgáltatások képes elviselni valós hibák. A szolgáltatások világ mai gyors innováció és a kód gyorsan átállni éles üzemre nagyon fontos. A Fault Analysis Service fejlesztőket szolgáltatás pontosan ezt.

Megkezdheti az alkalmazások és szolgáltatások használatával a beépített tesztelési [Tesztelési forgatókönyvek](service-fabric-testability-scenarios.md), vagy hozhat létre a saját tesztelési helyzetek használata a [műveletek tartalék](service-fabric-testability-actions.md) Fault Analysis Service által biztosított.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
