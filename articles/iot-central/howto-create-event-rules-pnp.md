---
title: Eseményvezérelt szabályok létrehozása és kezelése az Azure IoT Central alkalmazásban | Microsoft Docs
description: Az Azure IoT Central eseményvezérelt szabályai lehetővé teszik az eszközök közel valós idejű figyelését és a műveletek automatikus meghívását, például e-mailek küldését a szabály indításakor.
author: dominicbetts
ms.author: dobett
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1a7fc2b38e8354fb29255bb7f9d6b2c03ed53725
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879954"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Eseményvezérelt szabály létrehozása és értesítések beállítása az Azure IoT Central alkalmazásban (előzetes verziójú funkciók)

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Az Azure IoT Central használatával távolról is figyelheti a csatlakoztatott eszközöket. Az Azure IoT Central szabályai lehetővé teszik az eszközök közel valós idejű figyelését és a műveletek automatikus meghívását, például e-mailek küldését. Néhány kattintással megadhatja azt a feltételt, amelynél figyelni szeretné az eszköz adatait, és konfigurálnia kell a megfelelő műveletet. Ez a cikk azt ismerteti, hogyan hozhatók létre szabályok az eszköz által eljuttatott események figyelésére.

Az eszközök az esemény mérésével fontos vagy tájékoztató jellegű eseményeket küldhetnek. Az eseményvezérelt szabály akkor aktiválódik, ha az eszköz a kiválasztott eszköz eseményét jelzi.

## <a name="create-an-event-rule"></a>Eseményszabály létrehozása

Eseményvezérelt szabály létrehozásához az eszköz sablonjának meg kell határoznia legalább egy esemény mértékét. Ez a példa egy olyan hűtött automatából készült eszközt használ, amely egy ventilátoros motorral kapcsolatos hibát jelez. A szabály figyeli az eszköz által jelentett eseményt, és e-mailt küld az esemény jelentésekor.

1. Navigáljon a **szabályok** lapra.

1. Ha még nem hozott létre szabályokat, a következő képernyő jelenik meg:

   ![Még nincsenek szabályok](media/howto-create-event-rules-pnp/rules-landing-page1.png)

1. Válassza az **+ új szabály** lehetőséget a létrehozandó szabályok típusának megtekintéséhez.

1. Esemény-figyelési szabály létrehozásához válassza az **esemény** lehetőséget.

   ![Szabályok típusai](media/howto-create-event-rules-pnp/rule-types1.png)

1. Adjon meg egy nevet, amely segítséget nyújt a szabály azonosításához, majd nyomja le az ENTER billentyűt.

1. Válassza ki azt az eszköz-definíciót, amelyre a szabályt a hatókörek szakaszban szeretné kiterjeszteni. Ezen a képernyőn is kiszűrheti azokat az eszközöket, amelyekre a szabály vonatkozik a **+ szűrő**használatával. A szabály automatikusan az eszköz sablonja alatt lévő összes eszközre vonatkozik. A szabály letiltásához a fejlécben kattintson a **Letiltás** gombra.

### <a name="configure-the-rule-conditions"></a>A szabály feltételeinek konfigurálása

A feltétel határozza meg a szabály által figyelt feltételeket.

1. Válassza ki, hogy be vagy ki szeretné-e **állítani** az összesítést.

   - Összesítés nélkül a szabály a feltételnek megfelelő összes esemény-adatpontra elindítja a szabályt. Ha például úgy konfigurálja a szabály feltételét, hogy a ventilátoros **motor hibájának** bekövetkezésekor aktiválódik, a szabály szinte azonnal elindul, amikor az eszköz jelentést küld az eseményről.
   - Ha a **Count** összesítő függvényként van használatban, meg kell adnia egy **értéket** és egy időablakot, amely felett a feltételt ki kell értékelni. Ebben az esetben a rendszer összesíti az események számát, és a szabály csak akkor aktiválódik, ha az összesített események száma megegyezik az értékkel.

1. Válassza ki a figyelni kívánt eseményt a **mérték** legördülő listából. Ebben a példában a **ventilátor motoros hiba** esemény van kiválasztva.

1. Megadhatja a **Count** as összesítést is , és megadhatja a szabály által kiváltott értéket.

     Ha például akkor szeretne riasztást kapni, ha több mint három, 5 percnél több eszköz eseménye van, akkor válassza ki az eseményt, és állítsa az összesítő függvényt **számként**, operátorként a következő értékre, és adja meg a 3 **értéket** . Állítsa be az időablakot **5 percen**belül. A szabály akkor aktiválódik, ha az eszköz több mint három eseményt továbbít 5 percen belül. A szabály kiértékelésének gyakorisága megegyezik azidőablaktal, ami azt jelenti, hogy ebben a példában a szabály 5 percenként lesz kiértékelve.

 ![Állapot](media/howto-create-event-rules-pnp/aggregate-condition-filled-out1.png)

> [!NOTE]
> A **feltétel**több esemény-mérést is hozzáadhat. Ha több feltétel van megadva, az összes feltételnek teljesülnie kell a szabály működésének elindításához. Minden feltétel implicit módon csatlakozik egy "és" záradékhoz. Összesítés használatakor minden mérést összesíteni kell.

### <a name="configure-actions"></a>Műveletek konfigurálása

Ebből a szakaszból megtudhatja, hogyan állíthatja be, hogy milyen műveleteket kell végrehajtani a szabály indításakor. A rendszer meghívja a műveleteket, ha a szabályban megadott összes feltétel igaz értékre van kiértékelve.

1. Kattintson a **művelet** szakaszban a **+ művelet** elemre. Itt láthatja az elérhető műveletek listáját.  

   ![Művelet hozzáadása](media/howto-create-event-rules-pnp/add-action1.png)

1. Válassza ki az **e-mail** műveletet, adjon meg egy megjelenítendő nevet a műveletnek, egy érvényes e-mail-címet a **to** mezőben, és adjon meg egy megjegyzést, amely az e-mail törzsében jelenik meg, amikor a szabály eseményindítót tartalmaz.

   > [!NOTE]
   > Az e-maileket csak az alkalmazáshoz hozzáadott felhasználóknak küldi el a rendszer, és legalább egyszer bejelentkezett. További információ az Azure IoT Central [felhasználói kezeléséről](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

   ![Művelet konfigurálása](media/howto-create-event-rules-pnp/configure-action1.png)

1. A művelet mentéséhez válassza a **kész**lehetőséget.

1. A szabály mentéséhez válassza a **Mentés**lehetőséget. A szabály néhány percen belül elérhetővé válik, és megkezdi a figyelési események küldését az alkalmazásba. Ha a szabályban megadott feltétel teljesül, a szabály elindítja a konfigurált e-mail műveletet.

## <a name="parameterize-the-rule"></a>A szabály parametrizálja

A szabályok bizonyos Vales-ket származtatnak az **eszköz tulajdonságaiból** paraméterekként. A paraméterek használata olyan helyzetekben hasznos, ahol az események küszöbértékei eltérőek a különböző eszközökön. A szabály létrehozásakor válassza ki azt az eszköz tulajdonságot, amely meghatározza a küszöbértéket, például a **maximális ideális küszöbértéket**, és ne adjon meg abszolút értéket, például 3 eseményt. A szabály végrehajtásakor az megfelel az eszköz tulajdonságában beállított értéknek.

A paraméterek használata hatékony módszer a felügyelt szabályok számának csökkentésére.

A műveletek az **eszköz tulajdonságával** is konfigurálhatók paraméterként. Ha egy e-mail-cím eszköz tulajdonságként van tárolva, akkor a cím megadásakor is használható.

## <a name="delete-a-rule"></a>Szabály törlése

Ha már nincs szüksége egy szabályra, törölje azt a szabály megnyitásával, és válassza a **Törlés**lehetőséget. A szabály törlése eltávolítja azt az eszköz sablonból és az összes kapcsolódó eszközről.

## <a name="enable-or-disable-a-rule"></a>Szabály engedélyezése vagy letiltása

Válassza ki az engedélyezni vagy letiltani kívánt szabályt. Az **Engedélyezés** vagy **Letiltás** gomb kikapcsolásával engedélyezheti vagy letilthatja a szabály hatókörében lévő összes eszköz szabályát.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Eszköz szabályának engedélyezése vagy letiltása

Válassza ki az engedélyezni vagy letiltani kívánt szabályt. Vegyen fel egy szűrőt a **hatókörök** szakaszban egy adott eszköz belefoglalásához vagy kizárásához az eszköz sablonjában.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre szabályokat az Azure IoT Central alkalmazásban, a javasolt következő lépés az [eszközök kezelésének](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) megismerése
