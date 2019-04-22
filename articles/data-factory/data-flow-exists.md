---
title: Az Azure Data Factory, az adatfolyam-leképezés létezik átalakítása
description: Az Azure Data Factory, az adatfolyam-leképezés létezik átalakítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6ce27ba699ae766ed4d2428f67d91379464bb9f1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783462"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Az Azure Data Factory, az adatfolyam-leképezés létezik átalakítása

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az Exists átalakítás átalakítást, amely a szolgáltatás leáll vagy az adatok áramlása érdekében lehetővé teszi a sorok szűrése egy sor. Létezik átalakítása hasonlít a ```SQL WHERE EXISTS``` és ```SQL WHERE NOT EXISTS```. Egy szűrő átalakítás után az eredményül kapott sorokat a streamből származó vagy tartalmazza összes sorát, ahol szerepel az 1. forrásból származó oszlop értékeit forrás 2 vagy 2 forrás nem létezik.

![Beállítások létezik](media/data-flow/exsits.png "1 létezik")

A második adatforrás az Exists választhatja, hogy az adatfolyam összehasonlításával értékek Stream 1 elleni Stream 2.

1. forrásból származó és a forrás 2 értékük megtekintéséhez elleni Exists vagy nem létezik, válassza ki az oszlopot.

## <a name="multiple-exists-conditions"></a>Több létezik feltételek

Az oszlop feltételeinek Exsits minden egyes sorára, mellett található egy + elérhető bejelentkezési, ha az egérmutatót elérni sor. Ez lehetővé teszi, hogy hozzá több sort a Exists feltételek.

## <a name="next-steps"></a>További lépések

