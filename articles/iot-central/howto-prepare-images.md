---
title: Képek feltöltése az Azure IoT Central alkalmazáshoz |} A Microsoft Docs
description: A jelentéskészítő, megtudhatja, hogyan készítheti elő és képek feltöltése az Azure IoT Central alkalmazáshoz.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 18c44a3d91a4964d054c8e142394da7d69772ed0
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50960701"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Készítse elő és képek feltöltése az Azure IoT Central alkalmazáshoz

Ez a cikk bemutatja, hogyan, mint szerkesztő, szabhatja testre a Microsoft Azure IoT Central alkalmazásnak fel kell töltenie egyéni rendszerképek. Például egy képet az eszköz irányítópultját szabhatja testre.

## <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

1. Azure IoT Central alkalmazáshoz. További információkért lásd: a [hozzon létre egy alkalmazás rövid](quick-deploy-iot-central.md).
1. Egy eszköz méretezés és képfájlok átméretezése.

## <a name="choose-where-to-use-custom-images"></a>Válassza ki, hogy egyéni lemezképeket használ

Egyéni rendszerképek adhat hozzá a következő helyeken és az Azure IoT Central alkalmazáshoz:

* A **alkalmazáskezelő** lap

    ![Kép application manager oldalon](media/howto-prepare-images/applicationmanager.png)

* A kezdőlap

    ![Kép a kezdőlapon](media/howto-prepare-images/homepage.png)

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
| **Alkalmazás-kezelő** | 268 x 160 px |
| Eszköz-sablon | 64 x 64 képpont |
| Kezdőlap és irányítópult-csempék | A legkisebb méretű csempe 200 x 200 képpont, nagyobb csempék kisméretű csempe négyzet vagy téglalap alakú többszöröse lehet. Ha például 200-as x 400 képpont, 400 x 200 képpont vagy 400 x 400 képpont |

A legjobb megjelenítés az alkalmazásban létre kell hoznia a lemezképek, amelyek megfelelnek a dimenziók az előző táblázatban látható.

## <a name="upload-the-images"></a>A képek feltöltése

A következő szakaszok ismertetik, hogyan tölthet fel a rendszerképet a különböző helyeken:

### <a name="application-manager"></a>Alkalmazás-kezelő

Tölthet fel képeket a használatára a **alkalmazáskezelő**, keresse meg a **Alkalmazásbeállítások** lap a **felügyeleti** szakaszban. Ez a feladat végrehajtásához rendszergazdának kell lennie:

![Alkalmazás-Rendszerkép feltöltése](media/howto-prepare-images/uploadapplicationmanager.png)

A feltöltés képre, és válassza a helyi gépen a feltölteni kívánt fájl.

### <a name="home-page"></a>Kezdőlap

Töltsön fel egy képet, a kezdőlap használatára, lépjen a **kezdőlap** az alkalmazás és a kapcsoló tervezési mód. Ez a feladat végrehajtásához egy jelentéskészítő kell lennie:

![Kezdőlap-Rendszerkép feltöltése](media/howto-prepare-images/uploadhomepage.png)

A feltöltés képre, és válassza a helyi gépen a feltölteni kívánt fájl.

Miután feltölti a lemezképet, átméretezheti, amíg bekapcsolva a Tervező módban van.

### <a name="device-template"></a>Eszköz-sablon

Töltse fel a képet, hogy egy eszköz-sablont használja, lépjen **Device Explorer**, válassza ki az eszköz sablont és egy eszköz és tervezési mód váltani. Ez a feladat végrehajtásához egy jelentéskészítő kell lennie:

![Eszköz sablon Rendszerkép feltöltése](media/howto-prepare-images/uploaddevicetemplate.png)

A feltöltés képre, és válassza a helyi gépen a feltölteni kívánt fájl.

### <a name="device-dashboard"></a>Az eszköz irányítópultja

Használja az eszköz irányítópulton kép feltöltésével, navigáljon a **Device Explorer**, válassza ki az eszköz sablont, majd egy eszközt. Majd válassza ki a **irányítópult** oldal és a kapcsoló a Tervező módban. Ez a feladat végrehajtásához egy jelentéskészítő kell lennie:

![Eszköz irányítópult Rendszerkép feltöltése](media/howto-prepare-images/uploaddevicedashboard.png)

A feltöltés képre, és válassza a helyi gépen a feltölteni kívánt fájl.

Miután feltölti a lemezképet, átméretezése és áthelyezése során **tervezési mód** van-e kapcsolva.

### <a name="device-set-dashboard"></a>Eszköz irányítópult beállítása

Töltsön fel egy képet, az irányítópult egy eszköz beállítása használatát, navigáljon a **eszköz csoportok** , és válassza ki az eszköz beállítása, és egy eszköz. Majd válassza a **irányítópult** oldal és a kapcsoló **tervezési mód** meg:

![Töltse fel az eszköz az irányítópulton kép beállítása](media/howto-prepare-images/uploaddevicesetdashboard.png)

A feltöltés képre, és válassza a helyi gépen a feltölteni kívánt fájl.

Miután feltölti a lemezképet, méretezze át, és helyezze át, miközben tervezési mód engedélyezve van.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan készítheti elő és képek feltöltése az Azure IoT Central alkalmazáshoz, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Eszközök kezelése az Azure IoT Central alkalmazáshoz a](howto-manage-devices.md)