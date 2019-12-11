---
title: Az Azure import/export szolgáltatás REST API használata | Microsoft Docs
description: Ismerje meg, hogy hol találhatók az Azure import/export szolgáltatás REST API használatának erőforrásai, beleértve a segédanyagokat és a segédanyagokat is.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978866"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Az Azure Import/Export szolgáltatás REST API-jának használata

A Microsoft Azure Import/Export szolgáltatás az importálási/exportálási feladatok programozott vezérlésének engedélyezéséhez REST API tesz elérhetővé. A REST API segítségével elvégezheti az összes olyan importálási/exportálási műveletet, amelyet végrehajthat a [Azure Portal](https://portal.azure.com/). Emellett a REST API használatával bizonyos szemcsés műveleteket hajthat végre, például lekérdezheti a feladatok százalékos befejezését, ami jelenleg nem érhető el a Azure Portal.

Az importálási és exportálási feladatok létrehozásával és kezelésével kapcsolatos információkért lásd: [az Microsoft Azure import/export szolgáltatás használata az adatok blob Storageba történő átviteléhez](../storage-import-export-service.md) .

## <a name="service-endpoints"></a>Szolgáltatásvégpontok

Az Azure import/export szolgáltatás a Azure Resource Manager erőforrás-szolgáltatója, amely REST API-kat biztosít a következő HTTPS-végponton az importálási/exportálási feladatok kezeléséhez:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Verziókezelés

Az import/export szolgáltatásnak küldött kéréseknek meg kell adniuk a `api-version` paramétert, és az értékét `2016-11-01`értékre kell állítani.

## <a name="importexport-service-operations"></a>Importálási/exportálási szolgáltatási műveletek

[Importálási feladat létrehozása](../storage-import-export-creating-an-import-job.md)

[Exportálási feladat létrehozása](../storage-import-export-creating-an-export-job.md)

[Feladat állapotinformációinak lekérése](storage-import-export-retrieving-state-info-for-a-job.md)

[Feladatok számbavétele](../storage-import-export-enumerating-jobs.md)

[Feladatok megszakítása és törlése](storage-import-export-cancelling-and-deleting-jobs.md)

[A meghajtó-jegyzékfájlok biztonsági mentése](../storage-import-export-backing-up-drive-manifests.md)

[Import/Export-feladatok diagnosztizálása és hibajavítása](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Következő lépések

* [Tároló importálási/exportálási REST](/rest/api/storageimportexport)
