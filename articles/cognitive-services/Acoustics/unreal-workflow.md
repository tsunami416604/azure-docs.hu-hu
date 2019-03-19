---
title: Projekt Akusztika Unreal tervezési oktatóanyag
titlesuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban az Unreal és Wwise projekt Akusztika a Tervező munkafolyamatot ismerteti.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: c65ae71350383896c81fd7057d425822069fc5aa
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136956"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Projekt Akusztika Unreal/Wwise tervezési oktatóanyag
Ez az oktatóanyag ismerteti a Tervező telepítő és a projekt Akusztika Unreal és Wwise.

Szoftver-előfeltételeket:
* A projekt Akusztika Wwise és az Unreal beépülő modulok az Unreal projektben

A projekt Akusztika Unreal projektben lekéréséhez a következőket teheti:
* Kövesse a [projekt Akusztika Unreal integrációs](unreal-integration.md) utasításokat követve adja hozzá a projekt Akusztika az Unreal projekthez
* Másik lehetőségként használhatja a [projekt Akusztika mintaprojektet](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Projekt kiterjedő Wwise tulajdonságainak beállítása
Wwise globális akadály és hangelnyelés görbék, amelyek befolyásolják, hogyan-a projekt Akusztika beépülő modul a Wwise hang DSP meghajtók rendelkezik.

### <a name="design-wwise-occlusion-curves"></a>Wwise hangelnyelés görbék tervezése
Aktív projekt Akusztika esetén reagáljon a hangelnyelés kötetre, alacsony – pass szűrő (LPF), és nagy – pass szűrő (HPF) görbék Wwise beállíthatja. Azt javasoljuk, hogy a görbe kötettípus beállítás lineáris-100 dB-hangelnyelés érték 100 értékkel.

Ezzel a beállítással a projekt Akusztika szimuláció kiszámítja egy hangelnyelés-18 DB, ha azt fogja bemeneti adatokat az az alábbi görbe X = 18-ra, és a megfelelő Y értéke a alkalmazni gyengülés. Ehhez fél hangelnyelés, állítsa a végpontot-50 db-100 dB helyett, vagy-200 dB hangelnyelés exaggerate. Testre szabni, és bármely görbe játékát legjobban finomhangolásához.
 
![Projekt hangelnyelés görbévé](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Tiltsa le a Wwise akadály görbévé
A Wwise akadály görbék hatással a száraz elkülönítési szintet, de projekt Akusztika tervezési vezérlők és szimuláció használ a Nyugat-európai/száraz arányok kényszerítésére. Azt javasoljuk, hogy a akadály kötet görbe letiltása. A wetness tervezését, a később ismertetett Wetness beállítása vezérlőelem használata.
 
Ha akadály LPF/HPF görbék más célra használja, ellenőrizze, hogy őket Y = 0 X = 0 (azaz nem áll fenn LPF vagy HPF nincs akadály esetén).

![Projekt akadály görbévé](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Projekt Akusztika mixer paraméterek tervezése
Globális visszhang tulajdonságok a mixer beépülő modul fülre a projekt Akusztika busz felkeresésével szabályozhatja. Kattintson duplán a "Project Akusztika Mixer (egyéni)" a mixer beépülő modul beállítások panel megnyitásához.

Is láthatja, hogy a mixer beépülő modul rendelkezik egy "Végrehajtása Spatialization" lehetőséget. Ha projekt akusztikai beépített spatialization inkább használna, jelölje be a "Végrehajtása Spatialization" jelölőnégyzetet, és HRTF vagy Pásztázó közül választhat. Mindenképpen tiltsa le minden olyan száraz Aux buszok állított be, ellenkező esetben az előadásban a közvetlen elérési kétszer. A "Wetness módosítása" és "Visszhang idő Mértéktényező" használatával az visszhang vegyesen a globális felügyeljék. Vegye figyelembe kell indítsa újra az Unreal, majd soundbanks újragenerálása előtt szerezze meg a mixer beépülő modul konfigurációs módosítások végrehajtása Spatialization jelölőnégyzet például a Play áruházból.

![A projekt Akusztika mixer beépülő modul beállításai](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Set-projekt Akusztika szabályozza a Wwise actor-mixer-hierarchia megtervezése
Vezérlési paraméterek, az egyes színész mixer kattintson duplán az Aktor Mixer, majd kattintson a Mixer beépülő modul lapon. Itt meg fogja tudni megváltoztatni a paramétereket a hang szintjén. Ezeket az értékeket olyanokra cserélni beállítva (lásd alább), az Unreal oldaláról össze. Például ha a projekt Akusztika Unreal beépülő modul beállítja Outdoorness korrekciós 0.5-ös és Wwise beállítja, hogy-0.25, az eredményül kapott Outdoorness korrekció alkalmazza, amelyek egy objektumra hang, 0,25.

![Hang Mixer-beállítások szerint](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Győződjön meg, hogy a aux bus száraz küldési rendelkezik, és a kimeneti bus rendelkezik nedves küldése
Ne feledje, hogy a szükséges actor-mixer telepítő felcserélődések a szokásos száraz és nedves útválasztás Wwise. Visszhang jel (projekt Akusztika buszhoz set) az aktor mixer kimeneti buszon, és a felhasználó által definiált aux bus mentén száraz jel küld. Az útválasztás az szükséges, mert a Wwise mixer beépülő modul API-t a projekt Akusztika Wwise beépülő modult használó funkcióit.

![Hangalapú tervezési útmutató](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Távolságskála gyengülés görbék beállítása
Győződjön meg, hogy minden színész vonja használatával által használt gyengülés görbe projekt Akusztika kell elküldeni a "kimeneti bus kötet." értékre van állítva, felhasználó által definiált aux Wwise ezt végzi, az újonnan létrehozott gyengülés görbék alapértelmezés szerint. Ha egy meglévő projekt migráláshoz, ellenőrizze a görbe beállításait. 

Alapértelmezés szerint a projekt Akusztika szimuláció sugara 45 mérőszámok körül player helyét. Általában javasoljuk, hogy a gyengülés görbe-200 dB köré, hogy távolság beállítást. Ez a távolság nem szigorú korlátozás. Néhány fegyverek hangzás érdemes nagyobb radius. Ezekben az esetekben csoportosítani, hogy csak a lejátszó hely 45 m belül geometriai részt vesz. Ha a Windows Media player van a helyiségben, és a egy megbízható forrásból a hely, és azonnal 100m kívül esik, azt fogja kell megfelelően occluded. Ha a forrása a hely és a Windows Media player kívül, és azonnal 100 m, azt nem lehet megfelelően occluded.

![Gyengülés görbévé](media/atten-curve.png)

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Jelenet kiterjedő projekt Akusztika tulajdonságainak beállítása

A Akusztika terület aktor tesz elérhetővé számos szabályozza, hogy a rendszer működésének módosítása és a hibakeresést lehetnek hasznosak.

![Akusztika terület vezérlők](media/acoustics-space-controls.png)

* **Akusztika adatok:** Ezt a mezőt hozzá kell rendelni a létrehozott Akusztika eszköz a tartalom/Akusztika könyvtárból. A projekt Akusztika beépülő modul automatikusan hozzáadja a tartalom/Akusztika könyvtár csomagolt könyvtárakat a projekthez.
* **Csempe méretéhez:** Az egységek a régió, a figyelő, amely betölti a RAM-MAL Akusztika adatokat szeretne körül. Mindaddig, amíg figyelő azonnal mintavételek körül a Windows Media player betöltött, az eredmény ugyanaz, mint az összes mintavételek akusztikai adatainak betöltése nem. Nagyobb csempék használjon több RAM Memóriát, de a lemez I/O csökkentése
* **Automatikus Stream:** Ha engedélyezve van, automatikusan betölti az új csempét, a figyelő eléri az edge betöltött régió. Ha le van tiltva, kell betölteni a csempék manuálisan a kód vagy tervezetek keresztül
* **Gyorsítótár beosztás:** akusztikai lekérdezésekhez a gyorsítótár méretét szabályozza. Egy kisebb méretű gyorsítótár kevesebb RAM használja, de előfordulhat, hogy növelje a CPU-használata az egyes lekérdezések.
* **Akusztika engedélyezve:** A hibakeresési-vezérlőbe való gyors A / B a Akusztika szimuláció hordozását. Ez a vezérlő rendszer figyelmen kívül hagyja a szállítási konfigurációkat. A vezérlő hasznos keresése, ha egy adott hang hiba származik, a Akusztika számítások vagy valamilyen más típusú problémával a Wwise projektben.
* **Milyen távolságra frissítése:** Használja ezt a beállítást, ha szeretné távolság lekérdezések az előre létrehozott Akusztika információkat használja. Ezek a lekérdezések ray típuskonverziók hasonló, de voltak előre kiszámított úgy is sokkal kevesebb Processzor. Egy példa a használatra szolgál ki, a figyelő a legközelebbi felület diszkrét fényt. Teljes mértékben kihasználhatja a, kell kód vagy tervek használatával lekérdezés esetén.
* **Draw Stats:** Miközben UE a `stat Acoustics` , Processzor, a üzenettípusonként információkat tartalmazó jelennek meg a jelenleg betöltött térképen, a RAM-használatot, és a képernyő bal felső egyéb állapotinformáció.
* **Rajzoljon Voxels:** A figyelő a futtatókörnyezet interpolációs során használt voxel rács megjelenítése közel átfedő voxels. Ha egy modul voxel belül kibocsátónak, akusztikai lekérdezések nem fog működni.
* **Megrajzolja a mintavétel:** A helyszín számára a mintavételezők megjelenítése. A betöltési állapota függően különböző színek lesz.
* **Milyen távolságra rajzolása:** Ha frissítési Távokat engedélyezve van, ez jeleníti meg a mezőben a legközelebbi felületen kvantált irányban körül a figyelő a figyelővel.

## <a name="actor-specific-acoustics-design-controls"></a>Actor-specifikus Akusztika tervezési vezérlők
Ezek a tervezési vezérlők hatóköre az Unreal egyedi hang összetevője.

![Hang vezérlőelemei](media/audio-component-controls.png)

* **Hangelnyelés szorzó:** Azt szabályozza, hogy a hangelnyelés hatást. Értékek a hangelnyelés tartóssá > 1. Értékek < 1 lesz minimalizálása érdekében.
* **Wetness beállítása:** További visszhang dB
* **Decay idő szorzó:** Szabályozza a RT60 multiplicatively, alapján a Akusztika szimuláció kimenete
* **Outdoorness beállítása:** Azt szabályozza, is hogyan lehet a reverberation van. Értékek közelebb 0-ra több szobában, 1 közelebb van több is lehet. Ez a módosítás additív, így szobában, -1 értékre állítaná kényszeríti + 1 értékre állítaná kényszeríti szabadban.
* **Átviteli Db:** Egy további keresztül-a-fal hangot a hangerő-vonal az üzemel, kombinálva renderelési távolság gyengülés alapján.
* **Távolságskála lánc nedves arány:** Itt állíthatja be a forrás reverberation jellemzőit, mintha közelebb/további lapot, anélkül, hogy befolyásolná a közvetlen elérési útja.
* **Akusztikai paraméterek megjelenítése:** Adatok megjelenítése a debug közvetlenül az összetevőt, a játékbeli felett. (csak a nem a szállításhoz konfigurációk)

## <a name="blueprint-functionality"></a>Tervrajz funkció
A Akusztika terület aktor blueprint-en keresztül elérhető funkciók, például egy térkép betöltése vagy keresztül szintű parancsfájl-kezelési beállítások módosítása. Itt a két példa biztosítunk.

### <a name="add-finer-grained-control-over-streaming-load"></a>Részletesebben a felett streaming betöltési hozzáadása
A kényszerített terhelés csempe tervezet funkció használata kezelheti a streamelési saját maga helyett adatfolyam-lejátszó pozíció alapján automatikusan akusztikai adathoz:

![Streamelési B P](media/blueprint-streaming.png)

* **Cél:** A AcousticsSpace aktor
* **Központ helye:** A központ, a régiót, amelyben van szüksége az adatok betöltése
* **Mintavételezők kívül csempe eltávolítása:** Ha be van jelölve, az új régióban nem minden mintavételek el lesz távolítva, a RAM-MAL. Ha nincs bejelölve, az új régióban betölti a memóriába, amíg a mintavételek elhagyó is betölti a memóriába
* **Blokk befejezését követően:** Lehetővé teszi a csempe betöltése egy szinkronizált művelet

Csempe méretéhez már állítson be kényszerített terhelés csempe hívása előtt. Ha például ezt be ACE-fájlt, a csempe méretének beállítása és a egy régióban adatfolyam ehhez hasonló:

![Streamelési beállítása](media/streaming-setup.png)

### <a name="optionally-query-for-surface-proximity"></a>Szükség esetén a surface közelségi lekérdezés
Ha azt szeretné, hogy milyen közel felületek körül a figyelő egy adott irányba, a lekérdezés távolság függvényt is használhatja. Ez a funkció akkor lehet hasznos, a késleltetett fényt irányt vagy más surface közelségi által készített játék logika. A lekérdezés nem kevésbé költséges, mint egy ray cast, mert a Akusztika keresési tábla kikerülnek az eredményeket.

![Távolságskála lekérdezés](media/distance-query.png)

* **Cél:** A AcousticsSpace aktor
* **Keresse meg a iránya:** Az irány, lekérdezéséhez, a figyelő középre
* **Távolságskála:** Ha a lekérdezés sikeres, a legközelebbi felületére távolság
* **A visszatérési érték:** Logikai érték – igaz, ha a lekérdezés sikeresen befejeződött, egyébként hamis

## <a name="next-steps"></a>További lépések
* Fedezze fel a fogalmakat a [folyamat tervezése](design-process.md)
* [Azure-fiók létrehozása](create-azure-account.md) a saját jelenet os


