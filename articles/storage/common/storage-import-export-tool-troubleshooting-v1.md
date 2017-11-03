---
title: "Az Azure Import/Export eszköz hibaelhárítása |} Microsoft Docs"
description: "További tudnivalók azzal kapcsolatban, az az Azure Import/Export eszköz, és hogyan kezelje őket használata során tapasztalt gyakori problémákat."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: b91ca5eb-c557-460a-9afc-0590b38471f9
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 7bfda602dbc0ea47828a7c9243b8b9b09ec78432
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Az Azure Import/Export eszköz hibaelhárítása
A Microsoft Azure Import/Export eszköz hibaüzeneteket ad vissza, ha fut a problémákat. Ez a témakör néhány gyakori probléma, amely futtathatják azokat.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>A másolási munkamenet nem sikerül, mit kell tenni?  
 A másolási munkamenet sikertelensége esetén van két lehetőség közül választhat:  
  
 Ha a hiba Újrapróbálkozást lehetővé tevő, például ha a hálózati megosztás rövid ideig offline állapotban volt, és most újra online állapotba kerül, a Másolás munkamenet folytathatja. Ha a hiba nem Újrapróbálkozást lehetővé tevő, például ha a fájl nem megfelelő forráskönyvtár szerepel a parancssori paraméterek, a Másolás munkamenet megszakítása szeretné. Lásd: [előkészítése merevlemezek hogy az importálás](../storage-import-export-tool-preparing-hard-drives-import-v1.md) folytatása és másolási munkamenet megszakítása további információt.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Nem lehet folytatni vagy egy másolás munkamenet megszakítása.  
 Ha a példány neve az első másolási munkamenet meghajtóhoz, akkor a hibaüzenet a következő kell nyújtaniuk: "az első másolás munkamenet nem folytatható vagy megszakadt." Ebben az esetben törölje a régi napló fájlt, és futtassa újból a parancsot.  
  
 Ha a Másolás munkamenet nincs az elsőt a meghajtók, azt is mindig folytatódik vagy megszakadt.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>A naplófájl elvész, továbbra is létrehozhatók a feladatot?  
 A naplófájl a meghajtók adat másolása az ezen a meghajtón a teljes információkat tartalmaz, és szükség esetén vegyen fel további fájlokat a meghajtóra és az importálási feladat létrehozására használható. Ha a naplófájl elveszik, akkor végezze el újra a meghajtó másolása munkamenetek.  
  
## <a name="next-steps"></a>Következő lépések
 
* [Az azure import/export eszköz beállítása](../storage-import-export-tool-setup-v1.md)   
* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Importálási feladat javítása](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)
