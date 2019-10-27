---
title: Képek feltöltése az Azure IoT Central alkalmazásba | Microsoft Docs
description: Építőként megtudhatja, hogyan készítheti elő és tölthet fel képeket az Azure IoT Central alkalmazásba.
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d8a863ee971fa154740f3726034fc4604b02fd34
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949923"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Képek előkészítése és feltöltése az Azure IoT Central-alkalmazásba

Ez a cikk azt ismerteti, hogyan lehet testre szabni az Azure IoT Central alkalmazást egyéni lemezképek feltöltésével. Testreszabhatja például az eszköz irányítópultját az eszköz képével.

## <a name="before-you-begin"></a>Előzetes teendők

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

1. Azure IoT Central-alkalmazás. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
1. A képfájlok méretezésére és átméretezésére szolgáló eszköz.

## <a name="choose-where-to-use-custom-images"></a>Válassza ki, hol szeretné használni az egyéni lemezképeket

Egyéni lemezképeket az Azure IoT Central alkalmazás következő helyeihez adhat hozzá:

* A **saját alkalmazások** oldal

    ![Rendszerkép az Application Manager lapon](media/howto-prepare-images/applicationmanager.png)

* Az alkalmazás irányítópultja

    ![Alkalmazás-irányítópult képe](media/howto-prepare-images/homepage.png)

* Egy eszköz sablonja

    ![Rendszerkép az eszköz sablonján](media/howto-prepare-images/devicetemplate.png)

* Egy csempe egy eszköz irányítópultján

    ![Rendszerkép az eszköz csempén](media/howto-prepare-images/devicetile.png)

* Egy eszközre beállított irányítópult csempéje

    ![Rendszerkép az eszköz set csempén](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>A képek előkészítése

Mind a négy helyen használhat PNG, GIF vagy JPEG formátumú képeket.

A következő táblázat összefoglalja a használható képméreteket:

| Földrajzi egység | Méretek |
| -------- | ------ |
| Application Manager | 268x160 px |
| Eszköz sablonja | 64x64 px |
| Irányítópult-csempék | A legkisebb méretű csempe a 200x200 px, a nagyobb csempék pedig négyzetes vagy téglalap alakú, kis csempék lehetnek. Például 200x400 px, 400x200 px vagy 400x400 px |

Az alkalmazás legjobb megjelenítéséhez olyan képeket kell létrehoznia, amelyek megfelelnek az előző táblázatban látható dimenzióknak.

## <a name="upload-the-images"></a>Képek feltöltése

A következő szakaszok azt ismertetik, hogyan tölthetők fel a lemezképek a különböző helyszínekre:

### <a name="application-manager"></a>Application Manager

Ha fel szeretne tölteni egy képet, amelyet a **saját alkalmazások** oldalon szeretne használni, navigáljon az **alkalmazás beállításai** lapra az **Adminisztráció** szakaszban. A feladat végrehajtásához rendszergazdának kell lennie:

![Alkalmazás rendszerképének feltöltése](media/howto-prepare-images/uploadapplicationmanager.png)

Válassza ki az **alkalmazás rendszerképének** csempéjét a rendszerkép (268x160 px) a helyi gépről való feltöltéséhez.

### <a name="application-dashboard"></a>Alkalmazás irányítópultja

Ha fel szeretne tölteni egy rendszerképet az alkalmazás irányítópultján, navigáljon az alkalmazás **irányítópult** lapjára, és válassza a **Szerkesztés**lehetőséget. A feladat elvégzéséhez a következőnek kell lennie:

![Irányítópult-rendszerkép feltöltése](media/howto-prepare-images/uploadhomepage.png)

A rendszerkép **beállítása**területen válassza a **rendszerkép** csempét a rendszerkép helyi számítógépről való feltöltéséhez. A legkisebb méretű csempe a 200x200 px, a nagyobb csempék pedig négyzetes vagy téglalap alakú, kis csempék lehetnek. Például 200x400 px, 400x200 px vagy 400x400 px.

**Mentse** a feltöltött képet. A szerkesztési módban is átméretezheti. Ha elkészült, válassza a **kész** lehetőséget.

### <a name="device-template"></a>Eszköz sablonja

Ha egy sablont szeretne feltölteni egy eszközre, navigáljon az **eszközök sablonjaihoz** , és válassza ki az eszköz sablonját. A feladat elvégzéséhez a következőnek kell lennie:

![Eszköz sablonjának feltöltése](media/howto-prepare-images/uploaddevicetemplate.png)

Válassza ki a képcsempét a rendszerkép (64x64 px) a helyi gépről való feltöltéséhez.

### <a name="device-dashboard"></a>Az eszköz irányítópultja

Ha fel szeretne tölteni egy képet egy eszköz irányítópultján, navigáljon az **eszközök sablonjaihoz** , és válassza ki az eszköz sablonját. Ezután válassza az **irányítópult** fület. A feladat elvégzéséhez a következőnek kell lennie:

![Eszköz irányítópult-rendszerképének feltöltése](media/howto-prepare-images/uploaddevicedashboard.png)

A **rendszerkép konfigurálása**területen válassza a **rendszerkép** csempét, majd válassza ki a feltölteni kívánt fájlt a helyi gépről. A legkisebb méretű csempe a 200x200 px, a nagyobb csempék pedig négyzetes vagy téglalap alakú, kis csempék lehetnek. Például 200x400 px, 400x200 px vagy 400x400 px.

**Mentse** a feltöltött képet. A szerkesztési mód használatával átméretezheti és áthelyezheti azt. Ha elkészült, válassza a **kész** lehetőséget.

### <a name="device-set-dashboard"></a>Eszközbeállítások irányítópultja

Ha a képet egy eszközbeállítások irányítópultján szeretné feltölteni, navigáljon az **eszközök** készletéhez, és válassza ki az eszközt, majd egy eszközt. Ezután válassza az **irányítópult** lapot, és válassza a **Szerkesztés**lehetőséget:

![Eszköz készletének irányítópult-rendszerképének feltöltése](media/howto-prepare-images/uploaddevicesetdashboard.png)

A rendszerkép **beállítása**területen válassza a **rendszerkép** csempét a rendszerkép helyi számítógépről való feltöltéséhez. A legkisebb méretű csempe a 200x200 px, a nagyobb csempék pedig négyzetes vagy téglalap alakú, kis csempék lehetnek. Például 200x400 px, 400x200 px vagy 400x400 px.

**Mentse** a feltöltött képet. A szerkesztési mód használatával átméretezheti és áthelyezheti azt. Ha elkészült, válassza a **kész** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan készítheti elő és töltheti fel a rendszerképeket az Azure IoT Central-alkalmazásba, a következő lépéseket javasoljuk:

* [Az Azure IoT Central felhasználói felületének testreszabása](./howto-customize-ui.md)
* [Csempék hozzáadása az irányítópulthoz](./howto-add-tiles-to-your-dashboard.md)
* [Az Azure IoT Central-alkalmazásban lévő eszközök kezelése](howto-manage-devices.md)
