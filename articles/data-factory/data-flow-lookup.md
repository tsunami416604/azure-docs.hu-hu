---
title: Adatfolyam-keresés átalakításának leképezése
description: Azure Data Factory leképezési adatfolyam keresési transzformációja
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/03/2019
ms.openlocfilehash: 5cc54c95759ba1490f498305f05cc49a4411686d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930318"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory leképezési adatfolyam keresési transzformációja

A lookup használatával adjon meg egy másik forrásból származó referenciát az adatfolyamathoz. A keresési átalakításhoz olyan definiált forrásra van szükség, amely a hivatkozási táblára mutat, és megfelel a kulcs mezőinek.

![Keresési transzformáció](media/data-flow/lookup1.png "Keresés")

Válassza ki azokat a legfontosabb mezőket, amelyeknek meg kell egyeznie a bejövő adatfolyam mezői és a hivatkozási forrás mezői között. Először létre kell hoznia egy új forrást az adatfolyam-tervezési vászonon, hogy az a keresés jobb oldalán legyen.

A egyezések megtalálása esetén a hivatkozási forrásból származó sorok és oszlopok lesznek hozzáadva az adatfolyamhoz. Kiválaszthatja, hogy mely érdekes mezőket kívánja felvenni a fogadóba az adatfolyam végén. Azt is megteheti, hogy a keresés után egy kiválasztott átalakítót használ a mezőlista törléséhez, hogy csak a megőrizni kívánt streamek mezőit tartsa meg.

A keresési átalakítás a bal oldali külső illesztés megfelelőjét hajtja végre. Így a bal oldali forrásból származó összes sor megjelenik a jobb oldaláról származó egyezések kombinálásával. Ha a Keresés több egyező értékkel rendelkezik, vagy ha testre szeretné szabni a keresési kifejezést, érdemes átváltani egy JOIN átalakításra, és használhat keresztes illesztést is. Ezzel elkerülhető az esetleges Descartes-féle hibák végrehajtása a végrehajtás során.

## <a name="match--no-match"></a>Egyezés/nincs egyezés

A keresési átalakítás után a következő átalakításokkal vizsgálhatja meg az egyes egyeztetési sorok eredményét, ha a kifejezés függvény `isMatch()` használatával további döntéseket szeretne tenni a logikában attól függően, hogy a keresés sor egyezést eredményezett-e, vagy sem.

![Keresési minta](media/data-flow/lookup111.png "Keresési minta")

A keresési transzformáció használata után hozzáadhat egy feltételes felosztású átalakítási felosztást a ```isMatch()``` függvényhez. A fenti példában a sorok egyeztetése a felső streamen és a nem egyező sorokon halad végig a ```NoMatch``` adatfolyamon.

## <a name="first-or-last-value"></a>Első vagy utolsó érték

Ha a Keresés több egyezést is tartalmaz, érdemes lehet csökkenteni a több egyező sort az első vagy az utolsó egyezés kiválasztásával. Ezt a keresés után egy összesített átalakítással végezheti el.

Ebben az esetben egy ```PickFirst``` nevű összesített átalakítás használatos a keresési egyezések első értékének kiválasztásához.

![Keresési összesítés](media/data-flow/lookup333.png "Keresési összesítés")

![Első keresés](media/data-flow/lookup444.png "Első keresés")

## <a name="optimizations"></a>Optimalizálás

Data Factory az adatfolyamatok kibővített Spark-környezetekben futnak. Ha az adatkészlet elfér a munkavégző csomópont memóriájában, optimalizálhatja a keresési teljesítményt.

![Szórásos csatlakozás](media/data-flow/broadcast.png "Szórt csatlakozás")

### <a name="broadcast-join"></a>Szórásos csatlakozás

Válassza ki a bal oldali és/vagy jobb oldali szórásos illesztést, hogy az ADF kérése után a teljes adatkészletet a keresési kapcsolat egyik oldaláról a memóriába küldje. A kisebb adatkészletek esetében ez nagy mértékben növelheti a keresési teljesítményt.

### <a name="data-partitioning"></a>Adatparticionálás

Az adatparticionálást úgy is megadhatja, ha a keresési átalakítás optimalizálása lapján a "particionálás beállítása" lehetőséget választja, és olyan adatkészleteket hoz létre, amelyek jobban illeszkednek a memóriába egy munkavégző számára.

## <a name="next-steps"></a>Következő lépések

* A [Csatlakozás](data-flow-join.md) és a [meglévő](data-flow-exists.md) átalakítások hasonló feladatokat hajtanak végre az ADF-leképezési adatforgalomban. Tekintse meg a következő átalakításokat.
* [Feltételes felosztás](data-flow-conditional-split.md) és ```isMatch()``` használata sorok felosztásához a megfeleltetési és a nem egyező értékekhez
