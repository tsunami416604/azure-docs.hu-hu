---
title: A platformok közötti Azure CLI segítségével hozzon létre a klasszikus Azure-szolgáltatások riasztásai |} Microsoft Docs
description: E-maileket vagy értesítések indítsa el, vagy hívja a webhely URL-címek (webhookok) vagy az automation a megadott feltételek teljesülése esetén.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 8112b868bc8d2ca2a9478d38ee702d8b3350d48e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287106"
---
# <a name="use-the-cross-platform-azure-cli-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>A platformok közötti Azure CLI használata klasszikus metrika riasztások létrehozása az Azure-figyelő Azure-szolgáltatások 

> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Parancssori felület](insights-alerts-command-line-interface.md)
>
>
> [!NOTE]
> Ez a cikk ismerteti, hogyan régebbi klasszikus metrika riasztások létrehozásához. Az Azure figyelő most támogatja [újabb, metrika riasztások jobb](monitoring-near-real-time-metric-alerts.md). Ezek a riasztások több metrikát, és lehetővé teszik a dimenzionális metrikák riasztást küld. Újabb metrika riasztások Azure parancssori felületi támogatása hamarosan elérhető.
>
>

Ez a cikk bemutatja, hogyan állíthatja be az Azure klasszikus metrika riasztások a platformfüggetlen parancssori felület (CLI) használatával.

> [!NOTE]
> Az Azure a figyelő az "Azure Insights" nevezett új neve csak 2016 szeptemberétől 25. Azonban a névterek, és így az itt ismertetett parancsokat tartalmaz a word "insights."

Az Azure-szolgáltatások metrikáját alapuló, vagy az Azure-ban események alapuló riasztást kaphat.

* **Metrika értékek**: A riasztás elindítja a megadott metrika értékét ebbe a küszöbérték, akkor mindkét irányban rendel. Ez azt jelenti, hogy elindítja a mindkét Ha először a feltétel teljesül, majd amikor ez a feltétel már nem van teljesül.    

* **Tevékenység naplóeseményeket**: riasztást aktiválhatók *minden* esemény, vagy amikor bizonyos események következnek be. Tevékenységi naplóit kapcsolatos további információkért lásd: [tevékenység létrehozása (klasszikus) napló riasztások](monitoring-activity-log-alerts.md). 

A klasszikus metrika riasztások tegye a következőket, amikor elindítja a konfigurálhatja:

* A szolgáltatás-rendszergazda és a társadminisztrátorok e-mail értesítések küldéséhez. 
* Küldjön e-mailt az e-mail-címek.
* A webhook hívja.
* Egy Azure-runbook (csak a jelenleg az Azure portálon) végrehajtásának elindítása.

Beállíthatja, klasszikus metrika riasztási szabályok adatainak beolvasása a következő használatával: 

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI](insights-alerts-command-line-interface.md)
* [Az Azure figyelő REST API-n](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Is kaphat a parancsok parancs beírásával **-súgó** végén. Az alábbiakban egy példa látható: 

```console
 azure insights alerts -help
 azure insights alerts actions email create -help
 ```

## <a name="create-alert-rules-by-using-azure-cli"></a>Riasztási szabályok létrehozása az Azure parancssori felület használatával
1. Az előfeltételek telepítése után jelentkezzen be az Azure-bA. Lásd: [Azure figyelő CLI minták](insights-cli-samples.md) a parancsok, amely szükséges a kezdéshez. Ezek a parancsok segítségével beszerzése aláírt, bemutatja, hogy milyen előfizetéshez használ, és előkészítése az Azure-figyelő parancsok futtatásához.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Meglévő erőforráscsoport szabályok listájában, használja a következő formátumban: 

   **Azure-riasztások szabályához lista insights** *[kapcsolók] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Olyan szabály létrehozására, először rendelkezik néhány fontos adatot kell.
    * A **erőforrás-azonosító** az erőforrás be szeretné állítani egy riasztást.
    * A **metrikai meghatározásainak** , amelyek az adott erőforrás érhető el.

     Egy az erőforrás-azonosító eléréséhez módja az Azure-portálon. Feltételezve, hogy az erőforrás már létrehozott, válassza ki azt a portálon. A következő panelen, majd a a **beállítások** szakaszban jelölje be **tulajdonságok**. **ERŐFORRÁS-azonosító** mező a következő panelen. 
     
     Az erőforrás-azonosítója használatával is megkapható [Azure erőforrás-kezelő](https://resources.azure.com/).

     Az alábbiakban olvashat egy példa egy webalkalmazást az erőforrás-azonosító:

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Az elérhető mérőszámok és egységek listájának lekérése az előző példában erőforrás metrikáit, a következő paranccsal Azure parancssori felület:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* (az 1 perces időközönként) elérhető mérési granularitása van. Lehetősége van különböző metrika különböző Granularitás van használatakor.
     
4. Riasztási szabály a metrika-alapú létrehozásához használja a parancs a következő formátumban:

    **Azure-riasztások metrika szabálykészlet insights** *[kapcsolók] &lt;ruleName&gt; &lt;hely&gt; &lt;resourceGroup&gt; &lt;ablakméret&gt; &lt;operátor&gt; &lt;küszöbérték&gt; &lt;targetresourceid azonosítója&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    Az alábbi példa állít be egy webhely erőforráson riasztást. A riasztási eseményindítók, ha 5 percig, majd újra amikor megkapja sincs forgalom 5 percig következetesen kap minden forgalom.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. A webhook létrehozása vagy egy e-mailt küld, ha a klasszikus metrika riasztások következik be, először létre kell hoznia az e-mailben vagy webhook. Ezután hozzon létre azonnal a szabályt ezt követően. Már létrehozott szabályok webhookok vagy e-mailek nem lehet társítani.

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

    Ezek a parancsok a cikkben korábban létrehozott szabályok törlése.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>További lépések
* [Az Azure Figyelés áttekintése](monitoring-overview.md), beleértve a adattípusok összegyűjtheti, és figyelje.
* További információ [konfigurálása webhookokkal a riasztások](insights-webhooks-alerts.md).
* További információ [riasztások konfigurálása a naplózási eseményeket](monitoring-activity-log-alerts.md).
* További információ [Azure Automation-runbook](../automation/automation-starting-a-runbook.md).
* Első egy [diagnosztikai naplók gyűjtésére áttekintése](monitoring-overview-of-diagnostic-logs.md) a szolgáltatás részletes nagyon gyakori gyűjtéséhez.
* Első egy [metrikák gyűjtemény áttekintése](insights-how-to-customize-monitoring.md) ellenőrizze, hogy a szolgáltatás elérhető, és a gyors.
