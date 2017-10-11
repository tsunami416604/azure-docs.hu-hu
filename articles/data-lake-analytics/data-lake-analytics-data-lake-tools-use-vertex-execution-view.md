---
title: "Használja a Vertex végrehajtási nézetet a Data Lake Tools for Visual Studio |} Microsoft Docs"
description: "Ismerje meg, hogyan használható a Vertex végrehajtási nézetet való vizsgálatához Data Lake Analytics-feladatok."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/13/2016
ms.author: jgao
ms.openlocfilehash: b788e7bc8ded86ebd49cc0be73e5b4e1bcbeaba3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>A Vertex végrehajtási nézetet, a Data Lake Tools for Visual Studio használata
Ismerje meg, hogyan használható a Vertex végrehajtási nézetet való vizsgálatához Data Lake Analytics-feladatok.

## <a name="prerequisites"></a>Előfeltételek

U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával vonatkozó általános ismeretekre van szüksége.  Lásd: [oktatóanyag: Data Lake Tools for Visual Studio használatával U-SQL-parancsfájlok fejlesztése](data-lake-analytics-data-lake-tools-get-started.md).

## <a name="open-the-vertex-execution-view"></a>Nyissa meg a Vertex végrehajtási nézetet
Nyissa meg egy U-SQL-feladatot: Data Lake Tools for Visual Studio. Kattintson a **Vertex végrehajtási nézetet** bal alsó sarkában. Kérheti profilokat először betöltése, és a hálózati kapcsolattól függően hosszabb időt is igénybe vehet.

![A Data Lake Analytics eszközök Vertex végrehajtási nézetet](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Vertex végrehajtási nézetet ismertetése
A Vertex végrehajtási nézetet három részből áll:

![A Data Lake Analytics eszközök Vertex végrehajtási nézetet](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

A **csúcspont választó** a bal oldali megadható, hogy a csúcsban jelöl funkciók (például a felső 10 adatokat olvassa el, vagy válasszon fázisa). A leggyakrabban használt szűrők egyik tekintse meg a **kritikus úton csúcsban**. A **kritikus út** a leghosszabb lánc csatlakozik a csúcsban a U-SQL feladatok. A kritikus út ismertetése akkor hasznos, a feladatok optimalizálási ellenőrzésével, mely csúcspont a leghosszabb ideig tart.
  
![A Data Lake Analytics eszközök Vertex végrehajtási nézetet](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

A felső center ablaktáblán látható a **futási állapota a csúcsban**.
  
![A Data Lake Analytics eszközök Vertex végrehajtási nézetet](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Az alsó középső ablaktáblán minden csúcspont információkat jeleníti meg:
* Folyamat neve: A csomópont-példány nevét. A StageName különböző részlegein áll |} VertexName |} VertexRunInstance. Például a SV7_Split [62] .v1 csúcspont jelöli, a második futó példány (.v1, 0 kezdődő indexű) szakaszban SV7_Split 62 csúcspont szám.
* Összesített adatok olvasás/Written: A történt olvasás/írták a csúcspont.
* Állapot-és kilépési állapotát: A végső állapotúvá amikor befejeződik a csomópontra.
* Kilépési kód/hiba típusa: A hiba a csúcspont sikertelen.
* Létrehozási ok: A csúcspont létrehozásának okát.
* Erőforrás késés/folyamat várakozási ideje vagy PN várólista késés: adatok feldolgozására, és ahhoz, hogy a várólistán lévő erőforrások, várja meg a csúcspont igénybe vett idő.
* Folyamat/létrehozó GUID: Az aktuális futó csúcspont vagy létrehozója GUID azonosítója.
* Verzió: az N-edik példányát a futó csúcspont (a a rendszer lehet ütemezni csúcspont új példányát, a számos oka lehet, például a feladatátvétel számítási redundancia stb.)
* Idő létrehozott verzió.
* Feldolgozni létrehozása kezdési idő és dolgozza várakozik idő és dolgozza kezdési időt és dolgozza Complete idő: a csúcspont folyamat indításakor létrehozása; a csomópont folyamat indításakor a várólistára; az egyes csúcspont folyamat indításakor; Amikor az egyes csúcspont befejeződött.

## <a name="next-steps"></a>Következő lépések
* A diagnosztikai információk naplózása: [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Az Azure Data Lake Analytics diagnosztikai naplóinak elérése).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Analyze Website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) (Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával).
* Feladat részleteinek megtekintése: [használata feladat böngésző és az Azure Data lake Analytics-feladatok feladat megtekintése](data-lake-analytics-data-lake-tools-view-jobs.md)
