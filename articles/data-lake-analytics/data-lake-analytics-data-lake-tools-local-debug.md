---
title: Helyileg Azure Data Lake Analytics-kód hibaelhárítása
description: Ismerje meg, hogyan használható az Azure Data Lake Tools for Visual Studio helyi munkaállomáson U-SQL-feladatok hibakereséséhez.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 9e73fc4db1404842e6d3878d88d8f02511587bc9
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43044977"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Helyileg Azure Data Lake Analytics-kód hibaelhárítása

Használhatja az Azure Data Lake Tools for Visual Studio futtatásához és az Azure Data Lake Analytics-kód hibaelhárítása a helyi munkaállomáson, mint az Azure Data Lake Analytics szolgáltatásban.

Ismerje meg, hogyan [U-SQL-szkript futtatása helyi gépen](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Parancsfájlok és C#-szerelvények helyi hibakeresése

A hibakeresési C#-szerelvények nélkül elküldené és regisztrálná őket az Azure Data Lake Analytics szolgáltatásba. Többek között töréspontokat állíthat be, mind a háttérkód-fájlban, és a egy hivatkozott C#-projektben.

### <a name="debug-local-code-in-a-code-behind-file"></a>A háttérkód fájl helyi kódok hibakeresése

1. Állítson be töréspontokat a háttérkód-fájlban.
2. Válassza ki **F5** a parancsfájl helyi hibakereséséhez.

> [!NOTE]
   > Az alábbi eljárás csak a Visual Studio 2015 működik. A Visual Studio régebbi verzióit, akkor előfordulhat, hogy manuálisan kell hozzáadnia a **PDB** fájlokat.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Egy hivatkozott C#-projektben helyi kódok hibakeresése

1. Hozzon létre egy C#-szerelvényprojektet, és építse fel a kimenet előállítása **DLL** fájlt.
2. Regisztrálja a **DLL** fájlt egy U-SQL-utasítás használatával:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Állítson be töréspontokat a C#-kódban.
4. Válassza ki **F5** a hibakeresést a szkripten lére a C# **DLL** fájlt helyileg.


## <a name="next-steps"></a>További lépések

- Egy összetettebb lekérdezés egy példa: [használata Azure Data Lake Analytics webhelyek naplóinak elemzése](data-lake-analytics-analyze-weblogs.md).
- Feladatok részleteinek megtekintéséhez: [használja a Feladatböngésző és a feladat nézet az Azure Data Lake Analytics-feladatok](data-lake-analytics-data-lake-tools-view-jobs.md).
- A vertex végrehajtási nézet használata, lásd: [a Vertex végrehajtási nézet használata a Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
