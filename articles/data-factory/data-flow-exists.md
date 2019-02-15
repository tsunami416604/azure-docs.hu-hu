---
title: Az Azure Data Factory, az adatfolyam-leképezés létezik átalakítása
description: Az Azure Data Factory, az adatfolyam-leképezés létezik átalakítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 9d21b304f55ec746da4b7b42194fe0d168261b53
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271862"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Az Azure Data Factory, az adatfolyam-leképezés létezik átalakítása

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az Exists átalakítás átalakítást, amely a szolgáltatás leáll vagy az adatok áramlása érdekében lehetővé teszi a sorok szűrése egy sor. Létezik átalakítása hasonlít a ```SQL WHERE EXISTS``` és ```SQL WHERE NOT EXISTS```. Egy szűrő átalakítás után az eredményül kapott sorokat a streamből származó vagy tartalmazza összes sorát, ahol szerepel az 1. forrásból származó oszlop értékeit forrás 2 vagy 2 forrás nem létezik.

![Beállítások létezik](media/data-flow/exsits.png "1 létezik")

A második adatforrás az Exists választhatja, hogy az adatfolyam összehasonlításával értékek Stream 1 elleni Stream 2.

1. forrásból származó és a forrás 2 értékük megtekintéséhez elleni Exists vagy nem létezik, válassza ki az oszlopot.
