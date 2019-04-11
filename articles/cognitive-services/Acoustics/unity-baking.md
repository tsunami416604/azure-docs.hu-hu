---
title: Projekt Akusztika Unity Bake oktatóanyag
titlesuffix: Azure Cognitive Services
description: Ez az oktatóanyag azt ismerteti, és a Unity Project Akusztika sütés Akusztika.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 8875674b0f9c621a573dda591b4dc2b6f018a83c
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470338"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Projekt Akusztika Unity Bake oktatóanyag
Ez az oktatóanyag azt ismerteti, és a Unity Project Akusztika sütés Akusztika.

Szoftverkövetelmények:
* [Unity 2018.2 +](http://unity3d.com) Windows esetében
* [Unity-projektjét a projekt Akusztika beépülő modul integrált](unity-integration.md) vagy a [Akusztika Unity Project minta tartalom](unity-quickstart.md)
* Nem kötelező: Egy [Azure Batch-fiók](create-azure-account.md) felgyorsítása a kódfejlesztést használatával a felhő-számítástechnika

## <a name="open-the-project-acoustics-bake-window"></a>Nyissa meg a projekt Akusztika os ablak
Válasszon **ablak > Akusztika** a Unity menüből:

![Képernyőkép a Unity-szerkesztő Akusztika ablak menüpont kiemelésével](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>A navigációs háló létrehozása
Projekt Akusztika figyelő mintavételi pontok szimuláció elhelyezése egy navigációs háló használja. Használhatja a Unity [navigációs háló munkafolyamat](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html), vagy használjon egy másik 3D modellezési csomag a saját háló megtervezéséhez. 

## <a name="mark-acoustic-scene-objects"></a>Akusztikai jelenet objektumokat megjelölhesse
Projekt Akusztika szimuláció jelenet objektumok két típusú támaszkodik: az objektumok tükrözik, és hangfájlokat, a szimuláció, és korlátozza a szimuláció figyelő mintavételi pontjainak player navigációs háló occlude. Mindkét típusú objektumokat használatával vannak megjelölve az **objektumok** fülre. 

Mivel objektumok megjelölése egyszerűen ad a **AcousticsGeometry** vagy **AcousticsNavigation** összetevőket az objektum is használhatja a [standard Unity összetevő munkafolyamat](https://docs.unity3d.com/Manual/UsingComponents.html)való üresként való megjelölése objektumokat. Csak a háló Leképezőkhöz és Terrains jelölhető ki. Minden egyéb típusú objektumokat figyelmen kívül lesz hagyva. A jelölőnégyzetek fog üresként való megjelölése az összes érintett objektum.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Akusztikai hangelnyelés és tükröződés geometriai megjelölése
Nyissa meg a **objektumok** lapján a **Akusztika** ablak. Mint bármely objektumokat megjelölhesse **Akusztika geometriai** azok kell occlude, tükrözik, vagy számára a hangot. Akusztika geometriai többek között az alapoktól, falak, tető, a windows & ablak üvegből, takaró és nagy bútordarab tartalmazhatnak. Ezek az objektumok bármilyen tetszőleges az összetettségi szintet is használhat. Mivel a jelenet voxelized előtt szimuláció, nagyon részletes rácsvonalak, például a sok kis leveleket, olyan fák, amelyeknél nem költségesebb, mint egyszerűsített objektumok os.

Nem lehet hatással a Akusztika, például láthatatlan ütközési rácsvonalak, amit nem tartalmaznak.

A mintavétel számítás időben egy objektum átalakító (keresztül a **mintavételek** lapon, az alábbiakban) a bake eredmények rögzített. Áthelyezi a kijelölt objektumok valamelyikének a jelenetben megismétlése a mintavétel számítási és a jelenet rebaking lesz szükség.

### <a name="mark-the-navigation-mesh"></a>A navigációs háló megjelölése
Unity-a munkafolyamatot a létrehozott navigációs rácsvonalak fog észlelnie kell a Akusztika rendszer. Szeretné használni a saját rácsvonalak, jelölje meg azokat a **objektumok** fülre.

### <a name="for-reference-the-objects-tab-parts"></a>A dokumentáció: Az objektumok lapon részei
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

Ha a jelenethez semmit, az objektumok lapon néznek a következő képen látható:

![Nincs kijelölés lap Akusztika objektumok képernyőképe](media/objects-tab-no-selection-detail.png)

Ha valami a jelenet vagy -hierarchiáját ablakban a kijelölt, akkor ehhez hasonló lesz a következő képen látható:

![Képernyőkép a Akusztika objektumokat tartalmazó lapra kijelölést](media/objects-tab-selection-detail.png)

Ha vannak megjelölve az egyes objektumok, és nem bizonyos, a megfelelő jelölőnégyzetet az "vegyes" értéket jeleníti meg:

![Vegyes kijelölés ikonja kiemelve lap Akusztika objektumok képernyőképe](media/mixed-object-selection-detail.png)

A jelölőnégyzetére kattintva kényszeríti az összes objektum lesz megjelölve, és kattintson újra megszünteti az összes objektum.

Objektumok geometry és a navigációs jelölhető ki.

## <a name="select-acoustic-materials"></a>Válassza ki a akusztikai anyagok
Ha az objektumok lesznek megjelölve, kattintson a **anyagok** gombra, és rendelje hozzá a akusztikai anyagok. A projekt Akusztika anyagok rendszer vannak kötve, a Unity vizuális anyagok rendszer: két objektumok szeretné, hogy külön akusztikai anyagok, rendelkeznie kell külön vizuális anyagok.

Az akusztikai anyagok ellenőrzés kerülnek vissza minden egyes felületből eredményes energia mennyiségét. Az alapértelmezett akusztikai anyag tényleges hasonló elnyelő rendelkezik. Projekt Akusztika anyagok a vizuális anyagi neve alapján javasol. Az akusztikai anyag "Custom" rendelhet egy anyag-elnyelő együttható csúszka engedélyezéséhez.

Egy adott anyag szoba reverberation idején intenzitásfokozatok kapcsolódik, a elnyelő együttható a legtöbb anyagok elnyelő értékeket kellene 0,01 való 0,20 tartományban. Az ezen a tartományon kívül elnyelő együttható anyagok olyan nagyon elnyelő.

![Elnyelő együttható reverberation időben negatív összekapcsolását ábrázoló grafikon](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>A dokumentáció: Az anyagok lapon részei
![Unity lapján Akusztika anyagok képernyőképe](media/materials-tab-detail.png)

1. A **anyagok** lap gomb segítségével ezt a lapot.
2. Mit kell tennie, használatával ezen a lapon rövid leírása.
3. Ha be van jelölve, csak az objektum által felhasznált anyagokat megjelölve **Akusztika geometriai** fog szerepelni. Ellenkező esetben a jelenetben használt összes anyag jelennek meg.
4. Ezek a beállítások segítségével módosíthatja a legördülő menüből, kattintson a legördülő lista alatt (6) a Akusztika oszlopban látható sorrendjét. **Név** akusztikai anyagok név szerint rendezi. "Absorptivity" absorptivity alacsony, magas, sorrendben rendezi őket.
5. A jelenetben betűrendben felhasznált anyagokat listája. Ha a **megjelenítése csak a megjelölt** jelölőnégyzet ellenőrzött (3), csak az objektum által felhasznált anyagokat megjelölve **Akusztika geometriai** jelennek meg. Olyan anyagot találhat az Ide kattintva lesz, válassza a minden objektumot használó anyagot találhat az, hogy a jelenet.
6. Az akusztikai anyag jeleníti meg, hogy a jelenet anyag van rendelve. Kattintson a legördülő lista egy másik akusztikai anyagok jelenet anyag szeretné hozzárendelni. A rendezési sorrend, a menü jelenik meg, amikor rákattint egy elem itt használatával módosíthatja a **Akusztika Rendezés:** fenti (4).
7. A kiválasztott az előző oszlop anyag akusztikai elnyelő együttható mutatja. Egy érték nulla azt jelenti tökéletesen reflektív (nincs elnyelő) során egy érték 1, tökéletesen nedvszívó (nincs tükröződés). Nem lehet módosítani a elnyelő együttható, kivéve, ha a kijelölt anyag "Egyéni".
8. Egy "Egyéni", a csúszka rendelt anyag már le van tiltva, és kiválaszthatja a csúszkával elnyelő együttható, vagy beír egy értéket.

## <a name="calculate-and-review-listener-probe-locations"></a>Kiszámíthatja, és tekintse át a figyelő mintavételek helyeit
Miután hozzárendelte a anyagok, váltson át a **mintavételek** fülre, és **Calculate** figyelő mintavételi pontok szimuláció helyezi el.

### <a name="what-the-calculate-button-calculates"></a>A "Kiszámítása..." gomb kiszámítja
A **kiszámítása...**  gomb a kiválasztott akusztikai jelenet geometriai használja fel a jelenethez a szimuláció:

1. A geometriai telik el a jelenet rácsvonalak és a egy voxel kötet számítja ki. A voxel kötet olyan 3 dimenziós kötet, amely a teljes jelenet helyezi, és kis m3 "voxels" áll. A szimuláció gyakorisága, amely úgy van beállítva a voxels mérete határozza meg a **szimuláció feloldási** beállítás. Minden egyes voxel meg van jelölve, vagy hogy "nyissa meg a vezeték nélkül", vagy jelenet geometriai tartalmazó. Ha egy voxel tartalmazza a geometriai a voxel címkéjű van az adott geometriai rendelt anyag elnyelő együtthatója.
2. A navigációs mesh(es) helyezze figyelő mintavételi pontokat használ. Az algoritmus elosztja a térbeli lefedettség és szimuláció időt és a fájl mérete, közben biztosítva a konkurens megfontolások, amelyek a folyosókon szűkítheti és kis méretű tárolóhelyek mindig lefedettség bizonyos mennyiségű. Tipikus mintavételi pont tartomány kisebb jeleneteket a 100-ról való néhány ezer a nagy jelenetek számít.

Ezeket a számításokat a jelenethez méretétől és sebességétől, a gép, függően több percig is eltarthat.

### <a name="review-voxel-and-probe-placement"></a>Mintavétel, valamint voxel elhelyezési áttekintése
Előzetes verzió is voxel adatokat, és győződjön meg arról, készen áll a jelenethez os mintavételi pont helyét. Egy hiányos navigációs háló vagy hiányzó vagy nagyon akusztikai geometriai általában gyorsan látható lesz az előzetes verzióban érhető el. Mintavétel, valamint Voxel elhelyezése is engedélyezhető, vagy le van tiltva, a Gizmos menü segítségével:

![Képernyőkép a Gizmos menü a Unity-nél](media/gizmos-menu.png)

Akusztikai geometriai tartalmazó Voxels zöld kockák jelennek meg. Fedezze fel a jelenethez, és ellenőrizze, hogy mindent, ami lehet geometry rendelkezik voxels. A helyszín kamera nem lehet a voxels megjelenítéséhez az objektumának körülbelül 5 mérőszámok belül.

Ha összehasonlítja a voxels durva feloldási vs finom megoldás használatával létrehozott, látni fogja, hogy az a durva voxels kétszer akkora.

![Képernyőkép a Unity-szerkesztőben durva voxels előzetes](media/voxel-cubes-preview.png)

Szimuláció eredményeit a rendszer interpolált figyelő mintavételi pontok helykérelmei futásidőben között. Ellenőrizze a mintavétel pont bármely helyre, a Windows Media player várható utazási a jelenetben.

![Képernyőkép a mintavételek a Unity-szerkesztőben előzetes verzió](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Kezelje a jelenet átnevezése
A helyszín neve a jelenet csatlakozhat fájlokhoz a mintavétel pont elhelyezési és voxelization tárolására szolgál. A jelenet át lett nevezve, mintavételi pontok kiszámítása után, ha az anyagi-hozzárendelés és elhelyezési adatok elvesztését, és újra kell futtatnia.

### <a name="for-reference-parts-of-the-probes-tab"></a>A dokumentáció: A mintavételek lapon részei
![Képernyőkép a Akusztika mintavételek fülre a Unity-nél](media/probes-tab-detail.png)

1. A **mintavételek** ezt oldal megjelenítéséhez használt lap gomb
2. Mit kell tennie, használatával ezen a lapon rövid leírása
3. Válasszon egy durva vagy finom szimuláció feloldási ezeket használni. Elnagyolt gyorsabb, de bizonyos kompromisszumot kínál a rendelkezik. Lásd: [os feloldási](bake-resolution.md) alábbi részleteket.
4. Válassza ki a helyet, ahol a Akusztika adatfájlok kell elhelyezni, hogy ez a mező használatával. A gombra kattintva a "...", a Mappalista használatára. Az alapértelmezett érték **eszközök/AcousticsData**. Egy **szerkesztő** almappa is alatt ezen a helyen jön létre. Adatfájlok kapcsolatos további információkért lásd: [adatfájlok](#Data-Files) alatt.
5. Az adatfájlokat a helyszín neve lesz a megadott előtag ide használatával. Az alapértelmezett érték "Acoustics_ [helyszín neve]".
6. A mintavételezők kiszámított, miután a fenti vezérlők letiltásra kerül. Kattintson a **egyértelmű** a számítások törléséhez, és a vezérlők engedélyezése, hogy az új beállításokkal újraszámítása gombra.
7. Kattintson a **kiszámítása...**  gombra kattintva voxelize a helyszín és a mintavételi pontok helykérelmei kiszámításához. Ez a gépén helyileg történik, és a egy bake végrehajtása előtt kell elvégezni.

Ebben a verzióban a projekt Akusztika mintavételek nem helyezhető el manuálisan, és az automatizált folyamat a megadott keresztül kell helyezni a **mintavételek** fülre.

Lásd: [os feloldási](bake-resolution.md) durva vs további részleteiért finom megoldás.

## <a name="bake-your-scene-using-azure-batch"></a>Az Azure Batch használatával jelenet os
A számítási fürt a felhőben az Azure Batch szolgáltatás használatával jelenetet is os. A projekt Akusztika Unity beépülő modul közvetlenül csatlakozik az Azure Batch számára hozza létre, kezelhet és egy Azure Batch-fürtöt, az egyes bake üzemen. Az a **os** lapra, adja meg Azure hitelesítő adatait, válassza ki a fürt gép típusát és méretét, és kattintson **os**.

### <a name="for-reference-parts-of-the-bake-tab"></a>A dokumentáció: A Bake lapon részei
![Képernyőkép a Akusztika os fülre a Unity-nél](media/bake-tab-details.png)

1. Ezt a lapot szolgáló lap os gomb.
2. Mi a teendő ezen az oldalon rövid leírása.
3. Adja meg Azure hitelesítő adatait, ha az Azure-fiókja létrejött mezőket. További információkért lásd: [Azure Batch-fiók létrehozása](create-azure-account.md).
4. Docker a Akusztika eszközkészlet képcímke.
5. Indítsa el az Azure Portalon kezelheti az előfizetéseit, figyelheti és tekinteni a számlázási adatokat stb. 
6. Az Azure batch számítási csomópont típusa kiszámításához használandó. A csomóponttípus támogatnia kell az Azure az Adatközpont-helyen. Ha nem biztos maradjon a **Standard_F8s_v2**.
7. Ehhez a számításhoz használt csomópontok száma. Az itt hatással van a bake elvégzéséhez szükséges idő, és az Azure Batch alapvető foglalási korlátozza. Az alapértelmezett lefoglalási csak lehetővé teszi, hogy két 8 mag csomópontok vagy egy 16 mag csomópont, de bővíthetők. A core foglalási megkötések további információkért lásd: [Azure Batch-fiók létrehozása](create-azure-account.md).
8. A jelölőnégyzet bejelölésével konfigurálhatja a számítási készletet használandó [alacsony prioritású csomópontok](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Alacsony prioritású számítási csomópontok kell sokkal alacsonyabb költségek, de előfordulhat, hogy nem érhető el, vagy a tetszőleges időpontban háttérbe szorulhatnak.
9. A helyszín, a kiszámított mintavételi számát a **mintavételek** fülre. A mintavételek számának meghatározásához szimulációk esetében, amelyeket a felhőben futtatni kell a száma határozza meg. Mintavételezők számánál több csomópont nem adható meg.
10. Eltelt idő, vár a feladatnak a felhőben való futtatásra tennie. Ez nem vonatkozik a csomópont indítási idejének. Miután a feladat futni kezd, ez a arról, hogy mennyi ideig előtt kapja vissza az eredményeket kell lennie. Vegye figyelembe, hogy ez csak egy becsült.
11. A teljes, a szimulációk futtatásához szükséges számítási idő mennyisége. Ez a csomópont számítási idő, az Azure-ban használt teljes mennyisége. Lásd: [Estimating bake költség](#Estimating-bake-cost) alatt további információt az ezzel az értékkel.
12. Ez az üzenet arra kéri, ahol a bake eredményeit menti a rendszer a feladat befejezése után.
13. (Csak a speciális használata) Ha valamilyen okból nincs szüksége egy bake kapcsolatos megfeledkezni Unity kényszerítése küldte el (pl. letöltötte az eredményeket egy másik gépen), kattintson a **állapot törlése** elfelejtette a feladatról küldött gombra. Vegye figyelembe, hogy ez azt jelenti, hogy az eredményfájlt, ha készen áll, a rendszer **nem** tölthető le, és **ez nem ugyanaz, mint a feladat megszakítása**. A feladatot, ha fut, továbbra is a felhőben való futtatásra.
14. Kattintson a **os** gombra kattintva küldje el a bake a felhőbe. A feladat futása közben látható **feladat megszakítása** helyette.
15. Előkészíti a feldolgozási [Akusztika szimulálása a számítógépen](#Local-bake).
16. Ez a terület a bake állapotát jeleníti meg. Amikor elkészült, ennek meg kell jelennie **letöltött**.

Teljes körű információt arról, hogy aktív feladatok, a számítási készletek és a tárolás mindig megtekintheti a [az Azure portal](https://portal.azure.com).

A feladat futása közben a **os** gomb felirata **feladat megszakítása**. Ez a gomb használatával szakítsa meg a folyamatban lévő feladatot. Meg kell adnia annak megerősítéséhez, mielőtt a feladat meg lett szakítva. A feladat megszakítása nem lehet visszavonni, eredménytelen lesz elérhető, és továbbra is kell díjat fizetnie használt Azure-beli számítási bármikor.

Egy bake megkezdése után bezárhatja a Unity. Attól függően a projektet, a csomópont típusa és a csomópontok számát a felhő bake több óráig is tarthat. Töltse be újra a projektet, és nyissa meg a Akusztika ablakot a bake feladat állapota frissülni fog. Ha a feladat befejeződött, a rendszer letölti a kimeneti fájlt.

Az Azure-beli hitelesítő rendszer biztonságosan tárolja a helyi gépen, és a Unity-szerkesztő társított. Ezek szolgálnak, kizárólag az Azure-bA biztonságos kapcsolatot létesíteni.

### <a name="Estimating-bake-cost"></a> Az Azure bake költségek becslése

Megbecsülheti egy adott bake díjait, hajtsa végre a megjelenített érték **becsült költség számítási**, ez az időtartam és a kettő többszörösének, amely szerint az óránkénti költség, a helyi pénznemben számított a **VM csomóponttípus** választotta. Az eredmény nem tartalmazza a csomópont idő szükséges a csomópontok szolgáltatásszintje és futtatásához. Például, ha kiválasztja **Standard_F8s_v2** az adott csomóponttípus költsége pedig $ 0,40/óra, amely rendelkezik, és a számítási becsült költség 3 óra és 57 perc, a feladat futtatásához a becsült költség lesz $0,40 * ~ 4 óra = ~ $1.60. A tényleges költségek valószínűleg egy kicsit nagyobb miatt az első lépések a csomópontok hosszabb időt. Az óránkénti csomópont költsége annak a [Azure Batch szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (válassza "számításra optimalizált" vagy "nagy teljesítményű számítás" kategória) lap.

## <a name="Local-bake"></a> A számítógépen a jelenethez os
A saját számítógépen a jelenethez is os. Ez lehet hasznos, ha kísérleteztek Akusztika kisebb jeleneteket és az Azure Batch-fiók létrehozása előtt. Megjegyzés: a Akusztika szimuláció jelenet méretétől függően hosszabb időt is igénybe vehet.

### <a name="minimum-hardware-requirements"></a>Minimális hardverkövetelmények
* Legalább 8 maggal és 32 GB RAM-x86-64 processzor

Tegyük fel, a tesztelés egy 8 mag gép a Intel Xeon E5-1660 @ 3 GHz-es és 32 GB RAM-
* 100 mintákkal kisebb jeleneteket egy durva bake vagy 32 óráig finom bake nagyjából 2 órát is igénybe vehet.
* Egy közepes méretű jelenet 1000 mintákkal egy durva bake vagy vonatkozó finom bake 21 napos körülbelül 20 órát is igénybe vehet.

### <a name="setup-docker"></a>A telepítő Docker
Telepítse és konfigurálja a Docker a számítógépen, amely feldolgozza a szimuláció-
1. Telepítse a [Docker eszközkészlet](https://www.docker.com/products/docker-desktop).
2. Indítsa el a Docker-beállításait, keresse meg a "Speciális" lehetőség, és legalább 8GB RAM-MAL rendelkezik az erőforrások. A több processzort foglalhat le a Docker, annál gyorsabban bake fog befejeződni. ![Képernyőkép a Docker-példabeállítások](media/docker-settings.png)
3. Keresse meg "Megosztott meghajtók", és kapcsolja be a feldolgozáshoz használt meghajtó megosztása.![Képernyőfelvétel: a Docker megosztott meghajtót beállításai](media/docker-shared-drives.png)

### <a name="run-local-bake"></a>Helyi bake futtatása
1. Kattintson a "Előkészítése helyi os" gombra a a **os** lapra, és válasszon ki egy mappát, ahol a bemeneti fájlok és parancsfájlok végrehajtása menti a rendszer. Ezután futtathatja a bake bármely gépen mindaddig, amíg megfelel-e a minimális hardverkövetelményeknek, és a Docker telepítve van a mappa, hogy a gép másolásával.
2. Indítsa el a szimulációt, a "runlocalbake.bat" parancsfájl használatával. Ez a szkript beolvassa a szimuláció feldolgozáshoz szükséges eszközkészlet a projekt Akusztika Docker-rendszerképet, és a a szimuláció elindításához. 
3. A szimuláció befejeztével másolja az eredményül kapott .ace fájl vissza a Unity-projektjét. Ahhoz, hogy a Unity felismeri a bináris fájlként, ".bytes" hozzáfűzése a fájlkiterjesztés (például "Scene1.ace.bytes"). A részletes naplókat a szimuláció vannak tárolva "AcousticsLog.txt." Ha problémákat tapasztal, ossza meg ezt a diagnosztikát segítő fájlt.

## <a name="Data-Files"></a> A bake folyamat által hozzáadott adatfájlok

Nincsenek a bake során létrehozott négy adatfájlokat. Az egyik a szimuláció eredményeit tartalmazza, és a címet tartalmaz. A többi tárolja a Unity-szerkesztő kapcsolatos adatokat.

Szimuláció eredménye:
* **Eszközök/AcousticsData/Akusztika\_[SceneName].ace.bytes**: Ez a futásidejű keresési táblából, és a szimuláció eredményeit és a voxelized akusztikai jelenet elemeket tartalmaz. A helyét, és ez a fájl nevét is módosítható a mezői a **mintavételek** fülre.

Ügyeljen, nem arra, hogy a szimuláció eredmény fájl törlése. Nem állítható helyre, kivéve a jelenet rebaking szerint.

Editor adatfájlok:
* **Assets/Editor/[SceneName]\_AcousticsParameters.asset**: A fájl tárolja az adatokat a mezőkben a Akusztika felhasználói felületén. A hely és a fájl neve nem lehet módosítani.
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox**: Ez a fájl tartalmazza a voxelized Akusztika geometry és anyagi tulajdonságok használatával számítja a **Calculate...**  gombra a mintavételek lapon. A helyét, és ez a fájl nevét is módosítható a mezői a **mintavételek** fülre.
* **Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml**: Ez a fájl tartalmazza a szimuláció paraméterei kiszámítása a **Calculate...**  gombot a **mintavételek** fülre. A helyét, és ez a fájl nevét is módosítható a mezői a **mintavételek** fülre.

## <a name="set-up-the-acoustics-lookup-table"></a>Állítsa be a Akusztika keresési tábla
Húzza át a **projekt Akusztika** prefab a projekt panelről a jelenethez be:

![Képernyőkép a Akusztika prefab a Unity-nél](media/acoustics-prefab.png)

Kattintson a **ProjectAcoustics** játék objektum, és ugorjon a Megtekintő panelen. Adja meg a helyet a bake eredmény (a. A fájl, ACE **eszközök/AcousticsData**) által húzza és törlése a Akusztika Manager parancsfájlba, illetve a szövegbeviteli mező mellett a kör gombra kattintva.

![Képernyőkép a Akusztika Manager prefab a Unity-nél](media/acoustics-manager.png)  

## <a name="next-steps"></a>További lépések
* Fedezze fel a [for Unity vezérlők tervezése](unity-workflow.md)
* Fedezze fel a [projekt Akusztika tervezési alapelvek](design-process.md)

