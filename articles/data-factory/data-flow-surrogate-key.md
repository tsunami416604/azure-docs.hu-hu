---
title: Az Azure Data Factory-folyamatot helyettes kulcs átalakítását leképezése
description: Hogyan lehet Azure Data Factory-leképezés Flow helyettes kulcs adatátalakítás használatával létrehozhat egy egymást követő értékek
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: eaa1c577f7e208400d3430222b006e0dbbd7956a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698435"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>A folyamat helyettes kulcs átalakítását leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A helyettes kulcs átalakítási használatával adja hozzá az adatokat a folyamat sorkészlet növekvő nem üzleti tetszőleges kulcs értéket. Ez akkor hasznos, dimenziótáblák csillag sémában analitikai adatmodellt ahol a dimenziótáblák minden tagjának rendelkeznie kell egy egyedi kulcsot Kimball DW módszertan nem üzleti kulcs részét képező tervezésekor.

![Kulcs átalakító helyettesítő](media/data-flow/surrogate.png "helyettesítő kulcs átalakítása")

"Kulcsoszlop" Ez a név meg kell adnia az új helyettes kulcs oszlopra.

A növekményes érték kezdőpontját "Kezdeti érték".

## <a name="increment-keys-from-existing-sources"></a>Növekmény-kulcsok meglévő források

Ha szeretné, hogy a feladatütemezés kezdő érték, amely létezik az olyan adatforrások, következett közvetlenül a helyettes kulcs átalakítást származtatott oszlopot átalakítást, és adja hozzá a két érték együtt:

![SK hozzáadása maximális](media/data-flow/sk006.png "helyettes kulcs átalakítás hozzáadása maximális száma")

A kulcs értékét az előző maximális az, hogy van két módszer használható:

### <a name="database-sources"></a>Adatbázis-forrásokhoz

A "Query" lehetőség használatával MAX() válassza a forrásból, az adatforrás-átalakítás használatával:

![Lekérdezési kulcs helyettesítő](media/data-flow/sk002.png "helyettesítő kulcs transzformációs lekérdezés")

### <a name="file-sources"></a>Fájl-források

Ha az előző maximális értékét a fájlban, a forrás átalakítást, és a egy összesített átalakítást, és MAX() kifejezés függvény használatával az előző maximális érték:

![Kulcsfájl helyettesítő](media/data-flow/sk008.png "helyettesítő kulcsfájl")

Mindkét esetben a bejövő új adatait kell összekapcsolni együtt a forrás, amely tartalmazza az előző maximális érték:

![Kulcs illesztési helyettesítő](media/data-flow/sk004.png "helyettesítő kulcs való csatlakozás")

## <a name="next-steps"></a>További lépések

Ezekben a példákban a [csatlakozzon](data-flow-join.md) és [származtatott oszlopot](data-flow-derived-column.md) átalakításokat.
