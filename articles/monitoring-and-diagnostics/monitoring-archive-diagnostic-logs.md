---
title: Az Azure diagnosztikai naplók archiválása
description: Útmutató az Azure tárfiókokban hosszú távú megőrzési diagnosztikai naplók archiválása.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: d48828c8d2ec439f389fe4eddabb59599cc1680b
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752826"
---
# <a name="archive-azure-diagnostic-logs"></a>Az Azure diagnosztikai naplók archiválása

Ebben a cikkben megmutatjuk használatát az Azure-portálon, a PowerShell-parancsmagok, a parancssori felület vagy a REST API-t kell archiválnia a [Azure diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md) tárfiókokban. Ez a beállítás akkor hasznos, ha azt szeretné, hogy megőrzi a diagnosztikai naplók és a naplózási, statikus elemzési és biztonsági mentés egy választható adatmegőrzési. A tárfiók nem ugyanahhoz az előfizetéshez naplók kibocsátó mindaddig, amíg a beállítás konfigurálása felhasználó hozzáfér megfelelő RBAC mindkét előfizetéshez erőforrásként kell kell.

## <a name="prerequisites"></a>Előfeltételek

Kezdés előtt kell [hozzon létre egy tárfiókot](../storage/storage-create-storage-account.md) , amelyhez úgy archiválhatók a diagnosztikai naplókat. Erősen ajánlott, hogy nem használja a benne tárolt, így jobban szabályozhatja a hozzáférést a figyelési adatok, amelyeket más, nem figyelési adatokat tartalmazó meglévő tárfiókot. Azonban ha is archiválása a tevékenységnapló és diagnosztikai metrikák egy tárfiókba, logikus összes figyelési adatot elhelyez egy központi helyen, valamint a diagnosztikai naplók tárolási fiók használatával. A storage-fiók használata egy általános célú tárfiókkal, nem a blob storage-fiók kell lennie.

> [!NOTE]
>  Jelenleg nem archiválhatja adatok tárolási funkciókat biztosító fiókot, amellyel mögött egy védett virtuális hálózatot.

## <a name="diagnostic-settings"></a>Diagnosztikai beállítások

Az alábbi módszereket a diagnosztikai naplók archiválása, állítsa be a **diagnosztikai beállításának** egy adott erőforráshoz. Egy erőforrás diagnosztikai beállításának meghatározza a naplókat, valamint a metrika adatokat küldeni a célhelyre (storage-fiók, az Event Hubs névtér vagy Naplóelemzési). Az események minden egyes napló kategória és a storage-fiókban tárolt metrika értékét az adatmegőrzési (a megőrizni kívánt napok száma) is meghatároz. Ha egy megőrzési házirend nullára van beállítva, események naplózási kategóriát tárolja határozatlan ideig (azaz, végtelen). Adatmegőrzési ellenkező esetben lehet bármely 1 és 2147483647 között eltelt napok számát. [További diagnosztikai beállítások itt kapcsolatos](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings). Adatmegőrzési alkalmazott napi,, így napi (UTC) szerint naplókat, amelyik most már a megőrzési túl napjától végén házirend törlődni fog. Például ha egy nap adatmegőrzési, mai nap kezdetén a napló, a nap előtt tegnap törlése akkor történik meg. A törlési folyamat kezdődik éjfél UTC, de vegye figyelembe, hogy törli a tárfiókot az naplók akár 24 óráig is eltarthat. 

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="archive-diagnostic-logs-using-the-portal"></a>Archív diagnosztikai naplókat a portálon

1. A portál Azure figyelő keresse meg és kattintson a **diagnosztikai beállítások**

    ![Figyelés szakaszban Azure-figyelő](media/monitoring-archive-diagnostic-logs/diagnostic-settings-blade.png)

2. Opcionálisan erőforráscsoport és erőforrások típus szerint a lista szűréséhez, majd kattintson az erőforráson, amelynek szeretné beállítani a diagnosztikai.

3. Ha a beállítások nem található az erőforrás a választott, kéri beállítás létrehozása. Kattintson a "Diagnosztika bekapcsolásához."

   ![Diagnosztikai beállítás - nincsenek meglévő beállítások hozzáadása](media/monitoring-archive-diagnostic-logs/diagnostic-settings-none.png)

   Ha az erőforrás-meglévő beállítások, látni fogja már ehhez az erőforráshoz konfigurált beállítások listája. A "Hozzáadás diagnosztikai beállításának."

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/monitoring-archive-diagnostic-logs/diagnostic-settings-multiple.png)

3. Adjon a beállítás a neve, és jelölje be a **Tárfiók exportálása**, majd válasszon egy tárfiókot. Beállíthatja a napokban ezek a naplók segítségével számos a **megőrzés (nap)** csúszkák. Egy nulla napos megőrzési határozatlan ideig tárolja a naplókat.

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/monitoring-archive-diagnostic-logs/diagnostic-settings-configure.png)

4. Kattintson a **Save** (Mentés) gombra.

Néhány másodpercen belül az új beállítás jelenik meg az ehhez az erőforráshoz beállítások listáját, és diagnosztikai naplók archivált, hogy a tárfiókot, amint létrejön az új esemény-adatokat.

## <a name="archive-diagnostic-logs-via-azure-powershell"></a>Diagnosztikai naplók archiválása Azure PowerShell használatával

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| ResourceId |Igen |Erőforrás-azonosító az erőforrás, amelyen be szeretné állítani a diagnosztikai beállítását. |
| StorageAccountId |Nem |A diagnosztikai naplók mentésére tárfiók erőforrás-azonosító. |
| Kategóriák |Nem |Ahhoz, hogy a napló kategóriák vesszővel tagolt listája. |
| Engedélyezve |Igen |Logikai érték, amely jelzi, hogy diagnosztika engedélyezett vagy letiltott ezen az erőforráson. |
| RetentionEnabled |Nem |Logikai érték, amely azt jelzi, hogy engedélyezve vannak-e adatmegőrzési ezen az erőforráson. |
| RetentionInDays |Nem |Amely események kell megtartani 1 és 2147483647 közötti napok számát. A nulla érték határozatlan ideig tárolja a naplókat. |

## <a name="archive-diagnostic-logs-via-the-azure-cli-20"></a>Archív diagnosztikai naplók az Azure CLI 2.0 keresztül

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

Hozzáadhat további kategóriákra a diagnosztikai naplófájl szótárak ad hozzá a JSON-tömb, mint az átadott a `--logs` paraméter.

A `--resource-group` argumentum csak akkor kötelező, ha `--storage-account` nincs egy objektumot. Az archiválási Storage diagnosztikai naplók teljes dokumentációjáért lásd: a [CLI parancsdokumentációja](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Diagnosztikai naplók archiválása a REST API-n keresztül

[Lásd a jelen dokumentum](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings) olvashat, hogyan állíthat be egy diagnosztikai beállítás a Azure REST API használatával.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>A tárfiók a diagnosztikai naplók sémája

Miután állított be archiválási, tárolót jön létre a tárfiók, amint az esemény akkor következik be, a napló kategóriák engedélyezte. A tárolóban található blobok ugyanazt a formátumot kövesse a diagnosztikai naplók és a műveletnapló között. A blobok szerkezete van:

> insights - logs-{napló kategória neve} / resourceId = / ELŐFIZETÉSEK / {előfizetés-azonosító} /RESOURCEGROUPS/ {erőforráscsoport neve} /PROVIDERS/ {erőforrás-szolgáltató neve} / {resource type} / {erőforrás neve} / y = {négyjegyű numerikus year} / m = {kétjegyű numerikus month} / d {kétjegyű számozott napja} = / h = {kétjegyű 24 órás hour}/m=00/PT1H.json

Vagy egyszerűbb,

> insights - logs-{napló kategória neve} / resourceId = / {erőforrás-azonosítót} / y = {négyjegyű numerikus year} / m = {kétjegyű numerikus month} / d {kétjegyű számozott napja} = / h = {kétjegyű 24 órás hour}/m=00/PT1H.json

A blob neve lehet, például:

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json

Mindegyik PT1H.json blob tartalmazza a blob URL-jében meghatározott órában (például h=12) bekövetkezett események JSON-blobját. Az aktuális órában az események az előfordulásukkor lesznek a PT1H.json fájlhoz fűzve. A perc értéket (m = 00) mindig 00, mivel diagnosztikai alkalmazásnapló-események az egyes blobok óránként van felosztva.

PT1H.json fájlon belül mindegyik esemény tárolja a "rekordok" tömb, a következő formátumban:

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Elem neve | Leírás |
| --- | --- |
| time |Az esemény az esemény megfelelő a kérés feldolgozása az Azure-szolgáltatás által kiváltott idejét jelző időbélyegző. |
| resourceId |Erőforrás-azonosító az érintett erőforrás. |
| operationName |A művelet neve. |
| category |Az esemény napló kategóriáját. |
| properties |Állítsa be a `<Key, Value>` az esemény részleteit leíró párok (azaz szótárában). |

> [!NOTE]
> A tulajdonságok és ezek a tulajdonságok használatát eltérőek lehetnek attól függően, hogy az erőforrás.

## <a name="next-steps"></a>További lépések

* [Elemzés blobok letöltése](../storage/storage-dotnet-how-to-use-blobs.md)
* [Az adatfolyam diagnosztikai naplók az Event Hubs névtérhez](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Tudjon meg többet a diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md)
