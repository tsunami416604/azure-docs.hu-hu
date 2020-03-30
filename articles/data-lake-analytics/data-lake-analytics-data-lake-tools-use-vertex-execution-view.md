---
title: Vertex végrehajtási nézet a Data Lake tools for Visual Studio alkalmazásban
description: Ez a cikk ismerteti, hogyan használhatja a Vertex végrehajtási nézet a Data Lake Analytics-feladatok vizsgálatához.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: f5adbb75e6852551976aa040a1a1c723d2e3f59b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309719"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>A Vertex végrehajtási nézet használata a Data Lake Tools for Visual Studio alkalmazásban
Ismerje meg, hogyan használhatja a Vertex végrehajtási nézetet a Data Lake Analytics-feladatok levizsgáztatására.


## <a name="open-the-vertex-execution-view"></a>A Csúcspont végrehajtási nézetmegnyitása
Nyisson meg egy U-SQL feladatot a Data Lake Tools for Visual Studio alkalmazásban. Kattintson a bal alsó sarokban a **Vertex végrehajtási nézet** elemre. Előfordulhat, hogy a rendszer először a profilok betöltésére kéri, és a hálózati kapcsolattól függően eltarthat egy ideig.

![Data Lake Analytics eszközök vertex végrehajtási nézete](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>A Csúcspont végrehajtási nézetének ismertetése
A Vertex végrehajtási nézet három részből áll:

![Data Lake Analytics eszközök vertex végrehajtási nézete](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

A bal oldali **Csúcspont választóval** a csúcspontokat funkciók (például a 10 legfontosabb adat olvasása vagy színpad szerint) alapján választhatja ki. Az egyik leggyakrabban használt szűrő a **kritikus út csúcspontjainak**megtekintése. A **kritikus út** az U-SQL-feladat csúcspontjainak leghosszabb lánca. A kritikus út megértése akkor hasznos, ha optimalizálja a feladatokat, annak ellenőrzésével, hogy melyik csúcspont tart a leghosszabb ideig.
  
![Data Lake Analytics eszközök vertex végrehajtási nézete](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

A felső középső ablaktábla **az összes csúcsfutás állapotát**mutatja.
  
![Data Lake Analytics eszközök vertex végrehajtási nézete](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Az alsó középső ablaktábla az egyes csúcspontok adatait jeleníti meg:
* Folyamat neve: A csúcspont példány neve. A StageName| Csúcspontnév| VertexRunInstance. Például a SV7_Split[62].v1 csúcspont a vertex 62-es számának második futó példányát (.v1, index 0-tól kezdődően) jelenti a SV7_Split szakaszban.
* Összes adat olvasása/írása: Az adatokat ez a csúcspont olvasta/írta.
* Állapot/kilépésállapota: A végső állapot, amikor a csúcspont véget ér.
* Kilépési kód/hiba típusa: A hiba, amikor a csúcspont nem sikerült.
* Létrehozás oka: Miért jött létre a csúcspont.
* Erőforrás-késés/folyamat késése/PN-várólista késése: a csúcspont várakozási ideje az erőforrások, az adatok feldolgozása és a várólistában maradás.
* Folyamat/létrehozó GUID: GUID az aktuálisfutó csúcsponthoz vagy annak létrehozójához.
* Verzió: a futó csúcspont N-edik példánya (a rendszer több okból is ütemezheti a csúcspont új példányait, például feladatátvétel, számítási redundancia stb.)
* Verzió létrehozási ideje.
* Folyamat létrehozási idő/folyamat várólistára helyezett idő/folyamat kezdési ideje/folyamat befejezési ideje: amikor a csúcspont folyamat elindul a létrehozás; amikor a csúcspont folyamat a várólista bakezd; amikor az adott csúcspont folyamat elindul; amikor a bizonyos csúcspont befejeződött.

## <a name="next-steps"></a>További lépések
* A diagnosztikai információk naplózása: [Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](data-lake-analytics-diagnostic-logs.md).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Webhelynaplók elemzése az Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md).
* A feladat részleteinek megtekintéséhez olvassa el a [Feladatböngésző és a Feladatnézet használata az Azure Data lake Analytics-feladatokhoz](data-lake-analytics-data-lake-tools-view-jobs.md)
