---
title: Projekt akusztikai gyors üzembe helyezése Unreal-vel
titlesuffix: Azure Cognitive Services
description: A mintaszöveg használatával kísérletezzen a Project akusztikai tervezési vezérlőkkel az Unreal-ben és a Wwise-ben, és telepítse a Windows asztalra.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 06023b2758d09fe8ebe7c1301ef1a03d9c54aa41
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704771"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Projekt akusztikai Unreal/Wwise gyors útmutató
Ebben a rövid útmutatóban a Project akusztikai tervezési vezérlőkkel kísérletezik a megadott minta tartalmakat használva az Unreal Engine és a Wwise számára.

A szoftverre vonatkozó követelmények:
* [Unreal Engine](https://www.unrealengine.com/) 4,21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.6

## <a name="download-the-sample-package"></a>A minta csomag letöltése
Töltse le a [Project Acoustics Unreal + Wwise minta csomagot](https://www.microsoft.com/download/details.aspx?id=58090). A mintakód egy Unreal Engine-projektet, az adott Unreal-projekthez tartozó Wwise-projektet és a Project Acoustics Wwise beépülő modulját tartalmazza.

## <a name="set-up-the-project-acoustics-sample-project"></a>A Project akusztikai minta projekt beállítása
Ahhoz, hogy beállítsa a projekt akusztikai Unreal/Wwise minta projektjét, először telepítenie kell a Project akusztikai beépülő modult a Wwise-be. Ezután telepítse a Wwise bináris fájljait az Unreal projektbe, és állítsa be a Wwise Unreal beépülő modulját a projekt Akusztikaének támogatásához.

### <a name="install-the-project-acoustics-wwise-plugin"></a>A Project akusztikai Wwise beépülő modul telepítése
Nyissa meg a Wwise-indítót, majd a **plugins** ( **új beépülő modulok telepítése**) lapon válassza a **Hozzáadás a címtárból**lehetőséget. Válassza ki `AcousticsWwisePlugin\ProjectAcoustics` a letöltött csomag részét képező könyvtárat.

![Képernyőkép a Wwise-Indítóról a Wwise beépülő modul telepítése lehetőséggel](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Wwise bináris fájlok hozzáadása a projekt akusztikai Unreal Sample projekthez
A Wwise-Indítóban kattintson az **Unreal Engine (irreális motor** ) fülre, majd a **legutóbbi Unreal Engine-projektek** melletti hamburger menüre, és válassza a **Tallózás a projekthez**lehetőséget. Nyissa meg a minta `.uproject` Unreal Project fájlt a `AcousticsSample\AcousticsGame\AcousticsGame.uproject`csomagban.

![Képernyőkép a Wwise Launcher Unreal lapról](media/wwise-unreal-tab.png)

Ezután a projekt akusztikai minta projekt mellett kattintson a **Wwise integrálása a projektben**elemre.

![Képernyőkép a Wwise-Indítóról, amely a akusztikai játékok Unreal-projektjét mutatja](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>A Wwise Unreal beépülő modul funkcióinak kiterjesztése
A Project akusztikai Unreal beépülő moduljának további viselkedést kell kitennie a Wwise Unreal beépülő modul API-ból. A következő módosítások automatizálásához futtassa a Project Acoustics Unreal beépülő modullal megadott batch-fájlt:
* Belül `AcousticsGame\Plugins\ProjectAcoustics\Resources`futtassa a `PatchWwise.bat`parancsot.

    ![A Windows Intéző ablakának képernyőképe, amely a Wwise-projekt javításának parancsfájlját mutatja](media/patch-wwise-script.png)

* Ha nincs telepítve a DirectX SDK, attól függően, hogy milyen verziójú Wwise használ, érdemes megadnia a következőt `DXSDK_DIR` `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`tartalmazó sort:

    ![Képernyőkép a Kódszerkesztő DXSDK](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>Nyissa meg az Unreal projektet. 
A rendszer felkéri a modulok újraépítésére; kattintson az Igen gombra.

>Ha a projekt megnyitása nem sikerül a létrehozási hibáknál, ellenőrizze, hogy telepítette-e a Project Acoustics Wwise beépülő modult a Project akusztikus Wwise használt verzióra.

>Ha nem használja a [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.6-t, újra kell létrehoznia a hangbankokat, mielőtt a hangfelvételt a minta projektben játssza.

## <a name="experiment-with-project-acoustics-design-controls"></a>Kísérletezés a Project akusztikai tervezési vezérlőkkel
Figyelje meg, hogyan hangzik a jelenet az Unreal Editor Play (lejátszás) gombjára kattintva. Az asztalon a W, A, S, D és az egér használatával mozoghat. További billentyűparancsok megtekintéséhez nyomja le az **F1** billentyűt. Íme néhány tervezési tevékenység a kipróbáláshoz:

### <a name="modify-occlusion-and-transmission"></a>Elzáródás és átvitel módosítása
Az egyes Unreal hangszínészek esetében a Source Project akusztikai tervezési vezérlői vannak:

![Az Unreal Editor akusztikai tervezési vezérlőinek képernyőképe](media/demo-scene-sound-source-design-controls.png)

Ha az **elzáródási** szorzó nagyobb, mint 1 (az alapértelmezett érték 1), a elzáródás túlzott lesz. Ha 1-nél kisebb értéket ad meg, az elzáródás hatása finomabb lesz.

A falon belüli átvitel engedélyezéséhez mozgassa az **átviteli (db)** csúszkát a legalacsonyabb szintjéről. 

### <a name="modify-wetness-for-a-source"></a>A nedvesség módosítása a forrásnál
Ha módosítani szeretné, hogy a nedvesség milyen gyorsan változik atávolsággal, használja az észlelési távolsági láncot. A Project Acoustics a teljes területről kiszámítja a nedves szinteket a szimulációból, ami a távolságtól és a megérzékelt távolsági célzástól függ. A távolsági lánc növelésével a távolsághoz kapcsolódó nedves szintek egyre nagyobb mértékben eltúlozzák ezt a hatást. 1-nél kisebb értékek elhajlása esetén a távolságon alapuló visszaverődés sokkal finomabbra változik. Ez a hatás finomabb részletességgel is módosítható a **nedvesség (db)** beállításával.

Növelje a romlási időt a teljes térben a **romlási idő skálázásának**módosításával. Gondolja át azt az esetet, amikor a szimulációs eredmény 1,5 s bomlási idő. Ha a **romlási idő méretezése** 2 értékre van állítva, a rendszer a 3 s forrásra alkalmazza a romlási időt.

### <a name="modify-distance-based-attenuation"></a>Távolságon alapuló csillapítás módosítása
A Project akusztikai Wwise keverő beépülő modulja figyelembe veszi a Wwise-ben beépített, forrásként szolgáló távolságon alapuló gyengülést. A görbe módosítása megváltoztatja a száraz elérési út szintjét. A Project akusztikai beépülő modulja úgy állítja be a nedves szintet, hogy fenntartsa a szimulációs és kialakítási vezérlők által meghatározott nedves szárazanyag-kombinációt.

![Képernyőkép a Wwise csillapítási görbe paneljéről, és a szimuláció határa előtt a gyengítés nulla lesz](media/demo-sounds-attenuation.png)

A Project akusztikai szolgáltatás a szimulált lejátszók helyét középpontba kerülő "szimulációs régió" mezőben számítja ki a számítást. A rendszer az akusztikai eszközöket egy 45 m sugarú szimulációs régióval együtt sütötte, és a gyengítés a 45 m előtt 0-ra csökken. Habár ez a fakulás nem szigorú követelmény, az azt eredményezi, hogy csak a figyelő 45 m-n belüli geometriája occlude a hangokat.

## <a name="next-steps"></a>További lépések
* [A Project Acoustics](unreal-integration.md) beépülő modul integrálása az Unreal-projektbe
* [Azure-fiók létrehozása](create-azure-account.md) beépíteni kívánt saját tartalmak számára


