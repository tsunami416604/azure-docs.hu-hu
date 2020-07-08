---
title: Az Azure import/export-feladatok állapotának áttekintése – v1 | Microsoft Docs
description: Az importálási/exportálási feladatok állapotának megtekintéséhez az importálási vagy exportálási feladatok futtatásakor létrehozott naplófájlok használatát ismerheti meg.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 5ab0dd7f0e0ed90c205c37ddbb7ea3a9fca74ae9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85512268"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Azure-beli importálási/exportálási feladatok állapotának áttekintése a másolási naplófájlokkal
Ha az Microsoft Azure Import/Export szolgáltatás egy importálási vagy exportálási feladatokhoz tartozó meghajtókat dolgoz fel, a naplófájlokat a Storage-fiókba másolja, vagy a Blobok importálásához vagy exportálásához. A naplófájl az importált vagy exportált fájlok részletes állapotát tartalmazza. Az egyes másolási naplófájlok URL-címét a Befejezett feladatok állapotának lekérdezése során adja vissza a rendszer. További információért lásd a [feladatok beolvasása](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) című témakört.  

## <a name="example-urls"></a>Példa URL-címekre

Az alábbi URL-címek egy importálási feladatokhoz tartozó naplófájlok másolására szolgálnak két meghajtóval:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Lásd: [importálási/exportálási szolgáltatás naplófájljának formátuma](../storage-import-export-file-format-log.md) a másolási naplók formátuma és az állapotkódok teljes listája.  

## <a name="next-steps"></a>További lépések

 * [Az Azure import/export eszköz beállítása](storage-import-export-tool-setup-v1.md)   
 * [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Importálási feladat javítása](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
