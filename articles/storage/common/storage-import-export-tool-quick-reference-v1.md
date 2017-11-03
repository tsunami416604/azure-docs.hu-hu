---
title: "Gyors referenciaként a Microsoft Azure Import/Export eszköz importálási feladat parancsok - 1-es verzió |} Microsoft Docs"
description: "Az Azure Import/Export eszköz parancsreferencia a gyakran használt importálási feladat parancsok. A v1 az Import/Export eszköz vonatkozik."
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
ms.openlocfilehash: 370cf6fae7ae106e8341f65086c8b8187d335746
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Az importálási feladatokhoz használt gyakori parancsok rövid áttekintése
Ez a témakör egy rövid összefoglaló néhány gyakran használt parancsok. Tekintse meg a részletes használati [előkészítése merevlemezek hogy az importálás](../storage-import-export-tool-preparing-hard-drives-import-v1.md).  

## <a name="prepare-a-hard-drive-when-data-has-already-been-copied-to-the-hard-drive"></a>Készítse elő a merevlemez-meghajtóról, adatok már másolását a merevlemezre
 A következő parancsot előkészíti a merevlemez-meghajtóról, ha adatokat azt már másolta, azonban a BitLocker még nincs titkosítva:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Másolja a egyetlen könyvtárat a merevlemez-meghajtóra  
 A következő parancsot a BitLocker még nem titkosított merevlemez-meghajtóra másolja át egyetlen forráskönyvtárat:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-two-directories-to-a-hard-drive"></a>Két könyvtárak másolása a merevlemez-meghajtóra  
 Két forrás könyvtárak másolása meghajtóra, a következő parancsokkal:  
  
 Az első parancs határozza meg, a naplózási könyvtár, a tárfiók hívóbetűjét, a célként megadott meghajtóbetűjel, `format/encrypt` követelményeket és az általános paraméterek:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 A második parancs határozza meg, a napló fájlt, egy új munkamenet-Azonosítót és a forrás és cél helyeken:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>A merevlemez-meghajtóról egy második példány munkamenetben nagy fájl másolása  
 A következő parancsot egy nagyméretű fájlt egy merevlemezen, amely egy előző munkamenetben másolási előkészített másolja:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>Következő lépések

* [Munkafolyamat-minta a merevlemezek importálási feladatokhoz való előkészítésére](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
