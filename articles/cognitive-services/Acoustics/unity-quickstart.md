---
title: A Project Acoustics rövid útmutatója az Egységgel
titlesuffix: Azure Cognitive Services
description: A mintatartalom malmára használhatja a Project Acoustics tervezési vezérlőivel való kísérletezést az Unity programban, és telepítheti őket a Windows Asztali számítógépre.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72243025"
---
# <a name="project-acoustics-unity-quickstart"></a>Project Acoustics Unity rövid útmutató
A Project Acoustics mintatartalommal kísérletezhet a szimulációáltal támogatott tervezési vezérlőkkel.

Szoftverkövetelmények:
* [Unity 2018.2+](https://unity3d.com) Windows-hoz
* [Project Acoustics mintatartalom-csomag](https://www.microsoft.com/download/details.aspx?id=57346)

Mit tartalmaz a mintacsomag?
* Egységjelenet geometriával, hangforrásokkal és játékmenet-vezérlőkkel
* Project Akusztika beépülő modul
* Sült akusztikai eszközök a jelenet

## <a name="import-the-sample-package"></a>A mintacsomag importálása
Importálja a mintacsomagot egy új Unity projektbe.
1. A Unity, go to **Assets** > **Import Package** > **Custom Package**.

    ![Az Egység importálási csomag beállításai](media/import-package.png)  

1. Válassza **a ProjectAcoustics.unitypackage lehetőséget.**

1. Az **Importálás** gombra kattintva integrálhatja az Egység csomagot a projektbe.  
  
    ![A Unity importálási csomag párbeszédpanel](media/import-dialog.png)  

A csomag importálásához egy meglévő projektbe, [lásd: Unity integráció](unity-integration.md) további lépéseket és megjegyzéseket.

>[!NOTE]
>Az importálás befejezése után több hibaüzenet jelenik meg a konzolnaplóban. Folytassa a következő lépéssel, és indítsa újra az Egységet.

## <a name="restart-unity"></a>Egység újraindítása
Az akusztikai eszközkészlet sütni része a .NET 4.the bake portion of the acoustics toolkit requires the .NET 4. *x* scripting runtime verzió. A csomagimportálás frissíti a Unity lejátszó beállításait. A beállítás érvénybe léptetéséhez indítsa újra az Egységet. A beállítás érvénybe lépésének ellenőrzéséhez nyissa meg a **Lejátszó** beállításait:

![A Unity projekt beállítások menüje](media/player-settings.png)  

![A Unity Player Settings panel en .NET 4.x van kiválasztva](media/net45.png)  

>[!NOTE]
>Ez a képernyőkép a Unity 2018-ból készült. *x*. A kép eltérhet az Unity újabb verzióiban.

## <a name="open-the-project-acoustics-bake-window"></a>A Project Acoustics bake ablakának megnyitása
A Unity menüben válassza az **Ablak** menü **Akusztika** parancsát.

![Az Ablak menüBen kiemelt Akusztika beállítással ellátott Unity-szerkesztő](media/window-acoustics.png)

Megnyílik egy lebegő **akusztikaablak.** Ebben az ablakban állíthatja be az akusztikai szimuláció tulajdonságait.

![A Unity szerkesztő nyitott Akusztika ablakkal](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Kísérletezzen a tervezési vezérlőkkel
Nyissa meg a mintajelenetet a *ProjectAcousticsSample* mappában, és válassza ki a lejátszás gombot az Egység szerkesztőben. Használja a W, A, S, és D gombokat, és az egér mozogni. Ha össze szeretné hasonlítani, hogy a jelenet hogyan szólal jon fel akusztikával és anélkül, válassza az R billentyűt, amíg az átfedési szöveg pirosra nem vált, és az "Akusztika: Letiltva" szöveg jelenik meg. További vezérlők billentyűparancsainak megtekintéséhez válassza az F1 lehetőséget. A művelet jobb gombbal való kijelöléséhez, majd a művelet hez a bal gombbal kattintva is kattinthat.

Az *AcousticsAdjust* parancsfájl a mintajelenet hangforrásaihoz van csatolva. Lehetővé teszi a forrásonkénti tervezési paramétereket.

![A Unity AcousticsAdjust szkript](media/acoustics-adjust.png)

A következő szakaszok a rendelkezésre álló vezérlők használatával létrehozható hatások némelyikét ismertetik. Az egyes vezérlőkről a [Project Acoustics Unity Design oktatóanyaga című](unity-workflow.md)témakörben talál részletes információt.

### <a name="modify-distance-based-attenuation"></a>Távolságalapú csillapítás módosítása
A **Project Acoustics** Unity spatializer plug-in audio digitális jelfeldolgozása tiszteletben tartja az Egység szerkesztőbe beépített, forrásonkénti távolságalapú csillapítást. A távolságalapú csillapítás vezérlői az **Audio Source** összetevőben találhatók, amely a **3D hangbeállítások**alatt található a hangforrások **felügyelő** paneljén:

![Az Egység távolságcsillapítási beállítások panel](media/distance-attenuation.png)

A Project Acoustics egy "szimulációs régió" mezőben számít, amely a játékos helye köré épül. A mintacsomagban lévő akusztikai eszközök a lejátszó körül 45 méter hosszú szimulációs régióban sültek. Tehát a hangcsillapítást úgy kell megtervezni, hogy körülbelül 45 m-re csökkenjen.

### <a name="modify-occlusion-and-transmission"></a>Az elzáródás és az átvitel módosítása
* Ha az **Okkluziós** szorzó nagyobb, mint 1 (az alapértelmezett érték 1), az elzáródás eltúlzott. Az elzáródási hatás finomabbá tenni, állítsa be kevesebb, mint 1.

* A falon keresztüli átvitel engedélyezéséhez mozgassa a **Sebességváltó (dB)** csúszkát a legalacsonyabb beállítástól.

### <a name="modify-wetness-for-a-source"></a>Forrás nedvességének módosítása
* Ha módosítani szeretné, hogy a távolsággyorsan változik-e a nedvesség, használja **az Észlelési távolsághajlítást**. A szimuláció révén a Project Acoustics kiszámítja a nedves szinteket, amelyek észlelési távolságjelzéseket biztosítanak, és a távolságtól függően zökkenőmentesen változnak. A távolságnövelés eltúlozza ezt a hatást a távolsággal kapcsolatos nedves szintek növelésével. Az 1-nél kisebb hajlítási értékek finomabbá teszik a távolságalapú visszhangváltozást.

   Ennek finomabb beállításához módosítsa a **Wetness (dB)** beállítást.

* A bomlási idő növeléséhez állítsa be a **bomlási időskálát.** Ha egy adott forrásfigyelő helypár szimulációs eredménye 1,5 másodperces bomlási idő, és a **bomlási időskála** 2, a forrásra alkalmazott bomlási idő 3 másodperc.

## <a name="next-steps"></a>További lépések
* Olvassa el az [Unity-alapú Project Acoustics tervezési vezérlők részleteit.](unity-workflow.md)
* A [tervezési folyamat](design-process.md)mögött megmaradtak fogalmak további feltárása .
* [Hozzon létre egy Azure-fiókot](create-azure-account.md) az elősütési és sütési folyamatok felfedezéséhez.
