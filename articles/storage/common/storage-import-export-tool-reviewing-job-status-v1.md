---
title: Az Azure Import/Export-feladatok állapotának - v1 áttekintése |} A Microsoft Docs
description: Ismerje meg, hogyan használható a naplófájlok jön létre, amikor az importálás vagy exportálás feladat volt futtatva az importálási/exportálási feladat állapotának megtekintéséhez.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 306e3ccf19ba8db2de01e4b20a52707215a4a040
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470365"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Az Azure Import/Export-feladatok állapotának áttekintése a másolási naplófájlok segítségével
A Microsoft Azure Import/Export szolgáltatás egy importálás vagy exportálás feladathoz hozzárendelt meghajtókra dolgozza fel, amikor ír másolási naplófájlokat a storage-fiók segítségével, vagy, amelyről importálása vagy exportálása blobok. A naplófájl minden egyes importált vagy exportált fájllal kapcsolatos részletes állapotát tartalmazza. Egyes másolási naplófájlok URL-CÍMÉT adja vissza; egy befejezett feladat állapotának lekérdezése során Lásd: [Get Job](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) további információt.  

## <a name="example-urls"></a>Példa URL-címek

Az alábbiakban például URL-címeket, a másolási naplófájlokat a két meghajtó az importálási feladatokhoz:  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Lásd: [Import/Export szolgáltatás naplófájljainak formátuma](../storage-import-export-file-format-log.md) a naplók másolása és állapotkódok teljes listáját.  
  
## <a name="next-steps"></a>További lépések
 
 * [Az Azure Import/Export eszköz telepítése](storage-import-export-tool-setup-v1.md)   
 * [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Importálási feladat javítása](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
