---
title: Sémaeltolódás a leképezési adatfolyamban
description: Rugalmas adatfolyamok létrehozása az Azure Data Factoryban a sémaeltolódással
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 6e361d23860ce8f40abba5c246242cf345bb974c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606113"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Sémaeltolódás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Sémaeltolódás esetén a források gyakran módosítják a metaadatokat. Mezők, oszlopok és típusok menet közben adhatók hozzá, távolíthatók el vagy módosíthatók. A sémaeltolódás kezelése nélkül az adatfolyam sebezhetővé válik a felsőbb sugárúti adatforrás-változásokkal szemben. A tipikus ETL-minták sikertelenek, ha a bejövő oszlopok és mezők megváltoznak, mert általában ezekhez a forrásnevekhez vannak kötve.

A sémaeltolódás elleni védelem érdekében fontos, hogy az adatfolyam-kezelő eszköz létesítményei lehetővé tegyék, hogy adatmérnökként:

* Módosítható mezőnévvel, adattípussal, értékkel és mérettel rendelkező források meghatározása
* Olyan átalakítási paraméterek definiálása, amelyek a kódolt mezők és értékek helyett adatmintákkal dolgozhatnak
* Olyan kifejezések definiálása, amelyek névvel ellátott mezők helyett a bejövő mezőknek megfelelő mintákat értelmeznek

Az Azure Data Factory natív módon támogatja a rugalmas sémák, amelyek a végrehajtásról a végrehajtásra, így hozhat létre általános adatátalakítási logika anélkül, hogy újra kell fordítania az adatfolyamok.

Meg kell hoznia egy architekturális döntést az adatfolyamban, hogy elfogadja a séma sodródását a folyamat során. Ha ezt teszi, védelmet nyújt a forrásokból származó sémamódosítások ellen. Az oszlopok és típusok korai kötése azonban elveszhet az adatfolyamban. Az Azure Data Factory a séma-átlóási folyamatokat kései kötési folyamatként kezeli, így az átalakítások létrehozásakor az átsodródott oszlopnevek nem lesznek elérhetők a sémanézetekben a folyamat során.

Ez a videó bemutatja azokat az összetett megoldásokat, amelyek et az Adatfolyam-eltolódás funkcióval könnyedén hozhat létre az ADF-ben. Ebben a példában rugalmas adatbázissémák alapján hozunk létre újrafelhasználható mintákat:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Sémaeltolódás a forrásban

A forrásdefinícióból az adatfolyamba érkező oszlopok "sodródottként" vannak definiálva, ha nincsenek jelen a forrásvetésben. A forrásvetületet a forrástranszformáció vetítéslapjáról tekintheti meg. Amikor kiválaszt egy adatkészletet a forráshoz, az ADF automatikusan kiveszi a sémát az adatkészletből, és létrehoz egy projektet az adott adatkészletséma-definícióból.

A forrásátalakításban a sémaeltolódás olyan oszlopok olvasásaként van definiálva, amelyek nincsenek definiálva az adatkészletséma meghatározásával. Sémaeltolódás engedélyezéséhez jelölje be **a Sémaeltolódás engedélyezése** a forrásátalakításban jelölőnégyzetet.

![Séma-drift forrás](media/data-flow/schemadrift001.png "Séma-drift forrás")

Ha a sémaeltolódás engedélyezve van, a végrehajtás során a program az összes bejövő mezőt beolvassa a forrásból, és a teljes folyamaton keresztül a fogadóba továbbítja. Alapértelmezés szerint az összes újonnan észlelt oszlop, más néven *az elsodródott oszlopok*karakterlánc-adattípusként érkeznek. Ha azt szeretné, hogy az adatfolyam automatikusan kikövetkeztetje az elsodródott oszlopok adattípusait, jelölje be **az Elsodródott oszloptípusok jelölőnégyzetet** a forrásbeállításokban.

## <a name="schema-drift-in-sink"></a>Sémasodródás a mosogatóban

A fogadó átalakítása, séma eltolódása, amikor további oszlopokat ír a fogadó adatsémában meghatározott felülre. Sémaeltolódás engedélyezéséhez jelölje be **a Sémaeltolódás engedélyezése** a fogadóban az átalakításban jelölőnégyzetet.

![Sémasodródás-elesési mosogató](media/data-flow/schemadrift002.png "Sémasodródás-elesési mosogató")

Ha a sémaeltolódás engedélyezve van, győződjön meg arról, hogy a Leképezés lap **automatikus leképezése** csúszka be van kapcsolva. Ha ez a csúszka be van kapcsolva, az összes bejövő oszlop az úti célhoz kerül. Ellenkező esetben szabályalapú leképezést kell használnia az elsodródott oszlopok írásához.

![Automatikus leképezés elsüllyesztése](media/data-flow/automap.png "Automatikus leképezés elsüllyesztése")

## <a name="transforming-drifted-columns"></a>Sodródott oszlopok átalakítása

Ha az adatfolyam elsodródott oszlopokat tartalmaz, az átalakítások során a következő módszerekkel érheti el őket:

* A `byPosition` és `byName` a kifejezések segítségével kifejezetten hivatkozva hivatkozik egy oszlopra név vagy helyszám szerint.
* Oszlopminta hozzáadása származtatott oszlophoz vagy összesítő átalakításhoz, hogy megfeleljen a név, adatfolyam, pozíció vagy típus bármely kombinációjának
* Szabályalapú leképezés hozzáadása a Select vagy a Sink átalakításhoz, hogy az elsodródott oszlopokat egy mintán keresztül oszlopok aliasokhoz igazítsa

Az oszlopminták megvalósításáról az Oszlopminták az [adatfolyam leképezésében című témakörben](concepts-data-flow-column-pattern.md)talál további információt.

### <a name="map-drifted-columns-quick-action"></a>Drifted oszlopok leképezése gyorsművelet

Az elsodródott oszlopokra való kifejezett hivatkozáshoz gyorsan létrehozhat hozzárendeléseket ezekhez az oszlopokhoz egy adatelőnézeti gyorsművelettel. Ha a [hibakeresési mód](concepts-data-flow-debug-mode.md) be van kapcsolva, lépjen az Adatelőnézet fülre, és kattintson a **Frissítés** gombra az adatok előnézetének beolvasásához. Ha az adatgyár azt észleli, hogy sodródott oszlopok léteznek, kattintson **a Drifted leképezése** gombra, és hozzon létre egy származtatott oszlopot, amely lehetővé teszi, hogy a sémanézetekben az összes elsodródott oszlopra hivatkozzon.

![Térkép sodródott](media/data-flow/mapdrifted1.png "Térkép sodródott")

A létrehozott származtatott oszlop transzformációban minden sodródott oszlop le van képezve az észlelt névre és adattípusra. A fenti adatelőnézetben a "movieId" oszlop egész számként jelenik meg. Miután **a Map Drifted gombra** kattintott, a `toInteger(byName('movieId'))` movieId a Származtatott oszlopban definiálva van, és az alsóbb rétegbeli átalakítások sémájnézeteiben is szerepel.

![Térkép sodródott](media/data-flow/mapdrifted2.png "Térkép sodródott")

## <a name="next-steps"></a>További lépések
Az [adatfolyam-kifejezés nyelvén](data-flow-expression-functions.md)további lehetőségeket talál az oszlopmintákhoz és a sémaeltolódáshoz, beleértve a "byName" és a "byPosition" szavakat.
