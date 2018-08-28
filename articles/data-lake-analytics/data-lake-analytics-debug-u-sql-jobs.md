---
title: Felhasználó által definiált C#-kód nem sikerült az Azure Data Lake U-SQL-feladatok hibakereséséhez
description: Ez a cikk ismerteti az Azure Data Lake Tools for Visual Studio használatával U-SQL sikertelen csúcs hibakeresése.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 1fd1ce54dea672b19baea84bbf3a40b4365093b8
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046218"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Felhasználó által definiált C#-kód hibaelhárítása U-SQL-feladatok sikertelen

U-SQL C# használatával bővíthetőségi modell biztosít. A U-SQL-parancsfájlok könnyebbé vált a C#-függvényeket, és végezze el az analitikai függvények, amely nem támogatja a deklaratív SQL-szerű nyelven. A U-SQL-bővítést további tudnivalókért lásd: [U-SQL programozhatósági útmutató](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

A gyakorlatban kód szükség lehet a hibakeresés, de nehéz a felhő egyéni kóddal elosztott feladat korlátozott naplófájlokat a hibakereséshez. [Azure Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs) nevű szolgáltatást nyújt **csúcspont Debug sikertelen**, amely segít, könnyebben hibakeresést a bekövetkező hibák az egyéni kódot. U-SQL-feladat meghiúsul, ha a szolgáltatás tartja a sikertelen állapota, és az eszköz segítségével, hogy töltse le a felhőkörnyezet hiba a helyi gépen a hibakereséshez. A helyi letöltése a teljes felhőalapú környezetben, beleértve a bemeneti adatokat és a felhasználói kód rögzíti.

A következő videó bemutatja a csúcspont Debug nem sikerült az Azure Data Lake Tools for Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> A Visual Studio a következő két frissítést igényel a funkció használatát: [Microsoft Visual C++ 2015 újraterjeszthető Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) és a [Universal C futásidejű for Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Nem sikerült letölteni a csúcspont helyi számítógépre

Amikor egy sikertelen feladat az Azure Data Lake Tools for Visual Studio nyit meg, egy sárga figyelmeztető sáv a részletes hibaüzeneteket a hiba lapon láthatja.

1. Kattintson a **letöltése** letölteni a szükséges erőforrásokat és a bemeneti streamekhez. Kattintson a letöltés nem fejeződik be, ha **újra**.

2. Kattintson a **nyílt** létrehozni egy helyi hibakeresési környezetben a letöltés befejezése után. Egy új hibakeresési megoldás nyílnak meg, és ha rendelkezik meglévő megoldás megnyitása a Visual Studióban ellenőrizze, hogy mentse és zárja be, mielőtt hibakeresés.

![Az Azure Data Lake Analytics U-SQL hibakeresése a visual studio letöltési csúcspont](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>A hibakeresési környezet konfigurálása

> [!NOTE]
> Előtti, hibakeresés, mindenképp nézze **közös nyelvi futtatókörnyezet kivételek** a kivétel beállítások ablakban (**Ctrl + Alt + E**).

![Az Azure Data Lake Analytics U-SQL hibakeresése a visual studio beállítás](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

Új megjelent a Visual Studio-példányon előfordulhat, hogy, vagy előfordulhat, hogy nem található a felhasználó által definiált C#-forráskódot:

1. [A megoldásban a forráskód is található](#source-code-is-included-in-debugging-solution)

2. [A megoldásban a forráskód nem található](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Hibakeresés megoldás forráskódját tartalmazza

Van, hogy a C#-forráskódot rögzített két esetet:

1. A felhasználói kód a háttérkód fájlban van definiálva (általában Master elnevezésű `Script.usql.cs` a U-SQL-projektben).

2. A felhasználói kód definiált C# hordozhatóosztálytár-projektjének U-SQL-alkalmazáshoz, és a szerelvény regisztrálva **hibakeresési információ**.

A forráskód importálva lett-e a megoldás, ha a Visual Studio hibakereső eszközöket (tekintse meg, változókat, stb.) a probléma elhárításához is használhatja:

1. Nyomja meg **F5** hibakeresésének elkezdéséhez. A kód lefut, amíg egy kivétel által le van állítva.

2. Nyissa meg a forráskód fájlja, és állítson be töréspontokat, majd nyomja le az **F5** hibakeresése a kód lépésről lépésre.

    ![Az Azure Data Lake Analytics U-SQL-hibakeresési kivétel](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Forráskód nem érhető el a megoldás hibakeresés

Ha a felhasználói kód nem szerepel a háttérkód-fájl, vagy fürtszerepkörnév nem regisztrált a szerelvényt **hibakeresési információ**, majd a forráskód nem érhető el automatikusan a hibakeresési megoldásban. Ebben az esetben további lépések hozzáadása a forráskódja lesz szüksége:

1. Kattintson a jobb gombbal **megoldás 'VertexDebug' > hozzáadása > meglévő projekt...**  a szerelvény található a forráskódban, és adja hozzá a projektet a hibakeresési megoldás.

    ![Az Azure Data Lake Analytics U-SQL-hibakeresési projekt hozzáadása](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. A projekt mappa elérési útját **FailedVertexDebugHost** projekt. 

3. Kattintson a jobb gombbal **a szerelvény hozzáadása forráskód Projekt > Tulajdonságok**, jelölje be a **hozhat létre** lapra a bal oldalon, és illessze be a másolt elérési útja, \bin\debug végződő **kimeneti > kimeneti elérési út**. A végeredmény elérési út, például "<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\".

    ![Az Azure Data Lake Analytics U-SQL-hibakeresési pdb elérési útjának beállítása](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Ezek a beállítások után a hibakeresés indítása **F5** és töréspontokat a kiválasztott. A Visual Studio hibakereső eszközöket, (tekintse meg, változókat, stb.) a probléma elhárításához is használhatja.

> [!NOTE]
> Építse újra a szerelvény forráskód projekt minden alkalommal, amikor frissített .pdb fájlok létrehozásához a kód módosítása után.

## <a name="resubmit-the-job"></a>Küldje el újra a feladatot

Hibakereséshez után a projekt sikeresen befejeződik a kimeneti ablakban látható a következő üzenetet:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Az Azure Data Lake Analytics U-SQL-hibakeresési sikeres](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Újraküldeni a sikertelen feladat:

1. Feladatok háttérkód-megoldásokkal, másolja a C#-kódot a háttérkód forrásfájl (általában `Script.usql.cs`).

2. Szerelvények a feladatok kattintson a jobb gombbal a szerelvény forráskód projekt hibakeresési megoldásban, és regisztrálja a frissített .dll szerelvényeket, az Azure Data Lake-katalógusba.

3. Küldje el újra a U-SQL-feladatot.

## <a name="next-steps"></a>További lépések

- [U-SQL programozhatósági útmutató](data-lake-analytics-u-sql-programmability-guide.md)
- [U-SQL-felhasználó által definiált operátorok az Azure Data Lake Analytics-feladatok fejlesztése](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [U-SQL feladatok tesztelése és hibakeresése helyi futtatással és az Azure Data Lake U-SQL SDK használatával](data-lake-analytics-data-lake-tools-local-run.md)
- [Rendellenes ismétlődő feladat hibaelhárítása](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
