---
title: "Hozzon létre riasztásokat Azure-szolgáltatások - platformfüggetlen parancssori Felülettel |} Microsoft Docs"
description: "Eseményindító e-mailek, értesítések, a megadott feltételek teljesülnek webhely URL-címek (webhookok), vagy az automation hívni."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 5c6a2d27-7dcc-4f89-8752-9bb31b05ff35
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: robb
ms.openlocfilehash: 92246a8da73a244a1c9a924bed55711d71a20fd8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>Hozzon létre metrika riasztások Azure figyelése az Azure-szolgáltatások - platformfüggetlen parancssori Felülettel
> [!div class="op_single_selector"]
> * [Portál](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Parancssori felület](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan állíthat be Azure metrika riasztások a platformfüggetlen parancssori felület (CLI) használatával.

> [!NOTE]
> Az Azure figyelő csak 2016. Szeptembertől 25. az "Azure Insights" nevezett új neve. Azonban a névterek, ezért az alábbi parancsok továbbra is tartalmazza az "insights".
>
>

A figyelési metrikákat, vagy események, az Azure-szolgáltatások alapuló riasztást kaphat.

* **Metrika értékek** -a riasztás elindítja a megadott metrika értékét ebbe a küszöbérték mindkét irányban rendel. Ez azt jelenti, hogy elindítja a mindkét Ha először a feltétel teljesül, és majd ezt követően, hogy a feltétel mikor van már nem teljesül.    
* **Tevékenység naplóeseményeket** -riasztást aktiválhatók *minden* esemény, vagy csak akkor, ha egy bizonyos események következik be. További információt a naplófájl tevékenységriasztásokat [kattintson ide](monitoring-activity-log-alerts.md)

A metrika riasztások tegye a következőket, amikor elindítja a konfigurálhatja:

* e-mail értesítések küldéséhez a szolgáltatás-rendszergazda és a társadminisztrátorok
* e-mail küldéséhez megadott további e-maileket.
* A webhook hívása
* egy Azure-runbook (csak a jelenleg az Azure portálon) végrehajtásának elindítása

Konfigurálhatja és metrika riasztási szabályok adatainak beolvasása

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [parancssori felület (CLI)](insights-alerts-command-line-interface.md)
* [Az Azure figyelő REST API-n](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Mindig fogadhat parancsokhoz tartozó súgók parancs beírásával és üzembe - súgó végén. Példa:

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>A parancssori felület használatával riasztási szabályok létrehozása
1. Hajtsa végre az Előfeltételek és a bejelentkezés az Azure-bA. Lásd: [Azure figyelő CLI minták](insights-cli-samples.md). Összefoglalva a parancssori felület telepítése, és futtassa az alábbi parancsokat. Ezt a használatuk jelentkezett be, megjelenítése, hogy milyen előfizetéshez használ, és készítse elő az Azure-figyelő parancsok futtatásához.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Egy erőforráscsoportot a meglévő szabályok listájában, használja a következő képernyő **riasztások szabályához lista azure insights** *[kapcsolók] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Olyan szabály létrehozására, először rendelkezik néhány fontos adatot kell.
  * A **erőforrás-azonosító** be szeretné állítani egy riasztást az erőforrás
  * A **metrikai meghatározásainak** az adott erőforrás érhető el

     Egy az erőforrás-azonosító eléréséhez módja az Azure-portálon. Ha az erőforrás létrehozása már be van állítva, válassza ki azt a portálon. A következő paneljén válassza *tulajdonságok* alatt a *beállítások* szakasz. A *erőforrás-azonosító* mező a következő panelen. Egy másik módja a [Azure erőforrás-kezelő](https://resources.azure.com/).

     Egy példa egy webalkalmazást az erőforrás-azonosító

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Ezeket az előző példában erőforrás metrikáit listáját az elérhető mérőszámok és egységek beszerzéséhez használja a következő parancssori parancsot:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* a lépésköz legyen a rendelkezésre álló mérési (1 perces időközönként) van. Különböző Granularitás van használatával különböző metrika lehetőséget biztosít.
4. A metrika-alapú riasztási szabály létrehozásához használja a következő parancsot:

    **Azure-riasztások metrika szabálykészlet insights** *[kapcsolók] &lt;ruleName&gt; &lt;hely&gt; &lt;resourceGroup&gt; &lt;ablakméret&gt; &lt;operátor&gt; &lt;küszöbérték&gt; &lt;targetresourceid azonosítója&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    Az alábbi példa állít be egy webhely erőforráson riasztást. A riasztási eseményindítók, ha 5 percig, majd újra amikor megkapja sincs forgalom 5 percig következetesen kap minden forgalom.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. Webhook létrehozása vagy e-mailek küldése egy metrika riasztás aktiválódásakor, először létre kell hoznia az e-mailek és/vagy a webhook. Ezután hozzon létre azonnal a szabályt ezt követően. Nem társítható webhook vagy e-mailek már létrehozott szabályok a parancssori felület használatával.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Ellenőrizheti, hogy a riasztások elkészültek megfelelően az egyes szabályok alapján.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Szabályok törléséhez használja a parancsot a következő formátumban:

    **riasztások szabály törlése insights** [kapcsolók] &lt;resourceGroup&gt; &lt;szabálynév&gt;

    Ezek a parancsok törölje az ebben a cikkben korábban létrehozott szabályokat.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Következő lépések
* [Az Azure Figyelés áttekintése](monitoring-overview.md) többek között a adattípusok összegyűjtheti, és figyelje.
* További információ [konfigurálása webhookokkal a riasztások](insights-webhooks-alerts.md).
* További információ [riasztások konfigurálása a naplózási eseményeket](monitoring-activity-log-alerts.md).
* További információ [Azure Automation-forgatókönyveket](../automation/automation-starting-a-runbook.md).
* Első egy [diagnosztikai naplók gyűjtésére áttekintése](monitoring-overview-of-diagnostic-logs.md) nagyon gyakori gyűjtéséhez részletes a a szolgáltatásban.
* Első egy [metrikák gyűjtemény áttekintése](insights-how-to-customize-monitoring.md) ellenőrizze, hogy a szolgáltatás elérhető, és a gyors.
