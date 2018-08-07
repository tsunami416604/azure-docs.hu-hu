---
title: Az Azure Import/Export szolgáltatás REST API használatával |} A Microsoft Docs
description: Ismerje meg, hogy hol található az erőforrásokat az Azure Import/Export szolgáltatás REST API-t, beleértve az útmutató és a referencia használatának.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 3775a77a6dfc590e79e785e1604226c1187952de
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528408"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Az Azure Import/Export szolgáltatás REST API-jának használata

A Microsoft Azure Import/Export szolgáltatás REST API-val programozott vezérelhető az importálási/exportálási feladatok tesz elérhetővé. A REST API segítségével hajtsa végre az importálási/exportálási műveleteket hajthat végre a a [az Azure portal](https://portal.azure.com/). Emellett a REST API használatával bizonyos részletes műveletek, például egy feladatot, amely jelenleg nem áll rendelkezésre az Azure Portalon százalékos megvalósításának lekérdezése.

Lásd: [adatok átvitele a Blob Storage a Microsoft Azure Import/Export szolgáltatás használata](../storage-import-export-service.md) az Import/Export szolgáltatás és a egy oktatóanyag, amely bemutatja, hogyan használhatja a portálon hozzon létre és kezelhetők az importálás, és a feladatok exportálása áttekintését.

## <a name="service-endpoints"></a>Szolgáltatásvégpontok

Az Azure Import/Export szolgáltatás erőforrás-szolgáltató az Azure Resource Manager és egy REST API-k készlete következő HTTPS-végpontján biztosít importálási/exportálási feladatok kezeléséhez:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Verziókezelés

Az Import/Export szolgáltatáshoz érkező kérések meg kell adnia a `api-version` paraméter és az értékét állítsa `2016-11-01`.

## <a name="importexport-service-operations"></a>Importálási/exportálási szolgáltatás műveletek

[Importálási feladat létrehozása](../storage-import-export-creating-an-import-job.md)

[Exportálási feladat létrehozása](../storage-import-export-creating-an-export-job.md)

[Feladat állapotinformációinak lekérése](storage-import-export-retrieving-state-info-for-a-job.md)

[Feladatok számbavétele](../storage-import-export-enumerating-jobs.md)

[Feladatok megszakítása és törlése](storage-import-export-cancelling-and-deleting-jobs.md)

[Meghajtójegyzékek biztonsági mentése](../storage-import-export-backing-up-drive-manifests.md)

[Import/Export-feladatok diagnosztizálása és hibajavítása](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>További lépések

* [Storage Import/Export REST](/rest/api/storageimportexport)
