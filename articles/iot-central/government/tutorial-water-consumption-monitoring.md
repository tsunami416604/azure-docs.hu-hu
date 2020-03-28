---
title: 'Oktatóanyag: Vízfogyasztás-figyelő alkalmazás létrehozása az Azure IoT Central segítségével'
description: 'Oktatóanyag: Ismerje meg, hogy hozzon létre egy vízfogyasztás-figyelő alkalmazás segítségével Az Azure IoT Central alkalmazássablonok használatával.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 7f00947504e5c6355379ce1e400fd2325016e05a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77122074"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Oktatóanyag: Vízfogyasztás-figyelő alkalmazás létrehozása az Azure IoT Central segítségével

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Azure IoT Central vízfogyasztás-figyelő alkalmazást az Azure IoT Central vízfogyasztás-figyelési alkalmazássablon használatával.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Az Azure IoT Central vízfogyasztás-figyelési sablon használatával hozza létre a vízfogyasztás-figyelő alkalmazást.
> * Fedezze fel és szabja testre az operátor irányítópultját.
> * Fedezze fel az eszközsablonokat.
> * Fedezze fel a szimulált eszközöket.
> * Fedezze fel és konfigurálja a szabályokat.
> * Feladatok konfigurálása.
> * Az alkalmazás márkajelzésének testreszabása fehér címkézéssel.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán.](https://aka.ms/createazuresubscription)

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Vízfogyasztás-figyelő alkalmazás létrehozása az Azure IoT Central segítségével

Ebben a szakaszban az Azure IoT Central vízfogyasztás-figyelési sablon használatával hozza létre a vízfogyasztás-figyelő alkalmazást az Azure IoT Centralban.

Új Azure IoT Central vízfogyasztás-figyelő alkalmazás létrehozása:

1. Nyissa meg az [Azure IoT Central kezdőlapját.](https://aka.ms/iotcentral)

    Ha rendelkezik Azure-előfizetéssel, jelentkezzen be a hozzáféréshez használt hitelesítő adatokkal. Ellenkező esetben jelentkezzen be Microsoft-fiókkal.

    ![Lépjen a céges fiókjába](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. Válassza a **Build lehetőséget** a bal oldali ablaktáblán, és válassza a **Kormány** lapot. A **Kormány** lap több kormányzati alkalmazássablont jelenít meg.

   ![Kormányzati alkalmazássablonok létrehozása](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Válassza ki a **Vízfogyasztás figyelési** alkalmazás sablont.
Ez a sablon tartalmaz egy minta vízfogyasztáseszköz-sablont, egy szimulált eszközt, egy kezelői irányítópultot és egy előre konfigurált figyelési szabályokat.

1. Válassza **az Alkalmazás létrehozása lehetőséget** az Új **alkalmazáslétrehozási** űrlap megnyitásához a következő mezőkkel:
    * **Alkalmazás neve**: Alapértelmezés szerint az alkalmazás *vízfogyasztás-figyelést* használ, majd egy egyedi azonosító karakterláncot, amelyet az Azure IoT Central hoz létre. Ha meg kell választania egy rövid alkalmazásnevet. Később is módosíthatja az alkalmazás nevét.
    * **URL:** Az Azure IoT Central automatikusan létrehoz egy URL-címet az alkalmazás neve alapján. Választhat, hogy frissítse az URL-t, hogy a szeretet. Később is módosíthatja az URL-címet.
    * Ha Rendelkezik Azure-előfizetéssel, adja meg **a címtár,** **az Azure-előfizetés**és **a helyadatok.** Ha nem rendelkezik előfizetéssel, kiválaszthatja a **7 napos ingyenes próbaverziót,** és kivégezheti a szükséges elérhetőségi adatokat.

    A könyvtárakról és előfizetésekről további információt az [Alkalmazás rövid útmutatójának létrehozása című témakörben talál.](../core/quick-deploy-iot-central.md)

1. Válassza a Lap alján a **Létrehozás** lehetőséget.

    ![Az Azure IoT Central új alkalmazáslapja](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Az Azure IoT központi számlázási információs lapja](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

Most létrehozott egy vízfogyasztás-figyelő alkalmazást az Azure IoT Central vízfogyasztás-figyelési sablon használatával.

A vízfogyasztás-figyelő alkalmazás előre konfigurált:

* Minta operátori irányítópultok.
* Minta előre meghatározott vízáramlás és szelep eszköz sablonokat.
* Szimulált vízáramlás és intelligens szelepeszközök.
* Szabályok és munkahelyek.
* Minta márkajelzés e fehér címkézés használatával.

Ez az alkalmazás, és bármikor módosíthatja. Most nézzük meg az alkalmazást, és néhány testreszabást.

## <a name="explore-and-customize-the-operator-dashboard"></a>Az operátori irányítópult felfedezése és testreszabása

Az alkalmazás létrehozása után megnyílik a **Wide World vízfogyasztási irányítópultja.**

   ![Vízfogyasztás-figyelő műszerfal](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Szerkesztőként nézeteket hozhat létre és szabhat testre az irányítópulton az operátorok számára. Mielőtt megpróbálna testreszabni, kutatjuk át az irányítópultot.

> [!NOTE]
> Az irányítópulton megjelenő összes adat szimulált eszközadatokon alapul, amelyeket a következő szakaszban fogunk megvizsgálni.
  
Az irányítópult különböző csempékből áll:

* **Wide World Water Utility kép csempe**: Az első csempe a műszerfalon egy kép csempe a fiktív vízi közmű Wide World Water. A mozaikot testreszabhatja saját kép beszúrásával vagy eltávolításával.
* **Átlagos vízáramlás KPI csempe**: A KPI csempe úgy van beállítva, hogy példaként jelenítse meg *az elmúlt 30 perc átlagát*. Testreszabhatja a KPI csempét, és más típusra és időtartományra állíthatja be.
* **Eszköz parancs csempe:** Ezek a csempe tartalmazza a **Zár szelep,** **Nyitott szelep,** és **állítsa szelep pozíció** csempe. A parancsok kijelölése a szimulált eszköz parancslapjára vezet. Az Azure IoT Central ban a *parancs* egy *eszköz képesség* típusa. Ezt a koncepciót később, az oktatóanyag "Eszközsablon" című részében fogjuk feltárni.
* **Vízelosztási terület térkép:** A térkép az Azure Maps, amely közvetlenül konfigurálható az Azure IoT Central. A térképcsempe megjeleníti az eszköz helyét. Mutasson a térképfölé, és próbálja meg a vezérlőelemeket a térkép felett, például *nagyítás,* *kicsinyítés*vagy *kibontás.*

    ![Vízfogyasztás-figyelő műszerfal térképe](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Átlagos vízfolyásvonal-diagram** és **Környezeti feltétel vonaldiagram**: Egy vagy több eszköztelemetriát ábrázolhat vonaldiagramként a kívánt időtartományban.
* **Átlagos szelepnyomás-hőtérképdiagram**: Kiválaszthatja a hőtérkép-vizualizációs típusát az eszköz telemetriai adatainak, amelyeket egy színindexszel rendelkező időtartományban elosztva szeretne látni.
* **Riasztási küszöbértékek visszaállítása tartalomcsempék**: Felhívást tartalmazó tartalomcsempéket is megadhat, és beágyazhat egy műveletlapra mutató hivatkozást. Ebben az esetben az alaphelyzetbe állítási riasztási küszöbértéket az alkalmazás **feladatok**, ahol futtathatja az eszköz tulajdonságait. Ezt a lehetőséget később, az oktatóanyag "Feladatok konfigurálása" című részében fogjuk megvizsgálni.
* **Tulajdonságcsempék**: Az irányítópult on-már megjeleníti a **Valve működési adatait,** **a folyamatriasztási küszöbértékeket**és **a karbantartási információs** csempéket.

### <a name="customize-the-dashboard"></a>Az irányítópult testreszabása

Szerkesztőként testreszabhatja az irányítópulton lévő nézeteket az operátorok számára.

1. A **Szerkesztés** lehetőséget választva testreszabhatja a **Wide World vízfogyasztási irányítópultját.** Az irányítópultot a **Szerkesztés** menü kiválasztásával szabhatja testre. Miután az irányítópult **szerkesztési** módban van, hozzáadhat új csempéket, vagy konfigurálhatja azt.

     ![Irányítópult szerkesztése](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Válassza a **+ Új** lehetőséget új irányítópult létrehozásához és teljesen új beállításához. Több irányítópultja is lehet, és az irányítópultok között az irányítópult menüben válthat.

## <a name="explore-the-device-template"></a>Az eszközsablon felfedezése

Az Azure IoT Central eszközsablonja meghatározza egy eszköz képességeit, amely lehet telemetriai, tulajdonság vagy parancs. Szerkesztőként definiálhat egy vagy több eszközsablont az Azure IoT Centralban, amelyek a csatlakoztatni fogeszközök képességeit képviselik.

A vízfogyasztás-figyelő alkalmazás két referenciaeszköz-sablonnal érkezik, amelyek egy *áramlásmérőt* és egy *intelligens szelepeszközt* képviselnek.

Az eszközsablon megtekintése:

1. Válassza **az eszközsablonokat** az alkalmazás bal oldali ablaktábláján az Azure IoT Centralban. Az **Eszközsablonok** listában két eszközsablon látható, az **Intelligens szelep** és a **Folyamatmérő**.

   ![Eszközsablon](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Válassza ki a **Folyamatmérő** eszközsablont, és ismerkedjen meg az eszköz képességeivel.

     ![Eszközsablon folyamatmérője](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Az eszközsablon testreszabása

Az eszközsablon testreszabása:

1. Nyissa meg az Eszközsablonok menü **Testreszabás** **parancsát.**
1. Keresse `Temperature` meg a telemetriai típust.
1. A **megjelenítendő név** frissítése a rendszerre. `Temperature` `Reported temperature`
1. Frissítse a mértékegységet, vagy állítsa be a **Min értéket** és a **Maximális értéket**.
1. A módosítások mentéséhez válassza a **Mentés** gombot.

### <a name="add-a-cloud-property"></a>Felhőtulajdonság hozzáadása

1. Nyissa meg az Eszközsablonok menü **Felhőtulajdonságai** **lapját.**
1. Új felhőtulajdonság hozzáadása **a + Felhőalapú tulajdon hozzáadása**lehetőséget választva.
    Az Azure IoT Centralban hozzáadhat egy tulajdonságot, amely releváns az eszközhöz. Például egy felhő tulajdonság lehet egy telepítési területre, eszközadatokra vagy egyéb karbantartási információkra vonatkozó riasztási küszöbérték.
1. A módosítások mentéséhez válassza a **Mentés** gombot.

### <a name="views"></a>Nézetek

A vízfogyasztás-figyelő eszköz sablon előre definiált nézetekkel rendelkezik. Fedezze fel a nézeteket, és frissítéseket is elérhet. A nézetek határozzák meg, hogy az operátorok hogyan látják az eszközadatokat, de a felhő tulajdonságait is betáplálják.

  ![Eszközsablon-nézetek](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Közzététel

Ha módosításokat hajtott végre, győződjön meg az eszközsablon **közzétételéről.**

### <a name="create-a-new-device-template"></a>Új eszközsablon létrehozása

Válassza a **+ Új** lehetőséget új eszközsablon létrehozásához és a létrehozási folyamat követéséhez.
Létrehozhat egy teljesen új, teljesen új eszközsablont, vagy választhat egy eszközsablont az Azure Eszközkatalógusból.

## <a name="explore-simulated-devices"></a>Szimulált eszközök felfedezése

Az Azure IoT Centralban szimulált eszközöket hozhat létre az eszközsablon és -alkalmazás teszteléséhez. A vízfogyasztás-figyelő alkalmazás két szimulált eszközzel rendelkezik, amelyek a **Flow meter** és a **Smart Valve** eszközsablonokhoz vannak rendelve.

### <a name="view-the-devices"></a>Az eszközök megtekintése

1. Válassza **az Eszközök** > **minden eszköz lehetőséget** a bal oldali ablaktáblán.

   ![Minden eszköz ablaktábla](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Válassza az **Intelligens szelep 1 lehetőséget.**

    ![Intelligens szelep 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. A Parancsok lapon **láthatja** a **Smart Valve** eszközsablonban meghatározott három eszközparancsot (**Szelep bezárása**, **Szelep**nyitása és **Szeleppozíció beállítása**).

1. Fedezze fel az **Eszköz tulajdonságai** lapot és az **Eszközirányítópult** lapot.

> [!NOTE]
> Ne feledje, hogy az összes lap az eszközsablon nézetekből van konfigurálva.

### <a name="add-new-devices"></a>Új eszközök hozzáadása

Új eszközök hozzáadása az **Eszközök** lap **+ Új** lehetőség beállításával.

## <a name="explore-and-configure-rules"></a>Szabályok feltárása és konfigurálása

Az Azure IoT Centralban szabályokat hozhat létre az eszköz telemetriai adatainak automatikus figyelésére, és műveleteket indíthat el, ha egy vagy több feltétel teljesül. A műveletek közé tartozhat az e-mail értesítések küldése vagy a Microsoft Power Automate művelet vagy a webhook-művelet, amely adatokat küld más szolgáltatásoknak.

A létrehozott vízfogyasztás-figyelő alkalmazás három előre konfigurált szabó szabályok.

### <a name="view-rules"></a>Szabályok megtekintése

1. Válassza a **Szabályok** lehetőséget a bal oldali ablaktáblán.

   ![Szabályok ablaktábla](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Válassza a **Magas pH-riasztás lehetőséget,** amely az alkalmazás előre konfigurált szabályainak egyike.

     ![Magas pH-riasztás](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    A `High flow alert` szabály úgy van beállítva, hogy ellenőrizze a feltételt `Acidity (pH)` a `greater than` `Max flow threshold`. A maximális áramlási küszöbérték egy **felhőtulajdonság,** amelyet a Smart Valve eszközsablon definiál. Az érték `Max flow threshold` eszközpéldányonként van beállítva.

Most hozzunk létre egy e-mail műveletet.

Művelet hozzáadása a szabályhoz:

1. Válassza a **+ E-mail lehetőséget.**
1. Adja meg **a magas pH-riasztást** a művelet rövid **megjelenítendő neveként.**
1. Adja meg az Azure IoT Central-fiókjához társított e-mail címet a **Címzett mezőbe.**
1. Tetszés szerint írjon be egy megjegyzést, amelyet az e-mail szövegében szeretne szerepelni.
1. A művelet végrehajtásához válassza a **Kész** lehetőséget.
1. Az új szabály mentéséhez és aktiválásához válassza a **Mentés** lehetőséget.

Néhány percen belül e-mailt kell kapnia a beállított feltétel teljesülése után.

> [!NOTE]
> Az alkalmazás e-mailt küld minden alkalommal, amikor egy feltétel teljesül. A **Letiltás** gombra a szabály letiltásához válassza az automatikus szabályból érkező e-mailek fogadásának leállítását.
  
Új szabály létrehozása:

* A bal oldali ablaktábla **Szabályok** lapján válassza a **+ Új** lehetőséget.

## <a name="configure-jobs"></a>Feladatok konfigurálása

Az Azure IoT Central ban a feladatok lehetővé teszik, hogy több eszközön is elindítsa az eszköz- vagy felhőtulajdon-frissítéseket. A tulajdonságok mellett a feladatok segítségével eszközparancsokat is aktiválhat több eszközön. Az Azure IoT Central automatizálja a munkafolyamatot.

1. Válassza a **Feladatok** lehetőséget a bal oldali ablaktáblán.
1. Válassza a **+ Új**lehetőséget, és konfiguráljon egy vagy több feladatot.

## <a name="customize-your-application"></a>Az alkalmazás testreszabása

Szerkesztőként számos beállítást módosíthat az alkalmazás felhasználói élményének testreszabásához.

1. Válassza **a Felügyeleti** > **alkalmazás testreszabása**lehetőséget.
1. Az **Alkalmazás emblémaként**feltöltendő kép kiválasztásához kattintson a **Módosítás** gombra.
1. A böngészőlapokon megjelenő **böngészőikonkép** kiválasztásához kattintson a **Módosítás** gombra.
1. Az alapértelmezett **böngészőszíneket** html hexadecimális színkódok hozzáadásával is lecserélheti.

   ![Az alkalmazás emblémájának, a böngésző ikonjának és a böngésző színeinek kiválasztása](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. Az alkalmazásképeket a **Felügyeleti** > **alkalmazás beállításai**lehetőséget választva is módosíthatja. Ha alkalmazásképként feltölteni kívánt képet szeretne kiválasztani, válassza a **Kép kiválasztása** gombot.
1. Végül **módosíthatja a témát** is, ha az alkalmazás jobb felső sarkában lévő **Beállítások** ikonra kattint.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje azt.

1. Válassza **a Felügyelet** lehetőséget az Azure IoT Central alkalmazás bal oldali ablaktábláján.
1. Válassza **az Alkalmazásbeállítások**lehetőséget, majd a lap alján válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

* További információ a [vízfogyasztás-ellenőrzési fogalmakról.](./concepts-waterconsumptionmonitoring-architecture.md)
