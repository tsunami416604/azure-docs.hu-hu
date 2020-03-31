---
title: Azure Event Grid – Diagnosztikai naplók engedélyezése egy témakörhöz
description: Ez a cikk lépésenként ismerteti, hogyan engedélyezheti a diagnosztikai naplók egy Azure-eseményrács-témakör.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960502"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Diagnosztikai naplók egy Azure-eseményrács témaköréhez
A diagnosztikai beállítások lehetővé teszik az Event Grid-felhasználók számára a közzétételi és kézbesítési hibák naplózásának rögzítését és megtekintését a következő helyek egyikén: egy Azure-tárfiók, egy eseményközpont vagy egy Log Analytics-munkaterület. Ez a cikk lépésenkénti utasításokat tartalmaz egy eseményrács-témakör diagnosztikai naplóinak engedélyezéséhez.

## <a name="prerequisites"></a>Előfeltételek

- Kiépített eseményrács-témakör
- A diagnosztikai naplók rögzítésére kiépített cél. Ez az alábbi úti célok egyikét teheti meg:
    - Azure Storage-fiók
    - Eseményközpont
    - Log Analytics-munkaterület


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Témakör diagnosztikai naplóinak engedélyezésének lépései

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Nyissa meg azt az eseményrács-témakört, amelynek diagnosztikai naplóbeállításait engedélyezni szeretné. 
3. Válassza a bal oldali menü **Figyelés parancsának** **Diagnosztikai beállítások parancsát.**
4. A **Diagnosztikai beállítások** lapon válassza az **Új diagnosztikai beállítás hozzáadása**lehetőséget. 
    
    ![Diagnosztikai beállítás hozzáadása gomb](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Adja meg a diagnosztikai beállítás **nevét.** 

    ![Diagnosztikai beállítások - név](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Engedélyezze a naplók egy vagy több rögzítési célját, majd konfigurálja őket egy korábbi létrehozott rögzítési erőforrás kiválasztásával. 
    - Ha az **Archiválás tárfiókba**lehetőséget választja, válassza **a Tárfiók – Konfigurálás**lehetőséget, majd válassza ki a tárfiókot az Azure-előfizetésben. 

        ![Archiválás Azure-tárfiókba](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Ha a **Stream to an event hub**lehetőséget választja, válassza az **Eseményközpont – Konfigurálás**lehetőséget, majd válassza ki az Event Hubs névteret, az eseményközpontot és a hozzáférési szabályzatot. 
        ![Streamelés eseményközpontba](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Ha a **Küldés a Log Analytics szolgáltatásba**lehetőséget választja, válassza a Log Analytics munkaterületet.
        ![Küldés a Log Analyticsnek](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Válassza ki a **DeliveryFailures** és **publishFailures beállításokat** a **Napló** szakaszban. 
    ![A hibák kiválasztása](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Kattintson a **Mentés** gombra. A lap bezárásához válassza az **X** gombot a jobb sarokban. 
9. Most a **Diagnosztikai beállítások** lapon ellenőrizze, hogy megjelenik-e egy új bejegyzés a **Diagnosztikai beállítások** táblában. 
    ![Diagnosztikai beállítás a listában](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Engedélyezheti a témakör összes metrika gyűjteményét is. 

## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége, tegye fel a problémát a [Stack Overflow fórumon,](https://stackoverflow.com/questions/tagged/azure-eventgrid) vagy nyisson meg egy [támogatási jegyet.](https://azure.microsoft.com/support/options/) 
