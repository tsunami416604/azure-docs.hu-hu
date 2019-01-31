---
title: Az Azure Import/Export eszköz importálási feladat parancsok gyors áttekintése |} A Microsoft Docs
description: A gyakran használt importálási feladat parancsok az Azure Import/Export eszköz parancs áttekintése.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 7d21ab42188d5b8d6cb8c179a28d1b5bee822f05
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453586"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Az importálási feladatokhoz használt gyakori parancsok rövid áttekintése

Ez a cikk ismerteti, hogy egy rövid összefoglaló az egyes gyakran használt parancsok. A részletes használati, lásd: [merevlemezek szolgáltatás előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import.md).

## <a name="first-session"></a>Első munkamenet

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

## <a name="second-session"></a>Második munkamenet

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

## <a name="abort-latest-session"></a>Legújabb munkamenet megszakítása

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

## <a name="resume-latest-interrupted-session"></a>Legújabb megszakított munkamenet folytatása

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

## <a name="add-drives-to-latest-session"></a>A legutóbbi munkamenet meghajtók hozzáadása

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>További lépések

* [Munkafolyamat-minta a merevlemezek importálási feladatokhoz való előkészítésére](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
