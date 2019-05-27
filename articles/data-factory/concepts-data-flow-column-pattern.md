---
title: Az Azure Data Factory adatokat folyamat oszlop minták leképezése
description: Megismerkedhet az Azure Data Factory oszlop minták az adatfolyam-leképezés létrehozása általánosított sablont minták alkalmazásán át a mezőket az adatok folyamat során, függetlenül az alapul szolgáló séma-metaadatok
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 5131329f6675bc86374f5a5c081e0aaa7d36c0fe
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155241"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Az Azure data factory-társítási adatok elkezdenek beérkezni oszlop minták

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Több Azure Data Factory adatfolyam átalakítások támogatja az elgondolásra épül, "Oszlopok minták", úgy, hogy a sablon oszlopok helyett változtatható oszlopnevek minták alapján is létrehozhat. Ez a funkció a Kifejezésszerkesztő belül oszlopok átalakítása xact, adott mezőnevek átjárószolgáltatásnak-minták meghatározására használhatja. Minták hasznosak, ha a bejövő forrásmezőket módosítása gyakran, különösen az oszlopok módosítása a szöveges fájlokat, vagy a NoSQL-adatbázisok esetében. Ez a feltétel más "Séma eltéréseket" néven.

![oszlop minták](media/data-flow/columnpattern2.png "oszlop minták")

Oszlop minták hasznosak séma eltéréseket forgatókönyvek és is általános forgatókönyvek kezeléséhez. Azt, a feltételeknek megfelelő ahol nem áll teljesen hogy minden oszlop neve. A mintát is felel meg az oszlop neve, és oszlopok adatainak írja be, és a egy adott művelet bármely mező, amely megfelel az adatfolyamban végző átalakításkor kifejezés összeállítása a `name`  &  `type` mintákat.

Való hozzáadásakor egy kifejezés olyan átalakítást, amely elfogadja a mintákat, válassza az "Oszlop minta hozzáadása". Oszlop minták lehetővé teszi, hogy a séma eltéréseket oszlop egyező mintákat.

Sablon oszlop minták létrehozását, használjon `$$` a kifejezés a bemeneti streamből minden egyező mező való hivatkozást jelöl.

Ha használja a Kifejezésszerkesztő regex függvények egyikét, majd ezt követően használhatja $1, 2 USD, $3.. való hivatkozáshoz a subpatterns egyezik a regex kifejezés.

Oszlop minta forgatókönyv például összeg használ több bejövő mezőket. A SUM összesítő számítások az összesítésben átalakításában. Majd az összes egyezést mezőtípusok ÖSSZEGEZZÜK, hogy egyezik "egész" és a $$ használatával minden egyes egyezik a kifejezésben hivatkoznak.
