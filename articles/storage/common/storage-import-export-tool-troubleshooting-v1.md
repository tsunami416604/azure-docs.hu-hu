---
title: Az Azure import/export eszköz hibaelhárítása | Microsoft Docs
description: Ismerkedjen meg az Azure import/export eszköz használata során felmerülő gyakori problémákról és azok kezeléséről.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 4eeeb538bcd39eed40a92dd45e7ba7bed25558e2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978407"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Az Azure Import/Export eszköz hibaelhárítása
A Microsoft Azure Import/Export eszköz hibaüzeneteket ad vissza, ha problémákba ütközik. Ez a témakör felsorolja azokat a gyakori problémákat, amelyekkel a felhasználók futhatnak.  

## <a name="a-copy-session-fails-what-i-should-do"></a>A másolási munkamenet meghiúsul, mit kell tennem?  
 Ha egy másolási munkamenet meghiúsul, két lehetőség közül választhat:  

 Ha a hiba újrapróbálható, például ha a hálózati megosztás rövid ideig offline állapotban van, és most újra online állapotú, akkor folytathatja a másolási munkamenetet. Ha a hiba nem újrapróbálható, például ha helytelen forrásfájl-könyvtárat adott meg a parancssori paraméterekben, meg kell szakítania a másolási munkamenetet. A másolási munkamenetek folytatásával és megszakításával kapcsolatos további információkért lásd: [merevlemezek előkészítése importálási](../storage-import-export-tool-preparing-hard-drives-import-v1.md) feladatokhoz.  

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Nem tudom folytatni vagy megszakítani a másolási munkamenetet.  
 Ha a másolási munkamenet a meghajtó első másolási munkamenete, akkor a hibaüzenetnek meg kell jelennie: "az első másolási munkamenet nem folytatható és nem szakítható meg." Ebben az esetben törölheti a régi naplófájlt, és újra futtathatja a parancsot.  

 Ha a másolási munkamenet nem az első egy meghajtóhoz, akkor mindig folytatható vagy megszakítható.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Elveszítettem a naplófájlt, továbbra is létrehozhatom a feladatot?  
 A meghajtóhoz tartozó naplófájl tartalmazza az adatoknak a meghajtóra másolásának teljes adatait, és szükség van további fájlok hozzáadására a meghajtóhoz, és az importálási feladatok létrehozásához lesz használva. Ha a naplófájl elvész, a meghajtó összes másolási munkamenetét újra meg kell ismételni.  

## <a name="next-steps"></a>Következő lépések

* [Az Azure import/export eszköz beállítása](../storage-import-export-tool-setup-v1.md)   
* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Importálási feladat javítása](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)
