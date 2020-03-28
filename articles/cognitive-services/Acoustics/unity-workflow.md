---
title: Project Acoustics Unity Tervezési oktatóanyag
titlesuffix: Azure Cognitive Services
description: Ez az oktatóanyag a Project Acoustics unity tervezési munkafolyamatát ismerteti.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fd00e4105ce4edae9d014df2a83c5ae3aaf778da
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854264"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Project Acoustics Unity Tervezési oktatóanyag
Ez az oktatóanyag a Project Acoustics in Unity tervezési eszközeit és munkafolyamatait ismerteti.

Előfeltételek:
* Unity 2018.2+ Windows-hoz
* A Unity jelenet egy sült akusztikai eszköz

Ebben a bemutatóban kétféleképpen kaphat egységjelenetet egy sült akusztikai eszközzel:
* Adja hozzá a [Project Acoustics-t az Unity projekthez,](unity-integration.md)majd [szerezzen be egy Azure Batch-fiókot,](create-azure-account.md)majd [süsse meg az Egység jelenetet](unity-baking.md)
* Vagy használja a [Project Acoustics Unity mintatartalmat.](unity-quickstart.md)

## <a name="review-design-process-concepts"></a>Tervezési folyamat fogalmainak áttekintése
A Project Acoustics a források feldolgozásához gyakori digitális hanganyag-feldolgozási (DSP) módszereket használ, és a jól ismert akusztikai tulajdonságokat, például az elzáródást, a nedves/száraz keveréket és a visszaverődés farokhosszát (RT60) szabályozza. De a [Project Acoustics tervezési folyamat ának alapkoncepciója](design-process.md) az, hogy ahelyett, hogy ezeket a tulajdonságokat közvetlenül állítaná be, szabályozhatja, hogy a szimulációs eredmények hogyan használhatók ezeknek a tulajdonságoknak a meghajtójára. Az egyes vezérlők alapértelmezett beállításai a fizikailag pontos akusztikát jelölik.

## <a name="design-acoustics-for-each-source"></a>Tervezzen akusztikát minden forráshoz
A Project Acoustics számos forrásspecifikus akusztikai tervezési vezérlőt biztosít. Ez lehetővé teszi, hogy ellenőrizzék a mix egy jelenet hangsúlyozva egyes források és de-hangsúlyozva mások.

### <a name="adjust-distance-based-attenuation"></a>Távolságalapú csillapítás beállítása
Az audio DSP által biztosított **Project Acoustics** Unity spatializer plugin tiszteletben tartja a per-source távolság-alapú csillapítás beépített Unity Editor. A távolságalapú csillapítás vezérlői a hangforrások **felügyelő** paneljén található **Hangforrás** összetevőben, a **3D hangbeállítások**alatt találhatók:

![Képernyőkép: Unity távolságcsillapítási beállítások panel](media/distance-attenuation.png)

Az akusztika a lejátszó helye köré épülő "szimulációs régió" mezőben végzi el a számítást. Ha egy hangforrás távol van a lejátszótól, amely a szimulációs területen kívül helyezkedik el, csak a dobozon belüli geometria befolyásolja a hang terjedését (például elzáródást okoz), amely meglehetősen jól működik, ha az elzárók a lejátszó közelében vannak. Azonban azokban az esetekben, amikor a játékos a nyílt térben, de az occluders közel vannak a távoli hangforrás, a hang válhat irreálisan disoccluded. A javasolt megoldás az, hogy ilyen esetekben a hangcsillapítás körülbelül 45 m-re esik le, ami a játékos alapértelmezett vízszintes távolsága a doboz széléig.

![Képernyőkép a Unity SpeakerMode beállításpanelről](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Az elzáródás és az átvitel beállítása
Az **AcousticsAdjust** parancsfájl forráshoz csatolása lehetővé teszi az adott forrás hangolási paramétereinek beállítását. A parancsfájl csatolásához kattintson a **Felügyelő** panel alján található **Összetevő hozzáadása** gombra, és keresse meg a **Parancsfájlok > akusztika beállítása parancsra.** A szkript hat vezérlővel rendelkezik:

![A Unity AcousticsAdjust parancsfájl képernyőképe](media/acoustics-adjust.png)

* **Akusztika engedélyezése** – Azt szabályozza, hogy a kuszta alkalmazásra vonatkozik-e a forrás. Ha nincs bejelölve, a forrás lesz spatialized HRTFs vagy pásztázás, de nem lesz akusztika. Ez azt jelenti, hogy nincsenek obstrukciós, elzáródási vagy dinamikus reverberation paraméterek, mint például a szint és a bomlási idő. A visszhangot még mindig alkalmazzák rögzített szinttel és bomlási idővel.
* **Okklúzió** - Szorzó alkalmazása az akusztikai rendszer által kiszámított dB elzáródási szintre. Ha ez a szorzó nagyobb, mint 1, az elzáródás eltúlzott lesz, míg az 1-nél kisebb értékek finomabbá teszik az elzáródási hatást, és a 0 érték letiltja az elzáródást.
* **Sebességváltó (dB)** - Állítsa be a csillapítást (dB-ben) a geometrián keresztül történő átvitel által okozott anamnézisben. Állítsa ezt a csúszkát a legalacsonyabb szintre az átvitel letiltásához. Az akusztika a jelenet geometriája (portaling) körül érkező kezdeti száraz hangot térretyenyezi. Az átvitel további száraz érkezést biztosít, amely a látótávolság irányában térosított. Vegye figyelembe, hogy a forrás távolságcsillapítási görbéje is érvénybe lép.

### <a name="adjust-reverberation"></a>Visszhang beállítása
* **Nedvesség (dB)** - A zengetési teljesítmény beállítása dB-ben, a forrástól való távolságnak megfelelően. A pozitív értékek a hangot még inkább visszaverik, míg a negatív értékek szárazabbá teszik a hangot. Kattintson a görbe vezérlő (zöld vonal), hogy ki a görbe szerkesztő. A görbe módosításához kattintson a bal gombbal a pontok hozzáadásához, és húzza ezeket a pontokat a kívánt funkcióhoz. Az x tengely a forrástól való távolság, az y tengely pedig a dB-ben történő zengetés-beállítás. A görbék szerkesztésével kapcsolatos további információkért tekintse meg ezt a [Unity kézikönyvet.](https://docs.unity3d.com/Manual/EditingCurves.html) Ha vissza szeretné állítani a görbét az alapértelmezett értékre, kattintson a jobb gombbal a **Wetness** elemre, és válassza a Visszaállítás **parancsot.**
* **Bomlási időskála** - Beállít egy szorzót a bomlási időhöz. Ha például a sütési eredmény 750 ezredmásodperc bomlási időt ad meg, de ez az érték 1,5- re van állítva, a forrásra alkalmazott bomlási idő 1125 ezredmásodperc.
* **Kültéri ség** - Az akusztikai rendszer becslésének additív beállítása arról, hogy a forráson lévő visszhangnak hogyan kell megszólalnia. Ha ezt az értéket 1-re állítja, a forrás mindig teljesen a szabadban fog hangzani, míg a -1 értékre állítással a forrás hangja teljesen beltérben lesz.

Az **AcousticsAdjustExperimental** parancsfájl forráshoz csatolása további kísérleti hangolási paramétereket tesz lehetővé az adott forráshoz. A parancsfájl csatolásához kattintson a **Felügyelő** panel alján található **Összetevő hozzáadása** gombra, és keresse meg a **Parancsfájlok > akusztikai beállítása a kísérleti elemet**. Jelenleg van egy kísérleti kontroll:

![A Unity AcousticsAdjustExperimental parancsfájl képernyőképe](media/acoustics-adjust-experimental.png)

* **Észlelési távolság -** Exponenciális hajlítást alkalmazhat a száraz-nedves arány kiszámításához használt távolságra. Az akusztikai rendszer kiszámítja a nedves szinteket az egész térben, amelyek a távolságtól függően egyenletesen változnak, és észlelési távolságjelzéseket biztosítanak. Az 1-nél nagyobb hajlítási értékek eltúlozzák ezt a hatást a távolsággal kapcsolatos visszhangszintek növelésével, "távolivá" téve a hangot. Az 1-nél kisebb megvetemedési értékek finomabbá teszik a távolságalapú visszhangváltozást, így a hang "jelen" lesz.

## <a name="design-acoustics-for-all-sources"></a>Tervezzen akusztikát minden forráshoz
Az összes forrás paramétereinek módosításához kattintson a Unity **Audio Mixer**( Channel) csatornasávjára, és állítsa be a Project Acoustics Mixer effektus **paramétereit.**

![Képernyőkép a Project Acoustics Unity Mixer testreszabási paneléről](media/mixer-parameters.png)

* **Wetness Adjust** - Beállítja a zengetés erejét, a dB,-ban, az összes forrásból a jelenet alapján forrás-hallgató távolság. A pozitív értékek a hangot még inkább visszaverik, míg a negatív értékek szárazabbá teszik a hangot.
* **RT60 Skála** - Multiplicatív skalár a zengetés idáig.
* **Pásztázás -** Szabályozza, hogy a hang binaurális (0) vagy többcsatornás pásztázásként (1) legyen-e hallható. Az 1-en kívül bármely érték binaurális értéket jelez. A binaurális kimenet et a fejhallgatóval használható HRTF-ekkel, a többcsatornás kimenetpedig a VBAP-vel térítetted fel a többcsatornás térhatású hangszórórendszerekhez. Ha a többcsatornás pannert használja, ne felejtse el kiválasztani az eszköz beállításainak megfelelő hangszórómódot, amely a **Projektbeállítások** > **hangja**területen található.

## <a name="check-proper-sound-source-placement"></a>A hangforrás megfelelő elhelyezésének ellenőrzése
A megszállt voxels belsejében elhelyezett hangforrások nem kapnak akusztikus kezelést. Mivel a voxels túlnyúlik a látható jelenet geometrián, lehetőség van egy forrást voxelbe helyezni, miközben vizuális geometria nem zárja ki. A Project Acoustics voxels megtekintéséhez a **Gizmos** menü voxel rács jelölőnégyzetének a Jelenet nézet jobb felső részén lévő **jelölőnégyzetet váltsa** be.

![Képernyőkép a Unity Gizmos menüről](media/gizmos-menu.png)  

A voxel kijelző is segít meghatározni, ha vizuális alkatrészek a játék ban van egy transzformáció alkalmazni őket. Ha igen, alkalmazza ugyanazt az átalakítást az **Acoustics Managert**tároló GameObject objektumra.

### <a name="bake-time-vs-run-time-voxels"></a>Sütni idő vs futási idő voxels
Lehetőség van a voxels megtekintésére a szerkesztő ablakban a játék tervezési idejében és a játék ablakban futásidőben. A voxelek mérete ezekben a nézetekben eltérő. Ennek az az oka, hogy az akusztikai futásidejű interpoláció finomabb voxel rácsot használ a simább interpolációs eredmények érdekében. A hangforrás elhelyezését a futásidejű voxels segítségével kell ellenőrizni.

Tervezési idő voxels:

![Képernyőkép a Project Acoustics voxels-ről a tervezési idő alatt](media/voxels-design-time.png)

Futásidejű voxels:

![Képernyőkép a Project Acoustics voxels futásközbeni idejéről](media/voxels-runtime.png)

## <a name="next-steps"></a>További lépések
* A [tervezési folyamat](design-process.md) mögött megrejlő fogalmakat kiemelő esettanulmányok

