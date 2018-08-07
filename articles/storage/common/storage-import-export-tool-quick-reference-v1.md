---
title: Az Azure Import/Export eszköz importálási feladat parancsok - v1 rövid összefoglalása |} A Microsoft Docs
description: A gyakran használt importálási feladat parancsok az Azure Import/Export eszköz parancs áttekintése. Az Import/Export eszköz v1 utal.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 16e0fa6f7336b39f63b00564d37c1be308bebb16
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526283"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Az importálási feladatokhoz használt gyakori parancsok rövid áttekintése
Ez a témakör egy rövid összefoglaló az egyes gyakran használt parancsok. A részletes használati, lásd: [merevlemezek szolgáltatás előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md).  

## <a name="prepare-a-hard-drive-when-data-has-already-been-copied-to-the-hard-drive"></a>Készítse elő a merevlemez-meghajtóra, ha az adatok már másolta a merevlemezre
 A következő parancsot előkészíti a merevlemez-meghajtóra, amikor az adatok már másolta azt, de a BitLocker még be nem titkosított:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Merevlemez-meghajtóra másolja a egyetlen címtárban  
 A következő parancsot a BitLocker még nem titkosított merevlemez-meghajtóra másolja át egyetlen forráskönyvtár:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-two-directories-to-a-hard-drive"></a>Merevlemez-meghajtóra másolja a két címtár  
 Meghajtó két forrás címtár másolásához használja a következő parancsokat:  
  
 Az első parancs meghatározza a naplókönyvtár, a tárfiók kulcsát, a célként megadott meghajtóbetűjel, `format/encrypt` követelményeket és az általános paraméterek:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 A második parancs megadja a naplófájl, egy új munkamenet-azonosító és a forrás- és helyek:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>Nagy méretű fájl másolása a merevlemez egy második másolási munkamenetben  
 A következő parancsot egy előző munkamenetben másolási előkészített merevlemez-meghajtóra másolja át egyetlen nagy fájlok:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>További lépések

* [Munkafolyamat-minta a merevlemezek importálási feladatokhoz való előkészítésére](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
