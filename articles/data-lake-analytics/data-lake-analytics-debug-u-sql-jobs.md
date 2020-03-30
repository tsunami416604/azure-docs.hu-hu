---
title: Az Azure Data Lake U-SQL-feladatok C# kódja
description: Ez a cikk ismerteti, hogyan hibakeresés egy U-SQL sikertelen csúcspont az Azure Data Lake Tools for Visual Studio használatával.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 72239fc1679d2ebbfd9c9b5be6b79b58efb760cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315815"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Hibakeresés, felhasználó által definiált C# kód a sikertelen U-SQL-feladatokhoz

Az U-SQL egy bővíthetőségi modellt biztosít a C#használatával. Az U-SQL-parancsfájlokban könnyen meghívhat C# függvényeket, és olyan analitikus függvényeket hajthat végre, amelyeket az SQL-szerű deklaratív nyelv nem támogat. Ha többet szeretne megtudni az U-SQL bővíthetőségről, olvassa el az [U-SQL programozhatósági útmutatót.](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf) 

A gyakorlatban minden kód hibakeresést igényelhet, de nehéz hibakeresést végezni egy elosztott feladattal, amely nek egyéni kódja van a felhőben, korlátozott naplófájlokkal. [Az Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) egy sikertelen **csúcspont-debug**nevű szolgáltatást biztosít, amely megkönnyíti az egyéni kódban előforduló hibák hibakeresését. Ha az U-SQL-feladat sikertelen, a szolgáltatás megtartja a hibaállapotot, és az eszköz segít letölteni a felhőhiba-környezetet a helyi számítógépre hibakereséshez. A helyi letöltés rögzíti a teljes felhőkörnyezetet, beleértve a bemeneti adatokat és a felhasználói kódot.

Az alábbi videó bemutatja a sikertelen csúcspont-hibakeresést az Azure Data Lake Tools for Visual Studio alkalmazásban.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> A Visual Studio használatához a következő két frissítés szükséges: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) és a Windows Universal C [Runtime](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>A letöltés nem sikerült a helyi számítógépre

Amikor megnyit egy sikertelen feladatot az Azure Data Lake Tools for Visual Studio alkalmazásban, megjelenik egy sárga figyelmeztető sáv, amely részletes hibaüzeneteket tartalmaz a hiba lapon.

1. Kattintson **a Letöltés** gombra az összes szükséges erőforrás és bemeneti adatfolyam letöltéséhez. Ha a letöltés nem fejeződött be, kattintson az **Újra gombra.**

2. Helyi hibakeresési környezet létrehozásához kattintson a **Megnyitás** gombra a letöltés befejezése után. A rendszer új hibakeresési megoldást nyit meg, és ha már meg van nyitva a Visual Studio- ban, a hibakeresés előtt mentse és zárja be.

![Azure Data Lake Analytics U-SQL debug visual studio letöltési csúcspont](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>A hibakeresési környezet konfigurálása

> [!NOTE]
> A hibakeresés előtt ellenőrizze a **Közös nyelvi futásidejű kivételek jelölőnégyzetét** a Kivételbeállítások ablakban (**Ctrl + Alt + E**).

![Az Azure Data Lake Analytics U-SQL debug visual studio beállítása](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

Az új elindított Visual Studio-példányban előfordulhat, hogy nem találja a felhasználó által definiált C# forráskódot:

1. [A megoldásban megtalálom a forráskódot](#source-code-is-included-in-debugging-solution)

2. [Nem találom a forráskódot a megoldásban](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>A forráskód megtalálható a hibakeresési megoldásban

A C# forráskód két esetben kerül rögzítésre:

1. A felhasználói kód a háttérkódban van definiálva (általában egy U-SQL projektben). `Script.usql.cs`

2. A felhasználói kód az U-SQL alkalmazás C# osztálykönyvtár-projektjében van definiálva, és **debug-információval**rendelkező összeállításként van regisztrálva.

Ha a forráskódot importálja a megoldásba, a Visual Studio hibakereső eszközeivel (óra, változók stb.) elháríthatja a problémát:

1. Nyomja le az **F5** billentyűt a hibakeresés megkezdéséhez. A kód addig fut, amíg egy kivétel le nem állítja.

2. Nyissa meg a forráskódfájlt, és állítson be töréspontokat, majd nyomja le **az F5** billentyűt a kód hibakereséséhez.

    ![Az Azure Data Lake Analytics U-SQL hibakeresési kivétele](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>A forráskód nem szerepel a hibakeresési megoldásban

Ha a felhasználói kód nem szerepel a háttérkódfájlban, vagy nem regisztrálta a **kódösszeállítást hibakeresési adatokkal,** akkor a forráskód nem szerepel automatikusan a hibakeresési megoldásban. Ebben az esetben további lépésekre van szükség a forráskód hozzáadásához:

1. Kattintson a jobb gombbal **a "VertexDebug" megoldásra > > meglévő projekt hozzáadása...** az összeállítás forráskódjának megkereséséhez és a projekt hibakeresési megoldáshoz való hozzáadásához.

    ![Azure Data Lake Analytics U-SQL hibakeresési projekt hozzáadása](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. A **FailedVertexDebugHost** projekt projektelérési útjának beszereznie. 

3. Kattintson a jobb gombbal **a hozzáadott összeállítási forráskódprojektre > Tulajdonságok parancsra,** válassza a bal oldali **Build** lapot, és illessze be a \bin\debug végződésű átmásolt görbét **kimenetként > kimeneti elérési útként.** A végső kimeneti `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`útvonal olyan, mint a .

    ![Az Azure Data Lake Analytics U-SQL hibakeresési készlete pdb elérési útja](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

A beállítások után kezdje el a hibakeresést **az F5-tel** és a töréspontokkal. A Visual Studio hibakereső eszközeivel (óra, változók stb.) is elháríthatja a problémát.

> [!NOTE]
> A kód módosítása után minden alkalommal építse újra a kódösszeállítás forráskódprojektjét a frissített .pdb fájlok létrehozásához.

## <a name="resubmit-the-job"></a>A feladat újraküldése

Hibakeresés után, ha a projekt sikeresen befejeződik, a kimeneti ablak a következő üzenetet jeleníti meg:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Az Azure Data Lake Analytics U-SQL hibakeresési művelete sikeres](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

A sikertelen feladat újbóli elküldése:

1. A háttérkódokkal rendelkező feladatok esetén másolja a C# kódot a `Script.usql.cs`mögötti kód forrásfájlba (általában).

2. A szerelvényekkel rendelkező feladatok esetén kattintson a jobb gombbal a kódösszeállítás forráskód-projektjére a hibakeresési megoldásban, és regisztrálja a frissített .dll-szerelvényeket az Azure Data Lake-katalógusba.

3. Küldje el újra az U-SQL feladatot.

## <a name="next-steps"></a>További lépések

- [U-SQL programozhatósági útmutató](data-lake-analytics-u-sql-programmability-guide.md)
- [U-SQL felhasználó által definiált operátorok fejlesztése Azure Data Lake Analytics-feladatokhoz](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [U-SQL feladatok tesztelése és hibakeresése helyi futtatással és az Azure Data Lake U-SQL SDK használatával](data-lake-analytics-data-lake-tools-local-run.md)
- [Arendellenes ismétlődő feladat elhárítása](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
