---
title: A Project akusztikai Unreal Bake oktatóanyaga
titlesuffix: Azure Cognitive Services
description: Ez a dokumentum leírja, hogyan kell beküldeni az akusztikai sütni-t az Unreal Editor bővítmény használatával.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: michem
ROBOTS: NOINDEX
ms.openlocfilehash: 47946570db305ff3d54dfed9ea6f698e5deb7b72
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704793"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>A Project akusztikai Unreal Bake oktatóanyaga
Ez a dokumentum leírja, hogyan kell beküldeni az akusztikai sütni-t az Unreal Editor bővítmény használatával.

A sütni öt lépésből áll:

1. A lejátszó navigációs rácsvonalának létrehozása vagy címkézése
2. Akusztikai geometria címkézése
3. Akusztikai anyagok tulajdonságainak kiosztása geometriához
4. Mintavételi minta elhelyezése
5. Sütni

## <a name="open-the-project-acoustics-editor-mode"></a>A Project akusztikus szerkesztő mód megnyitása

Importálja a projekt akusztikai beépülő moduljának csomagját a projektbe. Ehhez az [Unreal Integration](unreal-integration.md) témakörben talál segítséget. Ha a beépülő modul integrálva van, nyissa meg az akusztikai FELÜLETET az új akusztikai mód ikonra kattintva.

![Képernyőkép az Unreal Editor akusztikai üzemmódjának beállításáról](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Akusztikai szereplők címkézése

Az objektumok lap az első lap, amely megjelenik az akusztikai mód megnyitásakor. Ezen a lapon megcímkézheti a levelében szereplő szereplőket, ami hozzáadja a **AcousticsGeometry** vagy a **AcousticsNavigation** címkét a szereplőkhöz.

Jelöljön ki egy vagy több objektumot a globális körvonalazó részben, vagy használja a **tömeges kijelölési** szakaszt, hogy segítsen kijelölni egy adott kategória összes objektumát. Az objektumok kijelölése után a **címkézés** szakaszban alkalmazhatja a kívánt címkét a kijelölt objektumokra.

Ha valami nem **AcousticsGeometry** és **AcousticsNavigation** címkével rendelkezik, a rendszer figyelmen kívül hagyja a szimulációban. Csak a statikus rácsvonalak, a Nav-rácsvonalak és a tájak támogatottak. Ha bármilyen mást címkéz, azt a rendszer figyelmen kívül hagyja.

### <a name="for-reference-the-objects-tab-parts"></a>Hivatkozás: Az objektumok lap részei

![Képernyőkép az akusztikai objektumok lapról az Unreal-ben](media/unreal-objects-tab-details.png)

1. A lap kiválasztása gombok (**objektumok** lap kiválasztva). Ezekkel a gombokkal megtudhatja, hogyan végezheti el az akusztikai sütjük különböző lépéseit felülről lefelé.
2. A lap használatának rövid leírása.
3. A szinten lévő szereplők számára elérhető választók.
4. A **kiválasztás** elemre kattintva a szint összes olyan objektuma kijelölhető, amely megfelel legalább az egyik ellenőrzött szereplő típusának.
5. Ha az **összes kijelölése** elemre kattint, a rendszer törli az aktuális kijelölést. Ez ugyanaz, mint az Escape-kulcs elütése.
6. Ezekkel a választógombokkal kiválaszthatja, hogy a kijelölt szereplőkkel kívánja-e alkalmazni a geometriát vagy a navigációs címkét.
7. A **címke** elemre kattintva hozzáadhatja a kiválasztott címkét az összes jelenleg kijelölt résztvevőhöz.
8. A **jelölését** elemre kattintva eltávolítja a kijelölt címkét az összes jelenleg kijelölt szereplőről.
9. A **címkézett kijelölése** elemre kattintva törölheti az aktuális kijelölést, és kiválaszthatja az összes szereplőt a jelenleg kijelölt címkével.
10. Ezek a statisztikák megmutatják, hogy hány szereplő van címkézve az egyes címkék típusával.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Akusztikai elzáródás és reflexiós geometria címkézése

Nyissa meg az akusztika ablak objektumok lapját. Az objektumok megjelölése akusztikai Geometriaként, ha occlude, tükrözni vagy elnyelni kívánja a hangot. Az akusztikai geometria olyan dolgokat tartalmazhat, mint például a felszín, a fal, a tetők, a Windows & ablakok üvege, a szőnyegek és a nagyméretű bútorok. Ezekhez az objektumokhoz tetszőleges bonyolultsági szintű komplexitást használhat. Mivel a jelenet a szimuláció előtt voxelized, a nagyon részletes rácsvonalak, például a sok kis mennyiségű fát tartalmazó fák nem drágábbak, mint az egyszerűsített objektumok.

Ne tartalmazzon olyan dolgokat, amelyek nem érintik az akusztikai tényezőket, például a láthatatlan ütközési hálókat.

Egy objektumnak a mintavételi számítás időpontjában történő átalakítása (az alábbi mintavételek lapon) a sütni eredményekben van rögzítve. A jelenetben megjelölt objektumok bármelyikének áthelyezéséhez újra kell végezni a mintavétel számítását és a jelenet kisütését.

### <a name="create-or-tag-a-navigation-mesh"></a>Navigációs háló létrehozása vagy címkézése

A navigációs háló a mintavételi pontok szimulációhoz való elhelyezésére szolgál. Használhatja az Unreal [NAV Mesh Bounds kötetet](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html), vagy megadhatja saját navigációs hálóját is. Legalább egy objektumot fel kell címkéznie **akusztikai navigációként**. Ha az Unreal navigációs hálóját használja, győződjön meg róla, hogy az elsőt építette.

### <a name="acoustics-volumes"></a>Akusztikai kötetek ###

További, speciális testreszabási műveleteket végezhet a navigációs területeken **akusztikai kötetek**használatával. Az **akusztikai kötetek** olyan szereplők, amelyek hozzáadhatók a jelenetekhez, amelyek lehetővé teszik, hogy kiválassza a navigációs hálóból felvenni és figyelmen kívül hagyó területeket. A színész olyan tulajdonságot tesz elérhetővé, amely a "Belefoglalás" és a "kizárás" között állítható be. A "include" kötetek biztosítják, hogy a rendszer csak a navigációs rácsvonal területeit vegye figyelembe, és "kizárják" a köteteket a figyelmen kívül hagyott területekre. A "kizárás" köteteket a rendszer mindig a "Belefoglalás" kötetek után alkalmazza. Ügyeljen arra, hogy az akusztikai köteteket az objektumok lapon lévő szokásos folyamattal lássa el **akusztikai navigálásként** . Ezeket a szereplőkkel ***nem*** címkézi automatikusan a rendszer.

![Képernyőfelvétel: akusztikai kötet tulajdonságai az Unreal-ben](media/unreal-acoustics-volume-properties.png)

A "kizárás" kötetek elsődleges célja, hogy részletesen szabályozható legyen, hol ne helyezzen mintavételt az erőforrás-használat szigorítására.

![Képernyőfelvétel: akusztikai kötet kizárása az Unreal-ben](media/unreal-acoustics-volume-exclude.png)

A "include" kötetek hasznosak a jelenet kézi részeinek létrehozásához, például ha több akusztikai zónába szeretné feltörni a jelenetet. Ha például van egy nagy méretű jelenet, amely sok kilométerre van kiválasztva, és két olyan érdeklődési területtel rendelkezik, amelyekkel az akusztikai elemeket szeretné sütni. Két nagy méretű "include" kötetet rajzolhat a jelenetbe, és mindegyikhez egyszerre létrehozhat ACE-fájlokat. Ebben az esetben a játékokban az trigger-kötetek és a tervrajzok együttes használatával is betöltheti a megfelelő ACE-fájlt, amikor a játékos megközelíti az egyes csempéket.

Az **akusztikai kötetek** csak a navigációt korlátozzák, a geometriát ***nem*** . Az "include" akusztikai köteten belüli összes mintavétel továbbra is a köteten kívüli összes szükséges geometriát lekéri a hullámos szimulációk végrehajtásakor. Ezért az egyik szakaszból a másikba való áthaladást okozó adatelzáródás vagy más akusztika miatti megszakítások nem állnak fenn.

## <a name="select-acoustic-materials"></a>Akusztikai anyagok kiválasztása

Az objektumok címkézése után kattintson az **anyagok** gombra az anyagok lapra való ugráshoz. Ezen a lapon adhatók meg az egyes anyagokhoz tartozó lényeges tulajdonságok a szinten. A szereplők címkézése előtt üresen marad.

Az akusztikai anyagok határozzák meg, hogy az egyes felületek milyen mennyiségű hangerőt tükröznek. Az alapértelmezett akusztikai anyag a betonhoz hasonló abszorpciós. A Project akusztika a jelenet anyagának neve alapján mutatja be az anyagokat.

A helyiségben lévő adott anyag visszaverődési ideje fordítottan kapcsolódik a hozzá tartozó abszorpciós együtthatóhoz, és a legtöbb anyagnál az 0,01 – 0,20 tartományba tartozó abszorpciós értékek szerepelnek. A tartományon kívüli abszorpciós együtthatóval rendelkező anyagok nagyon nedvszívóak. Ha például egy szoba túl Zengő, módosítsa a falak, a padló vagy a mennyezet akusztikai anyagát egy magasabb absorptivity. Az akusztikai anyagok hozzárendelése minden olyan szereplőre vonatkozik, amely a jelenet anyagát használja.

![A Zengő idő negatív korrelációját ábrázoló gráf abszorpciós együtthatóval](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Hivatkozás: Az anyagok lap részei

![Képernyőkép az akusztikai objektumok lapról az Unreal-ben](media/unreal-materials-tab-details.png)

1. A lap megjelenítéséhez használt **anyagok** lap gomb.
2. A lap használatának rövid leírása.
3. A szinten használt anyagok listája a **AcousticsGeometry**-ként címkézett szereplőktől. Ha ide kattint, az itt látható minden olyan objektum, amely az adott anyagot használja.
4. Megjeleníti azt az akusztikai anyagot, amelyhez a jelenet anyaga hozzá lett rendelve. Kattintson egy legördülő listára egy jelenet anyagának egy másik akusztikai anyaghoz való hozzárendeléséhez.
5. Az előző oszlopban kiválasztott anyag akusztikus abszorpciós együtthatóját mutatja. A nulla érték azt jelenti, hogy tökéletesen tükröző (nincs abszorpció), míg az 1 érték tökéletesen nedvszívó (nincs reflexió). Ha megváltoztatja ezt az értéket, a rendszer frissíti az akusztikai anyagokat (#4. lépés) az **Egyéni**értékre.

Ha módosítja a jelenetben lévő anyagokat, a Project akusztikai beépülő modulban lévő lapokat kell váltania az **anyagok** lapon látható módosítások megtekintéséhez.

## <a name="calculate-and-review-listener-probe-locations"></a>Figyelő mintavételi helyeinek kiszámítása és áttekintése

Az anyagok hozzárendelését követően váltson a mintavételek **lapra.**

### <a name="for-reference-parts-of-the-probes-tab"></a>Hivatkozás: A mintavételek lap részei

![Az Unreal-beli akusztikai mintavételi lapok képernyőképe](media/unreal-probes-tab-details.png)

1. Az oldal felépítéséhez használt **mintavétel lap gomb**
2. A lap használatának rövid leírása
3. Ezzel a beállítással durva vagy részletes szimulációs megoldást választhat. A durva gyorsabb, de bizonyos kompromisszumokkal rendelkezik. A részletekért lásd az alábbi [sütni](bake-resolution.md) -feloldást.
4. Válassza ki azt a helyet, ahol az akusztikai adatfájlokat a mező használatával kell elhelyezni. Kattintson a "..." gombra. mappa-választó használata. További információ az adatfájlokról: az [](#Data-Files) alábbi adatfájlok.
5. Az ehhez a jelenethez tartozó adatfájlok az itt megadott előtag használatával lesznek elnevezve. Az alapértelmezett érték a "[Level name] _AcousticsData".
6. Kattintson a **számítás** gombra a jelenet voxelize és a mintavételi pont helyeinek kiszámításához. Ez helyileg történik a gépen, és a sütni művelet előtt kell elvégezni. A mintavételek kiszámítását követően a fenti vezérlőelemek le lesznek tiltva, és ez a gomb egyértelművé válik. Kattintson a **Törlés** gombra a számítások törléséhez és a vezérlők engedélyezéséhez, hogy az új beállítások használatával újraszámítsa a beállításokat.

A mintavételeket a mintavételek **lapon megadott** automatikus folyamaton keresztül kell elhelyezni.


### <a name="what-the-calculate-button-calculates"></a>A "számítás" gomb kiszámítása

A **számítás** gombra kattintva megtekintheti az eddig megadott összes értéket (geometria, navigáció, anyagok, valamint a durva/részletes beállítás), és több lépésen haladhat át:

1. A geometria a jelenet rácsvonalait veszi át, és kiszámítja a Voxel kötetet. A Voxel kötet egy 3 dimenziós kötet, amely a teljes jelenetet magában foglalja, és a kis köbös "voxels". A voxels méretét a szimuláció gyakorisága határozza meg, amelyet a **szimulációs megoldás** beállítása határoz meg. Minden Voxel a "nyitott levegő" vagy a jelenet geometriáját tartalmazó jelöléssel van megjelölve. Ha egy Voxel geometriát tartalmaz, akkor a Voxel címkéje az adott geometriához rendelt anyag abszorpciós együtthatóját jelöli.
2. Ezután a navigációs adatai alapján kiszámítja azokat az akusztikusan érdekes helyeket, ahol a játékos elmehet. A helyek olyan ésszerűen kis készletet próbálnak megtalálni, amely kisebb területeket, például ajtókat és folyosókat, majd a helyiségeket is tartalmaz. Kisméretű jelenetek esetében ez általában kevesebb, mint 100 helyen, míg a nagyméretű jelenetek akár 1000 is lehetnek.
3. Minden, az általa kiszámított végső figyelő esetében több paramétert határoz meg, például hogy a "Megnyitás" a szóköz, a szoba mérete stb.
4. A számítások eredményeit a megadott helyen található fájlokban tárolja a rendszer (lásd az [](#Data-Files) alábbi adatfájlokat)

A jelenet méretétől és a gép sebességétől függően ezek a számítások több percet is igénybe vehetnek.

A számítások befejezését követően a Voxel és a mintavételi pont helyei is megtekinthetők, így biztosítható, hogy a sütni jó eredményeket nyújtson. A hibás navigációs rácsvonalak, illetve a hiányzó/extra geometriák általában gyorsan láthatóvá válnak az előzetes verzióban, így kijavítani.


## <a name="debug-display"></a>Hibakeresési megjelenítés

A mintavételi számítás befejezése után egy új szereplő jelenik meg a **AcousticsDebugRenderer**nevű világbeli körvonalazó. A renderelési mintavételek és a **renderelési Voxels** jelölőnégyzet bejelölésével engedélyezheti a hibakeresési megjelenítést a szerkesztői nézetablakban.

![Képernyőfelvétel: akusztikai hibakeresés a megjelenítő szereplővel az Unreal Editorban](media/acoustics-debug-renderer.png)

Ha nem lát voxels vagy mintavételt a szintjére, győződjön meg arról, hogy a valós idejű megjelenítés engedélyezve van a nézetablakban.

![Képernyőkép a valós idejű renderelési lehetőségről az Unreal-ben](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels

A Voxels a jelenet ablakban zöld kockákként jelennek meg a részt vevő geometriaban. A csak levegőt tartalmazó Voxels nem jelennek meg. A teljes jelenetben egy nagyméretű zöld mező jelenik meg, amely a szimulációban használni kívánt teljes Voxel-kötetet jelöli.
Mozgassa a jelenetét, és győződjön meg róla, hogy az akusztikailag csatornaelzáródás geometria voxels rendelkezik. Győződjön meg arról is, hogy a nem akusztikai objektumok, például az ütközési rácsvonalak nem lettek voxelized. A jelenet kamerájának kb. 5 méteren belül kell lennie a voxels megjelenítendő objektumnak.

Ha összehasonlítja a durva felbontással és a megoldással létrehozott voxels, látni fogja, hogy a durva voxels kétszer is nagy.

![Képernyőkép az akusztikai voxels előzetes verziójáról az Unreal Editorban](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Mintavételi pontok

A mintavételi pontok a lehetséges lejátszó (figyelő) helyek szinonimái. A sütés során a szimuláció kiszámítja, hogy az akusztikai pontok az összes lehetséges forrást csatlakoztatják az egyes mintavételi pontokhoz. Futásidőben a figyelő helye a közeli mintavételi pontok között interpolált.

Fontos annak ellenőrzését, hogy a mintavételi pontok léteznek-e, ahol a játékosnak várhatóan utaznia kell a helyszínen. A mintavételi pontokat a projekt akusztikai motorja helyezi a navigációs hálóra, és nem helyezhető át és nem szerkeszthető, ezért ügyeljen arra, hogy a navigációs háló minden lehetséges helyet letakarjon a mintavételi pontok vizsgálatával.

![Képernyőkép az akusztikai tesztek előzetes verziójáról az Unreal-ben](media/unreal-probes-preview.png)

A durva és a részletes megoldással kapcsolatos további részletekért lásd a [sütni](bake-resolution.md) -feloldást ismertető témakört.

## <a name="bake-your-level-using-azure-batch"></a>A szint sütni Azure Batch használatával

Az Azure Batch szolgáltatással a felhőben lévő számítási fürttel is sütni a jelenet. A Project akusztikai Unreal beépülő modulja közvetlenül kapcsolódik a Azure Batchhoz az egyes sütni Azure Batch-fürtök létrehozásához, kezeléséhez és lebontásához. A sütni lapon adja meg az Azure-beli hitelesítő adatait, válassza ki a fürt típusát és méretét, majd kattintson a sütni gombra.

### <a name="for-reference-parts-of-the-bake-tab"></a>Hivatkozás: A sütni lap részei

![Képernyőkép az Unreal Bake lapról](media/unreal-bake-tab-details.png)

1. Az oldal felépítéséhez használt sütni lap gomb.
2. Egy rövid leírás arról, hogy mi a teendő ezen a lapon.
3. Az Azure-fiók létrehozása után az Azure-beli hitelesítő adatok megadására szolgáló mezők. További információ: [create a Azure batch Account](create-azure-account.md).
4. Az előfizetések kezeléséhez Azure Portal elindítása, a használat figyelése és a számlázási információk megtekintése stb. 
5. Az Azure batch számítási csomópontjának típusa, amelyet a számításhoz kíván használni. Az Azure-adatközpont helyének támogatnia kell a csomópont típusát. Ha nem biztos benne, hagyja a következőt: **Standard_F8s_v2**.
6. A számításhoz használni kívánt csomópontok száma. Az itt megadott szám befolyásolja a sütni elvégzéséhez szükséges időt, és a Azure Batch fő foglalása korlátozza. Az alapértelmezett foglalás csak két 8 fő csomópontot vagy a 1 16 Core csomópontot teszi lehetővé, de bővíthető. Az alapvető foglalási korlátozásokkal kapcsolatos további információkért lásd: [Azure batch fiók létrehozása](create-azure-account.md).
7. Jelölje be ezt a jelölőnégyzetet, ha a számítási készletet [alacsony prioritású csomópontok](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)használatára szeretné konfigurálni. Az alacsony prioritású számítási csomópontok jóval alacsonyabb költségeket jelentenek, de előfordulhat, hogy nem mindig lesznek elérhetők, vagy bármikor előzik.
8. Az eltelt idő, ameddig a feladatainak a felhőben való futtatása várható. Ez nem tartalmazza a csomópont indítási idejét. Ha a feladatot elindítják, akkor ez azt mutatja, hogy mennyi ideig kell lennie ahhoz, hogy vissza lehessen állítani az eredményeket. Vegye figyelembe, hogy ez csak becslés.
9. A szimulációk futtatásához szükséges számítási idő teljes mennyisége. Ez az Azure-ban használni kívánt csomópont-számítási idő teljes mennyisége. Ennek az értéknek a használatáról további információt az alábbi, a [sütni-költségeket](#Estimating-bake-cost) ismertető cikkben talál.
10. Kattintson a sütni gombra a sütni a felhőbe való beküldéséhez. A feladat futása közben ez a feladat **megszakítását** mutatja. Ha a lapon hibák léptek fel, vagy ha a mintavételek **lapon nem** fejeződött be a munkafolyamat, a gomb le lesz tiltva.
11. A jelenet mintavételi száma a mintavételek **lapon számítva** . A mintavételek száma határozza meg, hogy hány szimulációt kell futtatni a felhőben. A mintavételnél több csomópontot nem adhat meg.
12. Ez az üzenet megadja a feladatok aktuális állapotát, vagy ha a lapon hibák léptek fel, ezek a hibák.

Az aktív feladatokról, a számítási készletekről és a [Azure Portal](https://portal.azure.com)a tárterületről mindig teljes körű információkhoz juthat.

Amíg egy feladat futtatja a **sütni** gomb módosításaita megszakítási feladatokra. Ezzel a gombbal megszakíthatja a feladat végrehajtását. Egy feladat megszakítása nem vonható vissza, így nem lesz elérhető eredmény, és továbbra is díjat számítunk fel a lemondás előtt felhasznált összes Azure-beli számítási idő után.

Miután elindította a sütni-et, lezárhatja az Unreal-et. A Felhőbeli sütni a projekttől, a csomópont típusától és a csomópontok számától függően több órát is igénybe vehet. A sütni-feladatok állapota a projekt újratöltése és a akusztikai ablak megnyitása után frissül. Ha a feladatot befejezte, a rendszer letölti a kimeneti fájlt.

Az Azure-beli hitelesítő adatok biztonságosan tárolódnak a helyi gépen, és az Unreal-projekthez vannak társítva. Kizárólag az Azure-hoz való biztonságos kapcsolat létesítésére szolgálnak.

### <a name="Estimating-bake-cost"></a>Az Azure Bake Cost becslése

Ha szeretné megbecsülni, hogy egy adott sütni Milyen költségekkel jár, a **becsült számítási költségek**, azaz az időtartam, és az óradíjat a kiválasztott virtuálisgép- **csomópont** helyi pénznemében adja meg. Az eredmény nem fogja tartalmazni a csomópontok üzembe helyezéséhez szükséges csomóponti időt. Ha például a **Standard_F8s_v2** lehetőséget választja, amelynek díja $0.40/HR, a becsült számítási költségek pedig 3 óra és 57 perc, a feladathoz tartozó becsült költségek $0,40 * ~ 4 óra = ~ $1,60. A tényleges költségeket valószínűleg egy kicsit magasabbra vált, mivel a csomópontok megkezdése hosszabb időt vesz igénybe. Az óránkénti csomópont költségét a [Azure batch díjszabási](https://azure.microsoft.com/pricing/details/virtual-machines/linux) oldalán találja (válassza a "számítás optimalizált" vagy a "nagy teljesítményű számítás" lehetőséget a kategória esetében).

### <a name="reviewing-the-bake-results"></a>A Bake-eredmények áttekintése

A sütni befejezését követően győződjön meg arról, hogy a voxels és a mintavételi pontok a várt helyeken találhatók a futásidejű beépülő modul futtatásával.

## <a name="Data-Files"></a>Adatfájlok

Ez a beépülő modul négy adatfájlt hoz létre különböző pontokon. A futtatáskor csak az egyik szükséges, és a projekt Content/akusztikai mappájába kerül, amelyet a rendszer automatikusan hozzáad a projekt csomagolási útvonalához. A másik három a akusztikai adatmappa belsejében található, és nincsenek csomagolva.

* **[Project]/Config/ProjectAcoustics.cfg**: Ez a fájl tárolja azokat az adattípusokat, amelyeket az akusztikai mód felhasználói felületének mezőiben megadott. A fájl helye és neve nem módosítható. A fájlban lévő más értékek is vannak, amelyek hatással vannak a sütni-re, de a speciális felhasználókra vonatkoznak, és nem szabad módosítani őket.
* **[Project]/Content/Acoustics/[LevelName]\_AcousticsData.ace**: Ez a fájl a sütni szimuláció során jön létre, és a futtatókörnyezet által a jelenet akusztikaának megjelenítéséhez használt keresési adatait tartalmazza. A fájl helye és neve módosítható a mintavételek **lapon lévő** mezők használatával. Ha ezt a fájlt a létrehozása után szeretné átnevezni, törölje a UAsset az Unreal-projektből, nevezze át a fájlt az Unreal használatával a Fájlkezelőben, majd importálja újra a fájlt az Unreal-be egy új UAsset létrehozásához. A UAsset saját maga általi átnevezése nem fog működni.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData.vox**: Ez a fájl a voxelized akusztikai geometriáját és az anyag tulajdonságait tárolja. Kiszámítva a mintavételek lap számítás gombjának használatával. A fájl helye és neve módosítható a mintavételek **lapon lévő** mezők használatával.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config.xml**: Ez a fájl tárolja a paramétereket a mintavételek **lapon a** **számítás** gomb használatával. A fájl helye és neve módosítható a mintavételek **lapon lévő** mezők használatával.

Ügyeljen arra, hogy ne törölje az Azure-ból letöltött *. ACE fájlt. Ez a fájl nem helyreállítható, kivéve a jelenet kisütését.

## <a name="next-steps"></a>További lépések
* Az [Unreal tervezési vezérlőinek](unreal-workflow.md) megismerése
* A [Project akusztikai tervezési fogalmak](design-process.md) megismerése

