---
title: A Vertex végrehajtási nézetet, a Data Lake Tools for Visual Studio használata
description: A cikkből megtudhatja, hogyan használható a Vertex végrehajtási nézetet való vizsgálatához Data Lake Analytics-feladatok.
services: data-lake-analytics
ms.service: data-lake-analytics
author: mumian
ms.author: jgao
manager: kfile
editor: jasonwhowell
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: af15bb9fd1131f598dc87f13c4af481b63d023e3
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735441"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>A Vertex végrehajtási nézetet, a Data Lake Tools for Visual Studio használata
Ismerje meg, hogyan használható a Vertex végrehajtási nézetet való vizsgálatához Data Lake Analytics-feladatok.


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

## <a name="next-steps"></a>További lépések
* A diagnosztikai információk naplózása: [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Az Azure Data Lake Analytics diagnosztikai naplóinak elérése).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Analyze Website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) (Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával).
* Feladat részleteinek megtekintése: [használata feladat böngésző és az Azure Data lake Analytics-feladatok feladat megtekintése](data-lake-analytics-data-lake-tools-view-jobs.md)
