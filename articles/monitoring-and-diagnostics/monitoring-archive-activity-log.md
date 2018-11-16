---
title: Az Azure tevékenységnapló archiválása
description: Hosszú távú adatmegőrzés storage-fiókban található az Azure-tevékenységnapló archiválása.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 33681c7c9e1a625757e3f9403820ed3f469bec64
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705786"
---
# <a name="archive-the-azure-activity-log"></a>Az Azure tevékenységnapló archiválása
Ebben a cikkben bemutatjuk a használatáról az Azure portal, PowerShell-parancsmagok vagy többplatformos parancssori felület archiválása a [ **Azure-tevékenységnapló** ](monitoring-overview-activity-logs.md) a storage-fiókban. Ez a beállítás akkor hasznos, ha szeretné megőrizni a naplózási, statikus elemzési és biztonsági mentés (, teljes körűen felügyelve az adatmegőrzési) 90 napnál hosszabb ideig Tevékenységnaplót. Ha csak szeretné megőrizni az események 90 napig, vagy kisebb, nem kell állítania archiválás tárfiókba, mivel a tevékenységnapló eseményei vannak az Azure platformon 90 napig őrizzük meg engedélyezése archiválás nélkül.

> [!WARNING]
> A tárfiókban lévő naplóadatok formátuma 2018. nov. 1-től JSON Lines lesz. [Ebben a cikkben olvashat ennek hatásairól, valamint arról, hogy hogyan frissítheti eszközeit az új formátum kezeléséhez.](./monitor-diagnostic-logs-append-blobs.md) 
>
> 

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, kell [hozzon létre egy tárfiókot](../storage/common/storage-quickstart-create-account.md) , amelyhez a Tevékenységnaplót archiválhatja. Kifejezetten ajánljuk, hogy nem használja egy meglévő tárfiókot, amely a benne tárolt, így jobban szabályozhatja a hozzáférést a figyelési adatok más, nem figyelési adatokat tartalmaz. Azonban ha is archiválása a diagnosztikai naplók és mérőszámok egy tárfiókba, célszerű a storage-fiókját a tevékenységnapló használatával az összes monitorozási adat ne egy központi helyen felhasználójának. A storage-fiók nem rendelkezik a mindaddig, amíg a beállítást konfiguráló felhasználónak mindkét előfizetéshez megfelelő RBAC-hozzáféréssel rendelkezik a naplókat kibocsátó előfizetésnek az azonos előfizetésben kell.

> [!NOTE]
>  Az adatokat egy biztonságos virtuális hálózaton mögött létrehozott tárfiók jelenleg nem archiválhatja.

## <a name="log-profile"></a>Naplóprofil
Az alábbi módszerek bármelyikével a tevékenységnapló archiválása, állítsa be a **Naplóprofil** -előfizetéssel. A napló profil meghatározza az eseményeket, amelyek tárolja, vagy streamelt adatok és a kimeneti típusa – storage-fiók és/vagy event hub. A storage-fiókban tárolt események a megtartási házirend (Ha a napok száma) is meghatározza. Ha a megőrzési házirend értéke nulla, események határozatlan ideig tárolja. Ellenkező esetben ez állítható bármilyen érték 1 és 2147483647 között. Adatmegőrzési házirendek, az alkalmazott napi, hogy naponta (UTC), naplók, amely mostantól a megőrzési ideje meghaladja a nap végén a rendszer törli a szabályzatot. Például ha egy nap adatmegőrzési, ma a nap kezdetén az a napja előtt tegnap naplóinak törlődnének. A törlési folyamat kezdődik UTC szerint éjfélig, de vegye figyelembe, hogy a naplók a tárfiókból a törlendő akár 24 órát is igénybe vehet. [További log kapcsolatos profilok Itt](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). 

## <a name="archive-the-activity-log-using-the-portal"></a>A portál használatával a tevékenységnapló archiválása
1. A portálon kattintson a **tevékenységnapló** a bal oldali navigációs menüben található hivatkozásra. Ha nem lát egy hivatkozást a tevékenységnapló, kattintson a **minden szolgáltatás** először hivatkozásra.
   
    ![Navigáljon a tevékenységnapló panel](media/monitoring-archive-activity-log/activity-logs-portal-navigate-v2.png)
2. Kattintson a panel tetején **exportálás eseményközpontba**.
   
    ![Kattintson az Exportálás gomb](media/monitoring-archive-activity-log/activity-logs-portal-export-v2.png)
3. A megjelenő panelen jelölje be a **exportálás tárfiókba** és a egy tárfiók kiválasztását.
   
    ![Storage-fiók beállítása](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. (0 és 365 között), amelynek tevékenységnapló eseményei kell megőrizni a tárfiókban lévő napok számának vagy a csúszka használatával, adja meg. Ha szeretné, hogy inkább az adatait megőrzi a tárfiókban lévő határozatlan ideig, ezt az értéket nullára. Ha szeretné a több mint 365 napok számát adja meg, az alábbiakban PowerShell vagy parancssori felület módokat kell használnia.
5. Kattintson a **Save** (Mentés) gombra.

## <a name="archive-the-activity-log-via-powershell"></a>A PowerShell Tevékenységnaplót archiválhatja

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your storage account belongs to>"
   $storageAccountName = "<your storage account name>"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -StorageAccountId $storageAccountId
   ```

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| StorageAccountId |Igen |Erőforrás-azonosító, amelyhez tevékenységeket tartalmazó naplók menteni a tárfiók. |
| Helyek |Igen |Régiók, amelynek szeretné tevékenységnapló eseményeket gyűjtő vesszővel tagolt listája. Megtekintheti összes régiók listáját az előfizetéshez a `(Get-AzureRmLocation).Location`. |
| RetentionInDays |Nem |Mely eseményeket meg kell őrizni, 1 és 2147483647 között eltelt napok száma. A nulla érték határozatlan ideig tárolja a naplók (végtelen). |
| Kategóriák |Nem |Eseménykategóriák kell gyűjteni, vesszővel tagolt listája. Lehetséges értékek: írási, törlési és művelet.  Ha nincs megadva, majd az összes lehetséges az alapértelmezett paraméterértékek |

## <a name="archive-the-activity-log-via-cli"></a>CLI-n keresztül a tevékenységnapló archiválása

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --storage-account-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>"
   ```

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| név |Igen |A napló-profil neve. |
| storage-account-id |Igen |Erőforrás-azonosító, amelyhez tevékenységeket tartalmazó naplók menteni a tárfiók. |
| helyek |Igen |Régiók, amelynek szeretné tevékenységnapló eseményeket gyűjtő szóközzel elválasztott listáját. Megtekintheti összes régiók listáját az előfizetéshez a `az account list-locations --query [].name`. |
| nap |Igen |Mely eseményeket meg kell őrizni, 1 és 2147483647 között eltelt napok száma. A nulla érték határozatlan ideig tárolja a naplók (végtelen).  Ha nulla, majd az engedélyezett paramétert meg kell igaz értékre. |
|engedélyezve | Igen |IGAZ vagy hamis.  Engedélyezi vagy letiltja a megtartási házirend segítségével.  Igaz értéke esetén a nap paraméter 0-nál nagyobb számnak kell lennie.
| kategóriák |Igen |Eseménykategóriák, érdemes gyűjtik szóközzel elválasztott listáját. Lehetséges értékek: írási, törlési és művelet. |

## <a name="storage-schema-of-the-activity-log"></a>A tevékenységnapló tároló sémája
Állított be archiválási, miután egy storage-tároló létrejön a storage-fiókban, amint egy tevékenységnapló-esemény következik be. A tárolóban lévő blobok ugyanazt az elnevezési konvenciót tevékenységeket tartalmazó naplók és a diagnosztikai naplók, hajtsa végre az alábbiak szerint:

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Például a blobnév lehet:

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Mindegyik PT1H.json blob tartalmazza a blob URL-CÍMBEN megadott egy órán belül bekövetkezett események JSON-blobját (például h = 12). Az aktuális órában az események az előfordulásukkor lesznek a PT1H.json fájlhoz fűzve. A perc értéke (m = 00) mindig 00, mert a tevékenységnapló eseményei óránként külön blobokba vannak osztva.

A PT1H.json fájlt belül minden egyes esemény tárolja a "rekord" tömb, a következő formátumban:

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Elem neve | Leírás |
| --- | --- |
| time |Időbélyeg, ha az esemény jött létre az Azure-szolgáltatás a megfelelő esemény kérelem feldolgozása. |
| resourceId |Erőforrás-azonosító az érintett erőforrás. |
| operationName |A művelet neve. |
| category |A művelet kategória működtek az adatbázisok. Írás, Olvasás, a műveletet. |
| resultType |Az eredmény típusú működtek az adatbázisok. Sikeres, sikertelen, Start |
| resultSignature |Az erőforrás típusától függ. |
| durationMs |Ennyi ezredmásodpercig tart a művelet időtartama |
| callerIpAddress |IP-cím a felhasználó hajtott végre a műveletet, egyszerű Felhasználónévi jogcím vagy egyszerű szolgáltatásnév jogcímet rendelkezésre állása alapján. |
| correlationId |Általában egy GUID Azonosítót a karakterláncként. Ugyanaz a uber művelet eseményeket, amelyek megosztása a korrelációs azonosító tartozik. |
| identity |Az engedélyezési és a jogcímek leíró JSON-blobját. |
| Engedélyezési |Az esemény tulajdonságainak RBAC-blobját. Általában tartalmazza az "action", "szerepkör" és "hatókör" tulajdonság. |
| szint |Az esemény szintjét. A következő értékek egyikét: "Kritikus", "Hiba", "Figyelmeztetés", "Tájékoztatási szintű" vagy "Részletes" |
| location |Régió, a helyét történt (vagy globális). |
| properties |Állítsa be a `<Key, Value>` párok (azaz szótár), az esemény részleteit leíró. |

> [!NOTE]
> A tulajdonságokat, és azokat a tulajdonságokat a használat függvényében az erőforrás.
> 
> 

## <a name="next-steps"></a>További lépések
* [Tölthet le blobokat elemzéshez](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Stream és az Event Hubs a tevékenységnaplóban](monitoring-stream-activity-logs-event-hubs.md)
* [További információ a tevékenységnaplóban](monitoring-overview-activity-logs.md)

