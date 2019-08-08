---
title: Projekt akusztikai egységének tervezési oktatóanyaga
titlesuffix: Azure Cognitive Services
description: Ez az oktatóanyag a Project Acoustics in Unity tervezési munkafolyamatát ismerteti.
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
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854264"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Projekt akusztikai egységének tervezési oktatóanyaga
Ez az oktatóanyag ismerteti a projekt akusztikai eszközeinek tervezési eszközeit és munkafolyamatát az Unity-ben.

Előfeltételek:
* Unity 2018.2 + Windowshoz
* Egy kisült akusztikai eszközzel rendelkező Unity-jelenet

Ebben az oktatóanyagban kétféle módon szerezhet be egy egységbeli jelenetet egy kisült akusztikai eszközzel:
* [Adja hozzá a Project Acoustics-t az Unity](unity-integration.md)-projekthez, majd szerezzen be [egy Azure batch fiókot](create-azure-account.md), majd [süssük meg az egység jelenetét](unity-baking.md)
* Vagy használja a [Project akusztikai Unity Sample-tartalmat](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Tervezési folyamattal kapcsolatos fogalmak áttekintése
A Project Acoustics közös hangalapú digitális jelfeldolgozási (DSP) módszereket használ a források feldolgozásához, és lehetővé teszi az ismerős akusztikai tulajdonságok, például az elzáródás, a nedves/száraz kombináció és a visszaverődési farok hosszának (RT60) a szabályozását. Az alapszintű [projekt akusztikai tervezési folyamatának koncepciója](design-process.md) azonban az, hogy ezek a tulajdonságok ne legyenek közvetlenül beállítva, így szabályozhatja, hogyan használja a rendszer a szimuláció eredményeit a tulajdonságok elvégzésére. Az egyes vezérlők alapértelmezett beállításai fizikailag pontos akusztikai értékeket jelentenek.

## <a name="design-acoustics-for-each-source"></a>Az egyes források kialakításához szükséges akusztika
A Project akusztika számos forrás-specifikus akusztikai tervezési vezérlőt biztosít. Ez lehetővé teszi az egyes jelenetekben lévő kombinációk szabályozását, néhány forrás kiemelésével és mások kiemelésével.

### <a name="adjust-distance-based-attenuation"></a>Távolságon alapuló csillapítás módosítása
A **Project akusztikai** Unity spatializer beépülő modul által biztosított HANGalapú DSP az egység-szerkesztőbe beépített, a forrás távolságon alapuló csillapítást veszi figyelembe. A távolságon alapuló csillapításhoz tartozó vezérlők a hangforrások **felügyelő** paneljén találhatók, a **3D hangbeállítások**alatt:

![Képernyőfelvétel az Unity Distance csillapítási beállításai panelről](media/distance-attenuation.png)

Az akusztikai számítások egy "szimulációs régió" mezőben jelennek meg, amely a lejátszó helyét középpontba kerül. Ha egy hangforrás távol van a lejátszótól, amely ezen a szimulációs régión kívül található, csak a mezőben lévő geometria befolyásolja a hangpropagálást (például elzáródást okoz), amely ésszerűen jól működik, ha a occluders a lejátszó közelében van. Azonban abban az esetben, ha a lejátszó nyitott térben van, de a occluders közel vannak a távoli hangforráshoz, a hang reálisan disoccluded válik. A javasolt megkerülő megoldás az, hogy az ilyen esetekben, ha a Hangcsillapítás a 0 értékre csökken körülbelül 45 m-re, a lejátszó alapértelmezett vízszintes távolsága a mező széléig.

![Képernyőfelvétel az Unity SpeakerMode beállítás panelről](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Elzáródás és átvitel módosítása
Ha a **AcousticsAdjust** -szkriptet egy forráshoz csatolja, az adott forráshoz tartozó paraméterek hangolását is lehetővé teszi. A parancsfájl csatolásához kattintson a **felügyelő** panel alján található **összetevő hozzáadása** elemre, és navigáljon a **Parancsfájlok > akusztikai beállítások módosításával**. A parancsfájl hat vezérlőelemmel rendelkezik:

![Képernyőfelvétel az Unity AcousticsAdjust parancsfájlról](media/acoustics-adjust.png)

* **Akusztika engedélyezése** – meghatározza, hogy az akusztikai beállítások alkalmazhatók-e a forrásra. Ha nincs bejelölve, a forrás HRTFs vagy pásztázással lesz felhasználva, de nem lesz akusztika. Ez nem jelent akadályt, elzáródást vagy dinamikus visszaverődési paramétereket, például a szintet és a romlási időt. A visszaverődést továbbra is rögzített szinttel és bomlási idővel alkalmazza a rendszer.
* **Elzáródás** – szorzót alkalmazhat az akusztikai rendszer által kiszámított, az elzáródási adatbázis szintjére. Ha ez a szorzó nagyobb, mint 1, a elzáródás túlzott lesz, míg az 1. értéknél kisebb értékek finomabbak, és a 0 érték letiltja a elzáródást.
* **Átvitel (db)** – a geometrián keresztüli átvitel által okozott gyengítés (dB-ben) beállítása. Állítsa be a csúszkát a legalacsonyabb szintre az átvitel letiltásához. Akusztikai spatializes a kezdeti száraz hangot a jelenet geometriájának (portaling) megérkezése előtt. A továbbítás egy további száraz beérkezést biztosít, amely térbeli irányban van. Vegye figyelembe, hogy a forráshoz tartozó távolsági gyengítő görbét is alkalmazza a rendszer.

### <a name="adjust-reverberation"></a>A visszaverődés módosítása
* **Nedvesség (db)** – úgy állítja be a reverb-teljesítményt, hogy az dB-ben a forrástól mért távolság szerint legyen elérhető. A pozitív értékek nagyobb Zengő, míg a negatív értékek szárazak. Kattintson a görbe vezérlőelemre (zöld vonal) a görbe szerkesztőjének megjelenítéséhez. Módosítsa a görbét úgy, hogy a bal gombbal kattint a pontok hozzáadására, és a kívánt függvényt a pontokra húzza. Az x tengely távolság a forrástól, az y tengely pedig az adatbázison belüli reverb-beállítás. A görbék szerkesztésével kapcsolatos további információkért tekintse meg ezt az [egységet ismertető útmutatót](https://docs.unity3d.com/Manual/EditingCurves.html). Ha vissza szeretné állítani a görbét az alapértelmezett értékre , kattintson a jobb gombbal a nedvességre, és válassza az **Alaphelyzet**
* **Bomlási idő skálázása** – egy szorzót állít be a romlási időre. Ha például a sütni eredmény 750 ezredmásodperces csökkenést határoz meg, de ez az érték 1,5, a forrásra alkalmazott bomlási idő 1 125 ezredmásodperc.
* **Kültéri** környezet – az akusztikai rendszer azon becslése, hogy a "szabadban" hogyan lehet a forráson alapuló visszhangot megszólalni. Ha ezt az értéket 1-re állítja, akkor a forrás mindig teljesen megszólaljon, míg a-1 értékre állításával a forrás teljesen bekapcsolja a hangot.

Ha a **AcousticsAdjustExperimental** parancsfájlt a forráshoz csatolja, a további kísérleti hangolási paramétereket is megadhat a forráshoz. A parancsfájl csatolásához kattintson a **felügyelő** panel alján található **összetevő hozzáadása** elemre, és navigáljon a **parancsfájlok > akusztikai beállítások beállítása kísérleti**értékre. Jelenleg egy kísérleti vezérlő van:

![Képernyőfelvétel az Unity AcousticsAdjustExperimental parancsfájlról](media/acoustics-adjust-experimental.png)

* **Észlelési távolsági hajlítás** – a száraz nedves arány kiszámításához használt távolságra exponenciális hajlítást alkalmazhat. Az akusztikai rendszerek az egész térben kiszámítják a nedves szinteket, és a távolságot és a megfigyelt távolsági célzást is biztosítják. Az 1. értéknél nagyobb értékek eltúlozása ez a hatás a távolsághoz kapcsolódó visszaverődési szintek növekedésével történik, ami a "távoli" hangot eredményezi. Az 1 értéknél kisebb értékek hajlításával a távolságon alapuló visszaverődés sokkal finomabbra változik, így a hang több "jelen" lehet.

## <a name="design-acoustics-for-all-sources"></a>Az összes forrás akusztikus kialakítása
Az összes forrás paramétereinek módosításához kattintson a Channel (csatorna) szalagra az egységhangkeverőben, és módosítsa a **Project akusztikai keverő** hatása paramétereit.

![Képernyőfelvétel a Project akusztika Unity keverő testreszabási paneljéről](media/mixer-parameters.png)

* A **nedvesség szabályozása** – a jelenetben a forrás-figyelő távolságon alapuló összes forrás esetében a reverb-erőt (db) állítja be. A pozitív értékek nagyobb Zengő, míg a negatív értékek szárazak.
* **RT60 Scale** -multiplikatív skaláris a reverb-időre.
* **Pásztázás** – meghatározza, hogy a hang a binaurális (0) vagy a többcsatornás pásztázás (1) kimenetként van-e kiválasztva. Az 1 érték melletti értékek a binaurális értéket jelzik. A binaurális kimenet a HRTFs használatával van összhangban a fejhallgatóval és többcsatornás kimenettel a többcsatornás surround VBAP való használatra. Ha a többcsatornás Panner használja, ügyeljen arra, hogy a **Project Settings** > **hang**alatt válassza ki az eszközbeállítások megegyező hangszórós módot.

## <a name="check-proper-sound-source-placement"></a>Megfelelő hangforrások elhelyezésének megkeresése
A foglalt voxels belül elhelyezett hangforrások nem fognak akusztikai kezelést kapni. Mivel a voxels kiterjesztik a látható jelenet geometriáját, lehetséges, hogy a forrást egy Voxel belül helyezi el, miközben a vizuális geometria unoccluded jelenik meg. A Project akusztika voxels megtekintheti a Voxel rács jelölőnégyzetet a minialkalmazások menüben, a **jelenet** nézet jobb felső sarkában.

![Az Unity Gadgets menü képernyőképe](media/gizmos-menu.png)  

A Voxel megjeleníthető annak a megállapítása is, hogy a játék vizuális összetevői rendelkeznek-e átalakítóval. Ha igen, alkalmazza ugyanezt a transzformációt az Acoustics **Managert**üzemeltető GameObject.

### <a name="bake-time-vs-run-time-voxels"></a>Sütni idő és futási idő voxels
A voxels megtekinthetők a szerkesztő ablakban a játék tervezési ideje és a játék ablakban futásidőben. A voxels mérete különbözik ezekben a nézetekben. Ennek az az oka, hogy a akusztikai futtatókörnyezet interpolációja finomabb Voxel rácsot használ a zökkenőmentes interpolációs eredményekhez. A hangforrások elhelyezését a futásidejű voxels kell ellenőrizni.

Tervezési idő voxels:

![Képernyőfelvétel a Project Acoustics voxels a tervezési idő alatt](media/voxels-design-time.png)

Futásidejű voxels:

![Képernyőfelvétel a Project Acoustics voxels futási ideje alatt](media/voxels-runtime.png)

## <a name="next-steps"></a>További lépések
* A [tervezési folyamat](design-process.md) hátterében rejlő fogalmakat kiemelő esettanulmányok megismerése

