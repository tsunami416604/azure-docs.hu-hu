---
title: A projekt Akusztika tervezési folyamat áttekintése
titlesuffix: Azure Cognitive Services
description: Ez a dokumentum ismerteti az összes három szakaszban a projekt Akusztika munkafolyamat-Tervező tervez express.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: cf38b2096e958a7484e5161277a608ec2cb88224
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470485"
---
# <a name="design-process-overview"></a>Tervezési folyamat áttekintése
Az összes három szakaszban a projekt Akusztika munkafolyamat-Tervező tervez fejezhető: előre os jelenet telepítése, a megbízható forrás elhelyezési és utáni bake Tervező. A folyamat kevesebb jelölőnyelvi társított visszhang kötetek elhelyezése Tervező szabályozhatja, hogyan álló jelenet renderelése; hangok megőrzése szükséges.

## <a name="pre-bake-design"></a>Üzem előtti bake kialakítása
Az üzem előtti bake jelenet telepítési folyamatot hoz létre a helyszín és a metaadatokat, amelyek használhatók a hangot wave szimuláció, amely kiválasztásával jelenet elemek részt vesz a szimuláció occlusions, tükröződés és reverberation biztosít, amely tartalmazza. A helyszín metaadatai a kijelölés akusztikai anyagok minden jelenet elemhez. Az akusztikai anyagok ellenőrzés kerülnek vissza minden egyes felületből eredményes energia mennyiségét.

Az alapértelmezett elnyelő együttható az összes felület érték 0,04, amely magas reflektív. Esztétikai és játékélményt hatásait a jelenet során különböző anyagokat, amely azokat a tér át egy területről a helyszín, egy másik hallani esetén különösen kiemelkedő a figyelők elnyelő együtthatója beállításával érheti el. Például egy világos való átállás sötét reverberant szobában, javítja a nem reverberant öltözet jelenet az átállás hatását. A hatás eléréséhez finomhangolja az elnyelő együttható magasabb öltözet jelenet anyagokon.

Egy adott anyag szoba reverberation idején intenzitásfokozatok kapcsolódik, a elnyelő együttható a legtöbb anyagok elnyelő értékeket kellene 0,01 való 0,20 tartományban. Az ezen a tartományon kívül elnyelő együttható anyagok olyan nagyon elnyelő.

![Visszhang grafikon](media/ReverbTimeGraph.png)

A [végig a felhasználói felület bake](bake-ui-walkthrough.md) az üzem előtti bake vezérlők részletesen ismerteti.

## <a name="sound-source-placement"></a>Hang forrás elhelyezése
Mintavétel, valamint voxels pontok futásidőben is súgó megtekintése folyamatban elakadt a voxelized geometriai belül eredményes forrásokkal kapcsolatos problémák megoldásában. Váltás a mintavétel, valamint a voxel rács pontok, a megfelelő jelölőnégyzet bejelölésével a Gizmos menü a jobb felső sarkában a jelenet nézetet. Ha a megbízható forrás belül egy falon voxel, helyezze át egy légi voxel.

![Gizmos menü](media/GizmosMenu.png)  

A voxel megjelenített segítségével határozza meg a vizuális összetevőket a játék egy-egy átalakítási vonatkozik-e. Ha igen, az azonos átalakítás alkalmazása a GameObject üzemeltetéséhez a **Akusztika Manager**.

### <a name="voxel-size-discrepancies"></a>Voxel mérete eltérések
Azt tapasztalhatja, hogy a voxels mutatja be, amelyek a helyszín rácsvonalak részt vesznek a Akusztika bake mérete a tervezési időt és a futtatókörnyezet nézetek különböző. Ez a különbség nem befolyásolja a kiválasztott szimuláció gyakorisága minőségi/részletességét azonban inkább egy biproduct voxelized jelenet modul használatával. Futásidőben a szimuláció voxels olyan "finomított", a forrás helye között interpolációs támogatásához. Ez is lehetővé teszi a tervezési időt elhelyezéséhez eredményes adatforrásokat közelebb helyezni a jelenet rácsvonalak engedélyezettnél szimuláció voxel mérete – mivel belül egy voxel forrásokból, amelyek tartalmaznak egy akusztikailag kezelt háló ne végezzen semmilyen hangot.

Az alábbiakban bemutató (előtti bake) tervezési voxels és futásidejű (utáni bake) voxels közötti különbség, mivel a Unity beépülő modul által ábrázolt két képek:

Tervezési idő voxels:

![VoxelsDesignTime](media/VoxelsDesignTime.png)

Futásidejű voxels:

![VoxelsRuntime](media/VoxelsRuntime.png)

A döntés-e a voxel háló pontosan tükrözi az architektúra szerkezeti jelenet rácsvonalak a Tervező módban voxels, nem a finomított voxels futásidejű képi használatával kell tenni.

## <a name="post-bake-design"></a>Utáni bake kialakítása
Bake eredményeket a hozzáférés-vezérlő bejegyzések fájlt minden forrás-figyelő hely pár során a jelenet hangelnyelés és reverberation paraméterek vannak tárolva. A fizikailag pontos eredményt is használható a projekt-van, és kiváló kiindulási pontot (SSO). A utáni bake tervezési folyamat szabályok alkalmazásán át a bake eredménye a paraméterekhez futtatás közben adja meg.

### <a name="distance-based-attenuation"></a>Távolság-alapú gyengülés
A hanganyag DSP által biztosított a **Microsoft Acoustics** Unity spatializer beépülő modul tiszteletben tartja a forrás távolság-alapú gyengülés beépített a Unity-szerkesztőbe. Távolság-alapú gyengülés vezérlők vannak a **hang forrás** összetevő található a **vizsgáló** hang panel alatt adatforrásokat **3D eredményes beállításai**:

![Távolságskála gyengülés](media/distanceattenuation.png)

Akusztika számítási lehetőségek eltérése a lejátszó helyre "szimuláció régió" mezőben hajt végre. Ha egy megbízható forrásból a szimuláció régión kívül található, a lejátszó távolságban belül a mezőbe csak geometriai hatással lesz a hang propagálás (például hangelnyelés okozó), amely viszonylag jól működik, ha occluders vannak-e a Windows Media player közelében. Azonban azokban az esetekben, amikor a Windows Media player nyissa meg a helyet, de a occluders buildjénél a távoli eredményes forrás a hangot is válnak irreálisan disoccluded. A javasolt megoldás, hogy ebben az esetben ellenőrizze, hogy a hangfájl gyengülés kapcsol körülbelül 45 m, a lejátszó az Edge-ben a mező alapértelmezett vízszintes távolság 0.

### <a name="tuning-scene-parameters"></a>Jelenetparaméterek hangolása
Minden forrás paramétereinek beállításához kattintson a csatorna sáv a Unity a **hang Mixer**, és módosítsa úgy a paramétereket a a **projekt Akusztika Mixer** érvénybe.

![Mixer testreszabása](media/MixerParameters.png)

* **Wetness igazítás** – beállítja a visszhang energiagazdálkodási, DB, a forrás-figyelő távolság alapján jelenet minden forrás. Pozitív értékek győződjön meg arról, hang több reverberant, amíg a negatív értékek hangjelzést több száraz.
* **RT60 méretezési** – tényezőt skaláris visszhang alkalommal.
* **Használja a Pásztázó** -vezérlők audio-e kimeneti binaural (0) vagy a többcsatornás pásztázásához (1). Bármilyen érték 1 mellett azt jelzi, hogy binaural. Binaural kimeneti a HRTFs spatialized fejhallgatót való használatra, és a többcsatornás kimeneti a VBAP spatialized többcsatornás legyen speaker rendszerekkel lehet használni. Használja a többcsatornás panner, ügyeljen arra, hogy a hangszóró –, amely megfelel az eszköz beállításainál válassza, ha található **Projektbeállítások** > **hang**.

![SpeakerMode](media/SpeakerMode.png)

### <a name="tuning-source-parameters"></a>Forrás paraméterek hangolása
Csatolja a **AcousticsAdjust** forráshoz parancsfájl lehetővé teszi, hogy a hangolási paraméterek ahhoz az adatforráshoz. Csatlakoztassa a parancsfájlt, kattintson a **összetevő felvétele** alján a **vizsgáló** panelen, és keresse meg a **parancsfájlok > Akusztika beállítása**. A szkript hat vezérlők rendelkezik:

![AcousticsAdjust](media/AcousticsAdjust.png)

* **Engedélyezze a Akusztika** – azt szabályozza, hogy Akusztika alkalmazott ebből a forrásból. Ha nincs bejelölve, a forrás lesz spatialized, HRTFs vagy pásztázásához, de nem lesznek nincs Akusztika. Ez azt jelenti, hogy nincs akadály hangelnyelés és dinamikus reverberation paraméterek – például szintjét és decay. Egy rögzített szint és a késleltetési idő reverberation továbbra is érvényesül.
* **Hangelnyelés** -alkalmazása egy szorzóval a Akusztika rendszer által számított hangelnyelés dB szintre. Ha ez szorzó 1-nél nagyobb, hangelnyelés fog kell exaggerated közben értékek 1-nél kisebb ne hangelnyelés hatása több változás is, és a 0 érték letiltja a hangelnyelés.
* **Átviteli (adatbázis)** – állítsa be a gyengülés (az adatbázis) keresztül geometriai átvitel okozta. Állítsa a csúszkát a legalacsonyabb átviteli letiltása. Akusztika körül jelenet geometriai (portaling) érkező, a kezdeti száraz hang spatializes. Átviteli biztosít egy további száraz gyűjtőbe, amely a sor-az-üzemel irányba spatialized van. Vegye figyelembe, hogy a forrás gyengülés távolság görbe is érvényesek.
* **Wetness (adatbázis)** – beállítja a visszhang energiagazdálkodási, dB, a forrás közötti távolságot megfelelően. Pozitív értékek győződjön meg arról, hang több reverberant, amíg a negatív értékek hangjelzést több száraz. Kattintson a görbe szerkesztő megjelenítéséhez a görbe vezérlő (zöld sor). A görbe pontok hozzáadása kattint, és húzza a kívánt kialakításához, a függvény ezen pontok módosítása Az x tengely forrás közötti távolságot, és az y tengely visszhang korrekciós dB-ben. Szerkesztési görbék további információkért lásd: Ez [Unity manuális](https://docs.unity3d.com/Manual/EditingCurves.html). Alaphelyzetbe állítja a görbe vissza az alapértelmezett, kattintson a jobb gombbal **Wetness** válassza **alaphelyzetbe**.
* **Decay időskálára** – Itt adható meg egy szorzóval vonatkozó a késleltetési idő. Például ha az bake eredményt adja meg egy késleltetési idő ezredmásodpercben 750, de ez az érték 1.5 van beállítva, a késleltetési idő a alkalmazni a forrás-, a 1,125 ezredmásodperc.
* **Outdoorness** -additív módosításának a becslés az Akusztika rendszer hogyan "szabadban" hang kell-e a reverberation egy forrás. Az érték 1 fogja elérhetővé tenni egy adatforrás mindig eredményes teljesen szabadban, -1 értékre állítaná készítsen egy forrás eredményes szobában közben.

Különböző forrásokból bizonyos esztétikai vagy játékélményt hatás eléréséhez különböző beállításokat lehet szükség. Párbeszédpanelen egy lehetséges példa. Az emberi sszes törlése reverberation beszéd, a több attuned van, amíg párbeszédpanel gyakran kell lennie a játékélményt érthető. Anélkül, hogy a párbeszédpanelen nem-diegetic helyezi a fiókot is a **Wetness** lefelé, módosításával a **technológiáira távolság Warp** alább leírt paraméter hozzáadásával néhány  **Átviteli** az egyes száraz hang boost propagálása falak használatával, illetve csökkenti a **hangelnyelés** portálokon keresztül érkezzen további megbízható 1-ről.

Csatolja a **AcousticsAdjustExperimental** forráshoz parancsfájl lehetővé teszi, hogy további kísérleti hangolási paraméterek ahhoz az adatforráshoz. Csatlakoztassa a parancsfájlt, kattintson a **összetevő felvétele** alján a **vizsgáló** panelen, és keresse meg a **parancsfájlok > Akusztika beállítása kísérleti**. Jelenleg egy kísérleti vezérlő:

![AcousticsAdjustExperimental](media/AcousticsAdjustExperimental.png)

* **Technológiáira távolság Warp** – exponenciális hajlítása száraz – Nyugat-európai arányának kiszámításához használt távolság a alkalmazni. A Akusztika rendszer kiszámítja a terület teljes nedves szinteket, amelyek zökkenőmentesen távolság számától függ, és adja meg a technológiáira távolság jelek. Hajlítási 1-nél nagyobb értékek a hatás exaggerate távolság kapcsolatos reverberation szintek, így a "távoli" hang növelésével. Hajlítása értékek 1-nél kisebb ügyeljen a távolság-alapú reverberation módosítása több változás is, és, hogy az eredményes további "e".

