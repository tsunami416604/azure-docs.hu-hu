---
title: Oktatóanyag – szabályok létrehozása és kezelése az Azure IoT Central-alkalmazásban
description: Ez az oktatóanyag azt mutatja be, hogy az Azure IoT Central-szabályok hogyan teszik lehetővé az eszközök közel valós idejű figyelését és a műveletek automatikus meghívását, például e-mailek küldését a szabály indításakor.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: e2ec01e372ebda79272b585ea6f1708029ea7b13
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702546"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Oktatóanyag: szabály létrehozása és értesítések beállítása az Azure IoT Central alkalmazásban (előzetes verziójú funkciók)

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Az Azure IoT Central használatával távolról is figyelheti a csatlakoztatott eszközöket. Az Azure IoT Central szabályai lehetővé teszik az eszközök közel valós idejű figyelését és a műveletek automatikus meghívását, például e-mailek küldését. Néhány kattintással megadhat egy feltételt, amely figyeli a telemetria az eszközökről, és konfigurál egy megfelelő műveletet. Ez a cikk azt ismerteti, hogyan hozhatók létre szabályok az eszköz által eljuttatott telemetria figyeléséhez.

Az eszközök a telemetria használatával numerikus adatok küldését az eszközről. Egy szabály akkor aktiválódik, ha a kiválasztott eszköz telemetria átlép egy megadott küszöbértéket.

Ebben az oktatóanyagban létrehoz egy szabályt, amely e-mailt küld, ha a környezeti érzékelő eszköz hőmérséklete meghaladja a 80&deg; F.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Szabály létrehozása
> * E-mail művelet hozzáadása

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, hajtsa végre az [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md) és [egy szimulált eszköz hozzáadása a IoT Central alkalmazás](./quick-create-pnp-device.md) -gyors útmutatóhoz a **környezeti érzékelő** eszköz sablonjának létrehozásához.

## <a name="create-a-rule"></a>Szabály létrehozása

Telemetria szabály létrehozásához az eszköz sablonjának meg kell határoznia legalább egy telemetria-mérést. Ez az oktatóanyag egy környezeti érzékelőt használó eszközt használ, amely hőmérséklet-és páratartalom-telemetria küld. Hozzáadta ezt az eszközt, és létrehozott egy szimulált eszközt a [szimulált eszköz hozzáadása a IoT Central alkalmazáshoz](./quick-create-pnp-device.md) című rövid útmutatóban. A szabály figyeli az eszköz által jelentett hőmérsékletet, és e-mailt küld, ha 80 fok fölé kerül.

1. A bal oldali ablaktáblán válassza a **szabályok**lehetőséget.

1. Ha még nem hozott létre szabályokat, a következő képernyő jelenik meg:

    ![Még nincsenek szabályok](media/tutorial-create-telemetry-rules/rules-landing-page1.png)

1. Új szabály hozzáadásához válassza a **+** lehetőséget.

1. A szabály azonosításához adja meg a name _hőmérséklet-figyelőt_ , majd nyomja le az ENTER billentyűt.

1. Válassza ki a **környezeti érzékelő** eszköz sablonját. Alapértelmezés szerint a szabály automatikusan az eszköz sablonhoz társított összes eszközre vonatkozik. Az eszközök egy részhalmazának szűréséhez válassza a **+ szűrés** lehetőséget, és használja az eszköz tulajdonságait az eszközök azonosításához. A szabály letiltásához állítsa be az **engedélyezett/letiltott** gombot a szabály fejlécében:

    ![Szűrők és engedélyezés](media/tutorial-create-telemetry-rules/device-filters.png)

### <a name="configure-the-rule-conditions"></a>A szabály feltételeinek konfigurálása

A feltételek határozzák meg a szabály által figyelt feltételeket. Ebben az oktatóanyagban úgy konfigurálja a szabályt, hogy a hőmérséklet meghaladja a 80&deg; F értéknél nagyobb hőmérsékletet.

1. Válassza a **hőmérséklet** lehetőséget a **telemetria** legördülő menüben.

1. Ezután válassza a **nagyobb, mint** a **kezelőt** , és adja meg a _80_ **értéket**.

    ![Állapot](media/tutorial-create-telemetry-rules/condition-filled-out1.png)

1. Igény szerint **időösszesítést**is beállíthat. Amikor kiválaszt egy időösszesítést, ki kell választania egy összesítési típust is, például az összesítési legördülő listából az átlagot vagy az összeget.

    * Összesítés nélkül a szabály minden olyan telemetria-adatponthoz aktiválódik, amely megfelel a feltételnek. Ha például a szabály úgy van konfigurálva, hogy a hőmérséklet értéke 80, akkor a szabály szinte azonnal elindul, amikor az eszköz a > 80 hőmérsékletet jelzi.
    * Az összesítéssel a szabály akkor aktiválódik, ha az időablakban található telemetria-adatpontok összesített értéke megfelel a feltételnek. Ha például a szabály úgy van beállítva, hogy ha a hőmérséklet meghaladja a 80-as hőmérsékletet, az időösszesítés 10 percre van beállítva, és az összesítés típusa átlag, akkor a szabály akkor aktiválódik, ha az eszköz átlagos hőmérsékletet > 80, amely 10 percen belül van kiszámítva időköz.

     ![Összesítési feltétel](media/tutorial-create-telemetry-rules/aggregate-condition-filled-out1.png)

A **+ feltétel**lehetőség kiválasztásával több feltételt is hozzáadhat egy szabályhoz. Ha több feltétel van megadva, az összes feltételnek teljesülnie kell a szabály működésének elindításához. Minden feltétel egy implicit `AND` záradékhoz van csatlakoztatva. Ha több feltételt használó időösszesítést használ, a telemetria összes értékét összesíteni kell.

### <a name="configure-actions"></a>Műveletek konfigurálása

Miután definiálta a feltételt, beállíthatja, hogy a szabály milyen műveleteket végezhet el. A rendszer meghívja a műveleteket, ha a szabályban megadott összes feltétel igaz értékre van kiértékelve. Jelenleg az egyetlen elérhető művelet a levelezés.

1. Válassza a **+ e-mail** lehetőséget a **műveletek** szakaszban.

1. Adja meg a _hőmérsékleti figyelmeztetést_ a művelet megjelenítendő neveként, az e-mail-címét a **to** mezőben, és _tekintse meg az eszközt!_ az e-mailek törzsében megjelenő Megjegyzés.

    > [!NOTE]
    > Az e-maileket csak az alkalmazáshoz hozzáadott felhasználóknak küldi el a rendszer, és legalább egyszer bejelentkezett. További információ az Azure IoT Central [felhasználói kezeléséről](howto-administer.md) .

   ![Művelet konfigurálása](media/tutorial-create-telemetry-rules/configure-action1.png)

1. A művelet mentéséhez válassza a **kész**lehetőséget. Egy szabályhoz több műveletet is hozzáadhat.

1. A szabály mentéséhez válassza a **Mentés**lehetőséget. A szabály néhány percen belül életbe lép, és megkezdi az alkalmazásnak küldött telemetria figyelését. Ha a szabályban megadott feltétel teljesül, a szabály elindítja a konfigurált e-mail műveletet.

Egy idő után egy e-mail-üzenet jelenik meg, amikor a szabály a következőt jeleníti meg:

![Példa e-mailre](media/tutorial-create-telemetry-rules/email.png)

## <a name="delete-a-rule"></a>Szabály törlése

Ha már nincs szüksége egy szabályra, törölje azt a szabály megnyitásával, és válassza a **Törlés**lehetőséget.

## <a name="enable-or-disable-a-rule"></a>Szabály engedélyezése vagy letiltása

Válassza ki az engedélyezni vagy letiltani kívánt szabályt. A szabályban szereplő összes eszközhöz tartozó szabály engedélyezéséhez vagy letiltásához kapcsolja be a szabály **Engedélyezés** vagy **Letiltás** gombját.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Eszköz szabályának engedélyezése vagy letiltása

Válassza ki az engedélyezni vagy letiltani kívánt szabályt. Vegyen fel egy szűrőt a **hatókörök** szakaszban egy adott eszköz belefoglalásához vagy kizárásához az eszköz sablonjában.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Telemetria-alapú szabály létrehozása
* Művelet hozzáadása

Most, hogy meghatározta a küszöbérték-alapú szabályt, a javasolt következő lépés az alábbiak megismerése:

> [!div class="nextstepaction"]
> [Folyamatos adatexportálás konfigurálása](./howto-export-data.md).
