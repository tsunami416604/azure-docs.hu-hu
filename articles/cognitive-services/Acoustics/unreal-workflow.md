---
title: Projekt akusztika Unreal Design oktatóanyag
titlesuffix: Azure Cognitive Services
description: Ez az oktatóanyag az Unreal és a Wwise Project Acoustics tervezési munkafolyamatát ismerteti.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854247"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Project Akusztika Unreal / Wwise Design tutorial
Ez az oktatóanyag ismerteti az Unreal és a Wwise Project Acoustics tervezési beállítását és munkafolyamatát.

Szoftver előfeltételei:
* Egy Unreal projekt a Project Acoustics Wwise és Unreal dugó

Ha egy Unreal projektet szeretne a Project Acoustics programmal, a következőket teheti:
* Kövesse a [Project Acoustics Unreal integrációs](unreal-integration.md) utasításait, hogy a Project Acoustics-t hozzáadja az Unreal projekthez
* Vagy használja a [Project Acoustics mintaprojektet.](unreal-quickstart.md)

## <a name="setup-project-wide-wwise-properties"></a>Projektszintű Wwise-tulajdonságok beállítása
A Wwise globális obstrukciós és elzáródási görbéi befolyásolják, hogy a Project Acoustics plugin hogyan hajtja a Wwise audio DSP-t.

### <a name="design-wwise-occlusion-curves"></a>Tervezzen Wwise elzáródási görbéket
Ha a Project Acoustics aktív, az a Wwise-ban beállított elzáródási térfogatra, alulátadó szűrőre (LPF) és hpf-görbére reagál. Azt javasoljuk, hogy a térfogatgörbe típusát -100 dB értékkel lineárisra állítja egy 100-as okklúziós értékesetén.

Ezzel a beállítással, ha a Project Acoustics szimuláció -18 dB elzáródást számít ki, akkor az X=18-nál az alábbi görbére kerül, és a megfelelő Y érték az alkalmazott csillapítás. A fél elzáródáshoz állítsa a végpontot -50 dB-re a -100 dB helyett, vagy -200 dB-re az elzáródás eltúlzására. Testre szabhatja és finomhangolhatja a játékához leginkább megfelelő görbéket.
 
![A Wwise elzáródási görbe szerkesztőjének képernyőképe](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Wwise akadálygörbék letiltása
A Wwise akadálygörbék elszigetelten befolyásolják a száraz szintet, de a Project Acoustics tervezési vezérlőket és szimulációt használ a nedves/száraz arányok kikényszerítésére. Javasoljuk, hogy tiltsa le az akadály térfogatgörbéjét. A nedvesség megtervezéséhez használja a később ismertetett Wetness Adjust vezérlőt.
 
Ha más célokra használja az akadályLPF/HPF görbéket, győződjön meg arról, hogy azokat Y=0 értékre állította x=0-nál (azaz nincs LPF vagy HPF, ha nincs akadály).

![Képernyőkép a Wwise akadálygörbe szerkesztőjéről](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Project Acoustics mixer paramétereinek tervezése
A globális zengetési tulajdonságokat a Project Acoustics Bus keverőbeépülő lapján szabályozhatja. Kattintson duplán a "Project Acoustics Mixer (Custom)" elemre a keverőbővítmény beállítási paneljének megnyitásához.

Azt is láthatja, hogy a keverő beépülő modul "Térterület-kezelés végrehajtása" opcióval rendelkezik. Ha inkább a Project Acoustic beépített térbelivét, jelölje be a "Térterület-kezelés végrehajtása" jelölőnégyzetet, és válasszon a HRTF vagy a Pásztázás közül. Győződjön meg róla, hogy tiltsa le a Száraz Aux buszok van beállítva, különben hallani fogja a közvetlen utat kétszer. Használja a "Wetness Adjust" és a "Reverb Time Scale Factor" gyakorolni a globális ellenőrzést a zengetés mix. Ne feledje, hogy újra kell indítania az Unreal-t, majd regenerálnia kell a soundbanks-t, mielőtt a lejátszást megnyomná, hogy felvegye a mixer plugin konfigurációs változásokat, például a "Térterület-kezelés végrehajtása" jelölőnégyzetet.

![Képernyőkép a Project Acoustics Wwise mixer beépülő modul beállításairól](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>A Project Akusztika tervezési vezérlőinek beállítása a Wwise aktakeverő hierarchiában
Az egyes aktorkeverők paramétereinek szabályozásához kattintson duplán a Actor-Mixer billentyűt, majd kattintson a Mixer beépülő modul fülre. Itt bármilyen paramétert módosíthat a hangszinten. Ezek az értékek az Unreal oldalról beállított értékekkel kombinálódnak (lásd alább). Ha például a Project Acoustics Unreal beépülő modul 0,5-re állítja a Kültéri korrekció beállítást egy objektumon, és a Wwise -0,25-re állítja, akkor az adott hangra alkalmazott kültéri korrekció 0,25 lesz.

![Képernyőkép a Wwise akta-keverő hierarchia hangkeverő-beállításairól](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Győződjön meg róla, hogy az aux busz száraz küldést biztosít, és a kimeneti busz nedves küldési
Ne feledje, hogy a szükséges aktorkeverő beállítás a wwise-ban a szokásos száraz és nedves útválasztást cseréli. Zengetésjelet hoz létre az aktorkeverő kimeneti buszán (a Project Acoustics Bus-ra állítva), és száraz jelet a felhasználó által definiált aux busz mentén. Ez az útválasztás szükséges a Wwise mixer plugin API funkciói miatt, amelyeket a Project Acoustics Wwise plugin használ.

![Képernyőkép a Wwise szerkesztőről, amely a Project Acoustics hangtervezési irányelveit mutatja be](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Távolságcsillapítási görbék beállítása
Győződjön meg arról, hogy a Project Acoustics használatával az aktorkeverők által használt csillapítási görbék a felhasználó által definiált aux küldési beállítással rendelkeznek a "kimeneti busz térfogata" beállításra. A Wwise alapértelmezés szerint ezt teszi az újonnan létrehozott csillapítási görbék esetében. Ha meglévő projektet telepít át, ellenőrizze a görbe beállításait.

Alapértelmezés szerint a Project Acoustics szimuláció sugara 45 méter a játékos helye körül. Általában azt javasoljuk, hogy a csillapítási görbét -200 dB-re kell alapozni a távolság körül. Ez a távolság nem egy nehéz megkötés. Néhány úgy hangzik, mint a fegyverek érdemes egy nagyobb sugarú. Ilyen esetekben, a kikötés, hogy csak a geometria belül 45 m a játékos helyét vesz részt. Ha a játékos egy szobában van, és a hangforrás a szobán kívül van, és 100 méterre van, akkor megfelelően ellesz zárva. Ha a forrás egy szobában van, és a játékos kívül van, és 100 méterre van, akkor nem lesz megfelelően elzárva.

![A Wwise csillapítási görbéinek képernyőképe](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Mixer kiegyenlítése utáni ###
 Egy másik dolog, amit érdemes csinálni, hogy adjunk egy post mixer equalizer. A Project Acoustics buszt tipikus zengetős buszként kezelheti (alapértelmezett zengetés módban), és szűrőt helyezhet rá a kiegyenlítéshez. Ennek egy mintája látható a Project Acoustics Wwise mintaprojektben.

![Képernyőkép a Wwise keverő utáni EQ-ról](media/wwise-post-mixer-eq.png)

Például egy magas áteresztőszűrő segíthet kezelni a basszust a közeli hangfelvételekből, amelyek boomy, irreális zengetést eredményeznek. Az EQ rtpc-ken keresztüli beállításával több post-bake vezérlést is elérhetsz, lehetővé téve a zengetés színének megváltoztatását a játék időben.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>A project akusztikai tulajdonságainak beállítása az egész jelenetre kiterjedően

Az Akusztikai terület szereplője számos olyan vezérlőt tár fel, amelyek módosítják a rendszer működését, és hasznosak a hibakeresésben.

![Képernyőkép az Unreal Acoustics térvezérléséről](media/acoustics-space-controls.png)

* **Akusztikai adatok:** Ezt a mezőt a Content/Acoustics könyvtárból egy sült akusztikai eszközhöz kell rendelni. A Project Acoustics beépülő modul automatikusan hozzáadja a Content/Acoustics könyvtárat a projekt csomagolt könyvtáraihoz.
* **Csempe mérete:** A figyelő körüli terület terjedelmei, amelyeket a RAM-ba akusztikai adatokbe szeretne betölteni. Mindaddig, amíg a figyelő szondák azonnal a lejátszó körül van betöltve, az eredmények ugyanazok, mint az akusztikai adatok betöltése az összes mintavételek. A nagyobb csempék több RAM-ot használnak, de csökkentik a lemez I/O-ját
* **Automatikus adatfolyam:** Ha engedélyezve van, automatikusan betöltődik az új csempékbe, ahogy a figyelő eléri a betöltött terület szélét. Ha le van tiltva, az új csempéket manuálisan kell betöltenie kód vagy tervrajzok segítségével
* **Gyorsítótár-méretezés:** az akusztikai lekérdezésekhez használt gyorsítótár méretét szabályozza. A kisebb gyorsítótár kevesebb RAM-ot használ, de növelheti a PROCESSZOR-használatot az egyes lekérdezéseknél.
* **Akusztika engedélyezve:** Hibakeresési vezérlő az Akusztika szimuláció gyors A/B-váltásának lehetővé tétele érdekében. Ezt a vezérlőt a rendszer figyelmen kívül hagyja a szállítási konfigurációkban. A vezérlő akkor hasznos, ha megállapíthatja, hogy egy adott hanghiba származik-e az akusztikai számításokból vagy a Wwise projekt más hibájából.
* **Távolságok frissítése:** Akkor használja ezt a beállítást, ha az előre megsült akusztikai információkat szeretné használni a távolságlekérdezésekhez. Ezek a lekérdezések hasonlóak a ray casts, de már előre kiszámított, így sokkal kevesebb CPU. Egy példa használat diszkrét reflexiók ki a legközelebbi felületre, hogy a figyelő. Ennek teljes kihasználásához kód vagy tervrajzok használatával kell lekérdezni a távolságokat.
* **Döntetlen statisztika:** Míg az UE-k `stat Acoustics` cpu-információkat tudnak nyújtani, ez az állapotkijelző a képernyő bal felső részén megjeleníti az aktuálisan betöltött ACE-fájlt, a RAM-használatot és az egyéb állapotinformációkat.
* **Voxels rajzolása:** A figyelő höz közeli fedvényvoxelek, amelyek a futásidejű interpoláció során használt voxel rácsot mutatják. Ha egy emitter egy futásidejű voxel belsejében van, sikertelen lesz a kusza lekérdezések.
* **Mintavételezés:** Mutasd meg az összes szondát ehhez a jelenethez. Ezek különböző színekben attól függően, hogy a terhelés állapotát.
* **Távolságok rajzolása:** Ha a Távolságok frissítése engedélyezve van, ez egy mezőt jelenít meg a figyelőhöz legközelebbi felületen a figyelő kvantált útvonalaiban.

## <a name="actor-specific-acoustics-design-controls"></a>Aktor-specifikus akusztikatervezési vezérlők
Ezek a tervezési vezérlők az Unreal egy adott audioösszetevőjére vannak kitéve.

![Képernyőkép az Unreal Audio Component Controls](media/audio-component-controls.png)

* **Okklúziós szorzó:** Szabályozza az elzáródási hatást. Az 1 > értékek felerősítik az elzáródást. Az 1 <értékek minimálisra csökkentik azt.
* **Nedvesség beállítása:** További zengetés dB
* **Bomlási idő szorzója:** Az RT60-at multiplikatív módon vezérli az akusztikai szimuláció kimenete alapján
* **Kültéri alkalmazkodás:** Szabályozza, hogy a szabadban a visszhang. A 0-hoz közelebbi értékek beltérben, az 1-hez közelebbiek a szabadban is. Ez a kiigazítás additív, így a -1 beállítás beltérben érvényes, a +1 beállítás pedig a szabadban.
* **Átviteli db:** Renderelj egy további falon keresztül hangot ezzel a hangossággal kombinálva a látótávolságon alapuló távolságcsillapítással.
* **Nedves áttételi távolság lánc:** Úgy állítja be a forrás visszhangjának jellemzőit, mintha közelebb/távolabb lenne, anélkül, hogy befolyásolná a közvetlen útvonalat.
* **Lejátszás a kezdőképernyőn:** Váltás: annak megadása, hogy a hang automatikusan lelegyen-e játszva a jelenet indításakor. Alapértelmezés szerint engedélyezett.
* **Akusztikus paraméterek megjelenítése:** A hibakeresési információk megjelenítése közvetlenül a játékban lévő összetevő tetején. (csak nem szállítási konfigurációk esetén)

## <a name="blueprint-functionality"></a>A tervezet funkciói
Az Acoustics Space szereplője a blueprint-en keresztül érhető el, amely olyan funkciókat biztosít, mint a térkép betöltése vagy a beállítások módosítása szintalapú parancsfájlok on keresztül. Itt két példát mutatunk be.

### <a name="add-finer-grained-control-over-streaming-load"></a>Finomabb szemcsés vezérlést adhat hozzá a streamelési terheléshez
Ha az akusztikai adatfolyam-továbbítást saját kezűleg szeretné kezelni, és nem szeretné automatikusan, a lejátszó pozíciója alapján, használhatja a Force Load Tile blueprint funkciót:

![Képernyőkép az Unreal Blueprint Streamelési beállításairól](media/blueprint-streaming.png)

* **Cél:** Az AcousticsSpace színésze
* **Középre helyezkedik el:** Az adatokat betöltődő régió középpontja
* **Mintavételek eltávolítása a csempén kívül:** Ha be van jelölve, az új régióban nem található összes mintavétel törlődik a RAM memóriából. Ha nincs bejelölve, az új terület betöltődik a memóriába, miközben a meglévő mintavételek is betöltődnek a memóriába
* **Befejezéskori blokkolás:** Szinkron műveletté teszi a csempe betöltését

A Korostra terhelési csempe hívása előtt a mozaikméretét már be kell állítani. Például ilyesmivel tölthet be egy ACE-fájlt, beállíthatja a csempeméretét, és streamelhet egy régióban:

![Képernyőkép az Unreal Streamelési beállításairól](media/streaming-setup.png)

Az ebben a példában használt Akusztikai adatok tervezetének függvénye a következő paraméterekkel rendelkezik:

* **Cél:** Az AcousticsSpace színésze.
* **Új Bake:** A betöltendő akusztikai adateszköz. Ha üresen hagyja/null értékre állítja, az új sütni enélkül üríti ki az aktuális sütést.

### <a name="optionally-query-for-surface-proximity"></a>Opcionálisan lekérdezheti a felület idomait
Ha meg szeretné tekinteni, hogy a figyelő körül milyen közeli felületek vannak egy adott irányban, használhatja a Lekérdezési távolság funkciót. Ez a funkció hasznos lehet az irányított késleltetett visszaverődések vezetéséhez, vagy más, a felületi közelség által vezérelt játéklogikához. A lekérdezés olcsóbb, mint a sugárküldés, mert az eredmények lekérése az akusztikai keresési táblából történik.

![Képernyőkép a Blueprint-távolság lekérdezéspéldáról](media/distance-query.png)

* **Cél:** Az AcousticsSpace színésze
* **Irány:** A lekérdezés iránya, a figyelő középre igazítása
* **Távolság:** Ha a lekérdezés sikeres, a legközelebbi felülethez való távolság
* **Visszatérési érték:** Logikai – igaz, ha a lekérdezés sikeres volt, egyébként hamis

## <a name="next-steps"></a>További lépések
* Fedezze fel a [tervezési folyamat](design-process.md) mögött imitojókat
* [Hozzon létre egy Azure-fiókot,](create-azure-account.md) hogy saját jelenetet süssön


