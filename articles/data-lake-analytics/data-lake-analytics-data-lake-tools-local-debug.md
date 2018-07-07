---
title: Helyileg Azure Data Lake Analytics-kód hibaelhárítása |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Data Lake Tools for Visual Studio helyi munkaállomáson U-SQL-feladatok hibakereséséhez.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: 181512c12c1e72e6aa8205aabd5ea22816d4c5df
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889592"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Helyileg Azure Data Lake Analytics-kód hibaelhárítása

Használhatja az Azure Data Lake Tools for Visual Studio futtatása és az Azure Data Lake Analytics-kód hibaelhárítása a munkaállomáson, mint az Azure Data Lake szolgáltatásban.

Ismerje meg, [U-SQL-szkript futtatása helyi gépen](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Parancsfájlok és C#-szerelvények helyi hibakeresése

A hibakeresési C#-szerelvények nélkül elküldené és regisztrálná őket az Azure Data Lake Analytics szolgáltatásba. Töréspontokat állíthat be a fájl mögötti kódban és a hivatkozott C#-projektben is.

### <a name="to-debug-local-code-in-code-behind-file"></a>Helyi kódok hibakeresése fájl mögötti kódban

1. Állítson be töréspontokat a fájl mögötti kódban.
2. Nyomja le az F5 billentyűt a parancsfájl helyi hibakereséséhez.

> [!NOTE]
   > A következő eljárás csak a Visual Studio 2015 esetében működik. A régebbi kiadásokban lehetséges, hogy kézzel kell megadnia a pdb-fájlokat.  
   >
   >

### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Helyi kódok hibakeresése egy hivatkozott C#-projektben

1. Hozzon létre egy C#-szerelvényprojektet, és állítsa be úgy, hogy hozza létre a kimeneti dll-fájlt.
2. Regisztrálja a dll-fájlt egy U-SQL-kivonat használatával:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Állítson be töréspontokat a C#-kódban.
4. Nyomja le az F5 billentyűt a C#-dll helyileg-fájlra hivatkozó parancsfájl hibakeresése.


## <a name="next-steps"></a>További lépések

- Egy összetettebb lekérdezés megtekintéséhez lásd: [használata Azure Data Lake Analytics webhelyek naplóinak elemzése](data-lake-analytics-analyze-weblogs.md).
- Feladatok részleteinek megtekintéséhez: [használja a Feladatböngésző és a feladat nézet az Azure Data Lake Analytics-feladatok](data-lake-analytics-data-lake-tools-view-jobs.md).
- A vertex végrehajtási nézet használata, lásd: [a Vertex végrehajtási nézet használata a Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
