---
title: Oszlopok mintái Azure Data Factory leképezési adatfolyamban
description: Általánosított Adatátalakítási minták létrehozása Azure Data Factory leképezési adatforgalmához tartozó oszlopos minták használatával
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 0c9a3c2ef05f4a11933ca7fc81c7c0f87a612293
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395884"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Oszlopok mintáinak használata a leképezési adatfolyamban

Számos leképezési adatfolyam-átalakítás lehetővé teszi a sablon oszlopainak a rögzített oszlopnevek helyett a mintázatok alapján történő hivatkozását. Ezt a megfeleltetést *oszlopbeli mintázatoknak*nevezzük. Megadhat mintázatokat az oszlopoknak a név, az adattípus, a stream vagy a pozíció alapján történő egyeztetéséhez a mezők nevének megadása helyett. Az oszlopok mintája két esetben hasznos:

* Ha a bejövő forrás mezői gyakran változnak, például a szövegfájlokban vagy a NoSQL-adatbázisokban lévő oszlopok módosításának esetére. Ez a forgatókönyv a [séma drift](concepts-data-flow-schema-drift.md)néven ismert.
* Ha egy gyakori műveletet szeretne végezni az oszlopok nagy csoportjára. Például minden olyan oszlopot, amely "Total" értékkel rendelkezik az oszlop nevében, dupla értékre kívánja feldolgozni.

Az oszlopok mintázata jelenleg a származtatott oszlop, az összesítés, a kiválasztás és a fogadó átalakításban érhető el.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Oszlopos minták származtatott oszlopban és összesítésben

Ha egy származtatott oszlopban vagy egy összesített átalakítás összesítések lapján szeretne oszlop mintát felvenni, kattintson a plusz ikonra a meglévő oszlop jobb oldalán. Válassza az **oszlop hozzáadása minta**lehetőséget. 

![oszlop mintázatai](media/data-flow/columnpattern.png "Oszlopminták")

A [Kifejezésszerkesztő](concepts-data-flow-expression-builder.md) segítségével adja meg a egyezési feltételt. Hozzon létre egy logikai kifejezést, amely az oszlop `name`, `type`, `stream`és `position` alapján megfelel az oszlopoknak. A minta minden olyan oszlopot érint, amely sodródik vagy definiálva lesz, ahol a feltétel igaz értéket ad vissza.

Az egyeztetési feltétel alá tartozó két kifejezés mező adja meg az érintett oszlopok új neveit és értékeit. Az egyező mező meglévő értékének megkereséséhez használja a `$$`. A bal oldali kifejezés mezőben a név és a jobb oldali kifejezés mező határozza meg az értéket.

![oszlop mintázatai](media/data-flow/columnpattern2.png "Oszlopminták")

A fenti oszlop mintája minden dupla típusú oszlopra illeszkedik, és egy összesített oszlopot hoz létre egymás után. Az új oszlop neve az egyező oszlop neve az "_total" értékkel összefűzve. Az új oszlop értéke a meglévő dupla érték lekerekített, összesített összege.

Az egyeztetési feltétel helyességének ellenőrzéséhez ellenőrizheti a **megvizsgálandó** lapon definiált oszlopok kimeneti sémáját, vagy beolvashatja az adatok pillanatképét az **adatelőnézet** lapon. 

![oszlop mintázatai](media/data-flow/columnpattern3.png "Oszlopminták")

## <a name="rule-based-mapping-in-select-and-sink"></a>Szabályon alapuló hozzárendelés a Select és a mosogatóban

A forrásban lévő oszlopok leképezése és az átalakítások kiválasztása esetén rögzített leképezési vagy szabály-alapú leképezéseket adhat hozzá. Ha ismeri az adatok sémáját, és meghatározott oszlopokat vár a forrás adatkészletből, hogy mindig megfeleljenek az adott statikus neveknek, használja a rögzített leképezést. Ha rugalmas sémákat használ, a szabályon alapuló leképezés használatával hozzon létre egy minta egyezést az oszlopok `name`, `type`, `stream`és `position` alapján. A rögzített és a szabályokon alapuló leképezések tetszőleges kombinációja lehet. 

Szabály alapú hozzárendelés hozzáadásához kattintson a **leképezés hozzáadása** elemre, és válassza a **szabály alapú leképezés**lehetőséget.

![szabály alapú leképezés](media/data-flow/rule2.png "Szabály alapú leképezés")

A bal oldali kifejezés mezőbe írja be a logikai egyezési feltételt. A jobb oldali kifejezés mezőben adja meg, hogy az egyeztetett oszlop hogyan lesz leképezve. A megegyező mező meglévő nevére való hivatkozáshoz használja a `$$`.

Ha a lefelé mutató Chevron ikonra kattint, megadhat egy regex-leképezési feltételt.

Kattintson a szemüveg ikonra egy szabály alapú hozzárendelés mellett, hogy megtekintse, hogy mely definiált oszlopok vannak összehasonlítva, és mire vannak leképezve.

![szabály alapú leképezés](media/data-flow/rule1.png "Szabály alapú leképezés")

A fenti példában két szabály-alapú hozzárendelés jön létre. Az első a "Movie" névvel ellátott összes oszlopot felveszi, és leképezi őket a meglévő értékekre. A második szabály a regexet használja a "Movie" kezdetű összes oszlop megfeleltetéséhez, és leképezi őket a "movieId" oszlopra.

Ha a szabály több azonos leképezést eredményez, engedélyezze az ismétlődő **bemenetek kihagyását** , vagy hagyja meg az ismétlődő **kimeneteket** az ismétlődések elkerülése érdekében.

## <a name="pattern-matching-expression-values"></a>Mintázattal egyező kifejezés értékei.

* a `$$` az egyes egyezések nevét vagy értékét futtatja futási időben
* `name` a bejövő oszlopok nevét jelöli
* `type` az egyes bejövő oszlopok adattípusát jelöli
* `stream` az egyes adatfolyamokhoz társított nevet vagy a folyamat átalakítását jelöli
* `position` az adatfolyamat oszlopainak sorszáma

## <a name="next-steps"></a>Következő lépések
* További információ az adatátalakítások hozzárendelési folyamatának [kifejezési nyelvéről](data-flow-expression-functions.md)
* Oszlopok mintáinak használata a fogadó [átalakításban](data-flow-sink.md) és az [átalakítás kiválasztása](data-flow-select.md) szabály alapú leképezéssel
