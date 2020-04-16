---
title: Adatfolyamok leképezése
description: Az Azure Data Factory leképezési adatfolyamainak áttekintése
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/28/2020
ms.openlocfilehash: 39d1f15b771168b618bfbc4951f2036a8b95b027
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418456"
---
# <a name="what-are-mapping-data-flows"></a>Mik azok a leképezési adatfolyamok?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az adatfolyamok leképezése vizuálisan tervezett adatátalakítások az Azure Data Factoryban. Az adatfolyamok lehetővé teszik az adatmérnökök számára, hogy kód írása nélkül fejlesszenek ki grafikus adatátalakítási logikát. Az eredményül kapott adatfolyamok az Azure Data Factory-folyamatokon belüli tevékenységekként kerülnek végrehajtásra, amelyek kibővített Apache Spark-fürtöket használnak. Az adatfolyam-tevékenységek a data factory ütemezési, vezérlési, folyamat- és figyelési képességeivel is bekapcsolhatók.

Az adatfolyamok feltérképezése teljes mértékben vizuális élményt nyújt kódolás nélkül. Az adatfolyamok a végrehajtási fürtön futnak a kibővített adatfeldolgozáshoz. Az Azure Data Factory kezeli az összes kódfordítást, útvonaloptimalizálást és az adatfolyam-feladatok végrehajtását.

## <a name="getting-started"></a>Első lépések

Adatfolyam létrehozásához jelölje ki a pluszjelet a **Gyári erőforrások csoportban,** majd válassza az **Adatfolyam**lehetőséget. 

![Új adatfolyam](media/data-flow/newdataflow2.png "új adatfolyam")

Ez a művelet az adatfolyam vászonra visz, ahol létrehozhatja az átalakítási logikát. Válassza **a Forrás hozzáadása lehetőséget** a forrásátalakítás konfigurálásának megkezdéséhez. További információ: [Source transformation](data-flow-source.md).

## <a name="data-flow-canvas"></a>Adatfolyam-vászon

Az adatfolyam vászon három részre oszlik: a felső sávra, a grafikonra és a konfigurációs panelre. 

![Vászon](media/data-flow/canvas1.png "Vászon")

### <a name="graph"></a>Graph

A diagram megjeleníti az átalakítási adatfolyamot. A forrásadatok vonalát mutatja, ahogy egy vagy több fogadóba áramlanak. Új forrás hozzáadásához válassza a **Forrás hozzáadása**lehetőséget. Új átalakítás hozzáadásához jelölje ki a pluszjelet egy meglévő átalakítás jobb alsó részén.

![Vászon](media/data-flow/canvas2.png "Vászon")

### <a name="azure-integration-runtime-data-flow-properties"></a>Az Azure-integráció staki adatfolyamának tulajdonságai

![Hibakeresés gomb](media/data-flow/debugbutton.png "Hibakeresés gomb")

Amikor elkezdi az adatfolyamokkal való munkát az ADF-ben, be szeretné kapcsolni a "Debug" kapcsolót az adatfolyamokhoz a böngésző felhasználói felületének tetején. Ez megpörgeti a Spark-fürt öt es és a folyamat hibakeresési végrehajtása. Beállíthatja a használt fürt méretét egy egyéni [Azure-integrációs futásidő](concepts-integration-runtime.md)kiválasztásával. A hibakeresési munkamenet az utolsó adatelőnézet vagy az utolsó hibakeresési folyamat végrehajtása után akár 60 percig is életben marad.

Amikor a folyamatok at adatfolyam-tevékenységekkel működőképessé teszi, az ADF a "Futtatás" tulajdonságban a [tevékenységhez](control-flow-execute-data-flow-activity.md) társított Azure-integrációs futásidőt használja.

Az alapértelmezett Azure-integrációs futásidejű egy kis négymagos egyfeldolgozós csomópont-fürt, amely lehetővé teszi az adatok előnézetének megtekintését és a hibakeresési folyamatok minimális költségek mellett történő gyors végrehajtását. Nagyobb Azure IR-konfiguráció beállítása, ha nagy adatkészleteken műveleteket hajt végre.

Utasíthatja az ADF-et, hogy fürterőforrások (VM-ek) készletét tartsa fenn az Azure infravörös adatfolyam-tulajdonságaiban egy TTL beállításával. Ez a művelet gyorsabb feladatvégrehajtást eredményez a későbbi tevékenységeknél.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Az Azure integrációs futásidejű és adatfolyam-stratégiák

##### <a name="execute-data-flows-in-parallel"></a>Adatfolyamok párhuzamos végrehajtása

Ha párhuzamosan hajtja végre az adatfolyamokat egy folyamatban, az ADF minden egyes tevékenység-végrehajtáshoz külön Spark-fürtöket hoz létre az egyes tevékenységekhez kapcsolódó Azure-integrációs futásidő beállításai alapján. Ha párhuzamos végrehajtásokat szeretne tervezni az ADF-folyamatokban, adja hozzá az adatfolyam-tevékenységeket a felhasználói felületelsőbbségi korlátozások nélkül.

A három lehetőség közül ez a beállítás valószínűleg a legrövidebb idő alatt hajtható végre. Azonban minden párhuzamos adatfolyam végrehajtása egy időben külön fürtökön, így az események rendezése nem determinisztikus.

Ha az adatfolyam-tevékenységeket párhuzamosan hajtja végre a folyamatokon belül, ajánlott nem használni a TTL-t. Ez a művelet azért van, mert az adatfolyam párhuzamos végrehajtása imperében, ugyanazon Azure-integrációs futásidejű használatával több melegkészlet-példányt eredményez az adat-előállító számára.

##### <a name="overload-single-data-flow"></a>Egyetlen adatáramlás túlterhelése

Ha az összes logikát egyetlen adatfolyamba helyezi, az ADF ugyanazt a feladat-végrehajtási környezetet hajtja végre egyetlen Spark-fürtpéldányon.

Ez a beállítás nagyobb kihívást jelenthet a követés és a hibaelhárítás, mivel az üzleti szabályok és az üzleti logika összezagyhatók. Ez a lehetőség nem nyújt sok újrafelhasználhatóságot.

##### <a name="execute-data-flows-serially"></a>Adatfolyamok sorozatos végrehajtása

Ha az adatfolyam-tevékenységeket sorosan hajtja végre a folyamatban, és beállított egy TTL-t az Azure infravörös konfigurációján, majd az ADF újrafelhasználja a számítási erőforrásokat (VM-ek), ami gyorsabb későbbi végrehajtási időt eredményez. Továbbra is kap egy új Spark-környezetminden végrehajtáshoz.

A három lehetőség közül ez a művelet valószínűleg a leghosszabb időt veszi igénybe a végpontok utáni végrehajtáshoz. De ez biztosítja a logikai műveletek tiszta elkülönítése minden adatfolyam lépés.

### <a name="configuration-panel"></a>Konfiguráció panel

A konfigurációpanelen az aktuálisan kiválasztott átalakításra vonatkozó beállítások láthatók. Ha nincs kijelölve átalakítás, akkor az adatfolyam ot jeleníti meg. A teljes adatfolyam-konfigurációban szerkesztheti a nevet és a leírást az **Általános** lapon, vagy paramétereket adhat hozzá a **Paraméterek** lapon. További információt az [Adatfolyam-paraméterek hozzárendelése](parameters-data-flow.md)című témakörben talál.

Minden átalakítás legalább négy konfigurációs lapot tartalmaz.

#### <a name="transformation-settings"></a>Átalakítási beállítások

Az egyes átalakítások konfigurációs ablaktáblájának első lapja az adott átalakításra vonatkozó beállításokat tartalmazza. További információkért tekintse meg az átalakítás dokumentációs oldalát.

![Forrásbeállítások lap](media/data-flow/source1.png "Forrásbeállítások lap")

#### <a name="optimize"></a>Optimalizálás

Az **Optimalizálás** lap a particionálási sémák konfigurálásához megadott beállításokat tartalmazza.

![Optimalizálás](media/data-flow/optimize1.png "Optimalizálás")

Az alapértelmezett beállítás az **aktuális particionálás használata**, amely arra utasítja az Azure Data Factory-t, hogy a Sparkon futó adatfolyamok natív particionálási sémát használja. A legtöbb esetben ezt a beállítást javasoljuk.

Vannak olyan esetek, ahol érdemes lehet módosítani a particionálás. Ha például az átalakításokat a tó egyetlen fájljába szeretné kiadni, válassza az **Egyetlen partíció** t a fogadóátalakítása lehetőséget.

Egy másik eset, ahol érdemes lehet a particionálási sémák szabályozása a teljesítmény optimalizálása. A particionálás beállításával szabályozhatja az adatok elosztását a számítási csomópontok és az adathely optimalizálása között, amely pozitív és negatív hatással lehet a teljes adatfolyam-teljesítményre. További információt az [Adatfolyam-teljesítményútmutatóban](concepts-data-flow-performance.md)talál.

Ha módosítani szeretné a particionálást bármely átalakításon, válassza az **Optimalizálás** lapot, és válassza a **Particionálás beállítása** választógombot. A particionálási lehetőségek sorozata jelenik meg. A particionálás legjobb módja az adatkötetektől, a jelöltkulcsoktól, a null értékektől és a számosságtól függően változik. 

Ajánlott eljárás az alapértelmezett particionálás, majd próbálja meg a különböző particionálási beállításokat. Tesztelheti a folyamathiba-keresési futtatások használatával, és megtekintheti a végrehajtási időt és a partíció használatát az egyes átalakítási csoportosítások a figyelési nézetből. További információ: [Monitoring data flows](concepts-data-flow-monitoring.md).

A következő particionálási lehetőségek érhetők el.

##### <a name="round-robin"></a>Ciklikus multiplexelés 

Ciklikus multiplexelés egy egyszerű partíció, amely automatikusan elosztja az adatokat egyenlően partíciók között. Használja ciklikus multiplexelés, ha nem rendelkezik jó kulcsjelöltek egy szilárd, intelligens particionálási stratégia megvalósításához. Beállíthatja a fizikai partíciók számát.

##### <a name="hash"></a>Kivonat

Az Azure Data Factory oszlopok kivonatát hozza létre egységes partíciók létrehozásához, így a hasonló értékű sorok ugyanabba a partícióba esnek. Ha a Kivonatoló beállítást használja, ellenőrizze a lehetséges partíciódöntéseket. Beállíthatja a fizikai partíciók számát.

##### <a name="dynamic-range"></a>Dinamikus tartomány

A dinamikus tartomány a Spark dinamikus tartományait használja a megadott oszlopok vagy kifejezések alapján. Beállíthatja a fizikai partíciók számát. 

##### <a name="fixed-range"></a>Rögzített tartomány

Hozzon létre egy olyan kifejezést, amely rögzített tartományt biztosít a particionált adatoszlopokon belüli értékekhez. A partíciók ferdítésének elkerülése érdekében a beállítás használata előtt jól meg kell értenie az adatokat. A kifejezéshez megadott értékek egy partíciófüggvény részeként használatosak. Beállíthatja a fizikai partíciók számát.

##### <a name="key"></a>Kulcs

Ha jól ismeri az adatok számosságát, a kulcsparticionálás jó stratégia lehet. A kulcsparticionálás partíciókat hoz létre az oszlop minden egyes egyedi értékéhez. A partíciók száma nem állítható be, mert a szám az adatok egyedi értékein alapul.

#### <a name="inspect"></a>Ellenőrizni

A **Vizsgálat** lapon az átalakuló adatfolyam metaadataiból láthatók. Megtekintheti az oszlopok számát, az oszlopok at, a hozzáadott oszlopokat, az adattípusokat, az oszlopsorrendet és az oszlophivatkozásokat. **A vizsgálat** a metaadatok írásvédett nézete. A Metaadatok **ellenőrzés** ablaktáblán való megtekintéséhez nincs szükség hibakeresési módra.

![Ellenőrizni](media/data-flow/inspect1.png "Ellenőrizni")

Ahogy az átalakítások során módosítja az adatok alakját, a Metaadat-változások folyamata megjelenik a **Vizsgálat** ablaktáblán. Ha nincs definiált séma a forrásátalakításban, akkor a metaadatok nem lesznek **láthatók** a Vizsgálat ablaktáblán. A metaadatok hiánya gyakori a sémaeltolódási forgatókönyvekben.

#### <a name="data-preview"></a>Adatelőnézet

Ha a hibakeresési mód be van kapcsolva, az **Adatelőnézet** lap interaktív pillanatképet ad az egyes átalakítások adatairól. További információt az [Adatelőnézet hibakeresési módban című témakörben](concepts-data-flow-debug-mode.md#data-preview)talál.

### <a name="top-bar"></a>Felső sáv

A felső sáv olyan műveleteket tartalmaz, amelyek befolyásolják a teljes adatfolyamot, például a mentést és az ellenőrzést. A diagram és a konfigurációs módok között is válthat a **Diagram megjelenítése** és a **Diagram elrejtése** gombbal.

![Diagram elrejtése](media/data-flow/hideg.png "Diagram elrejtése")

Ha elrejti a grafikont, az átalakítási csomópontok között oldalirányban böngészhet az **Előző** és a **Következő** gombon keresztül.

![Előző és következő gombok](media/data-flow/showhide.png "előző és következő gombok")

## <a name="next-steps"></a>További lépések

* További információ a [forrásátalakítás létrehozásáról.](data-flow-source.md)
* További információ az adatfolyamok [hibakeresési módban történő létrehozásáról.](concepts-data-flow-debug-mode.md)
