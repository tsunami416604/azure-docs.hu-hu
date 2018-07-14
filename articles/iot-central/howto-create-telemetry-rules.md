---
title: Hozzon létre, és az Azure IoT Central alkalmazáshoz a telemetriai adatok szabályok kezelése |} A Microsoft Docs
description: Az Azure IoT-központ-telemetria szabályok lehetővé teszik az eszközök, közel valós időben figyelheti és automatikusan követve indíthatók el műveletek, például egy e-mailt küldhet a szabály aktiválásakor.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 083410c6407ce7aa83c3829f884890561b0b44b8
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008212"
---
# <a name="create-a-telemetry-rule-and-set-up-an-action-in-your-azure-iot-central-application"></a>Telemetria szabály létrehozása és az Azure IoT Central alkalmazásban művelet beállítása

A Microsoft Azure IoT Central használatával távolról figyeli a csatlakoztatott eszközök. Az Azure IoT Central szabályok lehetővé teszik az eszközök, közel valós időben figyelheti és automatikusan meghívni a műveleteket, például egy e-mailt küld, vagy a Microsoft Flow munkafolyamatok indítására, amikor a szabály feltételek teljesülnek. Mindössze néhány kattintással a feltételeket, az adatok figyeléséhez, és konfigurálja a műveletet az elindításához megadhatja. Ez a cikk ismerteti részletesen a telemetriai adatok szabály.

Használja az Azure IoT Central [telemetriai mérések](howto-set-up-template.md) eszköz kapcsolatos adatok rögzítéséhez. Minden mérési legfőbb attribútumai, amelyek meghatározzák a mérték rendelkezik. Létrehozhat szabályokat minden típusú eszköz mérési figyelheti, és riasztást generál, ha a szabály gondoskodik arról. Telemetria szabály aktivál, hogy a kiválasztott eszköz telemetriai átlép egy küszöbértéket.

## <a name="create-a-telemetry-rule"></a>Telemetria szabály létrehozása

Ez a szakasz bemutatja, hogyan telemetriai szabály létrehozásához. Ebben a példában hőmérséklettel és páratartalommal kapcsolatos telemetriai adatokat küld a légkondicionálóját csatlakoztatott eszközt használja. A szabály az eszköz által jelentett hőmérséklet figyeli, és a egy e-mailt küld, ha ezt túllépik 80 fok.

1. Az eszköz a szabályt ad hozzá az eszköz részleteit megjelenítő oldalra léphet.

1. Szabályok még nem hozta létre, ha a következő képernyő jelenik meg:

    ![Még nincsenek szabályai](media/howto-create-telemetry-rules/image1.png)

1. Az a **szabályok** lapra, majd **+ új szabály** , milyen típusú szabályokat hozhat létre.

    ![Szabály típusa](media/howto-create-telemetry-rules/image2.png)

1. Válassza ki a **Telemetriai** csempére kattintva nyissa meg a szabály létrehozása az űrlap.

    ![Telemetria szabály](media/howto-create-telemetry-rules/image3.png)

1. Válasszon egy nevet, amely segít azonosítani a szabály az eszköz sablonban.

1. Váltsa át a szabály a sablon alapján létrehozott összes eszköz azonnal engedélyezéséhez **engedélyezése a szabály**.

### <a name="configure-the-rule-condition"></a>A szabály a feltétel konfigurálása

Ez a szakasz bemutatja, hogyan figyelheti a hőmérsékleti telemetria feltétel hozzáadása.

1. Válassza ki a **+** melletti **feltétel**.

1. Válassza ki a **hőmérséklet** telemetriai típusát a legördülő listából. Ezután válassza ki az operátort, és adjon meg egy küszöbértéket. Telemetriai adatok több feltételt is hozzáadhat. Ha több feltétel van megadva, minden feltételeknek teljesülniük kell elindítani a szabályhoz.

    ![Telemetria feltétel hozzáadása](media/howto-create-telemetry-rules/image4.png)

    > [!NOTE]
    > Válassza ki legalább egy telemetria-mérés, a telemetriai adatok szabályfeltétel meghatározása során.

1. Kattintson a **mentése** a szabály mentéséhez. A szabály élesíti néhány percen belül, és elindítja a telemetriát küld az alkalmazás figyelését.

### <a name="add-an-action"></a>Művelet hozzáadása

Ez a szakasz bemutatja, hogyan művelet hozzáadása a szabályhoz. Ez jeleníti meg az e-mail-művelet hozzáadása, de emellett [adjon hozzá egy Microsoft Flow műveletet](howto-add-microsoft-flow.md) elindít egy munkafolyamatot a Microsoft Flow, a szabály aktiválásakor a szabályhoz.

> [!NOTE]
> Jelenleg csak 1 művelet lehet egyetlen szabály van társítva.

1. Válassza ki a **+** melletti **műveletek**. Itt láthatja az elérhető műveletek listáját.

    ![Művelet hozzáadása](media/howto-create-telemetry-rules/image5.png)

1. Válassza ki a **E-mail** művelet, adjon meg egy érvényes e-mail-címmel a **való** mezőben, és ügyeljen arra, hogy a szabály aktiválásakor jelennek meg az e-mail törzsét adja meg.

    > [!NOTE]
    > Csak hozzá az alkalmazáshoz, és legalább egyszer bejelentkezett felhasználók kapnak e-mailt. Tudjon meg többet [felhasználókezelés](howto-administer.md) az Azure IoT Central.

   ![Művelet konfigurálása](media/howto-create-telemetry-rules/image6.png)

1. Kattintson a **Save** (Mentés) gombra. A szabály élesíti néhány percen belül, és elindítja a telemetriát küld az alkalmazás figyelését. Megegyezik a feltételt a szabályban megadott, a szabály eseményindítók a beállított e-mail-művelet.

## <a name="parameterize-the-rule"></a>A szabály paraméterezése

Szabályok is nyer az egyes értékeket **eszköztulajdonságok** paraméterekként. Paraméterek használatával hasznos forgatókönyvekben, ahol a telemetria küszöbértékek eltérőek lehetnek a különböző eszközökön. A szabály létrehozásakor válassza ki egy adott eszköztulajdonság, amely a küszöbértéket, megadja például **maximális ideális küszöbérték**, rögzített érték, például a 80 fok megadása helyett. A szabály végrehajtja, amikor az eszköz telemetriai adatok egyezése a eszköz tulajdonságban megadott érték.

Paraméterek használata hatékony módszert szabályok kezelése / eszköz sablon számának csökkentése érdekében.

Műveletek használatával is konfigurálhatók **Eszköztulajdonság** paraméterként. Ha egy e-mail-cím egy adott eszköztulajdonság van tárolva, akkor meghatározása során is használható a **való** címet.

## <a name="delete-a-rule"></a>Szabály törlése

Ha már nincs szüksége egy szabályt, törölje azt a szabály megnyitásával, majd válassza a **törlése**. A szabály törlése eltávolítja az eszköz sablon és a kapcsolódó eszközöket.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Engedélyezi vagy letiltja az eszköz sablon szabály

Keresse meg az eszközt, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. Választás a **engedélyezése a szabály az összes eszközt, ez a sablon** gombra a szabály engedélyezése vagy letiltása a szabály az eszköz-sablonhoz társított összes eszköz.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Engedélyezi vagy letiltja az eszköz szabály

Keresse meg az eszközt, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. Váltás a **engedélyezése a szabály az eszköz** gombra kattintva engedélyezheti vagy letilthatja a szabály az eszközön.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure IoT központi alkalmazás szabályok szerkesztése, az alábbiakban javasolt következő lépések:

> [!div class="nextstepaction"]
> [A Microsoft Flow-művelet hozzáadása a szabály](howto-add-microsoft-flow.md)
> [az eszközök kezelése](howto-manage-devices.md)
