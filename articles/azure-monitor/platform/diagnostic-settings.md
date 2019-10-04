---
title: Diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban | Microsoft Docs
description: Diagnosztikai beállítások létrehozásával továbbíthatja az Azure platform naplófájljait Azure Monitor naplókba, az Azure Storage-ba vagy az Azure Event Hubsba.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: ec1842d534dcb1e9ddef149d3ae879677b29e715
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262517"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Diagnosztikai beállítás létrehozása platform-naplók és-metrikák gyűjtéséhez az Azure-ban
Az Azure [platform-naplói](resource-logs-overview.md) részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokról és az azoktól függő Azure-platformról. Ez a cikk részletesen ismerteti a diagnosztikai beállítások létrehozását és konfigurálását, hogy a különböző célhelyekre gyűjtse a platform naplófájljait.

Minden Azure-erőforráshoz saját diagnosztikai beállítások szükségesek. A diagnosztikai beállítás a következőket határozza meg az erőforráshoz:

- A beállításban meghatározott célhelyekre továbbított naplók és metrikai adatok kategóriái. A rendelkezésre álló kategóriák a különböző erőforrástípusok esetében eltérőek lesznek.
- Egy vagy több célhely a naplók elküldéséhez. Az aktuális célhelyek közé tartozik Log Analytics munkaterület, Event Hubs és az Azure Storage.
- Adatmegőrzési szabályzat az Azure Storage-ban tárolt adatokhoz.
 
Egyetlen diagnosztikai beállítás is meghatározhatja az egyes célok egyikét. Ha szeretne adatokat küldeni egy adott célhelyhez (például két különböző Log Analytics-munkaterületre), akkor hozzon létre több beállítást. Minden erőforrás legfeljebb 5 diagnosztikai beállítással rendelkezhet.

> [!NOTE]
> A műveletnapló a többi platform naplóival megegyező célhelyre is továbbítható, de nincs beállítva diagnosztikai beállításokkal. További részletek: a [platform-naplók áttekintése az Azure-ban](platform-logs-overview.md#destinations) .

> [!NOTE]
> A rendszer automatikusan gyűjti a [platform metrikáit](metrics-supported.md) [Azure monitor metrikákat](data-platform-metrics.md). A diagnosztikai beállításokkal bizonyos Azure-szolgáltatások metrikáit gyűjthetik be Azure Monitor naplókba más figyelési adatokkal való elemzéshez [a naplók használatával.](../log-query/log-query-overview.md)

## <a name="destinations"></a>Célhelyek 
A platform naplói a következő táblázatban szereplő célhelyekre küldhetők el. Az egyes célhelyek konfigurációját ugyanazzal a folyamattal hajtja végre a jelen cikkben ismertetett diagnosztikai beállítások létrehozásához. Az alábbi táblázatban szereplő hivatkozásokra kattintva megtekintheti az adatok erre a célra történő küldésének részleteit.

| Destination | Leírás |
|:---|:---|
| [Log Analytics-munkaterület](resource-logs-collect-workspace.md) | A naplók egy Log Analytics munkaterületre való összegyűjtése lehetővé teszi, hogy a nagy teljesítményű naplók segítségével elemezze azokat más, Azure Monitor által gyűjtött figyelési adatokkal, valamint más Azure Monitor funkciók, például riasztások és vizualizációk kihasználása érdekében. |
| [Event hubok](resource-logs-stream-event-hubs.md) | A naplók küldésének Event Hubs lehetővé teszi az adatok továbbítását külső rendszerekre, például harmadik féltől származó SIEM-re és más log Analytics-megoldásokra. |
| [Azure Storage-fiók](resource-logs-collect-storage.md) | A naplók Azure Storage-fiókba való archiválása a naplózás, a statikus elemzés vagy a biztonsági mentés esetében hasznos. |




## <a name="create-diagnostic-settings-in-azure-portal"></a>Diagnosztikai beállítások létrehozása a Azure Portalban
A Azure Portal diagnosztikai beállításait a Azure Monitor menüből vagy az erőforrás menüjéből is konfigurálhatja.

1. A Azure Portal Azure Monitor menüjében kattintson a **diagnosztikai beállítások** elemre a **Beállítások** területen, majd kattintson az erőforrásra.

    ![Diagnosztikai beállítások](media/diagnostic-settings/menu-monitor.png)

    Vagy a Azure Portal erőforrás menüjében kattintson a **figyelés**lehetőségre a **diagnosztikai beállítások** elemre.

    ![Diagnosztikai beállítások](media/diagnostic-settings/menu-resource.png)

2. Ha a beállítások nem létezik az erőforráson kiválasztott, kéri létre beállítást. Kattintson a **Diagnosztika bekapcsolása** elemre.

   ![Diagnosztikai beállítás - beállítások nélkül hozzáadása](media/diagnostic-settings/add-setting.png)

   Ha vannak meglévő beállítások az erőforráson, látni fogja a már konfigurált beállítások listáját. Kattintson a **diagnosztikai beállítás hozzáadása** lehetőségre egy új beállítás hozzáadásához vagy egy meglévő **szerkesztési beállítás** megadásához. Az egyes beállítások nem lehetnek többek között a célhelyek közül.

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/diagnostic-settings/edit-setting.png)

3. Adja meg a beállítás nevét, ha még nem rendelkezik ilyennel.
4. Jelölje be az egyes célhelyek jelölőnégyzetét a naplók elküldéséhez. Kattintson a **Konfigurálás** elemre a beállítások megadásához az alábbi táblázatban leírtak szerint.

    | Beállítás | Leírás |
    |:---|:---|
    | Log Analytics-munkaterület | Munkaterület neve. |
    | Tárfiók | A Storage-fiók neve. |
    | Eseményközpont-névtér | Az a névtér, amelyben az Event hub létre lett hozva (ha ez az első adatfolyam-naplók), vagy adatfolyamként továbbítja a (ha már van olyan erőforrás, amely ezen a névtéren keresztül továbbítja a naplózási kategóriát).
    | Event Hubs neve | Opcionálisan megadhat egy Event hub-nevet, amely a beállításban szereplő összes értéket elküldi. Ha nem ad meg nevet, a rendszer minden egyes naplózási kategóriához létrehoz egy Event hub-t. Ha több kategóriát küld, érdemes megadnia egy nevet a létrehozott Event hubok számának korlátozásához. A részletekért tekintse meg az [Azure Event Hubs kvótáit és korlátozásait](../../event-hubs/event-hubs-quotas.md) . |
    | Eseményközpont szabályzatának neve | Meghatározza a folyamatos átviteli mechanizmushoz tartozó engedélyeket. |

    ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/diagnostic-settings/setting-details.png)

5. Jelölje be az egyes adatkategóriákhoz tartozó jelölőnégyzetet a megadott célhelyre való küldéshez. Ha a **Storage-fiókba való archiválás**lehetőséget választotta, akkor a [megőrzési időszakot](resource-logs-collect-storage.md#data-retention)is meg kell adnia.



> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Az Event hub "bejövő üzenetek" mérőszáma megvizsgálható és sorba állítható a várólista-szinten. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.

4. Kattintson a **Save** (Mentés) gombra.

Néhány pillanat elteltével megjelenik az új beállítás az erőforráshoz tartozó beállítások listájában, és a rendszer a naplókat a megadott célhelyre továbbítja az új esemény-adatforrások létrehozásakor. Vegye figyelembe, hogy egy esemény kibocsátása és a [log Analytics munkaterületen való megjelenése](data-ingestion-time.md)között akár tizenöt percet is igénybe vehet.



## <a name="create-diagnostic-settings-using-powershell"></a>Diagnosztikai beállítások létrehozása a PowerShell használatával
A [set-AzDiagnosticSetting](https://docs.microsoft.com/en-us/powershell/module/az.monitor/set-azdiagnosticsetting) parancsmaggal hozzon létre diagnosztikai beállítást [Azure PowerShell](powershell-quickstart-samples.md)használatával. A parancsmag dokumentációját a paraméterek leírását ismertető cikkben találja.

A következő példa egy PowerShell-parancsmagot tartalmaz a diagnosztikai beállítások mind a három célhely használatával történő létrehozásához.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -RetentionEnabled $true -RetentionInDays 7 -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Diagnosztikai beállítások létrehozása az Azure CLI-vel
Az az [monitor diagnosztikai-Settings Create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) paranccsal hozzon létre egy diagnosztikai beállítást az [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)használatával. A paraméterek leírását a parancs dokumentációjában találja.

A következő példa egy CLI-parancsot mutat be a diagnosztikai beállítások mind a három célhely használatával történő létrehozásához.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--metrics '[{"category": "AllMetrics","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Diagnosztikai beállítások konfigurálása REST API használatával
A diagnosztikai beállítások a [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/)használatával történő létrehozásához vagy frissítéséhez tekintse meg a [diagnosztikai beállításokat](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) .


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Diagnosztikai beállítások konfigurálása Resource Manager-sablon használatával
A diagnosztikai beállítások Resource Manager-sablonnal történő létrehozásához vagy frissítéséhez a Resource Manager-sablon használatával olvassa el a [diagnosztikai beállítások automatikus engedélyezése az erőforrás-létrehozásban](diagnostic-settings-template.md) című témakört.

## <a name="next-steps"></a>További lépések

* [További információ az Azure platform naplóiról](resource-logs-overview.md)