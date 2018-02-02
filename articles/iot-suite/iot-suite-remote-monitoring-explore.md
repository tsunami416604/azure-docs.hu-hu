---
title: "A távoli felügyeleti megoldás - Azure beolvasása használatába |} Microsoft Docs"
description: "Ez az oktatóanyag a távoli felügyeleti előkonfigurált megoldás bevezetése szimulált forgatókönyvek használja. Ezek a forgatókönyvek a távoli felügyeleti előkonfigurált megoldás üzembe helyezéséhez, először hoz létre."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 12/12/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: d8943db3ec6ef5875b2b884d42ea25dbb44a30e5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="explore-the-capabilities-of-the-remote-monitoring-preconfigured-solution"></a>A távoli felügyeleti előkonfigurált megoldás funkcióinak felfedezése

Ez az oktatóanyag bemutatja, hogy a távoli figyelési megoldást a főbb képességei. Ezek a képességek szemléltetésére az oktatóanyag ügyfél szolgáltatást használó általános forgatókönyvhöz IoT alkalmazás szimulált Contoso nevű vállalat bővíthető.

Az oktatóanyag, valamit megismerheti a tipikus IoT-forgatókönyvek esetén a távoli felügyeleti megoldás out-of-az-box biztosít.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * Megjelenítheti és az irányítópult eszközök szűréséhez
> * Egy riasztás válaszolni
> * Az eszközök a belső vezérlőprogram frissítése
> * Az eszközök rendszerezéséhez

A következő videó bemutatja a távoli felügyeleti megoldás részletes útmutatás:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-28-An-introduction-to-Azure-IoT-through-the-new-Remote-Monitoring-Preconfigured-Solution/Player]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez kell a távoli felügyeleti megoldás telepített példányát az Azure-előfizetéshez.

Ha még nem telepítette a távoli figyelési megoldást igényelnek, még el kell végeznie a [a távoli felügyeleti előkonfigurált megoldás üzembe helyezéséhez](iot-suite-remote-monitoring-deploy.md) oktatóanyag.

## <a name="the-contoso-sample-iot-deployment"></a>A Contoso IoT üzembe helyezési minta

Használhatja a Contoso IoT üzembe helyezési minta az alapvető forgatókönyv megérteni a távoli felügyeleti megoldás biztosítja, a-kész. Ezek a forgatókönyvek a valós IoT központi telepítések alapulnak. Nagy valószínűséggel lesznek szeretné testre szabhatja a távoli felügyeleti megoldás az adott igényeknek, de a Contoso minta segít az alapvető.

> [!NOTE]
> Ha a parancssori felület az előkonfigurált megoldás, a fájl központi telepítéséhez használt `deployment-{your deployment name}-output.json` központi telepítést végez a telepített minta eléréséhez URL-CÍMÉT például információkat tartalmaz.

A Contoso minta látja a szimulált eszköz és a szabályok meg őket. Miután megismerte az alapvető forgatókönyv, felfedezése több megoldás szolgáltatásainak folytathatja [végezze el a speciális eszközfigyelés a távoli felügyeleti megoldást használni](iot-suite-remote-monitoring-monitor.md).

Contoso kezelő eszközök különböző környezetekben számos vállalat esetében. Contoso tervek hatványra emelésének felhőalapú IoT-alkalmazásokhoz használandó távoli figyelését, és több eszköz kezelését egy központi alkalmazáshoz. Az alábbi szakaszokban a kezdeti konfiguráció a Contoso minta összefoglalása:

> [!NOTE]
> A Contoso bemutató módja csak egy szimulált eszközre továbbítani, és hozzon létre. Egyéb üzembe helyezési lehetőségei közé tartozik a saját egyéni eszközök létrehozását. A saját eszközök és a szabályok létrehozásával kapcsolatos további tudnivalókért lásd: [kezelése és az eszközök](iot-suite-remote-monitoring-manage.md) és [küszöbérték-alapú szabályok használatával kapcsolatos problémák észlelése](iot-suite-remote-monitoring-automate.md).

### <a name="contoso-devices"></a>Contoso-eszközök

Contoso intelligens eszközök különböző használ. Ezek az eszközök a különféle telemetriai adatfolyamot küldő a vállalat különböző szerepkörök teljesítéséhez. Ezenkívül mindegyik eszköztípus másik eszköz tulajdonságokkal rendelkezik, és támogatott módszerek.

A következő táblázat a kiépített eszköztípusok összegzését jeleníti meg:

| Eszköztípus        | Telemetria                                  | Tulajdonságok                                  | Címkék                    | Metódusok                                                                                      |
| ------------------ | ------------------------------------------ | ------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------- |
| Chiller            | Hőmérséklet, páratartalom, nyomás            | Típus, a belső vezérlőprogram-verziója, a modell               | Hely, emelet, egyetemi | Indítsa újra a belső vezérlőprogram frissítési, vészhelyzeti szelep kiadásban növekedése nyomás                          |
| Prototípusának eszköz | Hőmérséklet, terhelés, földrajzi helyhez        | Típus, a belső vezérlőprogram-verziója, a modell               | Hely, mód          | Indítsa újra, belső vezérlőprogram frissítési áthelyezés eszköz, Stop eszköz, hőmérséklet-kiadás, hőmérséklet növelése |
| Motor             | Tartály üzemanyag szint hűtőközeg érzékelő, vibráció | Típus, a belső vezérlőprogram-verziója, a modell               | Hely, emelet, egyetemi | Indítsa újra, belső vezérlőprogram frissítési üres tartály, kitöltés tartály                                              |
| Teherautó              | Földrajzi hely, sebessége rakomány hőmérséklet     | Típus, a belső vezérlőprogram-verziója, a modell               | Hely, betöltése          | Alacsonyabb rakomány hőmérséklet, növelje rakomány hőmérséklet, belső vezérlőprogram frissítése                         |
| Foglalhatja           | Emelet, vibráció, hőmérséklet              | Típusa, a belső vezérlőprogram-verziója, Model, földrajzi helyhez | Hely, a központ        | Állítsa le a foglalhatja, kezdő foglalhatja belső vezérlőprogram frissítése                                               |

> [!NOTE]
> A Contoso bemutató mintát rendelkezések két eszköz típusonkénti. Minden, egy megfelelően működik-e a szokásos módon határozzák meg a Contoso határain belül, és a van valamilyen hibásan működik. A következő szakaszban megismerheti az eszközök határozza meg a Contoso szabályok. Ezek a szabályok határozza meg a megfelelő működés határait.

### <a name="contoso-rules"></a>Contoso szabályok

A Contoso operátorok ismeri a küszöbértékeket, amelyek meghatározzák, hogy egy eszköz megfelelően működik-e. Például egy hűtő nem működik megfelelően, ha a terhelés, amely akkor jelent érték nagyobb, mint 250 PSI. Az alábbi táblázat minden egyes eszköz típusa határozza meg a Contoso küszöbérték-alapú szabályok:

| Szabály neve | Leírás | Küszöbérték | Súlyosság | Érintett eszközök |
| --------- | ----------- | --------- | -------- | ---------------- |
| Hűtő nyomás túl magas | Riasztást küld, ha hőmérsékletű elérni nagyobbnak, mint a szokásos terhelés szintek   |P > 250 psi       | Kritikus | Hőmérsékletű            |
| Prototípusának eszköz temp túl magas  | Riasztást küld, ha prototípusának eszközök elérni nagyobbnak, mint a normál hőmérséklet szintek  |T>80&deg; F |Kritikus | Prototípusának eszközök |
| Üres motor tartály  | Riasztást küld, ha motor tüzelőanyagtartály üres kerül.                     | F < 5 gallon | Információ     | Végrehajtók             |
| Nagyobb, mint a normál rakomány hőmérséklet | Ha teherautó tartozó rakomány hőmérséklet értéke magasabb, mint a normál                 | T<45&deg; F |Figyelmeztetés  | Teherautók              |
| Foglalhatja vibráció leállt      | Riasztást küld, ha foglalhatja nem teljesen (vibráció szint alapján)                     | V < 0,1 mm |Figyelmeztetés  | Felvonók           |

### <a name="operate-the-contoso-sample-deployment"></a>Működik a Contoso üzembe helyezési minta

A Contoso mintában a kezdeti telepítés most láthatta. A következő szakaszok ismertetik a Contoso minta három olyan forgatókönyvek, amelyek bemutatják, hogyan használhatja a kezelőnek a az előkonfigurált megoldás.

## <a name="respond-to-a-pressure-alarm"></a>A terhelés riasztás válaszolni

Ez a forgatókönyv bemutatja, hogyan azonosításához, és válaszolhat azokra hűtő eszköz által elindított egy riasztás. A hűtő Redmond, található 43, emelet 2 fejlesztése során.

Kezelőként megjelenik az irányítópulton, hogy van-e egy egy hűtő nyomás kapcsolatos riasztás. Pásztázás, és a részletek megtekintéséhez a térképen nagyítás.

1. Az a **irányítópult** lap a **rendszer riasztások** rács, megtekintheti a **hűtő nyomás túl magas** riasztás. A hűtő is ki van jelölve, a térképen:

    ![Irányítópult nyomás riasztás és az eszköz mutatja a térképen](media/iot-suite-remote-monitoring-explore/dashboardalarm.png)

1. Az eszköz részletes adatainak és telemetriai megtekintéséhez kattintson a kijelölt hűtő a térképen. A telemetriai adatokat egy nyomás csúcs jeleníti meg:

    ![Válassza ki a térképre részletesen eszközt](media/iot-suite-remote-monitoring-explore/dashboarddetail.png)

1. Bezárás **eszköz részletei**.

1. Navigáljon a **karbantartási** lapon, válassza ki **karbantartási** a navigációs menü.

Az a **karbantartási** lapon megtekintheti az adatokat a szabály a hűtő nyomás riasztás kiváltó.

1. Az értesítések listájában a szám, ahányszor a riasztás kiváltása, a visszaigazolások és a nyitott és lezárt riasztásokat jeleníti meg:

    ![Karbantartási lap rendelkezik kiváltott riasztások listáját jeleníti meg.](media/iot-suite-remote-monitoring-explore/maintenancealarmlist.png)

1. A lista első riasztás, a legutóbbi egy. Kattintson a **hűtő nyomás** riasztás társított eszközök és telemetriai adatainak megtekintése. A telemetriai adatokat egy nyomás csúcs a hűtő a jeleníti meg:

    ![Karbantartási lap megjeleníti a kiválasztott riasztáshoz telemetriai adat](media/iot-suite-remote-monitoring-explore/maintenancetelemetry.png)

Most azonosította a problémát, amely aktiválódik a riasztás és a társított eszköz. Kezelőként a következő lépésekre megerősíti a riasztás és a probléma elhárítása érdekében.

1. Azt jelzi, hogy most dolgozik a riasztás, módosítsa a **állapota riasztás** való **Visszaigazolva**:

    ![Válassza ki, és megerősíti a riasztás](media/iot-suite-remote-monitoring-explore/maintenanceacknowledge.png)

1. Hogy intézkedjen a hűtő, válassza ki azt, és válassza a **ütemezés**. Válassza ki **EmergencyValveRelease**, adja hozzá a projekt nevét **ChillerPressureRelease**, és válassza a **alkalmaz**. Ezek a beállítások azonnal végrehajtó feladat létrehozása:

    ![Válassza ki az eszközt, és ütemezés szerinti művelet](media/iot-suite-remote-monitoring-explore/maintenanceschedule.png)

1. A feladat állapotának megtekintéséhez, térjen vissza a **karbantartási** lapon, és a feladatok listájának megtekintése a **feladatok** nézet. A feladat futott a hűtő szelep nehezedő kijelölése látható:

    ![A feladatok nézetben a feladatok állapota](media/iot-suite-remote-monitoring-explore/maintenancerunningjob.png)

Végül ellenőrizze, hogy a hűtő telemetriai értékeinek vissza a normál értéket.

1. A riasztások rács megtekintéséhez nyissa meg a **irányítópult** lap.

1. A telemetriát nézetben válassza ki a eredeti riasztás a térképen az eszközt, és ellenőrizze, hogy egy vissza a normál értéket.

1. Az incidens lezárása, lépjen a **karbantartási** lapon válassza ki a riasztást, és az állapot beállítása **lezárva**:

    ![Válassza ki, majd zárja be a riasztás](media/iot-suite-remote-monitoring-explore/maintenanceclose.png)

## <a name="update-device-firmware"></a>Eszköz belső vezérlőprogram frissítése

A Contoso egy új típusú eszköz teszteli a mezőben. A tesztelési ciklus részeként győződjön meg arról, hogy az eszköz belső vezérlőprogram frissítése munkahelyi megfelelően kell. A következő lépések bemutatják a több eszközre a belső vezérlőprogram frissítésére a távoli felügyeleti megoldást használni.

A szükséges feladatok végrehajtásához használja a **eszközök** lap. Indítsa el az összes prototípusának eszköz szűrése:

1. Keresse meg a **eszközök** lap. Válassza ki a **prototípusának eszközök** szűrés a **szűrők** legördülő:

    ![Szűrő használata az eszközök lapon](media/iot-suite-remote-monitoring-explore/devicesprototypingfilter.png)

    > [!TIP]
    > Kattintson a **kezelése** a rendelkezésre álló szűrők kezeléséhez.

1. A prototípusának eszközök közül választhat:

    ![Jelöljön ki egy eszközt, az eszközök lapon](media/iot-suite-remote-monitoring-explore/devicesselect.png)

1. Kattintson a **ütemezés** gombra, majd válassza a **vezérlőprogram-frissítés**. Adja meg az értékeket **feladatnév** és **belső vezérlőprogram URI**. Válasszon **alkalmaz** a feladat futtatása most ütemezése:

    ![Az eszköz belső vezérlőprogram-frissítés ütemezése](media/iot-suite-remote-monitoring-explore/devicesschedulefirmware.png)

    > [!NOTE]
    > A szimulált eszköz valamennyi URL-cím, tetszés szerint használhatja a **belső vezérlőprogram URI** érték. A szimulált eszköz nem érhető el az URL-címet.

1. Megjegyzés: a feladat befolyásolja, hogy hány eszköz, és válassza a **alkalmaz**:

    ![A belső vezérlőprogram frissítési feladat elküldése](media/iot-suite-remote-monitoring-explore/devicessubmitupdate.png)

Használhatja a **karbantartási** lapon nyomon követheti a feladat futtatása közben.

1. A feladatok megtekintéséhez nyissa meg a **karbantartási** lapot, és kattintson **feladatok**.

1. Keresse meg a létrehozott a feladathoz kapcsolódó esemény. Győződjön meg arról, hogy a belső vezérlőprogram frissítési folyamat megfelelően kezdeményezték.

Létrehozhat egy szűrőt, amely a belső vezérlőprogram verziójának frissítése megfelelően ellenőrizze.

1. Létrehozhat egy szűrőt, lépjen a **eszközök** lapon, és válassza **szűrők kezelése**:

    ![Eszköz szűrők kezelése](media/iot-suite-remote-monitoring-explore/devicesmanagefilters.png)

1. Csak a belső vezérlőprogram verziójának eszközt magában foglal, szűrő létrehozása:

    ![Eszköz szűrő létrehozása](media/iot-suite-remote-monitoring-explore/devicescreatefilter.png)

1. Lépjen vissza a **eszközök** lapon, és ellenőrizze, hogy az eszköz rendelkezik-e az új belső vezérlőprogram-verziója.

## <a name="organize-your-assets"></a>Az eszközök rendszerezéséhez

A Contoso két különböző csapatok mező szolgáltatás tevékenységek rendelkezik:

* Az intelligens épület csapat felügyeli a hőmérsékletű felvonók és motorok.
* Az intelligens Vehicle csapat teherautók és prototípusának eszközök kezelése.

Hogy egyszerűbb kezelőként rendszerezése és az eszközök kezeléséhez, szeretné azokat a megfelelő csapat nevű tag.

Eszközök használata a címkenevek hozhat létre.

1. Az eszközök megjelenítéséhez nyissa meg a **eszközök** lapon, és válassza ki a **minden eszköz** szűrő:

    ![Minden eszköz megjelenítése](media/iot-suite-remote-monitoring-explore/devicesalldevices.png)

1. Válassza ki a **teherautók** és **prototípusának** eszközök. Válassza a **címke**:

    ![Válassza ki a prototípus és teherautó eszközöket](media/iot-suite-remote-monitoring-explore/devicesmultiselect.png)

1. Válasszon **címke** és hozzon létre egy új szöveges címke nevű **FieldService** értékű **ConnectedVehicle**. Válassza ki a feladat nevét. Kattintson a **alkalmaz**:

    ![Címke hozzáadása prototípus és teherautó eszközök](media/iot-suite-remote-monitoring-explore/devicesaddtag.png)

1. Válassza ki a **hűtő**, **foglalhatja**, és **motor** eszközök. Válassza a **címke**:

    ![Válassza ki a hűtő motor és foglalhatja eszközöket](media/iot-suite-remote-monitoring-explore/devicesmultiselect2.png)

1. Válasszon **címke** és hozzon létre egy új szöveges címke nevű **FieldService** értékű **SmartBuilding**. Válassza ki a feladat nevét. Kattintson a **mentése**:

    ![Címke hozzáadása hűtő, motor és foglalhatja eszközök](media/iot-suite-remote-monitoring-explore/devicesaddtag2.png)

-Szűrők létrehozásához használhatja az előfizetéscímkék értékeit.

1. Az a **eszközök** lapon, válassza ki **szűrők kezelése**:

    ![Eszköz szűrők kezelése](media/iot-suite-remote-monitoring-explore/devicesmanagefilters.png)

1. Hozzon létre egy új, a címke nevét használja szűrő **FieldService** és érték **SmartBuilding**. A szűrő mentése másként **intelligens épület**.

1. Hozzon létre egy új, a címke nevét használja szűrő **FieldService** és érték **ConnectedVehicle**. A szűrő mentése másként **csatlakoztatott Vehicle**.

Most már a Contoso operátor lekérdezheti a működési csapat anélkül, hogy az eszközök bármit módosíthat alapú eszközök.

## <a name="next-steps"></a>További lépések

Ebben az oktatóprogramban megismerte a:

>[!div class="checklist"]
> * Megjelenítheti és az irányítópult eszközök szűréséhez
> * Egy riasztás válaszolni
> * Az eszközök a belső vezérlőprogram frissítése
> * Az eszközök rendszerezéséhez

Most, hogy rendelkezik megismerte a távoli felügyeleti megoldás, a javasolt lépéseket a távoli felügyeleti megoldás a szolgáltatásokkal kapcsolatos további:

* [Az eszközök figyeléséhez](./iot-suite-remote-monitoring-monitor.md).
* [Az eszközök kezeléséhez](./iot-suite-remote-monitoring-manage.md).
* [Automatizálhatja a megoldás olyan szabályokkal készült](./iot-suite-remote-monitoring-automate.md).
* [A megoldás karbantartása](./iot-suite-remote-monitoring-maintain.md).
