---
title: Azure Data Factory az adatfolyam-oszlopok mintáinak leképezése
description: Általánosított Adatátalakítási minták létrehozása Azure Data Factory oszlopos minták használatával a leképezési adatfolyamatokban
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: d24988dfd5cbaf20e92c5afbbc39dc0c78e3ef6a
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314858"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Azure-beli adatfeldolgozó-leképezés adatforgalmának oszlop mintái

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Számos Azure Data Factory adatfolyam-átalakítás támogatja az "oszlopok mintáit", így a sablon oszlopai a rögzített oszlopnevek helyett mintázatok alapján hozhatók létre. Ezt a funkciót a Kifejezésszerkesztő segítségével definiálhatja az átalakítási oszlopoknak megfelelő mintázatokat, és nem kell pontos, konkrét mezőneveket megadni. A minták akkor hasznosak, ha a bejövő forrás mezői gyakran változnak, különösen abban az esetben, ha szövegfájlokban vagy NoSQL-adatbázisokban módosítanak oszlopokat. Ezt az állapotot más néven "Schema Drift"-ként nevezzük.

![oszlop mintázatai](media/data-flow/columnpattern2.png "Oszlop mintázatai")

Az oszlopos minták hasznosak a séma-eltolódási forgatókönyvek, valamint az általános forgatókönyvek kezelésére. Ez olyan feltételek esetén jó, amikor nem tudja teljesen tudni az egyes oszlopok nevét. Megadhatja az oszlop nevét és az oszlop adattípusát, és létrehozhat egy kifejezést az átalakításhoz, amely végrehajtja az adott műveletet a `name` `type` mintázatnak megfelelő adatfolyam  &  bármely mezőjén.

Ha olyan kifejezést ad hozzá egy átalakításhoz, amely mintákat fogad, válassza az "oszlop hozzáadása" mintát. Az oszlop mintázatai lehetővé teszik a séma drift oszlop egyeztetési mintáit.

A sablon oszlopainak mintáinak kiépítésekor a kifejezést használva `$$` a bemeneti adatfolyamban található minden egyező mezőre mutató hivatkozást jelöl.

Ha úgy dönt, hogy az egyik Expression Builder regex-függvényt használja, ezt követően használhatja a $1, $2, $3... a regex kifejezéssel megegyező almintákra való hivatkozáshoz.

Az oszlopdiagram példája egy több beérkező mezővel rendelkező összeg használata. Az összesített összeg kiszámítása az összesített átalakításban történik. Ezután az összeg értékkel egyező típusú mezőkhöz tartozó SUM függvényt használhatja, majd a $ $ értéket használva hivatkozhat a kifejezésben szereplő összes találatra.

## <a name="match-columns"></a>Oszlopok egyeztetése
![oszlop típusú mintázatok](media/data-flow/pattern2.png "Mintázat típusai")

Az oszlopokon alapuló mintázatok létrehozásához az oszlopnév, a típus, a stream vagy a pozíció, valamint a Expression functions és a reguláris kifejezések kombinációját használhatja.

![oszlop pozíciója](media/data-flow/position.png "Oszlop pozíciója")

## <a name="next-steps"></a>További lépések
További információ az ADF-leképezés adatáramlási [kifejezésének nyelvéről](http://aka.ms/dataflowexpressions) adatátalakítások esetén
