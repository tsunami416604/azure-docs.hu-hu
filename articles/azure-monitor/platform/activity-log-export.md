---
title: Az Azure-tevékenység naplójának exportálása
description: Exportálja az Azure-beli tevékenység naplóját archiválásra vagy Azure-Event Hubsre az Azure-on kívüli exportáláshoz.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 12c750f96b8852cdd6a6039ebfa750c2ee792a6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80396724"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Azure-Tevékenységnaplók exportálása a Storage-ba vagy az Azure Event Hubsba

> [!IMPORTANT]
> Az Azure-tevékenység naplójának az Azure Storage-ba és az Azure Event Hubsba való küldésének módszere [diagnosztikai beállításokra](diagnostic-settings.md)változott. Ez a cikk azt az örökölt metódust ismerteti, amelynek a folyamata elavult. Az összehasonlításhoz tekintse meg az [Azure-beli tevékenység naplójának összegyűjtésére és elemzésére](activity-log-collect.md) szolgáló frissítést Azure monitor.


Az [Azure-tevékenység naplója](platform-logs-overview.md) betekintést nyújt az Azure-előfizetésében bekövetkezett előfizetési szintű eseményekre. Amellett, hogy megtekinti a tevékenység naplóját a Azure Portal, vagy átmásolja egy Log Analytics-munkaterületre, ahol az a Azure Monitor által gyűjtött egyéb adatokkal is elemezhető, létrehozhat egy log-profilt, amely archiválja a műveletnapló egy Azure Storage-fiókba, vagy továbbíthatja azt egy Event hub-ba.

## <a name="archive-activity-log"></a>Archiválási tevékenység naplója
A műveletnapló a Storage-fiókba való archiválása akkor lehet hasznos, ha a naplózási adatok megmaradnak a 90 napnál hosszabb ideig (az adatmegőrzési szabályzat teljes körű ellenőrzése) a naplózás, a statikus elemzés vagy a biztonsági mentés számára. Ha csak 90 napig vagy kevesebb ideig kell megőriznie az eseményeket, nincs szükség a Storage-fiók archiválására, mivel a tevékenység-naplózási események az Azure-platformon maradnak meg a 90 napig.

## <a name="stream-activity-log-to-event-hub"></a>Stream-tevékenység naplója az Event hub-ba
Az [Azure Event Hubs](/azure/event-hubs/) egy adatstreaming platform-és esemény-betöltési szolgáltatás, amely másodpercenként több millió eseményt képes fogadni és feldolgozni. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. A következő két módszer használható a tevékenységi napló folyamatos átviteli funkciójának használatára:
* **Stream harmadik féltől származó naplózási és telemetria rendszerekre**: az Azure Event Hubs streaming a tevékenységnek a harmadik féltől származó Siem-és log Analytics-megoldásokban való beléptetésére szolgáló mechanizmus lesz.
* **Hozzon létre egy egyéni telemetria és naplózási platformot**: Ha már rendelkezik egy egyéni kialakítású telemetria-platformmal, vagy gondolkodik az első kiépítésében, a rugalmasan méretezhető közzétételi és előfizetési jellegű Event Hubs lehetővé teszi a tevékenység naplójának rugalmas betöltését.

## <a name="prerequisites"></a>Előfeltételek

### <a name="storage-account"></a>Tárfiók
Ha archiválja a tevékenység naplóját, [létre kell hoznia egy Storage-fiókot](../../storage/common/storage-account-create.md) , ha még nem rendelkezik ilyennel. Ne használjon olyan meglévő Storage-fiókot, amely más, nem figyelési adattárolási információkkal rendelkezik, így hatékonyabban vezérelheti a figyeléshez való hozzáférést. Ha a naplókat és mérőszámokat is archiválja egy Storage-fiókba, akkor dönthet úgy, hogy ugyanazt a Storage-fiókot használja, hogy az összes figyelési adat központi helyen maradjon.

A Storage-fióknak nem kell ugyanabban az előfizetésben lennie, mint az előfizetéshez tartozó naplókat, ha a beállítást konfiguráló felhasználó mindkét előfizetéshez megfelelő RBAC-hozzáféréssel rendelkezik. 

> [!TIP]
> Lásd: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) a biztonságos virtuális hálózat mögötti Storage-fiókhoz való hozzáférés biztosításához.

### <a name="event-hubs"></a>Event Hubs
Ha egy Event hubhoz küldi a tevékenység naplóját, akkor [létre kell hoznia egy Event hub](../../event-hubs/event-hubs-create.md) -t, ha még nem rendelkezik ilyennel. Ha korábban naplózta a tevékenység naplózási eseményeit erre a Event Hubs névtérre, az Event hub újra fel lesz használva.

A megosztott hozzáférési házirend határozza meg a folyamatos átviteli mechanizmus által biztosított engedélyeket. A Event Hubs való folyamatos átvitelhez a kezelés, a Küldés és a figyelés engedélyek szükségesek. A Event Hubs névtérhez tartozó megosztott hozzáférési házirendeket Azure Portal a Event Hubs névtér configure (Konfigurálás) lapján lehet létrehozni vagy módosítani.

Ha frissíteni szeretné a tevékenység naplójának profilját a folyamatos átvitelhez, rendelkeznie kell a ListKey engedéllyel az adott Event Hubs engedélyezési szabályhoz. A Event Hubs névtérnek nem kell ugyanabban az előfizetésben lennie, mint a naplókat kibocsátó előfizetésnek, feltéve, hogy a beállítást konfiguráló felhasználó RBAC-hozzáféréssel rendelkezik mindkét előfizetéshez, és mindkét előfizetés ugyanahhoz a HRE-bérlőhöz tartozik.

[Egy log-profil létrehozásával](#create-a-log-profile)továbbítsa a tevékenység naplóját egy Event hubhoz.

## <a name="create-a-log-profile"></a>Log-profil létrehozása
Megadhatja, hogyan exportálja az Azure-tevékenység naplóját a **log profil**használatával. Minden Azure-előfizetés csak egyetlen log-profillal rendelkezhet. Ezek a beállítások az **Exportálás** lehetőséggel konfigurálhatók a portál tevékenység napló paneljén. [A Azure Monitor REST API, a](https://msdn.microsoft.com/library/azure/dn931927.aspx)PowerShell-parancsmagok vagy a parancssori felület használatával is konfigurálhatók.

A napló profilja a következőket határozza meg.

**Hová kell elküldeni a tevékenység naplóját.** Jelenleg az elérhető lehetőségek a Storage-fiók vagy a Event Hubs.

**Mely esemény-kategóriákat kell elküldeni.** A naplózási profilok *és a műveletnapló* eseményeinek jelentése eltérő. A log profilban a *Kategória* a művelet típusát jelöli (írás, törlés, művelet). Egy tevékenység naplójában a (z *) "* tulajdonság az esemény*forrását vagy típusát jelöli (például adminisztráció, ServiceHealth és riasztás).

**Az exportálandó régiókat (helyszíneket) exportálni kell.** Minden helyet fel kell vennie, mivel a tevékenység naplójában számos esemény globális esemény.

**Mennyi ideig kell megőrizni a tevékenység naplóját egy Storage-fiókban.** A nulla napos megőrzés azt jelenti, hogy a naplók örökre meg lesznek őrizve. Ellenkező esetben az érték tetszőleges számú nap lehet 1 és 365 között.

Ha adatmegőrzési házirend van beállítva, de a naplófájlok tárolása egy Storage-fiókban le van tiltva, akkor a megőrzési szabályzatok nem lépnek érvénybe. Az adatmegőrzési szabályzatok naponta lesznek alkalmazva, így a nap végén (UTC) az adatmegőrzési házirendben már nem szereplő naplók törlődnek. Ha például egy nap adatmegőrzési szabályzattal rendelkezett, akkor a nap elején a tegnapi nap előtti naplók törlődnek. A törlési folyamat az UTC éjfélkor kezdődik, de vegye figyelembe, hogy a naplók törlését akár 24 óráig is eltarthat a Storage-fiókból.


> [!IMPORTANT]
> Ha a Microsoft. ininsights erőforrás-szolgáltató nincs regisztrálva, hibaüzenetet kaphat a log-profil létrehozásakor. A szolgáltató regisztrálásához tekintse meg az [Azure erőforrás-szolgáltatókat és-típusokat](../../azure-resource-manager/management/resource-providers-and-types.md) .


### <a name="create-log-profile-using-the-azure-portal"></a>Log-profil létrehozása a Azure Portal használatával

Hozzon létre vagy szerkesszen egy log-profilt a Azure Portal **Exportálás az Event hub** -be lehetőségével.

1. A Azure Portal **Azure monitor** menüjében válassza a **műveletnapló**elemet.
3. Kattintson a **Diagnosztikai beállítások** elemre.

   ![Diagnosztikai beállítások](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. A régi élményhez kattintson a lila szalagcímre.

    ![Korábbi élmény](media/diagnostic-settings-subscription/legacy-experience.png)

3. A megjelenő panelen a következőt kell megadnia:
   * Az exportálandó eseményekkel rendelkező régiók. Válassza ki az összes régiót, hogy ne hagyja ki a legfontosabb eseményeket, mivel a tevékenység naplója globális (nem regionális) napló, így a legtöbb eseményhez nem tartozik régió társítva.
   * Ha a Storage-fiókba szeretne írni:
       * Az a Storage-fiók, amelyre az eseményeket menteni szeretné.
       * Azon napok száma, amelyekben meg szeretné őrizni ezeket az eseményeket a tárolóban. A 0 nap beállítása örökre megőrzi a naplókat.
   * Ha az Event hub-ba szeretne írni:
       * Az a Service Bus névtér, amelyben az események továbbításához létre kell hozni egy Event hub-t.

     ![Exportálási tevékenység naplójának panelje](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. A beállítások mentéséhez kattintson a **Mentés** gombra. A rendszer azonnal alkalmazni fogja a beállításokat az előfizetésére.


### <a name="configure-log-profile-using-powershell"></a>A log profil konfigurálása a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha már létezik egy bejelentkezési profil, először el kell távolítania a meglévő log-profilt, majd létre kell hoznia egy újat.

1. A `Get-AzLogProfile` használatával azonosíthatja, hogy létezik-e egy log-profil.  Ha egy log-profil létezik, jegyezze fel a *Name (név* ) tulajdonságot.

1. A `Remove-AzLogProfile` használatával távolítsa el a napló profilt a Name ( *név* ) tulajdonság értéke alapján.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Új `Add-AzLogProfile` naplózási profil létrehozásához használja a következőt:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Tulajdonság | Kötelező | Leírás |
    | --- | --- | --- |
    | Name (Név) |Igen |A napló profiljának neve. |
    | StorageAccountId |Nem |Azon Storage-fiók erőforrás-azonosítója, amelybe menteni kell a tevékenység naplóját. |
    | serviceBusRuleId |Nem |Service Bus a Service Bus névtérhez tartozó szabály AZONOSÍTÓját, amelybe az Event hub-t létre szeretné hozni. Ez a következő formátumú karakterlánc: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Hely |Igen |Azoknak a régióknak a vesszővel tagolt listája, amelyeknek a tevékenység-naplózási eseményeket össze szeretné gyűjteni. |
    | RetentionInDays |Igen |Ennyi nap elteltével kell megőrizni az eseményeket a Storage-fiókban 1 és 365 között. A nulla érték határozatlan ideig tárolja a naplókat. |
    | Kategória |Nem |Az összegyűjteni kívánt események kategóriáinak vesszővel tagolt listája. A lehetséges értékek a következők: _írás_, _Törlés_és _művelet_. |

### <a name="example-script"></a>Példaszkript
A következő példa egy PowerShell-szkriptet hoz létre egy olyan log-profil létrehozásához, amely a tevékenység naplóját a Storage-fiókra és az Event hub-ra írja.

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


### <a name="configure-log-profile-using-azure-cli"></a>A log profil konfigurálása az Azure CLI használatával

Ha már létezik egy naplózási profil, először el kell távolítania a meglévő log-profilt, majd létre kell hoznia egy új napló-profilt.

1. A `az monitor log-profiles list` használatával azonosíthatja, hogy létezik-e egy log-profil.
2. A `az monitor log-profiles delete --name "<log profile name>` használatával távolítsa el a napló profilt a Name ( *név* ) tulajdonság értéke alapján.
3. Új `az monitor log-profiles create` naplózási profil létrehozásához használja a következőt:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Tulajdonság | Kötelező | Leírás |
    | --- | --- | --- |
    | név |Igen |A napló profiljának neve. |
    | Storage-Account-ID |Igen |Azon Storage-fiók erőforrás-azonosítója, amelybe menteni szeretné a tevékenység naplóit. |
    | helyek |Igen |Szóközzel tagolt lista azoknak a régióknak a listájához, amelyeknek a tevékenység-naplózási eseményeket össze szeretné gyűjteni. Az előfizetéshez tartozó összes régió listáját megtekintheti a `az account list-locations --query [].name`használatával. |
    | nap |Igen |Azon napok száma, amelyekhez meg kell őrizni az eseményeket 1 és 365 között. A nulla érték a naplókat határozatlan ideig (Forever) tárolja.  Ha nulla, akkor az engedélyezett paramétert false értékre kell állítani. |
    |engedélyezve | Igen |Igaz vagy hamis.  Az adatmegőrzési szabály engedélyezésére vagy letiltására szolgál.  Ha az értéke igaz, akkor a Days paraméternek 0-nál nagyobbnak kell lennie.
    | kategóriák |Igen |Az összegyűjteni kívánt események kategóriáinak szóközzel tagolt listája. A lehetséges értékek a következők: írás, törlés és művelet. |



## <a name="next-steps"></a>További lépések

* [További információ a tevékenység naplóról](../../azure-resource-manager/management/view-activity-logs.md)
* [Begyűjti a tevékenység naplóját Azure Monitor naplókba](activity-log-collect.md)
