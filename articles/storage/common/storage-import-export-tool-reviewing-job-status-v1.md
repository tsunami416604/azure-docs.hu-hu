---
title: Az Azure import/export-feladatok állapotának áttekintése – v1 | Microsoft Docs
description: Megtudhatja, hogyan használhatja az importálási vagy exportálási feladatok által létrehozott naplófájlokat a feladatok állapotának megtekintéséhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/26/2017
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 2fa5cfcf0cb6c20a8a0b64651da0b365e12a3d05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056322"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Azure-beli importálási/exportálási feladatok állapotának áttekintése a másolási naplófájlokkal
Amikor az Microsoft Azure Import/Export szolgáltatás feldolgozza az importálási vagy exportálási feladatokhoz társított meghajtókat, a a Blobok importálásához vagy exportálásához használt Storage-fiókba írja a naplófájlok másolását. A naplófájl az importált vagy exportált fájlok részletes állapotát tartalmazza. A szolgáltatás visszaadja az egyes másolási naplófájlok URL-címét a Befejezett feladatok állapotának lekérdezése során. További információ: [Get Job](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get).  

## <a name="example-urls"></a>Példa URL-címekre

Az alábbi URL-címek egy importálási feladatokhoz tartozó naplófájlok másolására szolgálnak két meghajtóval:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Lásd: [importálási/exportálási szolgáltatás naplófájljának formátuma](../storage-import-export-file-format-log.md) a másolási naplók formátuma és az állapotkódok teljes listája.  

## <a name="next-steps"></a>Következő lépések

 * [Az Azure import/export eszköz beállítása](storage-import-export-tool-setup-v1.md)   
 * [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Importálási feladat javítása](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)
