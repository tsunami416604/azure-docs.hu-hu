---
title: Az operátori nézetek testreszabása az Azure IoT Centralban | Microsoft Docs
description: Szerkesztőként testreszabhatja az operátori nézeteket az Azure IoT Central-alkalmazásban.
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/29/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: ba91bafbdb53269ef622feeceda7dc48359716de
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734691"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view-new-ui-design"></a>Oktatóanyag: Az Azure IoT Central operátor nézet (új felhasználói felület tervezése) testreszabása

Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan szabhatja testre az alkalmazás operátori nézeteit. Amikor szerkesztőként módosítja az alkalmazást, megtekintheti az operátori nézetek előnézetét a Microsoft Azure IoT Central-alkalmazásban.

Ebben az oktatóanyagban úgy szabja testre az alkalmazást, hogy a csatlakoztatott légkondicionáló eszközre vonatkozó információkat megjelenítse egy operátornak. A testreszabások lehetővé teszik, hogy az operátor kezelhesse az alkalmazáshoz csatlakoztatott légkondicionáló eszközöket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eszköz irányítópultjának konfigurálása
> * Az eszközbeállítások elrendezésének konfigurálása
> * Az eszköztulajdonságok elrendezésének konfigurálása
> * Az eszköz előnézetének megtekintése operátorként
> * Az alapértelmezett kezdőlap konfigurálása
> * Az alapértelmezett kezdőlap előnézetének megtekintése operátorként

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt el kell végeznie a két előző oktatóanyagot:

* [Új eszköztípus definiálása az Azure IoT Central-alkalmazásban](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
* [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="configure-your-device-dashboard"></a>Az eszköz irányítópultjának konfigurálása

Szerkesztőként meghatározhatja, milyen információk jelenjenek meg egy eszköz irányítópultján. Az a [az alkalmazásban egy új eszköztípus definiálása](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) oktatóanyagban hozzáadott egy vonaldiagramot és egyéb adatokat a **Légkondicionálóját csatlakoztatott** irányítópultot.

1. Szerkesztése a **Légkondicionálóját csatlakoztatott** eszköz sablont, válassza a **eszközsablonok** a bal oldali navigációs menüben:

    ![Eszköz sablonok lap](media/tutorial-customize-operator-experimental/devicetemplates.png)

2. Testre szabhatja az irányítópultját, kattintson a **Légkondicionálóját csatlakoztatva (1.0.0-s)** létrehozott sablon eszközt a [az alkalmazásban egy új eszköztípus definiálása](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) oktatóanyag.

3. Az irányítópult szerkesztéséhez válassza ki a **irányítópult** fülre.

4. Ha egy fő teljesítménymutatói (KPI-) csempét hozzá szeretne adni az irányítópulthoz, válassza a **KPI** lehetőséget:

    A KPI meghatározásához használja a következő táblázatban szereplő információkat:

    | Beállítás     | Érték |
    | ----------- | ----- |
    | Name (Név)        | Maximális hőmérséklet |
    | Időtartomány  | Előző 1 hét |
    | Mérés típusa | Telemetria |
    | Mérés | hőmérséklet |
    | Összesítés | Maximum |
    | Láthatóság  | Engedélyezve |

    ![KPI hozzáadása](media/tutorial-customize-operator-experimental/addkpi.png)

5. Kattintson a **Save** (Mentés) gombra. Most láthatja a KPI-csempét az irányítópulton:

    ![KPI-csempe](media/tutorial-customize-operator-experimental/temperaturekpi.png)

6. Az irányítópulton lévő csempék áthelyezéséhez vagy átméretezéséhez helyezze az egérmutatót a csempe fölé. Húzza át a csempét egy új helyre, és méretezze át.

## <a name="configure-your-settings-layout"></a>A beállítások elrendezésének konfigurálása

Szerkesztőként az eszközbeállítások operátori nézetét is konfigurálhatja. Az operátornak eszköz beállítása az eszköz beállításai lapon használ. Például az operátornak használja a beállítások lapon állítsa be a cél hőmérséklet számára a csatlakoztatott légkondicionálók.

1. A beállítások elrendezést a csatlakoztatott légkondicionálóját szerkesztéséhez válassza a **beállítások** fülre.

2. Áthelyezheti és átméretezheti a beállítások csempéket:

    ![A beállítások elrendezésének szerkesztése](media/tutorial-customize-operator-experimental/settingslayout.png)

## <a name="configure-your-properties-layout"></a>A tulajdonságok elrendezésének konfigurálása

Az irányítópult és a beállítások mellett az eszköztulajdonságok operátori nézetét is konfigurálhatja. Az operátor az eszköz tulajdonságai lap használatával kezeli az eszköz metaadatait. Például az operátornak egy eszköz sorozatszámát megtekintéséhez vagy frissítéséhez kapcsolattartási adatait a gyártó használja a Tulajdonságok lapon.

1. A Tulajdonságok elrendezést a csatlakoztatott légkondicionálóját szerkesztéséhez válassza a **tulajdonságok** fülre.

2. Áthelyezheti és átméretezheti a tulajdonságok mezőit:

    ![A tulajdonságok elrendezésének szerkesztése](media/tutorial-customize-operator-experimental/propertieslayout.png)

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>A csatlakoztatott légkondicionáló eszköz előnézetének megtekintése operátorként

Használja a **eszközsablonok** lapon szabhatja testre az irányítópult, a beállítások és a Tulajdonságok lapon olyan operátorra. Használja a **Device Explorer** megtekintése és használata az eszköz sablon lap.

1. Megtekintheti, és a csatlakoztatott légkondicionálóját sablont használja, az operátornak, lépjen a **Device Explorer** lapon, és válassza ki a szimulált eszközt, hogy a sablon alapján létrehozott IoT-központ:

    ![Megtekintése és használata az eszköz sablon](media/tutorial-customize-operator-experimental/usetemplate.png)

2. Frissítse az eszköz helyét, válassza a **tulajdonságok** és szerkesztheti a hely csempe értéket. Kattintson a **mentése**:

    ![Tulajdonságérték szerkesztése](media/tutorial-customize-operator-experimental/editproperty.png)

3. Ha egy beállítást szeretne küldeni a csatlakoztatott légkondicionálóhoz, válassza a **Beállítások** lehetőséget, módosítsa egy csempe beállításértékét, és válassza a **Frissítés** lehetőséget:

    ![Beállítás küldése az eszközre](media/tutorial-customize-operator-experimental/sendsetting.png)

    Amikor az eszköz elfogadja az új beállításértéket, a beállítás **szinkronizálva** értékkel jelenik meg a csempén.

4. Operátorként a szerkesztő által konfigurált módon tekintheti meg az eszköz irányítópultját:

    ![Az eszköz irányítópultjának operátori nézete](media/tutorial-customize-operator-experimental/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>Az alapértelmezett kezdőlap konfigurálása

Amikor egy szerkesztő vagy operátor bejelentkezik az Azure IoT Central-alkalmazásba, egy kezdőlapot lát. Szerkesztőként konfigurálhatja e kezdőlap tartalmát, hogy az operátor számára leghasznosabb és legfontosabb tartalmakat foglalja bele.

1. Testre szabhatja az alapértelmezett kezdőlapját, keresse meg a **otthoni** lapon és a Szerkesztés **szerkesztése** tetején, az oldal jobb. A panel becsúszik adhat hozzá objektumok listáját a balról a **kezdőlap** oldalon:

    ![Alkalmazásszerkesztő oldal](media/tutorial-customize-operator-experimental/builderhome.png)

2. Testreszabhatja a **kezdőlap** lapon, a csempék hozzáadása a **könyvtár**. Válassza a **Hivatkozás** lehetőséget, és adja hozzá a cég webhelyének részleteit. Kattintson a **mentése**:

    ![Hivatkozás hozzáadása a kezdőlaphoz](media/tutorial-customize-operator-experimental/addlink.png)

    > [!NOTE]
    > Az Azure IoT Central-alkalmazásban lévő oldalakhoz hivatkozásokat is adhat. Hozzáadhatja például egy eszköz irányítópultjának vagy a beállítások oldalának a hivatkozását.

3. A **Kép** lehetőséget is választhatja, és feltölthet egy megjelenítendő képet a kezdőlapra. A képek olyan URL-címmel rendelkezhetnek, amelyeket rájuk kattintva megnyithat:

    ![Kép hozzáadása a kezdőlaphoz](media/tutorial-customize-operator-experimental/addimage.png)

    További tudnivalókért lásd a [képek előkészítését és az Azure IoT Central-alkalmazásba való feltöltését](howto-prepare-images.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) ismertető szakaszt.

## <a name="preview-the-default-home-page-as-an-operator"></a>Az alapértelmezett kezdőlap előnézetének megtekintése operátorként

Kattintva megtekintheti a kezdőlap kezelőként **kész** tetején, az oldal jobb:

![A Tervezési mód be- és kikapcsolása](media/tutorial-customize-operator-experimental/operatorviewhome.png)

A hivatkozások és a képek csempéire kattintva a szerkesztőként beállított URL-címekre léphet.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte, hogyan szabhatja testre az alkalmazás operátori nézetét.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Az eszköz irányítópultjának konfigurálása
> * Az eszközbeállítások elrendezésének konfigurálása
> * Az eszköztulajdonságok elrendezésének konfigurálása
> * Az eszköz előnézetének megtekintése operátorként
> * Az alapértelmezett kezdőlap konfigurálása
> * Az alapértelmezett kezdőlap előnézetének megtekintése operátorként

Most, hogy megismerte, hogyan szabhatja testre az alkalmazás operátori nézetét, az alábbiakban megtalálja a javasolt következő lépéseket:

* [Az eszközök monitorozása (operátorként)](tutorial-monitor-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Új eszköz hozzáadása az alkalmazáshoz (operátorként és eszközfejlesztőként)](tutorial-add-device-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
