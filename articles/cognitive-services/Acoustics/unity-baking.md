---
title: Projekt akusztikai egység sütni oktatóanyag
titlesuffix: Azure Cognitive Services
description: Ez az oktatóanyag leírja az akusztikai és a projekt akusztikai funkcióit az Unity-ben.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 310decf8053ea16ba46250ba3aabe81c9c254e5e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243120"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Projekt akusztikai egység sütni oktatóanyag
Ez az oktatóanyag leírja az akusztikai sütést a Project Acoustics használatával az egységben.

A szoftverre vonatkozó követelmények:
* [Unity 2018.2 +](https://unity3d.com) Windows vagy MacOS rendszerhez
* A [projekt akusztikai beépülő modulja integrálva van az Ön Unity-projektjeibe](unity-integration.md) vagy a [projekt akusztikai egysége minta tartalmába](unity-quickstart.md)
* Nem *kötelező:* Egy [Azure batch-fiók](create-azure-account.md) , amely felgyorsítja a sütni-ket a felhő-számítástechnika használatával

## <a name="open-the-project-acoustics-bake-window"></a>A Project akusztikai sütni ablak megnyitása
Az egység területen válassza az **akusztika** elemet az **ablak** menüből.

![Az egység-szerkesztő az ablak menüjében Kiemelt akusztikai lehetőséggel](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Navigációs háló létrehozása
A Project Acoustics egy navigációs hálót használ a figyelő mintavételi pontjainak szimulációhoz való elhelyezéséhez. Használhatja az Unity [navigációs háló munkafolyamatot](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html). Másik 3D modellező csomagot is használhat a saját rácsvonalának megtervezéséhez.

## <a name="mark-acoustic-scene-objects"></a>Akusztikai jelenet objektumainak megjelölése
A Project Acoustics a következő két típusú jelenet-objektumra támaszkodik a szimulációhoz:
- A szimulációban a occlude és a hangot tükröző objektumok
- A Media Player navigációs rácsvonala, amely a figyelő mintavételi pontjait korlátozza a szimulációban

Mindkét objektumtípus a **Objects (objektumok** ) lapon van megjelölve.

Mivel a jelölési objektumok egyszerűen hozzáadja a *AcousticsGeometry* vagy a *AcousticsNavigation* összetevőt az objektumhoz, a [standard Unity összetevő-munkafolyamattal](https://docs.unity3d.com/Manual/UsingComponents.html) megadhatja vagy megjelölheti az objektumokat. A rácsvonalak és a terepek csak megjelölve adhatók meg. Az összes többi objektumtípus figyelmen kívül lesz hagyva. A jelölőnégyzetek az összes érintett objektumot megjelölik vagy megjelölik.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Az akusztikai elzáródás és a reflexiós geometria megjelölése
Nyissa meg az **akusztika** ablak **objektumok** lapját. Az objektumok megjelölése *akusztikai geometriaként* , ha occlude, tükrözni vagy elnyelni kívánja a hangot. Az akusztikai geometria olyan dolgokat tartalmazhat, mint például a felszín, a fal, a tetők, a Windows és az ablakos üveg, a szőnyegek és a nagyméretű bútorok. Ezekhez az objektumokhoz tetszőleges bonyolultsági szintű komplexitást használhat. Mivel a jelenet a szimuláció előtt voxelized, a nagy részletességű rácsvonalak, például a sok levélből álló fák nem sokkal drágábbak, mint az egyszerűsített objektumok.

Ne tartalmazzon olyan dolgokat, amelyek nem érintik az akusztikai tényezőket, például a láthatatlan ütközési hálókat.

Egy objektumnak a mintavételi számítás során történő átalakítása (a **mintavételek lapon)** a sütni eredményekben van rögzítve. Ha a jelenetben megjelölt objektumokat később helyezi át, a mintavételi számítást és a sütést újra kell végezni.

### <a name="mark-the-navigation-mesh"></a>A navigációs háló megjelölése
A Unity-munkafolyamaton keresztül létrehozott navigációs rácsvonalak az akusztikai rendszer által lesznek kiválasztva. Ha saját rácsvonalat szeretne használni, az **objektumok** lapról jelölheti meg őket.

### <a name="for-reference-the-objects-tab-parts"></a>Hivatkozás: az objektumok lap részei
A lap részei (a leírások után láthatók) a következők:

1. A TAB kiválasztási gombok (az **objektumok** lapon kiválasztva). Ezekkel a gombokkal mozoghat egy akusztikai sütni különböző lépésein, balról jobbra.
1. Egy rövid leírás arról, hogy mit tehet a lap használatával.
1. A hierarchia ablakához elérhető szűrők. Ezekkel a beállításokkal szűrheti a hierarchia ablakát a megadott típusú objektumokra, így egyszerűen megjelölheti őket. Ha még nem jelölt meg semmit az akusztika számára, akkor az utolsó két lehetőség kiválasztásával semmi sem jelenik meg. Ezek a beállítások azonban segítenek a megjelölt objektumok megkeresésében.
1. Ha nincs kiválasztva objektum, ez a szakasz a jelenet összes objektumának állapotát jeleníti meg.
    * Összesen: az aktív, nem rejtett objektumok teljes száma.
    * Figyelmen kívül hagyva: a megjelenítőket és a terepeket nem háló objektumok száma.
    * Mesh: a háló renderelő objektumainak száma.
    * Terep: a terepi objektumok száma.
    * Geometria: az **akusztikai geometriaként**megjelölt rácsvonalak vagy domborzati objektumok száma.
    * Navigáció: **akusztikai navigációként**megjelölt rácsvonalak vagy domborzati objektumok száma. Ez a szám nem tartalmazza az Unity NavMesh.
1. A jelenetben szereplő "megjelölésű" objektumok teljes száma, amelyek csak rácsvonalak és terepek. A jelölőnégyzetek segítségével megadhatja, hogy a megfelelő összetevőt geometriaként vagy navigációként adja-e meg az akusztikai objektumok számára.
1. Ha semmi sincs kiválasztva, ez a megjegyzés arra emlékeztet, hogy kijelöli az objektumokat, ha szükséges. Egy vagy mindkét jelölőnégyzet bejelölésével megadhatja az összes objektumot a jelenetben.
1. Az objektumok kijelölésekor ez a szakasz csak a kiválasztott objektumok állapotát jeleníti meg.
1. A "megjelölhető" kijelölt objektumok teljes száma. Jelölje be a jelölőnégyzetet, vagy törölje a jelölést a kijelölt objektumok kijelöléséhez vagy törléséhez.

Ha a jelenetben semmi sincs kiválasztva, az **objektumok** lap az alábbi képhez hasonlóan néz ki.

![Az akusztikai objektumok lap nincs kiválasztva](media/objects-tab-no-selection-detail.png)

Ha a jelenet vagy a hierarchia ablakban van kiválasztva, a lap a következő képhez hasonlóan néz ki.

![Az akusztikai objektumok lap kijelölések](media/objects-tab-selection-detail.png)

Ha egyes objektumok meg vannak jelölve, és néhány nem, a megfelelő jelölőnégyzetek a "vegyes" értéket jelenítik meg, például az alábbi ábrát.

![Az akusztikai objektumok lap Kiemelt ikonjainak vegyes kiválasztása](media/mixed-object-selection-detail.png)

Jelölje be a jelölőnégyzetet az összes elem megjelöléséhez. Törölje a jelölést az összes objektum kijelölésének megjelöléséhez.

Az objektumok a geometriához és a navigáláshoz egyaránt megadhatók.

## <a name="select-acoustic-materials"></a>Akusztikai anyagok kiválasztása
Az objektumok jelölése után kattintson az **anyagok** gombra. Ezután rendeljen hozzá akusztikai anyagokat. A Project akusztikai anyagok rendszere a Unity vizuális anyagok rendszeréhez van kötve. Ahhoz, hogy két objektumhoz külön akusztikai anyagok legyenek, külön vizuális anyagokat kell tartalmazniuk.

Az akusztikai anyag kiválasztása meghatározza, hogy az egyes felületek milyen mennyiségű hangerőt tükröznek. Az alapértelmezett akusztikai anyag az acélhoz hasonló abszorpciós. A Project akusztika a vizuális anyag neve alapján mutatja be az anyagokat. Azt is megteheti, hogy az akusztikai anyagok **Egyéni** anyagát hozzárendeli az egy állítható abszorpciós-együttható csúszka aktiválásához.

A helyiségben lévő adott anyag visszaverődési ideje fordítottan összefügg a hozzá tartozó abszorpciós együtthatóval. A legtöbb anyag 0,01 és 0,20 közötti abszorpciós értékeket tartalmaz. A tartományon kívüli abszorpciós együtthatóval rendelkező anyagok nagyon nedvszívóak.

![A gráf a visszaverődési idő negatív korrelációját és az abszorpciós együtthatót mutatja.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Hivatkozás: az anyagok lap részei
![Az egység akusztikai anyagok lapja](media/materials-tab-detail.png)
1. Az **anyagok** gomb megjeleníti ezt a lapot.
2. Egy rövid leírás arról, hogy mit tehet a lap használatával.
3. Ha bejelöli ezt a jelölőnégyzetet, csak az **akusztikai geometriának** jelölt objektumok által használt anyagok szerepelnek a listában. Ellenkező esetben a jelenetben használt összes anyag megjelenik.
4. Ezekkel a beállításokkal módosíthatja a beállítások sorrendjét az **akusztikai** oszlop (#6) menüjében. Az akusztikai anyagokat **név** vagy **Absorptivity**szerint rendezheti alacsonyról magasra.
5. A jelenetben használt anyagok betűrendbe rendezett listája. Ha a **csak** a kijelöltek megjelenítése jelölőnégyzet be van jelölve (#3), akkor csak az " **akusztikai geometria** " jelölésű objektumok által használt anyagok jelennek meg. Válasszon ki egy anyagot az adott anyagot használó jelenet összes objektumának kiválasztásához.
6. Az akusztikai anyag, amelyhez a jelenet anyaga hozzá lett rendelve. Válassza ki bármelyik elemet a jelenet anyagához rendelt akusztikai anyagok módosításához. A menük rendezési sorrendjének módosításához használja az **akusztikai elemek rendezése lehetőségek alapján** (#4).
7. A bal oldali oszlopban kiválasztott anyag akusztikus abszorpciós együtthatója (#6). A 0 érték azt jelenti, hogy tökéletesen tükröző (nem abszorpciós), míg az 1 módszer tökéletesen nedvszívó (nincs reflexió). Az abszorpciós együttható csak akkor módosítható, ha a kiválasztott anyag **Egyéni.**
8. Az **egyéniként**megjelölt anyagok esetében a csúszka aktiválva lesz. Áthelyezheti a csúszkát, vagy beírhat egy értéket egy abszorpciós együttható kiosztásához.

## <a name="calculate-and-review-listener-probe-locations"></a>Figyelő mintavételi helyeinek kiszámítása és áttekintése
Az anyagok hozzárendelését követően váltson a mintavételek **lapra.** válassza a **számítás** lehetőséget a figyelő mintavételi pontjainak szimulációhoz való elhelyezéséhez.

### <a name="what-the-calculate-button-does"></a>A "számítás" gomb
A **számítás** gomb a kiválasztott akusztikus jelenet geometriáját használja a jelenet szimulációhoz való előkészítéséhez:

- A geometria a jelenet rácsvonalait veszi át, és kiszámítja a *Voxel kötetet*. A Voxel kötet a teljes jelenetből álló kötet, amely kis köbös "voxels". A Voxel méretét a szimuláció gyakorisága határozza meg, amelyet a **szimulációs megoldás** beállítása szabályoz. Minden Voxel "nyitott levegőként" van megjelölve, vagy jelenet geometriát tartalmaz. Ha egy Voxel geometriát tartalmaz, a Voxel címkéje az adott geometriához rendelt anyag abszorpciós együtthatóját jelöli.
- A navigációs rácsvonalak használatával helyezi el a figyelő mintavételi pontjait. Az algoritmus egyensúlyt teremt a térbeli lefedettség és a szimulációs idő és fájlméret versengő problémái között. Célja annak biztosítása, hogy a szűk folyosók és a kis területek mindig rendelkezzenek lefedettséggel. A tipikus mintavételi pontok a kis méretű jeleneteknél több ezer 100-es tartományba esnek.

A jelenet méretétől és a gép sebességétől függően ezek a számítások több percet is igénybe vehetnek.

### <a name="review-voxel-and-probe-placement"></a>Voxel és mintavételi elhelyezés áttekintése
Tekintse meg mind a Voxel, mind a mintavételi pontok helyét, és győződjön meg arról, hogy készen áll a jelenet megadására. A nem teljes navigációs rácsvonalak vagy a hiányzó vagy a felesleges akusztikai geometria általában könnyen látható az előzetes verzióban. Engedélyezheti vagy letilthatja a Voxel és a mintavételi elhelyezést a **minialkalmazások** menüjének használatával.

![Az Unity minialkalmazások menüje](media/gizmos-menu.png)

Az akusztikus geometriát tartalmazó Voxels zöld kockákként jelennek meg. Fedezze fel a jelenetét, és győződjön meg róla, hogy minden geometriának voxels kell lennie. A jelenet kamerájának kb. 5 méteren belül kell lennie a voxels megjelenítendő objektumnak.

Ha összehasonlítja a durva felbontással létrehozott voxels, és a probléma megoldását, akkor láthatja, hogy a durva voxels kétszer is nagy.

![A durva voxels előzetes verziója az Unity Editorban](media/voxel-cubes-preview.png)

A Szimulációs eredmények a figyelő mintavételi pontjának helyei között, futásidőben vannak interpolált. Győződjön meg arról, hogy vannak-e mintavételi pontok minden olyan helyen, ahol a játékosnak elvárt a jelenet.

![A mintavétel előzetes verziója az Unity Editorban](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Ügyeljen a jelenet-Átnevezés lehetőségre
A jelenet neve a jelenet a mintavételi pont elhelyezését és a voxelization tároló fájlokhoz való összekapcsolására szolgál. Ha átnevezi a jelenetet a mintavételi pontok kiszámítását követően, az anyag-hozzárendelés és az elhelyezési adatok elvesznek, és újra kell futtatni.

### <a name="for-reference-parts-of-the-probes-tab"></a>Hivatkozás: a mintavételek lap részei
![Az egység akusztikai mintavételek lapja](media/probes-tab-detail.png)

1. A **mintavételek gomb megjeleníti ezt a lapot** .
2. A lapon elvégezhető műveletek rövid leírása.
3. Ezekkel a beállításokkal durva vagy részletes szimulációs megoldást állíthat be. A durva érték gyorsabb, de vannak kompromisszumok. Részletekért lásd a [sütni-feloldást](bake-resolution.md)ismertető témakört.
4. Megadja, hogy hová kell helyeznie az akusztikai adatfájlokat. Kattintson a " **...** " gombra a mappa-választó eléréséhez. Az alapértelmezett hely az *assets/AcousticsData*. Ezen a helyen a *szerkesztő* almappája is létrejön. További információ: [a sütni folyamat által hozzáadott adatfájlok](#Data-Files), a cikk későbbi részében.
5. Az itt megadott előtag a jelenet adatfájljainak elnevezésére szolgál. Az alapértelmezett érték a "Acoustics_ *[jelenet neve]* ".
6. A mintavételek kiszámítása után az imént leírt vezérlők le vannak tiltva. A **Clear (Törlés** ) gombra kattintva törölheti a számításokat, és engedélyezheti a vezérlőket, így új beállításokkal újraszámíthatja azokat.
7. Válassza a **számítás** lehetőséget a jelenet voxelize és a mintavételi pontok helyének kiszámításához. A számítás helyben történik a gépen. A sütni végrehajtása előtt el kell végezni.

A Project akusztika ezen verziójában a mintavételek nem helyezhetők manuálisan. Használja az automatikus **folyamatot a mintavételek lapon.**

A durva és a részletes megoldással kapcsolatos további információkért lásd a [sütni-feloldást](bake-resolution.md)ismertető témakört.

## <a name="bake-your-scene-by-using-azure-batch"></a>A jelenet Azure Batch használatával való sütni
A felhőben lévő számítási fürtön a Azure Batch szolgáltatás használatával sütni a jelenet. A Project akusztikai egység beépülő modulja közvetlenül kapcsolódik a Azure Batchhoz az egyes sütni Azure Batch-fürtök létrehozásához, kezeléséhez és lebontásához. A **sütni** lapon adja meg az Azure-beli hitelesítő adatait, válassza ki a fürt típusát és méretét, majd válassza a **sütni**lehetőséget.

### <a name="for-reference-parts-of-the-bake-tab"></a>Hivatkozás: a sütni lap részei
![Az egység akusztikai sütni lapja](media/bake-tab-details.png)

1. A **sütni** gomb megjeleníti ezt a lapot.
2. Az ezen a lapon elvégezhető műveletek rövid leírása.
3. Az Azure-fiók létrehozása után adja meg az Azure-beli hitelesítő adatait ezekben a mezőkben. További információ: [create a Azure batch Account](create-azure-account.md).
4. A Docker-rendszerkép címkéje mező az akusztikai eszközkészlethez.
5. Megnyitja a Azure Portal az előfizetések kezeléséhez, a használat figyeléséhez és a számlázási adatok megtekintéséhez.
6. Meghatározza a számításhoz használandó Azure Batch számítási csomópont típusát. Az Azure-adatközpont helyének támogatnia kell a csomópont típusát. Ha nem biztos a dolgában, hagyja **Standard_F8s_v2**.
7. A számításhoz használandó csomópontok száma. Ez a szám a Bake-időt befolyásolja. Ezt a Azure Batch Core foglalás korlátozza. Az alapértelmezett foglalás csak két 8 magos csomópontot vagy 1 16-Core csomópontot engedélyez, de kibontható. További információ az alapszintű foglalási megkötésekről: [Azure batch fiók létrehozása](create-azure-account.md).
8. Jelölje be ezt a jelölőnégyzetet, ha a számítási készletet [alacsony prioritású csomópontok](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)használatára szeretné konfigurálni. Az alacsony prioritású számítási csomópontok jóval alacsonyabb költségeket jelentenek. Előfordulhat azonban, hogy nem mindig lesznek elérhetők, vagy bármikor előzik.
9. A jelenet mintavételi **száma a mintavételek lapon számítva** . A mintavételek száma határozza meg a felhőben futtatandó szimulációk számát. A mintavételnél több csomópontot nem adhat meg.
10. A műveletnek a felhőben való futtatásához szükséges idő becslése, a csomópont indítási idejének kizárásával. A művelet futásának megkezdése után ebben a mezőben egy becslés jelenik meg, hogy mennyi ideig tart vissza az eredmények.
11. A szimulációk futtatásához szükséges számítási idő teljes mennyisége. Ez az érték az Azure-ban használni kívánt csomópont-számítási idő teljes mennyisége. További információkért lásd a cikk későbbi, az Azure Bake szolgáltatással kapcsolatos [becsült költségeit](#Estimating-bake-cost) .
12. Az üzenetből megtudhatja, hogy a rendszer hová menti a sütni eredményeit a feladatok befejezésekor.
13. *(Csak speciális használat esetén:)* ez a gomb a beküldött sütni felejtse el az egységet. Ha például egy másik gép használatával töltötte le az eredményeket, az **állapot törlése** gomb kiválasztásával elfelejtheti a feladatot. Ha elkészült, a rendszer *nem* tölti le az eredmény-fájlt. *Ez nem ugyanaz, mint a feladat megszakítása. Ha a fut, a továbbra is fut a felhőben.*
14. Ennek a gombnak a kiválasztásával elküldheti a sütni a felhőbe. A feladat futása közben ez a gomb a **feladat megszakítása**lesz.
15. Ennek a gombnak a kiválasztásával előkészítheti az [akusztikai szimulációk](#Local-bake)feldolgozását a számítógépen.
16. Ez a terület a sütni állapotát mutatja. Ha a sütni elkészült, a "letöltött" üzenet jelenik meg.

Az aktív feladatokról, a számítási készletekről és a [Azure Portal](https://portal.azure.com)a tárterületről mindig teljes körű információkhoz juthat.

A feladat futása közben a **sütni** gomb felirata a **feladat megszakítására**változik. Ezzel a gombbal megszakíthatja a folyamatban lévő feladatot. A rendszer kérni fogja a megerősítést. A feladat megszakítása nem vonható vissza. Ha megszakítja a megszakítást, nem lesz elérhető eredmény, de a rendszer továbbra is felszámítja az összes felhasznált Azure számítási időt.

A sütni elindítása után lezárhatja az egységet. A Felhőbeli sütni a projekttől, a csomópont típusától és a csomópontok számától függően több órát is igénybe vehet. A sütni-feladatok állapota a projekt újratöltése és a akusztikai ablak megnyitása után frissül. Ha a feladatot elvégezte, a rendszer letölti a kimeneti fájlt.

A biztonság érdekében az Azure-beli hitelesítő adatok tárolása a helyi gépen történik, és az Ön Unity szerkesztőjéhez van társítva. Csak az Azure-hoz való biztonságos kapcsolat létesítésére szolgálnak.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Futó feladatok állapotának megkeresése a Azure Portalon

1. Keresse meg a Bake-feladatok AZONOSÍTÓját a **sütni** lapon.

    ![A sütni lapon Kiemelt Unity Bake-feladat azonosítója](media/unity-job-id.png)  

2. Nyissa meg a [Azure Portalt](https://portal.azure.com), lépjen a Bake szolgáltatáshoz használt batch-fiókra, és válassza a **feladatok**lehetőséget.

    ![A feladatok hivatkozása a Azure Portal](media/azure-batch-jobs.png)  

3. Keresse meg a feladat AZONOSÍTÓját a feladatok listájában.

   ![A sütni feladat állapota kiemelve a Azure Portalban](media/azure-bake-job-status.png)  

4. Válassza ki a feladat AZONOSÍTÓját a kapcsolódó feladatok állapotának és a feladat általános állapotának megtekintéséhez.

   ![A sütni feladat állapota](media/azure-batch-task-state.png)  


### <a name="Estimating-bake-cost"></a>Az Azure sütni-díj becslése

Ha szeretné megbecsülni, hogy a sütni Milyen költségekkel jár, kezdje a **becsült számítási költségek** értékével, amely egy időtartam. Szorozza meg ezt az értéket a helyi pénznemben megadott, a kiválasztott virtuálisgép- **csomópont** óradíja alapján. Vegye figyelembe, hogy az eredmény nem tartalmazhatja a csomópontok üzembe helyezéséhez és futtatásához szükséges csomóponti időt.

Tegyük fel például, hogy kijelöli a csomópont típusát **Standard_F8s_v2** , amelynek a díja $0.40/HR. Ha a **becsült számítási költségek** 3 óra és 57 perc, a feladatok futtatásának becsült díja a következő: $0,40 * ~ 4 óra = ~ $1,60. A tényleges költségeket valószínűleg egy kicsit magasabbra kell állítani, mert a csomópontok megkezdésének ideje hosszabb időt vesz igénybe.

Az óránkénti csomópontok költségének megkeresése [Azure batch díjszabással](https://azure.microsoft.com/pricing/details/virtual-machines/linux). (Válassza ki a **számításhoz optimalizált** vagy **nagy teljesítményű számítást** kategóriaként.)

## <a name="Local-bake"></a>A jelenet kisütni a számítógépen
Saját SZÁMÍTÓGÉPén is megadhatja saját jelenetét. Ez a módszer hasznos lehet a kis méretű jelenetek akusztikai kipróbálásához Azure Batch fiók létrehozása előtt. Vegye figyelembe azonban, hogy a helyi akusztika szimulációja hosszú időt vehet igénybe a jelenet méretétől függően.

### <a name="minimum-hardware-requirements"></a>Minimális hardverkövetelmények
* X86-64 processzor legalább 8 maggal és 32 GB RAM-mal
* A [Hyper-V engedélyezve van](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) a Docker futtatásához

Például a tesztelés egy 8 magos gépen, Intel Xeon E5-1660 @ 3 GHz és 32 GB RAM:
* Az 100-es mintavételi kis jelenet körülbelül 2 órát vett igénybe a durva sütni vagy a 32 órán át a részletes sütni.
* Az 1 000-es mintavételt tartalmazó közepes méretű jelenet körülbelül 20 órát vett igénybe egy durva sütjük vagy 21 nap után.

### <a name="set-up-docker"></a>A Docker beállítása
Telepítse és konfigurálja a Docker-t azon a számítógépen, amely feldolgozza a szimulációt:
1. Telepítse a [Docker Desktopot](https://www.docker.com/products/docker-desktop).
2. Nyissa meg a Docker-beállításokat, lépjen a **speciális**elemre, és konfigurálja az erőforrásokat legalább 8 GB RAM-ra. Minél több CPU-t tud lefoglalni a Docker számára, annál gyorsabban fog megjelenni a sütni.  
![minta Docker-beállítások](media/docker-settings.png)
1. Nyissa meg a **megosztott meghajtókat**, és kapcsolja be a megosztást a feldolgozásra használt meghajtón.  
![Docker megosztott meghajtó lehetőségei](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>A helyi sütni futtatása
1. Válassza a **helyi sütés előkészítése** gombot a **sütni** lapon. Ezután válassza ki a mappa helyét a bemeneti fájlok és a végrehajtási parancsfájlok mentéséhez. Ezután bármilyen gépen futtathatja a sütni-t, ha megfelel a minimális hardverkövetelmények követelményeinek, és telepíti a Docker-t úgy, hogy az adott gépre másolja a mappát.
2. A szimuláció elindításához futtassa a *runlocalbake. bat* parancsfájlt a Windows rendszeren vagy a *Runlocalbake.sh* parancsfájlt MacOS rendszeren. Ez a szkript lekéri a projekt akusztikai Docker-rendszerképét a szimulációs feldolgozáshoz szükséges eszközkészlettel, és elindítja a szimulációt.
3. A szimuláció befejezése után másolja vissza az eredményül kapott *. ACE* fájlt az Unity-projektbe. Győződjön meg arról, hogy az egység bináris fájlként ismeri fel a (z) ". Bytes" fájlt a fájlkiterjesztés (például "Scene1. ACE. Bytes"). A szimuláció részletes naplóit a *AcousticsLog. txt fájlban* tárolja a rendszer. Ha bármilyen problémába ütközik, vizsgálja meg ezt a fájlt a probléma diagnosztizálásához.

## <a name="Data-Files"></a>A sütni folyamat által hozzáadott adatfájlok

A következő négy adatfájl jön létre a sütni folyamat során. Az egyik tartalmazza a szimuláció eredményeit, és a címmel ellátott hajókat. A többiek az Unity Editor kapcsolódó adatait tárolják.

Szimulációs eredmény:
* *Eszközök/AcousticsData/akusztika\_[SceneName]. ACE. Bytes*: Ez a fájl a futásidejű keresési tábla. A szimuláció eredményeit és a voxelized akusztikai jelenet elemeit tartalmazza. A fájl neve és **helye a mintavételek lapon** módosítható.

   *Ügyeljen arra, hogy ne törölje a szimulációs eredményhalmaz fájlját. Nem lehet helyreállítani, kivéve a jelenet kisütését.*

Szerkesztői adatfájlok:
* *Eszközök/szerkesztő/[SceneName]\_AcousticsParameters. Asset*: Ez a fájl az akusztikai felhasználói felület mezőiben megadott adatok tárolására szolgál. A fájl neve és helye nem módosítható.
* *Assets/AcousticsData/Editor/Acoustics_ [SceneName]. Vox*: Ez a fájl tárolja a voxelized akusztikai geometriáját és a kiszámított anyag-tulajdonságokat, Amikor kiválasztja a számítás **gombot a mintavételek lapon.** A fájl neve és **helye a mintavételek lapon** módosítható.
* *Assets/AcousticsData/Editor/akusztika\_[SceneName]\_config. XML*: Ez a fájl a **számítás**lehetőség kiválasztásakor kiszámított szimulációs paramétereket tárolja. A fájl neve és **helye a mintavételek lapon** módosítható.

## <a name="set-up-the-acoustics-lookup-table"></a>Az akusztikai keresési táblázat beállítása
Húzza a **projekt akusztikai** paneljét a projekt panelről a színtérbe:

![Az akusztikai panel az egységben](media/acoustics-prefab.png)

Válassza ki a **ProjectAcoustics** , majd nyissa meg az ellenőr panelt. Adja meg a sütni eredmény helyét (az. ACE-fájl, az *eszközök/AcousticsData*): húzza az akusztikus kezelő parancsfájlba, vagy kattintson a szövegmező melletti kör gombra.

![Az Acoustics Manager előregyártott egysége](media/acoustics-manager.png)

## <a name="next-steps"></a>Következő lépések
* Ismerje meg az [Unity design vezérlőit](unity-workflow.md).
* Ismerkedjen meg a [projekt akusztikai kialakításával kapcsolatos fogalmakkal](design-process.md).
