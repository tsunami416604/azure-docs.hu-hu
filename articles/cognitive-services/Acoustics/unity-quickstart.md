---
title: Projekt akusztikai útmutatója egységgel
titlesuffix: Azure Cognitive Services
description: A mintaszöveg használatával kísérletezzen a Project akusztikai tervezési vezérlőkkel az egységben, és telepítse a Windows asztalra.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 0ea020ca76381a4ae5d6b6e480c94e63f9aa2dab
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933136"
---
# <a name="project-acoustics-unity-quickstart"></a>Projekt akusztikai egység – rövid útmutató
A Project akusztikai minta tartalmának használata az egységhez a szimulált kialakítási vezérlőkkel való kísérletezéshez.

A szoftverre vonatkozó követelmények:
* [Unity 2018.2 +](https://unity3d.com) Windowshoz
* [Projekt akusztikai minta Content Package](https://www.microsoft.com/download/details.aspx?id=57346)

Mit tartalmaz a minta csomag?
* Egységbeli jelenet geometria, hangforrások és gameplay-vezérlőkkel
* Project akusztikai beépülő modul 
* A jelenethez készült sült akusztikai eszközök

## <a name="import-the-sample-package"></a>A mintavételi csomag importálása
Importálja a mintavételi csomagot egy új Unity-projektbe. 
* Az egység területen lépjen az **eszközök > importálás csomag > egyéni csomag...**

    ![Képernyőfelvétel az egység importálása csomag beállításairól](media/import-package.png)  

* Válassza a **ProjectAcoustics. unitypackage elemet.**

* Kattintson az **Importálás** gombra az egység csomag projektbe való integrálásához  
  
    ![Képernyőfelvétel az egység importálási csomag párbeszédpanelről](media/import-dialog.png)  

Ha egy meglévő projektbe importálja a csomagot, tekintse meg a további lépések és megjegyzések [Unity Integration](unity-integration.md) című témakört.

>[!NOTE] 
>Az importálás befejezése után több hiba jelenik meg a konzol naplójában.  Folytassa a következő lépéssel, és indítsa újra az egységet.

## <a name="restart-unity"></a>Egység újraindítása
A akusztikai eszközkészlet sütni részének a .NET 4. x parancsfájl-futtatási verziójának kell lennie. A csomag importálásakor a rendszer frissíti az Unity Player beállításait. A beállítás érvénybe léptetéséhez indítsa újra az egységet.

A beállítás érvénybe léptetéséhez nyissa meg a **lejátszó beállításait**:

![Képernyőfelvétel az Unity Player beállítások panelről](media/player-settings.png)  

![Képernyőfelvétel az Unity Player Settings panelről a .NET 4,5 kiválasztva](media/net45.png)  

>[!NOTE]
>A képernyőkép a 2018. x egységből készült. Az egység újabb verziói eltérőek lehetnek.

## <a name="open-the-project-acoustics-bake-window"></a>A Project akusztikai sütni ablak megnyitása
Válassza az **ablak > akusztika** elemet az egység menüben:

![Képernyőfelvétel az Unity Editor és a akusztikai ablak menüpont kiválasztásával](media/window-acoustics.png)

Ekkor megjelenik az Akusztikusok nevű új lebegő ablak.  Ebben az ablakban van beállítva az akusztikus szimulációk tulajdonságai.

![Képernyőkép az Unity editorról az akusztikus ablak megnyitásával](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-design-controls"></a>Kísérletezés a tervezési vezérlőkkel
Nyissa meg a minta jelenetet a **ProjectAcousticsSample** mappában, és kattintson a Play (lejátszás) gombra az Unity Editorban. Használja a W, A, S, D és az egeret a mozgáshoz. Ha szeretné összehasonlítani, hogy a jelenet akusztika nélkül szólaljon meg, nyomja meg az **R** gombot, amíg az átfedésben lévő szöveg pirosra vált, és a "Akusztika: Letiltva. " További billentyűparancsok megtekintéséhez nyomja le az **F1** billentyűt. A vezérlőket a jobb gombbal kattintva is használhatja, hogy kiválassza a végrehajtandó műveletet, majd a művelet végrehajtásához kattintson a bal gombbal.

A szkript **AcousticsAdjust** a minta jelenetben lévő hangforrásokhoz van csatlakoztatva, ami lehetővé teszi a forrásként szolgáló tervezési paramétereket. 

![Képernyőfelvétel az Unity AcousticsAdjust parancsfájlról](media/acoustics-adjust.png)

A következőkben néhány olyan effektus látható, amely a megadott vezérlőkkel hozható létre. Az egyes vezérlőkkel kapcsolatos részletes információkért tekintse meg a [projekt akusztikai egységének kialakítását ismertető oktatóanyagot](unity-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Távolságon alapuló csillapítás módosítása
A **Project akusztikai** Unity spatializer beépülő modul által biztosított HANGalapú DSP az egység-szerkesztőbe beépített, a forrás távolságon alapuló csillapítást veszi figyelembe. A távolságon alapuló csillapításhoz tartozó vezérlők a hangforrások **felügyelő** paneljén találhatók, a **3D hangbeállítások**alatt:

![Képernyőfelvétel az Unity Distance csillapítási beállításai panelről](media/distance-attenuation.png)

A Project akusztikai szolgáltatás egy "szimulációs régió" mezőben, a lejátszó helyét középpontba kerülő számítást végez. Mivel a mintavételi csomagban található akusztikai eszközök egy, a lejátszót körülvevő 45m-mérettel rendelkeznek, a hangcsillapítást úgy kell kialakítani, hogy körülbelül 45 m körül legyen 0.

### <a name="modify-occlusion-and-transmission"></a>Elzáródás és átvitel módosítása
* Ha az **elzáródási** szorzó nagyobb, mint 1 (az alapértelmezett érték 1), a elzáródás túlzott lesz. Ha 1-nél kisebb értéket ad meg, az elzáródás hatása finomabb lesz.

* A falon belüli átvitel engedélyezéséhez mozgassa az **átviteli (db)** csúszkát a legalacsonyabb szintjéről. 

### <a name="modify-wetness-for-a-source"></a>A nedvesség módosítása a forrásnál
* Ha módosítani szeretné, hogy a nedvesség milyen gyorsan változik atávolsággal, használja az észlelési távolsági láncot. A **Project Acoustics** a teljes területről kiszámítja a nedves szinteket a szimulációból, ami a távolságtól és a megérzékelt távolsági célzástól függ. A távolsági lánc növelésével a távolsághoz kapcsolódó nedves szintek egyre nagyobb mértékben eltúlozzák ezt a hatást. 1-nél kisebb értékek elhajlása esetén a távolságon alapuló visszaverődés sokkal finomabbra változik. Ez a hatás finomabb részletességgel is módosítható a **nedvesség (db)** beállításával.

* A romlási **idő méretének**módosításával növelheti a teljes terület romlási idejét. Ha egy adott forrás-figyelő elhelyezési pár esetén a szimulációs eredmény 1,5 s, a **romlási idő mérete** pedig 2, akkor a forrásra alkalmazott bomlási idő 3 zsírsavak.

## <a name="next-steps"></a>További lépések
* Az [Unity-alapú projekt akusztikai tervezési vezérlőinek](unity-workflow.md) részletes információi
* Ismerje meg a [tervezési folyamat](design-process.md) hátterében rejlő fogalmakat
* [Hozzon létre egy Azure-fiókot](create-azure-account.md) az előzetesen sütni és a sütni folyamatok megismeréséhez

