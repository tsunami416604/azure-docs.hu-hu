---
title: Képek feltöltése az Azure IoT Central alkalmazáshoz |} A Microsoft Docs
description: A jelentéskészítő, megtudhatja, hogyan készítheti elő és képek feltöltése az Azure IoT Central alkalmazáshoz.
author: dominicbetts
ms.author: dobett
ms.date: 10/31/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f0e4d1badd0ed992c96f6624813e575d0f286d8b
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216798"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Készítse elő és képek feltöltése az Azure IoT Central alkalmazáshoz

Ez a cikk bemutatja, hogyan, mint szerkesztő, szabhatja testre az Azure IoT Central alkalmazásnak fel kell töltenie egyéni rendszerképek. Például egy képet az eszköz irányítópultját szabhatja testre.

## <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

1. Azure IoT Central alkalmazáshoz. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
1. Egy eszköz méretezés és képfájlok átméretezése.

## <a name="choose-where-to-use-custom-images"></a>Válassza ki, hogy egyéni lemezképeket használ

Egyéni rendszerképek adhat hozzá a következő helyeken és az Azure IoT Central alkalmazáshoz:

* A **alkalmazáskezelő** lap

    ![Kép application manager oldalon](media/howto-prepare-images/applicationmanager.png)

* Az irányítópult

    ![Alkalmazás irányítópult lemezkép](media/howto-prepare-images/homepage.png)

* Egy eszköz sablon

    ![Az eszköz sablon rendszerképet](media/howto-prepare-images/devicetemplate.png)

* Az eszköz Irányítópulton egy csempe

    ![Az eszköz csempére kép](media/howto-prepare-images/devicetile.png)

* Egy csempe az eszközön irányítópult beállítása

    ![Az eszköz beállítása csempére kép](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>A rendszerképek előkészítése

Az összes négy helyszínen PNG, GIF, vagy JPEG-képek is használhatja.

A következő táblázat összefoglalja a lemezkép mérete is használhatja:

| Hely | Méretek |
| -------- | ------ |
| Application Manager | 268x160 px |
| Eszközsablon | 64 x 64 képpont |
| Irányítópult-csempék | A legkisebb méretű csempe 200 x 200 képpont, nagyobb csempék kisméretű csempe négyzet vagy téglalap alakú többszöröse lehet. Ha például 200-as x 400 képpont, 400 x 200 képpont vagy 400 x 400 képpont |

A legjobb megjelenítés az alkalmazásban létre kell hoznia a lemezképek, amelyek megfelelnek a dimenziók az előző táblázatban látható.

## <a name="upload-the-images"></a>A képek feltöltése

A következő szakaszok ismertetik a különböző helyeken található rendszerképek feltöltése:

### <a name="application-manager"></a>Alkalmazás-kezelő

Tölthet fel képeket a a **alkalmazáskezelő**, keresse meg a **Alkalmazásbeállítások** lapját a **felügyeleti** szakaszban. Ez a feladat végrehajtásához rendszergazdának kell lennie:

![Alkalmazás-Rendszerkép feltöltése](media/howto-prepare-images/uploadapplicationmanager.png)

Az előkészített képet feltölteni az alkalmazás-lemezképet csempére kattintva (268 x 160 px) a helyi gépen.

### <a name="application-dashboard"></a>Alkalmazás irányítópultja

Töltsön fel egy képet, az alkalmazás irányítópult, lépjen a **irányítópult** az alkalmazás, és kattintson a **szerkesztése**. Ez a feladat végrehajtásához egy jelentéskészítő kell lennie:

![Alkalmazás irányítópult Rendszerkép feltöltése](media/howto-prepare-images/uploadhomepage.png)

Konfigurálása kép alatt kattintson a lemezkép csempére, és töltse fel az előkészített rendszerképet a helyi gépen. A legkisebb méretű csempe 200 x 200 képpont, nagyobb csempék kisméretű csempe négyzet vagy téglalap alakú többszöröse lehet. Ha például 200-as x 400 képpont, 400 x 200 képpont vagy 400 x 400 képpont.

**Mentés** a feltöltött kép. Átméretezhető a szerkesztési módban. Kattintson a **kész** befejezésekor. 

### <a name="device-template"></a>Eszközsablon

Töltsön fel egy képet, az eszköz sablonból, navigáljon a **Device Explorer**, válassza ki az eszköz sablont, majd egy eszközt. Ez a feladat végrehajtásához egy jelentéskészítő kell lennie:

![Eszköz sablon Rendszerkép feltöltése](media/howto-prepare-images/uploaddevicetemplate.png)

A kép csempére az előkészített kép feltöltése (64 x 64 képpont) a helyi gépen. 

### <a name="device-dashboard"></a>Az eszköz irányítópultja

Töltsön fel egy képet, az eszköz irányítópulton, navigáljon a **Device Explorer**, válassza ki az eszköz sablont, majd egy eszközt. Majd válassza ki a **irányítópult** lapon, majd kattintson a **sablon szerkesztése**. Ez a feladat végrehajtásához egy jelentéskészítő kell lennie:

![Eszköz irányítópult Rendszerkép feltöltése](media/howto-prepare-images/uploaddevicedashboard.png)

A lemezkép konfigurálása a lemezkép csempére, és válassza a helyi gépen a feltölteni kívánt fájl. A legkisebb méretű csempe 200 x 200 képpont, nagyobb csempék kisméretű csempe négyzet vagy téglalap alakú többszöröse lehet. Ha például 200-as x 400 képpont, 400 x 200 képpont vagy 400 x 400 képpont.

**Mentés** a feltöltött kép. Átméretezheti és áthelyezheti, míg a szerkesztési módban. Kattintson a **kész** befejezésekor.

### <a name="device-set-dashboard"></a>Eszköz irányítópult beállítása

Töltsön fel egy képet, az eszköz beállítása irányítópulton, navigáljon a **eszköz csoportok** , és válassza ki az eszköz beállítása, és egy eszköz. Majd válassza ki a **irányítópult** lapon, majd kattintson a **szerkesztése**:

![Töltse fel az eszköz az irányítópulton kép beállítása](media/howto-prepare-images/uploaddevicesetdashboard.png)

Konfigurálása kép alatt kattintson a lemezkép csempére, és töltse fel az előkészített rendszerképet a helyi gépen. A legkisebb méretű csempe 200 x 200 képpont, nagyobb csempék kisméretű csempe négyzet vagy téglalap alakú többszöröse lehet. Ha például 200-as x 400 képpont, 400 x 200 képpont vagy 400 x 400 képpont.

**Mentés** a feltöltött kép. Átméretezheti és áthelyezheti, míg a szerkesztési módban. Kattintson a **kész** befejezésekor.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan készítheti elő és képek feltöltése az Azure IoT Central alkalmazáshoz, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Eszközök kezelése az Azure IoT Central alkalmazáshoz a](howto-manage-devices.md)