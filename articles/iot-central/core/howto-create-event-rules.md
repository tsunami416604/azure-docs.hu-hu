---
title: Eseményvezérelt szabályok létrehozása és kezelése az Azure IoT Central alkalmazásban | Microsoft Docs
description: Az Azure IoT Central eseményvezérelt szabályai lehetővé teszik az eszközök közel valós idejű figyelését és a műveletek automatikus meghívását, például e-mailek küldését a szabály indításakor.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: f932cb9621e4cfc62e2bb00d11030a7ff03450fc
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954018"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Eseményvezérelt szabály létrehozása és értesítések beállítása az Azure IoT Central alkalmazásban

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Az Azure IoT Central használatával távolról is figyelheti a csatlakoztatott eszközöket. Az Azure IoT Central szabályai lehetővé teszik az eszközök közel valós idejű figyelését és a műveletek automatikus meghívását, például e-mailek vagy triggerek Microsoft Flowának elküldését. Néhány kattintással megadhatja azt a feltételt, amelynél figyelni szeretné az eszköz adatait, és konfigurálnia kell a megfelelő műveletet. Ez a cikk azt ismerteti, hogyan hozhatók létre szabályok az eszköz által eljuttatott események figyelésére.

Az eszközök az esemény mérésével fontos vagy tájékoztató jellegű eseményeket küldhetnek. Az eseményvezérelt szabály akkor aktiválódik, ha az eszköz a kiválasztott eszköz eseményét jelzi.

## <a name="create-an-event-rule"></a>Eseményszabály létrehozása

Eseményvezérelt szabály létrehozásához az eszköz sablonjának meg kell határoznia legalább egy esemény mértékét. Ez a példa egy olyan hűtött automatából készült eszközt használ, amely egy ventilátoros motorral kapcsolatos hibát jelez. A szabály figyeli az eszköz által jelentett eseményt, és e-mailt küld az esemény jelentésekor.

1. Az **eszközök sablonjai** lapon Navigáljon arra az eszköz-sablonra, amelyhez hozzá kívánja hozzáadni a szabályt.

1. Ha még nem hozott létre szabályokat, a következő képernyő jelenik meg:

    ![Még nincsenek szabályok](media/howto-create-event-rules/rules_landing_page1.png)

1. A **szabályok** lapon válassza az **+ új szabály** lehetőséget a létrehozandó szabályok típusának megtekintéséhez.

1. Válassza ki az **esemény** csempét egy esemény-figyelési szabály létrehozásához.

    ![Szabályok típusai](media/howto-create-event-rules/rule_types1.png)

1. Adjon meg egy nevet, amely segít a szabály azonosításában az eszköz sablonjában.

1. A sablonból létrehozott összes eszközre vonatkozó szabály azonnali engedélyezéséhez kapcsolja be az **Engedélyezés szabályt a sablon összes eszközén**.

    ![Szabály részletei](media/howto-create-event-rules/rule_detail1.png)

    A szabály automatikusan az eszköz sablonja alatt lévő összes eszközre vonatkozik.

### <a name="configure-the-rule-conditions"></a>A szabály feltételeinek konfigurálása

A feltétel határozza meg a szabály által figyelt feltételeket.

1. Új feltétel hozzáadásához válassza a **feltételek** melletti **+** .

1. Válassza ki a figyelni kívánt eseményt a mérték legördülő listából. Ebben a példában a **ventilátor motoros hiba** esemény van kiválasztva.

   ![Állapot](media/howto-create-event-rules/condition_filled_out1.png)

1. Igény szerint a **Count** **összesítésként** is beállítható, és megadhatja a vonatkozó küszöbértéket.

   - Összesítés nélkül a szabály a feltételnek megfelelő összes esemény-adatpontra elindítja a szabályt. Ha például úgy konfigurálja a szabály feltételét, hogy a **ventilátoros motor hibájának** bekövetkezésekor aktiválódik, a szabály szinte azonnal elindul, amikor az eszköz jelentést küld az eseményről.
   - Ha a Count összesítő függvényként van használatban, meg kell adnia egy **küszöbértéket** és egy **összesített időablakot** , amely felett a feltételt ki kell értékelni. Ebben az esetben a rendszer összesíti az események számát, és a szabály csak akkor aktiválódik, ha az összesített események száma megegyezik a küszöbértékkel.

     Ha például akkor szeretne riasztást kapni, ha több mint három eszköz-esemény van 5 percen belül, válassza ki az eseményt, és állítsa az összesítő függvényt "Count", operátorként "nagyobb, mint", a "küszöbérték" pedig a 3 értékre. Állítsa be az "összesítési időszak" kifejezést "5 perc" értékre. A szabály akkor aktiválódik, ha az eszköz több mint három eseményt továbbít 5 percen belül. A szabály kiértékelésének gyakorisága megegyezik az **összesített időintervallummal**, ami azt jelenti, hogy ebben a példában a szabály 5 percenként lesz kiértékelve.

     ![Esemény feltételének hozzáadása](media/howto-create-event-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >A **feltétel**több esemény-mérést is hozzáadhat. Ha több feltétel van megadva, az összes feltételnek teljesülnie kell a szabály működésének elindításához. Minden feltétel implicit módon csatlakozik egy "és" záradékhoz. Összesítés használatakor minden mérést összesíteni kell.

### <a name="configure-actions"></a>Műveletek konfigurálása

Ebből a szakaszból megtudhatja, hogyan állíthatja be, hogy milyen műveleteket kell végrehajtani a szabály indításakor. A rendszer meghívja a műveleteket, ha a szabályban megadott összes feltétel igaz értékre van kiértékelve.

1. Válassza ki a **műveletek**melletti **+** . Itt láthatja az elérhető műveletek listáját.

    ![Művelet hozzáadása](media/howto-create-event-rules/add_action1.png)

1. Válassza ki az **e-mail** műveletet, adjon meg egy érvényes e-mail-címet a **to** mezőben, és adjon meg egy megjegyzést, amely az e-mail törzsében jelenik meg a szabály indításakor.

    > [!NOTE]
    > Az e-maileket csak az alkalmazáshoz hozzáadott felhasználóknak küldi el a rendszer, és legalább egyszer bejelentkezett. További információ az Azure IoT Central [felhasználói kezeléséről](howto-administer.md) .

   ![Művelet konfigurálása](media/howto-create-event-rules/configure_action1.png)

1. A szabály mentéséhez válassza a **Mentés**lehetőséget. A szabály néhány percen belül életbe lép, és megkezdi az alkalmazásnak küldött események figyelését. Ha a szabályban megadott feltétel megegyezik, a szabály elindítja a konfigurált e-mail műveletet.

További műveleteket is hozzáadhat a szabályhoz, például a Microsoft Flowhoz és a webhookokhoz. Szabályként legfeljebb 5 műveletet adhat hozzá.

- [Microsoft flow művelet](howto-add-microsoft-flow.md) Microsoft flow munkafolyamat kiindítására egy szabály indításakor 
- [Webhook-művelet](howto-create-webhooks.md) , amely értesíti a többi szolgáltatást, amikor egy szabály aktiválódik

## <a name="parameterize-the-rule"></a>A szabály parametrizálja

A műveletek az **eszköz tulajdonságával** is konfigurálhatók paraméterként. Ha egy e-mail-cím eszköz tulajdonságként van tárolva, **akkor a cím** megadásakor is használható.

## <a name="delete-a-rule"></a>Szabály törlése

Ha már nincs szüksége egy szabályra, törölje azt a szabály megnyitásával, és válassza a **Törlés**lehetőséget. A szabály törlése eltávolítja azt az eszköz sablonból és az összes kapcsolódó eszközről.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Eszközhöz tartozó szabály engedélyezése vagy letiltása

Navigáljon az eszközhöz, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. A **sablon összes eszközének engedélyezés szabályának** váltása az eszköz sablonhoz társított összes eszköz szabályának engedélyezéséhez vagy letiltásához.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Eszköz szabályának engedélyezése vagy letiltása

Navigáljon az eszközhöz, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. Az **eszközhöz tartozó** szabály engedélyezéséhez vagy letiltásához kapcsolja be a szabály engedélyezése ehhez a gombhoz beállítást.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan hozhat létre szabályokat az Azure IoT Central alkalmazásban, néhány további lépés:

- [Microsoft Flow művelet hozzáadása a szabályokban](howto-add-microsoft-flow.md)
- [Webhook-művelet hozzáadása a szabályokban](howto-create-webhooks.md)
- [Több művelet csoportosítása egy vagy több szabályból való futtatáshoz](howto-use-action-groups.md)
- [Az eszközök kezelése](howto-manage-devices.md)
