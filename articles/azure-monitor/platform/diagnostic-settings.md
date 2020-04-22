---
title: A diagnosztikai beállítások segítségével gyűjtse össze a platformmérő kretrikákat és naplókat, valamint az Azure-ban
description: Az Azure Monitor platformmetrikáit és naplóit diagnosztikai beállítással elküldheti az Azure Monitor-naplókba, az Azure storage-ba vagy az Azure Event Hubs-ba.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681167"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Diagnosztikai beállítás létrehozása az azure-beli erőforrásnaplók és metrikák összegyűjtéséhez

Az Azure-beli [platformnaplók,](platform-logs-overview.md) beleértve az Azure-tevékenységnaplót és az erőforrásnaplókat, részletes diagnosztikai és naplózási információkat nyújtanak az Azure-erőforrásokhoz és az Azure platformhoz, amelytől függenek. [A platformmetrikák](data-platform-metrics.md) at alapértelmezés szerint gyűjtik, és általában az Azure Monitor metrikák adatbázisában tárolják.

Ez a cikk részletesen ismerteti a diagnosztikai beállítások létrehozása és konfigurálása platform metrikák és platformnaplók küldése a különböző célhelyekre.

> [!IMPORTANT]
> Mielőtt létrehozna egy diagnosztikai beállítást a tevékenységnapló összegyűjtéséhez, először le kell tiltania az örökölt konfigurációkat. A részleteket [lásd: Azure-tevékenységnapló az örökölt beállításokkal.](diagnostic-settings-legacy.md)

Minden Azure-erőforráshoz saját diagnosztikai beállítás szükséges, amely a következő feltételeket határozza meg:

- A beállításban meghatározott célhelyekre küldött naplók és metrikaadatok kategóriái. A rendelkezésre álló kategóriák eltérőek lehetnek a különböző erőforrástípusoktól.
- Egy vagy több úti cél a naplók elküldéséhez. A jelenlegi úti célok közé tartozik a Log Analytics munkaterület, az Event Hubs és az Azure Storage.

Egyetlen diagnosztikai beállítás csak egy célt határozhat meg. Ha egy adott céltípus (például két különböző Log Analytics-munkaterület) egynél több tárolótípusra szeretne adatokat küldeni, akkor hozzon létre több beállítást. Minden erőforrás legfeljebb 5 diagnosztikai beállításokat.

> [!NOTE]
> [A platformmetrikák automatikusan](metrics-supported.md) kerülnek összegyűjtésre az [Azure Monitor metrikák ba.](data-platform-metrics.md) A diagnosztikai beállítások segítségével bizonyos Azure-szolgáltatások metrikákat gyűjthet az Azure Monitor Naplókba, hogy [a naplólekérdezések](../log-query/log-query-overview.md)használatával más figyelési adatokkal elemezhessék.

## <a name="destinations"></a>Célhelyek

A platformnaplók és a metrikák az alábbi táblázatban szereplő célhelyekre küldhetők. Az alábbi táblázatban található hivatkozásokat az adott célhelyre küldött adatokrészleteiért kövesse.

| Cél | Leírás |
|:---|:---|
| [Log Analytics-munkaterület](resource-logs-collect-workspace.md) | Naplók és metrikák gyűjtése egy Log Analytics-munkaterületle teszi, hogy elemezze őket más figyelési adatokat gyűjtött az Azure Monitor hatékony naplólekérdezések, valamint kihasználni más Azure Monitor funkciók, például a riasztások és vizualizációk. |
| [Eseményközpontok](resource-logs-stream-event-hubs.md) | Naplók és metrikák küldése az Event Hubs-ba lehetővé teszi az adatok streamelését külső rendszerekre, például külső SIEM-ekre és más naplóelemzési megoldásokra. |
| [Azure tárfiók](resource-logs-collect-storage.md) | Naplók és metrikák archiválása egy Azure storage-fiók naplózása, statikus elemzés vagy biztonsági mentés. Az Azure Monitor-naplókhoz és a Log Analytics-munkaterülethez képest az Azure storage kevésbé költséges, és a naplók határozatlan ideig tárolhatók. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Diagnosztikai beállítások létrehozása az Azure Portalon

Diagnosztikai beállításokat az Azure Portalon vagy az Azure Monitor menüből, vagy az erőforrás menüjéből.

1. Az Azure Portalon a diagnosztikai beállítások konfigurálásának helye az erőforrástól függ.

   - Egyetlen erőforrás esetén **kattintson** az erőforrás menüjének **Figyelő** gombjára.

        ![Diagnosztikai beállítások](media/diagnostic-settings/menu-resource.png)

   - Egy vagy több erőforrás esetén kattintson az Azure Monitor menü **Beállítások parancsának Diagnosztikai** **beállítások parancsára,** majd kattintson az erőforrásra.

      ![Diagnosztikai beállítások](media/diagnostic-settings/menu-monitor.png)

   - A tevékenységnaplóhoz kattintson a **Tevékenységnapló parancsra** az **Azure Monitor** menüben, majd a Diagnosztikai **beállítások parancsra.** Győződjön meg arról, hogy letiltja a tevékenységnapló örökölt konfigurációját. A részletekért olvassa el A [meglévő beállítások letiltása](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) témakört.

        ![Diagnosztikai beállítások](media/diagnostic-settings/menu-activity-log.png)

2. Ha a kijelölt erőforráson nincsenek beállítások, a program kéri egy beállítás létrehozását. Kattintson **a Diagnosztikai beállítás hozzáadása gombra.**

   ![Diagnosztikai beállítás hozzáadása - nincs meglévő beállítás](media/diagnostic-settings/add-setting.png)

   Ha az erőforráson vannak beállítások, megjelenik a már konfigurált beállítások listája. Új beállítás hozzáadásához kattintson a **Diagnosztikai beállítás hozzáadása** gombra, a Meglévő beállítás szerkesztéséhez a Szerkesztés **gombra** kattintva. Minden beállítás nak legfeljebb egy a céltípusok közül lehet.

   ![Diagnosztikai beállítás hozzáadása - meglévő beállítások](media/diagnostic-settings/edit-setting.png)

3. Adjon nevet a beállításnak, ha még nem rendelkezik ilyensel.

    ![Diagnosztikai beállítás hozzáadása](media/diagnostic-settings/setting-new-blank.png)

4. **Kategória részletei (mit kell irányítani)** - Jelölje be az egyes adatkategóriákat, amelyeket később szeretne elküldeni a később megadott célhelyekre. A kategóriák listája az egyes Azure-szolgáltatások tól függően változik.

     - **Az AllMetrics** az erőforrás platformmetrikáit az Azure Logs store-ba irányítja, de naplóformájában. Ezeket a metrikákat általában csak az Azure Monitor metrikák idősorozat-adatbázis. Elküldi őket az Azure Monitor Naplók tárolóba (amely a Log Analytics segítségével kereshető), hogy integrálja őket a lekérdezésekbe, amelyek más naplók között keresnek. Előfordulhat, hogy ez a beállítás nem érhető el minden erőforrástípushoz. Ha támogatott, az [Azure Monitor támogatott metrikák](metrics-supported.md) felsorolja, milyen metrikákat gyűjt, milyen erőforrás-típusok.

       > [!NOTE]
       > A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
       >
       > *Például:* Az "IOReadBytes" metrika egy blokkláncon lehet vizsgálni, és egy csomópont szinten. Diagnosztikai beállításokkal történő exportáláskor azonban az exportált metrika az összes csomópont összes olvasási bájtjaként felel meg.

     - **A naplók** az erőforrás típusától függően a rendelkezésre álló különböző kategóriákat sorolják fel. Ellenőrizze azokat a kategóriákat, amelyeket egy célállomásra szeretne irányítani.

5. **Cél adatok** – Jelölje be az egyes célállomások jelölőnégyzetét. Az egyes jelölőnégyzetek bejelöléséteknél a beállítások további információk hozzáadását teszik lehetővé.

      ![Küldés a Log Analytics vagy az Event Hubs szolgáltatásba](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** – Adja meg az előfizetést és a munkaterületet.  Ha nem rendelkezik munkaterülettel, a folytatás előtt létre kell [hoznia egyet.](../learn/quick-create-workspace.md)

    1. **Eseményközpontok** – Adja meg a következő feltételeket:
       - Az az előfizetés, amelynek az eseményközpont része
       - Az Eseményközpont névtere – Ha még nem rendelkezik ilyentel, [létre kell hoznia egyet](../../event-hubs/event-hubs-create.md)
       - Az eseményközpont neve (nem kötelező), amelynek az összes adatot el szeretné küldeni. Ha nem ad meg nevet, minden naplókategóriához létrejön egy eseményközpont. Ha több kategóriát küld, érdemes megadni egy nevet a létrehozott eseményközpontok számának korlátozására. A részletekért tekintse meg az [Azure Event Hubs kvótákat és -korlátozásokat.](../../event-hubs/event-hubs-quotas.md)
       - Az Event Hub-házirend (nem kötelező) A házirend határozza meg a streamelési mechanizmus engedélyeit. További információ: [Event-hubs-features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Tárolás** – Válassza ki az előfizetést, a tárfiókot és az adatmegőrzési szabályzatot.

        ![Küldés a tárolóba](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Fontolja meg az adatmegőrzési szabály 0-ra állítását, és manuálisan törölni az adatokat a tárolóból egy ütemezett feladat használatával, hogy elkerülje az esetleges zavart a jövőben.
        >
        > Először is, ha archiváláshoz használ tárhelyet, általában több mint 365 napig szeretné használni az adatokat. Másodszor, ha úgy dönt, egy adatmegőrzési szabály, amely nagyobb, mint 0, a lejárati dátum a naplókhoz lesz csatolva a tárolás időpontjában. A naplók dátuma nem módosítható a tárolás után.
        >
        > Ha például a *WorkflowRuntime* adatmegőrzési házirendet 180 napra állítja be, majd 24 órával később 365 napra állítja be, az első 24 órában tárolt naplók 180 nap után automatikusan törlődnek, míg az adott típusú további naplók 365 nap után automatikusan törlődnek. Az adatmegőrzési szabályzat későbbi módosítása nem teszi az első 24 órányi naplók 365 napig marad.

6. Kattintson a **Save** (Mentés) gombra.

Néhány pillanat múlva az új beállítás megjelenik az erőforrás beállításainak listájában, és a naplók streamelése a megadott célhelyekre, amikor új eseményadatok jönnek létre. Az esemény kibocsátása és a [Log Analytics-munkaterületen való megjelenhet](data-ingestion-time.md)között akár 15 perc is eltelhet.

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

- [További információ az Azure platform naplóiról](platform-logs-overview.md)
