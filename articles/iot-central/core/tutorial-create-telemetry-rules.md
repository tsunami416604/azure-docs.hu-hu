---
title: Oktatóanyag – Szabályok létrehozása és kezelése az Azure IoT Central alkalmazásban
description: Ez az oktatóanyag bemutatja, hogy az Azure IoT Central-szabályok hogyan teszik lehetővé az eszközök közel valós idejű figyelését, és automatikusan meghívja a műveleteket, például egy e-mailt, amikor a szabály aktiválódik.
author: dominicbetts
ms.author: dobett
ms.date: 04/06/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 555da74da65f3b1897a276cf819a263334cfa053
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80999061"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Oktatóanyag: Hozzon létre egy szabályt, és állítson be értesítéseket az Azure IoT Central alkalmazásban

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Az Azure IoT Central segítségével távolról figyelheti a csatlakoztatott eszközöket. Az Azure IoT Central-szabályok lehetővé teszik, hogy közel valós időben figyelje az eszközöket, és automatikusan meghívja a műveleteket, például egy e-mailt. Ez a cikk bemutatja, hogyan hozhat létre szabályokat az eszközök által küldött telemetriai adatok figyelésére.

Az eszközök telemetriai adatokat használnak az eszközről történő numerikus adatok küldéséhez. A szabály akkor aktiválódik, amikor a kijelölt eszköz telemetriai átlépi a megadott küszöbértéket.

Ebben az oktatóanyagban hozzon létre egy szabályt, amely e-mailt küld,&deg; ha a szimulált környezeti érzékelő eszköz hőmérséklete meghaladja a 70 F-ot.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Szabály létrehozása
> * E-mail művelet hozzáadása

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, töltse ki az [Azure IoT Central alkalmazás létrehozása](./quick-deploy-iot-central.md) és egy [szimulált eszköz hozzáadása az IoT Central alkalmazás](./quick-create-simulated-device.md) rövid útmutatóihoz az **MXChip IoT DevKit** eszközsablon létrehozásához.

## <a name="create-a-rule"></a>Szabály létrehozása

Telemetriai szabály létrehozásához az eszközsablonnak legalább egy telemetriai értéket tartalmaznia kell. Ez az oktatóanyag egy szimulált **MXChip IoT DevKit-eszközt** használ, amely hőmérséklet- és páratartalom-telemetriát küld. Hozzáadta ezt az eszközsablont, és létrehozott egy szimulált eszközt a [Szimulált eszköz hozzáadása az IoT Central alkalmazás](./quick-create-simulated-device.md) rövid útmutatójában. A szabály figyeli az eszköz által jelentett hőmérsékletet, és e-mailt küld, ha 70 fok fölé megy.

1. A bal oldali ablaktáblában válassza a **Szabályok**lehetőséget.

1. Ha még nem hozott létre szabályokat, a következő képernyő jelenik meg:

    ![Még nincsenek szabályok](media/tutorial-create-telemetry-rules/rules-landing-page1.png)

1. Új **+** szabály hozzáadásához jelölje be.

1. Írja be a _Hőmérséklet figyelő_ nevét a szabály azonosításához, és nyomja le az Enter billentyűt.

1. Válassza ki az **MXChip IoT DevKit** eszközsablont. Alapértelmezés szerint a szabály automatikusan vonatkozik az eszközsablonhoz társított összes eszközre. Az eszközök egy részhalmazának szűréséhez válassza a **+ Szűrő** lehetőséget, és az eszközök tulajdonságainak használatával azonosíthatja az eszközöket. A szabály letiltásához kapcsolja be az **Engedélyezve/Letiltva** gombot a szabály fejlécében:

    ![Szűrők és engedélyezés](media/tutorial-create-telemetry-rules/device-filters.png)

### <a name="configure-the-rule-conditions"></a>A szabályfeltételek konfigurálása

A feltételek határozzák meg a szabály által figyelt feltételeket. Ebben az oktatóanyagban beállíthatja, hogy a szabály&deg; akkor jelenjen meg, ha a hőmérséklet meghaladja a 70 F-ot.

1. Válassza ki a **Hőmérséklet** a **Telemetriai** legördülő kiválasztásában.

1. Ezután válassza **a Nagyobb, mint** az **Operátor** lehetőséget, és írja be a _70_ **értéket értékként.**

    ![Állapot](media/tutorial-create-telemetry-rules/condition-filled-out1.png)

1. Szükség esetén beállíthatja **az Időösszesítést.** Időösszesítés kiválasztásakor ki kell választania egy összesítési típust is, például átlagot vagy összeget az összesítés legördülő legördülő menüből.

    * Összesítés nélkül a szabály minden olyan telemetriai adatponthoz aktiválódik, amely megfelel a feltételnek. Ha például úgy konfigurálja a szabályt, hogy 70 feletti hőmérséklet esetén aktiválódjon, akkor a szabály szinte azonnal aktiválódik, ha az eszköz hőmérséklete meghaladja ezt az értéket.
    * Összesítéssel a szabály akkor aktiválódik, ha a telemetriai adatpontok összesített értéke az időablakban megfelel a feltételnek. Ha például úgy konfigurálja a szabályt, hogy 70 feletti hőmérséklet esetén aktiválódjon, és átlagosan 10 perces összesítéssel, akkor a szabály akkor aktiválódik, amikor az eszköz 70-nél nagyobb átlaghőmérsékletet jelent, amelyet 10 perces intervallumban számítanak ki.

     ![Összesített feltétel](media/tutorial-create-telemetry-rules/aggregate-condition-filled-out1.png)

A **szabályhoz**több feltételt is hozzáadhat a + Feltétel lehetőség kiválasztásával. Ha több feltétel van megadva, a szabály aktiválásához minden feltételnek teljesülnie kell. Minden feltételhez implicit `AND` záradék kapcsolódik. Ha több feltételsel használja az időösszesítést, az összes telemetriai értéket összesíteni kell.

### <a name="configure-actions"></a>Műveletek konfigurálása

Miután definiálta a feltételt, beállítja a szabály kilépéseként végrehajtandó műveleteket. A műveletek meghívása akkor lesz meghívva, ha a szabályban megadott összes feltétel igaz értéket ad ki.

1. Válassza a **+ E-mail lehetőséget** a **Műveletek** szakaszban.

1. Adja meg _a Hőmérséklet figyelmeztetés,_ mint a megjelenítendő nevét a művelet, az e-mail címét a **Címzett** mezőbe, és _ellenőriznie kell a készüléket!_ az e-mail törzsében megjelenő megjegyzésként.

    > [!NOTE]
    > A rendszer csak azoknak a felhasználóknak küldi el az e-maileket, akik et hozzáadtak az alkalmazáshoz, és legalább egyszer bejelentkeztek. További információ az Azure IoT Central [felhasználói felügyeletéről.](howto-administer.md)

   ![Művelet konfigurálása](media/tutorial-create-telemetry-rules/configure-action1.png)

1. A művelet mentéséhez válassza a **Kész gombot.** Egy szabályhoz több műveletet is hozzáadhat.

1. A szabály mentéséhez válassza a **Mentés gombot.** A szabály néhány percen belül élesben elindul, és megkezdi az alkalmazásnak küldött telemetriai adatok figyelését. Ha a szabályban megadott feltétel teljesül, a szabály elindítja a beállított e-mail műveletet.

Egy idő után e-mail ben kap egy e-mailt, amikor a szabály kigyullad:

![Példa e-mailre](media/tutorial-create-telemetry-rules/email.png)

## <a name="delete-a-rule"></a>Szabály törlése

Ha már nincs szüksége szabályra, törölje azt a szabály megnyitásával és a **Törlés gombra.**

## <a name="enable-or-disable-a-rule"></a>Szabály engedélyezése vagy letiltása

Válassza ki az engedélyezni vagy letiltani kívánt szabályt. A szabály **Engedélyezve/letiltva** gombjának váltása a szabályban lévő összes hatókörre vonatkozó szabály engedélyezéséhez vagy letiltásához.

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>Szabály engedélyezése vagy letiltása adott eszközökön

Válassza ki a testre szabni kívánt szabályt. A **Céleszközök** szakaszban egy vagy több szűrővel szűkítheti a szabály hatókörét a figyelni kívánt eszközökre.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Telemetria-alapú szabály létrehozása
* Művelet hozzáadása

Most, hogy definiált egy küszöbértéken alapuló szabályt, a javasolt következő lépés a következő lépés:

> [!div class="nextstepaction"]
> [Folyamatos adatexportálás konfigurálása](./howto-export-data.md).
