---
title: "Az előre konfigurált megoldások bemutatása | Microsoft Docs"
description: "Ebből az oktatóanyagból megtudhatja, hogyan telepíthet egy előre konfigurált Azure IoT Suite-megoldást."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: e68815c2dafc596c3560ad3fcb2a7bf96d29182b
ms.lasthandoff: 04/18/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Oktatóprogram: Az előre konfigurált megoldások bemutatása
## <a name="introduction"></a>Bevezetés
Az Azure IoT Suite [előre konfigurált megoldások][lnk-preconfigured-solutions] több Azure IoT-szolgáltatást kombinálnak, hogy általános IoT üzleti forgatókönyveket megvalósító végpontok közötti megoldásokat nyújtsanak. Az előre konfigurált *távoli figyelő* megoldás csatlakozik az eszközökhöz, és megfigyeli azokat. Ez a megoldás az eszközökről származó adatstream elemzésére használható, valamint az ezen streamre automatikusan válaszoló folyamatok létrehozásával az üzleti eredmények is javíthatók.

Ez az oktatóprogram bemutatja, hogyan építheti ki az előre konfigurált távoli figyelő megoldást. Valamint az előre konfigurált megoldás alapvető funkcióin is végigvezeti. Ezek közül számos funkcióhoz a megoldás *irányítópultján* keresztül férhet hozzá, amelyet a rendszer az előre konfigurált megoldás részeként telepít:

![Az előre konfigurált távoli figyelési megoldás irányítópultja][img-dashboard]

Az oktatóanyag elvégzéséhez aktív Azure-előfizetésre lesz szüksége.

> [!NOTE]
> Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ: [Ingyenes Azure-fiók létrehozása][lnk_free_trial].
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Amikor üzembe helyezi az előre konfigurált távoli figyelési megoldást, az előre fel van töltve olyan erőforrásokkal, amelyekkel elvégezhető egy általános távoli figyelési forgatókönyv. Ebben a forgatókönyvben a megoldáshoz csatlakoztatott több eszköz váratlan hőmérsékletértékeket jelent. A következő forgatókönyvek bemutatják, hogyan végezheti el a következőket:

* A váratlan hőmérsékletértékeket küldő eszközök azonosítása.
* Ezen eszközök konfigurálása, hogy részletesebb telemetriát küldjenek.
* A probléma megoldása ezen eszközök belső vezérlőprogramjának frissítésével.
* Annak ellenőrzése, hogy a művelet megoldotta-e a problémát.

A forgatókönyv fő előnye, hogy az összes műveletet távolról végezheti el, a megoldás irányítópultjáról. Nincs szüksége hozzá az eszközök fizikai címére.

## <a name="view-the-solution-dashboard"></a>A megoldás irányítópultjának megtekintése

A megoldás irányítópultján kezelheti az üzembe helyezett megoldást. Megtekintheti például a telemetriát, eszközöket adhat hozzá és szabályokat konfigurálhat.

1. Amikor a kiépítés befejeződött, és az előre konfigurált megoldás csempéje **Kész** állapotot jelez, válassza az **Indítás** gombot a távoli figyelési portál új lapon való megnyitásához.

    ![Az előre konfigurált megoldás indítása][img-launch-solution]

1. Alapértelmezés szerint a megoldás portálja az *irányítópultot* jeleníti meg. Az oldal bal oldali menüjével a megoldásportál más területeire navigálhat.

    ![Az előre konfigurált távoli figyelési megoldás irányítópultja][img-menu]

Az irányítópult az alábbi információkat jeleníti meg:

* A megoldáshoz csatlakoztatott egyes eszközök helyét megjelenítő térképet. A megoldás első futtatásakor 25 szimulált eszköz található. A szimulált eszközök Azure WebJobs feladatokként vannak megvalósítva, és a megoldás a Bing Térképek API-val jeleníti meg az információkat a térképen. A [gyakori kérdésekből][lnk-faq] megtudhatja, hogyan teheti dinamikussá a térképet.
* A **Telemetria előzményei** panelt, amely a páratartalommal és hőmérséklettel kapcsolatos telemetriát jelenít meg a kiválasztott eszközről közel valós időben, és összesített adatokat tartalmaz, például a maximális, minimális és átlagos páratartalmat.
* A **Riasztások előzményei** panelt, amely közelmúltbeli riasztási eseményeket jelenít meg, amikor egy telemetriaérték túllépett egy küszöbértéket. Saját riasztásokat is meghatározhat az előre konfigurált megoldás által létrehozott példák mellett.
* A **Feladatok** panelt, amely az ütemezett feladatok információit jeleníti meg. A **Felügyeleti feladatok** lapon ütemezheti a saját feladatait.

## <a name="view-alarms"></a>Riasztások megtekintése

A Riasztások előzményei panel azt jelzi, hogy öt eszköz jelent a vártnál magasabb telemetriaértékeket.

![TEENDŐ Riasztások előzményei a megoldás irányítópultján][img-alarms]

> [!NOTE]
> Ezeket a riasztásokat egy olyan szabály hozza létre, amely az előre konfigurált megoldás része. Ez a szabály riasztást hoz létre, amikor egy eszköz által küldött hőmérsékletérték 60-nál magasabb. Meghatározhat saját szabályokat és műveleteket a bal oldali menüben a [Szabályok](#add-a-rule) és a [Műveletek](#add-an-action) lehetőség kiválasztásával.

## <a name="view-devices"></a>Eszközök megtekintése

Az *eszközök* lista a megoldásban regisztrált összes eszközt megjeleníti. Az eszközlistában megtekintheti és szerkesztheti az eszközök metaadatait, eszközöket adhat hozzá vagy távolíthat el, és metódusokat hívhat meg az eszközökön. Az eszközlistában szűrheti és rendezheti is az eszközök listáját. Az eszközlistán látható oszlopokat szintén testre szabhatja.

1. Válassza az **Eszközök** elemet a megoldás eszközlistájának megjelenítéséhez.

   ![Az eszközlista megtekintése a megoldásportálon][img-devicelist]

1. Az eszközlistán először a kiépítési folyamat által létrehozott 25 szimulált eszköz látható. Hozzáadhat további szimulált eszközöket és fizikai eszközöket is a megoldáshoz.

1. Az eszközrészletek megtekintéséhez válasszon egy eszközt az eszközlistából.

   ![Az eszközrészletek megtekintése a megoldásportálon][img-devicedetails]

Az **Eszköz részletei** panel hat szakaszból áll:

* Hivatkozások listája, amelyekkel testreszabhatja az eszköz ikonját, letilthatja az eszközt, szabályt adhat hozzá, egy metódust hívhat meg vagy parancsot küldhet. Ezen parancsok (egy eszközről a felhőbe irányuló üzenetek) összehasonlításáért lásd a [felhőből egy eszközre irányuló kommunikáció útmutatóját][lnk-c2d-guidance].
* Az **Ikereszköz – címkék** szakaszban szerkesztheti az eszköz címkeértékeit. Megjelenítheti a címkeértékeket az eszközlistában, és a címkeértékekkel szűrheti az eszközlistát.
* Az **Ikereszköz – kívánt tulajdonságok** szakaszban beállíthatja az eszközre küldeni kívánt tulajdonságértékeket.
* Az **Ikereszköz – jelentett tulajdonságok** szakaszban az eszközről küldött tulajdonságértékek láthatók.
* Az **Eszköztulajdonságok** szakaszban az identitásjegyzékben lévő információk láthatók, például az eszközazonosító és a hitelesítési kulcsok.
* A **Legutóbbi feladatok** szakaszban az eszköz által legutóbb megcélzott feladatok információi láthatók.

## <a name="filter-the-device-list"></a>Az eszközlista szűrése

Szűrő használatával megjelenítheti csak azokat az eszközöket, amelyek váratlan hőmérsékletértékeket küldenek. A távoli megfigyelés előre konfigurált megoldása tartalmazza a **Nem kifogástalan eszközök** szűrőt, amellyel a 60 feletti hőmérsékleti középértékkel rendelkező eszközöket lehet megjeleníteni. Emellett [saját szűrőket is létrehozhat](#add-a-filter).

1. Az elérhető szűrők listájának megjelenítéséhez válassza a **Mentett szűrő megnyitása** lehetőséget. Ezután válassza a **Nem kifogástalan eszközök** elemet a szűrő alkalmazásához:

    ![A szűrők listájának megjelenítése][img-unhealthy-filter]

1. Az eszközlista most csak a 60 feletti hőmérsékleti középértékkel rendelkező eszközöket jeleníti meg.

    ![A nem kifogástalan eszközöket megjelenítő szűrt eszközlista megtekintése][img-filtered-unhealthy-list]

## <a name="update-desired-properties"></a>Eszköz kívánt tulajdonságainak frissítése

Sikerült tehát azonosítani néhány eszközt, amelyek javításra szorulhatnak. Tegyük fel, Ön úgy dönt, hogy a 15 másodperc nem elegendő a probléma egyértelmű diagnosztizálásához. A telemetriai gyakoriság öt másodpercre történő módosításával több adatpont nyerhető a hiba jobb felderítéséhez. Ez a beállításmódosítás a távoli eszközökre is elküldhető a megoldásportálról. Ha elvégezte a módosítást, elemezheti a hatását, majd az eredmény szerint cselekedhet.

Az alábbi lépésekkel futtathatja azt a feladatot, amellyel a megfelelő értékre módosíthatja a **TelemetryInterval** kívánt tulajdonságot az érintett eszközökön. Amikor az eszközök fogadják a **TelemetryInterval** új tulajdonságértékét, a beállításuk úgy változik meg, hogy 15 másodperc helyett öt másodpercenként küldjék a telemetriai adatokat:

1. Amikor a nem kifogástalan eszközök láthatók az eszközlistában, válassza a **Feladatütemező**, majd az **Ikereszköz szerkesztése** elemet.

1. Hívja meg a **Telemetriai időköz módosítása** feladatot.

1. Módosítsa a **Kívánt tulajdonság** (**desired.Config.TelemetryInterval**) értékét öt másodpercre.

1. Válassza az **Ütemezés** elemet.

    ![A TelemetryInterval tulajdonság módosítása öt másodperc értékre][img-change-interval]

1. A feladat végrehajtását a portál **Felügyeleti feladatok** lapján kísérheti figyelemmel.

> [!NOTE]
> Ha egy különálló eszközhöz szeretne módosítani egy kívánt tulajdonságot, feladat futtatása helyett használja az **Eszköz részletei** panel **Kívánt tulajdonságok** szakaszát.

Ez a feladat beállítja a **TelemetryInterval** kívánt tulajdonság értékét az ikereszközön a szűrővel választott összes eszközön. Az eszközök lekérdezik ezt az értéket az ikereszközről, és ennek megfelelően frissítik a működésüket. Amikor egy eszköz lekérdezi és feldolgozza az ikereszközről érkező kívánt tulajdonságot, beállítja a megfelelő jelentett értéktulajdonságot.

## <a name="invoke-methods"></a>Metódusok meghívása

Amíg a feladat fut, a nem kifogástalan eszközök listájában észreveszi, hogy az összes ilyen eszköz belső vezérlőprogramjának verziója elavult (az 1.6-os verziónál régebbi).

![A nem kifogástalan eszközök belső vezérlőprogramjának jelentett verziója – megtekintés][img-old-firmware]

Lehetséges, hogy a belső vezérlőprogram verziója a váratlan hőmérsékleti értékek forrása, mivel a kifogástalan eszközöket nemrég frissítette a 2.0-s verzióra. A **Régi belső vezérlőprogrammal rendelkező eszközök** beépített szűrővel azonosíthatók az elavult belsővezérlőprogram-verziót használó eszközök. A portálról ezután távolról frissítheti az összes olyan eszközt, amelyen még a belső vezérlőprogram régi verziói futnak:

1. Az elérhető szűrők listájának megjelenítéséhez válassza a **Mentett szűrő megnyitása** lehetőséget. Ezután válassza a **Régi belső vezérlőprogrammal rendelkező eszközök** elemet a szűrő alkalmazásához:

    ![A szűrők listájának megjelenítése][img-old-filter]

1. Az eszközlista most csak a régi verziójú belső vezérlőprogrammal rendelkező eszközöket jeleníti meg. Ez a lista a **Nem kifogástalan eszközök** szűrővel azonosított öt eszköz mellett három további eszközt tartalmaz:

    ![A régi eszközöket megjelenítő szűrt eszközlista megtekintése][img-filtered-old-list]

1. Válassza a **Feladatütemező**, majd a **Metódus meghívása** elemet.

1. A **Feladat neve** értéket állítsa a következőre: **Belső vezérlőprogram frissítése a 2.0-s verzióra**.

1. A **Metódus** beállításhoz válassza az **InitiateFirmwareUpdate** értéket.

1. Az **FwPackageUri** paramétert állítsa a következőre: **https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin**.

1. Válassza az **Ütemezés** elemet. Az alapértelmezett beállítás a feladat azonnali futtatása.

    ![Feladat létrehozása a kijelölt eszközök belső vezérlőprogramjának frissítéséhez][img-method-update]

> [!NOTE]
> Ha egy különálló eszközhöz szeretne meghívni egy metódust, feladat futtatása helyett használja az **Eszköz részletei** panel **Metódusok** elemét.

Ez a feladat meghívja az **InitiateFirmwareUpdate** közvetlen metódust a szűrővel választott összes eszköz esetében. Az eszközök azonnal választ küldenek az IoT Hubnak, majd aszinkron módon elindítják a belső vezérlőprogram frissítésének folyamatát. Az eszköz a jelentett tulajdonságértékeken keresztül biztosít állapotinformációt a belső vezérlőprogram frissítésének folyamatáról, ahogy a következő képernyőképeken is látható. A **Frissítés** ikonnal frissítheti az információkat az eszköz- és feladatlistákon:

![A belső vezérlőprogram épp futó frissítésének listáját megjelenítő feladatlista][img-update-1]
![A belső vezérlőprogram frissítésének állapotát megjelenítő eszközlista][img-update-2]
![A belső vezérlőprogram frissítésének befejezett listáját megjelenítő feladatlista][img-update-3]

> [!NOTE]
> Éles környezetben a feladatokat lehet úgy ütemezni, hogy a tervezett karbantartási időszakban fussanak.

## <a name="scenario-review"></a>Forgatókönyv áttekintése

Ebben a forgatókönyvben azonosított egy lehetséges hibát néhány távoli eszközével kapcsolatban, amihez az irányítópult riasztáselőzményei mellett egy szűrőt használt. Ezután a szűrő és egy feladat alkalmazásával távolról beállította az eszközöket úgy, hogy több információt biztosítsanak a probléma azonosításához. Végül egy szűrő és egy feladat segítségével karbantartást ütemezett az érintett eszközökön. Ha visszatér az irányítópulthoz, ellenőrizheti, hogy érkezett-e további riasztás az érintett eszközökről. Egy szűrővel ellenőrizheti, hogy a belső vezérlőprogram naprakész-e a megoldás összes eszközén, és meggyőződhet arról, hogy nincsenek további nem kifogástalan állapotú eszközök:

![Szűrő, amely kimutatja, hogy az összes eszköz belső vezérlőprogramja naprakész][img-updated]

![Szűrő, amely kimutatja, hogy az összes eszköz kifogástalan állapotú][img-healthy]

## <a name="other-features"></a>Egyéb jellemzők

A következő szakaszokban az előre konfigurált távoli figyelési megoldás néhány egyéb jellemzőjéről lesz szó, amelyek nem szerepeltek az előző forgatókönyvben.

### <a name="customize-columns"></a>Oszlopok testreszabása

Az eszközlistán látható információk testreszabásához válassza ki az **Oszlopszerkesztőt**. Hozzáadhat és eltávolíthat jelentett tulajdonságot és címkeértékeket megjelenítő oszlopokat. Átrendezheti és át is nevezheti az oszlopokat:

   ![Az Oszlopszerkesztő az eszközlistában][img-columneditor]

### <a name="customize-the-device-icon"></a>Az eszközikon testreszabása

Az **Eszköz részletei** panelen szabhatja testre az eszközlistában megjelenő eszközikont, a következő módon:

1. Válassza a ceruza ikont az eszköz **Rendszerkép szerkesztése** panelének megnyitásához:

   ![Eszközkép szerkesztőjének megnyitása][img-startimageedit]

1. Töltsön fel egy új rendszerképet vagy használja az egyik meglévő rendszerképet, majd válassza a **Mentés** parancsot:

   ![Eszközkép szerkesztőjének szerkesztési folyamata][img-imageedit]

1. A kiválasztott kép most megjelenik az eszköz **Ikon** oszlopában.

> [!NOTE]
> A rendszer a képet a Blob Storage-ban tárolja. Az ikereszközben lévő címke tartalmazza a Blob Storage-ban lévő kép hivatkozását.

### <a name="add-a-device"></a>Eszköz hozzáadása

Az előre konfigurált megoldás üzembe helyezésekor az eszközlistában látható 25 mintaeszköz kiosztása automatikusan megtörténik. Ezek az eszközök Azure WebJobs-feladatban futó, *szimulált eszközök*. A szimulált eszközök megkönnyítik az előre konfigurált megoldással történő kísérletezést, anélkül, hogy valódi, fizikai eszközök üzembe helyezésére lenne szükség. Valós eszközöknek a megoldáshoz történő csatlakoztatásáról a következő oktatóanyagban olvashat: [Az eszköz csatlakoztatása az előre konfigurált távoli figyelési megoldáshoz][lnk-connect-rm].

Az alábbi lépések bemutatják, hogyan adhat szimulált eszközt a megoldáshoz:

1. Lépjen vissza az eszközlistára.

1. Egy eszköz hozzáadásához válassza a bal alsó sarokban lévő **+ Eszköz hozzáadása** gombot.

   ![Eszköz hozzáadása az előre konfigurált megoldáshoz][img-adddevice]

1. Válassza a **Szimulált eszköz** csempe **Új hozzáadása** elemét.

   ![Az új eszköz adatainak megadása az irányítópulton][img-addnew]

   Új szimulált eszköz létrehozása mellett fizikai eszközt is hozzáadhat egy **Egyéni eszköz** létrehozásával. További információ fizikai eszközöknek a megoldáshoz csatlakoztatásáról: [Az eszköz csatlakoztatása az IoT Suite előre konfigurált távoli figyelési megoldáshoz][lnk-connect-rm].

1. Válassza a **Meghatározom a saját eszközazonosítómat** elemet, és írjon be egy egyéni eszközazonosító nevet, például a **mydevice_01** nevet.

1. Válassza a **Létrehozás** elemet.

   ![Új eszköz mentése][img-definedevice]

1. A **Szimulált eszköz hozzáadása** folyamat 3. lépésében válassza a **Kész** gombot az eszközlistához való visszatéréshez.

1. Az eszközlistában láthatja a **futó** eszközét.

    ![Új eszköz megtekintése az eszközlistában][img-runningnew]

1. Az új eszköz szimulált telemetriáját is megtekintheti az irányítópulton:

    ![Az új eszköz telemetriájának megtekintése][img-runningnew-2]

### <a name="disable-and-delete-a-device"></a>Eszköz letiltása és törlése

Letilthatja az eszközöket, és a letiltásuk után eltávolíthatja őket:

![Eszköz letiltása és eltávolítása][img-disable]

### <a name="add-a-rule"></a>Szabály hozzáadása

A most hozzáadott új eszköznek még nincsenek szabályai. Ebben a szakaszban olyan szabályt ad hozzá, amely riasztást küld, amikor az új eszköz által jelentett hőmérséklet meghaladja a 47 fokot. Mielőtt elkezdené, figyelje meg, hogy az irányítópulton az új eszköz telemetriaelőzményei szerint az eszköz hőmérséklete soha nem lép túl 45 fokon.

1. Lépjen vissza az eszközlistára.

1. Egy szabály hozzáadásához válassza ki az új eszközt az **Eszközlistában**, majd válassza a **Szabály hozzáadása** gombot.

1. Hozzon létre egy szabályt, amely a **Hőmérsékletet** használja adatmezőként és az **AlarmTemp** elemet használja kimenetként, amikor a hőmérséklet meghaladja a 47 fokot:

    ![Eszközszabály hozzáadása][img-adddevicerule]

1. A módosítások mentéséhez válassza a **Mentés és szabályok megtekintése** parancsot.

1. Az új eszköz részleteit tartalmazó panelen válassza a **Parancsok** elemet.

    ![Eszközszabály hozzáadása][img-adddevicerule2]

1. Válassza a **ChangeSetPointTemp** parancsot a parancslistából, és a **SetPointTemp** értékét állítsa 45-re. Ezután válassza a **Parancs küldése** elemet:

    ![Eszközszabály hozzáadása][img-adddevicerule3]

1. Lépjen vissza az irányítópultra. Rövid idő után új bejegyzést fog látni a **Riasztások előzményei** panelen, amikor az új eszköz hőmérséklete meghaladja a 47 fokos küszöbértéket:

    ![Eszközszabály hozzáadása][img-adddevicerule4]

1. Az irányítópult **Szabályok** oldalán tekintheti át és szerkesztheti az összes szabályt:

    ![Eszközszabályok listázása][img-rules]

1. Az irányítópult **Műveletek** oldalán tekintheti át és szerkesztheti a szabályra adott válaszként elvégezhető összes műveletet:

    ![Eszközműveletek listázása][img-actions]

> [!NOTE]
> Olyan műveletek határozhatók meg, amelyek e-mail-üzenetet vagy SMS-t küldenek egy szabályra válaszul, vagy üzletági rendszerrel integrálhatók egy [logikai alkalmazáson][lnk-logic-apps] keresztül. További információ: [Logikai alkalmazás csatlakoztatása az Azure IoT Suite előre konfigurált távoli figyelési megoldásához][lnk-logicapptutorial].

### <a name="manage-filters"></a>Szűrők kezelése

Az eszközlistában létrehozhat, menthet és újratölthet szűrőket az IoT Hubhoz csatlakoztatott eszközök testreszabott listájának megjelenítéséhez. Szűrő létrehozása:

1. Válassza az eszközlista feletti szűrő szerkesztése ikont:

    ![A szűrőszerkesztő megnyitása][img-editfiltericon]

1. A **Szűrőszerkesztő** szakaszban adjon hozzá mezőket, operátorokat és értékeket az eszközlista szűréséhez. Több záradékot is hozzáadhat a szűrő pontosítása érdekében. Válassza a **Szűrés** parancsot a szűrő alkalmazásához:

    ![Szűrő létrehozása][img-filtereditor]

1. Ebben a példában a listát gyártó és modellszám alapján szűrjük:

    ![Szűrt lista][img-filterelist]

1. Ha egyéni névvel szeretné menteni a szűrőt, válassza a **Mentés másként** ikont:

    ![Szűrő mentése][img-savefilter]

1. Ha egy korábban mentett szűrőt szeretne újból alkalmazni, válassza a **Mentett szűrő megnyitása** ikont:

    ![Szűrő megnyitása][img-openfilter]

Eszközazonosító, eszközállapot, kívánt tulajdonságok, jelentett tulajdonságok és címkék alapján hozhat létre szűrőket. Az **Eszköz részletei** panel **Címkék** szakaszában egyéni címkéket adhat az eszközhöz, egy feladat futtatásával pedig egyszerre több eszközön frissítheti a címkéket.

> [!NOTE]
> A **Szűrőszerkesztő** **Speciális nézetében** szerkesztheti közvetlenül a lekérdezés szövegét.

### <a name="commands"></a>Parancsok

Az **Eszköz részletei** panelen parancsokat küldhet az eszközre. Amikor egy eszköz először elindul, információkat küld a megoldásnak az általa támogatott parancsokról. A parancsok és metódusok közötti különbségekről [az Azure IoT Hub felhő és eszköz közötti kommunikációs lehetőségeiről][lnk-c2d-guidance] szóló cikkben talál további információt.

1. A kiválasztott eszköz **Eszköz részletei** panelén válassza a **Parancsok** elemet:

   ![Eszközparancsok az irányítópulton][img-devicecommands]

1. Válassza a **PingDevice** parancsot a parancslistából.

1. Válassza a **Parancs küldése** elemet.

1. A parancs előzményeiben láthatja a parancs állapotát.

   ![Parancsállapot az irányítópulton][img-pingcommand]

A megoldás nyomon követi az általa küldött összes parancs állapotát. Az eredmény kezdetben **Függőben** állapotú. Amikor az eszköz jelenti, hogy végrehajtotta a parancsot, az eredmény **Sikeres** állapotra vált.

## <a name="behind-the-scenes"></a>A színfalak mögött

Előre konfigurált megoldás üzembe helyezésekor az üzembehelyezési folyamat több erőforrást hoz létre a kiválasztott Azure-előfizetésben. Ezeket az erőforrásokat az Azure [Portalon][lnk-portal] tekintheti meg. Az üzembe helyezési folyamat létrehoz egy **erőforráscsoportot** az előre konfigurált megoldáshoz kiválasztott néven alapuló névvel:

![Előre konfigurált megoldás az Azure Portalon][img-portal]

Az egyes erőforrások beállításainak megtekintéséhez válassza ki az erőforrást az erőforráscsoport erőforráslistájában.

Megtekintheti az előre konfigurált megoldás forráskódját is. Az előre konfigurált távoli figyelési megoldás forráskódja az [azure-iot-remote-monitoring][lnk-rmgithub] GitHub-adattárban található:

* A **DeviceAdministration** mappa tartalmazza az irányítópult forráskódját.
* A **Simulator** mappa tartalmazza a szimulált eszköz forráskódját.
* Az **EventProcessor** mappa tartalmazza a bejövő telemetriát kezelő háttérfolyamat forráskódját.

Amikor elkészült, törölheti az előre konfigurált megoldást az Azure-előfizetésből az [azureiotsuite.com][lnk-azureiotsuite] webhelyen. Így könnyedén törölheti az előre konfigurált megoldás létrehozásakor megkapott összes erőforrást.

> [!NOTE]
> Ahhoz, hogy biztosan törölje az előre konfigurált megoldáshoz kapcsolódó összes elemet, törölje a megoldást az [azureiotsuite.com][lnk-azureiotsuite] webhelyről, és ne törölje az erőforráscsoportot a portálon.

## <a name="next-steps"></a>Következő lépések

Most, hogy üzembe helyezett egy működő előre konfigurált megoldást, a következő cikkek elolvasásával folytathatja az ismerkedést az IoT Suite használatával:

* [A távoli figyelési előre konfigurált megoldás bemutatója][lnk-rm-walkthrough]
* [Az eszköz csatlakoztatása az előre konfigurált távoli figyelési megoldáshoz][lnk-connect-rm]
* [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-menu]: media/iot-suite-getstarted-preconfigured-solutions/menu.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-alarms]: media/iot-suite-getstarted-preconfigured-solutions/alarms.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit2.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-unhealthy-filter]: media/iot-suite-getstarted-preconfigured-solutions/unhealthyfilter.png
[img-filtered-unhealthy-list]: media/iot-suite-getstarted-preconfigured-solutions/unhealthylist.png
[img-change-interval]: media/iot-suite-getstarted-preconfigured-solutions/changeinterval.png
[img-old-firmware]: media/iot-suite-getstarted-preconfigured-solutions/noticeold.png
[img-old-filter]: media/iot-suite-getstarted-preconfigured-solutions/oldfilter.png
[img-filtered-old-list]: media/iot-suite-getstarted-preconfigured-solutions/oldlist.png
[img-method-update]: media/iot-suite-getstarted-preconfigured-solutions/methodupdate.png
[img-update-1]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate1.png
[img-update-2]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate2.png
[img-update-3]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate3.png
[img-updated]: media/iot-suite-getstarted-preconfigured-solutions/updated.png
[img-healthy]: media/iot-suite-getstarted-preconfigured-solutions/healthy.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-faq]: iot-suite-faq.md
