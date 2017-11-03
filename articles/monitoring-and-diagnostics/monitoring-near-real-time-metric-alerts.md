---
title: "Majdnem valós idejű metrika riasztások az Azure figyelő |} Microsoft Docs"
description: "Közel valós idejű metrika riasztások engedélyezése az Azure erőforrás-metrikák 1 perces gyakorisággal figyelheti."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: aeeb6c2fb87e6c19991ef243ee7230f4e8f4e251
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>Közel valós idejű metrika riasztások (előzetes verzió)
Az Azure figyelő mostantól támogatja a közel valós idejű metrika riasztások (előzetes verzió) nevű metrika riasztások új típusú. Ez a funkció jelenleg nyilvános előzetes verziójához.
Ezek a riasztások eltér a normál metrika riasztások néhány módszer az

- **Továbbfejlesztett késés** -közel valós idejű metrika riasztások figyelheti metrika értékek változásait, amint 1 perc.
- **Metrika feltételek teljesebb körű vezérlése** -közel valós idejű metrika riasztások engedélyezése a felhasználók szélesebb riasztási szabályok meghatározásához. Az értesítések mostantól támogatják az a maximális, minimális, átlagos, és az összes érték a metrikák a figyelést.
- **Egyszerre több metrikák figyelését** -közel valós idejű metrika riasztások több metrikák (jelenleg két) kezelhető egyetlen szabállyal képes figyelni. A figyelmeztetés lekérdezi Ha mindkét a metrikák a megadott ideig megsértik a a vonatkozó küszöbértéket.
- **Moduláris értesítési rendszer** - metrika valós idejű riasztások használata mellett [művelet csoportok](monitoring-action-groups.md). Ez a funkció lehetővé teszi, hogy a felhasználók hozhatnak létre műveletek moduláris módon, és használja fel őket a sok riasztási szabályok.

> [!NOTE]
> Közel valós idejű metrika riasztások funkció jelenleg nyilvános előzetes verziójához. A funkció és a felhasználói élmény van változhat.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Milyen erőforrásokat hozhat létre közel valós idejű metrika riasztások?
Közel valós idejű metrika riasztások által támogatott típusú erőforrások teljes listája:

* Microsoft.ApiManagement/service
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Sql/servers/elasticpools
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.Timeseriesinsights
* Microsoft.CognitiveServices/accounts


## <a name="create-a-near-real-time-metric-alert"></a>Riasztás létrehozása, a közel valós idejű metrika
Jelenleg közel valós idejű metrika riasztásokat csak hozhatók létre az Azure portálon keresztül. Közel valós idejű metrika riasztások PowerShell parancssori felület (CLI) és Azure figyelő REST API-n keresztül konfigurálása támogatása hamarosan elérhető.

1. Az a [portal](https://portal.azure.com/), keresse meg az erőforrás figyelési érdekli, és válassza ki azt. Ehhez az erőforráshoz a felsorolt típusok egyikének kell lennie a [előző szakaszban](#what-resources-can-i-create-near-real-time-metric-alerts-for). Megteheti is azonos az összes támogatott erőforrások központilag figyelő > riasztásokat.

2. Válassza ki **riasztások** vagy **riasztási szabályok** a figyelés szakaszban. A szöveg és ikon eltérő lehet attól függően némileg különböző erőforrások.
   ![Figyelés](./media/insights-alerts-portal/AlertRulesButton.png)

3. Kattintson a **közel valós idejű metrikák riasztás (előzetes verzió) hozzáadása** parancsot. Ha a parancs szürkén jelenik meg, győződjön meg arról, az erőforrás van jelölve, a szűrő.

    ![Vegye fel a közel valós idejű metrikák riasztási gomb](./media/monitoring-near-real-time-metric-alerts/AddNRTAlertButton.png)

4. **Név** a riasztás szabályt, majd válassza ki a **leírása**, amely értesítési e-mailt is mutatja.
5. Válassza ki a **metrika** szeretne figyelni, majd kattintson egy **feltétel**, **idő összesítése**, és **küszöbérték** a mérték értékét. Kiválaszthatja egy másik **metrika** szeretne figyelni, majd kattintson a **feltétel**, **idő összesítése**, és **küszöbérték** értékét a második metrikát. 

    ![Vegye fel a közel valós idejű metrikák Alert1](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert1.png) ![közel valós idejű metrikák Alert2 hozzáadása](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert2.png)
6. Válassza ki a **időszak** idő a metrika szabályokat kell biztosítani a riasztási eseményindítók előtt. Így például használatakor az időtartam "keresztül az elmúlt 5 percben" és a riasztási keres processzor 80 %-ot (és 500 MB feletti NetworkIn) felett, a riasztás váltja ki, ha a CPU már következetesen fenti 80 % 5 perc. Akkor következik be, az első eseményindító, amennyiben azt újra váltja ki, ha 5 percig 80 % alatt marad a Processzor. A riasztás a következők szerint történik a **kiértékelési gyakoriságának**


6. Válasszon egy megfelelő **súlyossági** a legördülő listából.

7. Adja meg, ha egy új vagy meglévő használni kívánt **művelet csoport**.

8. Ha úgy dönt, hogy hozzon létre **új** művelet csoport, meg kell adni a művelet csoport nevét és egy rövid nevet, adjon meg műveleteket (SMS, e-mailek Webhook), és töltse ki a megfelelő részleteit.


8. Válassza ki **OK** végzett a riasztás létrehozása.   

Néhány percen belül a riasztás aktív, és elindítja a leírt módon.

## <a name="managing-near-real-time-metric-alerts"></a>Közel valós idejű metrika riasztások kezelése
Miután létrehozott egy riasztást, kijelölheti azt és:

* A metrika küszöbérték és a tényleges értékek az előző nap egy grafikonon megtekintése.
* Szerkesztheti és törölheti azt.
* **Tiltsa le a** vagy **engedélyezése** , ha azt szeretné, ideiglenesen leállítani, vagy folytassa a riasztás-mailjeire.



