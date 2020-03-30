---
title: Oszlopminták az Azure Data Factory leképezési adatfolyamában
description: Általános adatátalakítási minták létrehozása oszlopminták használatával az Azure Data Factory leképezési adatfolyamaiban
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: bfb4eeef482c4944e75b7805642bc93c23195208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065524"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Oszlopminták használata az adatfolyam leképezésében

Számos leképezési adatfolyam-átalakítás lehetővé teszi, hogy a sablonoszlopokra a minták alapján hivatkozzon a kódolt oszlopnevek helyett. Ezt az egyezést *oszlopmintáknak nevezzük.* A pontos mezőnevek megkövetelése helyett mintákat is definiálhat az oszlopok nak a név, adattípus, adatfolyam vagy pozíció alapján. Két olyan forgatókönyv létezik, ahol az oszlopminták hasznosak:

* Ha a bejövő forrásmezők gyakran változnak, például a szövegfájlok vagy a NoSQL adatbázisok oszlopainak módosítása. Ezt a forgatókönyvet [sémaeltolódásnak nevezzük.](concepts-data-flow-schema-drift.md)
* Ha egy általános műveletet szeretne végezni egy nagy oszlopcsoporton. Ha például minden olyan oszlopot kettős be dobásra szeretne vetni, amelynek oszlopnevében "összes" van.

Az oszlopminták jelenleg a származtatott oszlopban, összesítésben, kijelölésben és fogadóban érhetők el.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Oszlopminták a származtatott oszlopban és összesítésben

Ha egy származtatott oszlopban vagy egy összesítés összesítése lapján oszlopmintát szeretne hozzáadni, kattintson a meglévő oszlop jobb oldalán található plusz ikonra. Válassza **az Oszlopminta hozzáadása lehetőséget.** 

![oszlopminták](media/data-flow/columnpattern.png "Oszlopminták")

Használja a [kifejezésszerkesztőt](concepts-data-flow-expression-builder.md) az egyezési feltétel megadásához. Hozzon létre egy logikai kifejezést, `stream`amely `position` megfelel az oszlopoknak a `name`, `type`, és az oszlop alapján. A minta hatással lesz minden oszlop, sodródott vagy definiált, ahol a feltétel igaz értéket ad vissza.

Az egyezési feltétel alatti két kifejezésmező az érintett oszlopok új nevét és értékeit adja meg. Az `$$` egyező mező meglévő értékére való hivatkozás. A bal oldali kifejezés mező határozza meg a nevet, a jobb oldali kifejezésmező pedig az értéket.

![oszlopminták](media/data-flow/columnpattern2.png "Oszlopminták")

A fenti oszlopminta minden azonos típusú oszlopnak felel meg, és egyezésenként egy összesítő oszlopot hoz létre. Az új oszlop neve az egyező oszlop neve, amely "_total" -val van összefűzve. Az új oszlop értéke a meglévő kettős érték kerekített, összesített összege.

Az egyeztetési feltétel helyességének ellenőrzéséhez érvényesítheti a meghatározott oszlopok kimeneti sémáját a **Vizsgálat** lapon, vagy pillanatképet kaphat az adatokról az **Adatok előnézete** lapon. 

![oszlopminták](media/data-flow/columnpattern3.png "Oszlopminták")

## <a name="rule-based-mapping-in-select-and-sink"></a>Szabályalapú leképezés a kijelölésben és a fogadóban

Amikor oszlopokat térképez le a forrásban, és átalakításokat választ ki, rögzített leképezést vagy szabályalapú leképezéseket adhat hozzá. Egyezés `name`a `type` `stream`, `position` , és oszlopok alapján. A rögzített és a szabályalapú leképezések tetszőleges kombinációját használhatja. Alapértelmezés szerint az 50 oszlopnál nagyobb méretű összes vetület alapértelmezés szerint szabályalapú leképezést kap, amely minden oszlopban megfelel, és a bemeneti nevet adja ki. 

Szabályalapú leképezés hozzáadásához kattintson a **Leképezés hozzáadása** gombra, és válassza a **Szabályalapú leképezés**lehetőséget.

![szabályalapú leképezés](media/data-flow/rule2.png "Szabályalapú leképezés")

Minden szabályalapú leképezéshez két bemenet szükséges: az a feltétel, amelyalapján egyeztetni kell, és hogy mi az egyes leképezett oszlopok elnevezése. Mindkét értéket a [kifejezésszerkesztő](concepts-data-flow-expression-builder.md)adja meg. A bal oldali kifejezésmezőbe írja be a logikai egyezési feltételt. A jobb kifejezésmezőben adja meg, hogy az egyező oszlop hoz mihez lesz leképezve.

![szabályalapú leképezés](media/data-flow/rule-based-mapping.png "Szabályalapú leképezés")

A `$$` szintaxissal hivatkozzon egy egyező oszlop bemeneti nevére. A fenti képet példaként használva tegyük fel, hogy a felhasználó minden olyan karakterláncoszlopot meg szeretne egyeztetni, amelynek neve hat karakternél rövidebb. Ha egy bejövő oszlop `test`ot `$$ + '_short'` neveztek el, `test_short`a kifejezés átnevezi az oszlopot . Ha ez az egyetlen létező leképezés, akkor a feltételnek nem megfelelő összes oszlop el lesz hagyva a kimenetelen adatokból.

A minták egyaránt illeszkednek az elsodródott és a definiált oszlopokhoz. Ha meg szeretné tekinteni, hogy mely definiált oszlopokat képezi le egy szabály, kattintson a szabály melletti szemüveg ikonra. Ellenőrizze a kimenetet az adatok előnézetével.

### <a name="regex-mapping"></a>Regex feltérképezése

Ha a lefelé mutató sávnyíl ikonra kattint, megadhat egy regex-leképezési feltételt. A regex-leképezési feltétel megfelel a megadott regex feltételnek megfelelő összes oszlopnévnek. Ez szabványos szabályalapú hozzárendelésekkel kombinálva használható.

![szabályalapú leképezés](media/data-flow/regex-matching.png "Szabályalapú leképezés")

A fenti példa megegyezik `(r)` a regex mintával vagy bármely oszlopnévvel, amely kisbetűs r-t tartalmaz. A normál szabályalapú leképezéshez hasonlóan az összes egyező `$$` oszlopot a jobb oldali feltétel módosítja a szintaxis használatával.

### <a name="rule-based-hierarchies"></a>Szabályalapú hierarchiák

Ha a definiált vetület hierarchiával rendelkezik, szabályalapú leképezéssel képezheti le a hierarchiák aloszlopait. Adja meg az egyező feltételt és azt az összetett oszlopot, amelynek aloszlopait le szeretné képezni. A program minden egyező aloszlopot a jobb oldalon megadott "Név mint" szabállyal hoz létre.

![szabályalapú leképezés](media/data-flow/rule-based-hierarchy.png "Szabályalapú leképezés")

A fenti példa az összetett oszlop `a`összes aloszlopának megfelel. `a`két aloszlopot tartalmaz, `b` és `c`a. A kimeneti séma `b` két `c` oszlopot tartalmaz, és `$$`a "Név mint" feltétel .

## <a name="pattern-matching-expression-values"></a>Mintázategyező kifejezésértékek.

* `$$`lefordítja az egyes mérkőzések nevét vagy értékét futási időben
* `name`az egyes bejövő oszlopok nevét jelöli
* `type`az egyes bejövő oszlopok adattípusát jelöli
* `stream`az egyes adatfolyamok nevének, illetve a folyamat átalakításának
* `position`az oszlopok sorpozíciója az adatfolyamban

## <a name="next-steps"></a>További lépések
* További információ az adatátalakítások leképezési [adatfolyam-kifejezésnyelvéről](data-flow-expression-functions.md)
* Oszlopminták használata a [fogadó transzformációjában,](data-flow-sink.md) és [az átalakítás kiválasztása](data-flow-select.md) szabályalapú leképezéssel
