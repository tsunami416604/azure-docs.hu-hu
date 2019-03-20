---
title: Projekt Akusztika Unity tervezési oktatóanyag
titlesuffix: Azure Cognitive Services
description: Ez az oktatóanyag ismerteti a tervezési munkafolyamat a Unity Project Akusztika.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 7f079c511a32cfcf0fa018d40abb737ad08f3821
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137959"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Projekt Akusztika Unity tervezési oktatóanyag
Ez az oktatóanyag leírja a tervezői eszközök és a Unity Project Akusztika számára.

Előfeltételek:
* Unity 2018.2 + Windows esetében
* A létrehozott Akusztika eszköz a Unity jelenet

Ebben az oktatóanyagban Unity jelenet egy létrehozott Akusztika eszközt kétféleképpen kaphat:
* [Unity-projektjét, adja hozzá a projekt Akusztika](unity-integration.md), majd [lekérése az Azure Batch-fiók](create-azure-account.md), majd [a Unity-jelenet os](unity-baking.md)
* Másik lehetőségként használhatja a [Akusztika Unity Project mintatartalmakat próbálhat](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Tekintse át a tervezési folyamat alapelvei
Projekt Akusztika közös hang digitális jel feldolgozás (DSP) módszerek használatával feldolgozni a forrásból, és az ismerős Akusztika tulajdonságok hangelnyelés, Nyugat-európai/száraz vegyesen és reverberation tail hossza (RT60) szabályozható. De az alapvető [projekt Akusztika tervezési folyamat fogalom](design-process.md) van, hanem közvetlenül állítani ezeket a tulajdonságokat, meghatározhatja, hogyan történik a szimuláció eredményeit, ezeket a tulajdonságokat a meghajtó használata. Az alapértelmezett beállításokat az egyes vezérlőelemek fizikailag pontos Akusztika képviseli.

## <a name="design-acoustics-for-each-source"></a>Az egyes források tervezési Akusztika
Projekt Akusztika biztosít egy adatforrás-specifikus Akusztika kialakítási ellenőrzések száma. Ez lehetővé teszi a szabályozására, a mix álló jelenet renderelése; az egyes források tárgyalta és a másokkal való emphasizing.

### <a name="adjust-distance-based-attenuation"></a>Távolság-alapú gyengülés beállítása
A hanganyag DSP által biztosított a **projekt Akusztika** Unity spatializer beépülő modul tiszteletben tartja a forrás távolság-alapú gyengülés beépített a Unity-szerkesztőbe. Távolság-alapú gyengülés vezérlők vannak a **hang forrás** összetevő található a **vizsgáló** hang panel alatt adatforrásokat **3D eredményes beállításai**:

![Távolságskála gyengülés](media/distance-attenuation.png)

Akusztika számítási lehetőségek eltérése a lejátszó helyre "szimuláció régió" mezőben hajt végre. Ha egy megbízható forrásból a szimuláció régión kívül található, a lejátszó távolságban belül a mezőbe csak geometriai hatással lesz a hang propagálás (például hangelnyelés okozó), amely viszonylag jól működik, ha occluders vannak-e a Windows Media player közelében. Azonban azokban az esetekben, amikor a Windows Media player nyissa meg a helyet, de a occluders buildjénél a távoli eredményes forrás a hangot is válnak irreálisan disoccluded. A javasolt megoldás, hogy ebben az esetben ellenőrizze, hogy a hangfájl gyengülés kapcsol körülbelül 45 m, a lejátszó az Edge-ben a mező alapértelmezett vízszintes távolság 0.

![SpeakerMode](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Hangelnyelés és átviteli beállítása
Csatolja a **AcousticsAdjust** forráshoz parancsfájl lehetővé teszi, hogy a hangolási paraméterek ahhoz az adatforráshoz. Csatlakoztassa a parancsfájlt, kattintson a **összetevő felvétele** alján a **vizsgáló** panelen, és keresse meg a **parancsfájlok > Akusztika beállítása**. A szkript hat vezérlők rendelkezik:

![AcousticsAdjust](media/acoustics-adjust.png)

* **Engedélyezze a Akusztika** – azt szabályozza, hogy Akusztika alkalmazott ebből a forrásból. Ha nincs bejelölve, a forrás lesz spatialized, HRTFs vagy pásztázásához, de nem lesznek nincs Akusztika. Ez azt jelenti, hogy nincs akadály, hangelnyelés vagy dinamikus reverberation paraméterek – például szintjét és decay. Egy rögzített szint és a késleltetési idő reverberation továbbra is érvényesül.
* **Hangelnyelés** -alkalmazása egy szorzóval a Akusztika rendszer által számított hangelnyelés dB szintre. Ha ez szorzó 1-nél nagyobb, hangelnyelés fog kell exaggerated közben értékek 1-nél kisebb ne hangelnyelés hatása több változás is, és a 0 érték letiltja a hangelnyelés.
* **Átviteli (adatbázis)** – állítsa be a gyengülés (az adatbázis) keresztül geometriai átvitel okozta. Állítsa a csúszkát a legalacsonyabb átviteli letiltása. Akusztika körül jelenet geometriai (portaling) érkező, a kezdeti száraz hang spatializes. Átviteli biztosít egy további száraz gyűjtőbe, amely a sor-az-üzemel irányba spatialized van. Vegye figyelembe, hogy a forrás gyengülés távolság görbe is érvényesek.

### <a name="adjust-reverberation"></a>Reverberation beállítása
* **Wetness (adatbázis)** – beállítja a visszhang energiagazdálkodási, dB, a forrás közötti távolságot megfelelően. Pozitív értékek győződjön meg arról, hang több reverberant, amíg a negatív értékek hangjelzést több száraz. Kattintson a görbe szerkesztő megjelenítéséhez a görbe vezérlő (zöld sor). A görbe pontok hozzáadása kattint, és húzza a kívánt kialakításához, a függvény ezen pontok módosítása Az x tengely forrás közötti távolságot, és az y tengely visszhang korrekciós dB-ben. Szerkesztési görbék további információkért lásd: Ez [Unity manuális](https://docs.unity3d.com/Manual/EditingCurves.html). Alaphelyzetbe állítja a görbe vissza az alapértelmezett, kattintson a jobb gombbal **Wetness** válassza **alaphelyzetbe**.
* **Decay időskálára** – Itt adható meg egy szorzóval vonatkozó a késleltetési idő. Például ha az bake eredményt adja meg egy késleltetési idő ezredmásodpercben 750, de ez az érték 1.5 van beállítva, a késleltetési idő a alkalmazni a forrás-, a 1,125 ezredmásodperc.
* **Outdoorness** -additív módosításának a becslés az Akusztika rendszer hogyan "szabadban" hang kell-e a reverberation egy forrás. Az érték 1 fogja elérhetővé tenni egy adatforrás mindig eredményes teljesen szabadban, -1 értékre állítaná készítsen egy forrás eredményes teljesen szobában közben.

Csatolja a **AcousticsAdjustExperimental** forráshoz parancsfájl lehetővé teszi, hogy további kísérleti hangolási paraméterek ahhoz az adatforráshoz. Csatlakoztassa a parancsfájlt, kattintson a **összetevő felvétele** alján a **vizsgáló** panelen, és keresse meg a **parancsfájlok > Akusztika beállítása kísérleti**. Jelenleg egy kísérleti vezérlő:

![AcousticsAdjustExperimental](media/acoustics-adjust-experimental.png)

* **Technológiáira távolság Warp** – exponenciális hajlítása száraz – Nyugat-európai arányának kiszámításához használt távolság a alkalmazni. A Akusztika rendszer kiszámítja a terület teljes nedves szinteket, amelyek zökkenőmentesen távolság számától függ, és adja meg a technológiáira távolság jelek. Hajlítási 1-nél nagyobb értékek a hatás exaggerate távolság kapcsolatos reverberation szintek, így a "távoli" hang növelésével. Hajlítása értékek 1-nél kisebb ügyeljen a távolság-alapú reverberation módosítása több változás is, és, hogy az eredményes további "e".

## <a name="design-acoustics-for-all-sources"></a>Az összes tervezési Akusztika
Minden forrás paramétereinek beállításához kattintson a csatorna sáv a Unity a **hang Mixer**, és módosítsa úgy a paramétereket a a **projekt Akusztika Mixer** érvénybe.

![Mixer testreszabása](media/mixer-parameters.png)

* **Wetness igazítás** – beállítja a visszhang energiagazdálkodási, DB, a forrás-figyelő távolság alapján jelenet minden forrás. Pozitív értékek győződjön meg arról, hang több reverberant, amíg a negatív értékek hangjelzést több száraz.
* **RT60 méretezési** – tényezőt skaláris visszhang alkalommal.
* **Használja a Pásztázó** -vezérlők audio-e kimeneti binaural (0) vagy a többcsatornás pásztázásához (1). Bármilyen érték 1 mellett azt jelzi, hogy binaural. Binaural kimeneti a HRTFs spatialized fejhallgatót való használatra, és a többcsatornás kimeneti a VBAP spatialized többcsatornás legyen speaker rendszerekkel lehet használni. Használja a többcsatornás panner, ügyeljen arra, hogy a hangszóró –, amely megfelel az eszköz beállításainál válassza, ha található **Projektbeállítások** > **hang**.

## <a name="check-proper-sound-source-placement"></a>Ellenőrizze a megfelelő megbízható forrás elhelyezése
Betöltött voxels belül elhelyezett megbízható forrásból nem kap akusztikai kezelésére. Mivel voxels túlmutatnak az eddig a látható jelenet geometry, lehetséges, egy adatforrás egy voxel belül helyezi el, amíg unoccluded visual geometriai szerint jelenik meg. Projekt Akusztika voxels megtekintheti a voxel rács jelölőnégyzetet átállításával a **Gizmos** menüben, a jobb felső sarkában a **jelenet** megtekintése.

![Gizmos menü](media/gizmos-menu.png)  

Voxel megjelenítését is segít meghatározni a vizuális összetevőket a játék egy-egy átalakítási vonatkozik-e. Ha igen, az azonos átalakítás alkalmazása a GameObject üzemeltetéséhez a **Akusztika Manager**.

### <a name="bake-time-vs-run-time-voxels"></a>Futási idő voxels és bake idő
A játék tervezéskor-szerkesztő ablakban és a futásidőben a játék ablakban voxels lehetőség. Ezek a nézetek az eltérő a voxels mérete. Ennek az oka a Akusztika futásidejű interpolációs használ egy kifinomultabb voxel rács finomabb interpolációs eredmény. Hang forrás elhelyezési ellenőrizni kell a futtatókörnyezet voxels használatával.

Tervezési idő voxels:

![VoxelsDesignTime](media/voxels-design-time.png)

Futásidejű voxels:

![VoxelsRuntime](media/voxels-runtime.png)

## <a name="next-steps"></a>További lépések
* Böngésszen az esettanulmányok, kiemelve a fogalmakat a [folyamat tervezése](design-process.md)

