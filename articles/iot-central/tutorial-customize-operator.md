---
title: Az operátori nézetek testreszabása az Azure IoT Centralban | Microsoft Docs
description: Szerkesztőként testreszabhatja az operátori nézeteket az Azure IoT Central-alkalmazásban.
author: sandeeppujar
ms.author: sandeepu
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 0f03cdb7f407e0f568b543cc09527bb6a6e4c25a
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317220"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Oktatóanyag: Az Azure IoT Central operátor nézet testreszabása

Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan szabhatja testre az alkalmazás operátori nézeteit. Amikor szerkesztőként módosítja az alkalmazást, megtekintheti az operátori nézetek előnézetét a Microsoft Azure IoT Central-alkalmazásban.

Ebben az oktatóanyagban úgy szabja testre az alkalmazást, hogy a csatlakoztatott légkondicionáló eszközre vonatkozó információkat megjelenítse egy operátornak. A testreszabások lehetővé teszik, hogy az operátor kezelhesse az alkalmazáshoz csatlakoztatott légkondicionáló eszközöket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eszköz irányítópultjának konfigurálása
> * Az eszközbeállítások elrendezésének konfigurálása
> * Az eszköztulajdonságok elrendezésének konfigurálása
> * Az eszköz előnézetének megtekintése operátorként
> * Az alapértelmezett alkalmazás irányítópult konfigurálása
> * Az alapértelmezett irányítópult kezelőként előzetes verzió

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt el kell végeznie a két előző oktatóanyagot:

* [Új eszköztípus definiálása az Azure IoT Central-alkalmazásban](tutorial-define-device-type.md).
* [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Az eszköz irányítópultjának konfigurálása

Szerkesztőként meghatározhatja, milyen információk jelenjenek meg egy eszköz irányítópultján. Az [új eszköztípus az alkalmazásban történő definiálását](tutorial-define-device-type.md) ismertető oktatóanyagban vonaldiagramot és egyéb információkat adott a **Csatlakoztatott légkondicionáló-1** irányítópulthoz.

1. A **Csatlakoztatott légkondicionáló** eszköz sablonjának szerkesztéséhez válassza a bal oldali navigációs menüben a **Kereső** elemet:

    ![Kereső oldal](media/tutorial-customize-operator/explorer.png)

2. A csatlakoztatott légkondicionáló eszköz irányítópultjának testreszabásához válassza ki a **Csatlakoztatott légkondicionáló (1.0.0)** eszközsablont. Válassza ki az [új eszköztípus az alkalmazásban történő definiálását](tutorial-define-device-type.md) ismertető oktatóanyagban létrehozott **Csatlakoztatott légkondicionáló-1** eszközt:

    ![A csatlakoztatott légkondicionáló eszköz kiválasztása](media/tutorial-customize-operator/selectdevice.png)

    Egy eszközön, például a **Csatlakoztatott légkondicionáló-1** eszközön belül a **Sablon szerkesztése** lehetőséggel az alapul szolgáló sablont is módosíthatja. További információkért lásd az [új eszközsablon-verzió létrehozását](howto-version-devicetemplate.md) ismertető szakaszt.

3. Az irányítópult szerkesztéséhez válassza az **Irányítópult**, majd a **Sablon szerkesztése** lehetőséget:

    ![Eszközsablon irányítópultja oldal](media/tutorial-customize-operator/dashboard.png)

4. Ha egy fő teljesítménymutatói (KPI-) csempét hozzá szeretne adni az irányítópulthoz, válassza a **KPI** lehetőséget:

    ![KPI hozzáadása](media/tutorial-customize-operator/addkpi.png)

    A KPI meghatározásához használja a következő táblázatban szereplő információkat:

    | Beállítás     | Érték |
    | ----------- | ----- |
    | Name (Név)        | Maximális hőmérséklet |
    | Mérés | hőmérséklet |
    | Összesítés | Maximum |
    | Időtartomány  | Előző 1 hét |

5. Válassza a **Mentés** elemet. Most láthatja a KPI-csempét az irányítópulton:

    ![KPI-csempe](media/tutorial-customize-operator/temperaturekpi.png)

6. Az irányítópulton lévő csempék áthelyezéséhez vagy átméretezéséhez helyezze az egérmutatót a csempe fölé. Új helyre húzhatja vagy átméretezheti a csempét:

    ![Irányítópult elrendezésének szerkesztése](media/tutorial-customize-operator/dashboardlayout.png)

7. Válassza ki **kész** Amikor végzett változtatásokat.

## <a name="configure-your-settings-layout"></a>A beállítások elrendezésének konfigurálása

Szerkesztőként az eszközbeállítások operátori nézetét is konfigurálhatja. Az operátorok az eszközbeállítások oldalán konfigurálják az eszközöket. Egy operátor például a beállítások oldalán állítja be a csatlakoztatott légkondíció számára a célhőmérsékletet.

1. A csatlakoztatott légkondicionáló-beállítások elrendezésének szerkesztéséhez válassza a **Beállítások**, majd a **Sablon szerkesztése** lehetőséget:

    ![Beállítások lap](media/tutorial-customize-operator/settings.png)

2. Áthelyezheti és átméretezheti a beállítások csempéket:

    ![A beállítások elrendezésének szerkesztése](media/tutorial-customize-operator/settingslayout.png)

3. Válassza ki **kész** Amikor végzett változtatásokat.

> [!NOTE]
> **Sablonszerkesztési** módban nem szerkesztheti a beállítások értékeit.

## <a name="configure-your-properties-layout"></a>A tulajdonságok elrendezésének konfigurálása

Az irányítópult és a beállítások mellett az eszköztulajdonságok operátori nézetét is konfigurálhatja. Az operátorok az eszköztulajdonságok oldalán kezelik az eszközök metaadatait. Egy operátor például a tulajdonságok oldalán tekintheti meg az eszköz sorozatszámát vagy frissítheti a gyártó kapcsolattartási adatait.

1. A csatlakoztatott légkondicionáló-tulajdonságok elrendezésének szerkesztéséhez válassza a **Tulajdonságok**, majd a **Sablon szerkesztése** lehetőséget:

    ![Tulajdonságok lap](media/tutorial-customize-operator/properties.png)

2. Áthelyezheti és átméretezheti a tulajdonságok mezőit:

    ![A tulajdonságok elrendezésének szerkesztése](media/tutorial-customize-operator/propertieslayout.png)

3. Válassza ki **kész** Amikor végzett változtatásokat.

> [!NOTE]
> **Sablonszerkesztési** módban nem szerkesztheti a tulajdonságok értékeit.

## <a name="preview-the-device"></a>Az eszköz előzetes verzió

**Sablonszerkesztési** módban testreszabhatja az operátor irányítópult, a beállítások és a tulajdonságok oldalát. Ha nem **Sablonszerkesztési** módban van, operátorként tekintheti meg az alkalmazást.

1. A csatlakoztatott légkondicionálóját eszköz kezelőként megtekintéséhez jelölje ki **kész** leállítani, szerkesztő a sablont, és térjen vissza az eszközt az operátor nézetét.

2. Az eszköz helyének frissítéséhez szerkessze a helycsempén lévő értéket, és válassza a **Mentés** lehetőséget:

    ![Tulajdonságérték szerkesztése](media/tutorial-customize-operator/editproperty.png)

3. Ha egy beállítást szeretne küldeni a csatlakoztatott légkondicionálóhoz, válassza a **Beállítások** lehetőséget, módosítsa egy csempe beállításértékét, és válassza a **Frissítés** lehetőséget:

    ![Beállítás küldése az eszközre](media/tutorial-customize-operator/sendsetting.png)

    Amikor az eszköz elfogadja az új beállításértéket, a beállítás **szinkronizálva** értékkel jelenik meg a csempén.

4. Operátorként a szerkesztő által konfigurált módon tekintheti meg az eszköz irányítópultját:

    ![Az eszköz irányítópultjának operátori nézete](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>Az alapértelmezett irányítópult konfigurálása

Ha a jelentéskészítő vagy operátor jelentkezik be egy Azure IoT Central alkalmazáshoz, az irányítópult megjelenik. Mint szerkesztő konfigurálhatja a tartalom az alapértelmezett irányítópult, ha hozzá szeretné adni az operátornak a hasznos és a kapcsolódó tartalmat.

> [!NOTE]
> A felhasználók a saját személyes irányítópultok kialakítása és válasszon egyet az alapértelmezésként is.

1. Testre szabhatja az alapértelmezett irányítópult, keresse meg a **irányítópult** lapon, és válassza **szerkesztése** felső jobb. Megjelenik egy panel, egy erőforrástár-objektumok is hozzáadhat az irányítópulthoz.

    ![Irányítópult-oldalon](media/tutorial-customize-operator/builderhome.png)

2. Testre szabhatja az irányítópultot, a csempék hozzáadása a **könyvtár**. Válassza a **Hivatkozás** lehetőséget, és adja hozzá a cég webhelyének részleteit. Ezután válassza a **Mentés** lehetőséget:

    ![Hivatkozás hozzáadása az irányítópulthoz](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Az Azure IoT Central-alkalmazásban lévő oldalakhoz hivatkozásokat is adhat. Hozzáadhatja például egy eszköz irányítópultjának vagy a beállítások oldalának a hivatkozását.

3. Szükség esetén válasszon **kép** és töltsön fel egy képet az irányítópulton megjeleníthető. Kép URL-címe, amelyhez lépjen, ha kijelöli veheti fel:

    ![Kép hozzáadása az irányítópulthoz](media/tutorial-customize-operator/addimage.png)

    További tudnivalókért lásd a [képek előkészítését és az Azure IoT Central-alkalmazásba való feltöltését](howto-prepare-images.md) ismertető szakaszt.

## <a name="preview-the-dashboard"></a>Az irányítópult megtekintése

Kezelőként az irányítópult megtekintéséhez válassza ki a **kész** felső jobb

![A Tervezési mód be- és kikapcsolása](media/tutorial-customize-operator/operatorviewhome.png)

Keresse meg az URL-címeket lehet beállítani egy jelentéskészítő-hivatkozásokon és csempéket is választhat.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte, hogyan szabhatja testre az alkalmazás operátori nézetét.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Az eszköz irányítópultjának konfigurálása
> * Az eszközbeállítások elrendezésének konfigurálása
> * Az eszköztulajdonságok elrendezésének konfigurálása
> * Az eszköz előnézetének megtekintése operátorként
> * Az alapértelmezett alkalmazás irányítópult konfigurálása
> * Az alapértelmezett irányítópult kezelőként előzetes verzió

Most, hogy megismerte, hogyan szabhatja testre az alkalmazás operátori nézetét, az alábbiakban megtalálja a javasolt következő lépéseket:

* [Az eszközök monitorozása (operátorként)](tutorial-monitor-devices.md)
* [Új eszköz hozzáadása az alkalmazáshoz (operátorként és eszközfejlesztőként)](tutorial-add-device.md)
