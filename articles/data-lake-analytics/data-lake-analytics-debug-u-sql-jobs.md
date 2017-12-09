---
title: "Felhasználó által definiált C# programkódja sikertelen Azure Data Lake U-SQL feladatok Debug |} Microsoft Docs"
description: "Útmutató az Azure Data Lake Tools for Visual Studio használatával U-SQL sikertelen csúcspont hibakeresését."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/31/2017
ms.author: yanacai
ms.openlocfilehash: 739d46753729b70a24dbd3d6e2d78f8513e143e6
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Felhasználó által definiált C# programkódja sikertelen U-SQL feladatok hibakeresése

U-SQL egy C# használatával bővíthetőségi modell biztosít. A U-SQL-parancsfájlok könnyen C#-függvényeket, és hajtsa végre az analitikai függvények nem támogatja az SQL-szerű deklaratív nyelv esetében. A U-SQL bővítést további tudnivalókért lásd: [U-SQL programozástámogatási útmutató](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

A gyakorlatban kódok a hibakeresés esetleg, de erősen korlátozott naplófájlokat egy elosztott feladat a felhő egyéni kód a hibakereséshez. [Azure Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs) nevű funkcióval rendelkezik **csúcspont Debug sikertelen**, mert így könnyebben hibakeresése előforduló hibákat a saját kódjára. U-SQL-feladat sikertelen lesz, ha a szolgáltatás a hibaállapota tartja, és az eszköz segítségével töltse le a felhőkörnyezet hiba a helyi gép a hibakereséshez. A helyi letöltése a teljes felhőkörnyezetben, beleértve a bemeneti adatok és a felhasználói kód rögzíti.

A következő videó bemutatja a csúcspont Debug nem sikerült Azure Data Lake Tools for Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> A Visual Studio Ez a funkció használatához a következő két frissítésekre van szüksége: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) és a [Universal C futásidejű for Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Nem sikerült letölteni a csúcspont helyi géphez

Amikor megnyit sikertelen feladat a Azure Data Lake Tools for Visual Studio, egy sárga értesítési sáv részletes hibaüzeneteket a hiba lapon látható.

1. Kattintson a **letöltése** letölteni a szükséges erőforrások és a bemeneti adatfolyam. Kattintson a letöltés indul el, ha **újra**.

2. Kattintson a **nyitott** helyi hibakeresési környezetben létrehozásához a letöltés befejezése után. Az automatikusan létrehozott és megnyitott egy új Visual Studio-példány és a hibakeresési megoldás.

![Az Azure Data Lake Analytics U-SQL hibakeresési visual studio letöltése csúcspont](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>A hibakeresési környezetben konfigurálása

> [!NOTE]
> Mielőtt hibakeresés, ügyeljen arra, hogy ellenőrizze **közös nyelvi futtatókörnyezet kivételek** a kivétel beállítások ablakban (**Ctrl + Alt + E**).

![Az Azure Data Lake Analytics U-SQL hibakeresési visual studio-beállítás](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

Az új elindított Visual Studio-példány is, vagy előfordulhat, hogy nem található a felhasználó által definiált C# forráskód:

1. [A megoldásban a forráskód is található](#source-code-is-included-in-debugging-solution)

2. [A megoldásban a forráskód nem található](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Forráskód-hibakeresés megoldás tartalmazza

Van két olyan eset, hogy a C# forráskód rögzített:

1. A felhasználói kód a háttérkód fájlban van definiálva (általában nevű `Script.usql.cs` U-SQL projekt).

2. A felhasználói kód definiált C# hordozhatóosztálytár-projektjének U-SQL-alkalmazáshoz, és szerelvény regisztrálva **hibakeresési információ**.

Ha a megoldás forráskódját importálni, használhatja a Visual Studio hibakeresési eszközök (figyelési, változókat, stb.) a probléma elhárításához:

1. Nyomja le az **F5** a hibakeresés. A kód lefut, amíg egy kivétel miatt leállt.

2. Nyissa meg a forráskód fájlja, és állítson be töréspontokat, majd nyomja le az **F5** a kód lépésenkénti hibakereséséhez.

    ![Az Azure Data Lake Analytics U-SQL hibakeresési kivétel](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Forráskód nem szerepel a hibakeresés megoldás

Ha a felhasználói kód nem szerepel a háttérkód fájl, vagy nem igazolta, hogy az exportálásokat tartalmazó szerelvény **hibakeresési információ**, akkor a forráskód nem tartalmazza automatikusan a hibakeresési megoldás. Ebben az esetben szüksége további lépések elvégzésével adja hozzá a forrás-kódot:

1. Kattintson a jobb gombbal **megoldás "VertexDebug" > vegye fel > létező projekt...**  található az összeállítás forráskódot, és adja hozzá a projekt hibakeresési megoldás.

    ![Az Azure Data Lake Analytics U-SQL hibakeresési projekt hozzáadása](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. A projekt mappa elérési útja az beszerzése **FailedVertexDebugHost** projekt. 

3. Kattintson a jobb gombbal **a hozzáadott szerelvény forráskód Projekt > Tulajdonságok**, jelölje be a **Build** bal oldali lapon, és illessze be a másolt elérési útja, \bin\debug végződő **kimeneti > elérési utat a kimeneti**. A végső kimenetet elérési út, például a "<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\".

    ![Az Azure Data Lake Analytics U-SQL hibakeresési pdb elérési útjának beállítása](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Ezek a beállítások követően indítsa el a feltárására **F5** és töréspontok. A Visual Studio hibakereső eszközöket (figyelési, változókat, stb.) a probléma elhárításához is használhatja.

> [!NOTE]
> A szerelvény forráskód projekt újraépítése frissített .pdb fájlok létrehozásához a kód módosítása után minden alkalommal.

## <a name="resubmit-the-job"></a>Küldje el újra a feladatot

Hibakeresési információ után a projekt sikeresen befejeződik a kimeneti ablakban látható a következő üzenetet:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Az Azure Data Lake Analytics U-SQL hibakeresési sikeres](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

A küldje el újra a sikertelen feladatot:

1. A feladatok a háttérkód megoldásokkal, másolja a C#-kódban a háttérkód forrásfájl (általában `Script.usql.cs`).

2. A feladatokat a szerelvények kattintson a jobb gombbal a szerelvény forráskód projekt hibakeresési megoldásban, és regisztrálja a frissített .dll szerelvények, az Azure Data Lake-katalógusba.

3. Küldje el újra a U-SQL-feladatot.

## <a name="next-steps"></a>Következő lépések

- [U-SQL programozástámogatási útmutató](data-lake-analytics-u-sql-programmability-guide.md)
- [Azure Data Lake Analytics-feladatok U-SQL-felhasználó által definiált operátorok fejlesztése](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [U-SQL feladatok tesztelése és hibakeresése helyi futtatással és az Azure Data Lake U-SQL SDK használatával](data-lake-analytics-data-lake-tools-local-run.md)
- [Egy rendellenes ismétlődő feladat hibaelhárítása](data-lake-analytics-data-lake-tools-debug-recurring-job.md)