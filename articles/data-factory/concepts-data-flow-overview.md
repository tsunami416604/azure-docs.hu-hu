---
title: Adatfolyamatok leképezése Azure Data Factoryban | Microsoft Docs
description: Az adatfolyamatok Azure Data Factoryban való leképezésének áttekintése
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: 37fec388acda78f3d13c8e85ddddf780ad099d69
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387997"
---
# <a name="what-are-mapping-data-flows"></a>Mik azok a leképezési adatfolyamok?

Az adatfolyamatok leképezése vizuálisan tervezett adatátalakítások Azure Data Factoryban. Az adatforgalom lehetővé teszi, hogy az adatmérnökök programkód írása nélkül fejlesszenek grafikus Adatátalakítási logikát. Az eredményül kapott adatfolyamatok a kibővített Spark-fürtöket használó Azure Data Factory folyamatokon belüli tevékenységként lesznek végrehajtva. Az adatfolyam-tevékenységek a meglévő Data Factory ütemezési, vezérlési, folyamat-és figyelési képességein keresztül is működőképesek.

Az adatforgalom leképezése teljes körű vizuális élményt biztosít, és nem igényel kódolást. Az adatfolyamatok a saját végrehajtási fürtön lesznek végrehajtva a kibővített adatfeldolgozáshoz. Azure Data Factory kezeli az összes kód fordítását, az elérési út optimalizálását és az adatfolyam-feladatok végrehajtását.

## <a name="getting-started"></a>Bevezetés

Az adatfolyamatok létrehozásához kattintson a gyári erőforrások területen a plusz jelre. 

![új adatfolyam](media/data-flow/newdataflow2.png "új adatfolyam")

Ekkor megjelenik az adatáramlási vászon, ahol létrehozhatja az átalakítási logikát. A forrás-átalakítás konfigurálásának megkezdéséhez kattintson a "forrás hozzáadása" mezőre. További információ: forrás- [átalakítás](data-flow-source.md).

## <a name="data-flow-canvas"></a>Adatfolyam-vászon

Az adatfolyam-vászon három részből áll: a felső sáv, a gráf és a konfigurációs panel. 

![Vászon](media/data-flow/canvas1.png "Vászon")

### <a name="graph"></a>Gráf

A gráf megjeleníti az átalakítási adatfolyamot. Megjeleníti a forrásadatok vonalát, mivel az egy vagy több mosogatóba áramlik. Új forrás hozzáadásához kattintson a "forrás hozzáadása" mezőre. Új átalakítás hozzáadásához kattintson a meglévő átalakítás jobb alsó sarkában található pluszjelre.

![Vászon](media/data-flow/canvas2.png "Vászon")

### <a name="configuration-panel"></a>Konfigurációs panel

A konfigurációs panel megjeleníti az aktuálisan kiválasztott átalakításra vonatkozó beállításokat, vagy ha nincs kiválasztva átalakítás, az adatfolyam. A teljes adatfolyam-konfigurációban szerkesztheti a nevet és a leírást az **általános** lapon, vagy hozzáadhat paramétereket a parameters ( **Paraméterek** ) lapon. További információ: [az adatfolyam paramétereinek leképezése](parameters-data-flow.md).

Minden átalakításhoz legalább négy konfigurációs lap tartozik:

#### <a name="transformation-settings"></a>Átalakítási beállítások

Az egyes átalakítások konfigurációs paneljének első lapja az adott átalakításra vonatkozó beállításokat tartalmazza. További információkért tekintse meg az átalakítás dokumentációs oldalát.

![Forrás beállításai lap](media/data-flow/source1.png "Forrás beállításai lap")

#### <a name="optimize"></a>Optimalizálás

Az _optimalizálás_ lap a particionálási sémák konfigurálásához szükséges beállításokat tartalmazza.

![Optimalizálás](media/data-flow/optimize1.png "Optimalizálás")

Az alapértelmezett beállítás az "aktuális particionálás használata", amely arra utasítja a Azure Data Factoryot, hogy a meglevő particionálási séma a Sparkon futó adatfolyamatoknál natív legyen. A legtöbb esetben ez a beállítás az ajánlott módszer.

Vannak olyan példányok, amelyekben érdemes lehet módosítani a particionálást. Ha például egyetlen fájlba szeretné exportálni az átalakításokat a-tóban, válassza az "egyetlen partíció" lehetőséget egy fogadó transzformációban.

Egy másik esetben, ha a particionálási sémákat szeretné vezérelni, optimalizálja a teljesítményt. A particionálás beállítása lehetővé teszi az adatok elosztását a számítási csomópontok és az adatkörnyezet-optimalizálások között, amelyek mind pozitív, mind negatív hatással lehetnek a teljes adatfolyam-teljesítményre. További információ: az [adatfolyam teljesítményének útmutatója](concepts-data-flow-performance.md).

Ha módosítani szeretné a particionálást bármely transzformáción, kattintson az optimalizálás fülre, és válassza a "particionálás beállítása" választógombot. Ekkor megjelenik egy sor particionálási lehetőséggel. A particionálás legjobb módszere az adatmennyiségek, a jelölt kulcsok, a null értékek és a kardinális alapján eltérő lesz. Az ajánlott eljárás az alapértelmezett particionálás, majd a különböző particionálási beállítások kipróbálása. A figyelés nézetből tesztelheti a folyamat hibakeresési futtatását, és megtekintheti a végrehajtás idejét és a partíciók használatát az egyes átalakítási csoportosításokban. További információ: [az adatfolyamatok figyelése](concepts-data-flow-monitoring.md).

A rendelkezésre álló particionálási beállítások alább láthatók.

##### <a name="round-robin"></a>Ciklikus időszeletelés 

A ciklikus multiplexelés olyan egyszerű partíció, amely automatikusan elosztja az adategységeket a partíciók között. A ciklikus időszeletelést akkor érdemes használni, ha nem rendelkezik a megfelelő kulcsokkal a szilárd, intelligens particionálási stratégia megvalósításához. Megadhatja a fizikai partíciók számát.

##### <a name="hash"></a>Kivonat

Azure Data Factory az oszlopok kivonatát fogja létrehozni, hogy egységes partíciókat hozzon létre, így a hasonló értékekkel rendelkező sorok ugyanahhoz a partícióhoz fognak esni. A kivonatoló beállítás használatakor tesztelje a lehetséges partíciók döntését. Megadhatja a fizikai partíciók számát.

##### <a name="dynamic-range"></a>Dinamikus tartomány

A dinamikus tartomány a megadott oszlopok vagy kifejezések alapján a Spark dinamikus tartományokat fogja használni. Megadhatja a fizikai partíciók számát. 

##### <a name="fixed-range"></a>Rögzített tartomány

Hozzon létre egy olyan kifejezést, amely rögzített tartományt biztosít a particionált adatoszlopokban lévő értékek számára. Ennek a lehetőségnek a használata előtt érdemes megismernie az adatait, hogy elkerülje a partíciók eldöntését. A kifejezéshez megadott értékek a Partition függvény részeként lesznek felhasználva. Megadhatja a fizikai partíciók számát.

##### <a name="key"></a>Jelmagyarázat

Ha jól ismeri az Ön adatait, a fő particionálás lehet jó partíciós stratégia. A kulcsok particionálásakor a rendszer létrehozza a partíciókat az oszlop minden egyedi értékéhez. A partíciók száma nem állítható be, mert a szám az adatok egyedi értékein alapul.

#### <a name="inspect"></a>Vizsgálata

Az _ellenőrzés_ lapon megtekintheti az átalakítás alatt álló adatfolyam metaadatait. Láthatja az oszlopok számát, a megváltoztatott oszlopokat, a hozzáadott oszlopokat, az adattípusokat, az oszlopok sorrendjét és az oszlopok hivatkozásait. A vizsgálat a metaadatok csak olvasható nézete. Nem kell engedélyezni a hibakeresési módot a metaadatok megjelenítéséhez a vizsgálat ablaktáblán.

![Vizsgálata](media/data-flow/inspect1.png "Vizsgálata")

Amikor átalakításokon keresztül módosítja az adatok alakját, a metaadatok változásai a vizsgálat panelen jelennek meg. Ha nincs definiált séma a forrás-átalakításban, akkor a metaadatok nem lesznek láthatók a vizsgálat ablaktáblán. A metaadatok hiánya gyakori a séma-drift forgatókönyvekben.

#### <a name="data-preview"></a>Adatelőnézet

Ha a hibakeresési mód be van kapcsolva, az _adatelőnézet_ lap interaktív pillanatképet biztosít az adatokról az egyes transzformációk során. További információ: az [adatelőnézet hibakeresési módban](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Felső sáv

A felső sáv olyan műveleteket tartalmaz, amelyek befolyásolják a teljes adatfolyamot, például a mentést és az érvényesítést. A Graph és a konfigurációs üzemmód közötti váltás a **Graph megjelenítése** és a Graph billentyűk **elrejtése** gomb használatával is lehetséges.

![Gráf elrejtése](media/data-flow/hideg.png "Gráf elrejtése")

Ha elrejti a gráfot, később az **előző** és a **következő** gombokon keresztül navigálhat az átalakítási csomópontokon.

![Lépjen](media/data-flow/showhide.png "Lépjen")

## <a name="next-steps"></a>Következő lépések

* Útmutató a [forrás-átalakítás](data-flow-source.md) létrehozásához
* Ismerje meg, hogyan hozhat létre adatfolyamatokat [hibakeresési módban](concepts-data-flow-debug-mode.md)
