---
title: Valós eszköz hozzáadása Azure IoT Central-alkalmazáshoz | Microsoft Docs
description: Operátorként valós eszközt adhat az Azure IoT Central-alkalmazáshoz.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7191c98cfc522068dbea576a8f81776ae4301da8
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878172"
---
# <a name="tutorial-add-a-simulated-device-to-your-azure-iot-central-application-preview-features"></a>Oktatóanyag: Szimulált eszköz hozzáadása az Azure IoT Central-alkalmazáshoz (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ez az oktatóanyag bemutatja, hogyan adhat hozzá és konfigurálhat szimulált eszközt a Microsoft Azure IoT Central alkalmazáshoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új szimulált eszköz hozzáadása
> * Szimulált eszköz használata az építési élményben

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a szerkesztőnek el kell végeznie az első Build-oktatóanyagot az Azure IoT Central-alkalmazás létrehozásához:

* [Új eszköztípus definiálása](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Kötelező)

## <a name="add-a-simulated-device"></a>Szimulált eszköz hozzáadása

Ha valódi eszközt szeretne felvenni az alkalmazásba, használja az [új eszköz típusának meghatározása](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) oktatóanyagban létrehozott **környezeti érzékelő** -eszköz sablont.

1. Ha új eszközt szeretne felvenni, válassza az **eszközök** lehetőséget a bal oldali navigációs menüben. Az **eszközök** lapon az **összes eszköz** és a **környezeti érzékelő** eszköz sablonja látható.

1. Szimulált környezeti érzékelő eszköz hozzáadásához válassza az **+ új**lehetőséget. Használja a javasolt **eszköz azonosítóját** , vagy adja meg a saját kisbetűs **eszközének azonosítóját**. Megadhatja az új eszköz nevét is. Váltson a **szimulált** váltógomb értékre, majd válassza a **Létrehozás**lehetőséget.

    ![Szimulált eszköz](./media/tutorial-add-device-pnp/simulated-device.png)

Mostantól a Builder által az eszköz sablonja által létrehozott nézeteket szimulált adatként is használhatja.

## <a name="use-a-simulated-device-to-improve-views"></a>A nézetek fejlesztése szimulált eszköz használatával

Miután létrehozta az új szimulált eszközt, a szerkesztő ezzel az eszközzel folytathatja a fejlesztést és az eszköz sablonra épülő nézeteit.

1. Az eszköz nézetében másolja a létrehozott szimulált eszköz azonosítóját.

1. A bal oldali navigációs menüben válassza a **sablonok** fület, és válassza ki a **környezeti érzékelő** sablont.

1. Válassza ki a szerkeszteni kívánt nézeteket, vagy hozzon létre egy új nézetet. Kattintson az **előnézet eszköz konfigurálása**elemre. Itt választhat, hogy nincs-e előnézeti eszköz a teszteléshez konfigurálható valódi eszköz vagy a IoT Centralba felvett meglévő eszköz között.

1. Válassza a **kiválasztás egy futó eszközről** lehetőséget, és írja be a másolt szimulált eszköz **azonosítóját** .

1. Válassza az **Alkalmaz** lehetőséget. Most már megtekintheti ugyanazt a szimulált eszközt az eszköz sablon nézetének létrehozási felületén. Ez a nézet különösen hasznos a diagramok és egyéb vizualizációk esetében.

    ![Előnézet eszköz konfigurálása](./media/tutorial-add-device-pnp/configure-preview.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Új szimulált eszköz hozzáadása
* Szimulált eszköz használata az építési élményben

Most, hogy egy szimulált eszközt csatlakoztatt az Azure IoT Central-alkalmazáshoz, néhány javasolt következő lépéssel.

Operátorként a következőket sajátítja el:

> [!div class="nextstepaction"]
> [Szabályok konfigurálása](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

Eszközfejlesztőként a következőket sajátítja el:

> [!div class="nextstepaction"]
> [MXChip-IoT fejlesztői készlet-eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)



