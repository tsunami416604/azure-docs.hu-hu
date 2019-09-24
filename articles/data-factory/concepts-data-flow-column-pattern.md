---
title: Azure Data Factory az adatfolyam-oszlopok mintáinak leképezése
description: Általánosított Adatátalakítási minták létrehozása Azure Data Factory oszlopos minták használatával a leképezési adatfolyamatokban
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 4df831c1329ab13f19e6ecf979e404d4a90e5f72
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219761"
---
# <a name="mapping-data-flows-column-patterns"></a>Adatfolyamatok oszlopos mintáinak leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Számos Azure Data Factory adatfolyam-átalakítás támogatja az "oszlopok mintáit", így a sablon oszlopai a rögzített oszlopnevek helyett mintázatok alapján hozhatók létre. Ezt a funkciót a Kifejezésszerkesztő segítségével definiálhatja az átalakítási oszlopoknak megfelelő mintázatokat, és nem kell pontos, konkrét mezőneveket megadni. A minták akkor hasznosak, ha a bejövő forrás mezői gyakran változnak, különösen abban az esetben, ha szövegfájlokban vagy NoSQL-adatbázisokban módosítanak oszlopokat. Ezt az állapotot más néven "Schema Drift"-ként nevezzük.

Ez a "rugalmas séma" típusú kezelést jelenleg a származtatott oszlop és az összesített átalakítások, valamint a Select és a fogadó átalakítások "szabályon alapuló leképezés" néven is megtalálhatók.

![oszlop mintázatai](media/data-flow/columnpattern2.png "Oszlop mintázatai")

## <a name="column-patterns"></a>Oszlopminták
Az oszlopos minták hasznosak a séma-eltolódási forgatókönyvek, valamint az általános forgatókönyvek kezelésére. Ez olyan feltételek esetén jó, amikor nem tudja teljesen tudni az egyes oszlopok nevét. Megadhatja az oszlop nevét és az oszlop adattípusát, és létrehozhat egy kifejezést az átalakításhoz, amely végrehajtja az adott műveletet a `name` `type` mintázatnak megfelelő adatfolyam  &  bármely mezőjén.

Ha olyan kifejezést ad hozzá egy átalakításhoz, amely mintákat fogad, válassza az "oszlop hozzáadása" mintát. Az oszlop mintázatai lehetővé teszik a séma drift oszlop egyeztetési mintáit.

A sablon oszlopainak mintáinak kiépítésekor a kifejezést használva `$$` a bemeneti adatfolyamban található minden egyező mezőre mutató hivatkozást jelöl.

Ha úgy dönt, hogy az egyik Expression Builder regex-függvényt használja, ezt követően használhatja a $1, $2, $3... a regex kifejezéssel megegyező almintákra való hivatkozáshoz.

Az oszlopdiagram példája egy több beérkező mezővel rendelkező összeg használata. Az összesített összeg kiszámítása az összesített átalakításban történik. Ezután az összeg értékkel egyező típusú mezőkhöz tartozó SUM függvényt használhatja, majd a $ $ értéket használva hivatkozhat a kifejezésben szereplő összes találatra.

## <a name="match-columns"></a>Oszlopok egyeztetése
![oszlop típusú mintázatok](media/data-flow/pattern2.png "Mintázat típusai")

Az oszlopokon alapuló mintázatok létrehozásához az oszlopnév, a típus, a stream vagy a pozíció, valamint a Expression functions és a reguláris kifejezések kombinációját használhatja.

![oszlop pozíciója](media/data-flow/position.png "Oszlop pozíciója")

## <a name="rule-based-mapping"></a>Szabály alapú leképezés
A forrásban lévő oszlopok leképezése és az átalakítások kiválasztásakor lehetősége van a "rögzített leképezés" vagy a "szabály-alapú leképezés" kiválasztására. Ha ismeri az adatok sémáját, és meghatározott oszlopokat vár a forrás adatkészletből, amelyek mindig megfelelnek az adott statikus névnek, használhat rögzített leképezést is. Ha azonban rugalmas sémákat használ, használjon szabályokon alapuló leképezést. A fent ismertetett szabályok alapján létrehozhat egy minta egyezést.

![szabály-alapú leképezés](media/data-flow/rule2.png "Szabály-alapú leképezés")

Hozza létre a szabályokat a Kifejezésszerkesztő használatával. A kifejezések logikai értéket adnak vissza az egyező oszlopoknak (true), vagy kizárják az oszlopokat (false).

## <a name="pattern-matching-special-columns"></a>Speciális oszlopoknak megfelelő mintázat

* `$$`lefordítva az egyes egyezések nevére a hibakeresési módban és a futtatáskor futó időpontban
* `name`az egyes bejövő oszlopok nevét jelöli
* `type`az egyes bejövő oszlopok adattípusát jelöli
* `stream`a folyamaton belül az egyes adatfolyamokhoz vagy átalakításokhoz társított nevet jelöli
* `position`az adatfolyamat oszlopainak sorszáma

## <a name="next-steps"></a>További lépések
* További információ az ADF-leképezés adatáramlási [kifejezésének nyelvéről](https://aka.ms/dataflowexpressions) adatátalakítások esetén
* Oszlopok mintáinak használata a fogadó [átalakításban](data-flow-sink.md) és az [átalakítás kiválasztása](data-flow-select.md) szabály alapú leképezéssel
