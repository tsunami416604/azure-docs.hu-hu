---
title: Az Azure importálási/exportálási eszköz hibáinak elhárítása | Microsoft dokumentumok
description: Ismerje meg az Azure importálási/exportálási eszköz használata során észlelt gyakori problémákat, és hogyan kezelhető.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 4eeeb538bcd39eed40a92dd45e7ba7bed25558e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978407"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Az Azure Import/Export eszköz hibaelhárítása
A Microsoft Azure importálási/exportálási eszköz hibaüzeneteket ad vissza, ha problémákba ütközik. Ez a témakör néhány gyakori problémát sorol fel, amelyekkel a felhasználók problémák kalkulezek be.  

## <a name="a-copy-session-fails-what-i-should-do"></a>A másolási munkamenet sikertelen, mit kell tennem?  
 Ha egy másolási munkamenet sikertelen, két lehetőség közül választhat:  

 Ha a hiba újrapróbálkozásra képes, például ha a hálózati megosztás rövid ideig offline állapotban volt, és most újra online állapotba került, folytathatja a másolási munkamenetet. Ha a hiba nem próbálható meg újra, például ha nem a megfelelő forrásfájlkönyvtárat adta meg a parancssori paraméterekben, meg kell szakítania a másolási munkamenetet. A másolási munkamenetek folytatásáról és megszakításáról a [Merevlemezek előkészítése importálási feladathoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md) című témakörben talál további információt.  

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Nem lehet folytatni vagy megszakítani a másolási munkamenetet  
 Ha a másolási munkamenet a meghajtó első másolási munkamenete, akkor a hibaüzenetnek a következőt kell kimondania: "Az első másolási munkamenet nem folytatható vagy szakítható meg." Ebben az esetben törölheti a régi naplófájlt, és újrafuttathatja a parancsot.  

 Ha a másolási munkamenet nem az első, amely a meghajtóhoz tartozik, bármikor folytatható vagy megszakítható.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Elvesztettem a naplófájlt, létrehozhatom a feladatot?  
 A meghajtó naplófájlja tartalmazza az adatok erre a meghajtóra történő másolásának teljes adatait, és további fájlok hozzáadására van szükség a meghajtóhoz, és importálási feladat létrehozásához használható. Ha a naplófájl elvész, újra kell csinálnia a meghajtó összes másolási munkamenetét.  

## <a name="next-steps"></a>További lépések

* [Az azure importálási/exportálási eszköz beállítása](../storage-import-export-tool-setup-v1.md)   
* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Feladatok állapotának áttekintése a másolási naplófájlokkal](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Importálási feladat javítása](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)
