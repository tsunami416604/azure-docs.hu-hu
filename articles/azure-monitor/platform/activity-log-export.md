---
title: Az Azure tevékenységnapló exportálása
description: Az Azure tevékenységnapló exportálása tárolóba archiválás vagy az Azure Event hubs az Azure-on kívül.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: acf2526e79519e610614dc5217efbfe5e327b90f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248144"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>A storage vagy az Azure Event Hubs Azure tevékenységnapló exportálása
A [Azure-tevékenységnapló](activity-logs-overview.md) előfizetés-szintű eseményeit, amelyek az Azure-előfizetésében történt betekintést nyújt. A tevékenységnapló megtekintése az Azure Portalon, vagy másolja a Log Analytics-munkaterületet, ahol azok elemezhetők az Azure Monitor által összegyűjtött egyéb adatok, valamint egy Azure storage-fiók a tevékenységnaplót archiválhatja, vagy adatfolyamként való naplóprofil hozhat létre egy  Eseményközpont.

## <a name="archive-activity-log"></a>Tevékenységnapló archiválása
A tárfiókhoz a tevékenységnapló archiválása akkor hasznos, ha szeretné megőrizni a naplózási, statikus elemzési és biztonsági mentés (, teljes körűen felügyelve az adatmegőrzési) 90 napnál hosszabb ideig naplóadatokat. Ha csak szeretné megőrizni az események 90 napig, vagy kisebb, nem kell állítania archiválás tárfiókba, mivel a tevékenységnapló eseményei vannak az Azure platformon 90 napig őrizzük meg.

## <a name="stream-activity-log-to-event-hub"></a>Stream tevékenység naplózás az Eseményközpontba
[Az Azure Event Hubs](/azure/event-hubs/) egy adatstreamelési platform és Eseményfeldolgozási szolgáltatás, amely képes fogadni, és másodpercenként több millió folyamatán. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Két módon használható a tevékenységnaplóban a streamelési funkciót a következők:
* **Külső naplózás és a telemetriai rendszer Stream**: Idővel az Azure Event Hubs streamelési lesz a mechanizmus, amellyel a Tevékenységnaplót kanálu be külső siem-EK és a log analytics-megoldások.
* **Hozhat létre egy egyéni telemetriát és a naplózás platform**: Ha már rendelkezik egy személyre szabott telemetriai platform vagy szem előtt tartva létrehozására, rugalmasan skálázható közzétételi és előfizetési jellege az Event Hubs lehetővé teszi, hogy rugalmasan a tevékenységnaplóban. 

## <a name="prerequisites"></a>Előfeltételek

### <a name="storage-account"></a>Tárfiók
A Tevékenységnaplót vagyunk archiválása, ha szeretné [hozzon létre egy tárfiókot](../../storage/common/storage-quickstart-create-account.md) Ha még nem rendelkezik. Ne használjon egy meglévő tárfiókot, amely a benne tárolt, így jobban szabályozhatja a hozzáférést a figyelési adatok más, nem figyelési adatokat tartalmaz. Ha meg is archiválni diagnosztikai naplók és mérőszámok tárfiókhoz azonban semmi ok, dönthet úgy, hogy ugyanazt a tárfiókot használja, hogy az összes monitorozási adat egy központi helyen.

A storage-fiók nem rendelkezik a mindaddig, amíg a beállítást konfiguráló felhasználónak mindkét előfizetéshez megfelelő RBAC-hozzáféréssel rendelkezik a naplókat kibocsátó előfizetésnek az azonos előfizetésben kell.
> [!NOTE]
>  Jelenleg nem archiválhatja egy tárfiókot, amelyet mögött egy biztonságos virtuális hálózaton található adatokat.

### <a name="event-hubs"></a>Event Hubs
A Tevékenységnaplót küld egy eseményközpontnak, akkor a kell [létrehoz egy eseményközpontot](../../event-hubs/event-hubs-create.md) Ha még nem rendelkezik. Ha korábban streamelt Event Hubs-névtér a tevékenységnapló eseményei, ennek az eseményközpontnak fogja használni.

A megosztott elérési házirend határozza meg az engedélyeket, a streamelési mechanizmussal rendelkezik. Streamelés az Event Hubs és a kezelés, küldési és figyelési engedélyekkel kell rendelkeznie. Létrehozhat vagy módosíthat az Event Hubs-névtér az Azure Portalon, a konfigurálás lapot a megosztott elérési házirendeket az Event Hubs-névtér.

A tevékenységnapló naplóprofil tartalmazza a folyamatos átviteli frissítéséhez a ListKey engedéllyel kell rendelkeznie az adott Event Hubs engedélyezési szabályt. Az Event Hubs-névtér nem kell ugyanabban az előfizetésben kell az előfizetést, amelyhez a naplókat, a kibocsátó, mindaddig, amíg a beállítást konfiguráló felhasználónak megfelelő RBAC rendelkezik mindkét előfizetés való hozzáférést, és mindkét előfizetés az ugyanahhoz az AAD-bérlőhöz vannak.

A tevékenységnapló egy eseményközpontba által Stream [Log-profil létrehozásakor](#create-a-log-profile).

## <a name="create-a-log-profile"></a>-Log-profil létrehozása
Meghatározhatja, hogyan történik a az Azure-tevékenységnapló segítségével exportálja egy **naplóprofil**. Minden Azure-előfizetés legfeljebb egy naplóprofil. Ezek a beállítások segítségével konfigurálható a **exportálása** lehetőség a tevékenységnapló panel a portálon. Akkor is konfigurálható programozott módon [az Azure Monitor REST API használatával](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell-parancsmagok vagy a parancssori felület.

A napló-profil az alábbiakat határozza meg.

**Ha a tevékenységnapló küldendő adattípusokat.** Az elérhető lehetőségek jelenleg Storage-fiók vagy az Event Hubs.

**Mely kategóriák küldendő adattípusokat.** Szerinti *kategória* Naplóprofilok és a tevékenységnaplóhoz az események nem egyezik. A napló-profilban *kategória* művelet típusát adja meg (írás, törlés, a művelet). A tevékenységnapló-esemény a *kategória*"* a tulajdonság adja meg a forrás- vagy típusú eseményeket (például felügyeleti ServiceHealth és a riasztás).

**Mely régiókban (helyeken) exportálja.** Mivel a tevékenységnaplóban sok eseményt globális események tartalmaznia kell az összes hely.

**Mennyi ideig a tevékenységnapló fenn kell tartani a Storage-fiókban.** Egy nulla napnyi adatmegőrzéshez azt jelenti, hogy naplókat tartják örökre. Ellenkező esetben az érték lehet minden olyan 1 és 2147483647 között eltelt napok számát.

Ha a megőrzési házirend-beállításokat, de a naplók tárolása a storage-fiók le van tiltva, majd adatmegőrzési szabályzatok nem hatása. Adatmegőrzési házirendek, az alkalmazott napi, hogy naponta (UTC), naplók, amely mostantól a megőrzési ideje meghaladja a nap végén törli a házirendet. Például ha egy nap adatmegőrzési, ma a nap kezdetén az a napja előtt tegnap naplóinak törlődnének. A törlési folyamat kezdődik UTC szerint éjfélig, de vegye figyelembe, hogy a naplók a tárfiókból a törlendő akár 24 órát is igénybe vehet.



> [!WARNING]
> A naplóadatok JSON sorok 2018. november 1-től változik a tárfiókban formátumát. [Ebben a cikkben olvashat ennek hatásairól, valamint arról, hogy hogyan frissítheti eszközeit az új formátum kezeléséhez.](diagnostic-logs-append-blobs.md)
>
>

### <a name="create-log-profile-using-the-azure-portal"></a>Az Azure portal használatával log-profil létrehozása

Létrehozásához vagy szerkesztéséhez a napló-profilt a **exportálás eseményközpontba** lehetőség az Azure Portalon.

1. Az a **figyelő** az Azure Portalon, válassza a menü **exportálás eseményközpontba**.

    ![A portál Exportálás gomb](media/activity-log-export/portal-export.png)

3. A megjelenő panelen adja meg a következőket:
   * Régió, exportálhatja az eseményeket. Győződjön meg arról, hogy ne hagyja a fontos eseményeket, mivel a tevékenységnapló egy globális (nem régióhoz kötött) naplót, és így a legtöbb kapcsolódóan nem történik meg a hozzájuk társított régió az összes régióban kell választania. 
   * Ha szeretne írni a storage-fiók:
       * A Storage-fiók, amelyhez szeretné menteni az eseményeket.
       * nap meg szeretné őrizni a storage-ban ezek az események száma. 0 napra beállítását örökre megőrzi a naplókat.
   * Ha szeretne írni az event hubs:
       * a Service Bus Namespace, amelyben szeretné létrehozni a streamelési ezeket az eseményeket egy Eseményközpontba.

     ![Tevékenységnapló panel exportálása](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Kattintson a **mentése** ezek a beállítások mentéséhez. A rendszer azonnal alkalmazni fogja a beállításokat az előfizetésére.


### <a name="configure-log-profile-using-powershell"></a>PowerShell-lel naplóprofil konfigurálása

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha már létezik egy naplóprofil, először távolítsa el a meglévő log-profilt, majd hozzon létre egy újat.

1. Használat `Get-AzLogProfile` azonosítását, ha egy napló-profil létezik.  Ha egy napló profil létezik, vegye figyelembe a *neve* tulajdonság.

1. Használat `Remove-AzLogProfile` , távolítsa el a napló profilt származó értékkel a *neve* tulajdonság.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Használat `Add-AzLogProfile` egy új naplófájl-profil létrehozása:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Tulajdonság | Szükséges | Leírás |
    | --- | --- | --- |
    | Name (Név) |Igen |A napló-profil neve. |
    | StorageAccountId |Nem |Erőforrás-azonosító a tárfiók, ahol a tevékenységnapló menteni. |
    | serviceBusRuleId |Nem |Service Bus Szabályazonosító a Service Bus-névtér szeretné, hogy a létrehozott event hubs. Ez a karakterlánc a következő formátumban: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Location egység |Igen |Régiók, amelynek szeretné tevékenységnapló eseményeket gyűjtő vesszővel tagolt listája. |
    | RetentionInDays |Igen |Amely események kell megtartani a tárfiókban, 1 és 2147483647 között eltelt napok száma. A nulla érték határozatlan ideig tárolja a naplókat. |
    | Category |Nem |Eseménykategóriák kell gyűjteni, vesszővel tagolt listája. Lehetséges értékek a következők _írási_, _törlése_, és _művelet_. |

### <a name="example-script"></a>Példaszkript
Következő egy minta PowerShell-parancsfájlt, amely mindkét a tárolási fiók és az eseményközpont ír a tevékenységnapló-log-profil létrehozása.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Azure CLI-vel naplóprofil konfigurálása

Ha egy napló-profilja már létezik, akkor először távolítsa el a meglévő log-profilt, és hozzon létre egy új naplóprofil.

1. Használat `az monitor log-profiles list` azonosítását, ha egy napló-profil létezik.
2. Használat `az monitor log-profiles delete --name "<log profile name>` , távolítsa el a napló profilt származó értékkel a *neve* tulajdonság.
3. Használat `az monitor log-profiles create` egy új naplófájl-profil létrehozása:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Tulajdonság | Szükséges | Leírás |
    | --- | --- | --- |
    | name |Igen |A napló-profil neve. |
    | storage-account-id |Igen |Erőforrás-azonosító, amelyhez tevékenységeket tartalmazó naplók menteni a tárfiók. |
    | helyek |Igen |Régiók, amelynek szeretné tevékenységnapló eseményeket gyűjtő szóközzel elválasztott listáját. Megtekintheti összes régiók listáját az előfizetéshez a `az account list-locations --query [].name`. |
    | days |Igen |Mely eseményeket meg kell őrizni, 1 és 365 közötti napok számát. A nulla érték határozatlan ideig tárolja a naplók (végtelen).  Ha nulla, majd az engedélyezett paramétert meg kell igaz értékre. |
    |enabled | Igen |IGAZ vagy hamis.  Engedélyezi vagy letiltja a megtartási házirend segítségével.  Igaz értéke esetén a nap paraméter 0-nál nagyobb számnak kell lennie.
    | kategóriák |Igen |Eseménykategóriák, érdemes gyűjtik szóközzel elválasztott listáját. Lehetséges értékek: írási, törlési és művelet. |



## <a name="activity-log-schema"></a>Műveletnapló-séma
E küldi el az Azure storage- vagy Event Hub, a tevékenységnapló adatainak lesz írva JSON-ná, az alábbi formátumban.

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
A JSON-elemek az alábbi táblázatban ismertetett.

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
| szint |Az esemény szintjét. A következő értékek egyikét: _Kritikus fontosságú_, _hiba_, _figyelmeztetés_, _tájékoztató_, és _részletes_ |
| location |Régió, a helyét történt (vagy globális). |
| properties |Állítsa be a `<Key, Value>` párok (azaz szótár), az esemény részleteit leíró. |

> [!NOTE]
> A tulajdonságok és a használati ezeket a tulajdonságokat az erőforrás függvényében.



## <a name="next-steps"></a>További lépések

* [További információ a tevékenységnaplóban](../../azure-resource-manager/resource-group-audit.md)
* [Az Azure Monitor naplók gyűjtése a tevékenységnaplóban](activity-log-collect.md)
