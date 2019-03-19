---
title: A Unity Project Akusztika rövid útmutató
titlesuffix: Azure Cognitive Services
description: Minta-tartalmat használ, Tervező szabályozza a Unity Project Akusztika kísérletezhet, és üzembe helyezése Windows asztali.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: b4eedabbc47738eb2f5797ffd67a3e3ebc9529ca
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136224"
---
# <a name="project-acoustics-unity-quickstart"></a>Projekt Akusztika Unity a rövid útmutató
Használat projekt Akusztika minta Unity kísérletezhet a szimuláció-biztonsági Tervező szabályozza a tartalom.

Szoftverkövetelmények:
* [Unity 2018.2 +](http://unity3d.com) Windows esetében
* [Akusztika tartalom mintacsomagot projekt](https://www.microsoft.com/en-us/download/details.aspx?id=57346)

Mit tartalmaz a minta-csomag?
* Unity-jelenetet geometry, megbízható forrásból és játékélményt vezérlők
* Projekt Akusztika beépülő modul 
* A helyszín létrehozott Akusztika eszközök

## <a name="import-the-sample-package"></a>A minta-csomag importálása
A minta-csomag importálása egy új Unity-projektet. 
* A Unity, váltson **eszközök > csomag importálása > egyéni csomag...**

    ![Csomag importálása](media/import-package.png)  

* Válasszon **ProjectAcoustics.unitypackage**

Ha a csomagot importál egy meglévő projektbe, [Unity-integráció](unity-integration.md) további lépéseket és a megjegyzések.

## <a name="restart-unity"></a>Indítsa újra a Unity
A Akusztika eszközkészlet bake részének .NET 4.x scripting futásidejű verzióját igényli. Csomag importálása frissíteni fogja a Unity-lejátszó beállításai. Unity-Ez a beállítás érvénybe léptetéséhez indítsa újra.

Ez a beállítás érvénybe tartott megnyitásával ellenőrizheti a **Player beállítások**:

![Lejátszó beállításai](media/player-settings.png)

![.NET 4.5](media/net45.png)

## <a name="experiment-with-design-controls"></a>Kísérletezzen a Tervező vezérlők
Nyissa meg a minta jelenet a **ProjectAcousticsSample** mappára, majd kattintson a lejátszás gombra a Unity-szerkesztőben. Használat W, A, S, D és az egérrel való mozgáshoz. Hasonlítsa össze, hogy a jelenet úgy érzi, és anélkül Akusztika, nyomja le az **R** gombra mindaddig, amíg az átfedő szöveg változik, és a "Akusztika: Letiltva." További billentyűparancsok megtekintéséhez nyomja le az **F1** billentyűt. Vezérlők, amelyek is gyakorlatot kattintson a jobb gombbal, válassza ki a végrehajtandó műveletet, majd balra kattintson a művelet végrehajtásához.

A parancsfájl **AcousticsAdjust** a minta jelenet, amely lehetővé teszi a forrás Tervező paraméterek a megbízható forrásra van csatolva. 

![AcousticsAdjust](media/acoustics-adjust.png)

A következő felhőgazdaságtan az, hogy a megadott vezérlők elő lehet állítani. Minden egyes-vezérléssel kapcsolatos részletes információkért lásd: a [projekt Akusztika Unity tervezési oktatóanyag](unreal-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Távolság-alapú gyengülés módosítása
A hanganyag DSP által biztosított a **projekt Akusztika** Unity spatializer beépülő modul tiszteletben tartja a forrás távolság-alapú gyengülés beépített a Unity-szerkesztőbe. Távolság-alapú gyengülés vezérlők vannak a **hang forrás** összetevő található a **vizsgáló** hang panel alatt adatforrásokat **3D eredményes beállításai**:

![Távolságskála gyengülés](media/distance-attenuation.png)

Projekt Akusztika számítási lehetőségek eltérése a lejátszó helyre "szimuláció régió" mezőben hajt végre. A minta csomagban Akusztika eszközök a Windows Media player körülvevő 45 millió szimuláció régió mérettel is számlázásnak, mivel az eredményes gyengülés úgy kell megtervezni, körülbelül 45 m 0-ra csökken.

### <a name="modify-occlusion-and-transmission"></a>Hangelnyelés és átviteli módosítása
* Ha a **hangelnyelés** Szorzó (az alapértelmezett érték 1) 1-nél nagyobb, hangelnyelés exaggerated kell lesz. Révén az 1-nél kisebb értékre a hangelnyelés életbe lépjenek több változás is.

* Ahhoz, hogy a fali keresztül átviteli, helyezze át a **átviteli (adatbázis)** csúszka ki a legalacsonyabb szintre. 

### <a name="modify-wetness-for-a-source"></a>A forrás wetness módosítása
* Milyen gyorsan wetness módosítja a távolságot módosításához használja a **technológiáira távolság Warp**. **A projekt Akusztika** számítási erőforrások nedves a lemezterületet, a szimulációt, egész szinteket, amelyek zökkenőmentesen távolság számától függ, és adja meg a technológiáira távolság jelek. Ez a hatás növelése a távolság lánc exaggerates távolság kapcsolatos nedves szintjei által. Hajlítási értékek 1-nél kisebb győződjön meg arról, módosítsa a távolság-alapú reverberation több változás is. Erről is módosítható részletesebben részletesen módosításával a **Wetness (adatbázis)**.

* Növelje a késleltetési idő során a hely beállításával a **Decay időskálára**. Ha egy adott forrás-figyelőt hely pár szimuláció eredménye egy 1.5s, a késleltetési idő és a **Decay időskálára** értéke 2, a késleltetési idő a alkalmazni a forrás-3s.

## <a name="next-steps"></a>További lépések
* További részletes információ a a [Unity-alapú projekt Akusztika vezérlők tervezése](unity-workflow.md)
* A fogalmakat részletesebb megismerése a [folyamat tervezése](design-process.md)
* [Azure-fiók létrehozása](create-azure-account.md) az üzem előtti bake és folyamatok os

