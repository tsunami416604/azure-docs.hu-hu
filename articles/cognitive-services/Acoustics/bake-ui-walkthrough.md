---
title: A projekt Akusztika bake Akusztika
titlesuffix: Azure Cognitive Services
description: Ez a dokumentum ismerteti egy Akusztika bake a Unity-szerkesztő bővítménnyel be.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: ce377234cceb22bc2c6979bedd92d800355e75da
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872084"
---
# <a name="bake-acoustics"></a>Bake Akusztika

Ez a dokumentum ismerteti egy Akusztika bake a Unity-szerkesztő bővítménnyel be. A Akusztika további háttér-információkért lásd: [Akusztika mi](what-is-acoustics.md). Rövid útmutató, lásd: [bevezetés](getting-started.md).

## <a name="import-the-plugin"></a>A beépülő modul importálása

A Akusztika beépülő modul csomag importálása a projekthez. Nyissa meg a Akusztika felhasználói felület kiválasztásával **ablak > Akusztika** a Unity menüből:

![Nyissa meg Akusztika ablak](media/WindowAcoustics.png)

## <a name="principles"></a>Alapelvek

A Akusztika eszköz ablak gyűjti az adatokat a Akusztika, motor van szüksége a számára a jelenethez Akusztika kiszámításához. Ennek során egy bake öt lépésből áll:

1. Hozzon létre, vagy a lejátszó navigációs háló megjelölése
2. Megjelölés Akusztika geometriai
3. Akusztikai anyagok tulajdonságok hozzárendelésére geometriai
4. Mintavétel elhelyezési előzetes verzió
5. Bake

A bake befejezése után tekintse meg a [tervezési folyamat áttekintése Akusztika](design-process.md) választható utáni bake tervezési lépéseit.

## <a name="create-or-mark-a-navigation-mesh"></a>Hozzon létre vagy egy navigációs háló megjelölése

Navigációs rácsvonal helyezze el a mintavétel pontok szimuláció szolgál. Használhatja a Unity [navigációs háló munkafolyamat](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html), vagy megadhatja a saját navigációs háló. Unity-a munkafolyamatot a létrehozott navigációs rácsvonalak fog észlelnie kell a Akusztika rendszer. Szeretné használni a saját rácsvonalak, jelölje meg azokat a **objektumok** lapon a következő lépésben leírtak szerint.

## <a name="objects-tab"></a>Objektumok lap

Nyissa meg a **objektumok** lapján a **Akusztika** ablak. Ezen a lapon a jelenet, amely egyszerűen ad meg a **AcousticsGeometry** vagy **AcousticsNavigation** az objektum-összetevőket. Is használhatja a [standard Unity összetevő munkafolyamat](https://docs.unity3d.com/Manual/UsingComponents.html) való üresként való megjelölése objektumokat.

Jelöljön ki egy vagy több objektumot a jelenet ablakban vagy a hierarchiában, és ezután üresként való megjelölése őket a **AcousticsGeometry** vagy **AcousticsNavigation** alább leírtak szerint. Ha semmi sincs kijelölve, majd meg is üresként való megjelölése a jelenetben mindent egyszerre.

Csak a háló Leképezőkhöz és Terrains jelölhető ki. Minden egyéb típusú objektumokat figyelmen kívül lesz hagyva. A jelölőnégyzetek fog üresként való megjelölése az összes érintett objektum.

Ha a jelenethez semmit, akkor ehhez hasonló lesz a következő képen látható:

![Objektumok lapon nincs kijelölés](media/ObjectsTabNoSelectionDetail.png)

Ha valami a jelenet vagy -hierarchiáját ablakban a kijelölt, akkor ehhez hasonló lesz a következő képen látható:

![Objektumok lapon nincs kijelölés](media/ObjectsTabWithSelectionDetail.png)

### <a name="objects-tab-parts"></a>Objektumok lapon részei

A lap részei a következők:

1. A lap kiválasztása gombok (**objektumok** lapja van kiválasztva). A gombok használatával különféle lépéseiről állapotát egy Akusztika bake balról jobbra.
2. Mit kell tennie, használatával ezen a lapon rövid leírása.
3. Rendelkezésre álló szűrők a hierarchia időszak. Ezzel a hierarchia ablakban, a megadott típusú objektumok szűrésére, így egyszerűbben jelölheti meg őket. Ha nem rendelkezik a még be van jelölve, a Akusztika semmit, az utolsó két lehetőség kiválasztásával megtudhatja, semmi nem. Lehetnek azonban hasznos megjelölt objektumok megkeresése, ha ezzel végzett.
4. Amikor egyetlen objektum van kijelölve, ez a szakasz bemutatja az összes objektum állapotát a jelenetben:
    * Összesen – a jelenetben aktív, nem rejtett objektumok teljes száma.
    * Figyelmen kívül hagyja - objektumok, amelyek nem a háló Leképezőkhöz vagy Terrains számát.
    * Háló - jelenet objektumokat megjelenítő háló száma
    * Terep – a jelenetben objektumok a terep száma
    * Geometriai - a "Akusztika geometriai" jelölésű jelenet objektumok a háló vagy terep száma
    * Navigáció – a "Akusztika navigációs" jelölésű jelenet objektumok háló vagy terep száma. Ez a szám nem tartalmazza a Unity NavMesh.
5. A helyszín, amely csak a háló Leképezőkhöz és Terrains "be van jelölve-képes" objektumok teljes száma látható. Látható jelölőnégyzetek használhatja megjelölni (adja hozzá a megfelelő való) azokat az objektumokat geometriai vagy Akusztika belüli navigációt
6. Ha semmi sincs kijelölve, ez a Megjegyzés emlékezteti a jelölésre objektumok kijelölése, ha szükséges. Jelölje meg a jelenetben összes objektum anélkül, hogy bármit egyik vagy mindkét jelölőnégyzetet is ellenőrizheti.
7. Amikor az objektum van kijelölve, ez a szakasz csak a kijelölt objektumok állapotát jeleníti meg.
8. "Be van jelölve-képes" kijelölt objektumok teljes számát jeleníti meg. Az ellenőrzés vagy a jelölőnégyzetek eszközhitelesítést fog üresként való megjelölése csak a kijelölt objektumok.

Ha vannak megjelölve az egyes objektumok, és nem bizonyos, a megfelelő jelölőnégyzetet az "vegyes" értéket jeleníti meg:

![Vegyes érték jelölőnégyzet](media/MixedObjectSelectionDetail.png)

A jelölőnégyzetére kattintva kényszeríti az összes objektum lesz megjelölve, és kattintson újra megszünteti az összes objektum.

Objektumok geometry és a navigációs jelölhető ki.

### <a name="guidelines-for-marking-objects"></a>Objektumok megjelölése irányelvek

Ügyeljen arra, hogy minden olyan objektumok, **Akusztika geometriai** azok kell occlude, tükrözik, vagy számára a hangot. Akusztika geometriai többek között az alapoktól, falak, tető, a windows & ablak üvegből, takaró és nagy bútordarab tartalmazhatnak. Nem probléma, például a lámpák, dekorációs elem, a lámpák, kisebb objektumok belefoglalása, mivel azok nem számottevően növelheti a költségek bake. Fontos hagyja ki például az alapoktól, vagy a felső fő elemet. Ezenkívül nem közé tartozik többek között, amelyek a Akusztika ütközési hálók például nem lehet hatással.

A mintavétel számítás időben egy objektum átalakító (keresztül a **mintavételek** lapon, az alábbiakban) a bake eredmények rögzített. Áthelyezi a kijelölt objektumok valamelyikének a jelenetben megismétlése a mintavétel számítási és a jelenet rebaking lesz szükség.

## <a name="materials-tab"></a>Anyagok lap

Ha az objektumok lesznek megjelölve, kattintson a **anyagok** gombra kattintva nyissa meg a anyagok lapot.

### <a name="parts-of-the-materials-tab"></a>Az anyagok lapon részei

![Anyagok lap részletei](media/MaterialsTabDetail.png)

1. A **anyagok** lap gomb segítségével ezt a lapot.
2. Mit kell tennie, használatával ezen a lapon rövid leírása.
3. Ha be van jelölve, csak az objektum által felhasznált anyagokat megjelölve **Akusztika geometriai** fog szerepelni. Ellenkező esetben a jelenetben használt összes anyag jelennek meg.
4. Ezek a beállítások segítségével módosíthatja a legördülő menüből, kattintson a legördülő lista alatt (6) a Akusztika oszlopban látható sorrendjét. **Név** akusztikai anyagok név szerint rendezi. "Absorptivity" absorptivity alacsony, magas, sorrendben rendezi őket.
5. A jelenetben betűrendben felhasznált anyagokat listája. Ha a **megjelenítése csak a megjelölt** jelölőnégyzet ellenőrzött (3), csak az objektum által felhasznált anyagokat megjelölve **Akusztika geometriai** jelennek meg. Olyan anyagot találhat az Ide kattintva lesz, válassza a minden objektumot használó anyagot találhat az, hogy a jelenet.
6. Az akusztikai anyag jeleníti meg, hogy a jelenet anyag van rendelve. Kattintson a legördülő lista egy másik akusztikai anyagok jelenet anyag szeretné hozzárendelni. A rendezési sorrend, a menü jelenik meg, amikor rákattint egy elem itt használatával módosíthatja a **Akusztika Rendezés:** fenti (4).
7. A kiválasztott az előző oszlop anyag akusztikai elnyelő együttható mutatja. Egy érték nulla azt jelenti tökéletesen reflektív (nincs elnyelő) során egy érték 1, tökéletesen nedvszívó (nincs tükröződés). Nem lehet módosítani a elnyelő együttható, kivéve, ha a kijelölt anyag "Egyéni".
8. Egy "Egyéni", a csúszka rendelt anyag már le van tiltva, és kiválaszthatja a csúszkával elnyelő együttható, vagy beír egy értéket. Lényeges tulajdonságait további információkért lásd: [tervezési folyamat áttekintése Akusztika](design-process.md).

### <a name="guidelines-for-assigning-materials-or-absorption-values"></a>Útmutató az anyagok (vagy értékek elnyelő) hozzárendelése

Ezen a lapon megpróbálja kitalálni a elnyelő az anyagok Mi a neve alapján. Például a jelenet anyag neve LivingRoom_WoodTable, a hozzárendelt kezdeti akusztikai anyag "fa" lesz. Anyagok, ahol egy ismert anyag nem lehet meghatározni az "Alapértelmezett" anyagot, amely rendelkezik a tényleges hasonló elnyelő vannak hozzárendelve.

Minden helyszín anyagot találhat az akusztikai anyagok is hozzárendelheti. Például ha egy hely hangok túl reverberant, módosítsa, emelet, vagy akusztikai anyaga valami újabb absorptivity. Az akusztikai anyagi hozzárendelés, hogy a jelenet anyag használó összes objektumra vonatkozik.

## <a name="probes-tab"></a>Mintavételezők lap

Miután hozzárendelte a anyagok, váltson át a **mintavételek** fülre.

### <a name="parts-of-the-probes-tab"></a>A mintavételek lapon részei

![Mintavételezők lap részletei](media/ProbesTabDetail.png)

1. A **mintavételek** ezt oldal megjelenítéséhez használt lap gomb
2. Mit kell tennie, használatával ezen a lapon rövid leírása
3. Válasszon egy durva vagy finom szimuláció feloldási ezeket használni. Elnagyolt gyorsabb, de bizonyos kompromisszumot kínál a rendelkezik. Lásd: ["durva vs részletes feloldási"](#Coarse-vs-Fine-Resolution) alábbi részleteket.
4. Válassza ki a helyet, ahol a Akusztika adatfájlok kell elhelyezni, hogy ez a mező használatával. A gombra kattintva a "...", a Mappalista használatára. Az alapértelmezett érték **eszközök/AcousticsData**. Egy **szerkesztő** almappa is alatt ezen a helyen jön létre. Adatfájlok kapcsolatos további információkért lásd: ["Data-Files"](#Data-Files) alatt.
5. Az adatfájlokat a helyszín neve lesz a megadott előtag ide használatával. Az alapértelmezett érték "Acoustics_ [helyszín neve]".
6. A mintavételezők kiszámított, miután a fenti vezérlők letiltásra kerül. Kattintson a **egyértelmű** a számítások törléséhez, és a vezérlők engedélyezése, hogy az új beállításokkal újraszámítása gombra.
7. Kattintson a **kiszámítása...**  gombra kattintva voxelize a helyszín és a mintavételi pontok helykérelmei kiszámításához. Ez a gépén helyileg történik, és a egy bake végrehajtása előtt kell elvégezni.

Ebben a verzióban a projekt Akusztika mintavételek nem helyezhető el manuálisan, és az automatizált folyamat a megadott keresztül kell helyezni a **mintavételek** fülre.

### <a name="what-the-calculate-button-calculates"></a>A "Kiszámítása..." gomb kiszámítja

A **kiszámítása...**  gomb, amennyiben megadott összes adatot vesz igénybe (geometry, navigációs, anyagokat és a Elnagyolt/részletes beállítás), és több lépésre végighalad:

1. A geometriai telik el a jelenet rácsvonalak és a egy voxel kötet számítja ki. A voxel kötet olyan 3 dimenziós kötet, amely a teljes jelenet helyezi, és kis m3 "voxels" áll. A szimuláció gyakorisága, amely úgy van beállítva a voxels mérete határozza meg a **szimuláció feloldási** beállítás. Minden egyes voxel meg van jelölve, vagy hogy "nyissa meg a vezeték nélkül", vagy jelenet geometriai tartalmazó. Ha egy voxel tartalmazza a geometriai a voxel címkéjű van az adott geometriai rendelt anyag elnyelő együtthatója.
2. Akusztikailag érdekes helyeket, ahol előfordulhat, hogy nyissa meg a Windows Media player kiszámításához navigációs adatokat ezután használja. Ez megpróbálja megkeresni egy viszonylag kis készletét ezek a helyek, amely tartalmazza a kisebb területeket, például az átjáróknak és hallways, majd a termek tárolóhelyek megnyitásához. Kisebb jeleneteket a Ez általában a 100-nál kevesebb helyen, bár előfordulhat, hogy nagy jelenetek Kaliforniában akár.
3. Az utolsó figyelő helyekről, kiszámítja az egyes meghatározza, hogy a paraméterek számát, például hogyan "nyitott" az a terület méretét a hely van, stb.
4. Ezek a számítások eredményét a megadott helyen található fájlok tárolják (lásd: ["Data-Files"](#Data-Files) alább)

Ezeket a számításokat a jelenethez méretétől és sebességétől, a gép, függően több percig is eltarthat.

Ha ezeket a számításokat befejeződött, megtekintheti a voxel adatai és a mintavételi pontok helykérelmei annak biztosítására, hogy a bake kap jó eredmények egyaránt. Többek között egy rossz navigációs háló vagy hiányzó/nagyon geometriai általában gyorsan látható lesz az előzetes verzióban érhető el, kijavíthatja.

### <a name="scene-rename"></a>Jelenet átnevezése

A helyszín neve a jelenet csatlakozhat fájlokhoz a mintavétel pont elhelyezési és voxelization tárolására szolgál. A jelenet át lett nevezve, mintavételi pontok kiszámítása után, ha az anyagi-hozzárendelés és elhelyezési adatok elvesztését, és újra kell futtatnia.

## <a name="debug-display-through-gizmos"></a>Megjelenítési keresztül Gizmos hibakeresése

Alapértelmezés szerint mind a **mintavételek** és **Voxels** gizmos be vannak kapcsolva. Ezek bemutatják, mintavétel, valamint a voxels számított helyek mutasson. Akkor engedélyezhető, vagy le van tiltva, a Gizmos menü segítségével:

![Gizmos menü](media/GizmosMenu.png)

### <a name="voxels"></a>Voxels

Voxels jelennek meg a jelenet ablakban zöld kockák részt vevő geometriai körül. Csak a légi tartalmazó Voxels nem jelennek meg. A teljes jelenet, amely azt jelzi, hogy a teljes voxel kötet, amely felhasználja a szimuláció nagy zöld Keretes van.
Navigálás a jelenethez, és ellenőrizze, hogy mindent, ami lehet geometry rendelkezik voxels. A helyszín kamera nem lehet a voxels megjelenítéséhez az objektumának körülbelül 5 mérőszámok belül.

Ha összehasonlítja a voxels durva feloldási vs finom megoldás használatával létrehozott, látni fogja, hogy az a durva voxels kétszer akkora.

![Voxel előzetes verzió](media/VoxelCubesPreview.png)

### <a name="probe-points"></a>Mintavétel pontok

Mintavétel pontok azonos a lehetséges player (figyelő) helyen. Egy bake visszamehet az Akusztika egy megbízható forrás bármely pontján található a jelenet e mintavétel pontok mindegyikéhez számítható ki. Ha a Windows Media player nem közvetlenül a mintavétel pont helyen, az adatokat a mintavétel pontok azt a Windows Media player segítségével interpolálja a paramétereket, amelyek az adott helyen.

Ezért azt, fontos, hogy a mintavétel pontok bárhol létezik a Windows Media player várható utazási a jelenetben, valamint, hogy kis területek és átjáróknak megfelelően vannak-e megadva. Mintavétel pontok a projekt Akusztika motor a jelenethez geometriai értelmezése alapján kerülnek, és nem lehet áthelyezni vagy szerkeszteni a Tervező előzetes verzióban érhető el. Használja őket, ellenőrizze, hogy a geometriai jelölés és a navigációs mutató adatok.

![Mintavételezők előzetes verzió](media/ProbesPreview.png)

### <a name="Coarse-vs-Fine-Resolution"></a>Durva vs finom felbontás

Az egyetlen különbség a Elnagyolt és részletes feloldásának beállításai között a gyakoriságot, amellyel a szimuláció történik. Részletes gyakoriságot használja kétszer legmagasabb Elnagyolt szerint.
Bár ez egyszerűnek tűnhet, az az akusztikai szimuláció rendelkezik következmények számos:

* A legnagyobb a Elnagyolt kétszer, hosszú szerint működik, és ezért a voxels kétszer akkora van.
* A szimuláció idő voxel méretét, így egy 16-szor gyorsabban finom bake os Elnagyolt közvetlenül kapcsolódik.
* Nem lehet a portálok a (például ajtók vagy windows) kisebb, mint voxel szimulált. Durva beállítás okozhat egyes ezek kisebb portáljára, nem kell szimulált; ezért azok nem felel meg eredményes keresztül futásidőben. Láthatja, ha ez történik a voxels megtekintésével.
* A szimuláció kisebb gyakorisággal kevesebb diffraction sarkok és élek eredményez.
* Nem megbízható forrásból csoportdobozban voxels "kitöltött", amely, amely tartalmazza a geometriai voxels – ennek eredményeként nincs hang. Keresse meg a megbízható forrásból, így nem belül elnagyolt, a nagyobb voxels finom beállítást használja, mint nagyobb nehézséget jelent.
* A nagyobb voxels fog végberendezésébe több portálok, az alább látható módon. Az első rendszerkép használatával elnagyolt, míg a második azonos kezdőpanelje finom megoldás használatával lett létrehozva. A piros jelöléseket aszinkronitást nincs sokkal kevesebb behatolás kezdőpanelje finom beállítások használatával. A kék vonal kezdőpanelje alapján a geometriai, míg a piros vonalat a hatékony akusztikai portál voxel méretét határozzák meg. Hogyan a behatolás játszik az adott helyzetben függ teljesen hogyan a voxels illeszkedik a geometriai, a portál, amelynek a méretét és az objektumok a jelenetben helyét határozza meg.

![Coarse Doorway](media/CoarseVoxelDoorway.png)

![Fine Doorway](media/FineVoxelDoorway.png)

## <a name="bake-tab"></a>Bake lap

Ha elégedett a előzetes verziójában lévő adatokat, az a **os** fülre, és küldje el a bake a felhőbe.

### <a name="parts-of-the-bake-tab"></a>A bake lapon részei

![Bake lap részletei](media/BakeTabDetails.png)

1. Ezt a lapot szolgáló lap os gomb.
2. Mi a teendő ezen az oldalon rövid leírása.
3. Adja meg Azure hitelesítő adatait, ha az Azure-fiókja létrejött mezőket. További információkért lásd: [Azure Batch-fiók létrehozása](create-azure-account.md).
4. Docker a Akusztika eszközkészlet képcímke.
5. Indítsa el az Azure Portalon kezelheti az előfizetéseit, figyelheti és tekinteni a számlázási adatokat stb. 
6. Az Azure batch számítási csomópont típusa kiszámításához használandó. A csomóponttípus támogatnia kell az Azure az Adatközpont-helyen. Ha nem biztos maradjon a **Standard_F8s_v2**.
7. Ehhez a számításhoz használt csomópontok száma. Az itt hatással van a bake elvégzéséhez szükséges idő, és az Azure Batch alapvető foglalási korlátozza. Az alapértelmezett lefoglalási csak lehetővé teszi, hogy két 8 mag csomópontok vagy egy 16 mag csomópont, de bővíthetők. A core foglalási megkötések további információkért lásd: [Azure Batch-fiók létrehozása](create-azure-account.md).
8. A jelölőnégyzet bejelölésével konfigurálhatja a számítási készletet használandó [alacsony prioritású csomópontok](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Alacsony prioritású számítási csomópontok kell sokkal alacsonyabb költségek, de előfordulhat, hogy nem érhető el, vagy a tetszőleges időpontban háttérbe szorulhatnak.
9. A helyszín, a kiszámított mintavételi számát a **mintavételek** fülre. A mintavételek számának meghatározásához szimulációk esetében, amelyeket a felhőben futtatni kell a száma határozza meg. Mintavételezők számánál több csomópont nem adható meg.
10. Valószínű, hogy a feladatnak a felhőben való futtatásra tennie eltelt idő mennyisége. Ez nem vonatkozik a csomópont indítási idejének. Miután a feladat futni kezd, ez a arról, hogy mennyi ideig előtt kapja vissza az eredményeket kell lennie. Vegye figyelembe, hogy ez csak egy becsült.
11. A teljes, a szimulációk futtatásához szükséges számítási idő mennyisége. Ez a csomópont számítási idő, az Azure-ban használt teljes mennyisége. Lásd: [Estimating bake költség](#Estimating-bake-cost) alatt további információt az ezzel az értékkel.
12. Ez az üzenet arra kéri, ahol a bake eredményeit menti a rendszer a feladat befejezése után.
13. (Csak a speciális használata) Ha valamilyen okból nincs szüksége egy bake kapcsolatos megfeledkezni Unity kényszerítése küldte el (pl. letöltötte az eredményeket egy másik gépen), kattintson a **állapot törlése** elfelejtette a feladatról küldött gombra. Vegye figyelembe, hogy ez azt jelenti, hogy az eredményfájlt, ha készen áll, a rendszer **nem** tölthető le, és **ez nem ugyanaz, mint a feladat megszakítása**. A feladatot, ha fut, továbbra is a felhőben való futtatásra.
14. A Bake gombra kattintva küldje el a bake a felhőbe. A feladat futása közben látható **feladat megszakítása** helyette.
15. Előkészíti a helyi gépen Akusztika szimuláció feldolgozása. Lásd: [helyi bake](#Local-bake) további információt.  
16. Ez a terület a bake állapotát jeleníti meg. Amikor elkészült, ennek meg kell jelennie **letöltött**.

Teljes körű információt arról, hogy aktív feladatok, a számítási készletek és a tárolás mindig megtekintheti a [az Azure Portal](https://portal.azure.com).

A feladat futása közben a **os** gomb felirata **feladat megszakítása**. Ez a gomb használatával szakítsa meg a folyamatban lévő feladatot. Meg kell adnia annak megerősítéséhez, mielőtt a feladat meg lett szakítva. A feladat megszakítása nem lehet visszavonni, eredménytelen lesz elérhető, és továbbra is kell díjat fizetnie használt Azure-beli számítási bármikor.

Egy bake megkezdése után bezárhatja a Unity. Attól függően a projektet, a csomópont típusa és a csomópontok számát a felhő bake több óráig is tarthat. Töltse be újra a projektet, és nyissa meg a Akusztika ablakot a bake feladat állapota frissülni fog. Ha a feladat befejeződött, a rendszer letölti a kimeneti fájlt.

Az Azure-beli hitelesítő rendszer biztonságosan tárolja a helyi gépen, és a Unity-szerkesztő társított. Ezek szolgálnak, kizárólag az Azure-bA biztonságos kapcsolatot létesíteni.

### <a name="Estimating-bake-cost"></a> Az Azure bake költségek becslése

Megbecsülheti egy adott bake díjait, hajtsa végre a megjelenített érték **becsült költség számítási**, ez az időtartam és a kettő többszörösének, amely szerint az óránkénti költség, a helyi pénznemben számított a **VM csomóponttípus** választotta. Az eredmény nem tartalmazza a csomópont idő szükséges a csomópontok szolgáltatásszintje és futtatásához. Például, ha kiválasztja **Standard_F8s_v2** az adott csomóponttípus költsége pedig $ 0,40/óra, amely rendelkezik, és a számítási becsült költség 3 óra és 57 perc, a feladat futtatásához a becsült költség lesz $0,40 * ~ 4 óra = ~ $1.60. A tényleges költségek valószínűleg egy kicsit nagyobb miatt az első lépések a csomópontok hosszabb időt. Az óránkénti csomópont költsége annak a [Azure Batch szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (válassza "számításra optimalizált" vagy "nagy teljesítményű számítás" kategória) lap.

### <a name="reviewing-the-bake-results"></a>A bake találatok áttekintése

A bake befejezése után ellenőrizze, hogy a mintavétel, valamint voxels pontok a várt helyen a beépülő modul futásidejű futtatásával. További információk [tervezési folyamat áttekintése Akusztika](design-process.md).

## <a name="Local-bake"></a>Helyi bake
Helyi bake Akusztika szimulátor helyett az Azure Batch számítási fürtnek való kiszervezése azt a saját számítógépen fut. Ez lehet egy jó választás kísérleteztek Akusztika az Azure-előfizetés nélkül. Vegye figyelembe, hogy a Akusztika szimuláció van nagy számítási erőforrás-igényes, és a jelenet, a szimuláció konfiguráció és a nyers számítási teljesítmény, a feldolgozó gép méretétől függően hosszabb időt is igénybe vehet.

### <a name="minimum-hardware-requirements"></a>Minimális hardverkövetelmények
64-bit-es Intel processzorral, legalább 8 maggal és 32 GB RAM vagy nagyobb.

Tegyük fel, az Intel Xeon E5-1660 @ 3 GHz-es és 32 GB RAM - gépen 8 mag
* 100 mintákkal kisebb jeleneteket a egy durva bake és a egy vékony feloldási bake ~ 32 óráig KB. 2 órát vesz igénybe.
* 1000 mintákkal nagyobb jelenet órát is igénybe vehet legfeljebb körülbelül 20 a egy durva feloldásához és a egy vékony feloldási bake ~ 21 nap.

### <a name="setup-docker"></a>A telepítő Docker
Telepítse és konfigurálja a Docker a számítógépen, amely feldolgozza a szimuláció-
1. Telepítse a [Docker eszközkészlet](https://www.docker.com/products/docker-desktop).
2. Indítsa el a Docker-beállítások, keresse meg a "Speciális" beállításokat, és 8 GB RAM-MAL konfigurálására, hogy az erőforrások. A több processzort foglalhat le a Docker, annál gyorsabban bake fog befejeződni. ![Docker-példabeállítások](media/DockerSettings.png)
3. Keresse meg "Megosztott meghajtók", és kapcsolja be a feldolgozáshoz használt meghajtó megosztása.![DockerDriveSharing](media/DockerSharedDrives.png)

### <a name="run-local-bake"></a>Helyi bake futtatása
1. Kattintson a lap Bake "Előkészítése helyi os" gombjára, és válasszon ki egy mappát, ahol a bemeneti fájlok és parancsfájlok végrehajtása menti a rendszer. Ezután futtathatja a bake bármely gépen mindaddig, amíg megfelel-e a minimális hardverkövetelményeknek, és a Docker telepítve van a mappa, hogy a gép másolásával.
2. Indítsa el a szimulációt, a "runlocalbake.bat" parancsfájl használatával. Ez a szkript beolvassa a szimuláció feldolgozáshoz szükséges eszközkészlet a projekt Akusztika Docker-rendszerképet, és a a szimuláció elindításához. 
3. A szimuláció befejeztével másolja az eredményül kapott .ace fájl vissza a Unity-projektjét. Ahhoz, hogy a Unity felismeri a bináris fájlként, ".bytes" hozzáfűzése a fájlkiterjesztés (például "Scene1.ace.bytes"). A részletes naplókat a szimuláció vannak tárolva "AcousticsLog.txt." Ha problémákat tapasztal, ossza meg ezt a diagnosztikát segítő fájlt.

## <a name="Data-Files"></a>Adatfájlok

Nincsenek különböző időpontokban a beépülő modul által létrehozott négy adatfájlokat. Csak az egyik van szükség, futtatás közben, ezért a többi három találhatók mappák "Szerkesztő" nevű, így azok nem fordítható le, a projektbe.

* **Assets/Editor/[SceneName]\_AcousticsParameters.asset**: A fájl tárolja az adatokat a mezőkben a Akusztika felhasználói felületén. A hely és a fájl neve nem lehet módosítani. Ebben a fájlban tárolt más értékek, amelyek a bake hatással, de tapasztalt felhasználók számára, és nem szabad módosítani.
* **Eszközök/AcousticsData/Akusztika\_[SceneName].ace.bytes**: Ez a fájl, mi a bake szimuláció során létrejön, és a helyszín a Akusztika megjelenítése a modul által használt keresési adatait tartalmazza. A helyét, és ez a fájl nevét is módosítható a mezői a **mintavételek** fülre.
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox**: Ez a fájl tartalmazza a voxelized Akusztika geometry és a lényeges tulajdonságait. Kiszámítása a **Calculate...**  gombra a mintavételek lapon. A helyét, és ez a fájl nevét is módosítható a mezői a **mintavételek** fülre.
* **Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml**: Ez a fájl tartalmazza a paraméterek kiszámítása a **Calculate...**  gombot a **mintavételek** fülre. A helyét, és ez a fájl nevét is módosítható a mezői a **mintavételek** fülre.

Nem, hogy törli a *. ace.bytes fájlt letölteni a bake. Ez a fájl nem helyreállítható, kivéve a jelenet rebaking által.

## <a name="next-steps"></a>További lépések
* A megbízható forrásra bake eredményei alkalmazásának [tervezési folyamat áttekintése Akusztika](design-process.md).

