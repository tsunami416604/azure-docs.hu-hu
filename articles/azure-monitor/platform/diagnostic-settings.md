---
title: Diagnosztikai beállítások használatával gyűjthet platform metrikákat és naplókat, valamint az Azure-ban
description: Diagnosztikai beállítás használatával Azure Monitor platform metrikáit és naplóit Azure Monitor naplókba, Azure Storage-ba vagy Azure-ba Event Hubs.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681167"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Diagnosztikai beállítás létrehozása az erőforrás-naplók és-metrikák gyűjtéséhez az Azure-ban

Az Azure [platform-naplói](platform-logs-overview.md) , beleértve az Azure-tevékenység naplóját és az erőforrás-naplókat, részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokhoz és az Azure-platformtól függenek. A [platform metrikáit](data-platform-metrics.md) a rendszer alapértelmezés szerint gyűjti, és általában a Azure monitor metrikai adatbázisban tárolja.

Ez a cikk a diagnosztikai beállítások létrehozásával és konfigurálásával kapcsolatos részletes információkat tartalmaz a platform metrikáinak és a platformok naplóinak különböző célhelyekre küldéséhez.

> [!IMPORTANT]
> Mielőtt diagnosztikai beállítást hozna létre a műveletnapló összegyűjtéséhez, először le kell tiltania az örökölt konfigurációkat. A részletekért lásd: az [Azure-tevékenység naplójának összegyűjtése örökölt beállításokkal](diagnostic-settings-legacy.md) .

Minden Azure-erőforráshoz saját diagnosztikai beállítás szükséges, amely a következő feltételeket határozza meg:

- A beállításban meghatározott célhelyekre továbbított naplók és metrikai adatok kategóriái. A rendelkezésre álló kategóriák a különböző erőforrástípusok esetében eltérőek lesznek.
- Egy vagy több célhely a naplók elküldéséhez. Az aktuális célhelyek közé tartozik Log Analytics munkaterület, Event Hubs és az Azure Storage.

Egyetlen diagnosztikai beállítás a célhelyek közül legfeljebb egyet tud meghatározni. Ha szeretne adatokat küldeni egy adott célhelyhez (például két különböző Log Analytics-munkaterületre), akkor hozzon létre több beállítást. Minden erőforrás legfeljebb 5 diagnosztikai beállítással rendelkezhet.

> [!NOTE]
> A rendszer automatikusan gyűjti a [platform metrikáit](metrics-supported.md) [Azure monitor metrikákat](data-platform-metrics.md). A diagnosztikai beállításokkal bizonyos Azure-szolgáltatások metrikáit gyűjthetik be Azure Monitor naplókba más figyelési adatokkal való elemzéshez [a naplók használatával.](../log-query/log-query-overview.md)

## <a name="destinations"></a>Célhelyek

A platform naplói és metrikái a következő táblázatban található célhelyekre küldhetők. Az alábbi táblázatban szereplő hivatkozásokra kattintva megtekintheti az adatok erre a célra történő küldésének részleteit.

| Cél | Leírás |
|:---|:---|
| [Log Analytics-munkaterület](resource-logs-collect-workspace.md) | A naplók és metrikák egy Log Analytics munkaterületre való összegyűjtése lehetővé teszi, hogy a Azure Monitor által gyűjtött más figyelési adatokkal elemezze azokat a hatékony naplózási lekérdezésekkel, valamint más Azure Monitor funkciók, például riasztások és vizualizációk kihasználása érdekében. |
| [Event hubok](resource-logs-stream-event-hubs.md) | A naplók és metrikák küldésének Event Hubs lehetővé teszi az adatok továbbítását külső rendszerekre, például harmadik féltől származó SIEM-re és más log Analytics-megoldásokra. |
| [Azure Storage-fiók](resource-logs-collect-storage.md) | A naplók és mérőszámok Azure Storage-fiókba való archiválása hasznos lehet a naplózás, a statikus elemzés vagy a biztonsági mentés során. Azure Monitor naplókhoz és egy Log Analytics munkaterülethez képest az Azure Storage kevésbé költséges, és a naplók határozatlan ideig tarthatók. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Diagnosztikai beállítások létrehozása a Azure Portalban

A Azure Portal diagnosztikai beállításait a Azure Monitor menüből vagy az erőforrás menüjéből is konfigurálhatja.

1. A Azure Portal diagnosztikai beállításainak konfigurálása az erőforrástól függ.

   - Egyetlen erőforrás esetén kattintson a **diagnosztikai beállítások** elemre az erőforrás menüjében a **figyelés** lehetőség alatt.

        ![Diagnosztikai beállítások](media/diagnostic-settings/menu-resource.png)

   - Egy vagy több erőforrás esetében kattintson a Azure Monitor menü **Beállítások** területén található **diagnosztikai beállítások** elemre, majd kattintson az erőforrásra.

      ![Diagnosztikai beállítások](media/diagnostic-settings/menu-monitor.png)

   - A tevékenység naplójában kattintson a **Azure monitor** menüben a **tevékenység napló** elemre, majd a **diagnosztikai beállítások**lehetőségre. Győződjön meg arról, hogy letiltja a tevékenység naplójának örökölt konfigurációját. További részletek: a [meglévő beállítások letiltása](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) .

        ![Diagnosztikai beállítások](media/diagnostic-settings/menu-activity-log.png)

2. Ha nem találhatók beállítások a kiválasztott erőforráson, a rendszer kéri, hogy hozzon létre egy beállítást. Kattintson a **diagnosztikai beállítás hozzáadása**elemre.

   ![Diagnosztikai beállítás hozzáadása – nincsenek meglévő beállítások](media/diagnostic-settings/add-setting.png)

   Ha vannak meglévő beállítások az erőforráson, a már konfigurált beállítások listája jelenik meg. Kattintson a **diagnosztikai beállítás hozzáadása** lehetőségre egy új beállítás hozzáadásához vagy egy meglévő **szerkesztési beállítás** megadásához. Az egyes beállítások nem lehetnek többek között a célhelyek közül.

   ![Diagnosztikai beállítások hozzáadása – meglévő beállítások](media/diagnostic-settings/edit-setting.png)

3. Adja meg a beállítás nevét, ha még nem rendelkezik ilyennel.

    ![Diagnosztikai beállítás hozzáadása](media/diagnostic-settings/setting-new-blank.png)

4. **Kategória részletei (mit kell átirányítani)** – jelölje be a jelölőnégyzetet minden olyan adatkategória esetében, amelyet később szeretne elküldeni a célhelyekre. A kategóriák listája az egyes Azure-szolgáltatásokra változik.

     - A **AllMetrics** az erőforrás platformjának mérőszámait az Azure naplók tárolójába irányítja, de a napló formájában. Ezeket a mérőszámokat általában csak a Azure Monitor metrikák idősorozat-adatbázisába küldik. Küldje el őket a Azure Monitor logs Store-ba (amely Log Analytics használatával kereshető), hogy integrálja azokat olyan lekérdezésekben, amelyek más naplókon keresztül keresnek. Lehetséges, hogy ez a beállítás nem érhető el az összes erőforrástípus esetében. Ha ez támogatott, [Azure monitor támogatott mérőszámok](metrics-supported.md) felsorolják, hogy milyen mérőszámokat gyűjt a rendszer a milyen típusú erőforrásokhoz.

       > [!NOTE]
       > A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
       >
       > *Például*: az "IOReadBytes" metrika egy Blockchain megvizsgálható és feldolgozható a csomópontok szintjén. A diagnosztikai beállításokon keresztüli exportáláskor azonban az exportált metrika az összes csomópont olvasási bájtjaiként jelenik meg.

     - A **naplók** az erőforrástípus típusától függően az elérhető különböző kategóriákat listázza. Tekintse át a célhelyre átirányítani kívánt kategóriákat.

5. **Célhely részletei** – jelölje be az egyes célhelyek jelölőnégyzetét. Ha bejelöli az egyes mezők jelölőnégyzetét, úgy tűnik, hogy további információkat adhat hozzá.

      ![Küldés Log Analytics vagy Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** – adja meg az előfizetést és a munkaterületet.  Ha nem rendelkezik munkaterülettel, a [továbblépés előtt létre kell hoznia egyet](../learn/quick-create-workspace.md).

    1. **Event hubok** – Itt adhatja meg a következő feltételeket:
       - Az az előfizetés, amely az Event hub részét képezi
       - Az Event hub-névtér – ha még nem rendelkezik ilyennel, [létre kell hoznia egyet](../../event-hubs/event-hubs-create.md)
       - Az Event hub neve (nem kötelező) az összes érték küldéséhez. Ha nem ad meg nevet, a rendszer minden egyes naplózási kategóriához létrehoz egy Event hub-t. Ha több kategóriát küld, érdemes megadnia egy nevet a létrehozott Event hubok számának korlátozásához. A részletekért tekintse meg az [Azure Event Hubs kvótáit és korlátozásait](../../event-hubs/event-hubs-quotas.md) .
       - Az Event hub szabályzata (nem kötelező) a szabályzat meghatározza a folyamatos átviteli mechanizmushoz szükséges engedélyeket. További információ: [Event-hubok-features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Storage** – válassza ki az előfizetést, a Storage-fiókot és a megőrzési szabályzatot.

        ![Küldés a tárolóba](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Érdemes lehet 0-ra beállítani az adatmegőrzési szabályt, és manuálisan törölni az adatait a tárterületről egy ütemezett feladatokkal, hogy elkerülje a jövőben felhasználható félreértéseket.
        >
        > Először is, ha a Storage-ot archiválásra használja, az adatait általában több mint 365 napig érdemes megtekinteni. Másodszor, ha a 0 értéknél nagyobb adatmegőrzési szabályt választ, a lejárati dátum a tároláskor a naplókhoz van csatolva. A naplók dátuma a tárolás után nem módosítható.
        >
        > Ha például a *WorkflowRuntime* 180 napra állítja be az adatmegőrzési szabályzatot, majd 24 órával később beállítja azt 365 napra, akkor az első 24 órában tárolt naplók automatikusan törlődnek a 180 nap után, míg az adott típusú összes további napló automatikusan törlődik a 365 nap után. Az adatmegőrzési szabályzat későbbi módosítása nem teszi elérhetővé a naplók első 24 óráját 365 napig.

6. Kattintson a **Save** (Mentés) gombra.

Néhány pillanat elteltével megjelenik az új beállítás az erőforráshoz tartozó beállítások listájában, és a rendszer a naplókat a megadott célhelyre továbbítja az új esemény-adatforrások létrehozásakor. Egy esemény kibocsátásakor akár 15 percet is igénybe vehet, amikor megjelenik [egy log Analytics munkaterületen](data-ingestion-time.md).

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

## <a name="next-steps"></a>További lépések

- [További információ az Azure platform naplóiról](platform-logs-overview.md)
