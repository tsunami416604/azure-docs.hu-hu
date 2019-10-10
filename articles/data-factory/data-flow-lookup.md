---
title: Azure Data Factory leképezési adatfolyam keresési transzformációja
description: Azure Data Factory leképezési adatfolyam keresési transzformációja
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: d762bddbe098e30cbf9e9c02da3c06073a358b12
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249251"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory leképezési adatfolyam keresési transzformációja

A lookup használatával adjon meg egy másik forrásból származó referenciát az adatfolyamathoz. A keresési átalakításhoz olyan definiált forrásra van szükség, amely a hivatkozási táblára mutat, és megfelel a kulcs mezőinek.

![Keresési transzformáció](media/data-flow/lookup1.png "keresése")

Válassza ki azokat a legfontosabb mezőket, amelyeknek meg kell egyeznie a bejövő adatfolyam mezői és a hivatkozási forrás mezői között. Először létre kell hoznia egy új forrást az adatfolyam-tervezési vászonon, hogy az a keresés jobb oldalán legyen.

A egyezések megtalálása esetén a hivatkozási forrásból származó sorok és oszlopok lesznek hozzáadva az adatfolyamhoz. Kiválaszthatja, hogy mely érdekes mezőket kívánja felvenni a fogadóba az adatfolyam végén. Azt is megteheti, hogy a keresés után egy kiválasztott átalakítót használ a mezőlista törléséhez, hogy csak a megőrizni kívánt streamek mezőit tartsa meg.

A keresési átalakítás a bal oldali külső illesztés megfelelőjét hajtja végre. Így a bal oldali forrásból származó összes sor megjelenik a jobb oldaláról származó egyezések kombinálásával. Ha a Keresés több egyező értékkel rendelkezik, vagy ha testre szeretné szabni a keresési kifejezést, érdemes átváltani egy JOIN átalakításra, és használhat keresztes illesztést is. Ezzel elkerülhető az esetleges Descartes-féle hibák végrehajtása a végrehajtás során.

## <a name="match--no-match"></a>Egyezés/nincs egyezés

A keresési átalakítás után a következő átalakításokkal vizsgálhatja meg az egyes egyeztetési sorok eredményét a `isMatch()` Expression függvény használatával, hogy további döntéseket lehessen tenni a logikában attól függően, hogy a keresés egy sor egyezését eredményezte-e, vagy sem.

## <a name="optimizations"></a>Optimalizálás

Data Factory az adatfolyamatok kibővített Spark-környezetekben futnak. Ha az adatkészlet elfér a munkavégző csomópont memóriájában, optimalizálhatja a keresési teljesítményt.

![Szórásos csatlakozás](media/data-flow/broadcast.png "szórásos csatlakoztatása")

### <a name="broadcast-join"></a>Szórásos csatlakozás

Válassza ki a bal oldali és/vagy jobb oldali szórásos illesztést, hogy az ADF kérése után a teljes adatkészletet a keresési kapcsolat egyik oldaláról a memóriába küldje. A kisebb adatkészletek esetében ez nagy mértékben növelheti a keresési teljesítményt.

### <a name="data-partitioning"></a>Adatparticionálás

Az adatparticionálást úgy is megadhatja, ha a keresési átalakítás optimalizálása lapján a "particionálás beállítása" lehetőséget választja, és olyan adatkészleteket hoz létre, amelyek jobban illeszkednek a memóriába egy munkavégző számára.

## <a name="next-steps"></a>Következő lépések

A [Csatlakozás](data-flow-join.md) és a [meglévő](data-flow-exists.md) átalakítások hasonló feladatokat hajtanak végre az ADF-leképezési adatforgalomban. Tekintse meg a következő átalakításokat.
