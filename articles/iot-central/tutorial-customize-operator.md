---
title: Az operátori nézetek testreszabása az Azure IoT Centralban | Microsoft Docs
description: Szerkesztőként testreszabhatja az operátori nézeteket az Azure IoT Central-alkalmazásban.
author: sandeeppujar
ms.author: sandeepu
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 9b8293196114f55f633e67cf19cd0c9e60f6398e
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309460"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Oktatóanyag: Az Azure IoT Central operátori nézeteinek testreszabása

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

7. Ha végzett a módosításokkal, kattintson a **Kész** lehetőségre.

## <a name="configure-your-settings-layout"></a>A beállítások elrendezésének konfigurálása

Szerkesztőként az eszközbeállítások operátori nézetét is konfigurálhatja. Az operátorok az eszközbeállítások oldalán konfigurálják az eszközöket. Egy operátor például a beállítások oldalán állítja be a csatlakoztatott légkondíció számára a célhőmérsékletet.

1. A csatlakoztatott légkondicionáló-beállítások elrendezésének szerkesztéséhez válassza a **Beállítások**, majd a **Sablon szerkesztése** lehetőséget:

    ![Beállítások lap](media/tutorial-customize-operator/settings.png)

2. Áthelyezheti és átméretezheti a beállítások csempéket:

    ![A beállítások elrendezésének szerkesztése](media/tutorial-customize-operator/settingslayout.png)

3. Ha végzett a módosításokkal, kattintson a **Kész** lehetőségre.

> [!NOTE]
> **Sablonszerkesztési** módban nem szerkesztheti a beállítások értékeit.

## <a name="configure-your-properties-layout"></a>A tulajdonságok elrendezésének konfigurálása

Az irányítópult és a beállítások mellett az eszköztulajdonságok operátori nézetét is konfigurálhatja. Az operátorok az eszköztulajdonságok oldalán kezelik az eszközök metaadatait. Egy operátor például a tulajdonságok oldalán tekintheti meg az eszköz sorozatszámát vagy frissítheti a gyártó kapcsolattartási adatait.

1. A csatlakoztatott légkondicionáló-tulajdonságok elrendezésének szerkesztéséhez válassza a **Tulajdonságok**, majd a **Sablon szerkesztése** lehetőséget:

    ![Tulajdonságok lap](media/tutorial-customize-operator/properties.png)

2. Áthelyezheti és átméretezheti a tulajdonságok mezőit:

    ![A tulajdonságok elrendezésének szerkesztése](media/tutorial-customize-operator/propertieslayout.png)

3. Ha végzett a módosításokkal, kattintson a **Kész** lehetőségre.

> [!NOTE]
> **Sablonszerkesztési** módban nem szerkesztheti a tulajdonságok értékeit.

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>A csatlakoztatott légkondicionáló eszköz előnézetének megtekintése operátorként

**Sablonszerkesztési** módban testreszabhatja az operátor irányítópult, a beállítások és a tulajdonságok oldalát. Ha nem **Sablonszerkesztési** módban van, operátorként tekintheti meg az alkalmazást.

1. Ha operátorként szeretné megtekinteni a csatlakoztatott légkondicionáló eszközt, kattintson a **Kész** gombra a sablon szerkesztésének leállításához, és térjen vissza az eszköz operátori nézetéhez.

2. Az eszköz helyének frissítéséhez szerkessze a helycsempén lévő értéket, és válassza a **Mentés** lehetőséget:

    ![Tulajdonságérték szerkesztése](media/tutorial-customize-operator/editproperty.png)

3. Ha egy beállítást szeretne küldeni a csatlakoztatott légkondicionálóhoz, válassza a **Beállítások** lehetőséget, módosítsa egy csempe beállításértékét, és válassza a **Frissítés** lehetőséget:

    ![Beállítás küldése az eszközre](media/tutorial-customize-operator/sendsetting.png)

    Amikor az eszköz elfogadja az új beállításértéket, a beállítás **szinkronizálva** értékkel jelenik meg a csempén.

4. Operátorként a szerkesztő által konfigurált módon tekintheti meg az eszköz irányítópultját:

    ![Az eszköz irányítópultjának operátori nézete](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>Az alapértelmezett kezdőlap konfigurálása

Amikor egy szerkesztő vagy operátor bejelentkezik az Azure IoT Central-alkalmazásba, egy kezdőlapot lát. Szerkesztőként konfigurálhatja e kezdőlap tartalmát, hogy az operátor számára leghasznosabb és legfontosabb tartalmakat foglalja bele.

1. Az alapértelmezett kezdőlap testreszabásához lépjen a **Kezdőlapra**, és válassza a **Szerkesztés** lehetőséget az oldal jobb felső részén. A **Szerkesztés** választásakor a jobb oldalon megnyílik egy panel a kezdőlapra adható objektumok listájával.

    ![Alkalmazásszerkesztő oldal](media/tutorial-customize-operator/builderhome.png)

2. A kezdőlap testreszabásához adjon hozzá csempéket a **Könyvtárból**. Válassza a **Hivatkozás** lehetőséget, és adja hozzá a cég webhelyének részleteit. Ezután válassza a **Mentés** lehetőséget:

    ![Hivatkozás hozzáadása a kezdőlaphoz](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Az Azure IoT Central-alkalmazásban lévő oldalakhoz hivatkozásokat is adhat. Hozzáadhatja például egy eszköz irányítópultjának vagy a beállítások oldalának a hivatkozását.

3. A **Kép** lehetőséget is választhatja, és feltölthet egy megjelenítendő képet a kezdőlapra. A képek olyan URL-címmel rendelkezhetnek, amelyeket rájuk kattintva megnyithat:

    ![Kép hozzáadása a kezdőlaphoz](media/tutorial-customize-operator/addimage.png)

    További tudnivalókért lásd a [képek előkészítését és az Azure IoT Central-alkalmazásba való feltöltését](howto-prepare-images.md) ismertető szakaszt.

## <a name="preview-the-default-home-page-as-an-operator"></a>Az alapértelmezett kezdőlap előnézetének megtekintése operátorként

Ha nem kívánja tovább szerkeszteni a kezdőlapot, hanem operátorként szeretné megtekinteni annak előnézetét, válassza a **Kész** lehetőséget az oldal jobb felső részén

![A Tervezési mód be- és kikapcsolása](media/tutorial-customize-operator/operatorviewhome.png)

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

* [Az eszközök monitorozása (operátorként)](tutorial-monitor-devices.md)
* [Új eszköz hozzáadása az alkalmazáshoz (operátorként és eszközfejlesztőként)](tutorial-add-device.md)
