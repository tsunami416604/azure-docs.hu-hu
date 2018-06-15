---
title: Tekintse át az Azure Import/Export feladatállapot - 1-es verzió |} Microsoft Docs
description: Útmutató a naplófájlokban jön létre, amikor az importálási vagy exportálási feladat futtatása az importálási/exportálási feladat állapotának megtekintéséhez.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: c69d1d69-6403-4eee-9949-0185faeecfa1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.openlocfilehash: bdb30bc28c36ab9e969efc8be3b87b97e4027b39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873708"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>A másolási naplófájlok Azure Import/Export feladat állapota áttekintése
Amikor a Microsoft Azure Import/Export szolgáltatás dolgoz fel egy importálása vagy exportálása feladattal társított meghajtók, ír másolása a tárfiók naplófájljait, vagy amelyen importálunk vagy bináris objektumok exportálása. A naplófájl minden olyan fájlról, amely lett importálva, illetve nem exportálható részletes állapotát tartalmazza. Minden példány naplófájl URL-érték érkezett vissza egy befejezett feladat állapotának lekérdezése Lásd: [Get Job](/rest/api/storageservices/Get-Job3) további információt.  

## <a name="example-urls"></a>Példa URL-címek

Példa URL-címek másolása naplófájlok hogy az importálás két meghajtókon a következők:  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Lásd: [Import/Export szolgáltatás naplófájlformátum](../storage-import-export-file-format-log.md) másolási naplókat, valamint a teljes listát állapotkódok formátumát.  
  
## <a name="next-steps"></a>Következő lépések
 
 * [Az Azure Import/Export eszköz beállítása](storage-import-export-tool-setup-v1.md)   
 * [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Importálási feladat javítása](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
