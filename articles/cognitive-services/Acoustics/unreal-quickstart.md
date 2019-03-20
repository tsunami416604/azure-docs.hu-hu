---
title: Projekt Akusztika rövid útmutató az Unreal
titlesuffix: Azure Cognitive Services
description: Mintatartalmakat próbálhat használja, az Unreal és Wwise tervezési vezérlők projekt Akusztika kísérletezhet, és üzembe helyezése Windows asztali.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 1314e393d292145ef112e700abf6ab1ef199db7d
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138184"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Projekt Akusztika Unreal/Wwise rövid útmutató
Ebben a rövid útmutatóban fog kísérletezhet projekt Akusztika az Unreal Engine és Wwise megadott minta tartalom-Tervező szabályozza.

Szoftverkövetelmények:
* [Unreal Engine 4.21.](https://www.unrealengine.com/)
* [Wwise 2018.1.6](https://www.audiokinetic.com/products/wwise/)

## <a name="download-the-sample-package"></a>A minta-csomag letöltése
Töltse le a [projekt Akusztika Unreal + Wwise minta csomag](http://www.microsoft.com/downloads/details.aspx?FamilyID=f03dff5a-5780-462e-87ef-e6d039d0748d). A minta csomag tartalmazza, az Unreal Engine projekt, az Unreal projektben, és a projekt Akusztika Wwise beépülő modul Wwise projekt.

## <a name="set-up-the-project-acoustics-sample-project"></a>A projekt Akusztika mintaprojektet beállítása
A projekt Akusztika Unreal/Wwise mintaprojektet beállításához kell telepítenie a projekt Akusztika beépülő modul Wwise be. Ezután az Unreal projekt üzembe helyezése a Wwise bináris fájlokat, és állítsa be a Wwise Unreal beépülő modul projekt Akusztika támogatásához.

### <a name="install-the-project-acoustics-wwise-plugin"></a>A projekt Akusztika Wwise beépülő modul telepítése
Nyissa meg Wwise indítója, majd a **beépülő modulok** lap **új beépülő modulok telepítése**, jelölje be **könyvtár hozzáadása a**. Válassza ki a `AcousticsWwisePlugin\ProjectAcoustics` könyvtárban található a letöltött csomagot.

![Wwise beépülő modul telepítése](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>A projekt Akusztika Unreal mintaprojekt Wwise bináris fájlok hozzáadása
Wwise indítója, kattintson a **Unreal Engine** lapfülre, majd kattintson a Tovább gombra a hamburger menü **legutóbbi Unreal Engine projektek** válassza **keresse meg a projekt**. Nyissa meg a mintaprojektet Unreal `.uproject` a csomagban lévő fájlhoz `AcousticsSample\AcousticsGame\AcousticsGame.uproject`.

![Wwise Unreal lap](media/wwise-unreal-tab.png)

A projekt Akusztika mintaprojektet mellett kattintson a **integrálása Wwise projektben**.

![Wwise Akusztika játék Unreal projekt](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>A Wwise Unreal beépülő modul funkciójának bővítése
A projekt Akusztika Unreal beépülő modul további viselkedés szükséges lehet a Wwise Unreal API beépülő modul teszi közzé. Futtassa a parancsfájlt a projekt Akusztika Unreal beépülő modullal, ezek a módosítások automatizálásához biztosított:
* Belül `AcousticsGame\Plugins\ProjectAcoustics\Resources`futtassa `PatchWwise.bat`.

    ![Javítás Wwise parancsfájl](media/patch-wwise-script.png)

* Ha nem rendelkezik a DirectX SDK telepítve van, tegye megjegyzésbe a DXSDK_DIR tartalmazó sort kell `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![DXSDK Comment out](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>Nyissa meg az Unreal projektet. 
Rendszer rákérdez, hogy újraépíthesse modulok; Kattintson az Igen gombra.

Ha a projekt megnyitása sikertelen, az Összeállítási hibák, ellenőrizze, hogy telepítette a projekt Akusztika mintaprojektet a használt Wwise ugyanazt a verzióját, a projekt Akusztika Wwise beépülő modul.

## <a name="experiment-with-project-acoustics-design-controls"></a>Kísérletezzen a projekt Akusztika tervezési vezérlők
Figyeljen, hogy a jelenet úgy érzi, az Unreal szerkesztőben a lejátszás gombra kattintva. A számítógépen, használjon W, A, S, D, és az egérrel való mozgáshoz. További billentyűparancsok megtekintéséhez nyomja le az **F1** billentyűt. Az alábbiakban néhány tervezési tevékenység próbálkozhat:

### <a name="modify-occlusion-and-transmission"></a>Hangelnyelés és átviteli módosítása
Vannak a forrás projekt Akusztika tervezési vezérlőelemek minden Unreal eredményes színész:

![DemoSceneSoundSourceDesignControls](media/demo-scene-sound-source-design-controls.png)

Ha a **hangelnyelés** Szorzó (az alapértelmezett érték 1) 1-nél nagyobb, hangelnyelés exaggerated kell lesz. Révén az 1-nél kisebb értékre a hangelnyelés életbe lépjenek több változás is.

Ahhoz, hogy a fali keresztül átviteli, helyezze át a **átviteli (adatbázis)** csúszka ki a legalacsonyabb szintre. 

### <a name="modify-wetness-for-a-source"></a>A forrás wetness módosítása
Milyen gyorsan wetness módosítja a távolságot módosításához használja a **technológiáira távolság Warp**. Projekt Akusztika kiszámítja a lemezterületet, a szimulációt, egész nedves szinteket, amelyek zökkenőmentesen távolság számától függ, és adja meg a technológiáira távolság jelek. Ez a hatás növelése a távolság lánc exaggerates távolság kapcsolatos nedves szintjei által. Hajlítási értékek 1-nél kisebb győződjön meg arról, módosítsa a távolság-alapú reverberation több változás is. Erről is módosítható részletesebben részletesen módosításával a **Wetness (adatbázis)**.

Növelje a késleltetési idő során a hely beállításával **Decay időskálára**. Vegyük azt az esetet, ahol a szimuláció eredménye egy késleltetési idő az 1.5-ös s. Beállítás a **Decay időskálára** 2 eredményez a alkalmazni a 3-ból a forrás késleltetési idő s.

### <a name="modify-distance-based-attenuation"></a>Távolság-alapú gyengülés módosítása
A projekt Akusztika Wwise mixer beépülő modul tiszteletben tartja a forrás távolság-alapú gyengülés Wwise beépített. Ez a görbe módosítása változik a száraz-elérési szint. A projekt Akusztika beépülő modul lehetőség a nedves szint a Nyugat-európai száradó vegyesen szimuláció és a tervező által megadott fenntartásához.

![DemoSoundsAttenuation](media/demo-sounds-attenuation.png)

Mindegyik szimulált player helyen Eszközkezelőre "szimuláció régió" mezőben számítási projekt Akusztika hajtja végre. A minta csomagban Akusztika eszközök is számlázásnak szimuláció régió sugarú 45 m, és a attenuations arra tervezték, hogy előtti 45 m 0-ra csökken. Bár ez fakulási nem egy szigorú követelmény, azt, hogy csak a figyelő 45 m belül geometriai fog occlude hangok csoportosítani végzi.

## <a name="next-steps"></a>További lépések
* [A projekt Akusztika integrálása](unreal-integration.md) beépülő modul, az Unreal projektbe
* [Azure-fiók létrehozása](create-azure-account.md) beépíteni kívánt saját tartalmak számára


