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
ms.openlocfilehash: e26df58de08d0941b5e3165852ed0b26f8890f66
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854929"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Projekt akusztikai egység sütni oktatóanyag
Ez az oktatóanyag leírja az akusztikai és a projekt akusztikai funkcióit az Unity-ben.

A szoftverre vonatkozó követelmények:
* [Unity 2018.2 +](https://unity3d.com) Windowshoz
* [Projekt akusztikai beépülő modul integrálva a Unity-projektbe](unity-integration.md) vagy a [projekt akusztikai egysége minta tartalma](unity-quickstart.md)
* Nem kötelező: Egy [Azure batch-fiók](create-azure-account.md) , amellyel felgyorsíthatja a sütni a felhő-számítástechnika használatával

## <a name="open-the-project-acoustics-bake-window"></a>A Project akusztikai sütni ablak megnyitása
Válassza az **ablak > akusztika** elemet az egység menüben:

![Képernyőfelvétel az Unity Editor és a akusztikai ablak menüpont kiválasztásával](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Navigációs háló létrehozása
A Project Acoustics egy navigációs hálót használ a figyelő mintavételi pontjainak szimulációhoz való elhelyezéséhez. Használhatja az egység [navigációs rácsvonal](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)munkafolyamatát, vagy használhat egy másik 3D modellező csomagot a saját rácsvonalának megtervezéséhez. 

## <a name="mark-acoustic-scene-objects"></a>Akusztikai jelenet objektumainak megjelölése
A Project Acoustics a szimulációk két különböző típusú objektumára támaszkodik: azok az objektumok, amelyek tükrözik és occlude a hangerőt a szimulációban, valamint a lejátszó navigációs rácsvonalát, amely korlátozza a figyelő mintavételi pontjait a szimulációban. Mindkét objektumtípus a Objects ( **objektumok** ) lapon van megjelölve. 

Mivel a jelölési objektumok egyszerűen hozzáadja a **AcousticsGeometry** vagy a **AcousticsNavigation** összetevőt az objektumhoz, a [standard Unity összetevő](https://docs.unity3d.com/Manual/UsingComponents.html) -munkafolyamattal megadhatja vagy megjelölheti az objektumokat. Csak rácsvonal-leképező és terep lehet megjelölve. Az összes többi objektumtípus figyelmen kívül lesz hagyva. A jelölőnégyzetek az összes érintett objektumot megjelölik vagy törlik.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Az akusztikai elzáródás és a reflexiós geometria megjelölése
Nyissa meg az **akusztika** ablak **objektumok** lapját. Az objektumok megjelölése **akusztikai geometriaként** , ha occlude, tükrözni vagy elnyelni kívánja a hangot. Az akusztikai geometria olyan dolgokat tartalmazhat, mint például a felszín, a fal, a tetők, a Windows & ablakok üvege, a szőnyegek és a nagyméretű bútorok. Ezekhez az objektumokhoz tetszőleges bonyolultsági szintű komplexitást használhat. Mivel a jelenet a szimuláció előtt voxelized, a nagyon részletes rácsvonalak, például a sok kis mennyiségű fát tartalmazó fák nem drágábbak, mint az egyszerűsített objektumok.

Ne tartalmazzon olyan dolgokat, amelyek nem érintik az akusztikai tényezőket, például a láthatatlan ütközési hálókat.

Egy objektumnak a mintavételi számítás időpontjában történő átalakítása (az alábbi mintavételek lapon) a sütni eredményekben van rögzítve. A jelenetben megjelölt objektumok bármelyikének áthelyezéséhez újra kell végezni a mintavétel számítását és a jelenet kisütését.

### <a name="mark-the-navigation-mesh"></a>A navigációs háló megjelölése
Az egység munkafolyamatával létrehozott navigációs rácsvonalak az akusztikai rendszer által lesznek kiválasztva. Ha saját rácsvonalat szeretne használni, az **objektumok** lapról jelölheti meg őket.

### <a name="for-reference-the-objects-tab-parts"></a>Hivatkozás: Az objektumok lap részei
A lap oldalának részei:

1. A lap kiválasztása gombok (**objektumok** lap kiválasztva). Ezekkel a gombokkal megtudhatja, hogyan végezheti el az akusztikai sütjük különböző lépéseit balról jobbra.
2. A lap használatának rövid leírása.
3. A hierarchia ablakához elérhető szűrők. Ezzel a beállítással szűrheti a hierarchia ablakát a megadott típusú objektumokra, így könnyebben megjelölheti őket. Ha még nem jelölte meg az akusztikus elemek egyikét sem, az utolsó két lehetőség kiválasztásával semmi sem jelenik meg. Azonban hasznosak lehetnek a megjelölt objektumok megtalálásához, miután ezt megtette.
4. Ha nincs kiválasztva objektum, ez a szakasz a jelenet összes objektumának állapotát mutatja:
    * Összesen – a jelenetben aktív, nem rejtett objektumok teljes száma.
    * Figyelmen kívül hagyva – az objektumok száma, amelyek nem hálók vagy terepek.
    * Mesh (háló) – a színben megjelenített objektumok száma a jelenetben
    * Terep – a jelenetben lévő terepi objektumok száma
    * Geometria – a rácsvonalak vagy a terepi objektumok száma a jelenetben "akusztikai geometria" jelöléssel
    * Navigálás – a rácsvonalak vagy a terepi objektumok száma a jelenetben "akusztikai navigáció" jelöléssel. Ez a szám nem tartalmazza az egység NavMesh.
5. A jelenetben lévő "Mark-able" objektumok teljes számát jeleníti meg, amely csak a rácsvonalak és a terepek esetében használható. Megjeleníti azokat a jelölőnégyzeteket, amelyek segítségével megjelölheti (hozzáadhatja a megfelelő összetevőt) az objektumok geometriáként való megjelenítéséhez vagy az akusztikai navigációhoz.
6. Ha semmi sincs kiválasztva, ez a megjegyzés arra emlékeztet, hogy kijelöli az objektumokat, ha szükséges. Az egyik vagy mindkét jelölőnégyzet bejelölésével megadhatja a jelenet összes objektumát anélkül, hogy bármit kellene kijelölnie.
7. Az objektumok kijelölésekor ez a szakasz csak a kiválasztott objektumok állapotát jeleníti meg.
8. A "Mark-able" kijelölt objektumok teljes számát jeleníti meg. A jelölőnégyzetek ellenőrzésével vagy törlésével a rendszer csak a kijelölt objektumokat jelöli vagy törli.

Ha a jelenetben semmi sincs kiválasztva, az objektumok lap a következő képhez hasonlóan fog kinézni:

![Képernyőkép az akusztikai objektumok lapról kijelölés nélkül](media/objects-tab-no-selection-detail.png)

Ha a jelenet vagy a hierarchia ablakban van kiválasztva, a következő képhez hasonlóan fog kinézni:

![Képernyőfelvétel az akusztikai objektumok lapról a kijelölés látható](media/objects-tab-selection-detail.png)

Ha egyes objektumok meg vannak jelölve, és néhány nem, akkor a megfelelő jelölőnégyzet a "vegyes" értéket jeleníti meg:

![Képernyőkép az akusztikai objektumok lapról, vegyes kijelölési ikonnal kiemelve](media/mixed-object-selection-detail.png)

A jelölőnégyzetre kattintva minden objektum meg lesz jelölve, és az újra gombra kattintva törli az összes objektumot.

Az objektumok a geometriához és a navigáláshoz egyaránt megadhatók.

## <a name="select-acoustic-materials"></a>Akusztikai anyagok kiválasztása
Ha az objektumok meg vannak jelölve, kattintson az **anyagok** gombra, és rendelje hozzá az akusztikai anyagokat. A Project akusztikai anyagok rendszere a Unity vizualizációs rendszerhez van kötve: két objektumhoz külön akusztikai anyagok szükségesek, külön vizualizációs anyagokkal kell rendelkezniük.

Az akusztikai anyagok határozzák meg, hogy az egyes felületek milyen mennyiségű hangerőt tükröznek. Az alapértelmezett akusztikai anyag a betonhoz hasonló abszorpciós. A Project akusztika a vizuális anyag neve alapján mutatja be az anyagokat. Az "egyéni" akusztikai anyagot hozzárendelheti egy anyaghoz az abszorpciós együttható csúszkájának lehetővé tételéhez.

A helyiségben lévő adott anyag visszaverődési ideje fordítottan kapcsolódik a hozzá tartozó abszorpciós együtthatóhoz, és a legtöbb anyagnál az 0,01 – 0,20 tartományba tartozó abszorpciós értékek szerepelnek. Az ezen a tartományon kívüli abszorpciós együtthatóval rendelkező anyagok nagyon nedvszívóak.

![A Zengő idő negatív korrelációját ábrázoló gráf abszorpciós együtthatóval](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Hivatkozás: Az anyagok lap részei
![Az egység akusztikai anyagok lapjának képernyőképe](media/materials-tab-detail.png)

1. A lap megjelenítéséhez használt **anyagok** lap gomb.
2. A lap használatának rövid leírása.
3. Ha be van jelölve, csak a hangjelzéssel ellátott objektumok által használt anyagok lesznek felsorolva. Ellenkező esetben a jelenetben használt összes anyag fel lesz sorolva.
4. Ezekkel a beállításokkal módosíthatja a legördülő menü azon sorrendjét, amely akkor jelenik meg, ha az alábbi akusztikai oszlopban a legördülő listából kattint (#6). A **név** alapján rendezi az akusztikai anyagokat. A "Absorptivity" a Absorptivity sorrendje szerint rendezi őket alacsonyról magasra.
5. A jelenetben használt anyagok listája, betűrendbe rendezve. Ha a **csak a megjelölés csak** a megjelenített jelölőnégyzet be van jelölve (#3), akkor a rendszer csak az **akusztikai geometriának** jelölt objektumok által használt anyagokat jeleníti meg. Ha ide kattint, az itt látható minden olyan objektum, amely az adott anyagot használja.
6. Megjeleníti azt az akusztikai anyagot, amelyhez a jelenet anyaga hozzá lett rendelve. Kattintson egy legördülő listára egy jelenet anyagának egy másik akusztikai anyaghoz való hozzárendeléséhez. Megváltoztathatja a menü rendezési sorrendjét, ha egy elemre kattint a rendezési **akusztika:** fenti beállítások (#4) alapján.
7. Az előző oszlopban kiválasztott anyag akusztikus abszorpciós együtthatóját mutatja. A nulla érték azt jelenti, hogy tökéletesen tükröző (nincs abszorpció), míg az 1 érték tökéletesen nedvszívó (nincs reflexió). Az abszorpciós együttható nem módosítható, ha a kiválasztott anyag "Custom".
8. Az "egyéni" elemhez rendelt anyagok esetében a csúszka már nem lesz letiltva, és a csúszka használatával vagy egy érték beírásával kiválaszthatja az abszorpciós együtthatót.

## <a name="calculate-and-review-listener-probe-locations"></a>Figyelő mintavételi helyeinek kiszámítása és áttekintése
Az anyagok kiosztása után váltson a mintavételek lapra, és kattintson a **számítás** gombra a figyelő mintavételi pontjainak szimulációhoz való elhelyezéséhez.

### <a name="what-the-calculate-button-calculates"></a>Mi a "számítás..." gomb kiszámításakor
A **számítás...** gomb a kiválasztott akusztikus jelenet geometriáját használja a jelenet szimulációhoz való előkészítéséhez:

1. A geometria a jelenet rácsvonalait veszi át, és kiszámítja a Voxel kötetet. A Voxel kötet egy 3 dimenziós kötet, amely a teljes jelenetet magában foglalja, és a kis köbös "voxels". A voxels méretét a szimuláció gyakorisága határozza meg, amelyet a **szimulációs megoldás** beállítása határoz meg. Minden Voxel a "nyitott levegő" vagy a jelenet geometriáját tartalmazó jelöléssel van megjelölve. Ha egy Voxel geometriát tartalmaz, akkor a Voxel címkéje az adott geometriához rendelt anyag abszorpciós együtthatóját jelöli.
2. A navigációs háló (ka) t használja a figyelő mintavételi pontjainak elhelyezésére. Az algoritmus kiegyenlíti a térbeli lefedettséggel kapcsolatos versengő problémákat, a szimulációs időt és a fájlméretet, ugyanakkor gondoskodik arról, hogy a szűk folyosók és a kis terek mindig bizonyos mennyiségű lefedettséggel rendelkezzenek. A tipikus mintavételi pontok a kis méretű jeleneteknél több ezer 100-es tartományba esnek.

A jelenet méretétől és a gép sebességétől függően ezek a számítások több percet is igénybe vehetnek.

### <a name="review-voxel-and-probe-placement"></a>Voxel és mintavételi elhelyezés áttekintése
Tekintse meg mind a Voxel, mind a mintavételi pontok helyét, és győződjön meg arról, hogy készen áll a jelenet kisütni. A nem teljes navigációs rácsvonalak vagy a hiányzó vagy a felesleges akusztikai geometria általában gyorsan látható lesz az előzetes verzióban. A Voxel és a mintavételi elhelyezés engedélyezhető vagy letiltható a minialkalmazások menü használatával:

![Képernyőfelvétel a gadgetek menüjéről az egységben](media/gizmos-menu.png)

Az akusztikus geometriát tartalmazó Voxels zöld kockákként jelennek meg. Ismerkedjen meg a jelenettel, és ellenőrizze, hogy minden geometriának van-e voxels. A jelenet kamerájának kb. 5 méteren belül kell lennie a voxels megjelenítendő objektumnak.

Ha összehasonlítja a durva felbontással és a megoldással létrehozott voxels, látni fogja, hogy a durva voxels kétszer is nagy.

![Képernyőfelvétel a durva voxels előzetes verziójáról az Unity Editorban](media/voxel-cubes-preview.png)

A Szimulációs eredmények a figyelő mintavételi pontjának helyei között, futásidőben vannak interpolált. Győződjön meg arról, hogy a játékosnak a helyszín közelében kell lennie a mintavételi pontoknak.

![A mintavételek előzetes verziójának képernyőképe az Unity Editorban](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Ügyeljen a jelenet-Átnevezés lehetőségre
A jelenet neve a jelenet a mintavételi pont elhelyezését és a voxelization tároló fájlokhoz való összekapcsolására szolgál. Ha a jelenet a mintavételi pontok kiszámítása után átnevezve lett, az anyag-hozzárendelés és az elhelyezési adatok elvesznek, és újra kell futtatni.

### <a name="for-reference-parts-of-the-probes-tab"></a>Hivatkozás: A mintavételek lap részei
![A Unity akusztikus mintavételek lapjának képernyőképe](media/probes-tab-detail.png)

1. Az oldal felépítéséhez használt mintavétel lap gomb
2. A lap használatának rövid leírása
3. Ezek használatával durva vagy részletes szimulációs megoldást választhat. A durva gyorsabb, de bizonyos kompromisszumokkal rendelkezik. A részletekért lásd az alábbi [sütni](bake-resolution.md) -feloldást.
4. Válassza ki azt a helyet, ahol az akusztikai adatfájlokat a mező használatával kell elhelyezni. Kattintson a "..." gombra. mappa-választó használata. Az alapértelmezett érték az **eszközök/AcousticsData**. Ezen a helyen a **szerkesztő** almappája is létrejön. További információ az adatfájlokról: az [](#Data-Files) alábbi adatfájlok.
5. Az ehhez a jelenethez tartozó adatfájlok az itt megadott előtag használatával lesznek elnevezve. Az alapértelmezett érték a "Acoustics_ [jelenet neve]".
6. A mintavételek kiszámítása után a fenti vezérlőelemek le lesznek tiltva. Kattintson a **Törlés** gombra a számítások törléséhez és a vezérlők engedélyezéséhez, hogy az új beállítások használatával újraszámítsa a beállításokat.
7. Kattintson a **számítás...** gombra a jelenet voxelize és a mintavételi pont helyeinek kiszámításához. Ez helyileg történik a gépen, és a sütni művelet előtt kell elvégezni.

A Project akusztika ezen verziójában a mintavételek nem helyezhetők manuálisan, és a mintavételek lapon megadott automatikus folyamaton keresztül kell elhelyezni.

A durva és a részletes megoldással kapcsolatos további részletekért lásd a [sütni](bake-resolution.md) -feloldást ismertető témakört.

## <a name="bake-your-scene-using-azure-batch"></a>A jelenet Azure Batch használatával
Az Azure Batch szolgáltatással a felhőben lévő számítási fürttel is sütni a jelenet. A Project akusztikai Unity beépülő modul közvetlenül csatlakozik Azure Batchhoz, amely minden egyes sütni egy Azure Batch-fürtöt hoz létre, kezel és lebont. A **sütni** lapon adja meg az Azure-beli hitelesítő adatait, válassza ki a fürt típusát és méretét, majd kattintson a **sütni**gombra.

### <a name="for-reference-parts-of-the-bake-tab"></a>Hivatkozás: A sütni lap részei
![A Unity akusztikai sütni lapjának képernyőképe](media/bake-tab-details.png)

1. Az oldal felépítéséhez használt sütni lap gomb.
2. Egy rövid leírás arról, hogy mi a teendő ezen a lapon.
3. Az Azure-fiók létrehozása után az Azure-beli hitelesítő adatok megadására szolgáló mezők. További információ: [create a Azure batch Account](create-azure-account.md).
4. Docker-rendszerkép címkéje az akusztikai eszközkészlethez.
5. Az előfizetések kezeléséhez Azure Portal elindítása, a használat figyelése és a számlázási információk megtekintése stb. 
6. Az Azure batch számítási csomópontjának típusa, amelyet a számításhoz kíván használni. Az Azure-adatközpont helyének támogatnia kell a csomópont típusát. Ha nem biztos benne, hagyja a következőt: **Standard_F8s_v2**.
7. A számításhoz használni kívánt csomópontok száma. Az itt megadott szám befolyásolja a sütni elvégzéséhez szükséges időt, és a Azure Batch fő foglalása korlátozza. Az alapértelmezett foglalás csak két 8 fő csomópontot vagy a 1 16 Core csomópontot teszi lehetővé, de bővíthető. Az alapvető foglalási korlátozásokkal kapcsolatos további információkért lásd: [Azure batch fiók létrehozása](create-azure-account.md).
8. Jelölje be ezt a jelölőnégyzetet, ha a számítási készletet [alacsony prioritású csomópontok](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)használatára szeretné konfigurálni. Az alacsony prioritású számítási csomópontok jóval alacsonyabb költségeket jelentenek, de előfordulhat, hogy nem mindig lesznek elérhetők, vagy bármikor előzik.
9. A jelenet mintavételi száma a mintavételek lapon számítva. A mintavételek száma határozza meg, hogy hány szimulációt kell futtatni a felhőben. A mintavételnél több csomópontot nem adhat meg.
10. Az eltelt idő, ameddig a feladatainak a felhőben való futtatása várható. Ez nem tartalmazza a csomópont indítási idejét. Ha a feladatot elindítják, akkor ez azt mutatja, hogy mennyi ideig kell lennie ahhoz, hogy vissza lehessen állítani az eredményeket. Vegye figyelembe, hogy ez csak becslés.
11. A szimulációk futtatásához szükséges számítási idő teljes mennyisége. Ez az Azure-ban használni kívánt csomópont-számítási idő teljes mennyisége. Ennek az értéknek a használatáról további információt az alábbi, a [sütni-költségeket](#Estimating-bake-cost) ismertető cikkben talál.
12. Az üzenetből megtudhatja, hogy a rendszer hová menti a sütni eredményeit a feladatok befejeződése után.
13. (Csak speciális használatra) Ha valamilyen okból meg kell kényszeríteni az egységet, hogy felejtsen el egy beküldött sütni (például letöltötte az eredményeket egy másik géppel), kattintson az **állapot törlése** gombra az elküldött feladatokkal kapcsolatos felejtsd. Vegye figyelembe, hogy ez azt jelenti, hogy az eredményül kapott fájl **nem** lesz letöltve, és **Ez nem ugyanaz, mint a feladat megszakítása**. Ha a fut, a továbbra is fut a felhőben.
14. Kattintson a **sütni** gombra a sütni a felhőbe való beküldéséhez. A feladat futása közben ez a feladat **megszakítását** mutatja.
15. Előkészíti az [akusztikai szimulációk](#Local-bake)feldolgozását a számítógépen.
16. Ez a terület a sütni állapotát mutatja. Ha elkészült, a letöltéstkell megjelenítenie.

Az aktív feladatokról, a számítási készletekről és a [Azure Portal](https://portal.azure.com)a tárterületről mindig teljes körű információkhoz juthat.

Amíg egy feladat futtatja a **sütni** gomb módosításaita megszakítási feladatokra. Ezzel a gombbal megszakíthatja a feladat végrehajtását. A rendszer kérni fogja, hogy erősítse meg a feladat megszakítása előtt. Egy feladat megszakítása nem vonható vissza, így nem lesz elérhető eredmény, és a rendszer továbbra is felszámítja az összes felhasznált Azure számítási időt.

Miután elindította a sütni, lezárhatja az egységet. A Felhőbeli sütni a projekttől, a csomópont típusától és a csomópontok számától függően több órát is igénybe vehet. A sütni-feladatok állapota a projekt újratöltése és a akusztikai ablak megnyitása után frissül. Ha a feladatot befejezte, a rendszer letölti a kimeneti fájlt.

Az Azure-beli hitelesítő adatokat a rendszer biztonságosan tárolja a helyi gépen, és társítja az egység-szerkesztőhöz. Kizárólag az Azure-hoz való biztonságos kapcsolat létesítésére szolgálnak.

### <a name="Estimating-bake-cost"></a>Az Azure Bake Cost becslése

Ha szeretné megbecsülni, hogy egy adott sütni Milyen költségekkel jár, a **becsült számítási költségek**, azaz az időtartam, és az óradíjat a kiválasztott virtuálisgép- **csomópont** helyi pénznemében adja meg. Az eredmény nem fogja tartalmazni a csomópontok üzembe helyezéséhez szükséges csomóponti időt. Ha például a **Standard_F8s_v2** lehetőséget választja, amelynek díja $0.40/HR, a becsült számítási költségek pedig 3 óra és 57 perc, a feladathoz tartozó becsült költségek $0,40 * ~ 4 óra = ~ $1,60. A tényleges költségeket valószínűleg egy kicsit magasabbra vált, mivel a csomópontok megkezdése hosszabb időt vesz igénybe. Az óránkénti csomópont költségét a [Azure batch díjszabási](https://azure.microsoft.com/pricing/details/virtual-machines/linux) oldalán találja (válassza a "számítás optimalizált" vagy a "nagy teljesítményű számítás" lehetőséget a kategória esetében).

## <a name="Local-bake"></a>A jelenet kisütni a számítógépen
Saját SZÁMÍTÓGÉPén is megadhatja saját jelenetét. Ez akkor lehet hasznos, ha egy Azure Batch fiók létrehozása előtt kis színfalakkal rendelkező akusztikai lehetőségekkel kísérletezik. Vegye figyelembe, hogy az akusztikai szimuláció a jelenet méretétől függően hosszú időt is igénybe vehet.

### <a name="minimum-hardware-requirements"></a>Minimális hardverkövetelmények
* X86-64 processzor legalább 8 maggal és 32 GB RAM-mal

Például egy 8 magos, Intel Xeon E5-1660 @ 3 GHz-es és 32 GB RAM-mal rendelkező gépen
* Az 100-es mintavételi teszttel ellátott kis jelenet körülbelül 2 órát vehet igénybe a durva sütni vagy a 32 órán át.
* Az 1000-es mintavételt tartalmazó közepes méretű jelenet körülbelül 20 órát vehet igénybe egy durva sütjük vagy 21 nap után.

### <a name="setup-docker"></a>A Docker telepítése
A Docker telepítése és konfigurálása azon a számítógépen, amely a szimulációt fogja feldolgozni:
1. Telepítse a [Docker](https://www.docker.com/products/docker-desktop)-eszközkészletet.
2. Indítsa el a Docker-beállításokat, navigáljon a "speciális" beállításokhoz, és konfigurálja az erőforrásokat legalább 8 GB RAM-mal. Minél több CPU-t tud lefoglalni a Docker számára, annál gyorsabban fejeződik be a sütni. ![Képernyőfelvétel a Docker-beállításokról](media/docker-settings.png)
3. Navigáljon a "megosztott meghajtók" elemre, és kapcsolja be a megosztást a feldolgozáshoz használt meghajtón.![Képernyőfelvétel a Docker Shared Drive lehetőségeiről](media/docker-shared-drives.png)

### <a name="run-local-bake"></a>Helyi sütni futtatása
1. Kattintson a "helyi sütni előkészítése" gombra a **sütni** lapon, és válassza ki azt a mappát, ahol a bemeneti fájlok és a végrehajtási parancsfájlok el lesznek mentve. Ezután bármilyen gépen futtathatja a sütni-t, ha megfelel a minimális hardverkövetelmények követelményeinek, és a Docker telepítve van, ha a mappát átmásolja a gépre.
2. Indítsa el a szimulációt a "runlocalbake. bat" parancsfájl használatával. Ez a szkript beolvassa a projekt akusztikai Docker-rendszerképét a szimulációs feldolgozáshoz szükséges eszközkészlettel, és elindítja a szimulációt. 
3. A szimuláció befejezését követően másolja vissza az eredményül kapott. ACE fájlt az Unity-projektbe. Annak biztosítása érdekében, hogy az egység felismeri bináris fájlként, fűzze hozzá a ". Bytes" fájlt a fájlkiterjesztés (például "Scene1. ACE. Bytes"). A szimuláció részletes naplóit a "AcousticsLog. txt" fájl tárolja. Ha bármilyen problémába ütközik, ossza meg ezt a fájlt a diagnosztika támogatásához.

## <a name="Data-Files"></a>A sütni folyamat által hozzáadott adatfájlok

A sütni folyamat során négy adatfájl jön létre. Az egyik tartalmazza a szimuláció eredményeit, és a címmel ellátott hajókat. A többiek az Unity Editor kapcsolódó adatait tárolják.

Szimulációs eredmény:
* **Eszközök\_/AcousticsData/akusztika [SceneName]. ACE. Bytes**: Ez a futásidejű keresési táblázat, amely a szimuláció eredményeit és a voxelized akusztikai jelenet elemeit tartalmazza. A fájl helye és neve módosítható a mintavételek lapon lévő mezők használatával.

Ügyeljen arra, hogy ne törölje a szimulációs eredményhalmaz fájlját. Nem lehet helyreállítani, kivéve a jelenet kisütését.

Szerkesztői adatfájlok:
* **Assets/Editor/[SceneName]\_AcousticsParameters.asset**: Ez a fájl az akusztikai felhasználói felület mezőiben megadott adat tárolására szolgáló mezőket tárolja. A fájl helye és neve nem módosítható.
* **Assets/AcousticsData/Editor/Acoustics_ [SceneName]. Vox**: Ez a fájl tárolja a voxelized akusztikai geometriáját, valamint a mintavétel **...** gomb használatával kiszámított anyag-tulajdonságokat a mintavételek lapon. A fájl helye és neve módosítható a mintavételek lapon lévő mezők használatával.
* **Eszközök\_/AcousticsData/szerkesztő/akusztika [SceneName]\_config. XML**: Ez a fájl a mintavétel **..** . gomb használatával a mintavételek lapon lévő szimulációs paramétereket tárolja. A fájl helye és neve módosítható a mintavételek lapon lévő mezők használatával.

## <a name="set-up-the-acoustics-lookup-table"></a>Az akusztikai keresési táblázat beállítása
Húzza át a **projekt akusztikai** paneljét a projekt panelről a jelenetbe:

![Képernyőfelvétel az akusztikai panelről az Unity-ben](media/acoustics-prefab.png)

Kattintson a **ProjectAcoustics** játék objektumra, és nyissa meg az ellenőr panelt. Adja meg a sütni eredmény helyét (a. ACE-fájl, **eszköz/AcousticsData**) – húzza át az akusztikus kezelő parancsfájlba, vagy kattintson a szövegmező melletti kör gombra.

![Képernyőfelvétel a Acoustics Manager panelről az Unity-ben](media/acoustics-manager.png)  

## <a name="next-steps"></a>További lépések
* Az [Unity tervezési vezérlőinek](unity-workflow.md) megismerése
* A [Project akusztikai tervezési fogalmak](design-process.md) megismerése

