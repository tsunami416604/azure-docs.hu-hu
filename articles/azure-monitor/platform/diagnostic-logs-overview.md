---
title: Az Azure diagnosztikai naplók áttekintése
description: Ismerje meg, Mik azok az Azure diagnosztikai naplók, és hogyan használhatja őket egy Azure-erőforrás belül előforduló események megértéséhez.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: e9fcf36d6ece441c73e7d1224bd5918d2e74bf84
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56001980"
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Gyűjtése és felhasználása a naplófájlok adatait az Azure-erőforrások

## <a name="what-are-azure-monitor-diagnostic-logs"></a>Mik az Azure Monitor-diagnosztikai naplók

**Az Azure Monitor-diagnosztikai naplók** adja meg a szolgáltatás működésével kapcsolatos részletes, gyakori adatokat egy Azure-szolgáltatás által kibocsátott naplók. Az Azure Monitor lehetővé teszi a diagnosztikai naplók elérhető két típusát:
* **A bérlői naplók** – ezeket a naplókat a bérlői szintű szolgáltatások, amelyek az Azure-előfizetéssel, kívüliek biztosítja, mint például az Azure Active Directory-naplók.
* **Erőforrás-naplók** – Azure-szolgáltatások üzembe helyezése az Azure-előfizetéssel, például a hálózati biztonsági csoportok vagy a Storage-fiókok belüli erőforrások származnak ezek a naplók.

    ![Erőforrás diagnosztikai naplók és más típusú naplók ](./media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

Ezek a naplók a tartalom az Azure szolgáltatás- és erőforrás típusa szerint változó. Például a hálózati biztonsági csoport szabályainak számlálói, illetve a Key Vault-naplók két típusba diagnosztikai naplók.

Ezek a naplók különböznek a [tevékenységnapló](activity-logs-overview.md). A tevékenységnaplóban a műveletek a Resource Manager használatával, például egy virtuális gépet hoz létre, vagy a logikai alkalmazás törlése az előfizetés erőforrásain végzett betekintést nyújt. A tevékenységnapló előfizetés-szintű napló. Diagnosztikai naplók erőforrásszintű cybercrime végrehajtott műveletekkel belül az adott erőforráshoz, például a Key Vault titkos kulcs lekérése.

Ezek a naplók is a Vendég operációsrendszer-szintű diagnosztikai naplók különböznek. A vendég operációs rendszer a diagnosztikai naplók, ezek futhat virtuális gépen futó ügynök által gyűjtött vagy egyéb támogatott erőforrástípus. Diagnosztikai naplók erőforrásszintű nincs ügynök és a rögzítés erőforrás-specifikus adatok az Azure platform, igényelnek, miközben a Vendég operációsrendszer-szintű diagnosztikai naplók rögzítése az operációs rendszer és a egy virtuális gépen futó alkalmazások adatait.

Nem minden szolgáltatás támogatja a diagnosztikai naplók, az itt leírtak szerint. [Ez a cikk tartalmazza, mely szolgáltatásokat támogatja a diagnosztikai naplók szakasz listaelem](./../../azure-monitor/platform/diagnostic-logs-schema.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>Mire képes a diagnosztikai naplók
Az alábbiakban néhány, a diagnosztikai naplók segítségével teheti:

![Diagnosztikai naplók logikai elhelyezése](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* Menti azokat egy [ **Tárfiók** ](../../azure-monitor/platform/archive-diagnostic-logs.md) naplózási vagy manuális ellenőrzést. A megőrzési ideje (nap) használatával is megadhat **erőforrás diagnosztikai beállításait**.
* [Azokat a Stream **az Event Hubs** ](diagnostic-logs-stream-event-hubs.md) egy külső szolgáltatás vagy az egyéni elemzési megoldással, például a Power bi támogatunk.
* Elemezheti a [Azure Monitor](../../azure-monitor/platform/collect-azure-metrics-logs.md), ahol az adatok írása közvetlenül az Azure monitornak és nincs szükség, először a tárolás az adatokat írni.  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Használhatja a storage-fiók vagy az Event Hubs-névtér, amely nem ugyanabban az előfizetésben, mint a naplókat kibocsátó. A beállítást konfiguráló felhasználónak rendelkeznie kell a megfelelő RBAC-hozzáférés mindkét előfizetéshez.

> [!NOTE]
>  Hálózati forgalmi naplók tárfiókba mögötti egy biztonságos virtuális hálózaton jelenleg nem archiválhatja.

> [!WARNING]
> A tárfiókban lévő naplóadatok formátuma 2018. nov. 1-től JSON Lines lesz. [Ebben a cikkben olvashat ennek hatásairól, valamint arról, hogy hogyan frissítheti eszközeit az új formátum kezeléséhez.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

## <a name="diagnostic-settings"></a>Diagnosztikai beállítások

Erőforrás-diagnosztikai naplók vannak konfigurálva az erőforrás diagnosztikai beállításait használja. Bérlő diagnosztikai naplók egy bérlő diagnosztikai beállítás használatával konfigurálhatók. **Diagnosztikai beállítások** szolgáltatás vezérlőelem:

* Ha a diagnosztikai naplók és mérőszámok érkeznek (Storage-fiók, az Event Hubs, illetve az Azure Monitor).
* Melyik naplókategóriák küldik, és hogy metrikaadatok is elküldi a rendszer.
* Mennyi ideig minden naplókategória megőrződjön-e a storage-fiókban
    - Egy nulla napnyi adatmegőrzéshez azt jelenti, hogy naplókat tartják örökre. Ellenkező esetben az érték lehet minden olyan 1 és 2147483647 között eltelt napok számát.
    - Ha a megőrzési házirend-beállításokat, de a naplók tárolása a Storage-fiók le van tiltva, (például, ha csak az Event Hubs és a Log Analytics lehetőség be van jelölve), az adatmegőrzési szabályzatok nem befolyásolják.
    - Adatmegőrzési házirendek, az alkalmazott napi, hogy naponta (UTC), naplók, amely mostantól a megőrzési ideje meghaladja a nap végén törli a házirendet. Például ha egy nap adatmegőrzési, ma a nap kezdetén az a napja előtt tegnap naplóinak törlődnének. A törlési folyamat kezdődik UTC szerint éjfélig, de vegye figyelembe, hogy a naplók a tárfiókból a törlendő akár 24 órát is igénybe vehet.

Ezek a beállítások egyszerűen az Azure PowerShell és CLI-parancsokkal portál vagy a használatával a diagnosztikai beállítások vannak konfigurálva a [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Az Eseményközpont "Bejövő üzenetek" metrikája fel és ábrázolható egy üzenetsoronként. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Diagnosztikai naplók gyűjtésének engedélyezése

Diagnosztikai naplók gyűjtésének engedélyezhető [erőforrás létrehozása a Resource Manager-sablon részeként](./../../azure-monitor/platform/diagnostic-logs-stream-template.md) vagy egy erőforrás létrehozása a portálon, hogy erőforrás oldaláról után. Gyűjtemény bármely pontján, az Azure PowerShell vagy parancssori felület parancsai, vagy az Azure Monitor REST API használatával is engedélyezheti.

> [!TIP]
> Ezek az utasítások nem alkalmazható erre a minden erőforrás. Tekintse meg a séma hivatkozásokat, amelyek érvényesek az egyes erőforrástípusok különleges lépések megértéséhez a lap alján.

### <a name="enable-collection-of-diagnostic-logs-in-the-portal"></a>A portálon a diagnosztikai naplók gyűjtésének engedélyezéséhez

Erőforrás létrehozása vagy egy adott erőforrás zárolását, vagy az Azure Monitor után gyűjteménye, erőforrás-diagnosztikai naplók az Azure Portalon engedélyezheti. Ennek engedélyezéséhez az Azure Monitor-n keresztül:

1. Az a [az Azure portal](https://portal.azure.com), keresse meg az Azure Monitor, majd kattintson a **diagnosztikai beállítások**

    ![Az Azure Monitor figyelési szakasza](media/diagnostic-logs-overview/diagnostic-settings-blade.png)

2. Igény szerint szűrje a listát, erőforráscsoport vagy erőforrás típusa, majd kattintson az a erőforrás, amelynek szeretné egy diagnosztikai beállítás.

3. Ha a beállítások nem létezik az erőforráson kiválasztott, kéri létre beállítást. Kattintson a "Engedélyezze a diagnosztikát."

   ![Diagnosztikai beállítás - beállítások nélkül hozzáadása](media/diagnostic-logs-overview/diagnostic-settings-none.png)

   Ha meglévő beállításokat az erőforráson, látni fogja a beállítások már konfigurálva van a ehhez az erőforráshoz. Kattintson a "Diagnosztikai beállítás hozzáadása."

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/diagnostic-logs-overview/diagnostic-settings-multiple.png)

3. Adja meg a beállítás a neve, jelölje be a minden célhelyéhez, amely adatokat küldhet, és konfigurálja, hogy melyik erőforrást használja minden cél szeretné. Beállíthatja a megőrzi ezeket a naplókat a napok számát a **megőrzés (nap)** csúszkák (a storage-fiók cél csak érvényes). Egy nulla napnyi adatmegőrzéshez határozatlan ideig tárolja a naplókat.

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/diagnostic-logs-overview/diagnostic-settings-configure.png)

4. Kattintson a **Save** (Mentés) gombra.

Néhány pillanat múlva az új beállítás jelenik meg az ehhez az erőforráshoz beállítások listáját, és a diagnosztikai naplók érkeznek a megadott helyre, amint új esemény adat keletkezik.

A bérlői diagnosztikai beállítások csak konfigurálható a portál panelén, a bérlő szolgáltatás – ezek a beállítások nem jelennek meg az Azure Monitor diagnosztikai beállítások panelen. Például az Azure Active Directory naplóinak kattintva vannak konfigurálva a **adatok exportálási beállítások** az Auditnaplók panelen.

![AAD-diagnosztikai beállítások](./media/diagnostic-logs-overview/diagnostic-settings-aad.png)

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>PowerShell erőforrás-diagnosztikai naplók gyűjtésének engedélyezéséhez

Ahhoz, hogy az erőforrás-diagnosztikai naplók az Azure Powershellen keresztül gyűjteménye, használja a következő parancsokat:

Ahhoz, hogy a diagnosztikai naplókat egy tárfiókban, használja ezt a parancsot:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

A tárfiók azonosítója, amelyhez is szeretne küldeni a naplókat a storage-fiók erőforrás-azonosító.

Diagnosztikai naplók egy eseményközpontba streamelésének engedélyezéséhez használja ezt a parancsot:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

A service bus Szabályazonosító egy karakterláncérték, ebben a formátumban: `{Service Bus resource ID}/authorizationrules/{key name}`.

A Log Analytics-munkaterület-diagnosztikai naplók küldése engedélyezéséhez használja ezt a parancsot:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Az erőforrás-Azonosítóját a Log Analytics-munkaterület a következő paranccsal szerezheti be:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Kombinálhatja ezeket a paramétereket, több kimeneti beállítások engedélyezéséhez.

Azure PowerShell-lel bérlői diagnosztikai beállítások jelenleg nem konfigurálhatja.

### <a name="enable-collection-of-resource-diagnostic-logs-via-the-azure-cli"></a>Az Azure CLI-n keresztül erőforrás-diagnosztikai naplók gyűjtésének engedélyezéséhez

Ahhoz, hogy az erőforrás-diagnosztikai naplók az Azure CLI-n keresztül gyűjteménye, használja a [az monitor diagnostic-settings létrehozása](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) parancsot.

Diagnosztikai naplókat egy tárfiókban engedélyezése:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

A `--resource-group` argumentum csak akkor kötelező, ha `--storage-account` nem egy objektumot.

Diagnosztikai naplók egy eseményközpontba streamelésének engedélyezéséhez:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

A szabály azonosítója egy karakterláncérték, ebben a formátumban: `{Service Bus resource ID}/authorizationrules/{key name}`.

Ahhoz, hogy a Log Analytics-munkaterület-diagnosztikai naplók küldése:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

A `--resource-group` argumentum csak akkor kötelező, ha `--workspace` nem egy objektum-azonosító

Bármely paranccsal adhat hozzá további kategóriát a diagnosztikai napló szótárak ad hozzá a JSON-tömböt adhatók be a `--logs` paraméter. Kombinálhatja a `--storage-account`, `--event-hub`, és `--workspace` paraméterek több kimeneti beállítások engedélyezéséhez.

A parancssori felületről bérlői diagnosztikai beállítások jelenleg nem konfigurálhatja.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>REST API-n keresztül erőforrás-diagnosztikai naplók gyűjtésének engedélyezéséhez

Az Azure Monitor REST API használatával diagnosztikai beállítások módosításához tekintse meg a [ebben a dokumentumban](https://docs.microsoft.com/rest/api/monitor/).

Jelenleg nem konfigurálhatja bérlői diagnosztikai beállítások az Azure Monitor REST API használatával.

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Kezelheti az erőforrás diagnosztikai beállításait a portálon

Győződjön meg arról, hogy az összes erőforrás legyenek beállítva a diagnosztikai beállítások. Navigáljon a **figyelő** a portálon és a nyílt **diagnosztikai beállítások**.

![Diagnosztikai naplók panel a portálon](./media/diagnostic-logs-overview/diagnostic-settings-nav.png)

Kattintson az "Összes szolgáltatások" lehet a Monitor terület található.

Itt megtekintheti és szűrheti az összes olyan erőforrást, amely támogatja a diagnosztikai beállítások használatával ellenőrizheti, hogy engedélyezve van a diagnosztika. Lásd: is részletezéssel, ha a beállítások több erőforrás van beállítva, és ellenőrizze, melyik storage-fiók, az Event Hubs-névtér, illetve az adatok átvitele a Log Analytics-munkaterület.

![Diagnosztikai naplók eredmények a portál](./media/diagnostic-logs-overview/diagnostic-settings-blade.png)

Diagnosztikai beállítás hozzáadása megjeleníti a diagnosztikai beállítások nézet, ahol engedélyezhetnek, letilthatnak vagy módosíthatja a kiválasztott erőforrás diagnosztikai beállításait.

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Támogatott szolgáltatások, kategóriák és sémák a diagnosztikai naplók

[Ebben a cikkben](../../azure-monitor/platform/diagnostic-logs-schema.md) támogatott szolgáltatások, valamint naplókategóriák és ezek a szolgáltatások által használt sémák teljes listáját.

## <a name="next-steps"></a>További lépések

* [Az erőforrás-diagnosztikai naplók Stream **az Event Hubs**](diagnostic-logs-stream-event-hubs.md)
* [Módosítsa az erőforrás diagnosztikai beállításait az Azure Monitor REST API használatával](https://docs.microsoft.com/rest/api/monitor/)
* [Az Azure Monitor szolgáltatással az Azure storage-naplók elemzése](collect-azure-metrics-logs.md)
