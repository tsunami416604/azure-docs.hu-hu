---
title: Ajánlott eljárások az automatikus méretezéshez
description: Automatikus skálázási minták az Azure Web Apps, a Virtual Machine Scale sets és a Cloud Services
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 30210d15950302ead0a2406ffb59a61a28d4e54e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997318"
---
# <a name="best-practices-for-autoscale"></a>Ajánlott eljárások az automatikus méretezéshez
Az Azure Monitor automatikus skálázása csak érvényes [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service - webalkalmazások](https://azure.microsoft.com/services/app-service/web/), és [APIManagement-szolgáltatások](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="autoscale-concepts"></a>Automatikus skálázási fogalmak
* Csak egy erőforráshoz lehet *egy* automatikus skálázási beállítás
* Az automatikus skálázási beállítás egy vagy több profilok és az egyes profilok egy vagy több automatikus méretezési szabályokat is rendelkezhet.
* Az automatikus skálázási beállítás méretezhető példányok vízszintesen, amely *ki* növeli a példányok és *a* példányok számának csökkentésével.
  Az automatikus skálázási beállításhoz egy maximális, minimális és példányok alapértelmezett értékét.
* Az automatikus skálázási feladat, mindig a skálázás, a kapcsolódó metrikát ellenőrzi, hogy a beállított küszöbértéket. a horizontális felskálázás vagy horizontális leskálázási elért. Megtekintheti a lista metrikák az automatikus méretezés, hogy a skálázás [Azure Monitor automatikus skálázás gyakori metrikák](insights-autoscale-common-metrics.md).
* Minden küszöbértéke számítása a példány szintjén. Például "horizontális felskálázás egy példánnyal mikor átlagos CPU > 80 %-os, ha a példányok száma 2", azt jelenti, horizontális felskálázás, ha az összes példányra vetítve az átlagos Processzorhasználat 80 %-nál.
* Az összes automatikus skálázási hibák naplózása a tevékenységnaplóban. Konfigurálhatja egy [tevékenységnapló-riasztás](./monitoring-activity-log-alerts.md) így értesítést kaphat e-mailben, SMS vagy webhook, amikor az automatikus skálázás sikertelen.
* Hasonlóképpen az összes sikeres skálázási műveletek feladja a tevékenységnaplóban. Ezután konfigurálhatja a tevékenységnapló-riasztás, így értesítést kaphat e-mailben, SMS vagy webhook, amikor egy sikeres automatikus skálázási művelet. Az automatikus skálázási beállítás értesítések lapján keresztül sikeres skálázási műveletek értesítést kaphat e-mailben vagy webhook értesítések is konfigurálhatja.

## <a name="autoscale-best-practices"></a>Automatikus méretezés ajánlott eljárásairól
Használja az alábbi ajánlott eljárásokat használhatja az automatikus méretezés.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Győződjön meg arról, a maximális és minimális értékek különböző, és ezek között egy megfelelő margóval rendelkeznie
Ha egy beállítás, amely rendelkezik a minimális = 2, maximális = 2, és a jelenlegi példányszám: 2, akkor fordulhat elő, nem skálázási műveletet. Tartsa meg egy megfelelő margó között a maximális és minimális példányok számát, amelyek között lehet. Automatikus skálázási mindig méretezze át, ezek a korlátok között.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Manuális skálázás van állítsa alaphelyzetbe az automatikus skálázás minimális és maximális száma
Ha manuálisan frissíti a példányszám felett vagy alatt a maximális értéket, az automatikus skálázási motor automatikusan alkalmazkodik vissza (ha alább) minimális vagy maximális (Ha a fent). Például állítsa be a 3. és 6 közötti tartományba esik. Ha egy futó példány, az automatikus skálázási motor a következő futtatáskor három példányban méretezhető. Hasonlóan, ha manuálisan állítsa be a méretezési csoport-példányokhoz nyolc, a következő futtatási maximumára skálázza azt vissza a hat példány a következő futásakor.  Manuális skálázás csak átmenetileg létezik, kivéve, ha alaphelyzetbe állítja az automatikus méretezési szabályokat is.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Mindig használja a horizontális felskálázást és a horizontális leskálázási szabály, amely végrehajtja az növelése és csökkentése
Csak egy részét a kombinációt használja, ha az automatikus méretezés csak lépnek művelet (horizontális felskálázás, vagy a) egy irányban mindaddig, amíg eléri a maximális, vagy a minimális távolság megjelenítése megszámolja a profilban megadott. Ez nem optimális, ideális esetben azt szeretné, hogy az erőforrást az vertikális felskálázás a rendelkezésre állás biztosítása érdekében magas kihasználtságú időszakokban. Ehhez hasonlóan időnként, azt szeretné, hogy az erőforrás használatával csökkentheti az alacsony kihasználtságú, így is kiderülhet költséget takaríthat meg.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Válassza ki a megfelelő statisztika a diagnosztikai metrika
Diagnosztikai metrikák, közül választhat *átlagos*, *minimális*, *maximális* és *teljes* mint metrika szerint méretezhető. A leggyakrabban használt érték *átlagos*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Válassza ki a küszöbértékeket, gondosan az összes metrikus típus esetén
Azt javasoljuk, körültekintően a horizontális felskálázást és a horizontális leskálázási gyakorlati helyzetek alapján különböző küszöbértékek kiválasztása.

Hogy *nem ajánlott* automatikus méretezési beállításokkal, mint az azonos vagy nagyon hasonló küszöbértékei horizontális fel- és feltételeket az alábbi példák:

* 1-kal növelni a példányok száma, amikor szálak száma < = 600
* 1 csökkentse a példányok száma, amikor szálak száma > = 600

Lássunk egy példát, hogy mi a probléma, amely először szokatlannak tűnhet majd vezethet. Vegye figyelembe a következő lépéseket.

1. Nincsenek elsőként a két példányt, és majd példányonként szálak átlagos száma folyamatosan nő, hogy a 625 feltételezik.
2. Az automatikus méretezés egy harmadik példány hozzáadásával méretezhető.
3. Ezután azt feltételezik, hogy az átlagos szálak száma több példány 575 alá csökkent.
4. Vertikális leskálázást, mielőtt automatikus skálázási megpróbálja milyen a végállapot becslése lesz, ha méretezi az. Például 575 x 3 (jelenlegi példányszám) = 1,725 / 2 (Ha a vertikálisan leskálázni végleges száma) = 862.5 szálat. Ez azt jelenti, hogy az automatikus méretezés kellene azonnal kibővített újra után azt van ellátva, ha az átlagos szálak száma változatlan marad, vagy csak egy kis ideig még csökken is. Azonban ha azt vertikálisan újra, a teljes folyamat lenne ismételje meg, és a egy végtelen ciklust.
5. Ebben a helyzetben ("flapping" hívják) elkerülése érdekében az automatikus méretezés nem méretezhető egyáltalán. Ehelyett kihagyja, és a feltétel reevaluates legközelebb a szolgáltatás feladatának végrehajtása. Ez félreértésekhez vezethet legtöbben, mert az automatikus méretezés nem úgy tűnik, hogy működnek, ha az átlagos szálak száma 575 volt.

Becslés során egy horizontális leskálázási célja "váltakozó" olyan esetekben, ahol horizontális le- és horizontális felskálázási műveletek folyamatosan oda-vissza go elkerülése érdekében. Őrizze meg ezt a viselkedést, vegye figyelembe, ha úgy dönt, hogy a horizontális felskálázás azonos küszöbértékeinek, és a.

Azt javasoljuk, hogy a kibővített között, és a küszöbértékeket egy megfelelő margó kiválasztása. Tegyük fel fontolja meg a következő jobb szabály kombináció.

* 1-kal növelni a példányok száma, ha processzorhasználatról > = 80
* 1 csökkentse a példányok száma, amikor processzorhasználatról < = 60

Ebben az esetben  

1. Tegyük fel, hogy nincsenek 2 példány a következővel kell kezdődnie.
2. Ha az átlagos CPU-példányán: % kerül a 80-as, automatikus méretezés elvégzi a horizontális felskálázást, egy harmadik példány hozzáadása.
3. Most tegyük fel, hogy idővel a processzorhasználatról 60 tartozik.
4. Az automatikus méretezés a horizontális leskálázási szabály becslése a végső állapotának, mintha a horizontális leskálázási. Ha például 60 x 3 (jelenlegi példányszám) = 180 / 2 (Ha a vertikálisan leskálázni végleges száma) = 90. Így az automatikus méretezés nem horizontális leskálázási mert kibővített azonnal újra kellene. Ehelyett azt kihagyja vertikális leskálázást.
5. Ellenőrzi a következő alkalommal automatikus méretezés, továbbra is 50-re csökken a Processzor. Újra – 50 x 3-as példány becslése = 150 / 2 példányok = 75, amely a 80-as, a horizontális felskálázás küszöbérték alatt van, így méretezhetőségének köszönhetően a sikeresen 2 példány.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Méretezés speciális metrikák küszöbértékei szempontjai
 Speciális mérőszámokat, például a Storage vagy a Service Bus-üzenetsor hossza mérőszám a küszöbérték elérhető példányok jelenlegi száma üzenetek átlagos száma. Alaposan válassza ki a mérőszám a küszöbértéket.

Nézzük mutatnak be, egy példával szemlélteti, hogy biztosan megismerje a jobb viselkedését.

* Példányok növelhető 1 száma szerint, ha a tárolási üzenetsor message count > = 50
* Példányok csökkentése 1 száma szerint, amikor a Storage-üzenetsor üzenetet száma < = 10

Vegye figyelembe a következő lépéseket:

1. Nincsenek tárolási üzenetsor két példánnyal.
2. Üzenetek érkezni, és tekintse át a tárolási üzenetsort, ha a számuk beolvassa az 50. Előfordulhat, hogy azt feltételezik, hogy az automatikus skálázási el kell indulnia a horizontális felskálázási művelet. Azonban fontos megjegyezni, hogy továbbra is 50/2 = példányonként 25 üzeneteket. Tehát horizontális felskálázás nem történik meg. Az első horizontális felskálázás történhet meg, hogy a tárolási üzenetsor teljes üzenet száma 100 kell lennie.
3. Ezután azt feltételezik, hogy az üzenetek teljes száma eléri a 100.
4. A 3. tárolási üzenetsor példány kerül egy horizontális felskálázási művelet miatt.  A következő horizontális felskálázási művelet nem történik meg, amíg a várólista a teljes üzenetek száma eléri a 150, mert 150/3 = 50.
5. Most már az üzenetsorban lévő üzenetek száma kisebb beolvasása. A három példányban, az első horizontális leskálázási művelet történik, ha az összes várólistán található összes üzenet 30-ig adható hozzá, mert 30/3 = a horizontális leskálázási küszöbérték, példányonként 10 üzenetet.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Méretezés, amikor az automatikus skálázási beállítás több profilok szempontjai
Az automatikus skálázási beállítás választhat egy alapértelmezett profilt, amely minden olyan függőséget, ütemezés szerint, vagy idő nélkül mindig érvényes, vagy választhat egy ismétlődő vagy profil egy dátum- és időtartományt a fix időszakra.

Amikor az automatikus skálázási szolgáltatás feldolgozza őket, mindig ellenőrzi a következő sorrendben:

1. Rögzített dátum profil
2. Ismétlődő profil
3. Alapértelmezett profil ("mindig")

Ha egy profil feltétel teljesül, az automatikus méretezés nem ellenőrzi, a következő profil feltétel alá. Automatikus skálázási egyszerre csak egy profil dolgozza fel. Ez azt jelenti, ha szeretne egy alacsonyabb szintű profilból feldolgozási feltétel is tartalmazhatnak, meg kell adnia ezeket a szabályokat, valamint a jelenlegi profilban.

Tekintsük át, ez egy példa:

Az alábbi képen láthatók az automatikus skálázási beállítás minimális példányok alapértelmezett profil a = 2 és a maximális példányok = 10. Ebben a példában szabályok vannak konfigurálva, horizontális felskálázás Ha az a várólistán lévő üzenetek száma meghaladja a 10-es és horizontális leskálázási Ha az a várólistán lévő üzenetek száma kevesebb mint három. Az erőforrás így most már méretezheti két és tíz példányai között.

Emellett nincs egy ismétlődő profil hétfő beállítása. Példányok minimális értéke = 3 és legfeljebb = 10. Ez azt jelenti, hétfőn, az első automatikus skálázási ellenőrzi ezt az állapotot, ha a példányok száma a kettőt, méretezhetőségének köszönhetően a három új minimális. Mindaddig, amíg az automatikus méretezés továbbra is fennáll, ez a profil feltétel található egyező (hétfő), csak feldolgozza a CPU-alapú scale-out/a konfigurált szabályok ehhez a profilhoz. Jelenleg ez nem ellenőrzi az az üzenetsor hossza. Azonban ha azt szeretné ellenőrizni a várólista hossza feltételt is, kell vennie a ezeket a szabályokat, az alapértelmezett profil, valamint a hétfő profiljában.

Hasonlóképpen amikor az automatikus skálázási vált vissza az alapértelmezett profil, először ellenőrzi, ha a minimális és maximális feltételek teljesülnek-e. Ha időben példányok 12, méretezhetőségének köszönhetően a 10., az alapértelmezett profil számára engedélyezett maximális értéket.

![Az automatikus méretezési beállítások](./media/insights-autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Méretezés több szabály profil konfigurálásakor szempontjai
Nincsenek előfordulhat, hogy rendelkezik egy profil több szabályokat állíthat be. Ha több szabályok be vannak állítva a következő automatikus skálázási szabályok készletét használja szolgáltatások használatát.

A *horizontális felskálázása*, automatikus méretezés fut le, ha minden szabály teljesül.
A *horizontális leskálázási*, automatikus méretezés szükséges összes szabályt, amelyeknek teljesülniük kell.

Példa kedvéért tegyük fel, hogy rendelkezik-e a következő négy automatikus skálázási szabályok:

* Ha a CPU < 30 %, a horizontális leskálázási 1
* Ha a memória < 50 %, a horizontális leskálázási 1
* Ha a CPU-> 75 %, horizontális felskálázás 1
* Ha a memória > 75 %, horizontális felskálázás 1

Ezután a következő történik:

* Ha CPU 76 % és memória mérete 50 %-át, hogy horizontális felskálázást.
* Ha CPU 50 %-os és memória-e a 76 % azt horizontális felskálázást.

Másrészről, ha a CPU 25 % pedig memória 51 % automatikus skálázás nem **nem** horizontális leskálázási. Annak érdekében, hogy méretezési – CPU kell lennie 29 % és a memória 49 %.

### <a name="always-select-a-safe-default-instance-count"></a>Mindig jelölje be a biztonságos alapértelmezett példányszám
Az alapértelmezett példányszám fontos az automatikus méretezés, hogy ez a szolgáltatás skálázható, ha a metrikák nem érhetők el. Ezért válasszon egy alapértelmezett példányszámot, amely biztonságos, a számítási feladatokhoz.

### <a name="configure-autoscale-notifications"></a>Automatikus skálázási értesítések konfigurálása
Automatikus skálázási közzéteszi azt a tevékenységnapló, ha az alábbi feltételek bármelyike előfordul:

* Automatikus skálázási kiad egy skálázási művelet
* Automatikus skálázási szolgáltatás sikeresen végrehajtotta a skálázási műveletek
* Automatikus skálázási szolgáltatás meghibásodik, egy skálázási műveletet.
* Automatikus méretezési szolgáltatás a méretezési csoport döntéshozáshoz metrikák nem érhetők el.
* Metrikák elérhető (helyreállítási) ismét a méretezési csoport döntéshozáshoz.

Tevékenységnapló-riasztások segítségével figyelheti az automatikus skálázási motor állapotát. A példa [hozzon létre egy tevékenység Log riasztási az előfizetés összes automatikus skálázási motor műveletek figyelése](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) vagy [hozzon létre egy tevékenység Log riasztási figyelheti az összes sikertelen automatikus vertikális / horizontális felskálázás műveletek a a előfizetés](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

A tevékenységnapló-riasztások használatán sikeres skálázási műveletek automatikus skálázási beállítás értesítések lapján keresztül értesítés küldése e-mailben vagy webhook értesítéseket is konfigurálhatja.

## <a name="next-steps"></a>További lépések
- [Hozzon létre egy tevékenység Log riasztási az előfizetés összes automatikus skálázási motor műveletek figyelése.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Egy tevékenység Log riasztási figyelheti az összes sikertelen automatikus vertikális / horizontális felskálázás műveleteket az előfizetés létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)
