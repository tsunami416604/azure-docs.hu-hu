---
title: Azure Event Grid mérőszámok megtekintése és riasztások beállítása
description: Ez a cikk bemutatja, hogyan tekintheti meg Azure Event Grid témakörök és előfizetések metrikáit, és hogyan hozhat létre riasztásokat a Azure Portal használatával.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: spelluru
ms.openlocfilehash: e74d2d8982cac961aa65d6576c80a92cb53ce387
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85100493"
---
# <a name="monitor-event-grid-message-delivery"></a>Figyelő Event Grid üzenet kézbesítése 
Ez a cikk azt ismerteti, hogyan használható a portál a Event Grid témakörök és előfizetések metrikáinak megjelenítéséhez, valamint riasztások létrehozásához. 

## <a name="metrics"></a>Mérőszámok

A portál megjeleníti az események kézbesítésének állapotára vonatkozó metrikákat.

A következő témakörökben talál néhány mérőszámot:

* **Sikeres közzététel**: az esemény sikeresen elküldött a témakörbe, és 2xx-válaszsal lett feldolgozva.
* A **Közzététel sikertelen**: az esemény a témakörbe lett küldve, de hibakódtal elutasította.
* Nem **egyező**: az esemény sikeresen közzé lett téve a témakörben, de nem felel meg az esemény-előfizetésnek. Az esemény el lett dobva.

Az előfizetések esetében Íme néhány mérőszám:

* **Sikeres kézbesítés**: az esemény sikeresen kézbesítve lett az előfizetés végpontjának, és 2xx választ kapott.
* A **kézbesítés sikertelen volt**: minden alkalommal, amikor a szolgáltatás megpróbál eljuttatni, és az eseménykezelő nem ad vissza sikeres 2xx kódot, a **kézbesítés sikertelen** számlálója megnő. Ha ugyanazt az eseményt többször is megpróbáljuk kézbesíteni, és a művelet meghiúsul, a **sikertelen kézbesítés** számlálója az egyes hibáknál növekszik.
* **Lejárt események**: az esemény nem lett kézbesítve, és az összes újrapróbálkozási kísérlet elküldése megtörtént. Az esemény el lett dobva.
* **Egyeztetett események**: az esemény-előfizetés megfelelt a témakörben szereplő eseménynek.

    > [!NOTE]
    > A metrikák teljes listájáért lásd: [Azure Event Grid által támogatott metrikák](metrics.md).

## <a name="view-custom-topic-metrics"></a>Egyéni témakör metrikáinak megtekintése

Ha közzétett egy egyéni témakört, megtekintheti a metrikákat. 

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. A témakörben található keresősáv mezőbe írja be **Event Grid témaköröket**, majd válassza a **Event Grid témakörök** elemet a legördülő listából. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Event Grid témakörök keresése és kiválasztása":::
3. Válassza ki az egyéni témakört a témakörök listájában. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Egyéni témakör kiválasztása":::
4. Tekintse meg az egyéni esemény témakör metrikáit a **Event Grid témakör** oldalon. A következő ábrán az **alapvető** tudnivalókat tartalmazó szakasz az erőforráscsoport, az előfizetés stb. értékre van lecsökkentve. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Esemény metrikáinak megtekintése":::

A támogatott metrikákkal rendelkező diagramokat a **Event Grid témakör** oldal **mérőszámok** lapján lehet létrehozni.

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Témakör – mérőszámok lap":::

A metrikákkal kapcsolatos további tudnivalókért tekintse meg a [Azure monitor mérőszámait](../azure-monitor/platform/data-platform-metrics.md)

Tekintse meg például a **közzétett események** metrikájának mérőszámait tartalmazó diagramot.

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Közzétett események mérőszáma":::


## <a name="view-subscription-metrics"></a>Előfizetési mérőszámok megtekintése
1. Az előző szakasz lépéseit követve navigáljon a **Event Grid témakör** oldalára. 
2. Válassza ki az előfizetést az alsó ablaktáblán az alábbi példában látható módon. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Esemény-előfizetés kiválasztása":::    

    **Event Grid előfizetéseket** a Azure Portal keresési sávjában is megkeresheti, ha az esemény-előfizetés megjelenítéséhez a **témakör típusa**, az **előfizetés**és a **hely** lehetőséget választja. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Esemény-előfizetés kiválasztása Event Grid-előfizetések lapról":::        

    Egyéni témakörök esetén válassza ki a témakörök **típusaként** **Event Grid témákat** . A rendszertémák területen válassza ki az Azure-erőforrás típusát (például Storage- **fiókok (blob, GPv2))**. 
3. A diagramon az előfizetés kezdőlapján tekintheti meg az előfizetés mérőszámait. Az elmúlt 1 óra, 6 óra, 12 óra, 1 nap, 7 nap vagy 30 nap között az **általános**, a **hiba**, a **késés**és a **kézbesítetlen levelek** mérőszáma látható. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Metrikák az előfizetés kezdőlapján":::    

## <a name="view-system-topic-metrics"></a>Rendszertémakör metrikáinak megtekintése

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. A témakörben található keresősáv mezőbe írja be **Event Grid Rendszertémaköröket**, majd válassza a **Event Grid rendszertémakörök** lehetőséget a legördülő listából. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Event Grid rendszerbeli témakörök keresése és kiválasztása":::
3. A témakörök listájából válassza ki a rendszer témakört. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="A System témakör kiválasztása":::
4. Tekintse meg a rendszertémakör metrikáit a **Event Grid rendszertémakör** oldalon. A következő ábrán az **alapvető** tudnivalókat tartalmazó szakasz az erőforráscsoport, az előfizetés stb. értékre van lecsökkentve. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="A rendszertémakör metrikáinak megtekintése az Áttekintés oldalon":::

A támogatott metrikákkal rendelkező diagramokat a **Event Grid témakör** oldal **mérőszámok** lapján lehet létrehozni.

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="Rendszertémakör – mérőszámok lap":::

A metrikákkal kapcsolatos további tudnivalókért tekintse meg a [Azure monitor mérőszámait](../azure-monitor/platform/data-platform-metrics.md)


## <a name="set-alerts"></a>Riasztások beállítása
A Azure Portal témakörökre és tartományokra vonatkozó riasztásokat is beállíthat. 

A következő eljárás azt mutatja be, hogyan hozhat létre riasztást a **Kézbesítetlen események** metrikájában egy egyéni témakörhöz. Ebben a példában egy e-mailt küldünk az Azure erőforráscsoport-tulajdonosának, amikor egy témakörhöz tartozó kézbesítetlen események száma 10 fölé esik. 

1. A témakör **Event Grid témakör** lapján válassza a bal oldali menüben a **riasztások** elemet, majd válassza az **+ új riasztási szabály**lehetőséget. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Riasztások lap – új riasztási szabály gomb":::
    
    
    Riasztásokat a **metrikák** lapon is létrehozhat. A lépések hasonlóak. 

    :::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Metrikák lap – riasztás létrehozása gomb":::   
    
2. A **riasztási szabály létrehozása** lapon ellenőrizze, hogy a témakör ki van-e választva az erőforráshoz. Ezután kattintson a **feltétel kiválasztása**elemre. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Riasztások lap – feltétel kiválasztása":::    
3. A **jel logikai beállítása** lapon kövesse az alábbi lépéseket:
    1. Válasszon ki egy mérőszámot vagy egy tevékenységi naplóbejegyzés-bejegyzést. Ebben a példában a **Kézbesítetlen karakteres események** van kiválasztva. 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Elhalt levelekből származó események kiválasztása":::        
    2. Válasszon dimenziókat (nem kötelező). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Jellogika konfigurálása":::        
    3. Görgessen le. A **riasztási logika** szakaszban válasszon ki egy **operátort**, egy **összesítési típust**, és adjon meg egy **küszöbértéket**, és válassza a **kész**lehetőséget. Ebben a példában a riasztás akkor aktiválódik, ha a teljes kézbesítetlen üzenetek száma nagyobb, mint 10. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="Riasztási logika":::                
4. Vissza a **riasztási szabály létrehozása** lapon kattintson a **műveleti csoport kiválasztása**elemre.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="Műveleti csoport kiválasztása gomb":::
5. Új műveleti csoport létrehozásához válassza a **műveleti csoport létrehozása** lehetőséget az eszköztáron. Választhat egy meglévő műveleti csoportot is.        
6. A **műveleti csoport hozzáadása** oldalon hajtsa végre az alábbi lépéseket:
    1. Adja meg **a műveleti csoport nevét**.
    1. Adja meg a műveleti csoport **rövid nevét** .
    1. Válasszon ki egy **Azure-előfizetést** , amelyben létre kívánja hozni a műveleti csoportot.
    1. Válassza ki azt az **Azure-erőforráscsoportot** , amelyben létre kívánja hozni a műveleti csoportot.
    1. Adja meg **a művelet nevét**. 
    1. Válassza ki a **művelet típusát**. Ebben a példában az **e-mail-Azure Resource Manager szerepkör** van kiválasztva, konkrétan a **tulajdonosi** szerepkör. 
    1. A lap bezárásához kattintson **az OK gombra** . 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="Műveleti csoport hozzáadása lap":::                   
7. Vissza a **riasztási szabály létrehozása** lapon adja meg a riasztási szabály nevét, majd válassza a **riasztási szabály létrehozása**lehetőséget.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="Riasztási szabály neve":::  
8. A témakör **riasztások** lapján megjelenik egy hivatkozás a riasztási szabályok kezelésére, ha még nincsenek riasztások. Ha vannak riasztások, válassza a **kezelő riasztási szabályok** lehetőséget az eszköztáron.  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="Riasztások kezelése":::

    > [!NOTE]
    > Ez a cikk nem tárgyalja a riasztások létrehozásához használható különböző lépéseket és kombinációkat. A riasztások áttekintését lásd: [riasztások áttekintése](../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>További lépések

* További információ az események kézbesítéséről és újrapróbálkozásáról, [Event Grid az üzenetek kézbesítéséről, és próbálkozzon újra](delivery-and-retry.md).
* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez tekintse meg [az egyéni események létrehozása és irányítása Azure Event Grid](custom-event-quickstart.md)használatával című témakört.
