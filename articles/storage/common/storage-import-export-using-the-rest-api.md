---
title: "Az Azure Import/Export szolgáltatás REST API használatával |} Microsoft Docs"
description: "Ismerje meg, hol találhatók erőforrások az Azure Import/Export szolgáltatás REST API-t, beleértve az útmutató és a referenciaanyagot használatával."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 233f80e9-2e7f-48e0-9639-5c7785e7d743
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 9a5a97a5d9f06aa73f1ad521e112fa25f215724f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Az Azure Import/Export szolgáltatás REST API-jának használata

A Microsoft Azure Import/Export szolgáltatás közzétesz egy REST API, hogy az importálási/exportálási feladatok programozott hozzáférést. A REST API segítségével elvégzik az importálási/exportálási műveleteket hajthat végre a a [Azure-portálon](https://portal.azure.com/). Emellett a REST API használatával bizonyos a részletes műveletek, például egy feladatot, amely már nem érhető el az Azure portálon százalékos megvalósításának lekérdezése.

Lásd: [adatok átviteléhez a Blob Storage a Microsoft Azure Import/Export szolgáltatás használata](../storage-import-export-service.md) az Import/Export szolgáltatás és az oktatóanyag bemutatja, hogyan használhatja a portált létrehozása és kezelése az importálás és exportálni a feladatokat áttekintését.

## <a name="service-endpoints"></a>Szolgáltatás-végpontok

Az Azure Import/Export szolgáltatás egy erőforrás-szolgáltató az Azure Resource Manager és importálási/exportálási feladatok kezelése a REST API-készlet a következő HTTPS-végpont biztosít:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Verziókezelés

Az Import/Export szolgáltatás kéréseknek meg kell határozniuk a `api-version` paraméter, és állítsa be az értékét `2016-11-01`.

## <a name="importexport-service-operations"></a>Importálási/exportálási szolgáltatási műveletek

[Importálási feladat létrehozása](../storage-import-export-creating-an-import-job.md)

[Exportálási feladat létrehozása](../storage-import-export-creating-an-export-job.md)

[Feladat állapotinformációinak lekérése](storage-import-export-retrieving-state-info-for-a-job.md)

[Feladatok számbavétele](../storage-import-export-enumerating-jobs.md)

[Feladatok megszakítása és törlése](storage-import-export-cancelling-and-deleting-jobs.md)

[Meghajtó jegyzékfájlokat biztonsági mentése](../storage-import-export-backing-up-drive-manifests.md)

[Import/Export-feladatok diagnosztizálása és hibajavítása](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Következő lépések

* [Tárolási importálási/exportálási REST](/rest/api/storageimportexport)
