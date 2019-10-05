---
title: Új eszköztípus definiálása az Azure IoT Centralban | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan definiálhat új eszköztípust az Azure IoT Central alkalmazásban. Meghatározza a típus telemetriáját, állapotát, tulajdonságait és beállításait.
author: dominicbetts
ms.author: dobett
ms.date: 06/07/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: a3faa76c1506664a075648edc7d57fbba542b011
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960533"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Oktatóanyag: Új eszköz típusának meghatározása az Azure IoT Central alkalmazásban

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan definiálhat új eszköztípust egy eszközsablon használatával a Microsoft Azure IoT Central alkalmazásban. Az eszközsablon határozza meg az eszköztípus telemetriáját, állapotát, tulajdonságait és beállításait.

A valós eszköz csatlakoztatása előtt az alkalmazás teszteléséhez az IoT Central létrehoz egy szimulált eszközt az eszközsablonból annak létrehozásakor.

Ebben az oktatóanyagban egy **Csatlakoztatott légkondicionáló** eszközsablont hoz létre. Egy csatlakoztatott légkondicionáló eszköz:

* Telemetriát küld, például a hőmérsékletet és a páratartalmat.
* A jelentések állapota, például az, hogy be van-e kapcsolva.
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

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure IoT Central-alkalmazásra. Ha elvégezte az [Azure IoT Central-alkalmazás létrehozása](quick-deploy-iot-central.md) rövid útmutatót, újból felhasználhatja az abban létrehozott alkalmazást. Egyébként hajtsa végre az alábbi lépéseket egy üres Azure IoT Central-alkalmazás létrehozásához:

1. Navigáljon az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyére.

2. Írja be az Azure-előfizetés eléréséhez használt e-mail-címet és jelszót:

    ![Lépjen a céges fiókjába](./media/tutorial-define-device-type/sign-in.png)

3. Új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **új alkalmazás**lehetőséget:

    ![Az Azure IoT Central Application Manager lapja](./media/tutorial-define-device-type/iotcentralhome.png)

4. Új Azure IoT Central-alkalmazás létrehozása:
    
   * Válassza a **Próba** elemet. Próbaalkalmazás létrehozásához nincs szükség Azure-előfizetésre.
    
      A könyvtárakkal és előfizetésekkel kapcsolatban további információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
    
   * Válassza az **Egyéni alkalmazás** elemet.
    
   * Választhat egy rövid alkalmazásnevet is, például a **Contoso-légkondicionálók** nevet. Az Azure IoT Central létrehoz egy egyéni URL-előtagot. Ezt az URL-előtagot egy könnyebben megjegyezhető előtagra módosíthatja.
    
   * Kattintson a **Létrehozás** gombra.

     ![Azure IoT Central Alkalmazás létrehozása oldal](./media/tutorial-define-device-type/iotcentralcreate.png)

     További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).

## <a name="create-a-device-template"></a>Eszközsablon létrehozása

Szerkesztőként létrehozhatja és szerkesztheti az alkalmazásban lévő eszközsablonokat. Egy eszközsablon létrehozásakor az Azure IoT Central létrehoz egy szimulált eszközt a sablonból. A szimulált eszköz olyan telemetria hoz létre, amely lehetővé teszi az alkalmazás viselkedésének tesztelését a valódi eszköz csatlakoztatása előtt.

Új sablon az alkalmazáshoz való hozzáadásához nyissa meg az **eszközök sablonjai** lapot. Ehhez válassza ki az **eszközök sablonjait** a bal oldali navigációs menüben.

![Eszközök sablonjai lap](./media/tutorial-define-device-type/devicetemplates.png)

## <a name="add-a-device-template"></a>Eszköz sablonjának hozzáadása

A következő lépések bemutatják, hogyan hozhat létre egy új **Csatlakoztatott légkondicionáló** eszközsablont olyan eszközökhöz, amelyek hőmérsékleti telemetriát küldenek az alkalmazásra:

1. Az **eszközök sablonjai** lapon válassza az **+ új**lehetőséget:

    ![Eszközök sablonjai lap, eszköz sablonjának létrehozása](./media/tutorial-define-device-type/newtemplate.png)

2. A lapon a választható sablonok láthatók.

    ![Eszköz sablonjának könyvtára](./media/tutorial-define-device-type/devicetemplatelibrary.png)

3. Válassza az **Egyéni**lehetőséget, írja be a **csatlakoztatott légkondicionáló** nevet az eszköz sablonjának neveként, majd válassza a **Létrehozás**lehetőséget. Az eszköz képét is feltöltheti, amelyet az operátorok láthatnak az Eszközkeresőben:

    ![Egyéni eszköz](./media/tutorial-define-device-type/createcustomdevice.png)

4. A **csatlakoztatott légkondicionáló** eszköz sablonjában ellenőrizze, hogy a **mérések** lapon a telemetria határozza meg. Minden egyes definiált sablon külön lapokkal rendelkezik a következő eszközökhöz:

   * Adja meg az eszköz által eljuttatott _mértékeket_, például a telemetria, az eseményt és az állapotot.

   * Az eszköz vezérléséhez használt _Beállítások_ megadása.

   * Adja meg az eszköz metaadatainak _tulajdonságait_ .

   * A közvetlenül az eszközön futtatandó _parancsok_ megadása.

   * Az eszközhöz társított _szabályok_ megadása.

   * Szabja testre az eszköz _irányítópultját_ a kezelők számára.

     ![A légkondicionáló mérései](./media/tutorial-define-device-type/airconmeasurements.png)

     > [!NOTE]
     > Ha módosítani szeretné az eszköz sablonjának nevét, válassza ki a sablon nevét az oldal tetején.

5. A hőmérséklet telemetria mértékének hozzáadásához válassza az **+ új mérték**lehetőséget. Ezután válassza a **Telemetriát** a mérés típusaként:

    ![A csatlakoztatott légkondicionáló mérései](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. Az eszközsablonokhoz meghatározott minden telemetriatípus tartalmaz [konfigurációs beállításokat](howto-set-up-template.md), például a következőket:

   * Megjelenítési beállítások.

   * A telemetria részletei.

   * Szimulációs paraméterek.

     A **Hőmérséklet** telemetria konfigurálásához használja a következő táblázatban lévő információkat:

     | Beállítás              | Value         |
     | -------------------- | -----------   |
     | Megjelenítendő név         | Hőmérséklet   |
     | Mezőnév           | hőmérséklet   |
     | Mértékegységek                | P             |
     | Min                  | 60            |
     | Max                  | 110           |
     | Tizedeshelyek       | 0             |

     A telemetria megjelenítésének színét is kiválaszthatja. A telemetria-definíció mentéséhez válassza a **Mentés**lehetőséget:

     ![Hőmérsékleti szimuláció konfigurálása](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Rövid idő elteltével a **mérések** lap a szimulált csatlakoztatott légkondicionáló eszköz hőmérsékleti telemetria diagramját jeleníti meg. A vezérlőkkel kezelheti a láthatóságot, az összesítést, vagy szerkesztheti a telemetria definícióját:
 
    > [!NOTE]
    > A telemetria esetében az **átlag** az alapértelmezett összesítésként van beállítva. 

    ![Hőmérsékleti szimuláció megtekintése](./media/tutorial-define-device-type/viewsimulation.png)

8. A **Vonal**, **Halmozott** és **Időtartomány szerkesztése** vezérlőkkel is testreszabhatja a diagramot:

    ![A diagram testreszabása](./media/tutorial-define-device-type/customizechart.png)

## <a name="add-an-event-measurement"></a>Esemény mértékének megadása

Az események segítségével meghatározhatja az adott időponthoz tartozó adatokat, amelyeket az eszköz akkor küld, amikor egy esemény, például egy hiba vagy egy összetevő meghibásodik. Az Azure IoT Central képes szimulálni az eszköz eseményeit, hogy a valódi eszköz csatlakoztatása előtt tesztelje az alkalmazás viselkedését. Adja meg az eszköz sablonjának mérőszámait a **mérések** nézetben.

1. A **ventilátor motoros hiba** eseményének méréséhez válassza az **+ új mérték**lehetőséget. Ezután válassza az **Eseményt** a mérés típusaként:

    ![A csatlakoztatott légkondicionáló mérései](./media/tutorial-define-device-type/eventnew.png)

2. Az eszközsablonokhoz meghatározott minden eseménytípus tartalmaz [konfigurációs beállításokat](howto-set-up-template.md), például a következőket:

   * Megjelenítendő név.

   * Mező neve.

   * Súlyosság.

     A **Ventilátormotor-hiba** esemény konfigurálásához használja a következő táblázatban lévő információkat:

     | Beállítás              | Value             |
     | -------------------- | -----------       |
     | Megjelenítendő név         | Ventilátormotor-hiba   |
     | Mezőnév           | fanmotorerr       |
     | severity             | Hiba             |

     Az esemény definíciójának mentéséhez válassza a **Mentés**lehetőséget:

     ![Esemény mérésének konfigurálása](./media/tutorial-define-device-type/eventconfiguration.png)

3. Rövid idő elteltével a **mérések** lap a szimulált csatlakoztatott légkondicionáló eszközből véletlenszerűen generált események diagramját jeleníti meg. A vezérlőkkel kezelheti a láthatóságot, vagy szerkesztheti az esemény definícióját:

    ![Esemény szimulációjának megtekintése](./media/tutorial-define-device-type/eventview.png)

1. Az esemény további részleteinek megtekintéséhez válassza ki az eseményt a diagramon:

    ![Eseményadatok megtekintése](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Állapot mértékének meghatározása

Az állapot használatával meghatározhatja és megjelenítheti az eszköz vagy az összetevő állapotát egy adott időszakban. Az Azure IoT Central képes szimulálni az eszköz állapotát, hogy a valódi eszköz csatlakoztatása előtt tesztelje az alkalmazás viselkedését. A **Mérések** nézetben határozhatja meg az eszköztípusa állapotméréseit.

1. **Ventilátoros üzemmód** állapotának méréséhez válassza az **+ új mérték**lehetőséget. Ezután válassza az **Állapotot** a mérés típusaként:

    ![Csatlakoztatott légkondicionáló állapotának mérései](./media/tutorial-define-device-type/statenew.png)

2. Az adott eszközhöz definiált minden egyes állapot olyan [konfigurációs beállításokat](howto-set-up-template.md) tartalmaz, mint például a következő:

   * Megjelenítendő név.

   * Mező neve.

   * Választható megjelenítendő feliratokkal rendelkező értékek.

   * Az egyes értékek színe.

     A **Ventilátor mód** állapot konfigurálásához használja a következő táblázatban lévő információkat:

     | Beállítás              | Value             |
     | -------------------- | -----------       |
     | Megjelenítendő név         | Ventilátor mód          |
     | Mezőnév           | fanmode           |
     | Value                | 1                 |
     | Felirat megjelenítése        | Üzemel         |
     | Value                | 0                 |
     | Felirat megjelenítése        | Leállítva           |

     Az állapot-mérési definíció mentéséhez válassza a **Mentés**lehetőséget:

     ![Állapot mérésének konfigurálása](./media/tutorial-define-device-type/stateconfiguration.png)

3. Rövid idő elteltével a **mérések** lap a szimulált csatlakoztatott légkondicionáló eszközből véletlenszerűen generált állapotok diagramját jeleníti meg. A vezérlőkkel kezelheti a láthatóságot, vagy szerkesztheti az állapot definícióját:

    ![Állapot szimulációjának megtekintése](./media/tutorial-define-device-type/stateview.png)

4. Ha túl sok adatpontot küld az eszköz egy kis időtartamon belül, az állapot mérése egy másik vizualizációval jelenik meg. Válassza ki a diagramot, hogy az adott időszakon belül az összes adatpont megjelenjen időrendi sorrendben. Le is szűkítheti az időtartományt, hogy részletesebben lássa a méréseket.

## <a name="settings-properties-and-commands"></a>Beállítások, tulajdonságok és parancsok

A beállítások, a tulajdonságok és a parancsok az eszközsablonban meghatározott különböző értékek, és az egyes eszközökhöz vannak társítva:

* _Beállítások_ használatával küldhet konfigurációs adatokat egy eszközre az alkalmazásból. Egy operátor például egy beállítás használatával módosíthatja az eszköz telemetriai időközét két másodpercről öt másodpercre. Amikor egy operátor megváltoztatja a beállítást, a beállítás a felhasználói felületen függőben van megjelölve, amíg az eszköz nem válaszol a nyugtára.

* _Tulajdonságok_ használatával határozhatja meg az eszközzel társított metaadatokat. Két tulajdonságkategória érhető el:
    
  * Az _alkalmazástulajdonságok_ használatával rögzítheti az eszközzel kapcsolatos információkat az alkalmazásban. Az alkalmazástulajdonságokkal rögzítheti például egy eszköz helyét és utolsó szervizelési dátumát. Ezek a tulajdonságok az alkalmazásban tárolódnak, és nem szinkronizálhatók az eszközzel. Egy operátor értékeket rendelhet a tulajdonságokhoz.

  * _Eszköztulajdonságok_ használatával engedélyezheti, hogy egy eszköz tulajdonságértékeket küldjön az alkalmazásnak. Ezeket a tulajdonságokat csak az eszköz módosíthatja. Az operátorok számára az eszköztulajdonságok írásvédettek. A csatlakoztatott légkondicionáló forgatókönyvében a belső vezérlőprogram verziója és az eszköz sorozatszáma az eszköz által jelentett eszköztulajdonság.
    
    További információkért lásd az eszköz sablonjának beállítását ismertető útmutató [Tulajdonságok](howto-set-up-template.md#properties) részében.

* A _parancsok_ használatával távoli helyszínről kezelheti az eszközét az alkalmazáson keresztül. Közvetlenül futtathat parancsokat az eszközön a felhőből az eszközök vezérléséhez. Az operátor olyan parancsokat futtathat, mint például az újraindítás, amellyel azonnal újraindíthatja az eszközt.

## <a name="use-settings"></a>Beállítások használata

*Beállítások* használatával engedélyezheti, hogy egy operátor konfigurációs adatokat küldjön egy eszközre. Ebben a szakaszban egy olyan beállítást adhat a **Csatlakoztatott légkondicionáló** eszközsablonhoz, amely lehetővé teszi, hogy egy operátor beállítsa a csatlakoztatott légkondicionáló célhőmérsékletét.

1. Navigáljon a **Beállítások** lapra a **csatlakoztatott légkondicionáló** eszköz sablonja számára.

2. Különböző típusokhoz, például számokhoz vagy szövegekhez hozhat létre beállításokat. A **szám** lehetőség kiválasztásával adhat hozzá egy számsorozatot az eszközhöz.

3. A **Megadott hőmérséklet** beállítás konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Value           |
    | -------------------- | -----------     |
    | Megjelenítendő név         | Megadott hőmérséklet |
    | Mezőnév           | setTemperature  |
    | Mértékegység      | P               |
    | Tizedesjegyek       | 1               |
    | Minimális érték        | 20              |
    | Maximális érték        | 200             |
    | Kezdeti érték        | 80              |
    | Leírás          | A légkondicionáló célhőmérsékletének beállítása |

    Ezután válassza a **Mentés**lehetőséget:

    ![A Megadott hőmérséklet beállítás konfigurálása](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Amikor az eszköz elfogadja a beállítás módosítását, a beállítás **szinkronizálva** állapotra változik.

4. **A beállítások lap** elrendezését a beállítások csempék áthelyezésével és átméretezésével szabhatja testre:

    ![A beállítások elrendezésének testreszabása](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Tulajdonságok használata

*Alkalmazástulajdonságok* használatával tárolhatja az eszközzel kapcsolatos információkat az alkalmazásban. Ebben a szakaszban alkalmazástulajdonságokat adhat hozzá a **Csatlakoztatott légkondicionáló** eszközsablonhoz az eszköz helyének és az utolsó szervizelés dátumának tárolásához. Ezek a tulajdonságok szerkeszthetők az alkalmazásban. Az eszköz olyan tulajdonságokat is jelent, mint például a sorozatszám és a belső vezérlőprogram verziója, amely csak olvasható az alkalmazásban.

1. Navigáljon a **csatlakoztatott légkondicionáló** eszközéhez tartozó **Tulajdonságok** lapra.

1. Különböző típusú eszköztulajdonságokat hozhat létre, például számokat vagy szöveget. Ha hely tulajdonságot szeretne adni az eszközsablonhoz, válassza a **Hely** lehetőséget. A hely tulajdonság konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Value                |
    | -------------------- | -------------------- |
    | Megjelenítendő név         | Location             |
    | Mező neve           | location             |
    | Kezdeti érték        | Seattle, WA          |
    | Leírás          | Eszközhely      |

    A többi mező alapértelmezett értékeit ne módosítsa.

    ![Az eszköztulajdonságok konfigurálása](./media/tutorial-define-device-type/configureproperties.png)

    Kattintson a **Mentés** gombra.

1. Ha az utolsó szervizelés dátuma tulajdonságot szeretne adni az eszközsablonhoz, válassza a **Dátum** lehetőséget.

1. Az utolsó szervizelés dátuma tulajdonság konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Value                   |
    | -------------------- | ----------------------- |
    | Megjelenítendő név         | Utolsó szervizelés dátuma       |
    | Mező neve           | serviceDate             |
    | Kezdeti érték        | 1/1/2019                |
    | Leírás          | Utolsó szervizelés           |

    ![Az eszköztulajdonságok konfigurálása](./media/tutorial-define-device-type/configureproperties2.png)

    Kattintson a **Mentés** gombra.

1. A **Tulajdonságok** lap elrendezését a tulajdonságlapok áthelyezésével és átméretezésével szabhatja testre.

1. Ha egy eszköztulajdonságot, például a belső vezérlőprogram verzióját szeretné hozzáadni az eszközsablonhoz, válassza az **Eszköztulajdonság** lehetőséget.

1. A belső vezérlőprogram verziójának konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Value                   |
    | -------------------- | ----------------------- |
    | Megjelenítendő név         | Belső vezérlőprogram verziója        |
    | Mezőnév           | firmwareVersion         |
    | Adattípus            | text                    |
    | Leírás          | A légkondicionáló belső vezérlőprogramjának verziója |

    ![Belső vezérlőprogram verziójának konfigurálása](./media/tutorial-define-device-type/configureproperties3.png)

    Kattintson a **Mentés** gombra.

1. Ha egy eszköztulajdonságot, például sorozatszámot szeretne hozzáadni az eszközsablonhoz, válassza az **Eszköztulajdonság** lehetőséget.

1. A sorozatszám konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Value                   |
    | -------------------- | ----------------------- |
    | Megjelenítendő név         | Sorozatszám           |
    | Mezőnév           | serialNumber            |
    | Adattípus            | text                    |
    | Leírás          | A légkondicionáló sorozatszáma  |

    ![Sorozatszám konfigurálása](./media/tutorial-define-device-type/configureproperties4.png)

    Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Az eszköztulajdonságot az eszköz elküldi az alkalmazásnak. A belső vezérlőprogram verziójának és a sorozatszámnak az értékei frissülnek, amikor a valós eszköz csatlakozik az IoT Centralhoz.

## <a name="use-commands"></a>Parancsok használata

A _parancsok_ használatával engedélyezheti, hogy az operátor közvetlenül az eszközön futtasson parancsokat. Ebben a szakaszban egy olyan parancsot adhat a **Csatlakoztatott légkondicionáló** eszközsablonhoz, amely lehetővé teszi, hogy egy operátor a csatlakoztatott légkondicionálón adhasson vissza egy bizonyos üzenetet.

1. A sablon szerkesztéséhez navigáljon a **csatlakoztatott légkondicionáló** eszköz sablonjának **parancsok** lapjára.

1. Válassza az **+ új parancs** lehetőséget az eszközhöz tartozó parancs hozzáadásához és az új parancs konfigurálásának megkezdéséhez.

1. Az új parancs konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Value           |
    | -------------------- | -----------     |
    | Megjelenítendő név         | Echo parancs    |
    | Mező neve           | echo            |
    | Alapértelmezett határidő-túllépés      | 30              |
    | Adattípus         | text            |
    | Leírás          | Eszközparancs  |  

    További bemeneteket is hozzáadhat a parancshoz, ha a **beviteli mezőkhöz**a **+** elemet választja.

    ![Felkészülés beállítás hozzáadására](./media/tutorial-define-device-type/commandsecho1.png)

     Kattintson a **Mentés** gombra.

1. A **parancsok** lap elrendezését úgy szabhatja testre, hogy áthelyezi és átméretezi a parancs csempéit.

## <a name="view-your-simulated-device"></a>A szimulált eszköz megtekintése

Most, hogy meghatározta a **csatlakoztatott légkondicionáló** eszköz sablonját, testreszabhatja az **irányítópultját** , hogy tartalmazza a definiált mértékeket, beállításokat és tulajdonságokat. Ezután operátorként megtekintheti az irányítópult előnézetét:

1. Válassza az **irányítópult** fület a **csatlakoztatott légkondicionáló** eszköz sablonhoz.

1. Válassza ki a **diagramot** , és adja hozzá az összetevőt az **irányítópulthoz**.

1. Konfigurálja a **Vonaldiagram** összetevőt a következő táblázatban lévő információk használatával:

    | Beállítás      | Value       |
    | ------------ | ----------- |
    | Beosztás        | Hőmérséklet |
    | Időtartomány   | Az elmúlt 30 perc |
    | Mértékek     | Hőmérséklet ( **láthatóság** kijelölése a **hőmérséklet**mellett) |

    ![Vonaldiagram beállításai](./media/tutorial-define-device-type/linechartsettings.png)

    Ezután válassza a **Save** (Mentés) lehetőséget.

1. Válassza ki az **esemény előzményei** összetevőt az alábbi táblázatban szereplő információk alapján:

    | Beállítás      | Value       |
    | ------------ | ----------- |
    | Beosztás        | Ventilátoros motoros események |
    | Időtartomány   | Az elmúlt 30 perc |
    | Mértékek     | Ventilátoros motor hibája (a **ventilátoros motor hibája**melletti **láthatóság** kiválasztása) |

    ![Az események diagramjának beállításai](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Ezután válassza a **Save** (Mentés) lehetőséget.

1. Konfigurálja az **Állapotelőzmények** összetevőt a következő táblázatban lévő információk használatával:

    | Beállítás      | Value       |
    | ------------ | ----------- |
    | Beosztás        | Ventilátor mód |
    | Időtartomány   | Az elmúlt 30 perc |
    | Mértékek | Ventilátor mód (válassza a **láthatóság** lehetőséget a **ventilátor mód**mellett) |

    ![Vonaldiagram beállításai](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Ezután válassza a **Save** (Mentés) lehetőséget.

1. Az eszközbeállítások és a tulajdonságok az irányítópulthoz való hozzáadásához válassza a **beállítások és tulajdonságok**lehetőséget. Válassza a **Hozzáadás/Eltávolítás** lehetőséget az irányítópulton megjeleníteni kívánt beállítások vagy tulajdonságok hozzáadásához.

1. Konfigurálja a **Beállítások és tulajdonságok** összetevőt a következő táblázatban lévő információk használatával:

    | Beállítás                 | Value         |
    | ----------------------- | ------------- |
    | Beosztás                   | Eszköztulajdonságok |
    | Beállítások és tulajdonságok | Megadott hőmérséklet<br/>Sorozatszám<br/>Belső vezérlőprogram verziója |

    A **beállítások és tulajdonságok** lapon korábban definiált beállítások és tulajdonságok a **rendelkezésre álló oszlopokban**jelennek meg.

    ![A hőmérséklet-tulajdonság beállításainak megadása](./media/tutorial-define-device-type/propertysettings4.png)

    Ezután válassza a **Save** (Mentés) lehetőséget.

1. Most már megtekintheti a csatlakoztatott klíma szimulált adatait az irányítópulton. Az irányítópult csempéit és elrendezését az alábbiak szerint szerkesztheti:

    ![Irányítópult megtekintése](./media/tutorial-define-device-type/dashboard.png)

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

Most, hogy meghatározta az eszköz sablonját az Azure IoT Central-alkalmazásban, a következő lépéseket ajánljuk:

* [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules.md)
* [Operátori nézetek testreszabása](tutorial-customize-operator.md)