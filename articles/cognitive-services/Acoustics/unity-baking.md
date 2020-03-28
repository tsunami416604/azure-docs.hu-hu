---
title: Project Akusztika Unity Bake bemutató
titlesuffix: Azure Cognitive Services
description: Ez az oktatóanyag az akusztikai sütést ismerteti a Project Acoustics in Unity segítségével.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72243120"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Project Akusztika Unity Bake bemutató
Ez az oktatóanyag az akusztikai sütést ismerteti a Project Acoustics in Unity használatával.

Szoftverkövetelmények:
* [Unity 2018.2+](https://unity3d.com) Windows vagy MacOS rendszeren
* [A Project Acoustics plug-in integrálva van az Egység projektbe](unity-integration.md) vagy a [Project Acoustics Unity mintatartalomba](unity-quickstart.md)
* *Nem kötelező:* [Azure Batch-fiók](create-azure-account.md) a sütések felgyorsításához a felhőalapú számítástechnika használatával

## <a name="open-the-project-acoustics-bake-window"></a>A Project Acoustics bake ablakának megnyitása
A Unity menüben válassza az **Akusztika parancsot** az **Ablak** menüben.

![Az Ablak menüBen kiemelt Akusztika beállítással ellátott Unity-szerkesztő](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Navigációs háló létrehozása
A Project Acoustics navigációs hálót használ a figyelő mintavételi pontjainak szimulációhoz való helyzéséhez. Használhatja a Unity [navigációs háló munkafolyamatot.](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) Másik 3D-s modellezési csomag gal is megtervezheti saját hálóját.

## <a name="mark-acoustic-scene-objects"></a>Akusztikus jelenetobjektumok megjelölése
A Project Akusztika kétféle jelenetobjektumra támaszkodik a szimulációhoz:
- A szimulációban a hangot tükröző és elzáródó objektumok
- A lejátszó navigációs hálója, amely a hallgató mintavételi pontjait korlátozza a szimulációban

Mindkét objektumtípust az Objektumok lapon jelöli **meg.**

Mivel az objektumok jelölése egyszerűen hozzáadja az *AcousticsGeometry* vagy *AcousticsNavigation* összetevőket az objektumhoz, a [szabványos Unity összetevő munkafolyamatával](https://docs.unity3d.com/Manual/UsingComponents.html) is megjelölheti vagy kiválaszthatja az objektumok jelölését vagy jelölésének kijelölését. Csak hálós renderelőket és terepeket jelölhet meg. A program minden más objektumtípust figyelmen kívül hagy. A jelölőnégyzetek az összes érintett objektumot bejelölik, vagy nem jelölik be.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Akusztikus elzáródás és visszaverődés geometria megjelölése
Nyissa **meg** az **Akusztika** ablak Objektumok lapját. Jelölje meg az *objektumokat akusztikai geometriaként,* ha elkell hagynia, tükröznie vagy el kell fogadnia a hangot. Akusztikai geometria tartalmazhat olyan dolgokat, mint a föld, a falak, a tetők, az ablakok és az ablaküvegek, a szőnyegek és a nagy bútorok. Ezekhez az objektumokhoz tetszőleges szintű összetettségi szintet használhat. Mivel a jelenet voxelized szimuláció előtt, rendkívül részletes csakad, mint a fák, amelyek sok levelek, nem drágább sütni, mint az egyszerűsített tárgyakat.

Ne vegyen fel olyan dolgokat, amelyek nem befolyásolhatják az akusztikát, például a láthatatlan ütközési minta.

Egy objektum átalakítása a mintavételszámítás során (a **Szondák** lapon keresztül) rögzített a sütési eredményekben. Ha a jelenetben lévő megjelölt tárgyakat később áthelyezi, a szondaszámítást és a sütést újra kell végezni.

### <a name="mark-the-navigation-mesh"></a>A navigációs háló megjelölése
Az Egység munkafolyamaton keresztül létrehozott navigációs mintautakat az Akusztika rendszer fogja felvenni. Ha saját saját mintarajzait szeretné használni, jelölje meg őket az **Objektumok** lapon.

### <a name="for-reference-the-objects-tab-parts"></a>Referenciaként: Az Objektumok lap részei
A lap oldalának részei (a leírások után a képen) a következők:

1. A tabulátorkijelölő gombok (az **Objektumok** lap kijelölése). Használja ezeket a gombokat, hogy mozogjon a különböző lépéseket egy akusztika sütni, balról jobbra.
1. Rövid leírás arról, hogy mit tehet ezzel a lapval.
1. A hierarchiaablakhoz rendelkezésre álló szűrők. Ezekkel a beállításokkal szűrheti a hierarchiaablakot a megadott típusú objektumokra, hogy könnyen megjelölhesse őket. Ha még nem jelölt meg semmit az akusztika számára, az utolsó két lehetőség kiválasztása nem mutat semmit. Ezek a beállítások azonban segítenek megtalálni az objektumokat a megjelölésük után.
1. Ha nincs kijelölve objektum, ez a szakasz a jelenetben lévő összes objektum állapotát jeleníti meg.
    * Összesen: Az aktív, nem rejtett objektumok teljes száma.
    * Figyelmen kívül hagyva: Azon objektumok száma, amelyek nem hálómegjelenítők vagy terepek.
    * Háló: A hálómegjelenítő objektumok száma.
    * Terep: A terepobjektumok száma.
    * Geometria: Az **Akusztikageometria**ként megjelölt háló- vagy terepobjektumok száma.
    * Navigáció: Az **Akusztikai navigációként**megjelölt háló- vagy terepobjektumok száma. Ez a szám nem tartalmazza a Unity NavMesh.This number doesn't include the Unity NavMesh.
1. A jelenetben lévő "markable" objektumok teljes száma, amelyek csak hálórenderelők és terepek. A jelölőnégyzetekkel jelölje be (adja hozzá a megfelelő összetevőt) az objektumokat geometriaként vagy navigációként az akusztika számára.
1. Ha semmi sincs kijelölve, ez a megjegyzés arra emlékezteti, hogy szükség esetén jelöljön ki objektumokat jelölésre. A jelenet összes objektumának jelöléséhez bejelölheti az egyik vagy mindkét jelölőnégyzetet.
1. Ha az objektumok ki vannak jelölve, ez a szakasz csak a kijelölt objektumok állapotát jeleníti meg.
1. A kijelölt objektumok teljes száma. A jelölőnégyzetek jelölései vagy törlése csak a kijelölt objektumokat jelöli vagy törli a jelölésből.

Ha nincs kijelölve a jelenetben, az **Objektumok** lap az alábbi képhez hasonlóan néz ki.

![Az Akusztikai objektumok lap, ahol semmi sincs kijelölve](media/objects-tab-no-selection-detail.png)

Ha van valami kijelölve a jelenet vagy a hierarchia ablakban, a lap az alábbi képhez hasonlóan néz ki.

![Az Akusztikai objektumok lap kijelölésekkel](media/objects-tab-selection-detail.png)

Ha egyes objektumok meg vannak jelölve, mások pedig nem, a megfelelő jelölőnégyzetekben a "vegyes" érték jelenik meg, például az alábbi képen.

![Az Akusztikai objektumok lap kiemelt ikonok vegyes választékával](media/mixed-object-selection-detail.png)

Jelölje be a jelölőnégyzetet az összes elem jelöléséhez. Törölje a jelet a kijelölésből az összes objektum jelölésének megszüntetéséhez.

Az objektumok geometriához és navigációhoz egyaránt megjelölhetők.

## <a name="select-acoustic-materials"></a>Válasszon akusztikus anyagokat
Az objektumok megjelölése után válassza az **Anyagok** gombot. Ezután rendeljen hozzá akusztikai anyagokat. A Project Acoustics anyagrendszer az Unity vizuális anyagrendszerhez van kötve. Ahhoz, hogy két tárgy külön akusztikai anyagokkal rendelkezjen, külön vizuális anyagokkal kell rendelkeznie.

Az akusztikai anyag kiválasztása határozza meg az egyes felületekről visszavert hangenergia mennyiségét. Az alapértelmezett akusztikai anyag abszorpciója hasonló az acélhoz. A Project Acoustics a vizuális anyag nevén alapuló anyagokat javasol. Az akusztikai **anyagot egyéni** anyaghoz is hozzárendelheti egy állítható abszorpciós együttható csúszka aktiválásához.

Egy adott anyag visszaverési ideje egy helyiségben fordítottan összefügg annak abszorpciós együtthatójával. A legtöbb anyag abszorpciós értéke 0,01 és 0,20 között van. Az ezen a tartományon kívüli abszorpciós együtthatóval rendelkező anyagok nagyon nedvszívóak.

![A grafikon a reverberation idő és az abszorpciós együttható negatív korrelációját mutatja.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Referenciaként: Az Anyagok lap részei
![Az Akusztikai anyagok lap a Unity-ben](media/materials-tab-detail.png)
1. Az **Anyagok** gomb ezt a lapot jeleníti meg.
2. Rövid leírás arról, hogy mit tehet ezzel a lapval.
3. Ha ez a jelölőnégyzet be van jelölve, csak az **Akusztikageometria** jelöléssel ellátott objektumok által használt anyagok jelennek meg. Ellenkező esetben a jelenetben használt összes anyag megjelenik a listában.
4. Ezekkel a beállításokkal módosíthatja az **Akusztika** oszlop (#6) menüjének beállításainak sorrendjét. Rendezze az akusztikai anyagokat **név** vagy **abszorpció**szerint, alacsonytól magasig.
5. A jelenetben használt anyagok betűrendben rendezett listája. Ha a **Csak megjelölés megjelenítése** jelölőnégyzet be van jelölve (#3), csak **az Akusztikageometria** jelölésű objektumok által használt anyagok jelennek meg. Itt jelöljön ki egy anyagot az adott anyagot használó összes objektum kijelöléséhez a jelenetben.
6. Az akusztikai anyag, amelyhez a jelenet anyaga hozzá van rendelve. Jelöljön ki egy elemet a jelenetanyaghoz rendelt akusztikai anyag módosításához. A menük rendezési sorrendjének módosításához használja az **Akusztika rendezése** beállításokat (#4).
7. A bal oldali oszlopban kiválasztott anyag akusztikai abszorpciós együtthatója (#6). A 0 érték tökéletesen reflektív (felszívódás nélkül), míg az 1 tökéletesen felszívódást jelent (nincs reflexió). Az abszorpciós együttható csak akkor módosítható, ha a kiválasztott anyag **egyéni.**
8. **Egyéni**ként megjelölt anyag esetén a csúszka aktiválódik. Mozgathatja a csúszkát, vagy beírhat egy értéket az abszorpciós együttható hozzárendeléséhez.

## <a name="calculate-and-review-listener-probe-locations"></a>Figyelőmintavételi helyek számítása és áttekintése
Miután hozzárendelte az anyagokat, **váltson** a Mintavételek lapra. **Calculate**

### <a name="what-the-calculate-button-does"></a>Mit csinál a "Számítás" gomb?
A **Számítás** gomb a kiválasztott akusztikai jelenetgeometriával készíti elő a jelenetet a szimulációra:

- A geometriát a jelenetből veszi, és kiszámítja a *voxel térfogatát*. A voxel térfogata egy kötet az egész jelenet áll a kis köbös "voxels." A Voxel méretét a szimuláció gyakorisága határozza meg, amelyet a **szimuláció felbontása** beállítás vezérel. Minden voxel van jelölve a "szabad levegő", vagy tartalmazó jelenet geometria. Ha egy voxel geometriát tartalmaz, a voxel az adott geometriához rendelt anyag abszorpciós együtthatójával lesz címkézve.
- A navigációs mintaasegítségével helyezi el a figyelő mintavételi pontjait. Az algoritmus egyensúlyt teremt a térbeli lefedettség, a szimulációs idő és a fájlméret versengő problémái között. Célja annak biztosítása, hogy a szűk folyosók és a kis terek mindig legyenek lefedettséggel. A tipikus szondapontok száma a kis jelenetek esetében 100-tól néhány ezerig terjed a nagy jeleneteknél.

A jelenet méretétől és a gép sebességétől függően ezek a számítások több percet is igénybe vehetnek.

### <a name="review-voxel-and-probe-placement"></a>Voxel és szonda elhelyezésének áttekintése
Tekintse meg a voxel adatok és a mintavételi pont helyeket is, hogy biztosan készen álljon a jelenet sütésére. A hiányos navigációs háló vagy a hiányzó vagy extra akusztikai geometria általában jól látható az előnézetben. A Voxel és a mintavétel elhelyezésének engedélyezése vagy letiltása a **Gizmos** menü használatával.

![A Minialkalmazások menü egységben](media/gizmos-menu.png)

Az akusztikai geometriát tartalmazó voxelek zöld kockaként jelennek meg. Fedezze fel a jelenetet, és ellenőrizze, hogy minden, ami geometriának kell lennie voxels. A jelenet kamerájának körülbelül 5 méteren belül kell lennie az objektumtól, hogy a voxels megjelenjen.

Ha összehasonlítjuk a durva felbontással létrehozott voxeleket a finom felbontással, látni fogod, hogy a durva voxelek kétszer akkorák.

![A durva voxels előnézet a Unity szerkesztő](media/voxel-cubes-preview.png)

A szimulációs eredmények a figyelő mintavételi pontjai között kerülnek interpolálva futásidőben. Ellenőrizze, hogy vannak-e szondapontok minden olyan hely közelében, ahol a játékos várhatóan megy a jelenetet.

![A szondák előnézete az Unity szerkesztőben](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Vigyázzanak a jelenet átnevezésével
A jelenet neve a jelenetet a mintavételi pont elhelyezését és voxelizációját tároló fájlokhoz kapcsolja. Ha a mintavételi pontok kiszámítása után átnevezi a jelenetet, az anyag-hozzárendelési és elhelyezési adatok elvesznek, és újra kell futtatni őket.

### <a name="for-reference-parts-of-the-probes-tab"></a>Referenciaként: A Szondák lap részei
![Az Acoustics Probes lap az Egységben](media/probes-tab-detail.png)

1. A Szondák gomb **jelenik** meg ezen a lapon.
2. Rövid leírás arról, hogy mit tehet ezen a lapon.
3. Ezekkel a beállításokkal durva vagy finom szimulációs felbontást állíthat be. A durva gyorsabb, de vannak kompromisszumok. A részleteket lásd: [Bake resolution](bake-resolution.md).
4. Itt adható meg, hogy hová kell tenni az akusztikai adatfájlokat. A mappaválasztó eléréséhez kattintson a "**...**" gombra. Az alapértelmezett hely az *Assets/AcousticsData*. Ezen a helyen *szerkesztő* almappa is létrejön. További információ: [A sütési folyamat által hozzáadott adatfájlok](#Data-Files)a cikk későbbi részében.
5. Az itt megadott előtag a jelenet adatfájljainak elnevezésére szolgál. Az alapértelmezett érték a "Acoustics_*[Jelenetneve]*".
6. A szondák kiszámítása után az imént leírt vezérlők le vannak tiltva. A **Törlés** gombra kattintva törölheti a számításokat, és engedélyezheti a vezérlőket, hogy újraszámíthassa az új beállításokkal.
7. Válassza **a Számítás** lehetőséget a jelenet voxelize és a mintavételi pont helyeinek kiszámításához. A számítás helyileg történik a számítógépen. Meg kell tenni, mielőtt csinál egy sütni.

A Project Acoustics ezen verziójában a szondák nem helyezhetők el manuálisan. Használja az automatikus folyamatot a **Mintavételek** lapon.

A durva és a finom felbontással kapcsolatos további információkért lásd: [Sütésfelbontás.](bake-resolution.md)

## <a name="bake-your-scene-by-using-azure-batch"></a>Süsse meg a jelenetet az Azure Batch használatával
A jelenetet a felhőben egy számítási fürtön sütheti az Azure Batch szolgáltatás használatával. A Project Acoustics Unity beépülő modul közvetlenül csatlakozik az Azure Batch-hez, hogy minden egyes sütéshez egy Azure Batch-fürtöt azonnal elkészíthes, kezelhet és bontson le. A **Sütés** lapon adja meg az Azure-hitelesítő adatait, válassza ki a fürtgép típusát és méretét, majd válassza a **Sütés**lehetőséget.

### <a name="for-reference-parts-of-the-bake-tab"></a>Referenciaként: A Sütés lap részei
![Az Akusztika Bake lap unity](media/bake-tab-details.png)

1. A **Sütés** gomb jelenik meg ezen a lapon.
2. Rövid leírás arról, hogy mit tehet ezen az oldalon.
3. Adja meg az Azure-hitelesítő adatait ezekben a mezőkben, miután az Azure-fiók létrehozása. További információ: [Azure Batch-fiók létrehozása.](create-azure-account.md)
4. Az Akusztikai eszközkészlet Docker-képcímke mezője.
5. Megnyitja az Azure Portalt az előfizetések kezeléséhez, a használat figyeléséhez és a számlázási adatok megtekintéséhez.
6. Megadja az Azure Batch számítási csomópont típusát a számításhoz. A csomópont típusát az Azure-adatközpont helyének támogatnia kell. Ha nem biztos benne, **hagyja,** mint Standard_F8s_v2 .
7. A számításhoz használandó csomópontok száma. Ez a szám befolyásolja a sütési időt. Az Azure Batch alapvető foglalása korlátozza. Az alapértelmezett foglalás csak két 8 magos vagy egy 16 magos csomópontot engedélyez, de bővíthető. Az alapvető foglalási korlátozásokról az [Azure Batch-fiók létrehozása](create-azure-account.md)című témakörben talál további információt.
8. Jelölje be ezt a jelölőnégyzetet, ha a számítási készletet [alacsony prioritású csomópontok használatára](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)szeretné konfigurálni. Az alacsony prioritású számítási csomópontok sokkal alacsonyabb költséggel rendelkeznek. De lehet, hogy nem mindig állnak rendelkezésre, vagy lehet, hogy kísértés bármikor.
9. A mintavétel a Mintavétel ek lapon számított nak számítja ki a **jelenetet.** A mintavételek száma határozza meg a felhőben futtatandó szimulációk számát. Nem adhat meg több csomópontot, mint amennyi mintavétel.
10. A feladat felhőbeli futtatásához leigénybe vevő feladat becsült becslése, kivéve a csomópont indítási idejét. Miután a feladat futása megkezdődik, ez a mező becslést jelenít meg arról, hogy mennyi ideig tart az eredmények visszakapása.
11. A szimulációk futtatásához szükséges számítási idő teljes mennyisége. Ez az érték az Azure-ban használt csomópontszámítási idő teljes mennyisége. További információ: [Az Azure bake költségének becslése](#Estimating-bake-cost) a cikk későbbi részében.
12. Ez az üzenet megmutatja, hogy a sütés eredményei hol lesznek mentve, amikor a feladat befejeződik.
13. *(Csak speciális használat:)* Ez a gomb arra kényszeríti az Egységet, hogy felejtse el a beküldött sütést. Ha például az eredményeket egy másik géppel töltötte le, válassza az **Állapot törlése** gombot, hogy elfelejtse ezt a feladatot. Az eredményfájl, ha készen áll, *nem* lesz letöltve. *Ez nem ugyanaz, mint a feladat megszakítása. A feladat, ha fut, továbbra is fut a felhőben.*
14. Válassza ezt a gombot, hogy küldje el a sütni a felhőbe. Afeladat futása közben ez a gomb a **Feladat megszakítása**lesz.
15. Ezzel a gombbal felkészülhet az [akusztikai szimuláció feldolgozására a számítógépen.](#Local-bake)
16. Ez a terület mutatja az állapotát a sütni. Amikor a sütés befejeződött, a "Letöltött" látható.

Az Azure Portalon mindig teljes körű információkat kaphat az aktív feladatokról, a számítási készletekről és a [tárolásról.](https://portal.azure.com)

Afeladat futása közben a **Sütés** gomb címkéje **A feladat megszakítása lesz.** Ezzel a gombbal megszakíthatja a folyamatban lévő feladatot. A rendszer kérni fogja a megerősítést. A feladat megszakítása nem vonható vissza. A lemondáskor nem leszelérhető találat, de továbbra is díjat számítunk fel a felhasznált Azure-beli számítási időért.

Miután elkezdte a sütni, akkor zárja egység. A projekttől, a csomópont típusától és a csomópontok számától függően a felhősütés több órát is igénybe vehet. A sütési feladat állapota a projekt újratöltésekor és az Akusztika ablak megnyitásakor frissül. Ha a feladat befejeződött, a kimeneti fájl letöltődik.

A biztonság érdekében az Azure-hitelesítő adatok a helyi számítógépen tárolódnak, és az Egység szerkesztőjéhez vannak társítva. Csak biztonságos azure-kapcsolat létrehozására szolgálnak.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Futó feladat állapotának megkeresése az Azure Portalon

1. Keresse meg a bake munka azonosítót a **Bake** lapon.

    ![A Unity sütni feladat azonosító kiemelve a Bake lapon](media/unity-job-id.png)  

2. Nyissa meg az [Azure Portalt,](https://portal.azure.com)nyissa meg a sütéshez használt Batch-fiókot, és válassza a **Feladatok lehetőséget.**

    ![A Feladatok hivatkozás az Azure Portalon](media/azure-batch-jobs.png)  

3. Keresse meg a feladatazonosítót a feladatok listájában.

   ![Az Azure Portalon kiemelt bake feladat állapota](media/azure-bake-job-status.png)  

4. Válassza ki a feladatazonosítót a kapcsolódó feladatok állapotának és a feladat általános állapotának megtekintéséhez.

   ![A sütési feladat állapota](media/azure-batch-task-state.png)  


### <a name="estimate-azure-bake-cost"></a><a name="Estimating-bake-cost"></a>Az Azure sütési költségének becslése

A sütés költségének becsléséhez kezdje a **becsült számítási költség** értékkel, amely egy időtartam. Szorozza meg ezt az értéket a kiválasztott **virtuálisgép-csomópont típusának** helyi pénznemében kifejezett óránkénti költségével. Vegye figyelembe, hogy az eredmény nem tartalmazza a csomópontok működéséhez szükséges csomópontidőt.

Tegyük fel például, hogy **a csomóponttípushoz Standard_F8s_v2** választ, amelynek költsége 0,40 0000000000000000000000000000000000000000 Ha a **becsült számítási költség** 3 óra és 57 perc, a feladat futtatásának becsült költsége 0,40 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 A tényleges költség valószínűleg egy kicsit magasabb, mert az extra időt, hogy a csomópontok kezdődött.

Óránkénti csomópontok költségét az [Azure Batch díjszabása.](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (Kategóriaként válassza **a Számítási optimalizált** vagy **nagy teljesítményű számítás** lehetőséget.)

## <a name="bake-your-scene-on-your-pc"></a><a name="Local-bake"></a>Süsse meg a jelenetet a számítógépen
Azt is sütni a jelenetet a saját PC-n. Ez a módszer hasznos lehet a kis jelenetek akusztikai kísérletezéséhez, mielőtt létrehozna egy Azure Batch-fiókot. De vegye figyelembe, hogy a helyi akusztikai szimuláció hosszú időt vehet igénybe a jelenet méretétől függően.

### <a name="minimum-hardware-requirements"></a>Minimális hardverkövetelmények
* X86-64 processzor legalább 8 maggal és 32 GB RAM-mal
* [A Hyper-V engedélyezve van](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) a Docker futtatásához

Például egy 8 magos gépen végzett tesztelésünk során az Intel Xeon E5-1660 @ 3 GHz és 32 GB RAM:
* Egy kis jelenet 100 szondák körülbelül 2 óra alatt egy durva sütni, vagy 32 óra egy finom sütni.
* Egy közepes méretű jelenet 1000 szondával körülbelül 20 órát vett igénybe egy durva sütéshez, vagy 21 napig egy finom sütéshez.

### <a name="set-up-docker"></a>A Docker beállítása
Telepítse és konfigurálja a Docker-t azon a számítógépen, amely feldolgozza a szimulációt:
1. Telepítse a [Docker Desktop](https://www.docker.com/products/docker-desktop)alkalmazást .
2. Nyissa meg a Docker-beállításokat, nyissa meg a **Speciális**lehetőséget, és konfigurálja az erőforrásokat legalább 8 GB RAM-ra. Minél több processzort tud kiosztani a Docker számára, annál gyorsabban fejeződik be a sütés.  
![Minta Docker-beállítások](media/docker-settings.png)
1. Nyissa meg a **Megosztott meghajtók**, és kapcsolja be a feldolgozásra használt meghajtó megosztását.  
![A Docker megosztott meghajtóbeállításai](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>Fuss a helyi sütni
1. Válassza a **Helyi sütés előkészítése** gombot a **Sütés** lapon. Ezután válassza ki azt a mappát, ahhoz, hogy mentse a bemeneti fájlokat és a végrehajtási parancsfájlokat. Ezután futtathatja a sütni bármely gépen, amíg megfelel a minimális hardverkövetelményeknek, és telepítse a Docker-t a mappa másolásával a számítógépre.
2. A szimuláció elindításához futtassa a *runlocalbake.bat* parancsfájlt Windows rendszeren vagy a *runlocalbake.sh* parancsfájlt MacOS rendszeren. Ez a parancsfájl lekéri a Project Acoustics Docker-lemezképet a szimuláció feldolgozásához szükséges eszközkészlettel, és elindítja a szimulációt.
3. A szimuláció befejezése után másolja vissza az eredményül kapott *.ace* fájlt az Unity projektbe. Annak érdekében, hogy a Unity bináris fájlként ismerje fel, fűzz hozzá ".bytes" fájlt a fájlkiterjesztéshez (például "Scene1.ace.bytes"). A szimuláció részletes naplóit az *AcousticsLog.txt* fájl tárolja. Ha bármilyen probléma merül fel, vizsgálja meg ezt a fájlt, hogy segítsen diagnosztizálni a problémát.

## <a name="data-files-added-by-the-bake-process"></a><a name="Data-Files"></a>A sütési folyamat által hozzáadott adatfájlok

A következő négy adatfájl jön létre a sütési folyamat során. Az egyik tartalmazza a szimuláció eredményeit és a hajók a címet. A többiek unity szerkesztővel kapcsolatos adatokat tárolnak.

Szimuláció eredménye:
* *Eszközök/AcousticsData/Acoustics\_[SceneName].ace.bytes*: Ez a fájl a futásidejű kereshető tábla. Ez tartalmazza a szimuláció eredményeit és voxelized akusztikus jelenet elemeket. A fájl nevét és helyét a **Mintavételek lapon módosíthatja.**

   *Ügyeljen arra, hogy ne törölje a szimuláció eredményfájlját. Nem hasznosítható, kivéve, ha újrasütöd a helyszínt.*

Szerkesztő adatfájlok:
* *Eszközök/Szerkesztő/[SceneName]\_AcousticsParameters.asset*: Ez a fájl tárolja az Akusztika felhasználói felületének mezőiben megadott adatokat. A fájl neve és helye nem módosítható.
* *Eszközök/AcousticsData/Editor/Acoustics_[SceneName].vox:* Ez a fájl a voxelizált akusztika geometriáját és a **Mintavételek** lap **Számítás** gombjának kiválasztásával kiszámított anyagtulajdonságokat tárolja. A fájl nevét és helyét a **Mintavételek lapon módosíthatja.**
* *Eszközök/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml*: Ez a fájl a **Számítás**program ra vonatkozó szimulációs paramétereket tárolja. A fájl nevét és helyét a **Mintavételek lapon módosíthatja.**

## <a name="set-up-the-acoustics-lookup-table"></a>Az akusztikai keresd a táblát
Húzza a **Project Acoustics** előregyártott projektelemet a projektpanelről a jelenetbe:

![Az Akusztika előre gyártott Unity](media/acoustics-prefab.png)

Jelölje ki a **ProjectAcoustics** játékobjektumot, és lépjen a felügyelőpanelre. Adja meg a sütési eredmény helyét (az .ace fájl, *az Eszközök/AcousticsData mezőben):* Húzza azt az Acoustics Manager parancsfájlba, vagy jelölje ki a szövegmező melletti körgombot.

![Az Acoustics Manager előre gyártott egységben](media/acoustics-manager.png)

## <a name="next-steps"></a>További lépések
* Fedezze fel a [Unity tervezési vezérlőket.](unity-workflow.md)
* Fedezze fel [a Project Akusztika tervezési koncepcióit.](design-process.md)
