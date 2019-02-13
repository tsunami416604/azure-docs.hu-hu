---
title: Az Azure Data Factory adatokat folyamat oszlop minták leképezése
description: Az Azure Data Factory leképezési adatok folyamat oszlop minták segítségével alkalmazásán át a mezőket az adatok folyamat során, függetlenül az alapul szolgáló séma-metaadatok általánosított sablont minták létrehozása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: dd397259ebcd6afe34a47a6f853d826a845a1244
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213463"
---
# <a name="azure-data-factory-mapping-data-flow-concepts"></a>Az Azure Data Factory-folyamat – fogalmak leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Több Azure Data Factory adatfolyam átalakítások támogatja az elgondolásra épül, "Oszlopok minták", úgy, hogy a sablon oszlopok helyett változtatható oszlopnevek minták alapján is létrehozhat. Ez a funkció a Kifejezésszerkesztő belül oszlopok átalakítása xact, adott mezőnevek átjárószolgáltatásnak-minták meghatározására használhatja. Minták hasznosak, ha a bejövő forrásmezőket módosítása gyakran, különösen az oszlopok módosítása a szöveges fájlokat, vagy a NoSQL-adatbázisok esetében. Ez a más néven "Séma eltéréseket".

![oszlop minták](media/data-flow/columnpattern2.png "oszlop minták")

Oszlop minták hasznosak séma eltéréseket forgatókönyvek és is általános forgatókönyvek kezeléséhez. Azt, a feltételeknek megfelelő ahol nem áll teljesen hogy minden oszlop neve. A mintát is felel meg az oszlop neve, és oszlopok adatainak írja be, és a egy adott művelet bármely mező, amely megfelel az adatfolyamban végző átalakításkor kifejezés összeállítása a `name`  &  `type` mintákat.

Való hozzáadásakor egy kifejezés olyan átalakítást, amely elfogadja a mintákat, válassza az "Oszlop minta hozzáadása". Oszlop minták lehetővé teszi, hogy a séma eltéréseket oszlop egyező mintákat.

Sablon oszlop minták létrehozását, használjon `$$` a kifejezés a bemeneti streamből minden egyező mező való hivatkozást jelöl.

Ha használja a Kifejezésszerkesztő regex függvények egyikét, majd ezt követően használhatja $1, 2 USD, $3.. való hivatkozáshoz a subpatterns egyezik a regex kifejezés.

Oszlop minta forgatókönyv például összeg használ több bejövő mezőket. A SUM összesítő számítások az összesítésben átalakításában. Majd az összes egyezést mezőtípusok ÖSSZEGEZZÜK, hogy egyezik "egész" és a $$ használatával minden egyes egyezik a kifejezésben hivatkoznak.
