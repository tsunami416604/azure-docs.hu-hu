---
title: Projekt akusztikai gyors üzembe helyezése Unreal-vel
titlesuffix: Azure Cognitive Services
description: A minta tartalmakat a Project akusztikai tervezési vezérlőkkel kísérletezheti az Unreal-ben és a Wwise-ben, és üzembe helyezheti őket a Windows asztalra.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242923"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Projekt akusztikai Unreal/Wwise gyors útmutató
Ebben a rövid útmutatóban kísérletezni fog a Project akusztikai tervezési vezérlőkkel az Unreal Engine és a Wwise minta tartalmának használatával.

A minta tartalmának használatához szükséges szoftverek:
* [Unreal Engine](https://www.unrealengine.com/) 4,22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>A minta csomag letöltése
Töltse le a [Project Acoustics Unreal és Wwise Sample csomagot](https://www.microsoft.com/download/details.aspx?id=58090). A minta csomag a következőket tartalmazza:
- Unreal Engine-projekt
- Wwise projekt az Unreal projekthez
- Projekt akusztikai Wwise beépülő modulja

## <a name="set-up-the-project-acoustics-sample-project"></a>A Project akusztikai minta projekt beállítása
Először telepítse a Project Acoustics beépülő modult a Wwise-be. Ezután telepítse a Wwise bináris fájljait az Unreal projektbe. Ezt követően állítsa be a Wwise Unreal beépülő modult a projekt Akusztikaának támogatásához.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>A projekt akusztikai Wwise beépülő moduljának telepítése
Nyissa meg a Wwise-indítót. A **plugins** lap **új beépülő modulok telepítése**területén válassza a **Hozzáadás a címtárból**lehetőséget. Válassza ki a letöltött csomag részét képező *AcousticsWwisePlugin\ProjectAcoustics* könyvtárat.

![A Wwise beépülő modul telepítésének lehetősége a Wwise-Indítóban](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Wwise bináris fájlok hozzáadása a projekt akusztikai Unreal Sample projekthez
1. A Wwise-Indítóban válassza az **Unreal Engine (irreális motor** ) fület. 
1. Válassza a "hamburger" (ikon) menüt a **legutóbbi Unreal Engine-projektek**mellett, majd válassza a **Tallózás a projekthez**lehetőséget. Nyissa meg a minta Unreal Project *. uproject* fájlt a csomag *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![A Wwise-indító Unreal lapja](media/wwise-unreal-tab.png)

3. A Project akusztikai minta projekt mellett válassza a **Wwise integrálása a projektben**lehetőséget.

   ![A Wwise-indító a akusztikai játékok Unreal-projektjét mutatja be, az integráció lehetőség kiemelve.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>A Wwise Unreal beépülő modul funkcióinak kiterjesztése
A projekt akusztikai Unreal beépülő moduljának a Wwise Unreal beépülő modul API-ból elérhető további viselkedésre van szüksége. A módosítások automatizálása érdekében futtassa a projekthez tartozó Acoustics Unreal beépülő modullal rendelkező batch-fájlt.
* A *AcousticsGame\Plugins\ProjectAcoustics\Resources*-on belül futtassa a *PatchWwise. bat fájlt*.

    ![A Windows Intéző ablakban Kiemelt Wwise-projekt javításának parancsfájlja](media/patch-wwise-script.png)

* Ha nincs telepítve a DirectX SDK: az Ön által használt Wwise függően előfordulhat, hogy megjegyzésbe kell tennie a `DXSDK_DIR` *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*tartalmazó sort:

    ![A "DXSDK" nevű Kódszerkesztő megjegyzése](media/directx-sdk-comment.png)

* Ha a Visual Studio 2019 segítségével fordítja le a fordítást: a Wwise-mel való összekapcsolási hiba megoldásához manuálisan módosítsa a *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* *VSVersion* alapértelmezett értékét a **vc150**értékre:

    ![A VSVersion a "vc150" értékre módosult](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Az Unreal-projekt megnyitása 
Az Unreal projekt megnyitásakor a rendszer felszólítja a modulok újraépítésére. Válassza az **Igen**lehetőséget.

Ha nem sikerül megnyitni a projektet a létrehozási hibák miatt, ellenőrizze, hogy telepítette-e a Project Acoustics Wwise beépülő modult a Wwise azonos verziójára, amelyet a Project akusztikai minta projektben használt.

Ha a 2019,1-es verziónál korábbi [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) -verziót használ, a Project akusztikai minta projekt használatával nem hozhatja ki a hangbankokat. Integrálnia kell a Wwise 2019,1-es verzióját a Sample projektbe.

## <a name="experiment-with-project-acoustics-design-controls"></a>Kísérletezés a Project akusztikai tervezési vezérlőkkel
Figyelje meg, hogy a jelenet hogyan hangzik, ha az Unreal Editor Play (lejátszás) gombjára kattint. A W, A, S és D billentyűk és az egér használatával mozoghat. A további vezérlőkhöz tartozó billentyűparancsok megjelenítéséhez válassza az F1 lehetőséget.

A következő információk ismertetik a kipróbáláshoz szükséges tervezési tevékenységeket.

### <a name="modify-occlusion-and-transmission"></a>Elzáródás és átvitel módosítása
Az egyes Unreal hangszínészek esetében a forrásoldali projekt akusztikai tervezési vezérlői vannak.

![Az Unreal Editor akusztikai kialakításának vezérlői](media/demo-scene-sound-source-design-controls.png)

Ha az **elzáródási** szorzó nagyobb, mint 1 (az alapértelmezett érték 1), a elzáródás eltúlzott. Ha 1-nél kevesebb beállítás van, az elzáródás hatása finomabb.

A falon belüli átvitel engedélyezéséhez helyezze át az **átviteli (db)** csúszkát a legalacsonyabb szintjéről.

### <a name="modify-wetness-for-a-source"></a>A nedvesség módosítása a forrásnál
Ha módosítani szeretné, hogy a nedvesség milyen gyorsan változik a távolsággal, használja az **észlelési távolságot**. A Project Acoustics a teljes terület nedves szintjét a szimuláción keresztül számítja ki. A szintek zökkenőmentesen változnak a távolsággal, és az észlelési távolságra vonatkozó dákót biztosítanak. A hatás eltúlozása érdekében növelje a távolságtal kapcsolatos nedves szinteket a távolság hajlításának növeléséhez. 1-nél kisebb értékek hajlításával a távolságon alapuló visszaverődés-változás finomabb. Ezt a hatást is finomíthatja a **nedvesség (adatbázis)** beállítással.

A terület romlási idejének növeléséhez állítsa be a **bomlás időbeli skáláját**. Vegyünk egy olyan esetet, ahol a szimulációs eredmény 1,5 másodperces bomlási idő. A **romlási idő méretezése** 2 értékre a forrásra alkalmazott 3 másodperces romlási időt eredményez.

### <a name="modify-distance-based-attenuation"></a>Távolságon alapuló csillapítás módosítása
A Project akusztikai Wwise keverő beépülő modulja a Wwise beépített, a forráson alapuló távolsági csillapítást veszi figyelembe. A görbe módosítása megváltoztatja a száraz elérési út szintjét. A projekt akusztikai beépülő modulja úgy állítja be a nedves szintet, hogy fenntartsa a szimulációs és a kialakítási vezérlők által meghatározott nedves/száraz kombinációt.

![A Wwise gyengítő görbe paneljén a szimulált érték 0 lesz, a szimuláció határa előtt](media/demo-sounds-attenuation.png)

A Project akusztikai számítások egy "szimulációs régió" mezőben, amely az egyes szimulált lejátszók helyét középpontba kerül. A rendszer a mintavételi csomagban található akusztikai eszközöket a szimulációs régió 45 méteres sugarával sütötte. A csillapítások úgy lettek kialakítva, hogy 0 – 45 méter előtt legyenek. Habár ez a fakulás nem szigorú követelmény, az azt eredményezi, hogy a figyelő 45 méteren belüli geometriája occlude a hangokat.

## <a name="next-steps"></a>Következő lépések
* [Integrálja a projekt akusztikai](unreal-integration.md) beépülő modulját az Unreal-projektbe.
* [Hozzon létre egy Azure-fiókot](create-azure-account.md) saját sütni.
