---
title: Azure Data Factory leképezése adatfolyam-sémájának eltolódása
description: Rugalmas adatfolyamatok létrehozása a Azure Data Factory a séma drift
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 562daa024985a546ffb49c4da11eace3bc81a659
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314811"
---
# <a name="mapping-data-flow-schema-drift"></a>Adatáramlási séma leképezése – drift

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A séma-eltolódás fogalma az az eset, amikor a források gyakran változtatják meg a metaadatokat. A mezők, oszlopok, típusok stb. a menet közben hozzáadhatók, eltávolíthatók vagy módosíthatók. A séma-eltolódás kezelésének hiányában az adatfolyam a felsőbb rétegbeli adatforrások változásaihoz képest sebezhetővé válik. Ha a bejövő oszlopok és a mezők változnak, a tipikus ETL-minták meghiúsulnak, mivel ezek a forrásokhoz kapcsolódnak.

A séma-eltolódás elleni védelem érdekében fontos, hogy a létesítmények egy adatáramlási eszközben legyenek, amely lehetővé teszi, hogy adatmérnökként:

* Megváltoztathatatlan mezőneveket, adattípusokat, értékeket és méreteket tartalmazó források definiálása
* Olyan átalakítási paramétereket határozhat meg, amelyek rögzített mezők és értékek helyett adatmintákkal működhetnek.
* Megadhatja azokat a kifejezéseket, amelyek megértik a bejövő mezőkkel egyező mintákat, és nem a nevesített mezőket használja

## <a name="how-to-implement-schema-drift-in-adf-mapping-data-flows"></a>Séma-eltolódás megvalósítása az ADF-leképezési adatforgalomban
Az ADF natív módon támogatja azokat a rugalmas sémákat, amelyek a végrehajtástól a végrehajtásig változnak, így általános Adatátalakítási logikát hozhat létre az adatfolyamatok újrafordításának szükségessége nélkül.

* Válassza a "séma drift engedélyezése" lehetőséget a forrás-átalakításban

<img src="media/data-flow/schemadrift001.png" width="400">

* Ha ezt a beállítást választotta, a rendszer minden bejövő mezőt beolvas a forrásból minden adatfolyam-végrehajtás során, és a teljes folyamaton át továbbítja a fogadónak.

* Az újonnan észlelt oszlopok (sodródott oszlopok) alapértelmezés szerint karakterlánc-adattípusként lesznek megérkezni. Ha azt szeretné, hogy az ADF automatikusan kikövetkeztetse a forrásból az adattípusokat, válassza a forrás átalakítás lehetőséget.

* Ügyeljen arra, hogy az "automatikus leképezés" segítségével képezze le az összes új mezőt a fogadó átalakításban, hogy az összes új mező lekerüljön a célhelyre, és az "a séma eltolódásának engedélyezése" beállítás is legyen a fogadón.

<img src="media/data-flow/automap.png" width="400">

* Minden működni fog, ha új mezők jelennek meg ebben a forgatókönyvben egy egyszerű forrás-> fogadó (másolási) leképezéssel.

* Ha olyan átalakításokat szeretne hozzáadni a munkafolyamatban, amelyek kezelik a sémák eltolódását, akkor az oszlopokat név, típus és érték szerint egyeztetve használhatja.

* Kattintson a származtatott oszlop vagy az összesítő átalakítás lehetőségre az "oszlop hozzáadása" elemre, ha olyan átalakítást szeretne létrehozni, amely megérti a "séma-eltolódást".

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> Az adatfolyamatban építészeti döntés szükséges ahhoz, hogy elfogadja a séma-eltolódást a folyamat során. Ha ezt teszi, a forrásokból is védelmet biztosíthat a séma változásai ellen. Az oszlopok és típusok korai kötését azonban elveszítheti az adatfolyamban. Azure Data Factory kezeli a séma-áthaladási folyamatokat késői kötésű folyamatokként, így az átalakítások létrehozásakor az oszlopnevek nem lesznek elérhetők a séma nézeteiben a folyamat során.

<img src="media/data-flow/taxidrift1.png" width="400">

A taxisofőr-bemutató minta adatforgalmában van egy minta séma, amely az alsó adatfolyamatban a TripFare forrással rendelkezik. Az összesített átalakításban figyelje meg, hogy az összesítési mezőkhöz az "oszlop minta" kialakítást használjuk. A meghatározott oszlopok elnevezése vagy a pozíció szerinti oszlopok keresése helyett feltételezzük, hogy az adatváltozások megváltozhatnak, és nem jelennek meg ugyanabban a sorrendben a futtatások között.

Ebben a példában a Azure Data Factory adatfolyam-sémák eltolódásának kezeléséhez a rendszer létrehozta és összesíti a "Double" típusú oszlopokat, tudván, hogy az adattartomány tartalmazza az egyes utak árait. Ezt követően a forrás összes dupla mezőjében elvégezheti az összesített matematikai számítást, függetlenül attól, hogy az oszlop hol található, és az oszlop elnevezésének helyétől függetlenül.

A Azure Data Factory adatfolyam-szintaxis $ $ értékkel jelöli az összes egyező oszlopot a megfelelő mintából. Az oszlopnevek az összetett karakterlánc-keresés és a reguláris kifejezés függvények használatával is megegyeznek. Ebben az esetben hozzunk létre egy új összesített mezőnevet a "Double" típusú oszlop egyezései alapján, és fűzze hozzá a szöveget ```_total``` az egyező nevekhez: 

```concat($$, '_total')```

Ezután kerekítjük és összegezjük az egyes egyező oszlopok értékeit:

```round(sum ($$))```

Ezt kipróbálhatja a "taxi-bemutató" Azure Data Factory adatfolyam-minta használatával. Váltson át a hibakeresési munkamenetre az adatfolyam-tervezési felület tetején található hibakeresési kapcsoló használatával, hogy az eredmények interaktív módon megjelenjenek:

<img src="media/data-flow/taxidrift2.png" width="800">

## <a name="access-new-columns-downstream"></a>Új oszlopok elérése lejjebb
Amikor új oszlopokat állít elő oszlopos mintákkal, a következő módszerekkel érheti el az új oszlopokat az adatfolyam-átalakítások során:

* A "byPosition" paranccsal azonosíthatja az új oszlopokat a pozíció száma alapján.
* A "byName" paranccsal azonosíthatja az új oszlopokat a nevük alapján.
* Az oszlopok mintái a "név", a "Stream", a "position" vagy a "type", illetve az új oszlopoknak megfelelő kombinációt használják.

## <a name="next-steps"></a>További lépések
Az [adatáramlás kifejezésének nyelvén](data-flow-expression-functions.md) további létesítményeket talál az oszlopok és a séma drift esetében, beleértve az "byName" és a "ByPosition" típust is.
