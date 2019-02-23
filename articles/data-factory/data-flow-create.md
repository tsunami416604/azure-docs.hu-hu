---
title: Az adatfolyam leképezése az Azure Data Factory létrehozása
description: Az adatfolyam leképezése az Azure Data Factory létrehozása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: bb6ae9f97d681625218118b8adca116de1c0fb21
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728233"
---
# <a name="create-azure-data-factory-data-flow"></a>Az Azure Data Factory adatok folyamat létrehozása

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Leképezés adatáramlás az ADF-ben meg oly módon, nagy mennyiségű adat átalakítására bármely kódírás nélkül. Átalakítások sorozata hozhat létre egy Adatátalakítási feladatot a folyamattervezőben adatokat is tervezhet. Első tetszőleges számú adatforrás átalakítások Adatátalakítási lépéseket követi. Ezt követően fejezze be a adatfolyamait, fogadó kerül az eredményeket a célhelyen.

Első lépésként először létrehozunk egy új V2 Data Factory az Azure Portalról. Miután létrehozta az új gyári, kattintson a "Létrehozás és Monitorozás" csempére a Data Factory felhasználói felületének indítása.

![A folyamat lehetőséget](media/data-flow/v2dataflowportal.png "adatfolyam létrehozása")

Miután a Data Factory felhasználói felületén, a minta adatfolyamok is használhatja. A minták a ADF sablongyűjteményből érhetők el. Az ADF hozzon létre a "Folyamat létrehozása sablonból", és válassza ki a adatfolyam kategóriát a sablonok fotótárból.

![A folyamat lehetőséget](media/data-flow/template.png "adatfolyam létrehozása")

A rendszer felkéri az Azure Blob Storage-fiók adatait adja meg.

![A folyamat lehetőséget](media/data-flow/template2.png "adatfolyam létrehozása 2")

[Ezek a minták használt adatok itt található](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Töltse le a mintaadatokat, és tárolja a fájlokat az Azure Blob storage-fiókok, hogy a minták hajthat végre.

## <a name="create-new-data-flow"></a>Hozzon létre új adatfolyamot

Hozzon létre erőforrás "plusz" gomb ADF felhasználói felület létrehozása az adatok elkezdenek beérkezni használata

![A folyamat lehetőséget](media/data-flow/newresource.png "új erőforrás")

## <a name="next-steps"></a>További lépések

Az adatátalakítás az készítsen egy [átalakítási forrás](data-flow-source.md).
