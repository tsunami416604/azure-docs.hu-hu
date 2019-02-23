---
title: Az Azure Data Factory, az adatfolyam-leképezés létezik átalakítása
description: Az Azure Data Factory, az adatfolyam-leképezés létezik átalakítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 18d8a0e231e8b4dbe33911dd6267966674366904
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734489"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Az Azure Data Factory, az adatfolyam-leképezés létezik átalakítása

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az Exists átalakítás átalakítást, amely a szolgáltatás leáll vagy az adatok áramlása érdekében lehetővé teszi a sorok szűrése egy sor. Létezik átalakítása hasonlít a ```SQL WHERE EXISTS``` és ```SQL WHERE NOT EXISTS```. Egy szűrő átalakítás után az eredményül kapott sorokat a streamből származó vagy tartalmazza összes sorát, ahol szerepel az 1. forrásból származó oszlop értékeit forrás 2 vagy 2 forrás nem létezik.

![Beállítások létezik](media/data-flow/exsits.png "1 létezik")

A második adatforrás az Exists választhatja, hogy az adatfolyam összehasonlításával értékek Stream 1 elleni Stream 2.

1. forrásból származó és a forrás 2 értékük megtekintéséhez elleni Exists vagy nem létezik, válassza ki az oszlopot.
