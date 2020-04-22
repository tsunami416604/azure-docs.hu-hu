---
title: Átalakítás kijelölése az adatfolyam leképezésében
description: Azure Data Factory leképezési adatfolyam kiválasztása átalakítás
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/18/2020
ms.openlocfilehash: a90a2def874c7f081f83a34aea956083eb72879a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686495"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Átalakítás kijelölése az adatfolyam leképezésében

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az oszlopok átnevezéséhez, eldobásához vagy átrendezéséhez használja a select transformation parancsot. Ez az átalakítás nem módosítja a soradatokat, de kiválasztja, hogy mely oszlopokat propagálja a későbbi ek. 

A kiválasztott átalakításokban a felhasználók rögzített leképezéseket adhatnak meg, mintákat használhatnak szabályalapú leképezéshez, vagy engedélyezhetik az automatikus leképezést. A rögzített és a szabályalapú leképezések egyaránt használhatók ugyanazon a select transzformáción belül. Ha egy oszlop nem egyezik meg a definiált leképezések egyikével, a program elfogja dobni.

## <a name="fixed-mapping"></a>Rögzített leképezés

Ha a vetületben 50-nél kevesebb oszlop van definiálva, akkor az összes definiált oszlop alapértelmezés szerint rögzített hozzárendeléssel fog rendelkezni. A rögzített leképezés egy meghatározott, bejövő oszlopot vesz fel, és pontos nevet ad hozzá.

![Rögzített leképezés](media/data-flow/fixedmapping.png "Rögzített leképezés")

> [!NOTE]
> Sodródott oszlop nem képezhető le és nem nevezhető át rögzített leképezéssel

### <a name="mapping-hierarchical-columns"></a>Hierarchikus oszlopok leképezése

A rögzített leképezések segítségével egy hierarchikus oszlop aloszlopát egy legfelső szintű oszlophoz lehet hozzárendelve. Ha definiált hierarchiával rendelkezik, az oszlop legördülő menüsegítségével jelöljön ki egy aloszlopot. A kijelölési átalakítás új oszlopot hoz létre az aloszlop értékével és adattípusával.

![hierarchikus leképezés](media/data-flow/select-hierarchy.png "hierarchikus leképezés")

## <a name="rule-based-mapping"></a>Szabályalapú leképezés

Ha egyszerre több oszlopot szeretne leképezni, vagy a sodródott oszlopokat lefelé szeretné átadni, a szabályalapú leképezés segítségével oszlopminták használatával határozhatja meg a leképezéseket. Egyezés `name`a `type` `stream`, `position` , és oszlopok alapján. A rögzített és a szabályalapú leképezések tetszőleges kombinációját használhatja. Alapértelmezés szerint az 50 oszlopnál nagyobb méretű összes vetület alapértelmezés szerint szabályalapú leképezést kap, amely minden oszlopban megfelel, és a bemeneti nevet adja ki. 

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

Ha az oszlop nevében több regex egyezés van, akkor az "n" alatt `$n` hivatkozhat arra, hogy melyik egyezésre utal. A "$2" például az oszlopnévmásodik találatára utal.

### <a name="rule-based-hierarchies"></a>Szabályalapú hierarchiák

Ha a definiált vetület hierarchiával rendelkezik, szabályalapú leképezéssel képezheti le a hierarchiák aloszlopait. Adja meg az egyező feltételt és azt az összetett oszlopot, amelynek aloszlopait le szeretné képezni. A program minden egyező aloszlopot a jobb oldalon megadott "Név mint" szabállyal hoz létre.

![szabályalapú leképezés](media/data-flow/rule-based-hierarchy.png "Szabályalapú leképezés")

A fenti példa az összetett oszlop `a`összes aloszlopának megfelel. `a`két aloszlopot tartalmaz, `b` és `c`a. A kimeneti séma `b` két `c` oszlopot tartalmaz, és `$$`a "Név mint" feltétel .

### <a name="parameterization"></a>Paraméterezése

Az oszlopneveket szabályalapú leképezéssel paraméterezheti. Használja a ```name``` kulcsszót a bejövő oszlopnevek és egy paraméter egyeztetésére. Ha például rendelkezik adatfolyam-paraméterrel, ```mycolumn```létrehozhat egy szabályt, amely megegyezik bármely oszlopnévvel. ```mycolumn``` Az egyező oszlopot átnevezheti egy kódolt karakterláncra, például "üzleti kulcsra", és explicit módon hivatkozhat rá. Ebben a példában az ```name == $mycolumn``` egyeztetési feltétel, és a név feltétel "üzleti kulcs". 

## <a name="auto-mapping"></a>Automatikus hozzárendelés

Kijelölési transzformáció hozzáadásakor az **automatikus leképezés** az Automatikus leképezés csúszka váltásával engedélyezhető. Az automatikus leképezés esetén a kiválasztott átalakítás leképezi az összes bejövő oszlopot, az ismétlődések kivételével, a bevitelükkel megegyező névvel. Ez drifted oszlopokat is tartalmaz, ami azt jelenti, hogy a kimeneti adatok tartalmazhatnak olyan oszlopokat, amelyek nincsenek definiálva a sémában. Az elsodródott oszlopokról további információt [a sémaeltolódás című témakörben talál.](concepts-data-flow-schema-drift.md)

![Automatikus hozzárendelés](media/data-flow/automap.png "Automatikus hozzárendelés")

Ha az automatikus leképezés be van kapcsolva, a kiválasztott átalakítás tiszteletben tartja az ismétlődő beállítások kihagyását, és új aliast biztosít a meglévő oszlopokhoz. Aliasing akkor hasznos, ha több illesztések vagy keres ugyanazon az adatfolyamon, és önillesztési forgatókönyvek. 

## <a name="duplicate-columns"></a>Oszlopok másolása

Alapértelmezés szerint a select transformation csepp ismétlődő oszlopok mind a bemeneti és kimeneti vetület. Az ismétlődő bemeneti oszlopok gyakran illesztési és keresutók átalakításaiból származnak, ahol az illesztés mindkét oldalán megkettőződnek az oszlopnevek. Ismétlődő kimeneti oszlopok akkor fordulhatnak elő, ha két különböző bemeneti oszlopot másol ugyanarra a névre. A jelölőnégyzet beválasztásával megadhatja, hogy eldobja vagy továbbadja az ismétlődő oszlopokat.

![Ismétlődések kihagyása](media/data-flow/select-skip-dup.png "Ismétlődések kihagyása")

## <a name="ordering-of-columns"></a>Oszlopok sorrendje

A leképezések sorrendje határozza meg a kimeneti oszlopok sorrendjét. Ha egy bemeneti oszlop többször van leképezve, csak az első leképezés lesz tiszteletben. Minden ismétlődő oszlop csepegés, az első mérkőzés marad.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Szintaxis

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>Példa

Az alábbi példa egy kiválasztott leképezést és annak adatfolyam-parancsfájlját mutatja be:

![Példa parancsfájl kiválasztása](media/data-flow/select-script-example.png "Példa parancsfájl kiválasztása")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>További lépések
* Miután a Kijelölés használatával átnevezheti, átrendezheti és aliasoszlopokat szeretne átnevezni, átrendezheti és aliasoszlopokat használ, használja a [Fogadó átalakítást](data-flow-sink.md) az adatok adattárba való bekerüléséhez.
