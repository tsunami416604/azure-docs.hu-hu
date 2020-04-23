---
title: 'Oktatóanyag: Vízminőség-figyelő alkalmazás létrehozása az Azure IoT Central segítségével'
description: 'Oktatóanyag: Ismerje meg, hogyan hozhat létre vízminőség-figyelő alkalmazást az Azure IoT Central alkalmazássablonjaihasználatával.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 77ec999d63175f63c1de6e31fdb3f72c963d228c
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024472"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Oktatóanyag: Vízminőség-figyelő alkalmazás létrehozása az Azure IoT Central ban



Ez az oktatóanyag végigvezeti egy vízminőség-figyelő alkalmazás létrehozásán az Azure IoT Centralban. Az alkalmazást az Azure IoT Central **Water quality monitoring** alkalmazássablonból hozza létre.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A **vízminőség-figyelő** sablon segítségével vízminőség-ellenőrző alkalmazást hozhat létre.
> * Fedezze fel és szabja testre az operátori irányítópultot.
> * Fedezze fel a vízminőség-figyelő eszköz sablonját.
> * Fedezze fel a szimulált eszközöket.
> * Fedezze fel és konfigurálja a szabályokat.
> * Feladatok konfigurálása.
> * Az alkalmazások márkajelzésének testreszabása fehér címkézéssel.

## <a name="prerequisites"></a>Előfeltételek

Azt javasoljuk, hogy az oktatóanyag befejezéséhez azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán.](https://aka.ms/createazuresubscription)

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Vízminőség-figyelő alkalmazás létrehozása az Azure IoT Central ban

Ebben a szakaszban az Azure IoT központi **vízminőség-figyelési** sablon használatával hozzon létre egy vízminőség-figyelő alkalmazást.

1. Nyissa meg az [Azure IoT Central kezdőlapját.](https://aka.ms/iotcentral)

    Ha rendelkezik Azure-előfizetéssel, jelentkezzen be a hozzáféréshez használt hitelesítő adatokkal. Ellenkező esetben jelentkezzen be Microsoft-fiókkal:

    ![Bejelentkezés a szervezeti fiókba](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Válassza a **Build lehetőséget** az Azure IoT Central bal szélső ablaktábláján, és válassza a **Kormány** lapot. A kormányzati ablaktábla több kormányzati alkalmazássablont jelenít meg.

    ![Kormányzati alkalmazássablonok](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Válassza ki a **Vízminőség-figyelő** alkalmazás sablont. Ez az alkalmazássablon vízminőség-eszközsablont, szimulált eszközöket, kezelői irányítópultot és előre konfigurált figyelési szabályokat tartalmaz.

1. Válassza az **Alkalmazás létrehozása** lehetőséget. Megnyílik **az Új alkalmazás** ablaktábla, és a következő elemeket jeleníti meg:

    * **Alkalmazás neve**: Alapértelmezés szerint az alkalmazás neve **vízminőség-figyelés,** majd egy egyedi azonosító karakterlánc, amely az Azure IoT Central által generált. Ha szeretné, megadhat egy megjelenítendő nevet, vagy később módosíthatja az alkalmazás nevét.
    * **URL**: Bármilyen URL-t megadhat, vagy később módosíthatja az URL-értéket.
    * Ha Rendelkezik Azure-előfizetéssel, adja meg a **Címtár,** **az Azure-előfizetés**és a **Régió**értékét. Ha nem rendelkezik előfizetéssel, bekapcsolhatja a **7 napos ingyenes próbaverziót,** és kivégezheti a szükséges kapcsolattartási adatokat.

    A könyvtárakról és az előfizetésekről további információt az [Alkalmazás létrehozása](../core/quick-deploy-iot-central.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) rövid útmutató című témakörben talál.

1. A lap bal alsó részén válassza a **Létrehozás** gombot.

    ![Az Azure IoT Central új alkalmazás lapja](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![Az Azure IoT Central új alkalmazás számlázási adatai](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

Most létrehozott egy vízminőség-figyelő alkalmazást az Azure IoT Central **Water quality monitoring** sablon használatával.

Az új alkalmazás a következő előre konfigurált összetevőkkel érkezik:

* Kezelői irányítópultok
* Vízminőség-ellenőrző eszköz sablonok
* Szimulált vízminőség-ellenőrző eszközök
* Szabályok és munkahelyek
* Fehér címkézést használó márkajelzés

Az alkalmazást bármikor módosíthatja.

Ezután fedezze fel az alkalmazást, és készítsen néhány testreszabást.

## <a name="explore-and-customize-the-operator-dashboard"></a>Az operátori irányítópult felfedezése és testreszabása

Az alkalmazás létrehozása után megnyílik a **Wide World vízminőségi irányítópult-panelje.**

   ![A vízminőség-ellenőrző műszerfal](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Szerkesztőként nézeteket hozhat létre és szabhat testre az irányítópulton az operátorok általi használatra. De mielőtt megpróbálna testre szabni, először fedezze fel az irányítópultot.

Az irányítópulton látható összes adat szimulált eszközadatokon alapul, amelyeket a következő szakasz tárgyal.

Az irányítópult a következő csempéket tartalmazza:

* **Wide World vízi közüzemi kép csempe**: Az első csempe a bal felső sarokban a műszerfal egy kép, amely megmutatja a fiktív segédprogram nevű Wide World. Testreszabhatja a csempét, hogy saját képet használjon, vagy eltávolíthatja a csempét.

* **Átlagos pH KPI-csempék:** Az **átlagos pH-hoz hasonló KPI-csempék az irányítópult** ablaktáblájának tetején találhatók. Testreszabhatja a KPI csempéket, és különböző típus- és időtartományra állíthatja őket.

* **Vízfigyelési terület térkép:** Az Azure IoT Central az Azure Maps, amely közvetlenül beállíthatja az alkalmazásban az eszköz helyének megjelenítéséhez. Az alkalmazás helyadatait is leképezheti az eszközére, majd az Azure Maps segítségével megjelenítheti az adatokat a térképen. Mutasson a térképfölé, és próbálja meg a vezérlőket.

* **Átlagos pH-eloszlási hőtérkép-diagram:** Különböző vizualizációs diagramokat választhat, hogy az eszköz telemetriai adatait az alkalmazásnak leginkább megfelelő módon jelenítse meg.

* **Kritikus minőségi mutatók vonaldiagram**: Egy időtartományban vonaldiagramként ábrázolt eszköztelemetriai ábrázolása.  

* **A vegyi anyagok sávdiagramjának koncentrációja**: Az eszköz telemetriai adatai láthatóvá tehető egy sávdiagramon.

* **Művelet gomb**: Az irányítópult egy csempét tartalmaz azoperátor által közvetlenül a figyelési irányítópultról kezdeményezett műveletekhez. Az eszköz tulajdonságainak alaphelyzetbe állítása példa az ilyen műveletekre.

* **Tulajdonságlista csempéi:** Az irányítópult több tulajdonságcsempével rendelkezik, amelyek küszöbérték-információkat, eszközállapot-információkat és karbantartási információkat jelölnek.

### <a name="customize-the-dashboard"></a>Az irányítópult testreszabása

Szerkesztőként testreszabhatja az irányítópulton lévő nézeteket az operátorok általi használatra.

1. A **Szerkesztés** gombra választva testreszabhatja a **Wide World vízminőségi** irányítópult-panelt. Az irányítópult ot a **Szerkesztés** menü parancsainak kijelölésével szabhatja testre. Miután az irányítópult szerkesztési módban van, hozzáadhat új csempéket, vagy konfigurálhatja a meglévő fájlokat.

    ![Az irányítópult szerkesztése](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. A **konfigurálható** új irányítópult létrehozásához válassza a + Új lehetőséget. Több irányítópultja is lehet, és az irányítópult menüből navigálhat közöttük.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Vízminőség-figyelő eszközsablon felfedezése

Az Azure IoT Central eszközsablonja határozza meg az eszköz képességeit. A rendelkezésre álló képességek a telemetriai adatok, a tulajdonságok és a parancsok. Szerkesztőként definiálhat olyan eszközsablonokat az Azure IoT Centralban, amelyek a csatlakoztatott eszközök képességeit képviselik. Az eszközsablon és -alkalmazás teszteléséhez szimulált eszközöket is létrehozhat.

A létrehozott vízminőség-ellenőrző alkalmazás vízminőség-ellenőrző eszközsablonnal érkezik.

Az eszközsablon megtekintése:

1. Válassza **az eszközsablonokat** az alkalmazás bal szélső ablaktábláján az Azure IoT Centralban.
1. Az eszközsablonok listájában válassza a **Vízminőség-figyelő lehetőséget.** Megnyílik az eszközsablon.

    ![Az eszközsablon](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Az eszközsablon testreszabása

Gyakorolja a következő eszközsablon-beállítások testreszabását:

1. Az eszközsablon menüjében válassza a **Testreszabás parancsot.**
1. Lépjen a **Hőmérséklet** telemetriai típusra.
1. Módosítsa a **Megjelenítendő név** értékét **Jelentett hőmérsékletre.**
1. Módosítsa a mértékegységet, vagy állítsa be a **Min értéket** és **a Maximális értéket**.
1. Kattintson a **Mentés** gombra.

#### <a name="add-a-cloud-property"></a>Felhőtulajdonság hozzáadása

1. Az eszközsablon menüjében válassza a **Cloud tulajdonság lehetőséget.**
1. Új felhőtulajdonság hozzáadásához válassza a **+ Add Cloud Property**lehetőséget. Az Azure IoT Central ban hozzáadhat egy tulajdonságot, amely releváns egy eszközhöz, de nem várható, hogy az eszköz elküldi. Egy ilyen tulajdonság egy figyelmeztetési küszöbértéket, amely a telepítési területre, az eszközadatokra vagy a karbantartási információkra jellemző.
1. Kattintson a **Mentés** gombra.

### <a name="explore-views"></a>Nézetek felfedezése

A vízminőség-figyelő eszköz sablon előre meghatározott nézetekkel rendelkezik. A nézetek határozzák meg, hogy az operátorok hogyan látják az eszközadatokat, és hogyan határozzák meg a felhő tulajdonságait. Fedezze fel a nézeteket és a módosítások gyakorlati gyakorlatát.

  ![Eszközsablon-nézetek](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Az eszközsablon közzététele

Ha módosításokat hajt végre, mindenképpen válassza a **Közzététel** lehetőséget az eszközsablon közzétételéhez.

### <a name="create-a-new-device-template"></a>Új eszközsablon létrehozása

1. Válassza a **+ Új** lehetőséget új eszközsablon létrehozásához és a létrehozási folyamat követéséhez.
1. Hozzon létre egy egyéni eszközsablont, vagy válasszon egy eszközsablont az Azure IoT-eszközkatalógusból.

## <a name="explore-simulated-devices"></a>Szimulált eszközök felfedezése

Az alkalmazássablonból létrehozott vízminőség-figyelő alkalmazás két szimulált eszközzel rendelkezik. Ezek az eszközök leképezése a vízminőség-figyelő eszköz sablon.

### <a name="view-the-devices"></a>Az eszközök megtekintése

1. Válassza **az Eszközök** lehetőséget az alkalmazás bal szélső ablaktábláján.

   ![Eszközök](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Jelöljön ki egy szimulált eszközt.

    ![Eszköz kiválasztása 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. A **Felhőtulajdonságok** lapon módosítsa a **Savasság (pH) küszöbértékét** **8-ról** **9-re.**
1. Fedezze fel az **Eszköz tulajdonságai** lapot és az **Eszközirányítópult** lapot.

> [!NOTE]
> Az összes lap az **Eszközsablon nézetekből**lett konfigurálva.

### <a name="add-new-devices"></a>Új eszközök hozzáadása

Az **Eszközök** lapon válassza a **+ Új** lehetőséget új eszköz hozzáadásához.

## <a name="explore-and-configure-rules"></a>Szabályok feltárása és konfigurálása

Az Azure IoT Centralban olyan szabályokat hozhat létre, amelyek automatikusan figyelik az eszköz telemetriai adatokat. Ezek a szabályok akkor indítanak műveletet, ha valamelyik feltételük teljesül. Az egyik lehetséges művelet az e-mail értesítések küldése. Egyéb lehetőségek közé tartozik a Microsoft Flow művelet vagy webhook művelet adatok küldése más szolgáltatásoknak.

A létrehozott vízminőség-figyelő alkalmazás két előre konfigurált szabó szabékkal rendelkezik.

### <a name="view-rules"></a>Szabályok megtekintése

1. Válassza a **Szabályok** lehetőséget az alkalmazás bal szélső ablaktábláján.

   ![Szabályok](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Válassza a **Magas pH-riasztás lehetőséget,** amely az alkalmazás előre konfigurált szabályainak egyike.

   ![A magas pH-riasztási szabály](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   A **magas pH-riasztási** szabály úgy van beállítva, hogy ellenőrizze a savasság (pH) 8-nál nagyobb állapotát.

Ezután adjon hozzá egy e-mail műveletet a szabályhoz:

1. Válassza a **+ E-mail lehetőséget.**
1. A **Megjelenítendő név** mezőbe írja be a **Magas pH-riasztás t.**
1. A **Címzett** mezőbe írja be az Azure IoT Central-fiókjához társított e-mail címet.
1. Tetszés szerint írjon be egy megjegyzést, amelyet az e-mail szövegében szeretne szerepelni.
1. A művelet végrehajtásához válassza a **Kész** lehetőséget.
1. Az új szabály mentéséhez és aktiválásához válassza a **Mentés** lehetőséget.

Néhány percen belül e-mailt kell kapnia, ha a beállított feltétel teljesül.

> [!NOTE]
> Az alkalmazás minden alkalommal e-mailt küld, amikor egy feltétel teljesül. Válassza a **Letiltás lehetőséget** egy szabályhoz, ha le szeretné állítani az automatikus e-mailek fogadását a szabályból.
  
Új szabály létrehozásához válassza a **Szabályok** lehetőséget az alkalmazás bal szélső ablaktábláján, majd az **+Új**lehetőséget.

## <a name="configure-jobs"></a>Feladatok konfigurálása

Az Azure IoT Central-feladatokkal több eszközön is aktiválhatja az eszköz- vagy felhőtulajdonságok frissítéseit. A feladatok segítségével eszközparancsokat is aktiválhat több eszközön. Az Azure IoT Central automatizálja a munkafolyamatot.

1. Válassza **a Feladatok** lehetőséget az alkalmazás bal szélső ablaktábláján.
1. Válassza a **+Új** lehetőséget, és konfiguráljon egy vagy több feladatot.

## <a name="customize-your-application"></a>Az alkalmazás testreszabása

Szerkesztőként számos beállítást módosíthat az alkalmazás felhasználói élményének testreszabásához.

1. Válassza **a Felügyeleti** > **alkalmazás testreszabása**lehetőséget.
1. Az **Alkalmazás embléma csoportban**válassza a **Módosítás** lehetőséget, ha emblémaként szeretné kiválasztani a feltölteni kívánt képet.
1. A **Böngésző ikon csoportban**válassza a **Módosítás** lehetőséget a böngészőlapokon megjelenő kép kiválasztásához.
1. A **Böngészőszínek csoportban**az alapértelmezett értékeket HTML hexadecimális színkódokra cserélheti.
1. A **Téma**értékének módosításához válassza a **Beállítások** lehetőséget.

   ![Az alkalmazás testreszabása](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Az alkalmazáslemez képének frissítése

1. Válassza a **Felügyeleti** > **alkalmazás beállításai lehetőséget**.

1. A **Kép kiválasztása** gombbal kiválaszthatja az alkalmazásképként feltölteni kívánt képet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni az alkalmazást, törölje az alkalmazást a következő lépésekkel:

1. Nyissa **meg** a Felügyelet lapot az alkalmazás bal szélső ablaktábláján.
1. Válassza **az Alkalmazásbeállításai t,** majd a **Törlés** gombot.

    ![Az alkalmazás törlése](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>További lépések

* További információ a [vízminőség-ellenőrzési koncepciókról.](./concepts-waterqualitymonitoring-architecture.md)
