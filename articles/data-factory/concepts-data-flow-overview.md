---
title: Adatfolyamok leképezése
description: Az Azure Data Factory leképezési adatfolyamainak áttekintése
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/19/2019
ms.openlocfilehash: 210c1814325e689dd70af9caa7fad08deed933e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243795"
---
# <a name="what-are-mapping-data-flows"></a>Mik azok a leképezési adatfolyamok?

Az adatfolyamok leképezése vizuálisan tervezett adatátalakítások az Azure Data Factoryban. Az adatfolyamok lehetővé teszik az adatmérnökök számára, hogy kód írása nélkül fejlesszenek ki grafikus adatátalakítási logikát. Az eredményül kapott adatfolyamok az Azure Data Factory-folyamatokon belüli tevékenységekként kerülnek végrehajtásra, amelyek kibővített Spark-fürtöket használnak. Az adatfolyam-tevékenységek a data factory ütemezési, vezérlési, folyamat- és figyelési képességeivel működőképessé tehetők.

Az adatfolyamatok feltérképezése teljes körű vizuális élményt nyújt kódolás nélkül. Az adatfolyamok a saját végrehajtási fürtön fog futni a kibővített adatfeldolgozáshoz. Az Azure Data Factory kezeli az összes kódfordítást, útvonaloptimalizálást és az adatfolyam-feladatok végrehajtását.

## <a name="getting-started"></a>Első lépések

Adatfolyam létrehozásához jelölje ki a pluszjelet a **Gyári erőforrások csoportban,** majd válassza az **Adatfolyam**lehetőséget. 

![Új adatfolyam](media/data-flow/newdataflow2.png "új adatfolyam")

Ezzel az adatfolyam vászon, ahol létrehozhatja az átalakítási logika. Válassza **a Forrás hozzáadása lehetőséget** a forrásátalakítás konfigurálásának megkezdéséhez. További információ: [Source transformation](data-flow-source.md).

## <a name="data-flow-canvas"></a>Adatfolyam-vászon

Az adatfolyam vászon három részre oszlik: a felső sávra, a grafikonra és a konfigurációs panelre. 

![Vászon](media/data-flow/canvas1.png "Vászon")

### <a name="graph"></a>Graph

A diagram megjeleníti az átalakítási adatfolyamot. A forrásadatok vonalát mutatja, ahogy egy vagy több fogadóba áramlanak. Új forrás hozzáadásához válassza a **Forrás hozzáadása**lehetőséget. Új átalakítás hozzáadásához jelölje ki a pluszjelet egy meglévő átalakítás jobb alsó részén.

![Vászon](media/data-flow/canvas2.png "Vászon")

### <a name="azure-integration-runtime-data-flow-properties"></a>Az Azure-integráció staki adatfolyamának tulajdonságai

![Hibakeresés gomb](media/data-flow/debugbutton.png "Hibakeresés gomb")

Amikor elkezddolgozni az adatfolyamokkal az ADF-ben, be kell kapcsolnia a "Debug" kapcsolót az adatfolyamokhoz a böngésző felhasználói felületének tetején. Ez egy Azure Databricks-fürtöt hoz létre az interaktív hibakereséshez, az adatelőnézetekhez és a folyamathiba-hibakeresésekhez. Beállíthatja a használt fürt méretét egy egyéni [Azure-integrációs futásidő](concepts-integration-runtime.md)kiválasztásával. A hibakeresési munkamenet az utolsó adatelőnézet vagy az utolsó hibakeresési folyamat végrehajtása után akár 60 percig is életben marad.

Amikor a folyamatok adatfolyam-tevékenységekkel működőképessé válik, az ADF a "Futtatás" tulajdonságban a [tevékenységhez](control-flow-execute-data-flow-activity.md) társított Azure-integrációs futásidőt fogja használni.

Az alapértelmezett Azure-integrációs futásidejű egy kis négymagos egyfeldolgozós csomópont-fürt, amelynek célja, hogy lehetővé tegye az adatok előnézetét, és gyorsan végrehajthatja a hibakeresési folyamatokat minimális költségek mellett. Nagyobb Azure IR-konfiguráció beállítása, ha nagy adatkészleteken műveleteket hajt végre.

Utasíthatja az ADF-et, hogy fürterőforrások (VM-ek) készletét tartsa fenn az Azure infravörös adatfolyam-tulajdonságaiban egy TTL beállításával. Ez gyorsabb feladatvégrehajtást eredményez a későbbi tevékenységeknél.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Az Azure integrációs futásidejű és adatfolyam-stratégiák

##### <a name="execute-data-flows-in-parallel"></a>Adatfolyamok párhuzamos végrehajtása

Ha párhuzamosan futtat adatfolyamokat egy folyamatban, az ADF minden egyes tevékenység-végrehajtáshoz külön Azure Databricks-fürtöket fog felpörgetni az egyes tevékenységek hez kapcsolódó beállítások alapján. Ha párhuzamos végrehajtásokat szeretne tervezni az ADF-folyamatokban, adja hozzá az adatfolyam-tevékenységeket a felhasználói felületelsőbbségi korlátozások nélkül.

A három lehetőség közül ez a beállítás valószínűleg a legrövidebb idő alatt fog végrehajtani. Azonban minden párhuzamos adatfolyam végrehajtása egyidejűleg külön fürtökön, így az események rendezése nem determinisztikus.

Ha az adatfolyam-tevékenységeket párhuzamosan hajtja végre a folyamatokon belül, ajánlott nem használni a TTL-t. Ennek az az oka, hogy az adatfolyamok párhuzamos végrehajtása imény-integrációs futásidejű egyidejű használatával több melegkészlet-példányt eredményez az adat-előállító számára.

##### <a name="overload-single-data-flow"></a>Egyetlen adatáramlás túlterhelése

Ha az összes logikát egyetlen adatfolyamba helyezi, az ADF minden végrehajtása ugyanabban a feladat-végrehajtási környezetben egyetlen Spark-fürtpéldányon.

Ez a beállítás valószínűleg nehezebb encikó, és hibaelhárítás, mert az üzleti szabályok és az üzleti logika lesz zavaros együtt. Ez a lehetőség nem nyújt sok újrahasználhatóságot.

##### <a name="execute-data-flows-serially"></a>Adatfolyamok sorozatos végrehajtása

Ha az adatfolyam-tevékenységeket sorosan hajtja végre a folyamatban, és beállított egy TTL-t az Azure infravörös konfigurációján, majd az ADF újra felhasználja a számítási erőforrásokat (VM-ek), ami gyorsabb későbbi végrehajtási időt eredményez. Továbbra is kap egy új Spark-környezetminden egyes végrehajtáshoz.

Ebből a három lehetőségből valószínűleg a leghosszabb időt veszi igénybe a végpontok utáni végrehajtás. De ez biztosítja a logikai műveletek tiszta elkülönítése minden adatfolyam lépés.

### <a name="configuration-panel"></a>Konfiguráció panel

A konfigurációpanelen az aktuálisan kiválasztott átalakításra vonatkozó beállítások láthatók. Ha nincs kijelölve átalakítás, akkor az adatfolyam ot jeleníti meg. A teljes adatfolyam-konfigurációban szerkesztheti a nevet és a leírást az **Általános** lapon, vagy paramétereket adhat hozzá a **Paraméterek** lapon. További információt az [Adatfolyam-paraméterek hozzárendelése](parameters-data-flow.md)című témakörben talál.

Minden átalakításnak legalább négy konfigurációs lapja van.

#### <a name="transformation-settings"></a>Átalakítási beállítások

Az egyes átalakítások konfigurációs ablaktáblájának első lapja az adott átalakításra vonatkozó beállításokat tartalmazza. További információkért tekintse meg az átalakítás dokumentációs oldalát.

![Forrásbeállítások lap](media/data-flow/source1.png "Forrásbeállítások lap")

#### <a name="optimize"></a>Optimalizálás

Az **Optimalizálás** lap a particionálási sémák konfigurálásához megadott beállításokat tartalmazza.

![Optimalizálja](media/data-flow/optimize1.png "Optimalizálás")

Az alapértelmezett beállítás az **aktuális particionálás használata**, amely arra utasítja az Azure Data Factory-t, hogy a Sparkon futó adatfolyamok natív particionálási sémát használja. A legtöbb esetben ezt a beállítást javasoljuk.

Vannak olyan esetek, ahol érdemes lehet módosítani a particionálás. Ha például az átalakításokat a tó egyetlen fájljába szeretné kiadni, válassza az **Egyetlen partíció** t a fogadóátalakítása lehetőséget.

Egy másik eset, ahol érdemes lehet a particionálási sémák szabályozása a teljesítmény optimalizálása. A particionálás beállításával szabályozhatja az adatok elosztását a számítási csomópontok és az adathely optimalizálása között, amely pozitív és negatív hatással lehet a teljes adatfolyam-teljesítményre. További információt az [Adatfolyam-teljesítményútmutatóban](concepts-data-flow-performance.md)talál.

Ha módosítani szeretné a particionálást bármely átalakításon, válassza az **Optimalizálás** lapot, és válassza a **Particionálás beállítása** választógombot. Ezután megjelenik egy sor lehetőség a particionáláshoz. A particionálás legjobb módja az adatkötetek, a jelöltkulcsok, a null értékek és a számosság függvénye szerint eltérő lesz. 

Ajánlott eljárás az alapértelmezett particionálás, majd próbálja meg a különböző particionálási beállításokat. Tesztelheti a folyamathiba-keresési futtatások használatával, és megtekintheti a végrehajtási időt és a partíció használatát az egyes átalakítási csoportosítások a figyelési nézetből. További információ: [Monitoring data flows](concepts-data-flow-monitoring.md).

A következő particionálási lehetőségek érhetők el.

##### <a name="round-robin"></a>Ciklikus multiplexelés 

Ciklikus multiplexelés egy egyszerű partíció, amely automatikusan elosztja az adatokat egyenlően partíciók között. Akkor használja a ciklikus multiplexelést, ha nincs enek megfelelő kulcsjelöltegy szilárd, intelligens particionálási stratégia megvalósításához. Beállíthatja a fizikai partíciók számát.

##### <a name="hash"></a>Kivonat

Az Azure Data Factory oszlopok kivonatát hozza létre, hogy egységes partíciókat készítsen, így a hasonló értékű sorok ugyanabban a partícióban esnek. Ha a Kivonatoló beállítást használja, ellenőrizze a lehetséges partíciódöntéseket. Beállíthatja a fizikai partíciók számát.

##### <a name="dynamic-range"></a>Dinamikus tartomány

A dinamikus tartomány a Spark dinamikus tartományait fogja használni a megadott oszlopok vagy kifejezések alapján. Beállíthatja a fizikai partíciók számát. 

##### <a name="fixed-range"></a>Rögzített tartomány

Hozzon létre egy olyan kifejezést, amely rögzített tartományt biztosít a particionált adatoszlopokon belüli értékekhez. A partíciók ferdítésének elkerülése érdekében a beállítás használata előtt jól meg kell értenie az adatokat. A kifejezéshez megadott értékek et a rendszer egy partíciófüggvény részeként fogja használni. Beállíthatja a fizikai partíciók számát.

##### <a name="key"></a>Kulcs

Ha jól ismeri az adatok számosságát, a kulcsparticionálás jó stratégia lehet. A kulcsparticionálás partíciókat hoz létre az oszlop minden egyes egyedi értékéhez. A partíciók száma nem állítható be, mert a szám az adatok egyedi értékein alapul.

#### <a name="inspect"></a>Ellenőrizni

A **Vizsgálat** lapon az átalakuló adatfolyam metaadataiból láthatók. Megtekintheti az oszlopok számát, a módosított oszlopokat, a hozzáadott oszlopokat, az adattípusokat, az oszlopok rendezését és az oszlophivatkozásokat. **A vizsgálat** a metaadatok írásvédett nézete. A Metaadatok **ellenőrzés** ablaktáblán való megtekintéséhez nincs szükség hibakeresési módra.

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
