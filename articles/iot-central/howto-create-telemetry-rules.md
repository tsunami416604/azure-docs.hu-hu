---
title: Hozzon létre, és az Azure IoT központi alkalmazásban telemetriai szabályok kezelése |} Microsoft Docs
description: Az Azure IoT központi telemetriai szabályok lehetővé teszik az eszközök közel valós idejű figyelését, és automatikusan meghívni a műveletek, például egy e-mailek küldéséhez, amikor a szabály gondoskodik.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: caca4e9db898b3766995fde8c5eebd4767abd85b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629813"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Telemetria szabály létrehozása és az Azure IoT központi alkalmazásban értesítések beállítása

A Microsoft Azure IoT központi használatával távolról figyelheti a csatlakoztatott eszközön. Az Azure IoT központi szabályok lehetővé teszik az eszközök közel valós idejű figyelését, és automatikusan meghívni a műveletek, például egy e-mailek küldéséhez, amikor a szabály akkor váltja ki. Néhány kattintással segítségével megadhatja az állapotot, amelyet az eszköz adatok megfigyelheti és konfigurálhatja a művelet meghívásához. Ez a cikk ismerteti részletesen telemetriai szabály.

Használja az Azure IoT központi [telemetriai mérések](howto-set-up-template.md) eszköz adatok rögzítéséhez. Minden mérési, amelyek meghatározzák a mérési kulcsattribútummal rendelkezik. Minden eszköz mérési típusú figyelése, és elindítja a szabály riasztást generáljon szabályokat hozhat létre. A telemetriai adatok szabály váltja ki, ha a kijelölt telemetriát keverve használ a megadott küszöbértéket.

## <a name="create-a-telemetry-rule"></a>Telemetria szabály létrehozása

Ez a szakasz bemutatja, hogyan telemetriai szabály létrehozásához. A példa egy csatlakoztatott légkondicionálók eszköz által a hőmérséklet és a páratartalom telemetriai adatokat. A szabály az eszköz által jelentett hőmérséklete figyeli, és egy e-mailt küld, ha a fenti 80 fok kerül.

1. Az eszköz hozzáadásakor a szabályt, hogy az eszköz részleteit megjelenítő oldalra léphet.

1. Ha még nincsenek szabályok, a következő képernyő jelenik meg:

    ![Még nincsenek szabályok](media\howto-create-telemetry-rules\image1.png)

1. Az a **szabályok** lapra, majd **+ új szabály** hozhat létre szabályokat típusú.

    ![Szabályok típusai](media\howto-create-telemetry-rules\image2.png)

1. Válassza ki a **Telemetriai** csempére kattintva nyissa meg a képernyőn, a szabály létrehozásához.

    ![Telemetria szabály](media\howto-create-telemetry-rules\image3.png)

1. Válassza ki azokat a szabály az eszköz sablon neve.

1. Azonnal engedélyezése a szabály a sablon alapján létrehozott összes eszközt, váltás **engedélyezése a szabály**.

### <a name="configure-the-rule-condition"></a>A szabály feltételének konfigurálása

Ez a szakasz bemutatja, hogyan hőmérséklet telemetriai adatok figyeléséhez feltétel hozzáadásához.

1. Válassza ki a **+** melletti **feltétel**.

1. Válassza ki a **hőmérséklet** telemetriai típusát a legördülő listából. Ezután válassza ki azt az operátort, és adjon meg egy küszöbértéket. Telemetriai adatok több feltétel is hozzáadhat. Ha több feltétel meg van adva, a feltételeknek teljesülniük kell elindítani a szabályhoz.

    ![Telemetria feltétel hozzáadása](media\howto-create-telemetry-rules\image4.png)

    > [!NOTE]
    > Válassza ki legalább egy telemetriai mérési, ha egy telemetriai szabályfeltétel meghatározása.

### <a name="configure-the-action"></a>A művelet konfigurálása

Ez a szakasz bemutatja, hogyan adhatja meg a szabály működését a feltétel megegyezik egy műveletet.

1. Válassza ki a **+** melletti **műveletek**. Itt láthatja a rendelkezésre álló műveletek listáját. Nyilvános előzetes **E-mail** az egyetlen támogatott művelet.

    ![Művelet hozzáadása](media\howto-create-telemetry-rules\image5.png)

1. Válassza ki a **E-mail** művelet, adjon meg egy érvényes e-mail címet a **való** mezőben, és arra, hogy a szabály gondoskodik az e-mail törzsében jelenik meg.

    > [!NOTE]
    > E-mailek küldése csak a érhetőek el az alkalmazást, és legalább egyszer már bejelentkezett felhasználókat. További információ [felhasználókezelés](howto-administer.md) az Azure IoT-központ.

   ![A művelet konfigurálása](media\howto-create-telemetry-rules\image6.png)

1. A szabály mentéséhez válassza **mentése**. A szabály néhány percen belül élő kerül, és elindítja a telemetriai adatokat küldi el az alkalmazást a figyelést. A szabályban meghatározott feltétel megegyezik, akkor a szabály a beállított e-mail művelet váltja ki.

## <a name="parameterize-the-rule"></a>A szabály parametrizálja

Szabályok származtathatók bizonyos vales a **eszköztulajdonságok** paraméterekként. Paraméterek használata akkor hasznos, ahol telemetriai küszöbértékek különböző eszközökön eltérő forgatókönyvekben. A szabály létrehozásakor válassza ki, mint a küszöbérték meghatározó eszköz tulajdonság **maximális ideális küszöbérték**, abszolút értéke, például a 80 fok megadása helyett. A szabály végrehajtásakor a telemetriát megegyezik az eszköz tulajdonságában megadott érték.

Paraméterek használata az eszköz sablon / kezeléséhez szabályok számának csökkentése hatékony módot.

Műveletek használatával is konfigurálható **Eszköztulajdonságon** paraméterként. Ha egy eszköz tulajdonság tárolja az e-mail címet, majd ha is használható a **való** cím.

## <a name="delete-a-rule"></a>Szabály törlése

Ha már nincs szüksége egy szabályt, törölje azt nyissa meg a szabály és kiválasztása **törlése**. A szabály törlése eltávolítja az eszközt sablon és a kapcsolódó eszközök.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Engedélyezi vagy letiltja az eszköz sablon szabály

Keresse meg az eszközt, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. Elvégezte a **engedélyezése a szabály az összes eszköz a sablon** gombra a szabály engedélyezi vagy letiltja a a szabály az eszköz sablonhoz társított összes eszköz.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Engedélyezheti vagy tilthatja le a szabály egy eszközhöz

Keresse meg az eszközt, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. Váltás a **engedélyezése a szabály az eszköz** gombra kattintva engedélyezheti vagy letilthatja a szabály az eszköznek.

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megismerte az Azure IoT központi alkalmazásban szabályok szerkesztése, ez a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az eszközkezelés módjának](howto-manage-devices.md).