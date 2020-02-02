---
title: Azure Event Grid – diagnosztikai naplók engedélyezése egy témakörhöz
description: Ez a cikk lépésről lépésre bemutatja, hogyan engedélyezheti a diagnosztikai naplókat egy Azure Event Grid-témakörben.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960502"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Azure Event Grid-témakör diagnosztikai naplói
A diagnosztikai beállítások lehetővé teszik Event Grid felhasználók számára a közzétételi és kézbesítési hibák naplóinak rögzítését és megtekintését az alábbi helyek egyikében: egy Azure Storage-fiók, egy Event hub vagy egy Log Analytics munkaterület. Ez a cikk lépésről lépésre bemutatja, hogyan engedélyezheti a diagnosztikai naplókat egy Event Grid-témakörben.

## <a name="prerequisites"></a>Előfeltételek

- Kiépített Event Grid-témakör
- A diagnosztikai naplók rögzítésére szolgáló kiépített cél. A következő célok egyike lehet:
    - Azure Storage-fiók
    - Eseményközpont
    - Log Analytics-munkaterület


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>A diagnosztikai naplók engedélyezésének lépései a témakörben

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Lépjen arra az Event Grid-témakörre, amelyre vonatkozóan engedélyezni kívánja a diagnosztikai napló beállításait. 
3. A bal oldali menüben válassza a **diagnosztikai beállítások** lehetőséget a **figyelés** elemnél.
4. A **diagnosztikai beállítások** lapon válassza az **új diagnosztikai beállítás hozzáadása**elemet. 
    
    ![Diagnosztikai beállítások hozzáadása gomb](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Adja meg a diagnosztikai beállítás **nevét** . 

    ![Diagnosztikai beállítások – név](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Engedélyezzen egy vagy több rögzítési célhelyet a naplókhoz, majd konfigurálja őket úgy, hogy kiválasztja az előző létrehozott rögzítési erőforrást. 
    - Ha kijelöli az **archiválás egy Storage-fiókba**lehetőséget, válassza a **Storage-fiók konfigurálása**lehetőséget, majd válassza ki a Storage-fiókot az Azure-előfizetésében. 

        ![Archiválás Azure Storage-fiókba](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Ha **az adatfolyam lehetőséget választja egy Event hub**-ra, válassza az **Event hub-configure**lehetőséget, majd válassza ki a Event Hubs névteret, az Event hub és a hozzáférési házirendet. 
        Stream ![a egy Event hub-](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Ha a **küldés log Analytics**lehetőséget választja, válassza ki a log Analytics munkaterületet.
        ![küldés Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Válassza ki a **DeliveryFailures** és a **PublishFailures** beállításokat a **napló** szakaszban. 
    ![válassza ki a hibákat](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Kattintson a **Mentés** gombra. A lap bezárásához kattintson az **X** gombra a jobb oldali sarokban. 
9. Most vissza a **diagnosztikai beállítások** lapon ellenőrizze, hogy megjelenik-e új bejegyzés a **diagnosztikai beállítások** táblázatban. 
    ![diagnosztikai beállítás a listában](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     A témakör összes mérőszámának gyűjteményét is engedélyezheti. 

## <a name="next-steps"></a>Következő lépések
Ha további segítségre van szüksége, tegye fel a problémát a [stack overflow fórumba](https://stackoverflow.com/questions/tagged/azure-eventgrid) , vagy nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/). 
