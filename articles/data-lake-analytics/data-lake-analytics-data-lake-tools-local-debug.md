---
title: Az Azure Data Lake Analytics kód helyi hibakeresése
description: Megtudhatja, hogy miként használhatja az Azure Data Lake Tools for Visual Studio alkalmazást az U-SQL-feladatok helyi munkaállomáson történő hibakeresésére.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 0827311218202de447e5cf27356e00c4da020e94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61472991"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Az Azure Data Lake Analytics kód helyi hibakeresése

Az Azure Data Lake Tools for Visual Studio segítségével ugyanúgy futtathatja és debugolhatja az Azure Data Lake Analytics-kódot a helyi munkaállomáson, mint az Azure Data Lake Analytics szolgáltatásban.

További információ az [U-SQL parancsfájl helyi számítógépen való futtatásáról.](data-lake-analytics-data-lake-tools-local-run.md)

## <a name="debug-scripts-and-c-assemblies-locally"></a>Parancsfájlok és C#-szerelvények helyi hibakeresése

A C# szerelvényeket hibakeresés nélkül is elláthatja, és regisztrálhatja őket az Azure Data Lake Analytics szolgáltatásba. A töréspontokat a háttérkódfájlban és egy hivatkozott C# projektben is beállíthatja.

### <a name="debug-local-code-in-a-code-behind-file"></a>Helyi kód hibakeresése egy háttérkódfájlban

1. Töréspontok beállítása a mögötti kódfájlban.
2. Válassza **az F5** lehetőséget a parancsfájl helyi hibakereséséhez.

> [!NOTE]
   > Az alábbi eljárás csak a Visual Studio 2015-ben működik. Előfordulhat, hogy a Visual Studio régebbi verzióiban manuálisan kell hozzáadnia a **PDB-fájlokat.**  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Helyi kód hibakeresése egy hivatkozott C# projektben

1. Hozzon létre egy C# összeállítási projektet, és hozza létre a kimeneti **DLL-fájl** létrehozásához.
2. Regisztrálja a **DLL-fájlt** U-SQL utasítással:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Állítson be töréspontokat a C#-kódban.
4. Válassza **az F5** lehetőséget a parancsfájl hibakereséséhez a C# **DLL** fájl helyi hivatkozásával.


## <a name="next-steps"></a>További lépések

- Összetettebb lekérdezéspéldáját a Webhelynaplók elemzése az Azure Data Lake Analytics használatával című [témakörben találja.](data-lake-analytics-analyze-weblogs.md)
- A feladat részleteinek megtekintéséhez olvassa el a [Feladatböngésző és a Feladatnézet használata az Azure Data Lake Analytics-feladatokhoz (Feladatböngésző és feladatnézet használata az Azure Data Lake Analytics-feladatokhoz) témakört.](data-lake-analytics-data-lake-tools-view-jobs.md)
- A csúcspont végrehajtási nézetének használatához olvassa [el A Csúcspont végrehajtási nézet használata a Visual Studio Data Lake Tools alkalmazásban című témakört.](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
