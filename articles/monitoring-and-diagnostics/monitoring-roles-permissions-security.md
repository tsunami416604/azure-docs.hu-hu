---
title: Szerepkörök, engedélyek és biztonság az Azure Monitor használatának első lépései
description: Ismerje meg, hogyan korlátozza a hozzáférést az erőforrások figyelése az Azure Monitor beépített szerepkörök és engedélyek segítségével.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 6a197cf5667e1668b2007a5f552b813cb8ed68d3
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957828"
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Szerepkörök, engedélyek és biztonság az Azure Monitor használatának első lépései
Számos csapat kell szigorúan szabályozzák a hozzáférést a figyelési adatok és beállítások. Például, ha kizárólag a figyelést (a támogatási szakértők, fejlesztő és üzemeltető mérnököknek) dolgozó csapat tagjai rendelkezik, vagy ha egy felügyelt szolgáltató használ, érdemes hozzáférést biztosít nekik a csak figyelési adatok korlátozásával hozhat létre, módosít, vagy erőforrások törlése. Ez a cikk bemutatja, hogyan gyors beépített figyelési RBAC szerepkör alkalmazásához a felhasználónak az Azure-ban, vagy egy felhasználóhoz, aki csak korlátozott felügyeleti engedélyekre van szüksége a saját egyéni szerepkör létrehozása. Majd az Azure Monitor-kapcsolódó erőforrások és a bennük adatokhoz való hozzáférés korlátozásáról vonatkozó biztonsági szempontokat ismerteti.

## <a name="built-in-monitoring-roles"></a>Beépített figyelési szerepkörök
Az Azure Monitor beépített szerepkörök tervezett korlátozza miközben beszerzéséhez és konfigurálásához szükséges adatok infrastruktúra felügyeletért felelős továbbra is egy adott előfizetés erőforrásokhoz való hozzáférést. Az Azure Monitor két-a-beépített szerepkört kínál: A figyelési olvasó és közreműködő figyelése.

### <a name="monitoring-reader"></a>Olvasó figyelése
A Monitoring Reader szerepkörhöz hozzárendelt személyek is az összes monitorozási adat egy előfizetésben, de nem bármely erőforrás módosítása beállításainak megtekintése vagy szerkesztése bármilyen kapcsolódó erőforrások figyeléséhez. Ez a szerepkör a következő megfelelő szervezetekben, például a műveletek vagy támogatási mérnökök, tudnia, hogy a felhasználók számára:

* Figyelési irányítópult megtekintése a portálon, és a saját privát figyelési irányítópultokat hozhat létre.
* Meghatározott riasztási szabályok megtekintése [Azure-riasztások](monitoring-overview-unified-alerts.md)
* Metrikák a lekérdezés a [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx), [PowerShell-parancsmagok](insights-powershell-samples.md), vagy [platformfüggetlen CLI](insights-cli-samples.md).
* A lekérdezés a tevékenységnaplóban a portálon, az Azure Monitor REST API, PowerShell-parancsmagok vagy többplatformos parancssori felület használatával.
* Nézet a [diagnosztikai beállítások](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) erőforrás.
* Nézet a [naplóprofil](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) -előfizetéssel.
* Automatikus skálázási beállítások megtekintéséhez.
* Riasztási tevékenység megtekintése és beállításait.
* Application Insights-adatok elérése és adatok megtekintése az AI-Analytics.
* Keressen rá a Log Analytics-munkaterület adatok többek között a munkaterület használati adatait.
* A Log Analytics felügyeleti csoportok megtekintése.
* A Log Analytics keresési séma lekérése.
* A Log Analytics intelligenciacsomagok listázása.
* Kérje le, és hajtsa végre a Log Analytics-beli mentett keresések.
* A Naplóelemzési tárkonfiguráció lekéréséhez.

> [!NOTE]
> Ez a szerepkör nem olvasási hozzáférést biztosít egy eseményközpontba, folyamatos vagy a storage-fiókban tárolt Teljesítménynapló-adatokat. [Lásd alább](#security-considerations-for-monitoring-data) ezekhez az erőforrásokhoz való hozzáférés konfigurálásával kapcsolatos információkat.
> 
> 

### <a name="monitoring-contributor"></a>Közreműködő figyelése
A figyelés közreműködői szerepkörrel mások is láthatják az összes monitorozási adat, előfizetés és a létrehozása vagy módosítása a figyelési beállításokat, de nem módosítható az összes többi erőforrást. Ez a szerepkör kibővíti a figyelési olvasó szerepkört, és a szervezet figyelési csapat vagy akik mellett a fenti, engedélyeket is képesnek kell lenniük a felügyelt szolgáltatóknak tagjai számára megfelelő:

* Tegye közzé a figyelési irányítópult, egy megosztott irányítópultot.
* Állítsa be [diagnosztikai beállítások](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) egy resource.* számára
* Állítsa be a [naplóprofil](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) egy subscription.* számára
* Riasztási szabályok tevékenység és a beállításokat az [Azure Alerts](monitoring-overview-unified-alerts.md).
* Hozza létre az Application Insights-webtesztek és összetevőket.
* A Log Analytics-munkaterület megosztott kulcsainak listázása.
* Engedélyezi vagy letiltja a Log Analytics intelligenciacsomagok.
* Hozzon létre, és törölje, és hajtsa végre a Log Analytics-beli mentett keresések.
* Hozzon létre, és a Naplóelemzési tárkonfiguráció törlése.

* a felhasználó külön is engedéllyel kell listkeys műveletének a cél erőforráson (tárolási fiók- vagy event hub-névtér) egy naplóprofil vagy diagnosztikai beállítás.

> [!NOTE]
> Ez a szerepkör nem olvasási hozzáférést biztosít egy eseményközpontba, folyamatos vagy a storage-fiókban tárolt Teljesítménynapló-adatokat. [Lásd alább](#security-considerations-for-monitoring-data) ezekhez az erőforrásokhoz való hozzáférés konfigurálásával kapcsolatos információkat.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Monitorozási engedélyek és egyéni RBAC-szerepkörök
Ha a fenti beépített szerepkörök nem felelnek meg a csapat a pontos igényei, [hozzon létre egy egyéni RBAC-szerepkört](../role-based-access-control/custom-roles.md) részletesebb engedélyekkel. Az alábbiakban a hozzájuk tartozó leírások gyakori Azure Monitor RBAC műveletekről.

| Művelet | Leírás |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |A csoport olvasási, írási és törlési művelet. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Olvasási, írási és törlési tevékenységnapló-riasztások. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Olvasási, írási és törlési riasztási szabályok (a klasszikus riasztások). |
| Microsoft.Insights/AlertRules/Incidents/Read |Riasztási szabályok (az előzményeket a riasztási szabály aktiválása) incidensek listázása. Ez csak a portál vonatkozik. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Olvasási, írási és törlési automatikus méretezési beállításokkal. |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |Diagnosztikai beállítások olvasása/írása/törlése. |
| Microsoft.Insights/EventCategories/Read |Enumerálni az összes kategória lehetséges a tevékenységnaplóban. Használja az Azure Portalon. |
| Microsoft.Insights/eventtypes/digestevents/Read |Erre az engedélyre szükség a felhasználók számára, akik hozzáférhetnek a vizsgálati naplók a portálon keresztül. |
| Microsoft.Insights/eventtypes/values/Read |Tevékenységnapló eseményei (felügyeleti események) egy adott előfizetés listázása. Ezzel az engedéllyel csak a tevékenységnaplóban a szoftveres és a portál hozzáférést alkalmazható. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | Olvasási, írási és törlési hálózati forgalmi naplók diagnosztikai beállításait. |
| Microsoft.Insights/LogDefinitions/Read |Erre az engedélyre szükség a felhasználók számára, akik hozzáférhetnek a vizsgálati naplók a portálon keresztül. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |Olvasási, írási és törlési naplóprofilok (tevékenységnapló streamelés az event hub vagy a storage-fiók). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Olvasási, írási és törlési közel valós idejű metrikákhoz kapcsolódó riasztások |
| Microsoft.Insights/MetricDefinitions/Read |(Erőforrás rendelkezésre álló metrika típusok listája) metrikadefiníciók olvasása. |
| Microsoft.Insights/Metrics/Read |Olvassa el a erőforrás metrikáit. |
| Microsoft.Insights/Register/Action |Az Azure Monitor erőforrás-szolgáltató regisztrálásához. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Olvasási, írási és törlési naplóriasztások az Application Insights. |



> [!NOTE]
> Egy erőforráscsoport szükséges, hogy a felhasználó rendelkezik-e olvasási hozzáféréssel az erőforrás típusát és a hatókör az erőforrás eléréséhez riasztásokat, a diagnosztikai beállítások és a metrikák. ("Írás"), amely egy storage-fiókot vagy adatfolyamok az event hubs archívum diagnosztikai beállítás vagy a naplóhoz profil létrehozásához a felhasználót, hogy a célként megadott erőforrás listkeys műveletének engedéllyel is rendelkezik.
> 
> 

Például használja a fenti táblázat egy egyéni RBAC szerepkör létrehozhat egy "tevékenység Log olvasó" ehhez hasonló:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Monitorozási adatok történő futtatásának biztonsági szempontjai
Monitorozási adatok – különösen a naplófájlok – tartalmazhatnak bizalmas adatokat, például az IP-címek vagy felhasználó neve. Három alapvető képernyőn monitorozási adatok az Azure-ból származik:

1. A tevékenységnapló-, amely minden vezérlősík műveleteket ismerteti az Azure-előfizetése.
2. Diagnosztikai naplók, amelyek az erőforrás által kibocsátott naplók.
3. Mérőszámok, amely erőforrások által kibocsátott vannak.

Ezeken az adattípusokon mindhárom egy tárfiókban tárolja, vagy adatfolyamként történő Event Hub, mindkettő általános célú Azure-erőforrások is. Mivel az általános célú erőforrásokat, létrehozása, törlése és hozzájuk férni jogosultsághoz kötve a rendszergazda számára fenntartva. Javasoljuk, hogy a következő források figyelést kapcsolatos eljárások visszaélések megelőzése érdekében használjon:

* Egy egyedi, dedikált tárfiókot használják a figyelési adatok. Ha figyelési adatok szét több tárfiókra van szüksége, ne ossza meg a storage-fiók közötti figyelési használat, és előfordulhat, hogy-megfigyelési adatokat, mivel ezt véletlenül adjon azoknak, akik csak a figyelési adatok (például egy harmadik fél SIEM) hozzáférésre van szükségük hozzáférés figyelési adatokat.
* Használjon egy egyedi, dedikált a Service Bus- vagy Event Hub-névtér összes diagnosztikai beállítások között ugyanebből az okból a fenti.
* Férjenek hozzá az kapcsolatos figyelési storage-fiókok és az event hubs tartja őket egy külön erőforráscsoportot, és [hatókört használja](../role-based-access-control/overview.md#scope) a figyelési szerepkörök csak adott erőforráscsoporton való hozzáférés korlátozására.
* Soha nem engedélyezheti a listkeys műveletének tárfiókokat vagy az event hubs előfizetési hatókörben, amikor a felhasználó csak a figyelési adatok hozzá kell férnie. Ehelyett adhat ezeket az engedélyeket a felhasználó egy erőforrás vagy erőforráscsoport (Ha rendelkezik egy dedikált figyelési erőforráscsoport) hatókör.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Figyelés kapcsolódó tárfiókok való hozzáférés korlátozása
Amikor egy felhasználó vagy alkalmazás a monitorozási adatok tárfiókban való hozzáférésre van szüksége, érdemes [egy fiók SAS előállítása](https://msdn.microsoft.com/library/azure/mt584140.aspx) a tárfiók, amely a blob storage szolgáltatói csak olvasási hozzáféréssel rendelkező figyelési adatokat tartalmaz. A PowerShell a következőhöz hasonló lehet:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Ezután biztosíthat a jogkivonat az entitáshoz, hogy kell olvasni, hogy a tárolási fiók, és is listázása és a storage-fiókban lévő összes BLOB olvasni.

Azt is megteheti Ha ezt az engedélyt az RBAC vezérlésére van szüksége, meg lehet adni entitás az Microsoft.Storage/storageAccounts/listkeys/action engedélyt az adott tárfiók. Erre azért szükség a felhasználók számára a diagnosztikai beállítás vagy naplóprofil kell archiválni egy tárfiókba képeseknek kell lenniük. Létrehozhat például, hogy egy felhasználó vagy alkalmazás, amelyet csak egy storage-fiókból olvassa el a következő egyéni RBAC szerepkör:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [!WARNING]
> A listkeys műveletének engedély lehetővé teszi, hogy a felhasználót, hogy az elsődleges és másodlagos tárfiókkulcsok listázása. Ezek a kulcsok adja meg a felhasználónak minden aláírt engedélyek (olvasás, írás, blobok létrehozása, törlése, blobok, stb.) minden aláírt szolgáltatásokhoz (blob, queue, table, fájl) a tárfiók. Ha lehetséges, a fent leírt fiók SAS használatát javasoljuk.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Figyelés kapcsolatos event hubs-hozzáférés korlátozása
Az event hubs használatával követheti hasonló mintát, de először hozzon létre egy dedikált Listen engedélyezési szabályt. Ha szeretne hozzáférést biztosítani egy alkalmazás, amely csak a figyelési kapcsolatos event hubs figyelni kell, tegye a következőket:

1. Megosztott hozzáférési szabályzat létrehozása a streamelési figyelési jogcímek csak a figyelési adatok létrehozott esemény példáink a. Ezt megteheti a portálon. Például előfordulhat, hogy felhívja azt "monitoringReadOnly." Ha lehetséges érdemes a kulcs közvetlenül adhat a fogyasztói, majd ugorjon a következő lépéssel.
2. Ha a fogyasztó kell helyeznie a kulcs az ad-hoc van szüksége, adja meg a felhasználónak az adott event hubs listkeys műveletének műveletet. Ez akkor is szükséges a felhasználók számára a diagnosztikai beállítás, vagy jelentkezzen profil streamelés az event hubs képeseknek kell lenniük. Például előfordulhat, hogy az RBAC szabály létrehozása:
   
   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Biztonságos virtuális hálózaton belüli figyelése

Az Azure Monitor az Azure-erőforrások engedélyezi a szolgáltatások biztosítása érdekében hozzá kell férnie. Ha szeretné az Azure-erőforrások figyelése közben továbbra is biztonságossá tétele őket a hozzáférés a nyilvános internetre, engedélyezheti a következő beállításokat.

### <a name="secured-storage-accounts"></a>Biztonságos Storage-fiókok 

Monitorozási adatok tárfiókba gyakran íródik. Győződjön meg arról, hogy az adatokat másolni a Storage-fiók nem érhető el a jogosulatlan felhasználók érdemes. A fokozott biztonság érdekében zárolhatja engedélyezése csak az arra jogosult erőforrásokhoz való hálózati hozzáférés és a egy storage-fiókot a Microsoft services megbízható access egy storage-fiók használata a "kijelölt hálózatok" korlátozásával.
![Az Azure Storage beállításai párbeszédpanel](./media/monitoring-roles-permissions-security/secured-storage-example.png) Azure Monitor tekinteni egy ilyen "megbízható Microsoft-szolgáltatások", ha engedélyezi a megbízható Microsoft-szolgáltatások, a biztonságos tároló eléréséhez, az Azure monitor hozzáférhet a titkosított tárfiókba; engedélyezése diagnosztikai naplók az Azure Monitor tevékenységnapló és metrikák írása a Storage-fiók ezen védett körülmények között. Ezzel is engedélyezi a Log Analyticsben, hogy biztonságos tároló naplóinak olvasására.   


További információkért lásd: [hálózati, biztonsági és az Azure Storage](../storage/common/storage-network-security.md)

## <a name="next-steps"></a>További lépések
* [További információ az RBAC és engedélyek a Resource Managerben](../role-based-access-control/overview.md)
* [Olvassa el az Azure-beli Figyelés áttekintése](../azure-monitor/overview.md)

