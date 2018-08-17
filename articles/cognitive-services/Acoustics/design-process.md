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
ms.openlocfilehash: 3b35f78d66a6ae66b0a56818f5d4be455ce00de5
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181694"
---
# <a name="design-process-overview"></a>Tervezési folyamat áttekintése
Az összes három szakaszban a projekt Akusztika munkafolyamat-Tervező tervez fejezhető: előre a Tervező, megbízható forrás elhelyezési és utáni bake tervezési os. A folyamat kevesebb jelölőnyelvi társított visszhang kötetek elhelyezése Tervező szabályozhatja, hogyan álló jelenet renderelése; hangok megőrzése szükséges.

## <a name="pre-bake-design"></a>Üzem előtti bake kialakítása
Az üzem előtti bake tervezési folyamat hoz létre a helyszín és a metaadatokat, amelyek használhatók a hangot wave szimuláció, amely kiválasztásával jelenet elemek részt vesz a szimuláció occlusions, tükröződés és reverberation biztosít, amely tartalmazza. A helyszín metaadatai a kijelölés akusztikai anyagok minden jelenet elemhez. Az akusztikai anyagok ellenőrzés kerülnek vissza minden egyes felületből eredményes energia mennyiségét.

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

### <a name="tuning-scene-parameters"></a>Jelenetparaméterek hangolása
Minden forrás paramétereinek beállításához kattintson a csatorna sáv a Unity a **hang Mixer**, és módosítsa úgy a paramétereket a a **Akusztika Mixer** érvénybe.

![Mixer testreszabása](media/MixerParameters.png)

### <a name="tuning-source-parameters"></a>Forrás paraméterek hangolása
Csatolja a **AcousticsSourceCustomization** forráshoz parancsfájl lehetővé teszi, hogy a hangolási paraméterek ahhoz az adatforráshoz. A parancsfájl csatolásához kattintson **összetevő felvétele** alján a **vizsgáló** panelen, és keresse meg a **parancsfájlok > Akusztika forrás testreszabási**. A szkript három paraméterekkel rendelkezik:

![Forrás testreszabása](media/SourceCustomization.png)

* **Power beállítása visszhang** – beállítja a visszhang energiagazdálkodási, dB-ben. Pozitív értékek győződjön meg arról, hang több reverberant, amíg a negatív értékek hangjelzést több száraz.
* **Decay időskálára** – Itt adható meg egy szorzóval vonatkozó a késleltetési idő. Például ha az bake eredményt adja meg egy késleltetési idő ezredmásodpercben 750, de ez az érték 1.5 van beállítva, a késleltetési idő a alkalmazni a forrás-, a 1,125 ezredmásodperc.
* **Engedélyezze a Akusztika** – azt szabályozza, hogy Akusztika alkalmazott ebből a forrásból. Ha nincs bejelölve, a forrás lesz kell spatialized HRTFs, de Akusztika, azaz akadály hangelnyelés és dinamikus reverberation paraméterek – például szintjét és decay nélkül nélkül. Egy rögzített szint és a késleltetési idő reverberation továbbra is érvényesül.

Különböző forrásokból bizonyos esztétikai vagy játékélményt hatás eléréséhez különböző beállításokat lehet szükség. Párbeszédpanelen egy lehetséges példa. Az emberi sszes törlése reverberation beszéd, a több attuned van, amíg párbeszédpanel gyakran kell lennie a játékélményt érthető. Ez anélkül, hogy a párbeszédpanelen nem-diegetic lefelé a visszhang power fokozottabban fiók.
