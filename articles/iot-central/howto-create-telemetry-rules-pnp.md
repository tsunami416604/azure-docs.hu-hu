---
title: Telemetria-szabályok létrehozása és kezelése az Azure IoT Central alkalmazásban | Microsoft Docs
description: Az Azure IoT Central telemetria szabályai lehetővé teszik az eszközök közel valós idejű figyelését és a műveletek automatikus meghívását, például e-mailek küldését a szabály indításakor.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d6deecf558105701be591c1f08923eca2c1e3bbd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879941"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Telemetria-szabály létrehozása és értesítések beállítása az Azure IoT Central alkalmazásban (előzetes verziójú funkciók)

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Az Azure IoT Central használatával távolról is figyelheti a csatlakoztatott eszközöket. Az Azure IoT Central szabályai lehetővé teszik az eszközök közel valós idejű figyelését és a műveletek automatikus meghívását, például e-mailek vagy triggerek Microsoft Flowának elküldését. Néhány kattintással megadhatja azt a feltételt, amelynél figyelni szeretné az eszköz adatait, és konfigurálnia kell a megfelelő műveletet. Ez a cikk azt ismerteti, hogyan hozhatók létre szabályok az eszköz által eljuttatott telemetria figyeléséhez.

Az eszközök a telemetria-mérés használatával numerikus adatok küldhetők az eszközről. A telemetria szabály akkor aktiválódik, ha a kiválasztott eszköz telemetria átlép egy megadott küszöbértéket.

## <a name="create-a-telemetry-rule"></a>Telemetria-szabály létrehozása

Telemetria szabály létrehozásához az eszköz definíciójában meg kell adni legalább egy telemetria-mérést. Ez a példa egy hűtött automatából működő eszközt használ, amely hőmérséklet-és páratartalom-telemetria küld. A szabály figyeli az eszköz által jelentett hőmérsékletet, és e-mailt küld, ha 80 fok fölé kerül.

1. Navigáljon a **szabályok** lapra.

1. Ha még nem hozott létre szabályokat, a következő képernyő jelenik meg:

    ![Még nincsenek szabályok](media/howto-create-telemetry-rules-pnp/rules-landing-page1.png)

1. Válassza az **+ új szabály** lehetőséget a létrehozandó szabályok típusának megtekintéséhez.

1. Válassza a **telemetria** lehetőséget az eszköz telemetria figyelésére szolgáló szabály létrehozásához.

    ![Szabályok típusai](media/howto-create-telemetry-rules-pnp/rule-types1.png)


1. Adjon meg egy nevet, amely segítséget nyújt a szabály azonosításában és az ENTER billentyű leütésében.

1. Válassza ki azt az eszköz-definíciót, amelyre ezt a szabályt a hatókör szakaszban szeretné használni. Ezen a képernyőn is kiszűrheti azokat az eszközöket, amelyekre a szabály vonatkozik a **+ szűrő**használatával. A szabály automatikusan az eszköz sablonja alatt lévő összes eszközre vonatkozik. A szabály letiltásához a fejlécben kattintson a **Letiltás** gombra.

### <a name="configure-the-rule-conditions"></a>A szabály feltételeinek konfigurálása

A feltétel határozza meg a szabály által figyelt feltételeket.

1. Válassza ki, hogy be vagy ki szeretné-e **állítani** az összesítést.

      - Az Összesítés nem kötelező. Összesítés nélkül a szabály minden olyan telemetria-adatponthoz aktiválódik, amely megfelel a feltételnek. Ha például a szabály úgy van konfigurálva, hogy a hőmérséklet értéke 80, akkor a szabály szinte azonnal elindul, amikor az eszköz a > 80 hőmérsékletet jelzi.
      - Ha egy összesítő függvény (például átlag, min, Max, Count) van kiválasztva, akkor a felhasználónak meg kell adnia egy időablakot, amelyen a feltétel kiértékelése szükséges. Ha például az időszakot "5 perc" értékre állítja, és a szabály a 80-nál nagyobb átlagos hőmérsékletet keres, akkor a szabály akkor aktiválódik, ha az átlaghőmérséklet meghaladja a 80-et legalább 5 percnél. A szabály kiértékelésének gyakorisága megegyezik azidőablaktal, ami azt jelenti, hogy ebben a példában a szabály 5 percenként lesz kiértékelve.

1. Válassza ki a figyelni kívánt telemetria a **mérték** legördülő listából.

1. Ezután válasszon egy **operátort** , és adjon meg egy **értéket**.

     ![Állapot](media/howto-create-telemetry-rules-pnp/aggregate-condition-filled-out1.png)


>[!NOTE]
>A **+ feltétel**kiválasztásával több telemetria-mérés is hozzáadható. Ha több feltétel van megadva, az összes feltételnek teljesülnie kell a szabály működésének elindításához. Minden feltétel implicit módon csatlakozik egy "és" záradékhoz. Összesítés használatakor minden mérést összesíteni kell.

### <a name="configure-actions"></a>Műveletek konfigurálása

Ebből a szakaszból megtudhatja, hogyan állíthatja be, hogy milyen műveleteket kell végrehajtani a szabály indításakor. A rendszer meghívja a műveleteket, ha a szabályban megadott összes feltétel igaz értékre van kiértékelve.

1. Kattintson a **művelet** szakaszban a **+ művelet** elemre. Itt láthatja az elérhető műveletek listáját.  

    ![Művelet hozzáadása](media/howto-create-telemetry-rules-pnp/add-action1.png)


1. Válassza ki az **e-mail** műveletet, adjon meg egy megjelenítendő nevet a műveletnek, egy érvényes e-mail-címet a **to** mezőben, és adjon meg egy megjegyzést, amely az e-mail törzsében jelenik meg, amikor a szabály eseményindítót tartalmaz.

    > [!NOTE]
    > Az e-maileket csak az alkalmazáshoz hozzáadott felhasználóknak küldi el a rendszer, és legalább egyszer bejelentkezett. További információ az Azure IoT Central [felhasználói kezeléséről](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

   ![Művelet konfigurálása](media/howto-create-telemetry-rules-pnp/configure-action1.png)


1. A művelet mentéséhez válassza a **kész**lehetőséget.

1. A szabály mentéséhez válassza a **Mentés**lehetőséget. A szabály néhány percen belül életbe lép, és megkezdi az alkalmazásnak küldött telemetria figyelését. Ha a szabályban megadott feltétel teljesül, a szabály elindítja a konfigurált e-mail műveletet.

## <a name="parameterize-the-rule"></a>A szabály parametrizálja

A szabályok bizonyos Vales-ket származtatnak az **eszköz tulajdonságaiból** paraméterekként. A paraméterek használata olyan helyzetekben hasznos, ahol a telemetria küszöbértékei eltérőek a különböző eszközökön. A szabály létrehozásakor válasszon egy eszköz tulajdonságot, amely meghatározza a küszöbértéket, például az **ideális küszöbértéket**, abszolút érték megadása helyett, például 80 fok. A szabály végrehajtásakor az megfelel az eszköz tulajdonságban beállított telemetria.

A paraméterek használata hatékony módszer a felügyelt szabályok számának csökkentésére.

A műveletek az **eszköz tulajdonságával** is konfigurálhatók paraméterként. Ha egy e-mail-cím tulajdonságként van tárolva, akkor a cím megadásakor is használható.

## <a name="delete-a-rule"></a>Szabály törlése

Ha már nincs szüksége egy szabályra, törölje azt a szabály megnyitásával, és válassza a **Törlés**lehetőséget. A szabály törlése eltávolítja azt az eszköz sablonból és az összes kapcsolódó eszközről.

## <a name="enable-or-disable-a-rule"></a>Szabály engedélyezése vagy letiltása

Válassza ki az engedélyezni vagy letiltani kívánt szabályt. A szabályban szereplő összes eszközhöz tartozó szabály engedélyezéséhez vagy letiltásához kapcsolja be a szabály **Engedélyezés** vagy **Letiltás** gombját.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Eszköz szabályának engedélyezése vagy letiltása

Válassza ki az engedélyezni vagy letiltani kívánt szabályt. Vegyen fel egy szűrőt a **hatókörök** szakaszban egy adott eszköz belefoglalásához vagy kizárásához az eszköz sablonjában.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre szabályokat az Azure IoT Central alkalmazásban, a javasolt következő lépés az [eszközök kezelésének](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) megismerése
