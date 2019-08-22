---
title: Az operátori nézetek testreszabása az Azure IoT Centralban | Microsoft Docs
description: Szerkesztőként testreszabhatja az operátori nézeteket az Azure IoT Central-alkalmazásban.
author: sandeeppujar
ms.author: sandeepu
ms.date: 07/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: aa5ff93b4f1f6392f753a88d57a072f542fc16e2
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877272"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Oktatóanyag: Az Azure IoT Central-kezelő nézetének testreszabása

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan szabhatja testre az alkalmazás operátori nézeteit. Amikor szerkesztőként módosítja az alkalmazást, megtekintheti az operátori nézetek előnézetét a Microsoft Azure IoT Central-alkalmazásban.

Ebben az oktatóanyagban úgy szabja testre az alkalmazást, hogy a csatlakoztatott légkondicionáló eszközre vonatkozó információkat megjelenítse egy operátornak. A testreszabások lehetővé teszik, hogy az operátor kezelhesse az alkalmazáshoz csatlakoztatott légkondicionáló eszközöket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eszköz irányítópultjának konfigurálása
> * Az eszközbeállítások elrendezésének konfigurálása
> * Az eszköztulajdonságok elrendezésének konfigurálása
> * Az eszköz előnézetének megtekintése operátorként
> * Az alapértelmezett alkalmazás-irányítópult konfigurálása
> * Az alapértelmezett alkalmazás-irányítópult előnézete operátorként

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt el kell végeznie a két előző oktatóanyagot:

* [Új eszköztípus definiálása az Azure IoT Central-alkalmazásban](tutorial-define-device-type.md).
* [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Az eszköz irányítópultjának konfigurálása

Szerkesztőként meghatározhatja, milyen információk jelenjenek meg egy eszköz irányítópultján. Az [új eszköz definiálása](tutorial-define-device-type.md) az alkalmazáshoz oktatóanyagban egy vonalas diagramot és egyéb információkat adott hozzá a **csatlakoztatott légkondicionáló** irányítópulthoz.

1. A **csatlakoztatott légkondicionáló** eszköz sablonjának szerkesztéséhez válassza a bal oldali navigációs menü **eszközök sablonok** elemét:

    ![Eszközök sablonjai lap](media/tutorial-customize-operator/devicetemplates.png)

2. Az eszköz irányítópultjának testreszabásához válassza ki az [új eszköz definiálása](tutorial-define-device-type.md) az alkalmazásban című oktatóanyagban létrehozott **csatlakoztatott légkondicionáló (1.0.0)** eszköz sablonjának eszközét.

3. Az irányítópult szerkesztéséhez válassza az **irányítópult** fület.

4. Ha egy fő teljesítménymutatói (KPI-) csempét hozzá szeretne adni az irányítópulthoz, válassza a **KPI** lehetőséget:

    A KPI meghatározásához használja a következő táblázatban szereplő információkat:

    | Beállítás     | Érték |
    | ----------- | ----- |
    | Name (Név)        | Maximális hőmérséklet |
    | Időtartomány  | Elmúlt 1 hét |
    | Mérés típusa | Telemetria |
    | Mérés | hőmérséklet |
    | Összesítés | Maximum |
    | Láthatóság  | Enabled |

    ![KPI hozzáadása](media/tutorial-customize-operator/addkpi.png)

5. Kattintson a **Mentés** gombra. Most láthatja a KPI-csempét az irányítópulton:

    ![KPI-csempe](media/tutorial-customize-operator/temperaturekpi.png)

6. Az irányítópulton lévő csempék áthelyezéséhez vagy átméretezéséhez helyezze az egérmutatót a csempe fölé. A csempét áthelyezheti egy új helyre, vagy átméretezheti.

## <a name="configure-your-settings-layout"></a>A beállítások elrendezésének konfigurálása

Szerkesztőként az eszközbeállítások operátori nézetét is konfigurálhatja. Az operátor az eszközbeállítások lapot használja az eszköz konfigurálásához. Egy operátor például a beállítások lapon állíthatja be a csatlakoztatott légkondicionáló hőmérsékletét.

1. A csatlakoztatott légkondicionáló beállítások elrendezésének szerkesztéséhez válassza a **Beállítások** lapot.

2. Áthelyezheti és átméretezheti a beállítások csempéket:

    ![A beállítások elrendezésének szerkesztése](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>A tulajdonságok elrendezésének konfigurálása

Az irányítópult és a beállítások mellett az eszköztulajdonságok operátori nézetét is konfigurálhatja. Az operátor az eszköz tulajdonságai lapot használja az eszköz metaadatainak kezeléséhez. Egy operátor például a Tulajdonságok lapon megtekintheti az eszköz sorozatszámát, vagy frissítheti a gyártó kapcsolattartási adatait.

1. A csatlakoztatott légkondicionáló tulajdonságok elrendezésének szerkesztéséhez kattintson a **Tulajdonságok** fülre.

2. Áthelyezheti és átméretezheti a tulajdonságok mezőit:

    ![A tulajdonságok elrendezésének szerkesztése](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>Az eszköz előzetes verziója

Az **eszközbeállítások** lapon testreszabhatja az operátor irányítópultot, a beállításokat és a tulajdonságok lapfüleit. A **Device Explorer** lapon megtekintheti és használhatja az eszköz sablonját.

1. Ha operátorként szeretné megtekinteni és használni a csatlakoztatott légkondicionáló sablont, navigáljon a **Device Explorer** lapra, és válassza ki azt a szimulált eszközt, amelyet a sablonból IoT Central generált:

    ![Az eszköz sablonjának megtekintése és használata](media/tutorial-customize-operator/usetemplate.png)

2. Az eszköz helyének frissítéséhez válassza a **Tulajdonságok** lehetőséget, és szerkessze az értéket a hely csempén. Ezután válassza a **Mentés**lehetőséget:

    ![Tulajdonságérték szerkesztése](media/tutorial-customize-operator/editproperty.png)

3. Ha egy beállítást szeretne küldeni a csatlakoztatott légkondicionálóhoz, válassza a **Beállítások** lehetőséget, módosítsa egy csempe beállításértékét, és válassza a **Frissítés** lehetőséget:

    ![Beállítás küldése az eszközre](media/tutorial-customize-operator/sendsetting.png)

    Amikor az eszköz elfogadja az új beállításértéket, a beállítás **szinkronizálva** értékkel jelenik meg a csempén.

4. Operátorként a szerkesztő által konfigurált módon tekintheti meg az eszköz irányítópultját:

    ![Az eszköz irányítópultjának operátori nézete](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>Az alapértelmezett irányítópult konfigurálása

Amikor egy építő vagy operátor bejelentkezik egy Azure IoT Central alkalmazásba, az alkalmazás irányítópultja jelenik meg. Építőként konfigurálhatja az alapértelmezett irányítópult tartalmát, hogy tartalmazza a leghasznosabb és a releváns tartalmat az operátor számára.

> [!NOTE]
> A felhasználók saját irányítópultokat is létrehozhatnak, és alapértelmezettként választhatnak.

1. Az alapértelmezett alkalmazás-irányítópult testreszabásához navigáljon az **irányítópult** lapra, és válassza a **Szerkesztés** lehetőséget az oldal jobb felső részén. Megjelenik egy panel, amely az irányítópultra felvehető objektumok könyvtárával jelenik meg.

    ![Irányítópult lapja](media/tutorial-customize-operator/builderhome.png)

2. Az irányítópult testreszabásához adja hozzá a csempéketa könyvtárból. Válassza a **Hivatkozás** lehetőséget, és adja hozzá a cég webhelyének részleteit. Ezután válassza a **Mentés** lehetőséget:

    ![Hivatkozás hozzáadása az irányítópulthoz](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Az Azure IoT Central-alkalmazásban lévő oldalakhoz hivatkozásokat is adhat. Hozzáadhatja például egy eszköz irányítópultjának vagy a beállítások oldalának a hivatkozását.

3. Ha szeretné, válassza a **rendszerkép** lehetőséget, és töltse fel az irányítópulton megjelenítendő képet. A rendszerképnek URL-címe lehet, amelyre a kiválasztáskor navigál:

    ![Rendszerkép hozzáadása az irányítópulthoz](media/tutorial-customize-operator/addimage.png)

    További tudnivalókért lásd a [képek előkészítését és az Azure IoT Central-alkalmazásba való feltöltését](howto-prepare-images.md) ismertető szakaszt.

## <a name="preview-the-dashboard"></a>Az irányítópult előnézete

Ha az alkalmazás irányítópultját operátorként szeretné megtekinteni, válassza a **kész** lehetőséget az oldal jobb felső sarkában.

![A Tervezési mód be- és kikapcsolása](media/tutorial-customize-operator/operatorviewhome.png)

A hivatkozás és a képcsempék kiválasztásával megnyithatja a Builder-ként beállított URL-címeket.

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

* [Az eszközök monitorozása (operátorként)](tutorial-monitor-devices.md)
* [Új eszköz hozzáadása az alkalmazáshoz (operátorként és eszközfejlesztőként)](tutorial-add-device.md)