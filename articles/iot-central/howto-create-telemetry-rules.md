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
ms.openlocfilehash: 3ac8e488de11abc3471df836ca852cf3dc85c82f
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877352"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Telemetria-szabály létrehozása és értesítések beállítása az Azure IoT Central alkalmazásban

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Az Azure IoT Central használatával távolról is figyelheti a csatlakoztatott eszközöket. Az Azure IoT Central szabályai lehetővé teszik az eszközök közel valós idejű figyelését és a műveletek automatikus meghívását, például e-mailek vagy triggerek Microsoft Flowának elküldését. Néhány kattintással megadhatja azt a feltételt, amelynél figyelni szeretné az eszköz adatait, és konfigurálnia kell a megfelelő műveletet. Ez a cikk azt ismerteti, hogyan hozhatók létre szabályok az eszköz által eljuttatott telemetria figyeléséhez.

Az eszközök a telemetria-mérés használatával numerikus adatok küldhetők az eszközről. A telemetria szabály akkor aktiválódik, ha a kiválasztott eszköz telemetria átlép egy megadott küszöbértéket.

## <a name="create-a-telemetry-rule"></a>Telemetria-szabály létrehozása

Telemetria szabály létrehozásához az eszköz sablonjának meg kell határoznia legalább egy telemetria-mérést. Ez a példa egy hűtött automatából működő eszközt használ, amely hőmérséklet-és páratartalom-telemetria küld. A szabály figyeli az eszköz által jelentett hőmérsékletet, és e-mailt küld, ha 80 fok fölé kerül.

1. Az **eszközök sablonjai** lapon Navigáljon arra az eszköz-sablonra, amelyhez hozzá kívánja hozzáadni a szabályt.

1. Ha még nem hozott létre szabályokat, a következő képernyő jelenik meg:

    ![Még nincsenek szabályok](media/howto-create-telemetry-rules/rules_landing_page1.png)

1. A **szabályok** lapon válassza az **+ új szabály** lehetőséget a létrehozandó szabályok típusának megtekintéséhez.

1. Válassza a **telemetria** lehetőséget az eszköz telemetria figyelésére szolgáló szabály létrehozásához.

    ![Szabályok típusai](media/howto-create-telemetry-rules/rule_types1.png)

1. Adjon meg egy nevet, amely segít a szabály azonosításában az eszköz sablonjában.

1. A sablonhoz létrehozott összes eszközre vonatkozó szabály azonnali engedélyezéséhez kapcsolja be az **Engedélyezés szabályt a sablonhoz tartozó összes eszközhöz**.

   ![Szabály részletei](media/howto-create-telemetry-rules/rule_detail1.png)

    A szabály automatikusan az eszköz sablonja alatt lévő összes eszközre vonatkozik.

### <a name="configure-the-rule-conditions"></a>A szabály feltételeinek konfigurálása

A feltétel határozza meg a szabály által figyelt feltételeket.

1. Új **+** feltétel hozzáadásához válassza a **feltételek** tovább lehetőséget.

1. Válassza ki a figyelni kívánt telemetria a **mérték** legördülő listából.

1. Ezután válassza az **Összesítés**, az **operátor**lehetőséget, és adja meg a küszöbértéket.
   - Az Összesítés nem kötelező. Összesítés nélkül a szabály minden olyan telemetria-adatponthoz aktiválódik, amely megfelel a feltételnek. Ha például a szabály úgy van konfigurálva, hogy a hőmérséklet értéke 80, akkor a szabály szinte azonnal elindul, amikor az eszköz a > 80 hőmérsékletet jelzi.
   - Ha egy összesítő függvény (például átlag, min, Max, darabszám) van kiválasztva, akkor a felhasználónak meg kell adnia egy **összesített** időablakot, amely fölé a feltételt értékelni kell. Ha például az időszakot "5 perc" értékre állítja, és a szabály a 80-nál nagyobb átlagos hőmérsékletet keres, akkor a szabály akkor aktiválódik, ha az átlaghőmérséklet meghaladja a 80-et legalább 5 percnél. A szabály kiértékelésének gyakorisága megegyezik az **összesített**időintervallummal, ami azt jelenti, hogy ebben a példában a szabály 5 percenként lesz kiértékelve.

     ![Állapot](media/howto-create-telemetry-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >A **feltétel**alatt egynél több telemetria-mérés is hozzáadható. Ha több feltétel van megadva, az összes feltételnek teljesülnie kell a szabály működésének elindításához. Minden conditon implicit módon csatlakozik egy "AND" záradékhoz. Összesítés használatakor minden mérést összesíteni kell.

### <a name="configure-actions"></a>Műveletek konfigurálása

Ebből a szakaszból megtudhatja, hogyan állíthatja be, hogy milyen műveleteket kell végrehajtani a szabály indításakor. A rendszer meghívja a műveleteket, ha a szabályban megadott összes feltétel igaz értékre van kiértékelve.

1. Válassza ki **+** a műveletek melletti **műveletet**. Itt láthatja az elérhető műveletek listáját.  

    ![Művelet hozzáadása](media/howto-create-telemetry-rules/add_action1.png)

1. Válassza ki az **e-mail** műveletet, adjon meg egy érvényes e-mail-címet a **to** mezőben, és adjon meg egy megjegyzést, amely az e-mail törzsében jelenik meg a szabály indításakor.

    > [!NOTE]
    > Az e-maileket csak az alkalmazáshoz hozzáadott felhasználóknak küldi el a rendszer, és legalább egyszer bejelentkezett. További információ az Azure IoT Central [felhasználói kezeléséről](howto-administer.md) .

   ![Művelet konfigurálása](media/howto-create-telemetry-rules/configure_action1.png)

1. A szabály mentéséhez válassza a **Mentés**lehetőséget. A szabály néhány percen belül életbe lép, és megkezdi az alkalmazásnak küldött telemetria figyelését. Ha a szabályban megadott feltétel teljesül, a szabály elindítja a konfigurált e-mail műveletet.

További műveleteket is hozzáadhat a szabályhoz, például a Microsoft Flowhoz és a webhookokhoz. Szabályként legfeljebb 5 műveletet adhat hozzá.

- [Microsoft flow művelet](howto-add-microsoft-flow.md) Microsoft flow munkafolyamat kiindítására egy szabály indításakor 
- [Webhook-művelet](howto-create-webhooks.md) , amely értesíti a többi szolgáltatást, amikor egy szabály aktiválódik

## <a name="parameterize-the-rule"></a>A szabály parametrizálja

A szabályok bizonyos Vales-ket származtatnak az **eszköz tulajdonságaiból** paraméterekként. A paraméterek használata olyan helyzetekben hasznos, ahol a telemetria küszöbértékei eltérőek a különböző eszközökön. A szabály létrehozásakor válasszon egy eszköz tulajdonságot, amely meghatározza a küszöbértéket, például a **maximális ideális küszöbértéket**, abszolút érték, például 80 fok megadása helyett. A szabály végrehajtásakor az megfelel az eszköz tulajdonságban beállított telemetria.

A paraméterek használata hatékony módszer az eszközönkénti sablon kezelésére szolgáló szabályok számának csökkentésére.

A műveletek az **eszköz tulajdonságával** is konfigurálhatók paraméterként. Ha egy e-mail-cím tulajdonságként van tárolva, akkor a cím megadásakor is használható.

## <a name="delete-a-rule"></a>Szabály törlése

Ha már nincs szüksége egy szabályra, törölje azt a szabály megnyitásával, és válassza a **Törlés**lehetőséget. A szabály törlése eltávolítja azt az eszköz sablonból és az összes kapcsolódó eszközről.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Eszközhöz tartozó szabály engedélyezése vagy letiltása

Navigáljon az eszközhöz, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. A szabályban a sablonhoz tartozó **összes eszközön** engedélyezze vagy tiltsa le a szabályt az eszköz sablonhoz társított összes eszköz szabályának engedélyezéséhez vagy letiltásához.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Eszköz szabályának engedélyezése vagy letiltása

Navigáljon az eszközhöz, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. Az eszközhöz tartozó szabály engedélyezéséhez vagy letiltásához kapcsolja be a **szabály engedélyezése** ehhez a gombhoz beállítást.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre szabályokat az Azure IoT Central alkalmazásban, néhány további lépés:

- [Microsoft Flow művelet hozzáadása a szabályokban](howto-add-microsoft-flow.md)
- [Webhook-művelet hozzáadása a szabályokban](howto-create-webhooks.md)
- [Több művelet csoportosítása egy vagy több szabályból való futtatáshoz](howto-use-action-groups.md)
- [Az eszközök kezelése](howto-manage-devices.md)
