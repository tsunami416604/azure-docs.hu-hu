---
title: Project Akusztika Unreal Bake bemutató
titlesuffix: Azure Cognitive Services
description: Ez a dokumentum leírja az Unreal szerkesztő bővítmény használatával történő akusztikai sütés beküldésének folyamatát.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 7a868a5f9b06499e23710399733b0659d97f900d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854893"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Project Akusztika Unreal Bake bemutató
Ez a dokumentum leírja az Unreal szerkesztő bővítmény használatával történő akusztikai sütés beküldésének folyamatát.

Öt lépés, hogy csinál egy sütni:

1. A lejátszó navigációs hálójának létrehozása vagy címkézése
2. Címke akusztikai geometria
3. Akusztikai anyagok tulajdonságainak hozzárendelése a geometriához
4. A mintavétel elhelyezésének előnézete
5. Sütni

## <a name="open-the-project-acoustics-editor-mode"></a>A Project Acoustics szerkesztő üzemmód megnyitása

Importálja a Project Acoustics plugin csomagot a projektbe. Ezzel kapcsolatban az [Unreal Integration](unreal-integration.md) témakörben olvashat. A bővítmény integrálása után nyissa meg az Acoustics felhasználói felületet az új Akusztika mód ikonra kattintva.

![Képernyőkép az Unreal Editor Acoustics Mode beállításáról](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Tag szereplők akusztika

Az objektumok lap az első lap, amely az Akusztikai mód megnyitásakor jelenik meg. Ezen a lapon a szinten lévő szereplőket címkézheti, amelyek hozzáadja az **AcousticsGeometry** vagy **AcousticsNavigation** címkéket a szereplőkhöz.

Jelöljön ki egy vagy több objektumot a World Outliner területén, vagy a **Tömeges kijelölés** szakasz segítségével jelöljön ki egy adott kategóriába tartozó összes objektumot. Az objektumok kijelölése után a **Címkézés** szakasz segítségével alkalmazza a kívánt címkét a kijelölt objektumokra.

Ha valaminem rendelkezik **sem AcousticsGeometry,** sem **AcousticsNavigation** címkével, a szimuláció figyelmen kívül hagyja. Csak a statikus szem, a navigációs véletlenek és a tájképek támogatottak. Ha még valami mást címkéz, a rendszer figyelmen kívül hagyja.

### <a name="for-reference-the-objects-tab-parts"></a>Referenciaként: Az Objektumok lap részei

![Képernyőkép az Unreal Akusztikai objektumok lapjáról](media/unreal-objects-tab-details.png)

1. A tabulátorkijelölő gombok **(Kijelölt Objektumok** lap). Használja ezeket a gombokat, hogy séta a különböző lépéseket csinál egy akusztika sütni, fentről lefelé.
2. Rövid leírás arról, hogy mit kell tennie az oldal használatával.
3. Rendelkezésre álló választók szereplők a szinten.
4. A **Kijelölés** gombra kattintva kijelöli az összes olyan objektumot azon a szinten, amely megfelel legalább egy ellenőrzött aktatípusnak.
5. Az **Összes kijelölésének megszüntetése gombra** kattintva törli az aktuális kijelölést. Ez ugyanaz, mint üti a menekülési kulcsot.
6. Ezekkel a választógombokkal választhatja ki, hogy a Geometriát vagy a Navigációs címkét alkalmazza-e a kijelölt szereplőkre.
7. Ha a **Címke** gombra kattint, a kijelölt címkét az összes aktuálisan kiválasztott szereplőhöz hozzáadja.
8. A **Tagolás megszüntetése** gombra kattintva a kijelölt címke el lesz távolítható az összes jelenleg kijelölt szereplőből.
9. A **Címkézett kijelölése gombra** kattintva törlődik az aktuális kijelölés, és kijelöli az összes szereplőt az aktuálisan kijelölt címkével.
10. Ezek a statisztikák azt mutatják, hogy hány szereplő van címkézve az egyes címketípusokkal.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Tag akusztikai elzáródás és visszaverődés geometria

Nyissa meg az Akusztika ablak Objektumok lapját. Jelölje meg az objektumokat akusztikai geometriaként, ha elkell hagynia, tükröznie vagy el kell fogadnia a hangot. Akusztikai geometria tartalmazhat dolgokat, mint a föld, falak, tetők, ablakok & ablaküveg, szőnyegek, és a nagy bútorok. Ezekhez az objektumokhoz tetszőleges szintű összetettségi szintet használhat. Mivel a jelenet voxelized szimuláció előtt, rendkívül részletes csakad, mint a fák sok kis levelek, nem drágább sütni, mint az egyszerűsített tárgyakat.

Ne vegyen fel olyan dolgokat, amelyek nem befolyásolhatják az akusztikát, például a láthatatlan ütközési minta.

Egy objektum átalakítása a mintavételszámítás időpontjában (az alábbi Szondák lapon) rögzített a sütési eredményekben. A jelenetben megjelölt objektumok mozgatásához újra kell csinálni a szonda számítását, és újra kell sütni a jelenetet.

### <a name="create-or-tag-a-navigation-mesh"></a>Navigációs háló létrehozása vagy címkézése

A navigációs háló segítségével a szimuláció mintavételi pontjai helyezhető ek. Használhatja az Unreal [Nav Mesh Bounds kötetét,](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html)vagy megadhatja a saját navigációs hálóját. Legalább egy objektumot akusztikus navigációként kell **címkéznie.** Ha az Unreal navigációs hálóját használja, először győződjön meg arról, hogy megépítette.

### <a name="acoustics-volumes"></a>Akusztikai kötetek ###

Van további, speciális testreszabási tudod, hogy a navigációs területeken **akusztikai kötetek**. **Az akusztikai kötetek** olyan szereplők, amelyeket hozzáadhat a jelenethez, és amelyek lehetővé teszik a navigációs hálóból a felvehetővé és figyelmen kívül hagyandó területek kiválasztását. Az aktor egy tulajdonságot tár fel, amely átválthat az "Include" és a "Exclude" között. A "Include" kötetek biztosítják, hogy a program csak a navigációs háló ban lévő területeket veszi figyelembe, és a "Kizárás" kötetek megjelölik a figyelmen kívül hagyandó területeket. A "Kizárás" kötetek mindig a "Belefoglalás" kötetek után kerülnek alkalmazásra. Ügyeljen arra, hogy az **Akusztikai köteteket** **akusztikai navigációként** címkézze fel az Objektumok lap szokásos folyamatán keresztül. Ezek a szereplők ***nem*** automatikusan címkézve.

![Képernyőkép az Unreal Akusztikai kötet tulajdonságairól](media/unreal-acoustics-volume-properties.png)

A "Kizárás" kötetek főként arra vonatkoznak, hogy részletes encikba irányítsák, hogy hol ne helyezzenek szondákat az erőforrás-használat szigorításához.

![Képernyőkép az Unreal akusztikai kötetének kizárásáról](media/unreal-acoustics-volume-exclude.png)

A "Include" kötetek akkor hasznosak, ha egy jelenet manuális szakaszait szeretné létrehozni, például ha a jelenetet több akusztikai zónára szeretné bontani. Például, ha van egy nagy jelenet, sok kilométer négyzet, és van két érdekes területeket szeretne sütni akusztika. Két nagy "Include" kötetet rajzolhat a jelenetbe, és egyenként ace-fájlokat készíthet mindegyikhez. Ezután a játékban használhatja az eseményindító köteteket és a tervezethívásokat a megfelelő ACE fájl betöltéséhez, amikor a játékos megközelíti az egyes csempéket.

**AkusztikaI-kötetek** csak a navigációt korlátozzák, a geometriát ***nem.*** A "Include" **akusztikai köteten** belül lévő minden egyes szonda a hullámszimulációk végrehajtásakor továbbra is behúzza az összes szükséges geometriát a térfogaton kívül. Ezért nem lehet semmilyen megszakítása az elzáródásnak vagy más akusztikának, amely abból ered, hogy a játékos egyik szakaszból a másikba lép.

## <a name="select-acoustic-materials"></a>Válasszon akusztikus anyagokat

Miután az objektumok meg vannak címkézve, kattintson az **Anyagok** gombra az Anyagok lapra. Ez a lap adja meg a szint minden anyagának anyagtulajdonságait. Mielőtt bármelyik szereplő tanyán címkéző lenne, üres lesz.

Az akusztikai anyagok szabályozzák az egyes felületekről visszaverődő hangenergia mennyiségét. Az alapértelmezett akusztikai anyag felszívódását hasonló beton. A Project Akusztika a jelenet anyagának neve alapján javasol anyagokat.

Egy adott anyag visszhangzási ideje egy helyiségben fordítottan összefügg az abszorpciós együtthatójával, és a legtöbb anyag abszorpciós értéke a 0,01 és 0,20 közötti tartományban van. Az e tartomány feletti abszorpciós együtthatóval rendelkező anyagok nagyon nedvszívóak. Például, ha egy szoba hangzik túl visszhangzó, változtassa meg az akusztikus anyag a falak, padló, vagy a mennyezet valami nagyobb abszorpciós. Az akusztikai anyag-hozzárendelés minden olyan szereplőre vonatkozik, amely az adott jelenetanyagot használja.

![A reverberation idő és az abszorpciós együttható negatív korrelációját mutató grafikon](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Referenciaként: Az Anyagok lap részei

![Képernyőkép az Unreal Akusztikai objektumok lapjáról](media/unreal-materials-tab-details.png)

1. Az **Anyagok** lap gombja, amely az oldal felbeállítására szolgál.
2. Rövid leírás arról, hogy mit kell tennie az oldal használatával.
3. A szinten használt anyagok listája az **AcousticsGeometry**címkével ellátott szereplőktől származik. Ha egy anyagra kattintitt, akkor az összes olyan objektumot kijelöli a jelenetben, amely az adott anyagot használja.
4. Azt az akusztikai anyagot jeleníti meg, amelyhez a jelenet anyaga hozzá van rendelve. Kattintson egy legördülő menüre, ha a jelenetanyagot egy másik akusztikai anyaghoz szeretné hozzárendelni.
5. Az előző oszlopban kiválasztott anyag akusztikai abszorpciós együtthatóját mutatja. A nulla érték azt jelenti, tökéletesen fényvisszaverő (nincs felszívódás), míg az érték 1 azt jelenti, tökéletesen absorptive (nincs reflexió). Ha módosítja ezt az értéket, az Akusztikaanyag (#4 lépés) **egyénire frissül.**

Ha módosítja a jelenet anyagait, akkor a Project Akusztika beépülő modul ban lévő lapok at kell váltania ahhoz, hogy ezek a változások megjelenjenek az **Anyagok** lapon.

## <a name="calculate-and-review-listener-probe-locations"></a>Figyelőmintavételi helyek számítása és áttekintése

Az anyagok hozzárendelése után **váltson** a Mintavételek lapra.

### <a name="for-reference-parts-of-the-probes-tab"></a>Referenciaként: A Szondák lap részei

![Képernyőkép az Unreal Akusztikai szondák lapjáról](media/unreal-probes-tab-details.png)

1. A Lap felvetéséhez használt **Mintavételek** lap gomb
2. Rövid leírás arról, hogy mit kell tennie az oldal használatával
3. Ezzel durva vagy finom szimulációs felbontást választhat. Durva gyorsabb, de bizonyos kompromisszumokat. A részleteket lásd [a bake resolution](bake-resolution.md) alább.
4. Ebben a mezőben válassza ki azt a helyet, ahová az akusztikai adatfájlokat el kell helyezni. Kattintson a gombra a "..." a mappaválasztó használatához. Az adatfájlokról az alábbi [Adatfájlok](#Data-Files) című témakörben olvashat bővebben.
5. Az adott jelenet adatfájljai az itt megadott előtaggal lesznek elnevezve. Az alapértelmezett érték a "[Szintnév]_AcousticsData".
6. Kattintson a **Számítás gombra** a jelenet voxelize-jéhez és a mintavételi pont helyének kiszámításához. Ez történik helyben a gépen, és meg kell tenni, mielőtt csinál egy sütni. A szondák kiszámítása után a fenti vezérlők le lesznek tiltva, és ez a gomb megváltozik, hogy kimondjuk **a Törlés**. A **Törlés** gombra kattintva törölheti a számításokat, és engedélyezheti a vezérlőket, hogy az új beállításokkal újraszámíthassa a számítást.

A mintavételeket a **Mintavételek** lapon megadott automatizált folyamaton keresztül kell elhelyezni.


### <a name="what-the-calculate-button-calculates"></a>Mit számít a "Számítás" gomb?

A **Számítás** gomb az eddig megadott összes adatot (geometria, navigáció, anyagok és durva/finom beállítás) veszi át, és több lépésen megy keresztül:

1. A geometriát a jeleneti lávakból veszi, és kiszámítja a voxel térfogatát. A voxel kötet egy 3-dimenziós kötet, amely körülveszi az egész jelenetet, és alkotja a kis köbös "voxels". A voxelek méretét a szimuláció gyakorisága határozza meg, amelyet a **Szimuláció felbontása** beállítás határoz meg. Minden voxel van megjelölve, hogy vagy "szabad ban", vagy tartalmazó jelenet geometria. Ha egy voxel geometriát tartalmaz, akkor a voxel az adott geometriához rendelt anyag abszorpciós együtthatójával van címkézve.
2. Ezután a navigációs adatok atyját használja az akusztikailag érdekes helyek kiszámításához, ahová a játékos mehet. Megpróbálja megtalálni egy meglehetősen kis sor ilyen helyeken, amely magában foglalja a kisebb területeken, mint például ajtók és folyosók, majd a szobák, a nyílt terek. Kis jelenetek esetén ez általában kevesebb, mint 100 helyszín, míg a nagy jelenetek akár ezer is lehetnek.
3. Az egyes végső figyelő helyek kiszámítja, ez határozza meg a paraméterek száma, mint például, hogy "nyitott" a tér, a méret a szoba van, stb.
4. A számítások eredményeit a megadott helyen lévő fájlok ban tárolják (lásd az alábbi [adatfájlokat)](#Data-Files)

A jelenet méretétől és a gép sebességétől függően ezek a számítások több percet is igénybe vehetnek.

Miután ezek a számítások befejeződtek, megtekintheti mind a voxel adatokat, mind a mintavételi pont helyeket, hogy a sütés jó eredményeket adjon. Az olyan dolgok, mint a rossz navigációs háló vagy a hiányzó/extra geometria általában gyorsan láthatóvá válnak az előnézetben, így kijavíthatja azt.


## <a name="debug-display"></a>Hibakeresési megjelenítés

A szondaszámítás befejezése után egy új szereplő jelenik meg az **AcousticsDebugRenderer**nevű World Outliner-ben. A **Render Probes** és a **Render Voxels** jelölőnégyzetek bejelölésével a hibakeresési megjelenítés a szerkesztő nézetablakán belül jelenik meg.

![Képernyőkép az Acoustics Debug Renderer színészről az Unreal Szerkesztőben](media/acoustics-debug-renderer.png)

Ha nem lát voxeleket vagy szondákat a szinten, győződjön meg arról, hogy a valós idejű renderelés engedélyezve van a nézetablakban.

![Képernyőkép az Unreal valós idejű megjelenítési lehetőségéről](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels között

A voxelek zöld kockaként jelennek meg a jelenetablakban a részt vevő geometria körül. A csak levegőt tartalmazó Voxels nem jelenik meg. Van egy nagy zöld doboz körül az egész jelenetet, hogy jelöli a teljes voxel kötetet fog használni a szimuláció.
Mozogjon a jelenet, és ellenőrizze, hogy az akusztikailag elzárt geometria voxels. Ellenőrizze azt is, hogy a nem akusztikai objektumok, például az ütközési minta nem voxelizálta-e. A jelenet kamerájának körülbelül 5 méteren belül kell lennie az objektumtól, hogy a voxels megjelenjen.

Ha összehasonlítjuk a voxels létre durva felbontás vs finom felbontás, látni fogja, hogy a durva voxels kétszer akkora.

![Képernyőkép az Acoustics voxels előnézetéről az Unreal szerkesztőjében](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Mintavételi pontok

A mintavételi pontok egyet jelentenek a lehetséges játékos (figyelő) helyekkel. Sütéskor a szimuláció kiszámítja az összes lehetséges forráshelyet az egyes mintavételi pontokhoz kötő akusztikát. Futásidőben a figyelő helye interpolálva lesz a közeli mintavételi pontok között.

Fontos ellenőrizni, hogy a szonda pontok léteznek bárhol a játékos várhatóan utazni a jelenetet. A mintavételi pontokat a Project Acoustics motorja helyezi a navigációs hálóra, és nem helyezhető át és nem szerkeszthető, ezért a mintavételi pontok vizsgálatával biztosítsa, hogy a navigációs háló lefedje az összes lehetséges játékoshelyet.

![Képernyőkép az Unreal akusztikai szondáinak előnézetéről](media/unreal-probes-preview.png)

Lásd [Bake állásfoglalás](bake-resolution.md) további részleteket durva vs finom felbontás.

## <a name="bake-your-level-using-azure-batch"></a>Süsse a szintjét az Azure Batch használatával

Az Azure Batch szolgáltatás használatával a felhőben lévő számítási fürtsegítségével sütheti a jelenetet. A Project Acoustics Unreal beépülő modul közvetlenül csatlakozik az Azure Batch-hez, hogy minden egyes sütéshez egy Azure Batch-fürtöt azonnal létrehozza, kezelje és bontsa le. A Sütés lapon adja meg az Azure-hitelesítő adatait, válassza ki a fürtgép típusát és méretét, majd kattintson a Sütés gombra.

### <a name="for-reference-parts-of-the-bake-tab"></a>Referenciaként: A sütés lap részei

![Képernyőkép az Unreal Akusztikabake lapjáról](media/unreal-bake-tab-details.png)

1. A Bake Tab gomb, hogy ki ezt az oldalt.
2. Rövid leírás arról, hogy mi a teendő ezen az oldalon.
3. Az Azure-fiók létrehozása után adja meg az Azure-hitelesítő adatokat. További információ: [Azure Batch-fiók létrehozása.](create-azure-account.md)
4. Indítsa el az Azure Portalt az előfizetések kezeléséhez, a használat figyeléséhez és a számlázási adatok megtekintéséhez stb. 
5. Az Azure batch számítási csomópont típusát a számításhoz használni. A csomópont típusát az Azure adatközpont helyének támogatnia kell. Ha nem biztos benne, hagyja **a Standard_F8s_v2**.
6. A számításhoz használandó csomópontok száma. Az itt megadott szám befolyásolja a sütés befejezéséhez szükséges időt, és az Azure Batch alapvető allokációja korlátozza. Az alapértelmezett foglalás csak két 8 magos vagy egy 16 magos csomópontot engedélyez, de kibontható. Az alapvető foglalási korlátokról az [Azure Batch-fiók létrehozása](create-azure-account.md)című témakörben talál további információt.
7. Jelölje be ezt a jelölőnégyzetet, ha a számítási készletet [alacsony prioritású csomópontok használatára](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)szeretné konfigurálni. Az alacsony prioritású számítási csomópontok sokkal alacsonyabb költséggel rendelkeznek, de előfordulhat, hogy nem mindig érhetők el, vagy bármikor előzhetők.
8. Az eltelt idő mennyisége, amíg a feladat a felhőben fut. Ez nem foglalja magában a csomópontok indítási idejét. Miután a feladat elindul, ez arról szól, hogy mennyi ideig kell tartania, mielőtt visszakapja az eredményeket. Ne feledje, hogy ez csak egy becslés.
9. A szimulációk futtatásához szükséges számítási idő teljes mennyisége. Ez az Azure-ban használt csomópontszámítási idő teljes mennyisége. Az érték használatával kapcsolatos további információkért lásd az alábbi [Sütésköltség becslése](#Estimating-bake-cost) című témakört.
10. Kattintson a Bake gombra, hogy küldje el a sütni a felhőbe. Amíg egy feladat fut, ez a **Feladat megszakítása** látható. Ha ezen a lapon hibák vannak, vagy ha a **Mintavételek** lapon lévő munkafolyamat még nem fejeződött be, ez a gomb le lesz tiltva.
11. A mintavétel a Mintavétel ek lapon számított nak számítja ki a **jelenetet.** A mintavételek száma határozza meg a felhőben futtatandó szimulációk számát. Nem adhat meg több csomópontot, mint amennyi mintavétel van.
12. Ez az üzenet a feladat aktuális állapotát jelzi, vagy ha ezen a lapon hibák vannak, akkor mik ezek a hibák.

Az aktív feladatokról, a számítási készletekről és a tárolásról mindig teljes körű információkat kaphat az [Azure Portalon.](https://portal.azure.com)

Amíg a feladat fut a **Bake** gomb **módosításai A feladat törlése gombra**. Ezzel a gombbal megszakíthatja a folyamatban lévő feladatot. A feladat megszakítása nem vonható vissza, nem lesz elérhető eredmény, és a lemondás előtt felhasznált Azure-számítási idő után továbbra is díjat számítunk fel.

Ha már elkezdte a sütni, akkor zárja unreal. A projekttől, a csomópont típusától és a csomópontok számától függően a felhősütés több órát is igénybe vehet. A sütési feladat állapota a projekt újratöltésekor és az Akusztika ablak megnyitásakor frissül. Ha a feladat befejeződött, a kimeneti fájl letöltődik.

Az Azure-hitelesítő adatok biztonságosan tárolódnak a helyi számítógépen, és az Unreal projekthez vannak társítva. Kizárólag az Azure-ral való biztonságos kapcsolat létrehozására szolgálnak.

### <a name="estimating-azure-bake-cost"></a><a name="Estimating-bake-cost"></a>Az Azure sütési költségének becslése

Ha meg szeretné becsülni, hogy egy adott sütni mennyibe fog kerülni, vegye ki a **becsült számítási költség**értékét , amely egy időtartam, és szorozza meg a kiválasztott **virtuálisgép-csomóponttípus** helyi pénznemében megadott óránkénti költséggel. Az eredmény nem tartalmazza a csomópontok működéséhez szükséges csomópontidőt. Ha például **Standard_F8s_v2** választ a csomóponttípushoz, amelynek költsége 0,40 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 0000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 0000 000 000 000 0000 0000 000 000 0000 0000 A tényleges költség valószínűleg egy kicsit magasabb, mivel az extra időt, hogy a csomópontok kezdődött. Az óránkénti csomópontköltséget az [Azure Batch-díjszabási](https://azure.microsoft.com/pricing/details/virtual-machines/linux) lapon találja (válassza a "Számítási optimalizált" vagy a "Nagy teljesítményű számítás" lehetőséget a kategóriához).

### <a name="reviewing-the-bake-results"></a>A sütési eredmények áttekintése

Miután a sütni befejeződött, ellenőrizze, hogy a voxels és szonda pontok a várt helyeken futtatásával a runtime plugin.

## <a name="data-files"></a><a name="Data-Files"></a>Adatfájlok

Négy adatfájlok által létrehozott plugin különböző pontokon. Futásidőben csak az egyikre van szükség, és a projekt Tartalom/akusztika mappájába kerül, amely automatikusan hozzáadódik a projekt csomagolási útvonalához. A másik három az Acoustics Data mappában van, és nincsenek becsomagolva.

* **[Project]/Config/ProjectAcoustics.cfg**: Ez a fájl az Akusztikai mód felhasználói felületének mezőiben megadott adatokat tárolja. A fájl helye és neve nem módosítható. Vannak más értékek tárolt ebben a fájlban, amelyek befolyásolják a sütni, de ezek a haladó felhasználók számára, és nem kell módosítani.
* **[Project]/Content/Acoustics/[LevelName]\_AcousticsData.ace**: Ez a fájl az, ami a sütés-szimuláció során jön létre, és a futásidejű által a jelenet akusztikájának megjelenítéséhez használt keresőket tartalmazza. A fájl helye és neve a Mintavételek lap **mezőivel** módosítható. Ha a fájl létrehozását követően szeretné átnevezni ezt a fájlt, törölje az UAsset eszközt az Unreal projektből, nevezze át a fájlt az Unreal alkalmazáson kívülre a Fájlkezelőben, majd importálja újra ezt a fájlt az Unreal programba, hogy új UAsset-t hozlétre. A UAsset átnevezése önmagában nem fog működni.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData.vox**: Ez a fájl tárolja a voxelizált akusztika geometriáját és az anyag tulajdonságait. A számítás a **Mintavételek** lap **Számítás** gombjával történt. A fájl helye és neve a Mintavételek lap **mezőivel** módosítható.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config.xml**: Ez a fájl a **Mintavétel ek** lap **Számítás** gombjával számított paramétereket tárolja. A fájl helye és neve a Mintavételek lap **mezőivel** módosítható.

Ügyeljen arra, hogy ne törölje az Azure-ból letöltött *.ace fájlt. Ez a fájl nem állítható helyre, kivéve a jelenet újrasütésével.

## <a name="next-steps"></a>További lépések
* Fedezze fel az [Unreal tervezési vezérlőit](unreal-workflow.md)
* Fedezze fel a [Project Akusztika tervezési koncepcióit](design-process.md)

