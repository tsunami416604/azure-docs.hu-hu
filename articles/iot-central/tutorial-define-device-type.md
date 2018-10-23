---
title: Új eszköztípus definiálása az Azure IoT Centralban | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan definiálhat új eszköztípust az Azure IoT Central alkalmazásban. Meghatározza a típus telemetriáját, állapotát, tulajdonságait és beállításait.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 246d7f837fdf72ddb2d24139d2d3d83fcd43b640
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310648"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Oktatóanyag: Új eszköztípus definiálása az Azure IoT Central-alkalmazásban

Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan definiálhat új eszköztípust egy eszközsablon használatával a Microsoft Azure IoT Central alkalmazásban. Az eszközsablon határozza meg az eszköztípus telemetriáját, állapotát, tulajdonságait és beállításait.

A valós eszköz csatlakoztatása előtt az alkalmazás teszteléséhez az Azure IoT Central létrehoz egy szimulált eszközt az eszközsablonból annak létrehozásakor.

Ebben az oktatóanyagban egy **Csatlakoztatott légkondicionáló** eszközsablont hoz létre. Egy csatlakoztatott légkondicionáló eszköz:

* Telemetriát küld, például a hőmérsékletet és a páratartalmat.
* Jelenti az állapotot, például hogy be vagy ki van-e kapcsolva.
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

1. Lépjen az Azure IoT Central [Alkalmazáskezelő](https://aka.ms/iotcentral) oldalára.

2. Írja be az Azure-előfizetés eléréséhez használt e-mail-címet és jelszót:

   ![Lépjen a céges fiókjába](./media/tutorial-define-device-type/sign-in.png)

3. Egy új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **Új alkalmazás** lehetőséget:

    ![Azure IoT Central Alkalmazáskezelő oldal](./media/tutorial-define-device-type/iotcentralhome.png)

4. Új Azure IoT Central-alkalmazás létrehozása:
    
    * Válassza az **Ingyenes** elemet. A 7 napos ingyenes próbaidőszaknak nincsenek előfizetési követelményei.
    
       A könyvtárakkal és előfizetésekkel kapcsolatban további információért lásd az [Azure IoT Central-alkalmazás létrehozását](howto-create-application.md) ismertető cikket.
    
    * Válassza az **Egyéni alkalmazás** elemet.
    
    * Választhat egy rövid alkalmazásnevet is, például a **Contoso-légkondicionálók** nevet. Az Azure IoT Central létrehoz egy egyéni URL-előtagot. Ezt az URL-előtagot egy könnyebben megjegyezhető előtagra módosíthatja.
    
    * Válassza a **Létrehozás** elemet.

    ![Azure IoT Central Alkalmazás létrehozása oldal](./media/tutorial-define-device-type/iotcentralcreatenew.png)

    További információt az [Azure IoT Central-alkalmazás létrehozását](howto-create-application.md) ismertető cikkben talál.

## <a name="create-a-new-custom-device-template"></a>Új egyéni ügyfélsablon létrehozása

Szerkesztőként létrehozhatja és szerkesztheti az alkalmazásban lévő eszközsablonokat. Egy eszközsablon létrehozásakor az Azure IoT Central létrehoz egy szimulált eszközt a sablonból. A szimulált eszköz olyan telemetriát készít, amely lehetővé teszi, hogy tesztelje az alkalmazás viselkedését egy fizikai eszköz csatlakoztatása előtt.

Ha az új eszközsablont az alkalmazáshoz szeretné adni, az **Alkalmazásszerkesztő** oldalra kell lépnie. Ehhez válassza a bal oldali navigációs menüben az **Alkalmazásszerkesztő** lehetőséget.

![Alkalmazásszerkesztő oldal](./media/tutorial-define-device-type/builderhome.png)

## <a name="add-a-device-and-define-telemetry"></a>Eszköz hozzáadása és telemetria meghatározása

A következő lépések bemutatják, hogyan hozhat létre egy új **Csatlakoztatott légkondicionáló** eszközsablont olyan eszközökhöz, amelyek hőmérsékleti telemetriát küldenek az alkalmazásra:

1. Az **Alkalmazásszerkesztő** oldalon válassza az **Eszközsablon-készítő** lehetőséget:

    ![Alkalmazásszerkesztő oldal, Eszközsablon-készítő](./media/tutorial-define-device-type/builderhomedevices.png)

2. Az **Eszközsablonok** oldalon válassza az **Egyéni** lehetőséget. Az **Egyéni** eszközsablonok lehetővé teszik, hogy meghatározza a csatlakoztatott légkondicionáló összes jellemzőjét és viselkedését:

    ![Eszközök](./media/tutorial-define-device-type/builderhomedevicescustom.png)

3. Az **Új eszközsablon** oldalon írja be a **Csatlakoztatott légkondicionáló** nevet az eszközhöz, majd válassza a **Létrehozás** lehetőséget. Az eszköz képét is feltöltheti, amelyet az operátorok láthatnak az Eszközkeresőben:

    ![Egyéni eszköz](./media/tutorial-define-device-type/createcustomdevice.png)

4. A **Csatlakoztatott légkondicionáló** eszközsablonban győződjön meg arról, hogy a **Mérések** oldalon legyen, ahol meghatározhatja a telemetriát. Minden meghatározott eszközsablon különálló lapokkal rendelkezik a következőkhöz:

    * Az eszköz által küldött mérések, például telemetria, esemény és állapot meghatározása.
    
    * Az eszköz vezérléséhez használt beállítások meghatározása.
    
    * Az eszköz metaadatait alkotó tulajdonságok meghatározása.

    * A közvetlenül az eszközön futtatni kívánt parancsok meghatározása.
    
    * Az eszközhöz tartozó szabályok meghatározása.
    
    * Az eszköz irányítópultjának testreszabása az operátorok számára.

    Amikor eszközsablont határoz meg, először mindig válassza a **Sablon szerkesztése** parancsot a sablon szerkesztéséhez. Ha elkészült, válassza a **Kész** lehetőséget. 

    ![A légkondicionáló mérései](./media/tutorial-define-device-type/airconmeasurements.png)

    > [!NOTE]
    > Az eszköz vagy az eszközsablon nevének módosításához kattintson az oldal tetején lévő szövegre.

5. A hőmérsékleti telemetriamérés hozzáadásához válassza az **Új mérés** elemet. Ezután válassza a **Telemetriát** a mérés típusaként:

    ![A csatlakoztatott légkondicionáló mérései](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. Az eszközsablonokhoz meghatározott minden telemetriatípus tartalmaz [konfigurációs beállításokat](howto-set-up-template.md), például a következőket:

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

    A telemetria megjelenítésének színét is kiválaszthatja. A telemetria definíciójának mentéséhez válassza a **Mentés** lehetőséget:

    ![Hőmérsékleti szimuláció konfigurálása](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Egy rövid idő múlva a **Mérések** oldal megjeleníti a szimulált csatlakoztatott légkondicionáló eszköz hőmérsékleti telemetriájának diagramját. A vezérlőkkel kezelheti a láthatóságot, az összesítést, vagy szerkesztheti a telemetria definícióját:

    ![Hőmérsékleti szimuláció megtekintése](./media/tutorial-define-device-type/viewsimulation.png)

8. A **Vonal**, **Halmozott** és **Időtartomány szerkesztése** vezérlőkkel is testreszabhatja a diagramot:

    ![A diagram testreszabása](./media/tutorial-define-device-type/customizechart.png)

## <a name="define-event-measurement"></a>Esemény mérésének meghatározása

Események használatával határozhatja meg az eszköz által küldött, időponthoz kötött adatokat valamilyen fontos történés, például hiba vagy összetevőhiba jelzéséhez. A telemetriamérésekhez hasonlóan az Azure IoT Central képes eszközeseményeket szimulálni az alkalmazás tesztelése érdekében a fizikai eszköz csatlakoztatása előtt. A **Mérések** nézetben határozhatja meg az eszköztípus eseményméréseit.

1. A **Ventilátormotor-hiba** eseménymérés hozzáadásához válassza az **Új mérés** elemet. Ezután válassza az **Eseményt** a mérés típusaként:

    ![A csatlakoztatott légkondicionáló mérései](./media/tutorial-define-device-type/eventnew.png)

2. Az eszközsablonokhoz meghatározott minden eseménytípus tartalmaz [konfigurációs beállításokat](howto-set-up-template.md), például a következőket:

   * Megjelenítendő név.

   * Mező neve.

   * Súlyosság.

    A **Ventilátormotor-hiba** esemény konfigurálásához használja a következő táblázatban lévő információkat:

    | Beállítás              | Érték             |
    | -------------------- | -----------       |
    | Megjelenítendő név         | Ventilátormotor-hiba   |
    | Mezőnév           | fanmotorerr       |
    | Severity             | Hiba             |

    Az esemény definíciójának mentéséhez válassza a **Mentés** lehetőséget:

    ![Esemény mérésének konfigurálása](./media/tutorial-define-device-type/eventconfiguration.png)

3. Egy rövid idő múlva a **Mérések** oldal megjeleníti a szimulált csatlakoztatott légkondicionáló eszközhöz véletlenszerűen létrehozott események diagramját. A vezérlőkkel kezelheti a láthatóságot, vagy szerkesztheti az esemény definícióját:

    ![Esemény szimulációjának megtekintése](./media/tutorial-define-device-type/eventview.png)

1. Az eseménnyel kapcsolatos további részletek megtekintéséhez kattintson az eseményre a diagramban:

    ![Eseményadatok megtekintése](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-state-measurement"></a>Állapot mérésének meghatározása

Az állapot használatával meghatározhatja és megjelenítheti az eszköz vagy összetevőinek állapotát egy meghatározott időtartamra vonatkozóan. A telemetriamérésekhez hasonlóan az Azure IoT Central képes eszközállapotot szimulálni az alkalmazás tesztelése érdekében a fizikai eszköz csatlakoztatása előtt. A **Mérések** nézetben határozhatja meg az eszköztípusa állapotméréseit.

1. A **Ventilátor mód** mérés hozzáadásához válassza az **Új mérés** elemet. Ezután válassza az **Állapotot** a mérés típusaként:

    ![Csatlakoztatott légkondicionáló állapotának mérései](./media/tutorial-define-device-type/statenew.png)

2. Az eszközsablonokhoz meghatározott minden állapottípus tartalmaz [konfigurációs beállításokat](howto-set-up-template.md), például a következőket:

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

    Az állapotmérés definíciójának mentéséhez válassza a **Mentés** lehetőséget:

    ![Állapot mérésének konfigurálása](./media/tutorial-define-device-type/stateconfiguration.png)

3. Egy rövid idő múlva a **Mérések** oldal megjeleníti a szimulált csatlakoztatott légkondicionáló eszközhöz véletlenszerűen létrehozott állapotok diagramját. A vezérlőkkel kezelheti a láthatóságot, vagy szerkesztheti az állapot definícióját:

    ![Állapot szimulációjának megtekintése](./media/tutorial-define-device-type/stateview.png)

4. Abban az esetben, ha az eszköz túl sok adatpontot küld egy rövid időtartam alatt, az állapot mérése más vizualizációval jelenik meg, az alább látható módon. Ha a diagramra kattint, akkor az abba az időszakba eső összes adatpont időrendben jelenik meg. Le is szűkítheti az időtartományt, hogy részletesebben lássa a méréseket.

    ![Állapot részleteinek megtekintése](./media/tutorial-define-device-type/stateviewdetail.png)

## <a name="settings-properties-and-commands"></a>Beállítások, tulajdonságok és parancsok

A beállítások, a tulajdonságok és a parancsok az eszközsablonban meghatározott különböző értékek, és az egyes eszközökhöz vannak társítva:

* _Beállítások_ használatával küldhet konfigurációs adatokat egy eszközre az alkalmazásból. Egy operátor például egy beállítás használatával módosíthatja az eszköz telemetriai időközét két másodpercről öt másodpercre. Amikor egy operátor módosít egy beállítást, a beállítás függőben lévőként lesz megjelölve a felhasználói felületen, amíg az eszköz meg nem erősíti, hogy végrehajtotta a beállítás módosítását.

* _Tulajdonságok_ használatával határozhatja meg az eszközzel társított metaadatokat. Két tulajdonságkategória érhető el:
    
    * Az _alkalmazástulajdonságok_ használatával rögzítheti az eszközzel kapcsolatos információkat az alkalmazásban. Az alkalmazástulajdonságokkal rögzítheti például egy eszköz helyét és utolsó szervizelési dátumát. Ezek a tulajdonságok az alkalmazásban vannak tárolva, és nincsenek szinkronizálva az eszközzel. Egy operátor értékeket rendelhet a tulajdonságokhoz.

    * _Eszköztulajdonságok_ használatával engedélyezheti, hogy egy eszköz tulajdonságértékeket küldjön az alkalmazásnak. Ezeket a tulajdonságokat csak az eszköz módosíthatja. Az operátorok számára az eszköztulajdonságok írásvédettek. A csatlakoztatott légkondicionáló forgatókönyvében a belső vezérlőprogram verziója és az eszköz sorozatszáma az eszköz által jelentett eszköztulajdonság. 
    
    További információt az eszközsablonok beállításáról szóló útmutató [Tulajdonságok][lnk-define-template] szakaszában talál.

* A _parancsok_ használatával távoli helyszínről kezelheti az eszközét az alkalmazáson keresztül. Közvetlenül futtathat parancsokat az eszközön a felhőből az eszközök vezérléséhez. Az operátor olyan parancsokat futtathat, mint például az újraindítás, amellyel azonnal újraindíthatja az eszközt.

## <a name="use-settings"></a>Beállítások használata

*Beállítások* használatával engedélyezheti, hogy egy operátor konfigurációs adatokat küldjön egy eszközre. Ebben a szakaszban egy olyan beállítást adhat a **Csatlakoztatott légkondicionáló** eszközsablonhoz, amely lehetővé teszi, hogy egy operátor beállítsa a csatlakoztatott légkondicionáló célhőmérsékletét.

1. Navigáljon a **Csatlakoztatott légkondicionáló** eszközsablon **Beállítások** lapjára:

    ![Felkészülés beállítás hozzáadására](./media/tutorial-define-device-type/deviceaddsetting.png)

    Különböző típusokhoz, például számokhoz vagy szövegekhez hozhat létre beállításokat.

2. Válassza a **Szám** lehetőséget, hogy szám beállítást adjon az eszközéhez.

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

    Ezután válassza a **Mentés** lehetőséget:

    ![A Megadott hőmérséklet beállítás konfigurálása](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Amikor az eszköz elfogadja a beállítás módosítását, a beállítás **szinkronizálva** állapotra változik.

4. A **Beállítások** lap elrendezésének testreszabásához mozgathatja és átméretezheti a beállítások csempéket:

    ![A beállítások elrendezésének testreszabása](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Tulajdonságok használata 

*Alkalmazástulajdonságok* használatával tárolhatja az eszközzel kapcsolatos információkat az alkalmazásban. Ebben a szakaszban alkalmazástulajdonságokat adhat hozzá a **Csatlakoztatott légkondicionáló** eszközsablonhoz az eszköz helyének és az utolsó szervizelés dátumának tárolásához. Vegye figyelembe, hogy mindkettő az eszköz szerkeszthető tulajdonsága. Az eszköz írásvédett eszköztulajdonságokat is jelent, amelyek nem módosíthatók (például az eszköz sorozatszámát és a belső vezérlőprogram verzióját).
 
1. Navigáljon a **Csatlakoztatott légkondicionáló** eszközsablon **Tulajdonságok** lapjára:

    ![Felkészülés tulajdonság hozzáadására](./media/tutorial-define-device-type/deviceaddproperty.png)

    Különböző típusú eszköztulajdonságokat hozhat létre, például számokat vagy szöveget. Ha hely tulajdonságot szeretne adni az eszközsablonhoz, válassza a **Hely** lehetőséget.

1. A hely tulajdonság konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Érték                |
    | -------------------- | -------------------- |
    | Megjelenítendő név         | Hely             |
    | Mezőnév           | location             |
    | Kezdeti érték        | Seattle, WA          |
    | Leírás          | Eszközhely      |

    A többi mező alapértelmezett értékeit ne módosítsa.

    ![Az eszköztulajdonságok konfigurálása](./media/tutorial-define-device-type/configureproperties.png)

    Válassza a **Mentés** elemet.

1. Ha az utolsó szervizelés dátuma tulajdonságot szeretne adni az eszközsablonhoz, válassza a **Dátum** lehetőséget.

1. Az utolsó szervizelés dátuma tulajdonság konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Érték                   |
    | -------------------- | ----------------------- |
    | Megjelenítendő név         | Utolsó szervizelés dátuma       |
    | Mezőnév           | serviceDate             |
    | Kezdeti érték        | 2018.01.01.                |
    | Leírás          | Utolsó szervizelés           |

    ![Az eszköztulajdonságok konfigurálása](./media/tutorial-define-device-type/configureproperties2.png)

    Válassza a **Mentés** elemet.

5. A **Tulajdonságok** lap elrendezésének testreszabásához áthelyezheti és átméretezheti a tulajdonságok csempéit:

    ![A tulajdonságok elrendezésének testreszabása](./media/tutorial-define-device-type/propertieslayout.png)

1. Ha egy eszköztulajdonságot, például a belső vezérlőprogram verzióját szeretné hozzáadni az eszközsablonhoz, válassza az **Eszköztulajdonság** lehetőséget.

1.  A belső vezérlőprogram verziójának konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Érték                   |
    | -------------------- | ----------------------- |
    | Megjelenítendő név         | Belső vezérlőprogram verziója        |
    | Mezőnév           | firmwareVersion         |
    | Adattípus            | szöveg                    |
    | Leírás          | A légkondicionáló belső vezérlőprogramjának verziója |

    ![Belső vezérlőprogram verziójának konfigurálása](./media/tutorial-define-device-type/configureproperties3.png)
    
    Válassza a **Mentés** elemet.

1. Ha egy eszköztulajdonságot, például sorozatszámot szeretne hozzáadni az eszközsablonhoz, válassza az **Eszköztulajdonság** lehetőséget.

1. A sorozatszám konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Érték                   |
    | -------------------- | ----------------------- |
    | Megjelenítendő név         | Sorozatszám           |
    | Mezőnév           | serialNumber            |
    | Adattípus            | szöveg                    |
    | Leírás          | A légkondicionáló sorozatszáma  |

    ![Sorozatszám konfigurálása](./media/tutorial-define-device-type/configureproperties4.png)
    
    Válassza a **Mentés** elemet.
    
    > [!NOTE]
    > Az eszköztulajdonságot az eszköz elküldi az alkalmazásnak. A belső vezérlőprogram verziójának és a sorozatszámnak az értékei frissülnek, amikor a valós eszköz csatlakozik az IoT Centralhoz.

## <a name="use-commands"></a>Parancsok használata

A _parancsok_ használatával engedélyezheti, hogy az operátor közvetlenül az eszközön futtasson parancsokat. Ebben a szakaszban egy olyan parancsot adhat a **Csatlakoztatott légkondicionáló** eszközsablonhoz, amely lehetővé teszi, hogy egy operátor a csatlakoztatott légkondicionálón adhasson vissza egy bizonyos üzenetet.

1. Navigáljon a **Csatlakoztatott légkondicionáló** eszközsablon **Parancsok** lapjára a sablon szerkesztéséhez. 

1. Kattintson az **Új parancs** elemre, hogy parancsot adjon hozzá az eszközéhez, és megkezdje ez új parancs konfigurálását.

   Igény szerint különféle típusú parancsokat hozhat létre. 

1. Az új parancs konfigurálásához használja a következő táblázatban lévő információkat:

    | Mező                | Érték           |
    | -------------------- | -----------     |
    | Megjelenítendő név         | Echo parancs    |
    | Mezőnév           | echo            |
    | Alapértelmezett időtúllépés      | 30              |
    | Megjelenítés típusa         | szöveg            |
    | Leírás          | Eszközparancs  |  

    A **Beviteli mezők** **+** elemére kattintva további parancsbemeneteket adhat hozzá a parancshoz.

    ![Felkészülés beállítás hozzáadására](media/tutorial-define-device-type/commandsecho1.png)

     Válassza a **Mentés** elemet.

1. A **Parancsok** lap elrendezésének testreszabásához áthelyezheti és átméretezheti a parancsok csempéit:

    ![A beállítások elrendezésének testreszabása](media/tutorial-define-device-type/commandstileresize1.png)

## <a name="view-your-simulated-device"></a>A szimulált eszköz megtekintése

Most, hogy meghatározta a **Csatlakoztatott légkondicionáló** eszközsablont, testreszabhatja az **Irányítópultját**, hogy tartalmazza a meghatározott méréseket, beállításokat és tulajdonságokat. Ezután operátorként megtekintheti az irányítópult előnézetét:

1. Válassza a **Csatlakoztatott légkondicionáló** eszközsablon **Irányítópult** lapját:

    ![Csatlakoztatott légkondicionáló irányítópultok](./media/tutorial-define-device-type/aircondashboards.png)

1. Válassza a **Vonaldiagram** lehetőséget, hogy az összetevőt az **Irányítópulthoz** adja:

    ![Irányítópult összetevői](./media/tutorial-define-device-type/dashboardcomponents1.png)

1. Konfigurálja a **Vonaldiagram** összetevőt a következő táblázatban lévő információk használatával:

    | Beállítás      | Érték       |
    | ------------ | ----------- |
    | Cím        | Hőmérséklet |
    | Időtartomány   | Az elmúlt 30 perc |
    | Mértékek     | hőmérséklet (válassza a **hőmérséklet** melletti **Láthatóság** lehetőséget) |

    ![Vonaldiagram beállításai](./media/tutorial-define-device-type/linechartsettings.png)

    Ezután válassza a **Mentés** lehetőséget.

1. Konfigurálja az **Eseményelőzmények** összetevőt a következő táblázatban lévő információk használatával:

    | Beállítás      | Érték       |
    | ------------ | ----------- |
    | Cím        | Események |
    | Időtartomány   | Az elmúlt 30 perc |
    | Mértékek     | Ventilátormotor-hiba (válassza a **Ventilátormotor-hiba** melletti **Láthatóság** lehetőséget) |

    ![Vonaldiagram beállításai](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Ezután válassza a **Mentés** lehetőséget.

1. Konfigurálja az **Állapotelőzmények** összetevőt a következő táblázatban lévő információk használatával:

    | Beállítás      | Érték       |
    | ------------ | ----------- |
    | Cím        | Ventilátor mód |
    | Időtartomány   | Az elmúlt 30 perc |
    | Mértékek | Ventilátor mód (válassza a **Ventilátor mód** melletti **Láthatóság** lehetőséget) |

    ![Vonaldiagram beállításai](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Ezután válassza a **Mentés** lehetőséget.

1. Ha a megadott hőmérséklet-beállítást az irányítópulthoz szeretné adni, válassza a **Beállítások és tulajdonságok** lehetőséget. Kattintson a **Hozzáadás/eltávolítás** gombra az irányítópulton látni kívánt beállítások vagy tulajdonságok hozzáadásához. 

    ![Irányítópult összetevői](./media/tutorial-define-device-type/dashboardcomponents4.png)

1. Konfigurálja a **Beállítások és tulajdonságok** összetevőt a következő táblázatban lévő információk használatával:

    | Beállítás                 | Érték         |
    | ----------------------- | ------------- |
    | Cím                   | Megadott célhőmérséklet |
    | Beállítások és tulajdonságok | Megadott hőmérséklet |

    A korábban a Beállítások és tulajdonságok oldalain meghatározott beállítások és tulajdonságok az Elérhető oszlopok területen jelennek meg. 

    ![A hőmérséklet-tulajdonság beállításainak megadása](./media/tutorial-define-device-type/propertysettings4.png)

    Ezután válassza az **OK** gombot.

1. Ha az eszköz sorozatszámát az irányítópulthoz szeretné adni, válassza a **Beállítások és tulajdonságok** lehetőséget:

    ![Irányítópult összetevői](./media/tutorial-define-device-type/dashboardcomponents3.png)

1. Konfigurálja a **Beállítások és tulajdonságok** összetevőt a következő táblázatban lévő információk használatával:

    | Beállítás                 | Érték         |
    | ----------------------- | ------------- |
    | Cím                   | Sorozatszám |
    | Beállítások és tulajdonságok | Sorozatszám |

    ![Sorozatszám tulajdonság beállításai](./media/tutorial-define-device-type/propertysettings5.png)

    Ezután válassza az **OK** gombot.

1. Ha az eszköz belső vezérlőprogramjának verzióját az irányítópulthoz szeretné adni, válassza a **Beállítások és tulajdonságok** lehetőséget:

    ![Irányítópult összetevői](./media/tutorial-define-device-type/dashboardcomponents4.png)

1. Konfigurálja a **Beállítások és tulajdonságok** összetevőt a következő táblázatban lévő információk használatával:

    | Beállítás                 | Érték            |
    | ----------------------- | ---------------- |
    | Cím                   | Belső vezérlőprogram verziója |
    | Beállítások és tulajdonságok | Belső vezérlőprogram verziója |

    ![Sorozatszám tulajdonság beállításai](./media/tutorial-define-device-type/propertysettings6.png)

    Ezután válassza az **OK** gombot.

1. Ha operátorként szeretné megtekinteni az irányítópultot, kapcsolja ki a **Sablon szerkesztése** elemet az oldal jobb felső részén.

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

Most, hogy meghatározott egy eszközsablont az Azure IoT Central-alkalmazásban, itt talál néhány javasolt következő lépést:

* [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules.md)
* [Operátori nézetek testreszabása](tutorial-customize-operator.md)

[lnk-define-template]: /azure/iot-central/howto-set-up-template#properties