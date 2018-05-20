---
title: Létrehozása és kezelése az Azure IoT központi alkalmazásban eseményszabályok |} Microsoft Docs
description: Az Azure IoT központi esemény szabályok lehetővé teszik az eszközök közel valós idejű figyelését, és automatikusan meghívni a műveletek, például egy e-mailek küldéséhez, amikor a szabály gondoskodik.
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 932b1906b767ee7676f46ffd7242ad3d478d41c2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Egy esemény szabály létrehozása és az Azure IoT központi alkalmazásban értesítések beállítása

A Microsoft Azure IoT központi használatával távolról figyelheti a csatlakoztatott eszközön. Az Azure IoT központi szabályok lehetővé teszik az eszközök közel valós idejű figyelését, és automatikusan meghívni a műveletek, például egy e-mailek küldéséhez, amikor a szabály akkor váltja ki. Néhány kattintással segítségével megadhatja az állapotot, amelyet az eszköz adatok megfigyelheti és konfigurálhatja a művelet meghívásához. Ez a cikk ismerteti részletesen szabály figyelési esemény.

Használja az Azure IoT központi [esemény mérési](howto-set-up-template.md) eszköz adatok rögzítéséhez. Minden mérési, amelyek meghatározzák a mérési kulcsattribútummal rendelkezik. Minden eszköz mérési típusú figyelése, és elindítja a szabály riasztást generáljon szabályokat hozhat létre. Egy esemény szabály váltja ki, ha a kijelölt esemény nem jelentette-e az eszközön.

## <a name="create-an-event-rule"></a>Egy esemény szabály létrehozása

Ez a szakasz bemutatja, hogyan hozható létre esemény szabály. A példa egy hűtött Eladóautomata eszközt, hogy jelentések ventilátor motor hiba esemény. A szabály az eszköz által jelentett esemény figyeli, és egy e-mailt küld, amikor az esemény az elvártnak.

1. Az eszköz hozzáadásakor a szabályt, hogy az eszköz részleteit megjelenítő oldalra léphet.

1. Ha még nincsenek szabályok, a következő képernyő jelenik meg:

    ![Még nincsenek szabályok](media\howto-create-event-rules\image1.png)

1. Az a **szabályok** lapra, majd **+ új szabály** hozhat létre szabályokat típusú.

    ![Szabályok típusai](media\howto-create-event-rules\image2.png)

1. Kattintson a **esemény** a parancsra a szabály létrehozásához.

    ![Események szabálya](media\howto-create-event-rules\image3.png)

1. Válassza ki azokat a szabály az eszköz sablon neve.

1. Azonnal engedélyezése a szabály a sablon alapján létrehozott összes eszközt, váltás **engedélyezése a szabály**.

### <a name="configure-the-rule-condition"></a>A szabály feltételének konfigurálása

Ez a szakasz bemutatja, hogyan figyelheti a ventilátor motor hiba esemény mérési feltételek hozzáadása.

1. Válassza ki a **+** melletti **feltétel**.

1. A figyelni kívánt legördülő listából válassza ki az esemény mérési. Ebben a példában **ventilátor Motor hiba** esemény van beállítva.

1. Opcionálisan is megadhatja egy értéket, ha az eszköz által jelentett esemény egy adott érték figyelni szeretné. Például ha az eszköz ugyanarra az eseményre jelenti majd megadásával hibakód szabály feltételének értékként különböző hibakódokat biztosítja, hogy a szabály eseményindítók csak akkor, ha az eszköz elküldi ezt az értéket, az eseménytartalom. Ezen üresen hagyja, azt jelenti, hogy a szabály akkor indul el, amikor az eszköz küld az esemény esemény értékétől függetlenül.

    ![Feltétel hozzáadása](media\howto-create-event-rules\image4.png)

    > [!NOTE]
    > Egy esemény szabályfeltétel meghatározása esetén ki kell választania legalább egy esemény mérését.

### <a name="configure-the-action"></a>A művelet konfigurálása

Ez a szakasz bemutatja, hogyan adhatja meg a szabály működését a feltétel megegyezik egy műveletet.

1. Válassza ki a **+** melletti **műveletek**. Itt láthatja a rendelkezésre álló műveletek listáját. Nyilvános előzetes **E-mail** az egyetlen támogatott művelet.

    ![Művelet hozzáadása](media\howto-create-event-rules\image5.png)

1. Válassza ki a **E-mail** művelet, adjon meg egy érvényes e-mail címet a **való** mezőben, és arra, hogy a szabály gondoskodik az e-mail törzsében jelenik meg.

    > [!NOTE]
    > E-mailek küldése csak a érhetőek el az alkalmazást, és legalább egyszer már bejelentkezett felhasználókat. További információ [felhasználókezelés](howto-administer.md) az Azure IoT-központ.

   ![A művelet konfigurálása](media\howto-create-event-rules\image6.png)

1. A szabály mentéséhez válassza **mentése**. A szabály néhány percen belül élő kerül, és elindítja a figyelést az események küldi el az alkalmazást. A szabályban meghatározott feltétel megegyezik, akkor a szabály a beállított e-mail művelet váltja ki.

## <a name="parameterize-the-rule"></a>A szabály parametrizálja

Műveletek használatával is konfigurálható **Eszköztulajdonságon** paraméterként. Ha egy eszköz tulajdonság tárolja az e-mail címet, majd ha is használható a **való** cím.

## <a name="delete-a-rule"></a>Szabály törlése

Ha már nincs szüksége egy szabályt, törölje azt nyissa meg a szabály és kiválasztása **törlése**. A szabály törlése eltávolítja az eszközt sablon és a kapcsolódó eszközök.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Engedélyezi vagy letiltja az eszköz sablon szabály

Keresse meg az eszközt, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. Elvégezte a **engedélyezése a szabály az összes eszköz a sablon** gombra a szabály engedélyezi vagy letiltja a a szabály az eszköz sablonhoz társított összes eszköz.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Engedélyezheti vagy tilthatja le a szabály egy eszközhöz

Keresse meg az eszközt, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. Váltás a **engedélyezése a szabály az eszköz** gombra kattintva engedélyezheti vagy letilthatja a szabály az eszköznek.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a szabályok létrehozása az Azure IoT központi alkalmazás rendelkezik, ez a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az eszközkezelés módjának](howto-manage-devices.md).