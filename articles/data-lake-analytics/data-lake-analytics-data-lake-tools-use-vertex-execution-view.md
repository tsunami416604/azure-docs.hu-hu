---
title: A Vertex végrehajtási nézet a Data Lake Tools for Visual Studio használata
description: Ez a cikk ismerteti a Vertex végrehajtási nézet vizsgára a Data Lake Analytics-feladatok használata.
services: data-lake-analytics
ms.service: data-lake-analytics
author: mumian
ms.author: jgao
ms.reviewer: jasonwhowell
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: 9f834d697c0d3fe537bbdb190b0ba0d0f294ac87
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041013"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>A Vertex végrehajtási nézet a Data Lake Tools for Visual Studio használata
Ismerje meg, hogyan vizsgára a Data Lake Analytics-feladatok a Vertex végrehajtási nézet használata.


## <a name="open-the-vertex-execution-view"></a>Nyissa meg a Vertex végrehajtási nézet
Nyissa meg a U-SQL feladatok Data Lake Tools for Visual Studio. Kattintson a **a Vertex végrehajtási nézet** a bal alsó sarokban. Profilok betöltése először kérheti, és a hálózati kapcsolattól függően hosszabb időt is igénybe vehet.

![A Data Lake Analytics-eszközök a Vertex végrehajtási nézet](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>A Vertex végrehajtási nézet ismertetése
A Vertex végrehajtási nézet három részből áll:

![A Data Lake Analytics-eszközök a Vertex végrehajtási nézet](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

A **csúcspont választó** a bal oldali lehetővé teszi, hogy válassza csúcspontok funkcióihoz (mint például az első 10 adatokat olvasni, vagy válassza fázis szerint). A leggyakrabban használt szűrők egyike megtekintéséhez a **csúcspontok a kritikus útvonalat**. A **kritikus útvonalat** egy U-SQL-feladat a csúcspontok a leghosszabb lánc van. A kritikus útvonalat ismertetése hasznos a feladatok optimalizálásához úgy, mely a csúcspont a leghosszabb időt vesz igénybe.
  
![A Data Lake Analytics-eszközök a Vertex végrehajtási nézet](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

A felső center panel megjeleníti a **fut, a rendszer állapotának**.
  
![A Data Lake Analytics-eszközök a Vertex végrehajtási nézet](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Az alsó középső ablaktáblán minden csúcspont vonatkozó információkat tartalmazza:
* Název procesu: A vertex-példány nevét. Különböző részei StageName áll |} VertexName |} VertexRunInstance. Például a SV7_Split [62] .v1 csúcspont jelöli, a második futó példány számára (.v1, 0-val kezdődő indexű) szakaszban SV7_Split 62 csúcspont szám.
* Összesített adatok olvasási/Written: Az adatok nem írt vagy olvasott a csúcspont szerint.
* Állapot-és kilépési állapotát: A végső állapota a csúcspont véget ér.
* Kilépési kód/hiba típusa: A hiba, amikor a csúcspont nem sikerült.
* Létrehozási ok: Miért érdemes a csúcspont jött létre.
* Erőforrás késés/folyamat várakozási ideje vagy PN várólista késés: az idő a csúcspont, erőforrásokra várnia adatok feldolgozásához, és az üzenetsorban maradnak.
* Folyamat/létrehozója GUID-ja: A jelenlegi futó csúcspont vagy létrehozója GUID azonosítója.
* Verzió: az N-edik példányát a futó csúcspont (a a rendszer előfordulhat, hogy ütemezése csúcspontot új példányát, számos oka lehet, például feladatátvételi, számítási redundancia stb.)
* Idő létrehozott verzió.
* Létrehozás kezdési idő/folyamat várólistán idő/folyamat kezdési idő/folyamat Complete feldolgozni idő: a vertex-folyamat indulásakor létrehozása; a csúcspont folyamat indításakor várólistára; az egyes csúcspont folyamat indításakor; Amikor az egyes csúcspont befejeződött.

## <a name="next-steps"></a>További lépések
* A diagnosztikai információk naplózása: [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Az Azure Data Lake Analytics diagnosztikai naplóinak elérése).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Analyze Website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) (Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával).
* Feladatok részleteinek megtekintéséhez: [Feladatböngésző és használata Azure Data lake Analytics-feladatok feladat megtekintése](data-lake-analytics-data-lake-tools-view-jobs.md)
