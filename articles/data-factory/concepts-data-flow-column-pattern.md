---
title: Az Azure Data Factory adatokat folyamat oszlop minták leképezése
description: Megismerkedhet az Azure Data Factory oszlop minták az adatfolyam-leképezés létrehozása általánosított sablont minták alkalmazásán át a mezőket az adatok folyamat során, függetlenül az alapul szolgáló séma-metaadatok
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 08cdaafe00b7dc586ea75f6ff03fdb89107edee9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66430755"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Az Azure data factory-társítási adatok elkezdenek beérkezni oszlop minták

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Több Azure Data Factory adatfolyam átalakítások támogatja az elgondolásra épül, "Oszlopok minták", úgy, hogy a sablon oszlopok helyett változtatható oszlopnevek minták alapján is létrehozhat. Ez a funkció a Kifejezésszerkesztő belül használhatja oszlopok átalakítása pontosan, a megadott mezőnevek átjárószolgáltatásnak-minták meghatározására. Minták hasznosak, ha a bejövő forrásmezőket módosítása gyakran, különösen az oszlopok módosítása a szöveges fájlokat, vagy a NoSQL-adatbázisok esetében. Ez a feltétel más "Séma eltéréseket" néven.

![oszlop minták](media/data-flow/columnpattern2.png "oszlop minták")

Oszlop minták hasznosak séma eltéréseket forgatókönyvek és is általános forgatókönyvek kezeléséhez. Azt, a feltételeknek megfelelő ahol nem áll teljesen hogy minden oszlop neve. A mintát is felel meg az oszlop neve, és oszlopok adatainak írja be, és a egy adott művelet bármely mező, amely megfelel az adatfolyamban végző átalakításkor kifejezés összeállítása a `name`  &  `type` mintákat.

Való hozzáadásakor egy kifejezés olyan átalakítást, amely elfogadja a mintákat, válassza az "Oszlop minta hozzáadása". Oszlop minták lehetővé teszi, hogy a séma eltéréseket oszlop egyező mintákat.

Sablon oszlop minták létrehozását, használjon `$$` a kifejezés a bemeneti streamből minden egyező mező való hivatkozást jelöl.

Ha kíván használni a Kifejezésszerkesztő regex függvények egyikét, majd ezt követően használhatja $1, 2 USD, $3... az alárendelt minták hivatkozni megfeleltetni a regex kifejezés.

Oszlop minta forgatókönyv például összeg használ több bejövő mezőket. A SUM összesítő számítások az összesítésben átalakításában. Ezután használhatja, amely egyezik "egész" és a $$ segítségével minden egyes egyezik a kifejezésben hivatkoznak mezőtípusok minden egyező összege.
