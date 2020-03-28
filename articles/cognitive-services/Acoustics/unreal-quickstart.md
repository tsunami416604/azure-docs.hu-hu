---
title: Project Akusztika – rövid útmutató az Unreal segítségével
titlesuffix: Azure Cognitive Services
description: A mintatartalom malmára használhatja a Project Akusztika tervezési vezérlőivel való kísérletezést az Unreal és a Wwise alkalmazásban, és telepítheti őket a Windows asztali környezetére.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72242923"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Projekt akusztika Unreal/Wwise rövid útmutató
Ebben a rövid útmutatóban kísérletezhet a Project Akusztika tervezési vezérlőivel az Unreal Engine és a Wwise mintatartalmának használatával.

A mintatartalom használatára vonatkozó szoftverkövetelmények:
* [Irreális motor](https://www.unrealengine.com/) 4,22
* [Audiokinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>A mintacsomag letöltése
Töltse le a [Project Acoustics Unreal és a Wwise mintacsomagot.](https://www.microsoft.com/download/details.aspx?id=58090) A mintacsomag a következőket tartalmazza:
- Unreal Engine projekt
- Wwise projekt az Unreal projekthez
- Project Akusztika Wwise plug-in

## <a name="set-up-the-project-acoustics-sample-project"></a>A Project Acoustics mintaprojekt beállítása
Először telepítse a Project Acoustics beépülő modult a Wwise-ba. Ezután telepítse a Wwise bináris okat az Unreal projektbe. Ezután állítsa be a Wwise Unreal beépülő modult a Project Acoustics támogatásához.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>A Project Acoustics Wwise beépülő modul telepítése
Nyissa meg a Wwise launchert. A **Beépülő modulok** lap **Új beépülő modulok telepítése csoportjában**válassza **a Hozzáadás a könyvtárból**lehetőséget. Válassza ki a letöltött csomagban szereplő *AcousticsWwisePlugin\ProjectAcoustics* könyvtárat.

![A Wwise beépülő modul telepítésének lehetősége a Wwise Launcherben](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Wwise bináris fájlok hozzáadása a Project Acoustics Unreal mintaprojekthez
1. A Wwise Launcher ben válassza az **Unreal Engine (Unreal Engine)** lapot. 
1. Válassza a **Legutóbbi Unreal Engine Projects (Legutóbbi Unreal Engine Projects )** melletti "hamburger" (ikon) menüt, majd a Projekt **tallózása parancsot.** Nyissa meg a minta Unreal project *.uproject* fájlt az *AcousticsSample\AcousticsGame\AcousticsGame.uproject csomagban.*

   ![Az Unreal lap a Wwise Launcher](media/wwise-unreal-tab.png)

3. A Project Acoustics mintaprojekt mellett válassza **a Wwise integrálása a projektbe**lehetőséget.

   ![A Wwise Launcher az Acoustics Game Unreal projektet mutatja, kiemelt Integrálás lehetőséggel.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>A Wwise Unreal plug-in funkcióinak bővítése
A Project Acoustics Unreal plug-in további viselkedésre van szüksége a Wwise Unreal plug-in API-ban. Futtassa a Project Acoustics Unreal beépülő modulhoz elérkezett kötegfájlt a módosítások automatizálásához.
* Az *AcousticsGame\Plugins\ProjectAcoustics\Resources mappában*futtassa a *PatchWwise.bat parancsot.*

    ![A Wwise projekt javításához a Windows Intéző ablakában kiemelt parancsfájl](media/patch-wwise-script.png)

* Ha nincs telepítve a DirectX SDK: A használt Wwise verziójától függően előfordulhat, hogy megjegyzést kell `DXSDK_DIR` fűznie az *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs.* sorához:

    ![A kód szerkesztő bemutatás " DXSDK" magyarázat ki](media/directx-sdk-comment.png)

* Ha a Visual Studio 2019 használatával fordít: A Wwise összekapcsolási hibájának megkerüléséhez manuálisan módosítsa a *VSVersion* alapértelmezett értéket az *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs fájlban* **a vc150-re:**

    ![A KÓD szerkesztő -val VSVersion változtatott -hoz " vc150"](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Az Unreal Projekt megnyitása 
Amikor megnyitja az Unreal projektet, kérni fogja a modulok újraépítését. Válassza az **Igen** lehetőséget.

Ha a projekt megnyitása buildhibák miatt sikertelen, ellenőrizze, hogy telepítette-e a Project Acoustics Wwise beépülő modult a Wwise ugyanazon verziójára, mint amelyet a Project Acoustics mintaprojektben használt.

Ha a 2019.1-es verziónál korábbi [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) verziót használ, a Project Acoustics mintaprojekt teljében nem hozhat létre hangbankokat. Integrálnia kell a Wwise 2019.1-es verzióját a mintaprojektbe.

## <a name="experiment-with-project-acoustics-design-controls"></a>Kísérletezés a Project Akusztika tervezési vezérlőivel
Hallgassa meg, hogyan hangzik a jelenet kiválasztásával a lejátszás gombot az Unreal szerkesztő. Használja a W, A, S, és D gombokat, és az egér mozogni. A további vezérlők billentyűparancsainak megtekintéséhez válassza az F1 lehetőséget.

Az alábbi információk néhány kipróbálandó tervezési tevékenységet ismertetik.

### <a name="modify-occlusion-and-transmission"></a>Az elzáródás és az átvitel módosítása
Minden Unreal hangszereplőn a Project Acoustics tervvezérlői vannak.

![Az Unreal Editor akusztikai tervezési vezérlői](media/demo-scene-sound-source-design-controls.png)

Ha az **Okkluziós** szorzó nagyobb, mint 1 (az alapértelmezett érték 1), az elzáródás eltúlzott. Az 1-nél kisebb beállítás finomabbá teszi az elzáródási hatást.

A falon keresztüli átvitel engedélyezéséhez mozgassa a **Sebességváltó (dB)** csúszkát a legalacsonyabb szintről.

### <a name="modify-wetness-for-a-source"></a>Forrás nedvességének módosítása
Ha módosítani szeretné, hogy a távolsággyorsan változik-e a nedvesség, használja **az Észlelési távolsághajlítást**. A Project Acoustics szimulációval számítja ki a nedves szinteket az egész térben. A szintek zökkenőmentesen változnak a távolság és az észlelési távolság dákó. Hogy eltúlozza ezt a hatást, növelje a távolsághoz kapcsolódó nedves szinteket, hogy növelje a távolságláncot. Az 1-nél kisebb hajlítási értékek finomabbá teszik a távolságalapú visszhangváltozást. Ezt a hatást a **Wetness (dB)** beállítással is finomabban is módosíthatja.

A bomlási idő növeléséhez állítsa be a **bomlási időskálát.** Vegyünk egy olyan esetet, amikor a szimuláció eredménye 1,5 másodperces bomlási idő. A **Bomlási időskála 2-re** állítása 3 másodperces bomlási időt eredményez a forrásra.

### <a name="modify-distance-based-attenuation"></a>Távolságalapú csillapítás módosítása
A Project Acoustics Wwise keverő plug-in tiszteletben tartja a wwise-ba beépített forrástávolság-alapú csillapítást. Az ív módosítása megváltoztatja a száraz görbe szintjét. A Project Acoustics plug-in a szimulációs és tervezési vezérlők által meghatározott nedves/száraz keverék fenntartásához állítja be a nedves szintet.

![A Wwise csillapítási görbe panel, amely a csillapítást 0-ra mutatja a szimuláció határa előtt](media/demo-sounds-attenuation.png)

A Project Acoustics egy "szimulációs régió" mezőben számít, amely az egyes szimulált játékosok helye köré épül. A mintacsomaga akusztikai eszközeit 45 méteres szimulációs régiósugarával sütötték. A csillapítást úgy tervezték, hogy 45 méter előtt 0-ra essen. Bár ez a bukás nem szigorú követelmény, azt a kikötést hordozza, hogy csak a hallgató 45 méteres geometriája elzárja a hangokat.

## <a name="next-steps"></a>További lépések
* [Integrálja a Project Acoustics](unreal-integration.md) beépülő modult az Unreal projektbe.
* [Hozzon létre egy Azure-fiókot](create-azure-account.md) a saját sütéseihez.
