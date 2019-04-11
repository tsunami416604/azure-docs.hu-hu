---
title: Projekt Akusztika Unreal Bake oktatóanyag
titlesuffix: Azure Cognitive Services
description: Ez a dokumentum ismerteti az Unreal szerkesztő bővítményének használatával Akusztika bake be.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: michem
ms.openlocfilehash: 48a1c4350b438761aa2e2d8c7e57a872c86ca292
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470372"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Projekt Akusztika Unreal Bake oktatóanyag
Ez a dokumentum ismerteti az Unreal szerkesztő bővítményének használatával Akusztika bake be.

Ennek során egy bake öt lépésből áll:

1. Hozzon létre vagy a lejátszó navigációs háló címkézése
2. Címke Akusztika geometriai
3. Akusztikai anyagok tulajdonságok hozzárendelésére geometriai
4. Mintavétel elhelyezési előzetes verzió
5. Bake

## <a name="open-the-project-acoustics-editor-mode"></a>A projekt Akusztika szerkesztő mód megnyitásához

Importálja a projekt Akusztika beépülő modul csomagot a projekthez. A témakörben talál segítséget a [Unreal integrációs](unreal-integration.md) témakör. A beépülő modul-integrálása után az új Akusztika mód ikonra kattintva nyissa meg a Akusztika felhasználói felület.

![Képernyőkép az Unreal szerkesztő Akusztika mód beállítása](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Címke Akusztika actors –

Az objektumok lap jelenik meg az első lapra, amely a Akusztika mód megnyitásakor megjelenik. Ezen a lapon, a címke actors használni a szintje, amely hozzáadja a **AcousticsGeometry** vagy **AcousticsNavigation** az actors-címkéket.

Jelöljön ki egy vagy több objektumot a világ Outliner, vagy használja a **több, tömegesen kijelölt** szakasz segítségével kiválaszthatja egy adott kategória összes objektumához. A kijelölt objektumok használata a **címkézést** című szakaszt a alkalmazni a kívánt címke a kijelölt objektumok.

Ha valami nincs se **AcousticsGeometry** sem **AcousticsNavigation** kódot, figyelmen kívül a szimuláció. Csak a statikus rácsvonalak nav rácsvonalak, és környezetünk támogatottak. Ha bármi más, akkor figyelmen kívül.

### <a name="for-reference-the-objects-tab-parts"></a>A dokumentáció: Az objektumok lapon részei

![Unreal lapján Akusztika objektumok képernyőképe](media/unreal-objects-tab-details.png)

1. A lap kiválasztása gombok (**objektumok** lapja van kiválasztva). A gombok használatával különféle lépéseiről állapotát egy Akusztika bake felülről lefelé.
2. Mit kell tennie, használatával ezen a lapon rövid leírása.
3. Aktorok a szint a rendelkezésre álló választók.
4. Kattintson a **kiválasztása** összes objektum választ ki, amelyek megfelelnek a ellenőrzött aktorok közül legalább egy szinten található.
5. Kattintson a **zes** törölni fogja a jelenleg kijelölt. Ez a ugyanaz, mint az ESC billentyű lenyomásával.
6. Ezek a választógombok segítségével döntse el, a Geometry és navigációs címke alkalmazásához a kiválasztott actors.
7. Kattintson a **címke** ad hozzá a kijelölt címke az összes kijelölt actors.
8. Kattintson a **Címketörlés** eltávolítja a kijelölt címke az összes kijelölt szereplő.
9. Kattintson a **válassza ki a címkézett** törölje a kijelölést, és válassza ki a jelenleg kijelölt címkével ellátott összes szereplő.
10. Statisztikánkat bemutatják, hogy hány actors minden címke típusú címkével vannak ellátva.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Címke Akusztika hangelnyelés és tükröződés geometriai

Nyissa meg az objektumok fülre az Akusztika ablak. Objektumok megjelölése Akusztika geometriai kell occlude, tükrözik, vagy számára a hangot. Akusztika geometriai többek között az alapoktól, falak, tető, a windows & ablak üvegből, takaró és nagy bútordarab tartalmazhatnak. Ezek az objektumok bármilyen tetszőleges az összetettségi szintet is használhat. Mivel a jelenet voxelized előtt szimuláció, nagyon részletes rácsvonalak, például a sok kis leveleket, olyan fák, amelyeknél nem költségesebb, mint egyszerűsített objektumok os.

Nem lehet hatással a Akusztika, például láthatatlan ütközési rácsvonalak, amit nem tartalmaznak.

A mintavétel számítás időben egy objektum átalakító (mintavételek lapján alább) a bake eredmények rögzített. Áthelyezi a kijelölt objektumok valamelyikének a jelenetben megismétlése a mintavétel számítási és a jelenet rebaking lesz szükség.

### <a name="create-or-tag-a-navigation-mesh"></a>Hozzon létre vagy egy navigációs háló címkézése

Navigációs rácsvonal helyezze el a mintavétel pontok szimuláció szolgál. Használhatja az Unreal [Nav háló Rozsah kötet](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html), vagy megadhatja a saját navigációs háló. Legalább egy objektumot használ fel kell címkéznie **Akusztika navigációs**. Ha Unreal a navigációs háló használ, győződjön meg arról, először felépítette.

### <a name="acoustics-volumes"></a>Akusztika kötetek ###

A navigációs területeket teheti további, a speciális testreszabási van **Akusztika kötetek**. **Akusztika kötetek** actors a jelenethez adhat hozzá, amelyek lehetővé teszik, hogy válassza ki a területek tartalmazzák, és a navigációs háló figyelmen kívül vannak. Az aktor tesz elérhetővé, amely bekapcsolható tulajdonság "Include" és "Kihagyja" között. "Include" kötetek győződjön meg arról, a bennük lévő navigációs háló csak területeit minősülnek, és "Kizárása" kötetek megjelölése figyelmen kívül lesz hagyva a fenti területekre. "Kizárása" kötetek mindig "Include" kötetek követően lesznek alkalmazva. Ellenőrizze, hogy a címke **Akusztika kötetek** , **Akusztika navigációs** objektumok lapján a szokásos folyamatát. Ezek az aktorok vannak ***nem*** automatikusan címkézett.

![Az Unreal Akusztika kötet képernyőkép tulajdonságai](media/unreal-acoustics-volume-properties.png)

"Kizárása" kötetek főként van kialakítva, hogy hová mintavételezőket szigorítása erőforrás-használat nem érdemes a minden részletre kiterjedő felügyeleti lehetőséget biztosítanak.

![Képernyőkép az Unreal Akusztika kötethez kizárása](media/unreal-acoustics-volume-exclude.png)

Kötetek "Include" hasznosak létrehozása manuális szakaszok jelenet, például hogy szeretné-e a jelenethez több akusztikai zónában történő részekre. Például ha egy nagy jelenetet, számos kilométerben négyzet, és két típus szerinti Akusztika os a kívánt rendelkezik. Két nagy "Include" kötetek rajzolása a jelenetben, és mindegyikhez ACE fájlok előállításához egyenként. Majd játékban köteteket is használhat az eseményindító tervezet hívások kombinálva betölteni a megfelelő hozzáférés-vezérlő bejegyzések fájlt, amikor a Windows Media player megközelíti a csempéket.

**Akusztika kötetek** csak korlátozhatja a navigációs és ***nem*** a geometriai. Egyes Hálózatfigyelő belül "Include" **Akusztika kötet** továbbra is kéri az összes szükséges geometriai kívül a kötet wave szimulációk végrehajtásakor. Ezért nem lehetnek bármely folytonosság hangelnyelés vagy más, a Windows Media player egy másikra egy szakaszban metsző eredő Akusztika megszakítását.

## <a name="select-acoustic-materials"></a>Válassza ki a akusztikai anyagok

Ha az objektumok címkével vannak ellátva, kattintson a **anyagok** gombra kattintva nyissa meg a anyagok lapot. Ezen a lapon szinten található minden egyes anyag anyagok tulajdonságainak megadásához használható. Mielőtt bármilyen actors címkével vannak ellátva, üres lesz.

Az akusztikai anyagok ellenőrzés kerülnek vissza minden egyes felületből eredményes energia mennyiségét. Az alapértelmezett akusztikai anyag tényleges hasonló elnyelő rendelkezik. Projekt Akusztika anyagok jelenet anyagi neve alapján javasol.

Egy adott anyag szoba reverberation idején intenzitásfokozatok kapcsolódik, a elnyelő együttható a legtöbb anyagok elnyelő értékeket kellene 0,01 való 0,20 tartományban. A fenti ezt a tartományt elnyelő együttható anyagok nagyon elnyelő. Például ha egy hely hangok túl reverberant, módosítsa, emelet, vagy akusztikai anyaga valami újabb absorptivity. Az akusztikai anyagi hozzárendelés, hogy a jelenet anyag használó összes szereplő vonatkozik.

![Elnyelő együttható reverberation időben negatív összekapcsolását ábrázoló grafikon](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>A dokumentáció: Az anyagok lapon részei

![Unreal lapján Akusztika objektumok képernyőképe](media/unreal-materials-tab-details.png)

1. A **anyagok** lap gomb segítségével ezt a lapot.
2. Mit kell tennie, használatával ezen a lapon rövid leírása.
3. A lista a szintet, a szereplők származó anyagok címkével **AcousticsGeometry**. Olyan anyagot találhat az Ide kattintva lesz, válassza a minden objektumot használó anyagot találhat az, hogy a jelenet.
4. Az akusztikai anyag jeleníti meg, hogy a jelenet anyag van rendelve. Kattintson a legördülő lista egy másik akusztikai anyagok jelenet anyag szeretné hozzárendelni.
5. A kiválasztott az előző oszlop anyag akusztikai elnyelő együttható mutatja. Egy érték nulla azt jelenti tökéletesen reflektív (nincs elnyelő) során egy érték 1, tökéletesen nedvszívó (nincs tükröződés). Ez az érték módosítását frissíteni fogja az Akusztika anyag (#4. lépés) az **egyéni**.

Ha módosítja az anyagokat a jelenet, szüksége lesz a projekt Akusztika beépülő modult, hogy ezek a módosítások megjelennek a Váltás a lapok a **anyagok** fülre.

## <a name="calculate-and-review-listener-probe-locations"></a>Kiszámíthatja, és tekintse át a figyelő mintavételek helyeit

Miután hozzárendelte a anyagok, váltson át a **mintavételek** fülre.

### <a name="for-reference-parts-of-the-probes-tab"></a>A dokumentáció: A mintavételek lapon részei

![Képernyőkép a Akusztika mintavételek lapján Unreal](media/unreal-probes-tab-details.png)

1. A **mintavételek** ezt oldal megjelenítéséhez használt lap gomb
2. Mit kell tennie, használatával ezen a lapon rövid leírása
3. Ezzel egy durva vagy finom szimuláció megoldás kiválasztásához. Elnagyolt gyorsabb, de bizonyos kompromisszumot kínál a rendelkezik. Lásd: [os feloldási](bake-resolution.md) alábbi részleteket.
4. Válassza ki a helyet, ahol a Akusztika adatfájlok kell elhelyezni, hogy ez a mező használatával. A gombra kattintva a "...", a Mappalista használatára. Adatfájlok kapcsolatos további információkért lásd: [adatfájlok](#Data-Files) alatt.
5. Az adatfájlokat a helyszín neve lesz a megadott előtag ide használatával. Az alapértelmezett érték "_AcousticsData [szint neve]".
6. Kattintson a **Calculate** gombra kattintva voxelize a helyszín és a mintavételi pontok helykérelmei kiszámításához. Ez a gépén helyileg történik, és a egy bake végrehajtása előtt kell elvégezni. Miután a mintavételezők kiszámított, a fenti vezérlőelemek le lesz tiltva, és tegyük fel, hogy ez a gomb változik **egyértelmű**. Kattintson a **egyértelmű** a számítások törléséhez, és a vezérlők engedélyezése, hogy az új beállításokkal újraszámítása gombra.

Mintavételezők kell helyezni a megadott automatikus folyamaton keresztül a **mintavételek** fülre.


### <a name="what-the-calculate-button-calculates"></a>A "Számítás" gomb kiszámítja

A **Calculate** gomb, amennyiben megadott összes adatot vesz igénybe (geometry, navigációs, anyagokat és a durva/részletes beállítás), és több lépésre végighalad:

1. A geometriai telik el a jelenet rácsvonalak és a egy voxel kötet számítja ki. A voxel kötet olyan 3 dimenziós kötet, amely a teljes jelenet helyezi, és kis m3 "voxels" áll. A szimuláció gyakorisága, amely úgy van beállítva a voxels mérete határozza meg a **szimuláció feloldási** beállítás. Minden egyes voxel meg van jelölve, vagy hogy "nyissa meg a vezeték nélkül", vagy jelenet geometriai tartalmazó. Ha egy voxel tartalmazza a geometriai a voxel címkéjű van az adott geometriai rendelt anyag elnyelő együtthatója.
2. Akusztikailag érdekes helyeket, ahol előfordulhat, hogy nyissa meg a Windows Media player kiszámításához navigációs adatokat ezután használja. Ez megpróbálja megkeresni egy viszonylag kis készletét ezek a helyek, amely tartalmazza a kisebb területeket, például az átjáróknak és hallways, majd a termek tárolóhelyek megnyitásához. Kisebb jeleneteket a Ez általában a 100-nál kevesebb helyen, miközben nagy jelenetek előfordulhat, hogy akár egy ezer.
3. Az utolsó figyelő helyekről, kiszámítja az egyes meghatározza, hogy a paraméterek számát, például hogyan "nyitott" az a terület méretét a hely van, stb.
4. Ezek a számítások eredményét a megadott helyen található fájlok tárolják (lásd: [adatfájlok](#Data-Files) alább)

Ezeket a számításokat a jelenethez méretétől és sebességétől, a gép, függően több percig is eltarthat.

Ha ezeket a számításokat befejeződött, megtekintheti a voxel adatai és a mintavételi pontok helykérelmei annak biztosítására, hogy a bake kap jó eredmények egyaránt. Többek között egy rossz navigációs háló vagy hiányzó/nagyon geometriai általában gyorsan látható lesz az előzetes verzióban érhető el, kijavíthatja.


## <a name="debug-display"></a>Megjelenítési hibakeresése

Mintavétel számítási befejezése után egy új aktor fog megjelenni a világ Outliner nevű **AcousticsDebugRenderer**. Ellenőrzése a **Render mintavételek** és **Render Voxels** jelölőnégyzetek lehetővé teszi a szerkesztő nézőpontban hibakeresési megjelenítését.

![Ábrázoló képernyőfelvétel Akusztika Debug megjelenítő aktor Unreal-szerkesztő](media/acoustics-debug-renderer.png)

Bármely voxels vagy mintavételek átfedésben a szinten nem jelenik meg, hogy valós idejű megjelenítési beállítás van engedélyezve a nézőpont.

![Valós idejű megjelenítési beállítás, az Unreal képernyőképe](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels

Voxels jelennek meg a jelenet ablakban zöld kockák részt vevő geometriai körül. Csak a légi tartalmazó Voxels nem jelennek meg. A teljes jelenet, amely azt jelzi, hogy a teljes voxel kötet, amely felhasználja a szimuláció nagy zöld Keretes van.
Navigálás a jelenethez, és ellenőrizze, hogy a akusztikailag occluding geometriai voxels rendelkezik-e. Emellett ellenőrizze, hogy nem Akusztika objektumok, például ütközési rácsvonalak voxelized még nem lett. A helyszín kamera nem lehet a voxels megjelenítéséhez az objektumának körülbelül 5 mérőszámok belül.

Ha összehasonlítja a voxels durva feloldási vs finom megoldás használatával létrehozott, látni fogja, hogy az a durva voxels kétszer akkora.

![Képernyőkép a Akusztika voxels előzetes verziója, az Unreal szerkesztő](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Mintavétel pontok

Mintavétel pontok azonos a lehetséges player (figyelő) helyen. Amikor sütés, a szimuláció az összes lehetséges forrása hely csatlakozik minden mintavétel pont Akusztika számítja ki. Futásidőben a figyelő helyre van interpolált közeli mintavételi pontok közötti.

Fontos ellenőrizni, hogy a mintavétel pontok létezik, bárhol a Windows Media player várható utazási a jelenetben. Mintavétel pontok a projekt Akusztika motor navigációs háló kerülnek, és nem lehet áthelyezni vagy szerkeszthetők, ezért győződjön meg arról a navigációs háló tartalmazza az összes lehetséges player hely vizsgálatával szerezheti be a mintavétel pontokat.

![Képernyőkép a Akusztika mintavételek Unreal az előzetes verzió](media/unreal-probes-preview.png)

Lásd: [os feloldási](bake-resolution.md) durva vs további részleteiért finom megoldás.

## <a name="bake-your-level-using-azure-batch"></a>Az Azure Batch használatával szintjét os

A számítási fürt a felhőben az Azure Batch szolgáltatás használatával jelenetet is os. A projekt Akusztika Unreal beépülő modul közvetlenül csatlakozik az Azure Batch számára hozza létre, kezelhet és egy Azure Batch-fürtöt, az egyes bake üzemen. Bake lapon adja meg Azure hitelesítő adatait, és válassza ki a fürt gép típusát és méretét, Bake kattintson.

### <a name="for-reference-parts-of-the-bake-tab"></a>A dokumentáció: A bake lapon részei

![Képernyőkép a Akusztika os lapján Unreal](media/unreal-bake-tab-details.png)

1. Ezt a lapot szolgáló lap os gomb.
2. Mi a teendő ezen az oldalon rövid leírása.
3. Adja meg Azure hitelesítő adatait, ha az Azure-fiókja létrejött mezőket. További információkért lásd: [Azure Batch-fiók létrehozása](create-azure-account.md).
4. Indítsa el az Azure Portalon kezelheti az előfizetéseit, figyelheti és tekinteni a számlázási adatokat stb. 
5. Az Azure batch számítási csomópont típusa kiszámításához használandó. A csomóponttípus támogatnia kell az Azure az Adatközpont-helyen. Ha nem biztos maradjon a **Standard_F8s_v2**.
6. Ehhez a számításhoz használt csomópontok száma. Az itt hatással van a bake elvégzéséhez szükséges idő, és az Azure Batch alapvető foglalási korlátozza. Az alapértelmezett lefoglalási csak lehetővé teszi, hogy két 8 mag csomópontok vagy egy 16 mag csomópont, de bővíthetők. A core foglalási megkötések további információkért lásd: [Azure Batch-fiók létrehozása](create-azure-account.md).
7. A jelölőnégyzet bejelölésével konfigurálhatja a számítási készletet használandó [alacsony prioritású csomópontok](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Alacsony prioritású számítási csomópontok kell sokkal alacsonyabb költségek, de előfordulhat, hogy nem érhető el, vagy a tetszőleges időpontban háttérbe szorulhatnak.
8. Valószínű, hogy a feladatnak a felhőben való futtatásra tennie eltelt idő mennyisége. Ez nem vonatkozik a csomópont indítási idejének. Miután a feladat futni kezd, ez a arról, hogy mennyi ideig előtt kapja vissza az eredményeket kell lennie. Vegye figyelembe, hogy ez csak egy becsült.
9. A teljes, a szimulációk futtatásához szükséges számítási idő mennyisége. Ez a csomópont számítási idő, az Azure-ban használt teljes mennyisége. Lásd: [Estimating bake költség](#Estimating-bake-cost) alatt további információt az ezzel az értékkel.
10. A Bake gombra kattintva küldje el a bake a felhőbe. A feladat futása közben látható **feladat megszakítása** helyette. Ha ezen a lapon minden olyan hibák, vagy ha a munkafolyamatot az a **mintavételek** lapon nem fejeződött be, ez a gomb le lesz tiltva.
11. A helyszín, a kiszámított mintavételi számát a **mintavételek** fülre. A mintavételek számának meghatározásához szimulációk esetében, amelyeket a felhőben futtatni kell a száma határozza meg. Mintavételezők számánál több csomópont nem adható meg.
12. Ez az üzenet közli, hogy a feladat aktuális állapotát, vagy ha ezen a lapon találhatók esetleges hibákat, mi ezeket a hibákat.

Teljes körű információt arról, hogy aktív feladatok, a számítási készletek és a tárolás mindig megtekintheti a [az Azure portal](https://portal.azure.com).

A feladat futása közben a **os** gomb felirata **feladat megszakítása**. Ez a gomb használatával szakítsa meg a folyamatban lévő feladatot. A feladat megszakítása nem lehet visszavonni, eredménytelen lesz elérhető, és továbbra is kell díjat fizetnie törlés előtt használt Azure-beli számítási bármikor.

Egy bake megkezdése után bezárhatja az Unreal. Attól függően a projektet, a csomópont típusa és a csomópontok számát a felhő bake több óráig is tarthat. Töltse be újra a projektet, és nyissa meg a Akusztika ablakot a bake feladat állapota frissülni fog. Ha a feladat befejeződött, a rendszer letölti a kimeneti fájlt.

Az Azure-beli hitelesítő biztonságosan tárolja a helyi gépen, és az Unreal projekthez tartozó. Ezek szolgálnak, kizárólag az Azure-bA biztonságos kapcsolatot létesíteni.

### <a name="Estimating-bake-cost"></a> Az Azure bake költségek becslése

Megbecsülheti egy adott bake díjait, hajtsa végre a megjelenített érték **becsült költség számítási**, ez az időtartam és a kettő többszörösének, amely szerint az óránkénti költség, a helyi pénznemben számított a **VM csomóponttípus** választotta. Az eredmény nem tartalmazza a csomópont idő szükséges a csomópontok szolgáltatásszintje és futtatásához. Például, ha kiválasztja **Standard_F8s_v2** az adott csomóponttípus költsége pedig $ 0,40/óra, amely rendelkezik, és a számítási becsült költség 3 óra és 57 perc, a feladat futtatásához a becsült költség lesz $0,40 * ~ 4 óra = ~ $1.60. A tényleges költségek valószínűleg egy kicsit nagyobb miatt az első lépések a csomópontok hosszabb időt. Az óránkénti csomópont költsége annak a [Azure Batch szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (válassza "számításra optimalizált" vagy "nagy teljesítményű számítás" kategória) lap.

### <a name="reviewing-the-bake-results"></a>A bake találatok áttekintése

A bake befejezése után ellenőrizze, hogy a mintavétel, valamint voxels pontok a várt helyen a beépülő modul futásidejű futtatásával.

## <a name="Data-Files"></a>Adatfájlok

Nincsenek különböző időpontokban a beépülő modul által létrehozott négy adatfájlokat. Csak az egyik futásidőben van szükség, és a fájlfejlécben található a projekt tartalom/Akusztika mappába, amely automatikusan hozzáadódik a projekt csomagolási elérési útját. A másik három a Akusztika adatok mappában találhatók, és nem vannak csomagolva.

* **[Project]/Config/ProjectAcoustics.cfg**: Ez a fájl tárolja az adatokat, a felhasználói felületen Akusztika mód mezőkben. A hely és a fájl neve nem lehet módosítani. Ebben a fájlban tárolt más értékek, amelyek a bake hatással, de tapasztalt felhasználók számára, és nem szabad módosítani.
* **[Project]/Content/Acoustics/[LevelName]\_AcousticsData.ace**: Ez a fájl, mi a bake szimuláció során létrejön, és a helyszín a Akusztika megjelenítése a modul által használt keresési adatait tartalmazza. A helyét, és ez a fájl nevét is módosítható a mezői a **mintavételek** fülre.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData.vox**: Ez a fájl tartalmazza a voxelized Akusztika geometry és a lényeges tulajdonságait. Kiszámítása a **Calculate** gombot a **mintavételek** fülre. A helyét, és ez a fájl nevét is módosítható a mezői a **mintavételek** fülre.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config.xml**: Ez a fájl tartalmazza a paraméterek kiszámítása a **Calculate** gombot a **mintavételek** fülre. A helyét, és ez a fájl nevét is módosítható a mezői a **mintavételek** fülre.

Ügyeljen, nem arra, hogy az Azure-ból letöltött *.ace fájl törlése. Ez a fájl nem helyreállítható, kivéve a jelenet rebaking által.

## <a name="next-steps"></a>További lépések
* Fedezze fel a [Unreal vezérlők tervezése](unreal-workflow.md)
* Fedezze fel a [projekt Akusztika tervezési alapelvek](design-process.md)

