---
title: Diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban
description: Diagnosztikai beállításokat hozhat létre az Azure-platformnaplók Azure Monitor-naplókba, az Azure storage-ba vagy az Azure Event Hubs-ba való továbbításához.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fb2f9ff5af68575d9f9d29e9a6aca83d603395b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672412"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Diagnosztikai beállítás létrehozása platformnaplók és metrikák gyűjtéséhez az Azure-ban
Az Azure-beli [platformnaplók,](platform-logs-overview.md) beleértve az Azure-tevékenységnaplót és az erőforrásnaplókat, részletes diagnosztikai és naplózási információkat nyújtanak az Azure-erőforrásokhoz és az Azure platformhoz, amelytől függenek. Ez a cikk részletesen ismerteti a diagnosztikai beállítások létrehozását és konfigurálását a platformnaplók különböző célokra történő küldéséhez.

> [!IMPORTANT]
> Mielőtt létrehozna egy diagnosztikai beállítást a tevékenységnapló összegyűjtéséhez, először le kell tiltania az örökölt konfigurációkat. A részleteket [lásd: Azure-tevékenységnapló az örökölt beállításokkal.](diagnostic-settings-legacy.md)

Minden Azure-erőforráshoz saját diagnosztikai beállítás szükséges, amely a következőket határozza meg:

- A beállításban meghatározott célhelyekre küldött naplók és metrikaadatok kategóriái. A rendelkezésre álló kategóriák eltérőek lehetnek a különböző erőforrástípusoktól.
- Egy vagy több úti cél a naplók elküldéséhez. A jelenlegi úti célok közé tartozik a Log Analytics munkaterület, az Event Hubs és az Azure Storage.
 
Egyetlen diagnosztikai beállítás csak egy célt határozhat meg. Ha egy adott céltípus (például két különböző Log Analytics-munkaterület) egynél több tárolótípusra szeretne adatokat küldeni, akkor hozzon létre több beállítást. Minden erőforrás legfeljebb 5 diagnosztikai beállításokat.


> [!NOTE]
> [A platformmetrikák automatikusan](metrics-supported.md) kerülnek összegyűjtésre az [Azure Monitor metrikák ba.](data-platform-metrics.md) A diagnosztikai beállítások segítségével bizonyos Azure-szolgáltatások metrikákat gyűjthet az Azure Monitor Naplókba, hogy [a naplólekérdezések](../log-query/log-query-overview.md)használatával más figyelési adatokkal elemezhessék.

## <a name="destinations"></a>Célhelyek 
A platformnaplók az alábbi táblázatban szereplő célállomásokra küldhetők. Az egyes célhelyek konfigurációja ugyanazzal a folyamattal történik a jelen cikkben ismertetett diagnosztikai beállítások létrehozásához. Az alábbi táblázatban található hivatkozásokat az adott célhelyre küldött adatokrészleteiért kövesse.

| Cél | Leírás |
|:---|:---|
| [Log Analytics-munkaterület](resource-logs-collect-workspace.md) | Naplók gyűjtése egy Log Analytics-munkaterület lehetővé teszi, hogy elemezze őket más figyelési adatokat gyűjtött az Azure Monitor hatékony naplólekérdezések, valamint kihasználni más Azure Monitor funkciók, például a riasztások és vizualizációk. |
| [Event Hubs](resource-logs-stream-event-hubs.md) | Naplók küldése az Event Hubs lehetővé teszi, hogy adatokat továbbítson a külső rendszerek, például a külső SIEMs és más log elemzési megoldások. |
| [Azure tárfiók](resource-logs-collect-storage.md) | Naplók archiválása egy Azure storage-fiók naplózása, statikus elemzés vagy biztonsági mentés. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Diagnosztikai beállítások létrehozása az Azure Portalon
Diagnosztikai beállításokat az Azure Portalon vagy az Azure Monitor menüből, vagy az erőforrás menüjéből.

1. Az Azure Portalon a diagnosztikai beállítások konfigurálásának helye az erőforrástól függ.

   - Egyetlen erőforrás esetén **kattintson** az erőforrás menüjének **Figyelő** gombjára.

        ![Diagnosztikai beállítások](media/diagnostic-settings/menu-resource.png)

    - Egy vagy több erőforrás esetén kattintson az Azure Monitor menü **Beállítások parancsának Diagnosztikai** **beállítások parancsára,** majd kattintson az erőforrásra.
    
        ![Diagnosztikai beállítások](media/diagnostic-settings/menu-monitor.png)

    - A tevékenységnaplóhoz kattintson a **Tevékenységnapló parancsra** az **Azure Monitor** menüben, majd a Diagnosztikai **beállítások parancsra.** Győződjön meg arról, hogy letiltja a tevékenységnapló örökölt konfigurációját. A részletekért olvassa el A [meglévő beállítások letiltása](diagnostic-settings-legacy.md#disable-existing-settings) témakört.

        ![Diagnosztikai beállítások](media/diagnostic-settings/menu-activity-log.png)

2. Ha a kijelölt erőforráson nincsenek beállítások, a program kéri egy beállítás létrehozását. Kattintson **a Diagnosztikai beállítás hozzáadása gombra.**

   ![Diagnosztikai beállítás hozzáadása - nincs meglévő beállítás](media/diagnostic-settings/add-setting.png)

   Ha az erőforráson vannak beállítások, megjelenik a már konfigurált beállítások listája. Új beállítás hozzáadásához kattintson a **Diagnosztikai beállítás hozzáadása** gombra, a Meglévő beállítás szerkesztéséhez a Szerkesztés **gombra** kattintva. Minden beállítás nak legfeljebb egy a céltípusok közül lehet.

   ![Diagnosztikai beállítás hozzáadása - meglévő beállítások](media/diagnostic-settings/edit-setting.png)

3. Adjon nevet a beállításnak, ha még nem rendelkezik ilyensel.
4. A naplók elküldéséhez jelölje be az egyes célhelyek jelölőnégyzetét. A **Beállítás gombra** kattintva adja meg a beállításokat az alábbi táblázatban leírtak szerint.

    | Beállítás | Leírás |
    |:---|:---|
    | Log Analytics-munkaterület | Munkaterület neve. |
    | Tárfiók | A tárfiók neve. |
    | Eseményközpont-névtér | Az a névtér, ahol az eseményközpontot létrehozták (ha ez az első alkalommal streamelési naplók) vagy streamelt (ha már vannak olyan erőforrások, amelyek streamelés, hogy a napló kategóriában erre a névtérbe).
    | Event Hubs neve | Tetszés szerint meg adhat egy eseményközpont-nevet a beállításban lévő összes adat elküldéséhez. Ha nem ad meg nevet, minden naplókategóriához létrejön egy eseményközpont. Ha több kategóriát küld, érdemes megadni egy nevet a létrehozott eseményközpontok számának korlátozására. A részletekért tekintse meg az [Azure Event Hubs kvótákat és -korlátozásokat.](../../event-hubs/event-hubs-quotas.md) |
    | Az eseményközpont házirendjének neve | A streamelési mechanizmus engedélyeit határozza meg. |

    ![Diagnosztikai beállítás hozzáadása - meglévő beállítások](media/diagnostic-settings/setting-details.png)

5. Jelölje be a megadott célhelyekre küldő adatkategóriák jelölőnégyzetét. A kategóriák listája az egyes Azure-szolgáltatások tól függően változik.

   > [!NOTE]
   > A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
   >
   > *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.

6. Kattintson a **Mentés** gombra.

Néhány pillanat múlva az új beállítás megjelenik az erőforrás beállításainak listájában, és a naplók streamelése a megadott célhelyekre, amikor új eseményadatok jönnek létre. Vegye figyelembe, hogy egy esemény kibocsátása és a [Log Analytics-munkaterületen való megjelenhet](data-ingestion-time.md)között legfeljebb tizenöt perc lehet.



## <a name="create-diagnostic-settings-using-powershell"></a>Diagnosztikai beállítások létrehozása a PowerShell használatával
A [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) parancsmag segítségével hozzon létre egy diagnosztikai beállítást az [Azure PowerShell](powershell-quickstart-samples.md)használatával. A paraméterek leírását a parancsmag dokumentációjában találja.

> [!IMPORTANT]
> Ez a módszer nem használható az Azure-tevékenységnaplóhoz. Ehelyett használja [a diagnosztikai beállítás létrehozása az Azure Monitor egy Resource Manager-sablon használatával](diagnostic-settings-template.md) hozzon létre egy Erőforrás-kezelő sablont, és üzembe helyezi azt a PowerShell használatával.

Az alábbiakban egy példa PowerShell-parancsmag, amely mindhárom cél használatával diagnosztikai beállítást hoz létre.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Diagnosztikai beállítások létrehozása az Azure CLI használatával
Az [az monitor diagnosztikai beállítások létrehozása](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) parancs segítségével hozzon létre egy diagnosztikai beállítást az Azure [CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)segítségével. A paraméterek leírását a parancs dokumentációjában találja.

> [!IMPORTANT]
> Ez a módszer nem használható az Azure-tevékenységnaplóhoz. Ehelyett használja [a diagnosztikai beállítás létrehozása az Azure Monitor egy Resource Manager sablon használatával](diagnostic-settings-template.md) hozzon létre egy Erőforrás-kezelő sablont, és telepítse azt a CLI használatával.

A következő példa cli parancsot, hogy hozzon létre egy diagnosztikai beállítást a három cél.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Diagnosztikai beállítások konfigurálása a REST API használatával
A [diagnosztikai beállítások](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) létrehozásához vagy frissítéséhez tekintse meg a diagnosztikai beállításokat az Azure Monitor REST API [használatával.](https://docs.microsoft.com/rest/api/monitor/)


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Diagnosztikai beállítások konfigurálása az Erőforrás-kezelő sablonnal
Lásd: [Diagnosztikai beállítás létrehozása az Azure Monitorban egy Erőforrás-kezelő sablon használatával](diagnostic-settings-template.md) diagnosztikai beállítások létrehozásához vagy frissítéséhez egy Erőforrás-kezelő sablonhasználatával.

## <a name="next-steps"></a>További lépések

* [További információ az Azure platform naplóiról](platform-logs-overview.md)
