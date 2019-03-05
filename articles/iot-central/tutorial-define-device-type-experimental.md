---
title: Új eszköztípus definiálása az Azure IoT Centralban | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan definiálhat új eszköztípust az Azure IoT Central alkalmazásban. Meghatározza a típus telemetriáját, állapotát, tulajdonságait és beállításait.
author: dominicbetts
ms.author: dobett
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 894e4c3fb47a8d936c4e27e2c8c164cc43f3d20e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317237"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-new-ui-design"></a>Oktatóanyag: Egy új eszköz típusának megadása az Azure IoT Central alkalmazásban (új felhasználói felület tervezése)

Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan definiálhat új eszköztípust egy eszközsablon használatával a Microsoft Azure IoT Central alkalmazásban. Az eszközsablon határozza meg az eszköztípus telemetriáját, állapotát, tulajdonságait és beállításait.

A valós eszköz csatlakoztatása előtt az alkalmazás teszteléséhez az IoT Central létrehoz egy szimulált eszközt az eszközsablonból annak létrehozásakor.

Ebben az oktatóanyagban egy **Csatlakoztatott légkondicionáló** eszközsablont hoz létre. Egy csatlakoztatott légkondicionáló eszköz:

* Telemetriát küld, például a hőmérsékletet és a páratartalmat.
* Például, hogy be- vagy kikapcsolása állapotot jelez.
* Olyan eszköztulajdonságokkal rendelkezik, mint a belső vezérlőprogram verziója és a sorozatszám.
* Olyan beállításokkal rendelkezik, mint a célhőmérséklet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új eszközsablon létrehozása
> * Telemetria hozzáadása az eszközhöz
> * Szimulált telemetria megtekintése
> * Esemény mérésének meghatározása
> * Szimulált események megtekintése
> * Állapot mérésének meghatározása
> * Szimulált állapot megtekintése
> * Beállítások és tulajdonságok használata
> * Parancsok használata
> * A szimulált eszköz megtekintése az irányítópulton

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure IoT Central-alkalmazásra. Ha elvégezte az [Azure IoT Central-alkalmazás létrehozása](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) rövid útmutatót, újból felhasználhatja az abban létrehozott alkalmazást. Egyébként hajtsa végre az alábbi lépéseket egy üres Azure IoT Central-alkalmazás létrehozásához:

1. Lépjen az Azure IoT Central [Alkalmazáskezelő](https://aka.ms/iotcentral) oldalára.

2. Írja be az Azure-előfizetés eléréséhez használt e-mail-címet és jelszót:

    ![Lépjen a céges fiókjába](./media/tutorial-define-device-type-experimental/sign-in.png)

3. Egy új Azure IoT központi alkalmazás létrehozásának megkezdéséhez válassza **új alkalmazás**:

    ![Azure IoT Central Alkalmazáskezelő oldal](./media/tutorial-define-device-type-experimental/iotcentralhome.png)

4. Új Azure IoT Central-alkalmazás létrehozása:
    
    * Válassza a **Próba** elemet. Próbaalkalmazás létrehozásához nincs szükség Azure-előfizetésre.
    
       A könyvtárakkal és előfizetésekkel kapcsolatban további információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
    
    * Válassza az **Egyéni alkalmazás** elemet.
    
    * Választhat egy rövid alkalmazásnevet is, például a **Contoso-légkondicionálók** nevet. Az Azure IoT Central létrehoz egy egyéni URL-előtagot. Ezt az URL-előtagot egy könnyebben megjegyezhető előtagra módosíthatja.
    
    * Kattintson a **Létrehozás** gombra.

    ![Azure IoT Central Alkalmazás létrehozása oldal](./media/tutorial-define-device-type-experimental/iotcentralcreate.png)

    További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="create-a-new-custom-device-template"></a>Új egyéni ügyfélsablon létrehozása

Szerkesztőként létrehozhatja és szerkesztheti az alkalmazásban lévő eszközsablonokat. Egy eszközsablon létrehozásakor az Azure IoT Central létrehoz egy szimulált eszközt a sablonból. A szimulált eszköz, amely lehetővé teszi, hogy tesztelje az alkalmazás viselkedésének valós eszköz csatlakoztatása előtt telemetriát hoz létre.

Csatlakoznia kell egy új eszköz sablon hozzáadása az alkalmazáshoz, a **eszközsablonok** lapot. Ehhez tehát válassza ki a **eszközsablonok** a bal oldali navigációs menüben.

![Eszköz sablonok lap](./media/tutorial-define-device-type-experimental/devicetemplates.png)

## <a name="add-a-device-template"></a>Egy eszköz sablon hozzáadása

A következő lépések bemutatják, hogyan hozhat létre egy új **Csatlakoztatott légkondicionáló** eszközsablont olyan eszközökhöz, amelyek hőmérsékleti telemetriát küldenek az alkalmazásra:

1. Az a **eszközsablonok** lapon jelölje be **+**:

    ![Sablonok oldaláról, eszköz-sablon létrehozása](./media/tutorial-define-device-type-experimental/newtemplate.png)

3. Az a **egyéni eszköz sablon** lap, adja meg **Légkondicionálóját csatlakoztatott** az eszközt, és válassza ki a nevet **létrehozás**. Az eszköz képét is feltöltheti, amelyet az operátorok láthatnak az Eszközkeresőben:

    ![Egyéni eszköz](./media/tutorial-define-device-type-experimental/createcustomdevice.png)

4. Az a **Légkondicionálóját csatlakoztatott** eszköz sablon, ellenőrizze, hogy nekik a **mérések** lapra, ahol megadhatja a telemetriát. Minden eszköz sablon határoz meg, hogy külön lapokon rendelkezik:

    * Adja meg a _mérések_, például a telemetriai esemény és állapotáról, az eszköz által küldött.

    * Adja meg a _beállítások_ használja az eszköz vezérlésére.

    * Adja meg a _tulajdonságok_ , amelyek az eszköz metaadatait.

    * Adja meg a _parancsok_ közvetlenül az eszközön futtatható.

    * Adja meg a _szabályok_ az eszközhöz társított.

    * Az eszköz testre szabásához _irányítópult_ az operátorok számára.

    ![A légkondicionáló mérései](./media/tutorial-define-device-type-experimental/airconmeasurements.png)

    > [!NOTE]
    > Ha módosítani szeretné az eszköz-sablon nevét, válassza ki a sablon nevét az oldal tetején.

5. A hőmérsékleti telemetria mérték hozzáadásához válassza **+ új mérték**. Ezután válassza a **Telemetriát** a mérés típusaként:

    ![A csatlakoztatott légkondicionáló mérései](./media/tutorial-define-device-type-experimental/airconmeasurementsnew.png)

6. Az eszközsablonokhoz meghatározott minden telemetriatípus tartalmaz [konfigurációs beállításokat](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), például a következőket:

    * Megjelenítési beállítások.

    * A telemetria részletei.

    * Szimulációs paraméterek.

    A **Hőmérséklet** telemetria konfigurálásához használja a következő táblázatban lévő információkat:

    | Beállítás              | Érték         |
    | -------------------- | -----------   |
    | Megjelenítendő név         | Hőmérséklet   |
    | Mezőnév           | hőmérséklet   |
    | Egység                | F             |
    | Min                  | 60            |
    | Max                  | 110           |
    | Tizedeshelyek       | 0             |

    A telemetria megjelenítésének színét is kiválaszthatja. A telemetria-definíció mentéséhez válassza **mentése**:

    ![Hőmérsékleti szimuláció konfigurálása](./media/tutorial-define-device-type-experimental/temperaturesimulation.png)

7. Rövid idővel a **mérések** lapon látható a csatlakoztatott légkondicionálóját szimulált eszközről hőmérsékleti telemetria. A vezérlőkkel kezelheti a láthatóságot, az összesítést, vagy szerkesztheti a telemetria definícióját:

    ![Hőmérsékleti szimuláció megtekintése](./media/tutorial-define-device-type-experimental/viewsimulation.png)

8. A **Vonal**, **Halmozott** és **Időtartomány szerkesztése** vezérlőkkel is testreszabhatja a diagramot:

    ![A diagram testreszabása](./media/tutorial-define-device-type-experimental/customizechart.png)

## <a name="add-an-event-measurement"></a>Egy esemény mérték felvétele

Események segítségével határozza meg, amely az eszköz küld, amikor nincs hiba vagy összetevőhiba például időponthoz adatok. Az Azure IoT Central szimulálhatja eszköz események ahhoz, hogy tesztelje az alkalmazás viselkedésének valós eszköz csatlakoztatása előtt. Az eszköz-sablon esemény mértékek definiálása a **mérések** megtekintése.

1. Hozzáadása a **ventilátor Motor hiba** esemény mérési válassza **+ új mérték**. Ezután válassza az **Eseményt** a mérés típusaként:

    ![A csatlakoztatott légkondicionáló mérései](./media/tutorial-define-device-type-experimental/eventnew.png)

2. Az eszközsablonokhoz meghatározott minden eseménytípus tartalmaz [konfigurációs beállításokat](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), például a következőket:

   * Megjelenítendő név.

   * Mező neve.

   * Súlyosság.

    A **Ventilátormotor-hiba** esemény konfigurálásához használja a következő táblázatban lévő információkat:

    | Beállítás              | Érték             |
    | -------------------- | -----------       |
    | Megjelenítendő név         | Ventilátormotor-hiba   |
    | Mezőnév           | fanmotorerr       |
    | Severity             | Hiba             |

    Az esemény-definíció mentéséhez válassza **mentése**:

    ![Esemény mérésének konfigurálása](./media/tutorial-define-device-type-experimental/eventconfiguration.png)

3. Rövid idővel a **mérések** lapon látható a csatlakoztatott légkondicionálóját szimulált eszközről véletlenszerűen létrehozott eseményeket. A vezérlőkkel kezelheti a láthatóságot, vagy szerkesztheti az esemény definícióját:

    ![Esemény szimulációjának megtekintése](./media/tutorial-define-device-type-experimental/eventview.png)

1. Az esemény további részleteit, jelölje ki a diagramon az esemény:

    ![Eseményadatok megtekintése](./media/tutorial-define-device-type-experimental/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Állapot a mérés definiálása

Állapot segítségével határozza meg, és az eszköz vagy az összetevő állapotának megjelenítése egy időszakon belül. Az Azure IoT Central szimulálhatja ahhoz, hogy tesztelje az alkalmazás viselkedését, mielőtt valós eszköz csatlakoztatása az eszköz állapotát. A **Mérések** nézetben határozhatja meg az eszköztípusa állapotméréseit.

1. Hozzáadása egy **ventilátor mód** állapot mérési, jelölje be **+ új mérték**. Ezután válassza az **Állapotot** a mérés típusaként:

    ![Csatlakoztatott légkondicionáló állapotának mérései](./media/tutorial-define-device-type-experimental/statenew.png)

2. Megadhat egy eszköz sablon tartalmazza állapota minden típusú [konfigurációs beállítások](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) például:

   * Megjelenítendő név.

   * Mező neve.

   * Választható megjelenítendő feliratokkal rendelkező értékek.

   * Az egyes értékek színe.

    A **Ventilátor mód** állapot konfigurálásához használja a következő táblázatban lévő információkat:

    | Beállítás              | Érték             |
    | -------------------- | -----------       |
    | Megjelenítendő név         | Ventilátor mód          |
    | Mezőnév           | fanmode           |
    | Érték                | 1                 |
    | Felirat megjelenítése        | Üzemel         |
    | Érték                | 0                 |
    | Felirat megjelenítése        | Leállítva           |

    Az állapot mérési definíció mentéséhez válassza **mentése**:

    ![Állapot mérésének konfigurálása](./media/tutorial-define-device-type-experimental/stateconfiguration.png)

3. Rövid idővel a **mérések** lapon látható a véletlenszerűen előállított a csatlakoztatott légkondicionálóját szimulált eszközről az államok. A vezérlőkkel kezelheti a láthatóságot, vagy szerkesztheti az állapot definícióját:

    ![Állapot szimulációjának megtekintése](./media/tutorial-define-device-type-experimental/stateview.png)

4. Az eszköz által küldött belül egy kis időtartam túl sok adatpont esetén az állapot mértéket egy másik vizualizációval rendelkező jelenik meg. Válassza ki a diagram összes adatponttal adott időtartamon belül időrendi sorrendben jelennek meg. Le is szűkítheti az időtartományt, hogy részletesebben lássa a méréseket.

## <a name="settings-properties-and-commands"></a>Beállítások, tulajdonságok és parancsok

A beállítások, a tulajdonságok és a parancsok az eszközsablonban meghatározott különböző értékek, és az egyes eszközökhöz vannak társítva:

* _Beállítások_ használatával küldhet konfigurációs adatokat egy eszközre az alkalmazásból. Egy operátor például egy beállítás használatával módosíthatja az eszköz telemetriai időközét két másodpercről öt másodpercre. Módosításakor az operátornak egy beállítást, a beállítás mindig "folyamatban" van megjelölve a felhasználói felületen, amíg az eszköz egy visszaigazoló fűzi hozzá.

* _Tulajdonságok_ használatával határozhatja meg az eszközzel társított metaadatokat. Két tulajdonságkategória érhető el:
    
    * Az _alkalmazástulajdonságok_ használatával rögzítheti az eszközzel kapcsolatos információkat az alkalmazásban. Az alkalmazástulajdonságokkal rögzítheti például egy eszköz helyét és utolsó szervizelési dátumát. Ezek a tulajdonságok vannak tárolva az alkalmazást, és nem szinkronizálja az eszközzel. Egy operátor értékeket rendelhet a tulajdonságokhoz.

    * _Eszköztulajdonságok_ használatával engedélyezheti, hogy egy eszköz tulajdonságértékeket küldjön az alkalmazásnak. Ezeket a tulajdonságokat csak az eszköz módosíthatja. Az operátorok számára az eszköztulajdonságok írásvédettek. A csatlakoztatott légkondicionáló forgatókönyvében a belső vezérlőprogram verziója és az eszköz sorozatszáma az eszköz által jelentett eszköztulajdonság.
    
    További információkért lásd: [tulajdonságok](howto-set-up-template-experimental.md#properties) az eszköz sablon beállításával kapcsolatos útmutatója.

* A _parancsok_ használatával távoli helyszínről kezelheti az eszközét az alkalmazáson keresztül. Közvetlenül futtathat parancsokat az eszközön a felhőből az eszközök vezérléséhez. Az operátor olyan parancsokat futtathat, mint például az újraindítás, amellyel azonnal újraindíthatja az eszközt.

## <a name="use-settings"></a>Beállítások használata

*Beállítások* használatával engedélyezheti, hogy egy operátor konfigurációs adatokat küldjön egy eszközre. Ebben a szakaszban egy olyan beállítást adhat a **Csatlakoztatott légkondicionáló** eszközsablonhoz, amely lehetővé teszi, hogy egy operátor beállítsa a csatlakoztatott légkondicionáló célhőmérsékletét.

1. Keresse meg a **beállítások** lapján a **csatlakoztatott Légkondicionálóját** eszköz sablont.

2. Különböző típusokhoz, például számokhoz vagy szövegekhez hozhat létre beállításokat. Válassza ki **szám** száma beállítás az eszköz hozzáadásához.

3. A **Megadott hőmérséklet** beállítás konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Érték           |
    | -------------------- | -----------     |
    | Megjelenítendő név         | Megadott hőmérséklet |
    | Mezőnév           | setTemperature  |
    | Mértékegység      | F               |
    | Tizedesjegyek       | 1               |
    | Minimális érték        | 20              |
    | Maximális érték        | 200             |
    | Kezdeti érték        | 80              |
    | Leírás          | A légkondicionáló célhőmérsékletének beállítása |

    Válassza ki **mentése**:

    ![A Megadott hőmérséklet beállítás konfigurálása](./media/tutorial-define-device-type-experimental/configuresetting.png)

    > [!NOTE]
    > Amikor az eszköz elfogadja a beállítás módosítását, a beállítás **szinkronizálva** állapotra változik.

4. Elrendezésének testre szabhatja a **beállítások** áthelyezésével és a beállítások csempék átméretezése lapon:

    ![A beállítások elrendezésének testreszabása](./media/tutorial-define-device-type-experimental/settingslayout.png)

## <a name="use-properties"></a>Tulajdonságok használata

*Alkalmazástulajdonságok* használatával tárolhatja az eszközzel kapcsolatos információkat az alkalmazásban. Ebben a szakaszban alkalmazástulajdonságokat adhat hozzá a **Csatlakoztatott légkondicionáló** eszközsablonhoz az eszköz helyének és az utolsó szervizelés dátumának tárolásához. Ezek a tulajdonságok akkor szerkeszthető az alkalmazásban. Az eszköz azt is jelenti, például a sorozatszámot és a belső vezérlőprogram verziója, az alkalmazás írásvédett tulajdonságok.

1. Keresse meg a **tulajdonságok** lapján a **csatlakoztatott Légkondicionálóját** eszköz sablont.

1. Különböző típusú eszköztulajdonságokat hozhat létre, például számokat vagy szöveget. Ha hely tulajdonságot szeretne adni az eszközsablonhoz, válassza a **Hely** lehetőséget. A hely tulajdonság konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Érték                |
    | -------------------- | -------------------- |
    | Megjelenítendő név         | Hely             |
    | Mezőnév           | location             |
    | Kezdeti érték        | Seattle, WA          |
    | Leírás          | Eszközhely      |

    A többi mező alapértelmezett értékeit ne módosítsa.

    ![Az eszköztulajdonságok konfigurálása](./media/tutorial-define-device-type-experimental/configureproperties.png)

    Kattintson a **Mentés** gombra.

1. Ha az utolsó szervizelés dátuma tulajdonságot szeretne adni az eszközsablonhoz, válassza a **Dátum** lehetőséget.

1. Az utolsó szervizelés dátuma tulajdonság konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Érték                   |
    | -------------------- | ----------------------- |
    | Megjelenítendő név         | Utolsó szervizelés dátuma       |
    | Mezőnév           | serviceDate             |
    | Kezdeti érték        | 1/1/2019                |
    | Leírás          | Utolsó szervizelés           |

    ![Az eszköztulajdonságok konfigurálása](./media/tutorial-define-device-type-experimental/configureproperties2.png)

    Kattintson a **Mentés** gombra.

1. Elrendezésének testre szabhatja a **tulajdonságok** áthelyezése és átméretezése tulajdonság csempék lapon.

1. Ha egy eszköztulajdonságot, például a belső vezérlőprogram verzióját szeretné hozzáadni az eszközsablonhoz, válassza az **Eszköztulajdonság** lehetőséget.

1. A belső vezérlőprogram verziójának konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Érték                   |
    | -------------------- | ----------------------- |
    | Megjelenítendő név         | Belső vezérlőprogram verziója        |
    | Mezőnév           | firmwareVersion         |
    | Adattípus            | szöveg                    |
    | Leírás          | A légkondicionáló belső vezérlőprogramjának verziója |

    ![Belső vezérlőprogram verziójának konfigurálása](./media/tutorial-define-device-type-experimental/configureproperties3.png)

    Kattintson a **Mentés** gombra.

1. Ha egy eszköztulajdonságot, például sorozatszámot szeretne hozzáadni az eszközsablonhoz, válassza az **Eszköztulajdonság** lehetőséget.

1. A sorozatszám konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Érték                   |
    | -------------------- | ----------------------- |
    | Megjelenítendő név         | Sorozatszám           |
    | Mezőnév           | serialNumber            |
    | Adattípus            | szöveg                    |
    | Leírás          | A légkondicionáló sorozatszáma  |

    ![Sorozatszám konfigurálása](./media/tutorial-define-device-type-experimental/configureproperties4.png)

    Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Az eszköztulajdonságot az eszköz elküldi az alkalmazásnak. A belső vezérlőprogram verziójának és a sorozatszámnak az értékei frissülnek, amikor a valós eszköz csatlakozik az IoT Centralhoz.

## <a name="use-commands"></a>Parancsok használata

A _parancsok_ használatával engedélyezheti, hogy az operátor közvetlenül az eszközön futtasson parancsokat. Ebben a szakaszban egy olyan parancsot adhat a **Csatlakoztatott légkondicionáló** eszközsablonhoz, amely lehetővé teszi, hogy egy operátor a csatlakoztatott légkondicionálón adhasson vissza egy bizonyos üzenetet.

1. Keresse meg a **parancsok** lapján a **csatlakoztatott Légkondicionálóját** eszköz sablon a sablon szerkesztésével.

1. Válassza ki **+ új parancs** parancs hozzáadása az eszköz és az új parancs konfigurálásának megkezdéséhez.

1. Az új parancs konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Érték           |
    | -------------------- | -----------     |
    | Megjelenítendő név         | Echo parancs    |
    | Mezőnév           | echo            |
    | Alapértelmezett időtúllépés      | 30              |
    | Megjelenítés típusa         | szöveg            |
    | Leírás          | Eszközparancs  |  

    A következő paranccsal további bemenetek kiválasztásával adhat hozzá **+** a **beviteli mezők**.

    ![Felkészülés beállítás hozzáadására](./media/tutorial-define-device-type-experimental/commandsecho1.png)

     Kattintson a **Mentés** gombra.

1. Elrendezésének testre szabhatja a **parancsok** áthelyezése és átméretezése a parancs csempék lapon.

## <a name="view-your-simulated-device"></a>A szimulált eszköz megtekintése

Most már meghatározta a **Légkondicionálóját csatlakoztatott** eszköz-sablon testre szabhatja a **irányítópult** a mérések, beállítások és tulajdonságok meghatározott tartalmazza. Ezután operátorként megtekintheti az irányítópult előnézetét:

1. Válassza ki a **irányítópult** lapján a **csatlakoztatott Légkondicionálóját** eszköz sablont.

1. Válassza ki **vonaldiagram** alakzatot a összetevő hozzáadása a **irányítópult**.

1. Konfigurálja a **Vonaldiagram** összetevőt a következő táblázatban lévő információk használatával:

    | Beállítás      | Érték       |
    | ------------ | ----------- |
    | Cím        | Hőmérséklet |
    | Időtartomány   | Az elmúlt 30 perc |
    | Mértékek     | Hőmérséklet (válasszon **láthatósági** melletti **hőmérséklet**) |

    ![Vonaldiagram beállításai](./media/tutorial-define-device-type-experimental/linechartsettings.png)

    Ezután válassza a **Save** (Mentés) lehetőséget.

1. Válassza ki a **Eseményelőzmények** összetevő az alábbi táblázatban szereplő információk segítségével:

    | Beállítás      | Érték       |
    | ------------ | ----------- |
    | Cím        | Motor események ventilátor |
    | Időtartomány   | Az elmúlt 30 perc |
    | Mértékek     | Motor hiba ventilátor (válasszon **látható-e** melletti **ventilátor Motor hiba**) |

    ![Esemény diagram beállításai](./media/tutorial-define-device-type-experimental/dashboardeventchartsetting.png)

    Ezután válassza a **Save** (Mentés) lehetőséget.

1. Konfigurálja az **Állapotelőzmények** összetevőt a következő táblázatban lévő információk használatával:

    | Beállítás      | Érték       |
    | ------------ | ----------- |
    | Cím        | Ventilátor mód |
    | Időtartomány   | Az elmúlt 30 perc |
    | Mértékek | Mód ventilátor (válasszon **látható-e** melletti **ventilátor mód**) |

    ![Vonaldiagram beállításai](./media/tutorial-define-device-type-experimental/dashboardstatechartsetting.png)

    Ezután válassza a **Save** (Mentés) lehetőséget.

1. Az Eszközbeállítások, illetve a Tulajdonságok hozzáadása az irányítópulthoz, válassza a **beállításait és tulajdonságait**. Válassza ki **hozzáadása/eltávolítása** , adja meg a beállításokat, vagy a tulajdonságok, adja meg az irányítópult megtekintéséhez.

1. Konfigurálja a **Beállítások és tulajdonságok** összetevőt a következő táblázatban lévő információk használatával:

    | Beállítás                 | Érték         |
    | ----------------------- | ------------- |
    | Cím                   | Eszköztulajdonságok |
    | Beállítások és tulajdonságok | Megadott hőmérséklet<br/>Sorozatszám<br/>Belső vezérlőprogram verziója |

    Beállítások és a korábban definiált tulajdonságok a **beállításait és tulajdonságait** lapok láthatók **elérhető oszlopok**.

    ![A hőmérséklet-tulajdonság beállításainak megadása](./media/tutorial-define-device-type-experimental/propertysettings4.png)

    Ezután válassza a **Save** (Mentés) lehetőséget.

1. Most már megtekintheti szimulált adatok számára a csatlakoztatott Légkondicionálóját az irányítópulton. A csempék és az irányítópult elrendezése szerkesztheti:

    ![Az irányítópult megtekintése](./media/tutorial-define-device-type-experimental/dashboard.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Új eszközsablon létrehozása
> * Telemetria hozzáadása az eszközhöz
> * Szimulált telemetria megtekintése
> * Eszközesemények meghatározása
> * Szimulált események megtekintése
> * Az állapot meghatározása
> * Szimulált állapot megtekintése
> * Beállítások és tulajdonságok használata
> * Parancsok használata
> * A szimulált eszköz megtekintése az irányítópulton

Most, hogy egy eszköz sablont az Azure IoT Central alkalmazásban definiált, Íme a javasolt következő lépések:

* [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Operátori nézetek testreszabása](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)