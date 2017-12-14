---
title: "Ajánlott eljárások az automatikus skálázás |} Microsoft Docs"
description: "Automatikus skálázás minták Azure Web Apps, a virtuálisgép-méretezési készletek és a Cloud Services csomag"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 9fa2b94b-dfa5-4106-96ff-74fd1fba4657
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: ancav
ms.openlocfilehash: d5b33b15c315c7538bba7bf9ae067946f3b6d3c4
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="best-practices-for-autoscale"></a>Ajánlott eljárások az automatikus méretezéshez
Ez a cikk útmutatást ad az ajánlott eljárások az Azure-ban automatikus skálázást. Az Azure a figyelő automatikus skálázás vonatkozik csak a [virtuálisgép-méretezési csoportok](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Felhőszolgáltatások](https://azure.microsoft.com/services/cloud-services/), és [App Service - webalkalmazások](https://azure.microsoft.com/services/app-service/web/). Más Azure-szolgáltatások különböző méretezési módszer használatára.

## <a name="autoscale-concepts"></a>Automatikus skálázás fogalmak
* Egy erőforrás csak rendelkezhet *egy* automatikus skálázási beállítás
* Az automatikus skálázási beállítás egy vagy több profilok és az egyes profilok lehet egy vagy több automatikus skálázási szabályok.
* Az automatikus skálázási beállítás méretezi példányok vízszintesen, amely *kimenő* a példányok növelésével és *a* példányok számának csökkentésével.
  Az automatikus skálázási beállítás tartozik egy maximális, minimális és példányok alapértelmezett értéke.
* Az automatikus skálázási feladat mindig bővítse a, a kapcsolódó metrikát ellenőrizni, hogy az elért kibővített vagy méretezési a beállított küszöbértéket. Listáját megtekintheti a mérőszámok, hogy az automatikus skálázás méretezheti által a [Azure figyelő automatikus skálázás közös metrikák](insights-autoscale-common-metrics.md).
* Minden küszöbértéke egy példány szintjén kiszámítása. Például "1 példány által végzett méretezési amikor átlagos CPU > 80 %, ha a példányok száma 2", azt jelenti, hogy a kibővített 80 %-nál nagyobb átlagos CPU-csomagbeli összes példányra esetén.
* A műveletnapló minden automatikus skálázási hibák naplózása. Ezután úgy konfigurálhatja egy [figyelmeztetés a napló](./monitoring-activity-log-alerts.md) , hogy akkor is értesítést e-mailben, SMS-webhook, amikor az automatikus skálázás sikertelen stb.
* Hasonlóképpen az összes sikeres skálázási művelet feladja a műveletnapló. Is konfigurálhat egy figyelmeztetés a naplót, hogy akkor is értesítést e-mailben, SMS-webhookokkal, amikor a sikeres automatikus skálázási művelet stb. E-mailben vagy webhook értesítések tájékoztatást kaphat az automatikus skálázási beállítás értesítések lapján keresztül sikeres skálázási műveletekhez is konfigurálhatja.

## <a name="autoscale-best-practices"></a>Automatikus skálázás gyakorlati tanácsok
Használhatja az automatikus skálázás használja az alábbi gyakorlati tanácsokat.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Győződjön meg arról, a maximális és minimális értékek különböző és közöttük egy megfelelő margóval rendelkeznie
Ha a beállítást, amely rendelkezik a minimális = 2, maximális = 2, és az aktuális példányszám: 2, akkor fordulhat elő, nem skálázási művelet. Tartsa meg egy megfelelő margója közötti a maximális és minimális példányok számát, amely átfogó jellegűek. Automatikus skálázás mindig méretezze át a korlátok között.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Manuális skálázás automatikus skálázás min és max alaphelyzetbe állítása
Manuálisan frissítse a példányok száma fölött vagy alatt a maximális értéket, ha az automatikus skálázás motor automatikusan méretezi a minimális (Ha alacsonyabb) vagy a maximális (Ha újabb). Például állítsa be a 3. és 6 közötti tartományba esik. Ha egy futó példány, az automatikus skálázás motor méretezi a következő futásakor 3 példányára. Hasonlóképpen ha manuálisan a skálázási 8 példányok, a következő futtatási automatikus skálázási lesz skálázva azt vissza a következő futásakor 6 példányok.  Manuális skálázás csak nagyon ideiglenes, kivéve, ha alaphelyzetbe állítja az automatikus skálázási szabályok is.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Mindig használja a kibővített és skálázási szabály, amely végrehajtja az növekedés és csökkentése
Csak egy részét együttes használatakor automatikus skálázás méretezési modul, amely egyetlen, vagy, amíg a maximálisan engedélyezett, vagy a minimális, elérésekor.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Válassza ki a megfelelő statisztika a diagnosztika mérőszám
Diagnosztika metrikáihoz közül választhat *átlagos*, *minimális*, *maximális* és *teljes* , bővítse a metrikát. A leggyakoribb statisztikai adat *átlagos*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Válassza ki a küszöbértékeket, gondosan az összes mérték
Azt javasoljuk, körültekintően a kibővített és a skála gyakorlati helyzetek alapján különböző küszöbértékek kiválasztása.

A Microsoft *nem javasoljuk* automatikus skálázás beállításai többek között az alábbi példák az azonos vagy nagyon hasonló küszöbérték értékkel, és a feltételek:

* 1 növeléséhez a példányok száma, amikor szálak száma < = 600
* 1 csökkentése példányok száma, amikor szálak száma > = 600

Nézzük például mi is vezethet, hogy tűnhet félrevezető. Vegye figyelembe az alábbi sorrendben.

1. Tegyük fel, 2 esetben először állnak, és majd példányonként szálak átlagos száma eléri a 625.
2. Automatikus skálázás arányosan kimenő hozzáadja a 3. példányt.
3. A következő azt feltételezik, hogy 575 hárul példány között az átlagos szálak száma.
4. Előtt skálázás, milyen a végső állapot becsléséhez automatikus skálázás próbál lesz, ha a kiterjesztett. Például 575 x 3 (aktuális példányszám) = 1,725 / 2 (ha méretezhető végleges száma) 862.5 szálak =. Ez azt jelenti, hogy automatikus skálázás kellene azonnal kibővített újra még azt követően, méretezhető, ha a átlagos szálak számának változatlan marad, vagy csak kis mértékben is tartozik. Azonban ha azt újra, a teljes folyamat akár méretezhető lenne ismételje meg a, ami ki végtelen ciklus.
5. Ez a helyzet ("flapping" hívják) elkerülése érdekében automatikus skálázás nem csökkentheti egyáltalán. Ehelyett kihagyja, és a feltétel reevaluates legközelebb a szolgáltatás feladatot hajt végre. Ez sikerült megzavarják sokan, mert automatikus skálázás nem működik, amikor az átlagos szál 575 volt.

Során egy méretezési a becslés célja "váltakozó állapotú" olyan helyzetekben, a méretezés és a kibővített műveletek folyamatosan hová oda-vissza elkerülése érdekében. Ez a viselkedés tartani, szem előtt, ha úgy dönt, hogy a kibővített azonos küszöbértékeit, és a.

Azt javasoljuk, hogy a kibővített között, és a küszöbértékek egy megfelelő margó kiválasztása. Tegyük fel fontolja meg a következő jobb szabály kombinációja.

* 1 növeléséhez a példányok száma, amikor CPU % > = 80
* 1 csökkentése példányok száma, amikor CPU % < = 60

Ebben az esetben  

1. Tegyük fel, 2 esetben kezdje állnak.
2. Ha példányok között az átlagos CPU % kerül a 80-as, automatikus skálázás méretezi a kimenő hozzáadja egy harmadik példányt.
3. Mostantól azt feltételezi, hogy idővel a CPU % esik 60.
4. Az automatikus skálázás tartozó skála a szabály a végső állapot becslése, mintha méretezési az. Például 60 x 3 (aktuális példányszám) = 180 / 2 (ha méretezhető végleges száma) = 90. Így az automatikus skálázás nem méretezési be, mert a kibővített azonnal újra kellene. Ehelyett kihagyja skálázás.
5. Ellenőrzi a következő alkalommal automatikus skálázás, a CPU-t továbbra is fennáll, 50 essen. Becslése újra - példány 50 x 3 = 150 / 2 példányok = 80-as és a kibővített os küszöb alatt van, a méretezés a sikeresen 2 példányokhoz 75.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Különleges metrikák küszöbértékei skálázás szempontjai
 Például a tároló- és Service Bus-üzenetsorba hossza metrika különleges metrikáihoz küszöbértéke kiszolgálónként példányok jelenlegi száma üzenetek átlagos száma. Gondosan válassza ki a mérőszám a küszöbértéket.

Egy példa jobb viselkedés ismerje meg, hogy a most bemutatják azt.

* Példányok növelhető 1 száma szerint, ha a tároló várólista üzenet-száma > = 50
* Példányok csökkentése 1 száma szerint, ha Storage Üzenetsorába száma < = 10

Vegye figyelembe az alábbi sorrendben:

1. Nincsenek 2 tároló várólista példányok.
2. Üzeneteket érkezni, és a tároló várólista tekintse át, ha a számuk beolvassa az 50. Ön azt feltételezik, hogy az automatikus skálázási elindul egy kibővíthető műveletet. Azonban ügyeljen arra, hogy a rendszer továbbra is 50/2 = példányonként 25 üzeneteket. Igen kibővített nem történik meg. Az első kibővített megtörténjen-e a tároló várólista az összes üzenet számán legyen 100.
3. A következő azt feltételezik, hogy az összes üzenet számán eléri-e a 100 értéket.
4. A 3. tároló várólista példánya kerül egy kibővített művelet miatt.  A következő kibővített művelet nem történik meg, amíg az összes üzenet számán a várólista eléri a 150, mert 150/3 = 50.
5. Most már a várólistában lévő üzenetek számának beolvasása kisebb. 3 osztályt, az első skálázási művelet történik, ha az összes várólistán található összes üzenet 30-ig felvenni, mert 30/3 = a skála a küszöbérték, példányonként 10 üzeneteket.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Ha az automatikus skálázási beállításban több profilok méretezéshez szempontok
Az automatikus skálázási beállításban választhat egy alapértelmezett profilt, amely minden függőség ütemezés vagy idő nélkül mindig érvényes, vagy választhatja azt egy ismétlődő vagy profil egy meghatározott ideig dátum és idő tartománnyal.

Amikor az automatikus skálázás szolgáltatás feldolgozza azokat, mindig ellenőrzi a következő sorrendben:

1. Rögzített dátum profil
2. Ismétlődő profil
3. Alapértelmezett profil ("Always")

Ha egy profil feltétel teljesül, automatikus skálázás nem ellenőrzi, a következő profil feltétel alá. Automatikus skálázás egyszerre csak egy profil dolgozza fel. Ez azt jelenti, ha azt szeretné, így magába foglalja a feldolgozási feltétel alsó szintű profilból, meg kell adnia ezeket a szabályokat, valamint az aktuális profil.

Tekintsük át, ennek egy példát:

Az alábbi képen láthatók az automatikus skálázási beállítás minimális példányok alapértelmezett profillal = 2 és a maximális példányok = 10. Ebben a példában szabályok kibővített az a várólistán lévő üzenetek száma nagyobb, mint 10 esetén és a skála-a az üzenetek száma a várólistán kevesebb mint 3 esetén. Ezzel az erőforrás méretezhető 2 és 10 példányai között.

Emellett nincs egy ismétlődő profil hétfő beállítása. A példányok minimális érték 3 és legfeljebb példányok = = 10. Ez azt jelenti, hétfőn, az első alkalommal automatikus skálázási ellenőrzi, hogy ez az állapot, ha a példányok száma 2, 3 új minimális méretezés. Mindaddig, amíg az automatikus skálázás továbbra is fennáll, ez a profil feltétel található egyező (hétfő), azt csak dolgozza fel a CPU-alapú scale-out/a szabályok konfigurálva ehhez a profilhoz. Most azt nem ellenőrzi a várólista hossza. Azonban ha azt szeretné ellenőrizni a várólista hossza feltétel is, bele kell foglalni ezeket a szabályokat, az alapértelmezett profil, valamint a hétfő profil.

Hasonlóképpen automatikus skálázás visszatér az alapértelmezett profil, amikor először ellenőrzi, ha a minimális és maximális feltételek teljesülnek-e. Ha a jelenleg a példányok száma 12, méretezés a 10-re, az alapértelmezett profil engedélyezett maximumot.

![automatikus skálázási beállításokat](./media/insights-autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Több szabály profil konfigurálásakor skálázás szempontjai
Előfordulhatnak olyan esetek, ahol előfordulhat, hogy több szabályt beállítani egy profilt. A következő automatikus skálázási szabályok készletét szolgáltatások használatával használata, amikor több szabályok be vannak állítva.

A *horizontális felskálázás*, automatikus skálázás fut, ha minden szabály teljesül.
A *méretezési a*, automatikus skálázás szükséges összes szabályt teljesülniük kell.

Mutatja be, hogy rendelkezik-e a következő 4 automatikus skálázási szabályok használata:

* Ha a CPU < 30 %, méretezési-1
* Ha memória < 50 %-kal, méretezési-1
* Ha a CPU-> 75 %, kibővített 1
* Ha memória > 75 %, kibővített 1

Ezután a következő történik:

* Ha CPU 76 % és memória 50 %-át, hogy a kibővített.
* Ha CPU 50 %-át, és memória 76 % azt kibővített.

Másrészről, ha a CPU 25 % pedig memória 51 % automatikus skálázás nem **nem** méretezési a. Annak érdekében, hogy méretezési a CPU 29 % és a memória 49 % legyen.

### <a name="always-select-a-safe-default-instance-count"></a>Mindig jelölje be a biztonságos alapértelmezett példányszám
Az alapértelmezett példányszám fontos automatikus skálázás méretezi, hogy ez a szolgáltatás, ha metrikák nem érhetők el. Ezért válasszon egy alapértelmezett példányok száma, amelyek a munkaterhelések biztonságos.

### <a name="configure-autoscale-notifications"></a>Értesítések automatikus skálázás konfigurálása
Automatikus skálázás fogja elküldeni a tevékenység naplóba, ha az alábbi feltételek bármelyike teljesül:

* Automatikus skálázás kibocsát egy skálázási művelet
* Automatikus skálázási szolgáltatás sikeresen befejezte a skálázási művelet
* Automatikus skálázás szolgáltatás nem tud a méretezési művelet végrehajtása.
* Metrikák automatikus skálázás szolgáltatás egy méretezési döntést nem érhetők el.
* Adatok gyűjtése le elérhető (helyreállítási) újból, hogy a skála döntést hoznak.

Tevékenységnapló riasztást használhatja az automatikus skálázás motor állapotának figyelésére is. Az alábbiakban példákat [hozzon létre egy tevékenység napló riasztási figyelheti az előfizetés minden automatikus skálázási motor műveletek](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) vagy [hozzon létre egy tevékenység napló riasztási figyelheti az összes sikertelen automatikus skálázás méretezési / műveleteket az előfizetés kiterjesztése](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Mellett napló tevékenységriasztásokat, e-mailben vagy webhook értesítések tájékoztatást kaphat az automatikus skálázási beállítás értesítések lapján keresztül sikeres skálázási műveletekhez is konfigurálhatja.

## <a name="next-steps"></a>Következő lépések
- [Hozzon létre egy tevékenység napló riasztási előfizetés minden automatikus skálázási motor műveletek figyelése.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Hozzon létre egy tevékenység napló riasztási figyelheti az összes sikertelen automatikus skálázás méretezési / műveleteket az előfizetés kiterjesztése](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)
