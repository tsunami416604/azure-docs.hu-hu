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
ms.sourcegitcommit: 9e1bcba086a9f70c689a5d7d7713a8ecdc764492
ms.openlocfilehash: 8248e0a02cb0775a87f0c8130e53b98f8bcfe581
ms.lasthandoff: 02/27/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Oktatóprogram: Az előre konfigurált megoldások bemutatása
## <a name="introduction"></a>Bevezetés
Az Azure IoT Suite [előre konfigurált megoldások][lnk-preconfigured-solutions] több Azure IoT-szolgáltatást kombinálnak, hogy általános IoT üzleti forgatókönyveket megvalósító végpontok közötti megoldásokat nyújtsanak. Az előre konfigurált *távoli figyelő* megoldás csatlakozik az eszközökhöz, és megfigyeli azokat. Ez a megoldás az eszközökről származó adatstream elemzésére használható, valamint az ezen streamre automatikusan válaszoló folyamatok létrehozásával az üzleti eredmények is javíthatók.

Ez az oktatóprogram bemutatja, hogyan építheti ki az előre konfigurált távoli figyelő megoldást. Valamint az előre konfigurált megoldás alapvető funkcióin is végigvezeti. Ezek közül számos funkcióhoz a megoldás irányítópultján keresztül férhet hozzá, amelyet a rendszer az előre konfigurált megoldás részeként telepít:

![Az előre konfigurált távoli figyelési megoldás irányítópultja][img-dashboard]

Az oktatóanyag elvégzéséhez aktív Azure-előfizetésre lesz szüksége.

> [!NOTE]
> Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ: [Ingyenes Azure-fiók létrehozása][lnk_free_trial].
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>A megoldás irányítópultjának megtekintése
A megoldás irányítópultján kezelheti az üzembe helyezett megoldást. Megtekintheti például a telemetriát, eszközöket adhat hozzá és szabályokat konfigurálhat.

1. Amikor a kiépítés befejeződött, és az előre konfigurált megoldás csempéje **Kész** állapotot jelez, kattintson az **Indítás** gombra a távoli figyelési megoldás új lapon való megnyitásához.
   
   ![Az előre konfigurált megoldás indítása][img-launch-solution]
2. Alapértelmezés szerint a megoldás portálja a *megoldás irányítópultját* jeleníti meg. A bal oldali menüből választhat más nézeteket.
   
   ![Az előre konfigurált távoli figyelési megoldás irányítópultja][img-dashboard]

Az irányítópult az alábbi információkat jeleníti meg:

* A térkép a megoldáshoz csatlakoztatott egyes eszközök helyét jeleníti meg. A megoldás első futtatásakor négy szimulált eszköz van. A szimulált eszközök Azure WebJobs feladatokként vannak megvalósítva, és a megoldás a Bing Térképek API-val jeleníti meg az információkat a térképen.
* A **Telemetria előzményei** panel a páratartalommal és hőmérséklettel kapcsolatos telemetriát jelenít meg a kiválasztott eszközről közel valós időben, és összesített adatokat tartalmaz, például a maximális, minimális és átlagos páratartalmat.
* A **Riasztások előzményei** panel közelmúltbeli riasztási eseményeket jelenít meg, amikor egy telemetriaérték túllépett egy küszöbértéken. Saját riasztásokat is meghatározhat az előre konfigurált megoldás által létrehozott példák mellett.
* A **Feladatok** panel az ütemezett feladatok információit jeleníti meg. A **Felügyeleti feladatok** lapon ütemezheti a saját feladatait.

## <a name="view-the-device-list"></a>Az eszközlista megtekintése
Az *eszközlista* a megoldásban regisztrált összes eszközt megjeleníti. Az eszközlistában megtekintheti és szerkesztheti az eszközök metaadatait, eszközöket adhat hozzá vagy távolíthat el, és metódusokat hívhat meg az eszközökön.

1. Kattintson a bal oldali menü **Eszközök** elemére a megoldás eszközlistájának megjelenítéséhez.
   
   ![Az eszközök listája az irányítópulton][img-devicelist]
2. Az eszközlistán először a kiépítési folyamat által létrehozott négy szimulált eszköz látható. Hozzáadhat további szimulált eszközöket és fizikai eszközöket is a megoldáshoz.
3. Az eszközlistán látható információk testreszabásához kattintson az **Oszlopszerkesztőre**. Hozzáadhat és eltávolíthat jelentett tulajdonságot és címkeértékeket megjelenítő oszlopokat. Átrendezheti és át is nevezheti az oszlopokat:
   
   ![Oszlopszerkesztő az irányítópulton][img-columneditor]
4. Az eszközrészletek megtekintéséhez kattintson egy eszközre az eszközlistában.
   
   ![Eszközadatok az irányítópulton][img-devicedetails]

Az **Eszköz részletei** panel hat szakaszból áll:

* Hivatkozások listája, amelyekkel testreszabhatja az eszköz ikonját, letilthatja az eszközt, szabályt adhat hozzá, egy metódust hívhat meg vagy parancsot küldhet. Ezen parancsok (egy eszközről a felhőbe irányuló üzenetek) összehasonlításáért lásd a [felhőből egy eszközre irányuló kommunikáció útmutatóját][lnk-c2d-guidance].
* Az **Ikereszköz – címkék** szakaszban szerkesztheti az eszköz címkeértékeit. Megjelenítheti a címkeértékeket az eszközlistában, és a címkeértékekkel szűrheti az eszközlistát.
* Az **Ikereszköz – kívánt tulajdonságok** szakaszban beállíthatja az eszközre küldeni kívánt tulajdonságértékeket.
* Az **Ikereszköz – jelentett tulajdonságok** szakaszban az eszközről küldött tulajdonságértékek láthatók.
* Az **Eszköztulajdonságok** szakaszban az identitásjegyzékben lévő információk láthatók, például az eszközazonosító és a hitelesítési kulcsok.
* A **Legutóbbi feladatok** szakaszban az eszköz által legutóbb megcélzott feladatok információi láthatók.

## <a name="customize-the-device-icon"></a>Az eszközikon testreszabása

Az **Eszköz részletei** panelen szabhatja testre az eszközlistában megjelenő eszközikont, a következő módon:

1. Kattintson a ceruza ikonra az eszköz **Kép szerkesztése** panelének megnyitásához:
   
   ![Eszközkép szerkesztőjének megnyitása][img-startimageedit]
2. Töltsön fel egy új képet, vagy használja az egyik meglévő képet, majd kattintson a **Mentés** parancsra:
   
   ![Eszközkép szerkesztőjének szerkesztési folyamata][img-imageedit]
3. A kiválasztott kép most megjelenik az eszköz **Ikon** oszlopában.

> [!NOTE]
> A rendszer a képet a Blob Storage-ban tárolja. Az ikereszközben lévő címke tartalmazza a Blob Storage-ban lévő kép hivatkozását.
> 
> 

## <a name="invoke-a-method-on-a-device"></a>Metódus meghívása az eszközön
Az **Eszköz részletei** panelen metódusokat hívhat meg az eszközön. Amikor egy eszköz először elindul, információkat küld a megoldásnak az általa támogatott metódusokról.

1. Kattintson a kiválasztott eszköz **Eszköz részletei** panelének **Metódusok** elemére:
   
   ![Eszközmetódusok az irányítópulton][img-devicemethods]
2. Válassza a metódusok listájában az **Újraindítás** elemet.
3. Kattintson a **Metódus meghívása** parancsra.
4. A metódus előzményeiben láthatja a metódus meghívásának állapotát.
   
   ![Metódusállapot az irányítópulton][img-pingmethod]

A megoldás nyomon követi az általa indított összes metódus állapotát. Amikor az eszköz befejezi a metódus futtatását, egy új bejegyzést lát a metóduselőzmények táblázatában.

Egyes metódusok aszinkron feladatokat indítanak az eszközön. Az **InitiateFirmwareUpdate** metódus például egy aszinkron feladatot indít a frissítés elvégzéséhez. Az eszköz jelentett tulajdonságokkal jelenti a belső vezérlőprogram frissítésének állapotát annak futtatása során.

## <a name="send-a-command-to-a-device"></a>Parancs elküldése egy eszközre
Az **Eszköz részletei** panelen parancsokat küldhet az eszközre. Amikor egy eszköz először elindul, információkat küld a megoldásnak az általa támogatott parancsokról.

1. A kiválasztott eszköz **Eszköz részletei** panelén kattintson a **Parancsok** elemre:
   
   ![Eszközparancsok az irányítópulton][img-devicecommands]
2. Válassza a **PingDevice** parancsot a parancslistából.
3. Kattintson a **Parancs küldése** parancsra.
4. A parancs előzményeiben láthatja a parancs állapotát.
   
   ![Parancsállapot az irányítópulton][img-pingcommand]

A megoldás nyomon követi az általa küldött összes parancs állapotát. Az eredmény kezdetben **Függőben** állapotú. Amikor az eszköz jelenti, hogy végrehajtotta a parancsot, az eredmény **Sikeres** állapotra vált.

## <a name="add-a-new-simulated-device"></a>Új szimulált eszköz hozzáadása
Az előre konfigurált megoldás üzembe helyezésekor az eszközlistában látható négy mintaeszköz kiosztása automatikusan megtörténik. Ezek az eszközök Azure WebJobs-feladatban futó, *szimulált eszközök*. A szimulált eszközök megkönnyítik az előre konfigurált megoldással történő kísérletezést, anélkül, hogy valódi, fizikai eszközök üzembe helyezésére lenne szükség. Valós eszközöknek a megoldáshoz történő csatlakoztatásáról a következő oktatóanyagban olvashat: [Az eszköz csatlakoztatása az előre konfigurált távoli figyelési megoldáshoz][lnk-connect-rm].

Az alábbi lépések bemutatják, hogyan adhat szimulált eszközt a megoldáshoz:

1. Lépjen vissza az eszközlistára.
2. Egy eszköz hozzáadásához kattintson a bal alsó sarokban lévő **+ Eszköz hozzáadása** gombra.
   
   ![Eszköz hozzáadása az előre konfigurált megoldáshoz][img-adddevice]
3. Kattintson a **Szimulált eszköz** csempe **Új hozzáadása** elemére.
   
   ![Az új eszköz adatainak megadása az irányítópulton][img-addnew]
   
   Új szimulált eszköz létrehozása mellett fizikai eszközt is hozzáadhat egy **Egyéni eszköz** létrehozásával. További információ fizikai eszközöknek a megoldáshoz csatlakoztatásáról: [Az eszköz csatlakoztatása az IoT Suite előre konfigurált távoli figyelési megoldáshoz][lnk-connect-rm].
4. Válassza a **Meghatározom a saját eszközazonosítómat** elemet, és írjon be egy egyéni eszközazonosító nevet, például a **mydevice_01** nevet.
5. Kattintson a **Létrehozás** gombra.
   
   ![Új eszköz mentése][img-definedevice]
6. A **Szimulált eszköz hozzáadása** folyamat 3. lépésében kattintson a **Kész** gombra az eszközlistához való visszatéréshez.
7. Az eszközlistában láthatja a **futó** eszközét.
   
    ![Új eszköz megtekintése az eszközlistában][img-runningnew]
8. Az új eszköz szimulált telemetriáját is megtekintheti az irányítópulton:
   
    ![Az új eszköz telemetriájának megtekintése][img-runningnew-2]

## <a name="device-properties"></a>Eszköztulajdonságok
A előre konfigurált távoli figyelési megoldás [ikereszközökkel][lnk-device-twin] szinkronizálja az eszköz metaadatait az eszközök és a megoldás háttérrendszere között. Az ikereszközök egyéni eszközök tulajdonságértékeit tároló, az IoT Hubon tárolt JSON-dokumentumok. Az eszközök rendszeres időközönként, *jelentett tulajdonságokként* küldenek metaadatokat a megoldás háttérrendszerének, hogy azokat az ikereszköz tárolja. A megoldás háttérrendszere beállíthatja a *kívánt tulajdonságokat* az ikereszközön, hogy a metaadatok frissítését elküldje az eszközöknek. A jelentett tulajdonságok az eszköz által legutóbb küldött metaadatértékeket jelenítik meg. További információ: [Eszközidentitás-jegyzék, ikereszköz és DocumentDB][lnk-devicemetadata].

> [!NOTE]
> Az eszköz DocumentDB-adatbázissal is tárolja a parancsokhoz és metódusokhoz kapcsolódó eszközadatokat.
> 
> 

1. Lépjen vissza az eszközlistára.
2. Válassza ki az új eszközt az **Eszközlistában**, majd kattintson a **Szerkesztés** elemre az **Ikereszköz – kívánt tulajdonságok** szerkesztéséhez:
   
   ![Eszköz kívánt tulajdonságainak szerkesztése][img-editdevice]
3. Állítsa a **Kívánt tulajdonság nevét** **Szélesség** névre, az értékét pedig **47.639521** értékre. Ezután kattintson az **Eszközjegyzék módosításainak mentése** elemre.
   
    ![Az eszköz kívánt tulajdonságának szerkesztése][img-editdevice2]
4. Az **Eszköz részletei** panelen az új szélességérték először egy kívánt tulajdonságként jelenik meg, a régi szélességérték pedig jelentett tulajdonságként jelenik meg:
   
    ![Jelentett tulajdonság megtekintése][img-editdevice3]
5. Jelenleg az előre konfigurált megoldásban a szimulált eszközök csak a **Desired.Config.TemperatureMeanValue** és a **Desired.Config.TelemetryInterval** kívánt tulajdonságot dolgozzák fel. A valós eszközöknek az összes kívánt tulajdonságot be kell olvasniuk az IoT Hubról, módosítaniuk kell annak konfigurációját, és jelentett tulajdonságokként jelenteniük kell az új értékeket az IoT Hubnak.

Az **Eszköz részletei** panelen az **Ikereszköz – címkék** szakaszt ugyanúgy szerkesztheti, ahogyan az **Ikereszköz – kívánt tulajdonságok** szakaszt. De a kívánt tulajdonságokkal ellentétben a címkék nincsenek szinkronizálva az eszközzel. A címkék csak az ikereszközön léteznek az IoT Hubon. A címkék az eszközlistában egyéni szűrők építéséhez hasznosak.

## <a name="sort-the-device-list"></a>Az eszközlista rendezése

Az eszközlista rendezéséhez kattintson egy oszlopfejléc mellé. Az első kattintás növekvő sorrendű, a második kattintás csökkenő sorrendű rendezést eredményez:

![Eszközlista rendezése][img-sortdevices]

## <a name="filter-the-device-list"></a>Az eszközlista szűrése

Az eszközlistában létrehozhat, menthet és újratölthet szűrőket az IoT Hubhoz csatlakoztatott eszközök testreszabott listájának megjelenítéséhez. Szűrő létrehozása:

1. Kattintson az eszközlista feletti szűrő szerkesztése ikonra:
   
   ![A szűrőszerkesztő megnyitása][img-editfiltericon]
2. A **Szűrőszerkesztő** szakaszban adjon hozzá mezőket, operátorokat és értékeket az eszközlista szűréséhez. Több záradékot is hozzáadhat a szűrő pontosítása érdekében. Kattintson a **Szűrés** parancsra a szűrő alkalmazásához:
   
   ![Szűrő létrehozása][img-filtereditor]
3. Ebben a példában a listát gyártó és modellszám alapján szűrjük:
   
   ![Szűrt lista][img-filterelist]
4. Ha egyéni névvel szeretné menteni a szűrőt, kattintson a **Mentés másként** ikonra:
   
   ![Szűrő mentése][img-savefilter]
5. Ha egy korábban mentett szűrőt szeretne újból alkalmazni, kattintson a **Mentett szűrő megnyitása** ikonra:
   
   ![Szűrő megnyitása][img-openfilter]

Eszközazonosító, eszközállapot, kívánt tulajdonságok, jelentett tulajdonságok és címkék alapján hozhat létre szűrőket.

> [!NOTE]
> A **Szűrőszerkesztő** **Speciális nézetében** szerkesztheti közvetlenül a lekérdezés szövegét.
> 
> 

## <a name="add-a-rule-for-the-new-device"></a>Szabály hozzáadása az új eszközhöz
A most hozzáadott új eszköznek még nincsenek szabályai. Ebben a szakaszban olyan szabályt ad hozzá, amely riasztást küld, amikor az új eszköz által jelentett hőmérséklet meghaladja a 47 fokot. Mielőtt elkezdené, figyelje meg, hogy az irányítópulton az új eszköz telemetriaelőzményei szerint az eszköz hőmérséklete soha nem lép túl 45 fokon.

1. Lépjen vissza az eszközlistára.
2. Egy szabály hozzáadásához válassza ki az új eszközt az **Eszközlistában**, majd kattintson a **Szabály hozzáadása** gombra.
3. Hozzon létre egy szabályt, amely a **Hőmérsékletet** használja adatmezőként és az **AlarmTemp** elemet használja kimenetként, amikor a hőmérséklet meghaladja a 47 fokot:
   
    ![Eszközszabály hozzáadása][img-adddevicerule]
4. A módosítások mentéséhez kattintson a **Mentés és szabályok megtekintése** parancsra.
5. Az új eszköz részleteit tartalmazó panelen kattintson a **Parancsok** elemre.
   
   ![Eszközszabály hozzáadása][img-adddevicerule2]
6. Válassza a **ChangeSetPointTemp** parancsot a parancslistából, és a **SetPointTemp** értékét állítsa 45-re. Ezután kattintson a **Parancs küldése** parancsra:
   
   ![Eszközszabály hozzáadása][img-adddevicerule3]
7. Lépjen vissza a megoldás irányítópultjára. Rövid idő után új bejegyzést fog látni a **Riasztások előzményei** panelen, amikor az új eszköz hőmérséklete meghaladja a 47 fokos küszöbértéket:
   
   ![Eszközszabály hozzáadása][img-adddevicerule4]
8. Az irányítópult **Szabályok** oldalán tekintheti át és szerkesztheti az összes szabályt:
   
    ![Eszközszabályok listázása][img-rules]
9. Az irányítópult **Műveletek** oldalán tekintheti át és szerkesztheti a szabályra adott válaszként elvégezhető összes műveletet:
   
    ![Eszközműveletek listázása][img-actions]

> [!NOTE]
> Olyan műveletek határozhatók meg, amelyek e-mail-üzenetet vagy SMS-t küldenek egy szabályra válaszul, vagy üzletági rendszerrel integrálhatók egy [logikai alkalmazáson][lnk-logic-apps] keresztül. További információ: [Logikai alkalmazás csatlakoztatása az Azure IoT Suite előre konfigurált távoli figyelési megoldásához][lnk-logicapptutorial].
> 
> 

## <a name="disable-and-remove-devices"></a>Eszközök letiltása és eltávolítása
Letilthatja az eszközöket, és a letiltásuk után eltávolíthatja őket:

![Eszköz letiltása és eltávolítása][img-disable]

## <a name="run-jobs"></a>Feladatok futtatása
Feladatokat ütemezhet több művelet az eszközökön történő elvégzéséhez. Létrehoz egy feladatot az eszközök listájához. Ez a lista tartalmazhatja az összes eszközt, vagy lehet egy, az eszközlistában lévő [szűrőeszközökkel](#filter-the-device-list) létrehozott szűrt lista. Egy feladat metódust hívhat meg a lista összes eszközén, vagy frissítheti az eszközlistában lévő összes eszköz ikereszközét.

### <a name="create-a-job-to-invoke-a-method"></a>Feladat létrehozása metódus meghívásához

A következő lépések bemutatják, hogyan hozhat létre egy, a lista összes eszközén a belső vezérlőprogram frissítési metódusát elindító feladatot. A metódus csak a metódust támogató eszközökön lesz meghívva:

1. Az eszközlistában lévő szűrőeszközökkel hozhatja létre azon eszközök listáját, amelyek a belső vezérlőprogram frissítését fogadják:
   
   ![A szűrőszerkesztő megnyitása][img-editfiltericon]
2. A szűrt listában kattintson a **Feladatütemezőre**:
   
   ![A feladatütemező megnyitása][img-clickjobscheduler]
3. A **Feladat ütemezése** panelen kattintson a **Metódus meghívása** parancsra.
4. A **Metódus meghívása** lapon írja be a meghívni kívánt metódus részleteit, majd kattintson az **Ütemezés** parancsra:
   
   ![Metódusfeladat konfigurálása][img-invokemethodjob]

Az **InitiateFirmwareUpdate** metódus aszinkron módon indít el egy feladatot az eszközön, és azonnal visszatér. Ezután a belső vezérlőprogram frissítési folyamata jelentett tulajdonságokkal jelenti a frissítési folyamatot annak futtatása során.

### <a name="create-a-job-to-edit-the-device-twin"></a>Feladat létrehozása az ikereszköz szerkesztéséhez

A következő lépések bemutatják, hogyan hozhat létre egy, a lista összes eszközének ikereszközét szerkesztő feladatot:

1. Az eszközlistában lévő szűrőeszközökkel hozhatja létre azon eszközök listáját, amelyek az ikereszközök szerkesztéseit fogadják:
   
   ![A szűrőszerkesztő megnyitása][img-editfiltericon]
2. A szűrt listában kattintson a **Feladatütemezőre**:
   
   ![A feladatütemező megnyitása][img-clickjobscheduler]
3. A **Feladat ütemezése** panelen kattintson az **Ikereszköz szerkesztése** elemre.
4. Az **Ikereszköz szerkesztése** lapon írja be a szerkesztendő **Kívánt tulajdonságok** és **Címkék** részleteit, majd kattintson az **Ütemezés** parancsra:
   
   ![Metódusfeladat konfigurálása][img-edittwinjob]

### <a name="monitor-the-job"></a>A feladat figyelése
Figyelheti a **Felügyeleti feladatok** lapon ütemezett feladatok állapotát. A **Feladat részletei** panelen jelennek meg a kiválasztott feladattal kapcsolatos információk:
   
   ![Feladat állapotának megtekintése][img-jobstatus]

A feladatokkal kapcsolatos információkat az **irányítópulton** is megtekintheti:
   
   ![Feladatok megtekintése az irányítópulton][img-jobdashboard]


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
> 
> 

## <a name="next-steps"></a>Következő lépések
Most, hogy üzembe helyezett egy működő előre konfigurált megoldást, a következő cikkek elolvasásával folytathatja az ismerkedést az IoT Suite használatával:

* [A távoli figyelési előre konfigurált megoldás bemutatója][lnk-rm-walkthrough]
* [Az eszköz csatlakoztatása az előre konfigurált távoli figyelési megoldáshoz][lnk-connect-rm]
* [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-devicemethods]: media/iot-suite-getstarted-preconfigured-solutions/devicemethods.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-pingmethod]: media/iot-suite-getstarted-preconfigured-solutions/pingmethod.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
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
[img-sortdevices]: media/iot-suite-getstarted-preconfigured-solutions/sortdevices.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-clickjobscheduler]: media/iot-suite-getstarted-preconfigured-solutions/clickscheduler.png
[img-invokemethodjob]: media/iot-suite-getstarted-preconfigured-solutions/invokemethodjob.png
[img-edittwinjob]: media/iot-suite-getstarted-preconfigured-solutions/edittwinjob.png
[img-jobstatus]: media/iot-suite-getstarted-preconfigured-solutions/jobstatus.png
[img-jobdashboard]: media/iot-suite-getstarted-preconfigured-solutions/jobdashboard.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-device-twin-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
