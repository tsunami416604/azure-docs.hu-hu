---
title: Az Azure Import/Export eszköz hibaelhárítása |} A Microsoft Docs
description: Megismerheti a leggyakoribb problémáinak, az Azure Import/Export eszközzel, és hogyan kezelje őket használata esetén.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 9a4e47143515c7f9c21d701809c35d61853d91ec
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471912"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Az Azure Import/Export eszköz hibaelhárítása
A Microsoft Azure Import/Export eszköz hibaüzenetek adja vissza, ha fut problémákat. Ez a témakör néhány olyan gyakori problémát, hogy a felhasználók megadtuk sorolja fel.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>A másolási munkamenet nem sikerül, mit kell tenni?  
 Ha a másolási munkamenet nem sikerül, két lehetőség van:  
  
 Ha a hiba – Újrapróbálkozást lehetővé tevő, például ha a hálózati megosztás egy rövid ideig offline állapotban volt, és most újra online állapotba kerül, a másolási munkamenet folytathatja. Ha a hiba nem Újrapróbálkozást lehetővé tevő, például ha a parancssori paramétereit a megfelelő forrás könyvtárát megadott, a másolási munkamenet megszakítása szeretne. Lásd: [merevlemezek szolgáltatás előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md) folytatása és a munkamenetek másolás megszakítása további információt.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Nem lehet folytatni vagy egy másolási munkamenet megszakítása.  
 Ha a másolási munkamenet az első másolás munkamenet-meghajtó, a következő hibaüzenet jelenik meg állapotban: "Az első másolás munkamenet nem folytathatók vagy megszakítva." Ebben az esetben törölje a régi journal-fájlt, és futtassa újra a parancsot.  
  
 Ha a másolási munkamenet nem az elsőt a meghajtó, azt is mindig folytathatók vagy megszakítva.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>A naplófájl elvész, így is létrehozhatok a feladatot?  
 A naplófájl-meghajtó az adatok másolása a meghajtón a teljes körű információkat tartalmaz, és szükség esetén vegyen fel további fájlokat a meghajtóra és importálási feladat létrehozására használható. Ha a naplófájl elveszik, akkor ismételje meg a meghajtó másolása munkamenetek.  
  
## <a name="next-steps"></a>További lépések
 
* [Az azure import/export eszköz telepítése](../storage-import-export-tool-setup-v1.md)   
* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Importálási feladat javítása](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)
