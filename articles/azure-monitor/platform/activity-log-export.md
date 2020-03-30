---
title: Az Azure-tevékenységnapló exportálása
description: Exportálja az Azure-tevékenységnaplót a tárolóba archiváláshoz vagy az Azure Event Hubs-hoz az Azure-on kívüli exportáláshoz.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: edaa585ffb3448a80b021aa924a9d654ac829931
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096286"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Azure-tevékenységnapló exportálása tárhelyre vagy Azure-eseményközpontokba

> [!IMPORTANT]
> Az Azure-tevékenységnapló Azure Storage és Az Azure Event Hubs szolgáltatásba való elküldésének módja [diagnosztikai beállításokra](diagnostic-settings.md)változott. Ez a cikk az elavult örökölt módszert ismerteti. Az összehasonlításért olvassa el az [Azure-tevékenységnapló-gyűjtemény frissítése és exportálása.](diagnostic-settings-legacy.md)


Az [Azure-tevékenységnapló](platform-logs-overview.md) betekintést nyújt az Azure-előfizetésben bekövetkezett előfizetési szintű eseményekbe. A mellett, hogy megtekinti a tevékenységnaplót az Azure Portalon, vagy átmásolja egy Log Analytics-munkaterületre, ahol elemezhető az Azure Monitor által gyűjtött egyéb adatokkal, létrehozhat egy naplóprofilt a tevékenységnapló archiválására egy Azure-tárfiókba, vagy streamelheti egy Eseményközpont.

## <a name="archive-activity-log"></a>Archiválási tevékenységnapló
A tevékenységnapló tárfiókba való archiválása akkor hasznos, ha 90 napnál hosszabb ideig szeretné megőrizni a naplóadatokat (az adatmegőrzési házirend teljes körű szabályozásával) naplózás, statikus elemzés vagy biztonsági mentés esetén. Ha csak 90 napig vagy annál rövidebb ideig kell megőriznie az eseményeket, nem kell archiválást beállítania egy tárfiókhoz, mivel a tevékenységnapló-események 90 napig megmaradnak az Azure platformon.

## <a name="stream-activity-log-to-event-hub"></a>Tevékenységnapló streamelése az Eseményközpontba
[Az Azure Event Hubs](/azure/event-hubs/) egy adatfolyam-továbbítási platform és eseménybetöltési szolgáltatás, amely másodpercenként több millió eseményt képes fogadni és feldolgozni. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. A tevékenységnapló streamelési funkciójának használata a következő:
* **Streamelés külső naplózási és telemetriai rendszerekre:** Idővel az Azure Event Hubs streamelése lesz a mechanizmus, amely a tevékenységnaplót külső SIEM-ekbe és log elemzési megoldásokba továbbítja.
* **Egyéni telemetriai és naplózási platform létrehozása:** Ha már rendelkezik egy egyéni tervezésű telemetriai platformmal, vagy az on-one létrehozásán gondolkodik, az Event Hubs nagymértékben méretezhető közzététel-előfizetés jellege lehetővé teszi a tevékenységnapló rugalmas betöltését.

## <a name="prerequisites"></a>Előfeltételek

### <a name="storage-account"></a>Tárfiók
Ha archiválja a tevékenységnaplót, létre kell [hoznia egy tárfiókot,](../../storage/common/storage-account-create.md) ha még nem rendelkezik ilyen. Ne használjon olyan meglévő tárfiókot, amely más, nem figyelési adatokat tárol, így jobban szabályozhatja a figyelési adatokhoz való hozzáférést. Ha a naplók és a metrikák egy tárfiókba is archiválja, dönthet úgy, hogy ugyanazt a tárfiókot használja az összes figyelési adat központi helyen való tárolásához.

A tárfiók nem kell ugyanabban az előfizetésben, mint az előfizetést kibocsátó naplók, amíg a felhasználó, aki konfigurálja a beállítást a megfelelő RBAC-hozzáférés mindkét előfizetéshez. 

> [!TIP]
> Lásd: [Az Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) a biztonságos virtuális hálózat mögötti tárfiókhoz való hozzáférés biztosításához.

### <a name="event-hubs"></a>Event Hubs
Ha a tevékenységnaplót egy eseményközpontba küldi, akkor létre kell [hoznia egy eseményközpontot,](../../event-hubs/event-hubs-create.md) ha még nem rendelkezik ilyennel. Ha korábban streamelte a tevékenységnapló-eseményeket erre az Eseményközpontok névtérre, majd az eseményközpontot újra felhasználja a rendszer.

A megosztott hozzáférési szabályzat határozza meg az engedélyeket, amely a streamelési mechanizmus rendelkezik. Az Event Hubs-ba való streameléshez kezelési, küldési és figyelési engedélyek szükségesek. Az Event Hubs névtér megosztott hozzáférési szabályzatait az Azure Portalon az Event Hubs névtér Konfigurálás lapján hozhat létre vagy módosíthatja.

A tevékenységnapló-profil streamelési adatokkal való frissítéséhez listkey engedéllyel kell rendelkeznie az adott Eseményközpont engedélyezési szabályához. Az Event Hubs névtér nem kell ugyanabban az előfizetésben, mint az előfizetés, amely a naplókat, mindaddig, amíg a felhasználó, aki konfigurálja a beállítást, megfelelő RBAC-hozzáféréssel rendelkezik mindkét előfizetéshez, és mindkét előfizetés ugyanabban az AAD-bérlőben van.

A tevékenységnapló tanévenként [naplóprofilt hozhat létre.](#create-a-log-profile)

## <a name="create-a-log-profile"></a>Naplóprofil létrehozása
Az Azure-tevékenységnapló exportálásának módját **naplóprofil**használatával határozhatja meg. Minden Azure-előfizetés csak egy naplóprofillal rendelkezhet. Ezek a beállítások a portál Tevékenységnapló **panelexportálási** beállításával konfigurálhatók. Az Azure Monitor REST [API, a](https://msdn.microsoft.com/library/azure/dn931927.aspx)PowerShell-parancsmagok vagy a CLI használatával is konfigurálhatók programozott módon.

A naplóprofil a következőket határozza meg.

**A tevékenységnapló elküldésének helye.** Jelenleg a rendelkezésre álló lehetőségek a tárfiók vagy az eseményközpontok.

**Mely eseménykategóriákat kell elküldeni.** A *kategória* jelentése a naplóprofilok ban és a tevékenységnapló-eseményekben eltérő. A naplóprofilban a *kategória* a művelettípusát jelöli (Írás, Törlés, Művelet). Egy tevékenységnapló-eseményben a "* *tulajdonság*az esemény forrását vagy típusát jelöli (például Felügyelet, ServiceHealth és Alert).

**Mely régiókat (helyeket) kell exportálni.** Az összes helyet fel kell tüntetnie, mivel a tevékenységnaplóban számos esemény globális esemény.

**Mennyi ideig kell megőrizni a tevékenységnaplót egy tárfiókban.** A nulla napos megőrzés azt jelenti, hogy a naplók örökre meg lesznek őrizve. Ellenkező esetben az érték 1 és 365 közötti napok száma lehet.

Ha az adatmegőrzési házirendek be vannak állítva, de a naplók tárolása egy tárfiókban le van tiltva, majd az adatmegőrzési házirendek nincs hatása. Az adatmegőrzési szabályok naponta alkalmazva vannak, így a nap végén (UTC) törlődnek a naplók attól a naptól kezdve, amikor az adatmegőrzési házirenden túl van. Ha például egy napos adatmegőrzési házirenddel volt, a nap elején a tegnap előtti nap naplói törlődnek. A törlési folyamat utc éjfélkor kezdődik, de vegye figyelembe, hogy akár 24 órát is igénybe vehet, amíg a naplók törlődnek a tárfiókból.


> [!IMPORTANT]
> Hibaüzenet jelenhet meg a naplóprofil létrehozásakor, ha a Microsoft.Insights erőforrás-szolgáltató nincs regisztrálva. Tekintse meg [az Azure-erőforrás-szolgáltatók és -típusok](../../azure-resource-manager/management/resource-providers-and-types.md) a szolgáltató regisztrálásához.


### <a name="create-log-profile-using-the-azure-portal"></a>Naplóprofil létrehozása az Azure Portal használatával

Hozzon létre vagy szerkesztsen egy naplóprofilt az **Exportálás az Eseményközpontba** beállítással az Azure Portalon.

1. Az **Azure-portál Azure Monitor** menüjében válassza a **Tevékenységnapló**lehetőséget.
3. Kattintson a **Diagnosztikai beállítások** elemre.

   ![Diagnosztikai beállítások](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Kattintson a lila banner az örökölt élmény.

    ![Örökölt élmény](media/diagnostic-settings-subscription/legacy-experience.png)

3. A megjelenő panelen adja meg a következőket:
   * Az exportálandó eseményeket beigmegő régiók. Válassza ki az összes régiót, hogy ne maradjon le a legfontosabb eseményekről, mivel a tevékenységnapló globális (nem regionális) napló, és így a legtöbb eseményhez nincs régió társítva.
   * Ha a tárfiókba szeretne írni:
       * A tárfiók, amelyre szeretné menteni az eseményeket.
       * Az események tárolásban való megőrzéséhez kívánt napok száma. A 0 napos beállítás örökre megőrzi a naplókat.
   * Ha az eseményközpontba szeretne írni:
       * A Service Bus-névtér, amelyben szeretné, hogy egy Event Hub létre kell hozni ezeket az eseményeket.

     ![Exportálási tevékenység naplója](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. A beállítások mentéséhez kattintson a **Mentés** gombra. A rendszer azonnal alkalmazni fogja a beállításokat az előfizetésére.


### <a name="configure-log-profile-using-powershell"></a>Naplóprofil konfigurálása a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha már létezik naplóprofil, először el kell távolítania a meglévő naplóprofilt, majd létre kell hoznia egy újat.

1. Ezzel `Get-AzLogProfile` azonosíthatja, hogy létezik-e naplóprofil.  Ha létezik naplóprofil, jegyezze fel a *name* tulajdonságot.

1. Ezzel `Remove-AzLogProfile` távolíthatja el a naplóprofilt a *name* tulajdonság értékének használatával.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Új `Add-AzLogProfile` naplóprofil létrehozásához használható:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Tulajdonság | Kötelező | Leírás |
    | --- | --- | --- |
    | Név |Igen |A naplóprofil neve. |
    | StorageAccountId |Nem |A tárfiók erőforrás-azonosítója, ahová a tevékenységnaplót menteni kell. |
    | szolgáltatásBusRuleId |Nem |A Service Bus-szabály azonosítója a Service Bus névtérhez, amelyben létre szeretné hozni az eseményközpontokat. Ez egy karakterlánc a `{service bus resource ID}/authorizationrules/{key name}`következő formátummal: . |
    | Hely |Igen |Vesszővel tagolt régiók listája, amelyekhez tevékenységnapló-eseményeket szeretne gyűjteni. |
    | RetencióInDays |Igen |Azon napok száma, amelyekesetén az eseményeket meg kell őrizni a tárfiókban, 1 és 365 között. A nulla érték határozatlan ideig tárolja a naplókat. |
    | Kategória |Nem |Vesszővel tagolt eseménykategóriák, amelyeket össze kell gyűjteni. Lehetséges értékek: _Write_, _Delete_és _Action_. |

### <a name="example-script"></a>Példaszkript
A következőkben egy powershell-parancsfájlminta egy naplóprofil létrehozásához, amely a tevékenységnaplót egy tárfiókba és az eseményközpontba írja.

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


### <a name="configure-log-profile-using-azure-cli"></a>Naplóprofil konfigurálása az Azure CLI használatával

Ha már létezik naplóprofil, először el kell távolítania a meglévő naplóprofilt, majd létre kell hoznia egy új naplóprofilt.

1. Ezzel `az monitor log-profiles list` azonosíthatja, hogy létezik-e naplóprofil.
2. Ezzel `az monitor log-profiles delete --name "<log profile name>` távolíthatja el a naplóprofilt a *name* tulajdonság értékének használatával.
3. Új `az monitor log-profiles create` naplóprofil létrehozásához használható:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Tulajdonság | Kötelező | Leírás |
    | --- | --- | --- |
    | név |Igen |A naplóprofil neve. |
    | tárfiók azonosítója |Igen |Annak a tárfióknak az erőforrás-azonosítója, amelybe a tevékenységnaplókat menteni kell. |
    | Helyek |Igen |Terület-elválasztott régiók listája, amelyek hez tevékenységnapló-eseményeket szeretne gyűjteni. Az előfizetés összes régiójának listáját a `az account list-locations --query [].name`segítségével tekintheti meg. |
    | nap |Igen |Azon napok száma, amelyekre az eseményeket meg kell őrizni, 1 és 365 között. A nulla érték fogja tárolni a naplókat a végtelenségig (örökre).  Ha nulla, akkor az engedélyezett paramétert hamisra kell állítani. |
    |engedélyezve | Igen |Igaz vagy hamis.  Az adatmegőrzési szabály engedélyezésére vagy letiltására szolgál.  Ha Igaz, akkor a days paraméternek 0-nál nagyobb értéknek kell lennie.
    | kategóriák |Igen |Az összegyűjtendő eseménykategóriák térre tagolt listája. A lehetséges értékek az Írás, a Törlés és a Művelet. |



## <a name="next-steps"></a>További lépések

* [További információ a tevékenységnaplóról](../../azure-resource-manager/management/view-activity-logs.md)
* [Tevékenységnapló gyűjtése az Azure figyelőnaplóiba](activity-log-collect.md)
