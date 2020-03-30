---
title: Az Azure importálási/exportálási feladat állapotának áttekintése - 1. Microsoft dokumentumok
description: Megtudhatja, hogy az importálási/exportálási feladat futtatásakor létrehozott naplófájlok hogyan használhatók az importálási/exportálási feladat állapotának megtekintéséhez.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 33e6ecca0eb4ca05b1f6bf53cdb3bd4ac8c8d8a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978437"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Az Azure importálási/exportálási feladat állapotának áttekintése naplófájlok másolásával
Amikor a Microsoft Azure importálási/exportálási szolgáltatási folyamatok egy importálási vagy exportálási feladathoz társított meghajtók, azt írja a másolási naplófájlokat a tárfiókba, vagy ahonnan blobok importálása vagy exportálása. A naplófájl részletes állapotot tartalmaz az importált vagy exportált fájlokról. Az egyes másolati naplófájlok URL-címét a rendszer a befejezett feladat állapotának lekérdezésekénél adja vissza; További információt a [Munkabeszerezés című](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) témakörben talál.  

## <a name="example-urls"></a>Példa URL-címek

A következő példák példa URL-címek a két meghajtóval rendelkező importálási feladat naplófájljainak másolásához:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 A másolási naplók formátumát és az állapotkódok teljes listáját lásd: [Import/Export service Log File Format](../storage-import-export-file-format-log.md) .  

## <a name="next-steps"></a>További lépések

 * [Az Azure importálási/exportálási eszközbeállítása](storage-import-export-tool-setup-v1.md)   
 * [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Importálási feladat javítása](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
