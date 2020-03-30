---
title: Az Azure import/export szolgáltatás REST API használata | Microsoft dokumentumok
description: Megtudhatja, hogy hol találhat erőforrásokat az Azure import/export szolgáltatás REST API-jának használatával, beleértve az útmutatót és a referenciaanyagokat is.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978866"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Az Azure Import/Export szolgáltatás REST API-jának használata

A Microsoft Azure importálási/exportálási szolgáltatás a REST API-t teszi elérhetővé az importálási/exportálási feladatok programozott szabályozásának engedélyezéséhez. A REST API segítségével végrehajthatja az [Azure Portalon](https://portal.azure.com/)végrehajtható összes importálási/exportálási műveletet. Emellett a REST API-t használhatja bizonyos részletes műveletek végrehajtásához, például egy feladat százalékos befejezésének lekérdezéséhez, amely jelenleg nem érhető el az Azure Portalon.

Az Import/Export szolgáltatás áttekintése [az Adatok blobstorage-ba történő átviteléhez a Microsoft Azure importálási/exportálási szolgáltatás használata](../storage-import-export-service.md) című témakörben és egy oktatóanyagban ismerteti, hogyan használhatja a portált importálási és exportálási feladatok létrehozására és kezelésére.

## <a name="service-endpoints"></a>Szolgáltatásvégpontok

Az Azure import/export szolgáltatás az Azure Resource Manager egyik erőforrás-szolgáltatója, és a következő HTTPS-végponton rest API-kat biztosít az importálási/exportálási feladatok kezeléséhez:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Verziókezelés

Az importálási/exportálási szolgáltatáshoz `api-version` érkező kérelmeknek `2016-11-01`meg kell adniuk a paramétert, és az értékét a értékére kell állítaniuk.

## <a name="importexport-service-operations"></a>Be- és exportálási szolgáltatás műveletei

[Importálási feladat létrehozása](../storage-import-export-creating-an-import-job.md)

[Exportálási feladat létrehozása](../storage-import-export-creating-an-export-job.md)

[Feladat állapotinformációinak lekérése](storage-import-export-retrieving-state-info-for-a-job.md)

[Feladatok enumerálása](../storage-import-export-enumerating-jobs.md)

[Feladatok megszakítása és törlése](storage-import-export-cancelling-and-deleting-jobs.md)

[Meghajtójegyzékek biztonsági mentése](../storage-import-export-backing-up-drive-manifests.md)

[Import/Export-feladatok diagnosztizálása és hiba utáni helyreállítása](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>További lépések

* [Tárolás importálása/exportálása REST](/rest/api/storageimportexport)
