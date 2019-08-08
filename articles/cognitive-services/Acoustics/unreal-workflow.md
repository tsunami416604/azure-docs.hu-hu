---
title: A Project akusztikai Unreal design oktatóanyaga
titlesuffix: Azure Cognitive Services
description: Ez az oktatóanyag az Unreal és a Wwise projekt-akusztikai tervezési munkafolyamatát ismerteti.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 817a11171c5b4b4ef205e5fbb04f9b6d6d85b248
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854247"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Projekt akusztikai Unreal/Wwise tervezési oktatóanyag
Ez az oktatóanyag az Unreal és a Wwise projekt-akusztikai tervezési beállításait és munkafolyamatait ismerteti.

Szoftver előfeltételei:
* Egy Unreal-projekt a Project Acoustics Wwise és az Unreal plugins

Ahhoz, hogy egy Unreal-projektet a Project Acoustics használatával kapjon, a következőket teheti:
* Kövesse a [Project Acoustics Unreal integrációs](unreal-integration.md) utasításait, hogy a projekt akusztikai adatait hozzáadja az Unreal projekthez
* Vagy használja a [Project akusztikai minta projektjét](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Az egész projektre kiterjedő Wwise tulajdonságainak beállítása
A Wwise globális akadályt és elzáródási görbéket tartalmaz, amelyek befolyásolják, hogy a Project akusztikai beépülő modulja hogyan vezeti a Wwise audio DSP-t.

### <a name="design-wwise-occlusion-curves"></a>Tervezési Wwise elzáródás görbék
Ha a Project Acoustics aktív, az a Wwise-ben beállított elzáródási kötetre, a kis pass szűrőre (LPF) és a nagy pass szűrőre (HPF) vonatkozó görbékre reagál. Azt javasoljuk, hogy a kötet-görbe típusát állítsa lineárisra a-100 dB értékkel a 100-es Elzáródási értékhez.

Ezzel a beállítással, ha a projekt akusztikai szimulációja a-18 dB elzáródását számítja ki, az alábbi görbét adja az X = 18 értéknél, a megfelelő Y érték pedig a csillapítást alkalmazza. A Half elzáródás elvégzéséhez állítsa a végpontot-50 dB-re a-100 dB helyett, vagy a-200 dB-t az eltúlzott elzáródáshoz. Testre szabhatja és finomíthatja a játékhoz legjobban illeszkedő görbéket.
 
![Képernyőfelvétel a Wwise elzáródási görbe szerkesztőjéről](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Wwise-elzáródási görbék letiltása
A Wwise-elzáródási görbék elkülönítik a száraz szintet, de a Project Acoustics a tervezési vezérlők és a szimuláció használatával kényszeríti ki a nedves/száraz arányt. Javasoljuk, hogy tiltsa le az akadályozási kötet görbéjét. A nedvesség megtervezéséhez használja a később ismertetett nedvesség-módosítási vezérlőt.
 
Ha más célra akadályozó LPF/HPF görbéket használ, győződjön meg róla, hogy az Y = 0 értéknél beállította az X = 0 értéket (azaz nincs LPF vagy HPF, ha nincs akadály).

![Képernyőkép a Wwise-elzáródási görbe szerkesztőjéről](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Projekt akusztikai keverő paramétereinek megtervezése
A globális reverb-tulajdonságokat a Project akusztikai Buszjának keverő beépülő modul lapján érheti el. A keverő beépülő modul beállításainak paneljének megnyitásához kattintson duplán a "Project akusztikai keverő (egyéni)" elemre.

Azt is láthatja, hogy a keverő beépülő modul "spatialization végrehajtása" lehetőséget is tartalmaz. Ha inkább a Project Acoustic beépített spatialization használja, jelölje be a "spatialization végrehajtása" jelölőnégyzetet, és válasszon a HRTF vagy a pásztázás közül. Ügyeljen arra, hogy tiltsa le a beállított összes száraz Aux-buszt, ellenkező esetben a közvetlen elérési utat kétszer fogja hallani. A "nedvesség-módosítás" és a "reverb időméretezési tényező" használatával globálisan vezérelheti a reverb-kombinációt. Megjegyzés: a soundbanks újraindításához újra kell indítania az Unreal-t, majd újra létre kell hoznia a lejátszást a keverő beépülő modul konfigurációs módosításaival, például az "spatialization végrehajtása" jelölőnégyzettel.

![Képernyőfelvétel a Project akusztikai Wwise keverő beépülő moduljának beállításairól](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>A Project Acoustics tervezési vezérlőinek beállítása a Wwise Actor-mixer hierarchiában
Az egyes Actors-keverő paramétereinek szabályozásához kattintson duplán a Actor-mixer elemre, majd kattintson a keverő beépülő modul fülére. Itt bármilyen paramétert módosíthat a hangszintesen. Ezek az értékek kombinálva vannak az Unreal oldalon beállított értékekkel (lásd alább). Ha például a Project Acoustics Unreal beépülő modulja egy objektumon a 0,5-re állítja a kivetítési beállítást, és a Wwise beállítja a-0,25 értékre, az adott hangra alkalmazott, eredményül kapott stabilitási beállítás 0,25.

![Képernyőfelvétel a hangkeverők beállításairól a Wwise Actor-mixer-hierarchia esetében](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Győződjön meg arról, hogy az AUX-busz rendelkezik a száraz küldéssel, és a kimeneti busz nedves küldéssel rendelkezik
Ne feledje, hogy a szükséges Actor-mixer-telepítő megváltoztatja a szokásos száraz és nedves útválasztást a Wwise-ben. Reverb-jeleket állít elő a Actor-mixer kimeneti buszján (a Project Acoustics Bus beállítása) és a száraz jelet a felhasználó által megadott Aux-buszon. Ez az útválasztás szükséges ahhoz, hogy a Wwise keverő beépülő modul API-ját a Project Acoustics Wwise beépülő modulja használja.

![Képernyőkép a Wwise-Szerkesztőről a Project Acoustics hangtervezési iránymutatásainak megjelenítéséhez](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Távolsági gyengítő görbék beállítása
Győződjön meg arról, hogy az Actors-keverők által használt összes gyengítő görbe a Project Acoustics használatával felhasználó által definiált Aux küldési készlettel rendelkezik a kimeneti busz kötetéhez. A Wwise alapértelmezés szerint az újonnan létrehozott gyengítő görbékre vonatkozik. Ha meglévő projektet telepít át, ellenőrizze a görbe beállításait.

Alapértelmezés szerint a projekt akusztikai szimulációja 45 méteres sugarú, amely a játékos helye körül van. Általában azt javasoljuk, hogy a gyengítő görbét a-200 dB értékre állítsa a távolság körül. Ez a távolság nem rögzített korlátozás. Bizonyos hangok, például a fegyverek esetében érdemes nagyobb sugarat használni. Ilyen esetekben a kikötés azt eredményezi, hogy csak a játékos helyének 45 m-en belüli geometriák fognak részt venni. Ha a lejátszó egy helyiségben található, és a hangforrás a helyiségen kívül esik, és 100 méter távolságra van, akkor a rendszer megfelelően kikerül. Ha a forrás egy helyiségben van, és a lejátszó kívül van, és 100 m távolságban van, nem lesz megfelelően leállítva.

![Képernyőkép a Wwise csillapítási görbékről](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Keverő utáni kiegyenlítő ###
 Egy másik dolog, amit érdemes megtennie, vegyen fel egy post mixer Equalizert. A projekt akusztikai buszát tipikus reverb-buszként kezelheti (alapértelmezett reverb módban), és egy szűrőt is beállíthat, hogy kiegyenlítő legyen. Ebből a projektből megtekintheti ezt a mintát a Project Acoustics Wwise.

![Képernyőfelvétel a Wwise utáni EQ-ról](media/wwise-post-mixer-eq.png)

A magas szintű pass szűrő például segít kezelni a mélyhang-felvételt, amely a boom, a nem reális reverb. Több post-Bake-szabályozást is elérhet, ha az EQ-t a RTPCs-on keresztül állítja be, így a játék időpontjában megváltoztathatja a reverb színét.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Scene-Wide Project akusztikai tulajdonságok beállítása

Az akusztikai terület színésze számos olyan vezérlőelemet tesz elérhetővé, amelyek a rendszer viselkedését módosítják, és a hibakeresés során hasznosak.

![Az Unreal akusztikai Space Controls képernyőképe](media/acoustics-space-controls.png)

* **Akusztikai adatvédelem:** Ezt a mezőt a Content/akusztikai könyvtárból származó, sült akusztikai eszközhöz kell rendelni. A Project Acoustics beépülő modul automatikusan hozzáadja a tartalom/akusztikai könyvtárat a projekt csomagolt könyvtáraihoz.
* **Csempe mérete:** A figyelő azon területének a mérete, amelyre az akusztikai adatmennyiséget szeretné betölteni a MEMÓRIÁba. Ha a figyelő a lejátszót közvetlenül a játékos betöltésével tölti be, az eredmények ugyanazok, mint az összes mintavétel akusztikai adatai. Nagyobb méretű csempék több MEMÓRIÁT használnak, de csökkentik a lemez I/O-t
* **Automatikus stream:** Ha engedélyezve van, a automatikusan betölti az új csempéket, mivel a figyelő eléri egy betöltött régió szegélyét. Ha le van tiltva, az új csempéket kód vagy tervrajzok használatával manuálisan kell betölteni
* **Gyorsítótár-méretezés:** az akusztikai lekérdezésekhez használt gyorsítótár méretét szabályozza. A kisebb gyorsítótár kevesebb MEMÓRIÁT használ, de az egyes lekérdezéseknél növelheti a CPU-használatot is.
* **Akusztika engedélyezve:** Hibakeresési vezérlő, amely lehetővé teszi az akusztikai szimuláció gyors A/B-es kapcsolását. A rendszer figyelmen kívül hagyja ezt a vezérlőt a szállítási konfigurációkban. A vezérlő hasznos lehet annak megállapításához, hogy egy adott hanghiba az akusztikai számításokban vagy valamilyen más, a Wwise-projektben található problémából származik-e.
* **Frissítési távolságok:** Akkor használja ezt a beállítást, ha a távolsági lekérdezésekhez az előre lesütött akusztikai adatokat szeretné használni. Ezek a lekérdezések hasonlóak a Ray-öntvényekhez, de előre kiszámítva lettek, így sokkal kevesebb CPU-t vesznek igénybe. A használati példa a legközelebbi felületről a figyelőre irányuló diszkrét reflexiók esetében használható. A teljes körű kihasználása érdekében programkódot vagy tervrajzokat kell használnia a távolságok lekérdezéséhez.
* **Statisztikák rajzolása:** Míg az UE `stat Acoustics` -k a CPU-adatokat is megadhatják, ez az állapot jelenik meg a képernyő bal felső részén a jelenleg betöltött Ace-fájl, a RAM-használat és egyéb állapotadatok megjelenítésével.
* **Voxels rajzolása:** Az átfedésben lévő voxels ahhoz a figyelőhöz közeledik, amely a futásidejű interpoláció során használt Voxel-rácsot mutatja. Ha a kibocsátó egy futásidejű Voxel belül van, akkor az akusztikai lekérdezések sikertelenek lesznek.
* **Mintavételek rajzolása:** A jelenet összes mintavételének megjelenítése. A betöltési állapotuktól függően eltérő színűek lesznek.
* **Távolságok rajzolása:** Ha a frissítési távolságok engedélyezve vannak, a figyelő a legközelebbi felületen jeleníti meg a figyelőt a figyelő által megjelenő kvantálási irányban.

## <a name="actor-specific-acoustics-design-controls"></a>Actor-specifikus akusztikai tervezési vezérlők
Ezek a kialakítási vezérlők hatóköre az Unreal egyedi hangösszetevője.

![Képernyőkép az Unreal audio Component Controls szolgáltatásról](media/audio-component-controls.png)

* **Elzáródási szorzó:** Az elzáródás hatásának szabályozása. Az 1. > értékek kiegészítik a elzáródást. Az 1 érték < a lehető legkisebbre állítja.
* **Nedvesség-beállítás:** További reverb dB
* **Romlási idő szorzója:** A RT60 multiplicatively vezérli az akusztikai szimuláció kimenete alapján
* **A belső beállítás beállítása:** Meghatározza, hogy a visszaverődés milyen módon legyen a szabadban. A 0 értékkel közelebbi értékek több beltérben találhatók, és az 1-nél közelebb vannak a szabadban. Ez a beállítás adalékanyag, ezért ha az-1 értékre állítja a beállítást, a rendszer a + 1 értékre állítja a szabadban.
* **Átviteli adatbázis:** Az ezzel a hangerővel ellátott, a szemtől távol álló távolság csillapításával kombinálva jeleníthető meg egy további, a falra épülő hang.
* **Nedves arány távolságának hajlítása:** A forráshoz tartozó visszaverődési tulajdonságokat úgy állítja be, hogy az a közvetlen elérési úttal nem befolyásolható legyen, és hogy közelebb/távolabb lenne.
* **Lejátszás a Start menüben:** Váltás annak megadásához, hogy a hangot automatikusan le kell-e játszani a jelenet indításakor. Alapértelmezés szerint engedélyezve van.
* **Akusztikai paraméterek megjelenítése:** Hibakeresési információk megjelenítése közvetlenül a játékon belüli összetevő tetején. (csak a nem szállítási konfigurációk esetében)

## <a name="blueprint-functionality"></a>A terv funkciói
Az akusztikai terület színésze a tervrajzon keresztül érhető el, és olyan funkciókat biztosít, mint például a Térkép betöltése vagy a beállítások módosítása a szintű parancsfájlok használatával. Két példát is biztosítunk.

### <a name="add-finer-grained-control-over-streaming-load"></a>Az adatfolyam terhelésének finomabb szintű szabályozása
Ha az adatfolyamot nem a lejátszó pozíciója alapján automatikusan szeretné felügyelni, hanem saját maga is kezelheti az akusztikus adatfolyamot, használja a Force Load csempe Blueprint függvényt:

![Képernyőkép a Blueprint streaming lehetőségeiről az Unreal-ben](media/blueprint-streaming.png)

* **Cél** A AcousticsSpace Actor
* **Középpont pozíciója:** A régió középpontja, amelyhez be kell tölteni az adatmennyiséget
* **A csempén kívüli mintavételek eltávolítása:** Ha be van jelölve, az új régióban nem szereplő összes mintavétel el lesz távolítva a MEMÓRIÁból. Ha nincs bejelölve, a rendszer betölti az új régiót a memóriába, miközben a meglévő mintavételek is betöltődik a memóriába.
* **Blokkolás befejezésekor:** A csempe egy szinkron művelet betöltését teszi lehetővé

A csempe méretét már be kell állítani a kényszerített betöltési csempe hívása előtt. Például az alábbihoz hasonló módon teheti meg az ACE-fájlok betöltését, a csempék méretét és a streamet egy adott régióban:

![Képernyőkép a streaming telepítési beállításairól az Unreal-ben](media/streaming-setup.png)

A példában használt Load akusztikai adattervi függvény a következő paraméterekkel rendelkezik:

* **Cél** A AcousticsSpace színész.
* **Új sütni:** A betöltendő akusztikai adategység. Ha ezt a mezőt üresen hagyja, vagy nullára állítja, akkor az aktuális sütjük nem töltődik be újra.

### <a name="optionally-query-for-surface-proximity"></a>Opcionális lekérdezés a felszíni közelséghez
Ha szeretné megtekinteni, hogy a felületek milyen közel vannak a figyelőhöz, használhatja a lekérdezési távolság funkciót. Ez a függvény hasznos lehet az irányított késleltetett reflexiók, illetve a felszíni közelség által vezérelt más játékokhoz. A lekérdezés kevésbé költséges, mint a Ray Cast, mert az eredmények az akusztikai keresési táblázatból származnak.

![Képernyőkép – példa a terv távolságára – lekérdezés](media/distance-query.png)

* **Cél** A AcousticsSpace Actor
* **Nézet iránya:** A lekérdezés iránya, középpontban a Figyelőnél
* **Távolság** Ha a lekérdezés sikeres, a legközelebbi felület távolsága
* **Visszatérési érték:** Logikai – igaz, ha a lekérdezés sikeres, ellenkező esetben hamis

## <a name="next-steps"></a>További lépések
* Ismerje meg a [tervezési folyamat](design-process.md) hátterében rejlő fogalmakat
* [Hozzon létre egy Azure-fiókot](create-azure-account.md) saját jelenetének sütni


