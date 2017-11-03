---
title: "Hiba az Analysis Services áttekintése |} Microsoft Docs"
description: "Ez a cikk ismerteti a tartalék elemzési szolgáltatás a Service Fabric hibák hogy és tesztelési forgatókönyvek futtatott a szolgáltatásokhoz."
services: service-fabric
documentationcenter: .net
author: anmolah
manager: timlt
editor: vturecek
ms.assetid: 1f064276-293a-4989-a513-e0d0b9fdf703
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: f275fa5d3d6d727b016e55c188321d7e68091a33
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-the-fault-analysis-service"></a>A tartalék Analysis Service bemutatása
A tartalék elemzési szolgáltatás végzi a Microsoft Azure Service Fabric épülő szolgáltatások tesztelése. A tartalék Analysis Service idéz elő a jelentéssel bíró hibák, és a teljes teszt forgatókönyvek az alkalmazások futtatásához. Ezeket a hibákat és forgatókönyvek gyakorolja, és érvényesítse a számos állapotok és átmenetek, amelyeknek a szolgáltatás teljes élettartamuk összes ellenőrzött, biztonságos és egységes módon.

Műveletek a célcsoport-kezelési vizsgálja, hogy a szolgáltatás egyes hibák. A szolgáltatás fejlesztők segítségével ezek építőelemeiként összetett forgatókönyvek írni. Példa:

* Indítsa újra az olyan helyzetekben, ahol egy számítógép vagy virtuális gép újraindítása után tetszőleges számú szimulálása csomópont.
* Helyezze át egy replikát készít az állapot-nyilvántartó szolgáltatás terheléselosztás, feladatátvétel vagy az alkalmazásfrissítés szimulálásához.
* Egy állapotalapú szolgáltatás segítségével olyan helyzetet, ahol írási műveletek nem folytatható, mert nincs elég "biztonsági másolat" vagy "másodlagos" replikák új adatokat fogadni a kvórum elvesztése meghívni.
* Olyan helyzet, ahol minden a memóriában teljesen tárolt adatok törléséig kimenő létrehozásához állapotalapú Service az adatvesztés meghívni.

Forgatókönyvek a következők egy vagy több művelet álló összetett műveletek. A tartalék Analysis Service két beépített teljes eseteit tartalmazza:

* Chaos forgatókönyv
* Feladatátvételi forgatókönyv

## <a name="testing-as-a-service"></a>Szolgáltatásként tesztelése
A tartalék Analysis Service a Service Fabric-szolgáltatás, amely a Service Fabric-fürt automatikusan elindul. Ez a szolgáltatás gazdaként van, a vizsgálat a forgatókönyv végrehajtási és állapotelemzésre. 

![Hiba az Analysis Services][0]

Egy tartalék művelet vagy teszt forgatókönyv elindításakor egy parancsot a tartalék Analysis Service akkor küldi a tartalék művelet vagy teszt forgatókönyv futtatásához. A tartalék Analysis Services rendszer állapotalapú, így azt megbízhatóan hibák és a forgatókönyvek futtatása, és ellenőrizze az eredményeket. Például egy hosszú futású tesztkörnyezet megbízhatóan által hajtható végre a tartalék Analysis Service. És tesztek végrehajtása vannak zajlik a fürtben található, mert a szolgáltatás állapotát, a fürt és a szolgáltatások biztosításához hibákkal kapcsolatos további részletes információkért tekintse meg.

## <a name="testing-distributed-systems"></a>Elosztott rendszerek tesztelése
A Service Fabric miatt a írást, és jelentősen egyszerűbb elosztott méretezhető alkalmazások kezelése. A tartalék elemzési szolgáltatás lehetővé teszi, hasonlóképpen könnyebb egy elosztott alkalmazás tesztelése. Számos három fő során felmerülő kérdések megoldandó tesztelése során.

1. Olyan valós forgatókönyv előforduló hibák szimulálva/létrehozásakor: a fontos szempontja a Service Fabric egyik, hogy engedélyezi-e az elosztott alkalmazások számára a különféle hibák helyreállításához. Azonban, hogy felderítenie helyreállíthatja-e az alkalmazás teszteléséhez kell eljárást, amely a valós hibák ellenőrzött tesztkörnyezetben szimulálása/készítése.
2. Korrelált hibák képességét: alapvető hibák a rendszerben, például hálózati hibák és a gép hibák, könnyen készítsen külön-külön. Nem triviális létrehozása, amely akkor fordulhat elő, a valós életben miatt ezek az egyes hibák kapcsolati forgatókönyvek jelentős számú.
3. Egységes felhasználói élmény fejlesztésére és üzembe különböző szintjei között: fault injektálási sok rendszert, amelyek képesek különböző típusú hibák. Azonban a felhasználói élménye mindegyik gyenge, amikor egy beépített fejlesztői forgatókönyvek esetén az azonos tesztek futtatása nagy tesztkörülmények között, éles környezetben vizsgálatokhoz a.

Miközben sok mechanizmusok e problémák, a rendszer, amelyet azonos az kötelező garanciák – egészen a egy beépített fejlesztői környezetből, tesztelése az éles-fürtök hiányzik. A tartalék Analysis Service a fejlesztőket az alkalmazás tesztelése az üzleti logika összpontosíthat. A tartalék Analysis Services összes igényének tesztelése a szolgáltatás az alapul szolgáló elosztott rendszerrel folytatott kommunikációjuktól biztosít.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Valós hibát jelentő forgatókönyvek szimulálva/létrehozása
Megbízhatóságát egy elosztott rendszer-hibákkal szemben, teszteléséhez, igazolnia kell egy olyan mechanizmus hibák létrehozásához. A elméletileg generálásához. a hiba, például egy csomópont úgy tűnik, könnyen, kezdődik elérte-e ugyanazokat a Service Fabric próbál megoldani konzisztencia problémákat. Tegyük fel, ha azt szeretné, hogy egy csomópont le kell állítania a szükséges munkafolyamat a következő:

1. Az ügyféltől adjon ki egy leállítási csomópont kérelmet.
2. A kérelem elküldése a helyes csomópont.
   
    a. Ha a csomópont nem található, a kell sikertelen.
   
    b. Ha a csomópont található, az kell visszaadnia csak ha a csomópont le van állítva.

Ha ellenőrizni szeretné a sikertelen teszt szempontjából, a vizsgálat tudnia kell, hogy ez a hiba előidézett, a hiba ténylegesen történik. A biztosítja, hogy a Service Fabric biztosít, amely vagy a csomópont halad, vagy már korábban le volt a parancs a csomópont elérésekor. Mindkét esetben a vizsgálat kell tudni megfelelően a állapotával kapcsolatos információkat az okból és sikeres, vagy helytelenül az érvényesítés sikertelen. A rendszer a Service Fabric hibák ugyanazokat a kulcstulajdonságokat ehhez kívül végrehajtott sikerült találati számos hálózati hardveres és szoftverekkel kapcsolatos problémák akadályozzák az előző garanciát. Mellett a problémák előtt is hangsúlyoztuk a Service Fabric rendszer konfigurálja újra a problémák megoldása a fürt állapota, és ezért a tartalék Analysis Service is ahhoz, hogy megkapja az garanciák megfelelő halmazát.

### <a name="generating-required-events-and-scenarios"></a>Szükséges események és forgatókönyvek létrehozása
Ugyan valós meghibásodásának következetesen szimulálása bonyolult ahhoz, még akkor is szigorúbb képességét kapcsolódó hibák. Például egy adatvesztés történik a megőrzött állapot-nyilvántartó szolgáltatás fordulhat elő, a következőket:

1. A replikáció csak a replikák írási kvórum észlelt. A másodlagos replikákat az elsődleges verziót használják.
2. Az írási kvórum a (mert kódcsomag vagy fog csomópont) lesz replikák miatt leáll.
3. Az írási kvórum nem helyreállnak mivel az adatok a replikák számára (mert a lemez sérülés vagy a gép különösen) elvesznek.

A kapcsolódó hibák fordulhat elő, a valós életben, de nem gyakran egyes hibák. Fontos a forgatókönyvek esetén tesztelése előtt akkor fordulhat elő, éles környezetben képes. Még fontosabb azt a képességet a szimulálhatja a termelési számítási feladatokhoz ellenőrzött körülmények között (közepén összes mérnökök meg kell oldani a nap). Ez az javulás, mint azt fordulhat elő, 2:00 órakor éles környezetben az első alkalommal

### <a name="unified-experience-across-different-environments"></a>Egységes felhasználói élmény különböző környezetek között
Az eljárás-ra volt lép, egyet a fejlesztési környezet, egy a teszteket, és egy üzemi három különböző felhasználócsoportokhoz létrehozásához. A modell a következő volt:

1. A fejlesztői környezetben, amelyek lehetővé teszik az egyes módszerek egység tesztek Állapotváltások eredményez.
2. A tesztkörnyezetben a végpont vizsgálatok különböző hibát jelentő forgatókönyvek, amelyek sikertelen eredményez.
3. Nem természetes hibák megelőzése érdekében, és győződjön meg arról, hogy van-e hiba rendkívül gyors emberi válasz eredeti tartsa meg az üzemi környezetben.

A Service Fabric, a tartalék elemzési szolgáltatás azt is javaslat, amely kapcsolja be ezt, és a fejlesztői környezet módszert üzemi használata. Ennek eléréséhez két módja van:

1. Ellenőrzött hibák, hogy a tartalék Analysis Service API-egy 1 keretes környezetben egészen az éles fürt használja.
2. Ahhoz, hogy megkapja a fürt egy elleni, a hibák automatikus indukciós okozó, használja a tartalék Analysis Service automatikus hibák létrehozásához. Konfigurációs hibák mértéke szabályozása lehetővé teszi, hogy másképp vizsgálandó különböző környezetekben ugyanazt a szolgáltatást.

A Service Fabric abban az esetben, ha a hibák mértéke a különböző környezetekben eltérő lenne a tényleges mechanizmusok lenne azonos. Ez egy sokkal gyorsabb kód való-telepítési folyamatot, és tesztelje a szolgáltatások valós terhelés alatt képes tesz lehetővé.

## <a name="using-the-fault-analysis-service"></a>A tartalék elemzési szolgáltatás használatával
**C#**

Hiba az Analysis Services szolgáltatások akkor a Microsoft.ServiceFabric NuGet-csomagot a System.Fabric névtérben. A hiba az Analysis Services szolgáltatásainak használatával közé tartozik a nuget-csomagot a projekt hivatkozásként.

**PowerShell**

PowerShell használatához telepítenie kell a Service Fabric SDK. Az SDK telepítése után a a ServiceFabric PowerShell modul az automatikus betöltött használatára.

## <a name="next-steps"></a>Következő lépések
Valóban felhőméretű szolgáltatások létrehozására, kiemelten fontos annak biztosítása érdekében előtt, és a központi telepítést követően, hogy a szolgáltatások is képes elviselni a valós életben hibák. A szolgáltatások világ ma, gyorsan innovációját és gyors áthelyezése a kód üzemi nagyon fontos. A tartalék Analysis Service szolgáltatásfejlesztők pontosan ennek segítségével.

Az alkalmazások és szolgáltatások segítségével a beépített tesztelés megkezdése [forgatókönyvek tesztelése](service-fabric-testability-scenarios.md), vagy a saját Tesztelési forgatókönyvek használatával hozhatnak létre a [műveletek fault](service-fabric-testability-actions.md) a tartalék Analysis Services által biztosított.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
