---
title: Diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban
description: Diagnosztikai beállítások létrehozásával továbbíthatja az Azure platform naplófájljait Azure Monitor naplókba, az Azure Storage-ba vagy az Azure Event Hubsba.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fb2f9ff5af68575d9f9d29e9a6aca83d603395b3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672412"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Diagnosztikai beállítás létrehozása platform-naplók és-metrikák gyűjtéséhez az Azure-ban
Az Azure [platform-naplói](platform-logs-overview.md) , beleértve az Azure-tevékenységeket és az erőforrás-naplókat, részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokról és az azoktól függő Azure-platformról. Ez a cikk részletesen ismerteti, hogyan lehet diagnosztikai beállításokat létrehozni és konfigurálni a platform naplófájljainak különböző célhelyekre küldéséhez.

> [!IMPORTANT]
> Mielőtt diagnosztikai beállítást hozna létre a műveletnapló összegyűjtéséhez, először le kell tiltania az örökölt konfigurációkat. A részletekért lásd: az [Azure-tevékenység naplójának összegyűjtése örökölt beállításokkal](diagnostic-settings-legacy.md) .

Minden Azure-erőforráshoz saját diagnosztikai beállítások szükségesek, amely az alábbiakat határozza meg:

- A beállításban meghatározott célhelyekre továbbított naplók és metrikai adatok kategóriái. A rendelkezésre álló kategóriák a különböző erőforrástípusok esetében eltérőek lesznek.
- Egy vagy több célhely a naplók elküldéséhez. Az aktuális célhelyek közé tartozik Log Analytics munkaterület, Event Hubs és az Azure Storage.
 
Egyetlen diagnosztikai beállítás a célhelyek közül legfeljebb egyet tud meghatározni. Ha szeretne adatokat küldeni egy adott célhelyhez (például két különböző Log Analytics-munkaterületre), akkor hozzon létre több beállítást. Minden erőforrás legfeljebb 5 diagnosztikai beállítással rendelkezhet.


> [!NOTE]
> A rendszer automatikusan gyűjti a [platform metrikáit](metrics-supported.md) [Azure monitor metrikákat](data-platform-metrics.md). A diagnosztikai beállításokkal bizonyos Azure-szolgáltatások metrikáit gyűjthetik be Azure Monitor naplókba más figyelési adatokkal való elemzéshez [a naplók használatával.](../log-query/log-query-overview.md)

## <a name="destinations"></a>Destinations 
A platform naplói a következő táblázatban szereplő célhelyekre küldhetők el. Az egyes célhelyek konfigurációját ugyanazzal a folyamattal hajtja végre a jelen cikkben ismertetett diagnosztikai beállítások létrehozásához. Az alábbi táblázatban szereplő hivatkozásokra kattintva megtekintheti az adatok erre a célra történő küldésének részleteit.

| Cél | Leírás |
|:---|:---|
| [Log Analytics-munkaterület](resource-logs-collect-workspace.md) | A naplók egy Log Analytics munkaterületre való összegyűjtése lehetővé teszi, hogy a nagy teljesítményű naplók segítségével elemezze azokat más, Azure Monitor által gyűjtött figyelési adatokkal, valamint más Azure Monitor funkciók, például riasztások és vizualizációk kihasználása érdekében. |
| [Event Hubs](resource-logs-stream-event-hubs.md) | A naplók küldésének Event Hubs lehetővé teszi az adatok továbbítását külső rendszerekre, például harmadik féltől származó SIEM-re és más log Analytics-megoldásokra. |
| [Azure Storage-fiók](resource-logs-collect-storage.md) | A naplók Azure Storage-fiókba való archiválása a naplózás, a statikus elemzés vagy a biztonsági mentés esetében hasznos. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Diagnosztikai beállítások létrehozása a Azure Portalban
A Azure Portal diagnosztikai beállításait a Azure Monitor menüből vagy az erőforrás menüjéből is konfigurálhatja.

1. A Azure Portal diagnosztikai beállításainak konfigurálása az erőforrástól függ.

   - Egyetlen erőforrás esetén kattintson a **diagnosztikai beállítások** elemre az erőforrás menüjében a **figyelés** lehetőség alatt.

        ![Diagnosztikai beállítások](media/diagnostic-settings/menu-resource.png)

    - Egy vagy több erőforrás esetében kattintson a Azure Monitor menü **Beállítások** területén található **diagnosztikai beállítások** elemre, majd kattintson az erőforrásra.
    
        ![Diagnosztikai beállítások](media/diagnostic-settings/menu-monitor.png)

    - A tevékenység naplójában kattintson a **Azure monitor** menüben a **tevékenység napló** elemre, majd a **diagnosztikai beállítások**lehetőségre. Győződjön meg arról, hogy letiltja a tevékenység naplójának örökölt konfigurációját. További részletek: a [meglévő beállítások letiltása](diagnostic-settings-legacy.md#disable-existing-settings) .

        ![Diagnosztikai beállítások](media/diagnostic-settings/menu-activity-log.png)

2. Ha a beállítások nem létezik az erőforráson kiválasztott, kéri létre beállítást. Kattintson a **diagnosztikai beállítás hozzáadása**elemre.

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

5. Jelölje be az egyes adatkategóriákhoz tartozó jelölőnégyzetet a megadott célhelyre való küldéshez. A kategóriák listája minden egyes Azure-szolgáltatás esetében eltérő lesz.

   > [!NOTE]
   > A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
   >
   > *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.

6. Kattintson a **Save** (Mentés) gombra.

Néhány pillanat elteltével megjelenik az új beállítás az erőforráshoz tartozó beállítások listájában, és a rendszer a naplókat a megadott célhelyre továbbítja az új esemény-adatforrások létrehozásakor. Vegye figyelembe, hogy egy esemény kibocsátása és a [log Analytics munkaterületen való megjelenése](data-ingestion-time.md)között akár tizenöt percet is igénybe vehet.



## <a name="create-diagnostic-settings-using-powershell"></a>Diagnosztikai beállítások létrehozása a PowerShell használatával
A [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) parancsmaggal hozzon létre diagnosztikai beállítást [Azure PowerShell](powershell-quickstart-samples.md)használatával. A parancsmag dokumentációját a paraméterek leírását ismertető cikkben találja.

> [!IMPORTANT]
> Ez a metódus nem használható az Azure-beli tevékenység naplójában. Ehelyett [hozzon létre diagnosztikai beállítást a Azure monitor Resource Manager-sablonnal](diagnostic-settings-template.md) egy Resource Manager-sablon létrehozásához és a PowerShell használatával történő telepítéséhez.

A következő példa egy PowerShell-parancsmagot tartalmaz a diagnosztikai beállítások mind a három célhely használatával történő létrehozásához.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Diagnosztikai beállítások létrehozása az Azure CLI-vel
Az az [monitor diagnosztikai-Settings Create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) paranccsal hozzon létre egy diagnosztikai beállítást az [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)használatával. A paraméterek leírását a parancs dokumentációjában találja.

> [!IMPORTANT]
> Ez a metódus nem használható az Azure-beli tevékenység naplójában. Ehelyett a [Azure monitor diagnosztikai beállítás használatával Resource Manager-sablonnal](diagnostic-settings-template.md) hozzon létre egy Resource Manager-sablont, és telepítse azt a CLI-vel.

A következő példa egy CLI-parancsot mutat be a diagnosztikai beállítások mind a három célhely használatával történő létrehozásához.



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

### <a name="configure-diagnostic-settings-using-rest-api"></a>Diagnosztikai beállítások konfigurálása REST API használatával
A diagnosztikai beállítások a [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/)használatával történő létrehozásához vagy frissítéséhez tekintse meg a [diagnosztikai beállításokat](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) .


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Diagnosztikai beállítások konfigurálása Resource Manager-sablon használatával
A diagnosztikai beállítások Resource Manager-sablonnal történő létrehozásához vagy frissítéséhez tekintse [meg a diagnosztikai beállítás létrehozása a Azure monitor erőforrás-kezelő sablonnal](diagnostic-settings-template.md) című témakört.

## <a name="next-steps"></a>Következő lépések

* [További információ az Azure platform naplóiról](platform-logs-overview.md)
