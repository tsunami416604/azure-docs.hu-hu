---
title: Képek feltöltése az Azure IoT központi alkalmazás |} Microsoft Docs
description: Szerkesztő, mert megtudhatja, hogyan készítse elő, és a képek feltöltése az Azure IoT központi alkalmazáshoz.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7fd9c8ed5559b00bc755e3f04c768dceeb487562
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628021"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Készítse elő, és a képek feltöltése az Azure IoT központi alkalmazáshoz

Ez a cikk ismerteti, hogyan, szerkesztő, mert testre szabhatja a Microsoft Azure IoT központi alkalmazás egyéni képek feltöltése. Például testre szabhatja az eszköz ábrázoló eszköz irányítópultot.

## <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

1. Azure IoT központi alkalmazás. További információkért lásd: [létrehozása az Azure IoT központi alkalmazás](howto-create-application.md).
1. Méretezés és képfájlok átméretezése eszköz.

## <a name="choose-where-to-use-custom-images"></a>Adja meg az egyéni lemezképek használandó helyét

Egyéni lemezképek adhat hozzá az Azure IoT központi alkalmazásban a következő helyeken:

* A **alkalmazáskezelő** lap

    ![Alkalmazás manager lapján kép](media/howto-prepare-images/applicationmanager.png)

* A kezdőlap

    ![Kép kezdőlapján](media/howto-prepare-images/homepage.png)

* Egy eszköz sablon

    ![Az eszköz sablon kép](media/howto-prepare-images/devicetemplate.png)

* Az eszköz Irányítópulton egy csempére

    ![A eszközcsempe kép](media/howto-prepare-images/devicetile.png)

* Az eszközön egy csempe irányítópult beállítása

    ![A készlet eszközcsempe kép](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>A képek készítésére

Az összes négy helyszínen PNG, GIF, vagy JPEG-képek is használhatja.

A következő táblázat összefoglalja a lemezkép mérete használhatja:

| Hely | Méretek |
| -------- | ------ |
| **Alkalmazás-kezelő** | 268 x 160 képpont |
| Eszköz sablon | 64 x 64 képpont |
| Kezdőlap és irányítópulton található csempe | A legkisebb méretű csempe értéke 200 x 200 képpont, nagyobb csempék kis csempéit négyzet vagy téglalap alakú többszörösei lehet. Például 200 x 400 képpont, 400 x 200 képpont vagy 400 x 400 képpont |

A legjobb jeleníthető meg az alkalmazásban készítsen lemezképeket, amelyek megfelelnek az előző táblázatban a dimenziók.

## <a name="upload-the-images"></a>A képek feltöltése

Az alábbi szakaszok ismertetik a különböző helyeken használja a képek feltöltése:

### <a name="application-manager"></a>Alkalmazás-kezelő

Töltse fel a használandó kép a **alkalmazáskezelő**, keresse meg a **Alkalmazásbeállítások** lapját a **felügyeleti** szakasz. A feladat végrehajtásához rendszergazdának kell lennie:

![Töltse fel az alkalmazás-lemezképet](media/howto-prepare-images/uploadapplicationmanager.png)

Kattintson a feltöltés lemezképet, és válassza az a helyi gép feltölteni a fájlt.

### <a name="home-page"></a>Kezdőlap

Töltse fel a kezdőlapon használandó kép, navigáljon a **kezdőlap** az alkalmazás és a kapcsoló Tervező módban a. Ez a feladat elvégzéséhez Szerkesztő kell lennie:

![Töltse fel a Kezdőlap képe](media/howto-prepare-images/uploadhomepage.png)

Kattintson a feltöltés lemezképet, és válassza az a helyi gép feltölteni a fájlt.

A feltöltését követően átméretezhető tervezési módba bekapcsolt közben.

### <a name="device-template"></a>Eszköz sablon

Töltse fel a képet, hogy egy eszköz-sablont használja, navigáljon a **eszköz Explorer**, válassza ki az eszköz sablont és egy eszköz, és Tervező módban váltani. Ez a feladat elvégzéséhez Szerkesztő kell lennie:

![Eszköz sablon-Rendszerkép feltöltése](media/howto-prepare-images/uploaddevicetemplate.png)

Kattintson a feltöltés lemezképet, és válassza az a helyi gép feltölteni a fájlt.

### <a name="device-dashboard"></a>Az eszköz irányítópultja

Töltse fel az eszköz irányítópulton használandó kép, navigáljon a **eszköz Explorer**, válassza ki az eszköz sablont, és egy eszköz. Válassza ki a **irányítópult** oldal és a kapcsoló a Tervező módban. Ez a feladat elvégzéséhez Szerkesztő kell lennie:

![Eszköz irányítópult lemezkép feltöltése](media/howto-prepare-images/uploaddevicedashboard.png)

Kattintson a feltöltés lemezképet, és válassza az a helyi gép feltölteni a fájlt.

A feltöltését követően átméretezése, és közben áthelyezése **tervezési módba** bekapcsolt.

### <a name="device-set-dashboard"></a>Irányítópult beállítva

Töltse fel az eszköz set irányítópulton használandó kép, navigáljon a **eszköz beállítása** , és válassza ki a eszköz-készlet, és egy eszköz. Válassza ki a **irányítópult** lap és kapcsoló **tervezési módba** meg:

![Töltse fel az irányítópult kép eszköz beállítása](media/howto-prepare-images/uploaddevicesetdashboard.png)

Kattintson a feltöltés lemezképet, és válassza az a helyi gép feltölteni a fájlt.

Miután feltölti a lemezképet, átméretezése, és közben tervezési módba bekapcsolt áthelyezése.

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megtudta, hogyan lehet előkészíteni és a képek feltöltése az Azure IoT központi alkalmazáshoz, ez a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az Azure IoT központi alkalmazásban eszközök kezelése](howto-manage-devices.md)