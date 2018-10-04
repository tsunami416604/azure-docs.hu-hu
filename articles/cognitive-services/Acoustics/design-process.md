---
title: Akusztika – Cognitive Services a tervezési folyamat áttekintése
description: Ez a dokumentum ismerteti az összes három szakaszban a projekt Akusztika munkafolyamat-Tervező tervez express.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: b6bb04d9cec690198de663189dacd41fcbe960eb
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248604"
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

## <a name="post-bake-design"></a>Utáni bake kialakítása
Bake eredményeket a hozzáférés-vezérlő bejegyzések fájlt minden forrás-figyelő hely pár során a jelenet hangelnyelés és reverberation paraméterek vannak tárolva. A fizikailag pontos eredményt is használható a projekt-van, és kiváló kiindulási pontot (SSO). A utáni bake tervezési folyamat szabályok alkalmazásán át a bake eredménye a paraméterekhez futtatás közben adja meg.

### <a name="distance-based-attenuation"></a>Távolság-alapú gyengülés
A hanganyag DSP által biztosított a **Microsoft Acoustics** Unity spatializer beépülő modul tiszteletben tartja a forrás távolság-alapú gyengülés beépített a Unity-szerkesztőbe. Távolság-alapú gyengülés vezérlők vannak a **hang forrás** összetevő található a **vizsgáló** hang panel alatt adatforrásokat **3D eredményes beállításai**:

![Távolságskála gyengülés](media/distanceattenuation.png)

Akusztika számítási lehetőségek eltérése a lejátszó helyre "szimuláció régió" mezőben hajt végre. Ha egy megbízható forrásból a szimuláció régión kívül található, a lejátszó távolságban belül a mezőbe csak geometriai hatással lesz a hang propagálás (például hangelnyelés okozó), amely viszonylag jól működik, ha occluders vannak-e a Windows Media player közelében. Azonban azokban az esetekben, amikor a Windows Media player nyissa meg a helyet, de a occluders buildjénél a távoli eredményes forrás a hangot is válnak irreálisan disoccluded. A javasolt megoldás, hogy ebben az esetben ellenőrizze, hogy a hangfájl gyengülés kapcsol körülbelül 45 m, a lejátszó az Edge-ben a mező alapértelmezett vízszintes távolság 0.

### <a name="tuning-scene-parameters"></a>Jelenetparaméterek hangolása
Minden forrás paramétereinek beállításához kattintson a csatorna sáv a Unity a **hang Mixer**, és módosítsa úgy a paramétereket a a **Akusztika Mixer** érvénybe.

![Mixer testreszabása](media/MixerParameters.png)

### <a name="tuning-source-parameters"></a>Forrás paraméterek hangolása
Csatolja a **AcousticsDesign** forráshoz parancsfájl lehetővé teszi, hogy a hangolási paraméterek ahhoz az adatforráshoz. A parancsfájl csatolásához kattintson **összetevő felvétele** alján a **vizsgáló** panelen, és keresse meg a **parancsfájlok > Akusztika tervezési**. A szkript hat vezérlők rendelkezik:

![AcousticsDesign](media/AcousticsDesign.png)

* **Hangelnyelés tényező** -alkalmazása egy szorzóval a Akusztika rendszer által számított hangelnyelés dB szintre. Ha ez szorzó 1-nél nagyobb, hangelnyelés fog kell exaggerated közben értékek 1-nél kisebb ne hangelnyelés hatása több változás is, és a 0 érték letiltja a hangelnyelés.
* **Átviteli (adatbázis)** – állítsa be a gyengülés (az adatbázis) keresztül geometriai átvitel okozta. Állítsa a csúszkát a legalacsonyabb átviteli letiltása. Akusztika körül jelenet geometriai (portaling) érkező, a kezdeti száraz hang spatializes. Átviteli biztosít egy további száraz gyűjtőbe, amely a sor-az-üzemel irányba spatialized van. Vegye figyelembe, hogy a forrás gyengülés távolság görbe is érvényesek.
* **Wetness beállítása (adatbázis)** – beállítja a visszhang energiagazdálkodási, dB, a forrás közötti távolságot megfelelően. Pozitív értékek győződjön meg arról, hang több reverberant, amíg a negatív értékek hangjelzést több száraz. Kattintson a görbe szerkesztő megjelenítéséhez a görbe vezérlő (zöld sor). A görbe pontok hozzáadása kattint, és húzza a kívánt kialakításához, a függvény ezen pontok módosítása Az x tengely forrás közötti távolságot, és az y tengely visszhang korrekciós dB-ben. Ez [Unity manuális](https://docs.unity3d.com/Manual/EditingCurves.html) görbék szerkesztéséről további részletekért. Alaphelyzetbe állítja a görbe vissza az alapértelmezett, kattintson a jobb gombbal **Wetness beállítása** válassza **alaphelyzetbe**.
* **Decay időskálára** – Itt adható meg egy szorzóval vonatkozó a késleltetési idő. Például ha az bake eredményt adja meg egy késleltetési idő ezredmásodpercben 750, de ez az érték 1.5 van beállítva, a késleltetési idő a alkalmazni a forrás-, a 1,125 ezredmásodperc.
* **Engedélyezze a Akusztika** – azt szabályozza, hogy Akusztika alkalmazott ebből a forrásból. Ha nincs bejelölve, a forrás lesz kell spatialized HRTFs, de Akusztika, azaz akadály hangelnyelés és dinamikus reverberation paraméterek – például szintjét és decay nélkül nélkül. Egy rögzített szint és a késleltetési idő reverberation továbbra is érvényesül.
* **Outdoorness korrekciós** -additív módosításának a becslés az Akusztika rendszer hogyan "szabadban" hang kell-e a reverberation egy forrás. Ezt a beállítást 1 készítsen egy forrás mindig eredményes teljesen szabadban, a beállítás során,-1 lesz győződjön forrás eredményes szobában.

Különböző forrásokból bizonyos esztétikai vagy játékélményt hatás eléréséhez különböző beállításokat lehet szükség. Párbeszédpanelen egy lehetséges példa. Az emberi sszes törlése reverberation beszéd, a több attuned van, amíg párbeszédpanel gyakran kell lennie a játékélményt érthető. Anélkül, hogy a párbeszédpanelen nem-diegetic helyezi a fiók is a **Wetness beállítása** lefelé, módosításával a **technológiáira távolság Warp** alább leírt paraméter hozzáadásával néhány **Átviteli** az egyes száraz hang boost propagálása falak használatával, illetve csökkenti a **tényező nem érhető el.** portálokon keresztül érkezzen további megbízható 1-ről.

Csatolja a **AcousticsDesignExperimental** forráshoz parancsfájl lehetővé teszi, hogy további kísérleti hangolási paraméterek ahhoz az adatforráshoz. A parancsfájl csatolásához kattintson **összetevő felvétele** alján a **vizsgáló** panelen, és keresse meg a **parancsfájlok > Akusztika tervezési kísérleti**. Jelenleg egy kísérleti vezérlő:

![AcousticsDesignExperimental](media/AcousticsDesignExperimental.png)

* **Technológiáira távolság Warp** – exponenciális hajlítása száraz – Nyugat-európai arányának kiszámításához használt távolság a alkalmazni. A Akusztika rendszer kiszámítja a terület teljes nedves szinteket, amelyek zökkenőmentesen távolság számától függ, és adja meg a technológiáira távolság jelek. Több mint 1 exaggerate a hatás szerint távolság kapcsolatos reverberation szintjei értékek hajlítása, és az eredményes "távoli", míg hajlítása értékek 1-nél kisebb ügyeljen a távolság-alapú reverberation módosítása több változás is, így a hangot több "jelent-e".

