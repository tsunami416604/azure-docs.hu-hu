---
title: "Azure Import/Export eszköz importálási feladat parancsok rövid összefoglalása |} Microsoft Docs"
description: "Az Azure Import/Export eszköz parancsreferencia a gyakran használt importálási feladat parancsok."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: d51ae35ead0e7d8289de663e5b7b48d28271e810
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Az importálási feladatokhoz használt gyakori parancsok rövid áttekintése

Ez a cikk ismerteti, hogy egy rövid összefoglaló néhány gyakran használt parancsok. Tekintse meg a részletes használati [előkészítése merevlemezek hogy az importálás](../storage-import-export-tool-preparing-hard-drives-import.md).

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

## <a name="add-drives-to-latest-session"></a>Adja hozzá meghajtók legújabb-munkamenethez

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>Következő lépések

* [Munkafolyamat-minta a merevlemezek importálási feladatokhoz való előkészítésére](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
