---
title: Létrehozása és kezelése az Azure IoT központi alkalmazás eseményszabályok |} A Microsoft Docs
description: Az Azure IoT Central esemény szabályok lehetővé teszik az eszközök, közel valós időben figyelheti és automatikusan követve indíthatók el műveletek, például egy e-mailt küldhet a szabály aktiválásakor.
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: c5697f6d4ca2c9d9948b7cdd005a6a75bdabb246
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222565"
---
# <a name="create-an-event-rule-and-set-up-an-action-in-your-azure-iot-central-application"></a>Hozzon létre egy esemény-szabályt, és állítsa be az Azure IoT Central alkalmazásban művelet

A Microsoft Azure IoT Central használatával távolról figyeli a csatlakoztatott eszközök. Az Azure IoT Central szabályok lehetővé teszik az eszközök, közel valós időben figyelheti és automatikusan meghívni a műveleteket, például egy e-mailt küld, vagy a Microsoft Flow munkafolyamatok indítására, amikor a szabály feltételek teljesülnek. Mindössze pár kattintással megadhatja a feltétel az adatok figyelésére, és konfigurálja a műveletet az elindításához. Ez a cikk ismerteti a figyelési szabály részletesen esemény.

Használja az Azure IoT Central [esemény mérési](howto-set-up-template.md) eszköz kapcsolatos adatok rögzítéséhez. Minden mérési legfőbb attribútumai, amelyek meghatározzák a mérték rendelkezik. Létrehozhat szabályokat minden típusú eszköz mérési figyelheti, és riasztást generál, ha a szabály gondoskodik arról. Egy esemény szabály aktivál, hogy a kiválasztott eszköz esemény az eszköz által jelentett.

## <a name="create-an-event-rule"></a>Eseményszabály létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre egy esemény-szabályt. Ebben a példában a jelentések ventilátor motor hibaesemény egy hűtött Eladóautomata eszközt használ. A szabály az eseményt, az eszköz által jelentett figyeli, és a egy e-mailt küld, amikor az eseményt jelentett.

1. Az eszköz a szabályt ad hozzá az eszköz részleteit megjelenítő oldalra léphet.

1. Szabályok még nem hozta létre, ha a következő képernyő jelenik meg:

    ![Még nincsenek szabályai](media/howto-create-event-rules/image1.png)

1. Az a **szabályok** lapra, majd **+ új szabály** , milyen típusú szabályokat hozhat létre.

    ![Szabály típusa](media/howto-create-event-rules/image2.png)

1. Kattintson a **esemény** a szabály létrehozásához a képernyő megnyitásához.

    ![Események szabálya](media/howto-create-event-rules/image3.png)

1. Válasszon egy nevet, amely segít azonosítani a szabály az eszköz sablonban.

1. Váltsa át a szabály a sablon alapján létrehozott összes eszköz azonnal engedélyezéséhez **engedélyezése a szabály**.

### <a name="configure-the-rule-condition"></a>A szabály a feltétel konfigurálása

Ez a szakasz bemutatja, hogyan figyelheti a ventilátor motor hiba esemény mérték feltétel hozzáadása.

1. Válassza ki a **+** melletti **feltétel**.

1. A figyelni kívánt legördülő listából válassza ki az esemény mérési. Ebben a példában **ventilátor Motor hiba** esemény lett kiválasztva.

1. Igény szerint is megadhat egy értéket abban az esetben, ha figyelemmel szeretné követni egy adott érték az esemény, az eszköz által jelentett. Például ha az eszköz a különböző hibakódok majd biztosít a hibakódot a szabály a feltétel értékként az ugyanahhoz az eseményhez jelentéseket biztosítja, hogy a szabály aktiválásakor csak akkor, ha az eszköz az adott értéket küldi az eseménytartalom. Ezt a részt hagyja, azt jelenti, hogy a szabály aktivál, amikor az eszköz által az esemény event értékétől függetlenül.

    ![Feltétel hozzáadása](media/howto-create-event-rules/image4.png)

    > [!NOTE]
    > Egy esemény szabályfeltétel meghatározása során ki kell választania legalább egy eseményt mérték.

1. Kattintson a **mentése** a szabály mentéséhez. A szabály élesíti néhány percen belül, és elindítja az alkalmazásnak küldött események figyelését.

### <a name="add-an-action"></a>Művelet hozzáadása

A TIS példa bemutatja, hogyan művelet hozzáadása a szabályhoz. Ez jeleníti meg az e-mail-művelet hozzáadása, de más műveleteket is hozzáadhat:
-  [Microsoft Flow művelet](howto-add-microsoft-flow.md) elindít egy munkafolyamatot a Microsoft Flow, amikor egy szabály akkor lesz kiváltva
- [Webhook művelettel](howto-create-webhooks.md) más szolgáltatások értesíti, amikor egy szabály akkor lesz kiváltva

> [!NOTE]
> Jelenleg csak 1 művelet lehet egyetlen szabály van társítva.

1. Válassza ki a **+** melletti **műveletek**. Itt láthatja az elérhető műveletek listáját.

    ![Művelet hozzáadása](media/howto-create-event-rules/image5.png)

1. Válassza ki a **E-mail** művelet, adjon meg egy érvényes e-mail-címmel a **való** mezőben, és ügyeljen arra, hogy a szabály aktiválásakor jelennek meg az e-mail törzsét adja meg.

    > [!NOTE]
    > Csak hozzá az alkalmazáshoz, és legalább egyszer bejelentkezett felhasználók kapnak e-mailt. Tudjon meg többet [felhasználókezelés](howto-administer.md) az Azure IoT Central.

   ![Művelet konfigurálása](media/howto-create-event-rules/image6.png)

1. Kattintson a **Save** (Mentés) gombra. A szabály élesíti néhány percen belül, és elindítja az alkalmazásnak küldött események figyelését. Megegyezik a feltételt a szabályban megadott, a szabály eseményindítók a beállított e-mail-művelet.

## <a name="parameterize-the-rule"></a>A szabály paraméterezése

Műveletek használatával is konfigurálhatók **Eszköztulajdonság** paraméterként. Ha egy e-mail-cím egy adott eszköztulajdonság van tárolva, akkor meghatározása során is használható a **való** címet.

## <a name="delete-a-rule"></a>Szabály törlése

Ha már nincs szüksége egy szabályt, törölje azt a szabály megnyitásával, majd válassza a **törlése**. A szabály törlése eltávolítja az eszköz sablon és a kapcsolódó eszközöket.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Engedélyezi vagy letiltja az eszköz sablon szabály

Keresse meg az eszközt, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. Választás a **engedélyezése a szabály az összes eszközt, ez a sablon** gombra a szabály engedélyezése vagy letiltása a szabály az eszköz-sablonhoz társított összes eszköz.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Engedélyezi vagy letiltja az eszköz szabály

Keresse meg az eszközt, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. Váltás a **engedélyezése a szabály az eszköz** gombra kattintva engedélyezheti vagy letilthatja a szabály az eszközön.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan hozhat létre szabályokat az Azure IoT Central alkalmazáshoz, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [A Microsoft Flow-művelet hozzáadása a szabály](howto-add-microsoft-flow.md)
> [kezelése az eszközök](howto-manage-devices.md).
