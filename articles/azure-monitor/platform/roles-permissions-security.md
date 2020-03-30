---
title: Szerepkörök, engedélyek és biztonság az Azure Monitorban
description: Ismerje meg, hogyan használhatja az Azure Monitor beépített szerepköreit és engedélyeit a figyelési erőforrásokhoz való hozzáférés korlátozására.
author: johnkemnetz
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 81309f0b5781e6302887a5b079ed359e70659834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658982"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Szerepkörök, engedélyek és biztonság az Azure Monitorban

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Sok csapatnak szigorúan szabályoznia kell a figyelési adatokhoz és beállításokhoz való hozzáférést. Ha például olyan csapattagjai vannak, akik kizárólag a figyelésen dolgoznak (támogatási mérnökök, DevOps-mérnökök), vagy ha felügyelt szolgáltatót használ, akkor csak a figyelési adatokhoz szeretne hozzáférést biztosítani számukra, miközben korlátozza a létrehozási, módosítási vagy erőforrások törléséhez. Ez a cikk bemutatja, hogyan lehet gyorsan alkalmazni egy beépített figyelési RBAC szerepkört egy azure-beli felhasználóra, vagy saját egyéni szerepkört hozhat létre egy korlátozott figyelési engedélyre szoruló felhasználó számára. Ezután ismerteti az Azure Monitorhoz kapcsolódó erőforrások biztonsági szempontjait, és azt, hogy hogyan korlátozhatja a hozzáférést a benne lévő adatokhoz.

## <a name="built-in-monitoring-roles"></a>Beépített monitorozási szerepkörök
Az Azure Monitor beépített szerepkörei úgy vannak kialakítva, hogy korlátozzák az erőforrásokhoz való hozzáférést egy előfizetésben, miközben továbbra is lehetővé teszik az infrastruktúra figyelési felelősei számára a szükséges adatok beszerzése és konfigurálása. Az Azure Monitor két beépített szerepkört biztosít: egy figyelési olvasót és egy figyelési közreműködőt.

### <a name="monitoring-reader"></a>Monitoring olvasó
A Figyelési olvasó szerepkörhöz rendelt személyek megtekinthetik az előfizetés összes figyelési adatát, de nem módosíthatják az erőforrásokat, és nem szerkeszthetik az erőforrások figyeléséhez kapcsolódó beállításokat. Ez a szerepkör megfelelő a szervezet felhasználói számára, például a támogatási vagy üzemeltetési mérnökök számára, akiknek a következőket kell tudniuk:

* Megtekintheti a figyelési irányítópultokat a portálon, és létrehozhatja saját privát figyelési irányítópultjait.
* [Az Azure-riasztásokban](alerts-overview.md) definiált riasztási szabályok megtekintése
* Metrikák lekérdezése az [Azure Monitor REST API,](https://msdn.microsoft.com/library/azure/dn931930.aspx) [A PowerShell-parancsmagok](powershell-quickstart-samples.md)vagy a [platformfüggetlen CLI](cli-samples.md)használatával.
* A tevékenységnapló lekérdezése a portál, az Azure Monitor REST API, a PowerShell-parancsmagok vagy a platformfüggetlen CLI használatával.
* Az erőforrás [diagnosztikai beállításainak](diagnostic-settings.md) megtekintése.
* Előfizetés [naplóprofiljának](activity-log-export.md) megtekintése.
* Automatikus skálázási beállítások megtekintése.
* A riasztási tevékenység és a beállítások megtekintése.
* Elérheti az Application Insights-adatokat, és megtekintheti az ai Analytics adatait.
* Keressen a Log Analytics munkaterületi adatai között, beleértve a munkaterület használati adatait is.
* Tekintse meg a Log Analytics felügyeleti csoportokat.
* A keresési séma lekérése a Log Analytics-munkaterületen.
* A figyelési csomagok listázása a Log Analytics-munkaterületen.
* Mentett keresések lekérése és végrehajtása a Log Analytics-munkaterületen.
* A Log Analytics munkaterület-tárolási konfigurációjának beolvasása.

> [!NOTE]
> Ez a szerepkör nem ad olvasási hozzáférést az eseményközpontba streamelt vagy egy tárfiókban tárolt naplóadatokhoz. [Az](#security-considerations-for-monitoring-data) erőforrásokhoz való hozzáférés konfigurálásával kapcsolatos további tudnivalókat alább talál.
> 
> 

### <a name="monitoring-contributor"></a>Közreműködő figyelése
A közreműködői szerepkörhöz rendelt személyek megtekinthetik az előfizetés összes figyelési adatát, és létrehozhatják vagy módosíthatják a figyelési beállításokat, de nem módosíthatják az egyéb erőforrásokat. Ez a szerepkör a Figyelési olvasó szerepkör egyik szuperhalmaza, és megfelelő a szervezet figyelési csapatának tagjai vagy felügyelt szolgáltatók számára, akiknek a fenti engedélyeken kívül a következőket is tudniuk kell:

* Figyelési irányítópultok közzététele megosztott irányítópultként.
* Erőforrás [diagnosztikai beállításainak](diagnostic-settings.md) megadása.\*
* Állítsa be az előfizetés [naplóprofilját.](activity-log-export.md)\*
* Állítsa be a riasztási szabályok tevékenységét és [beállításait az Azure Alerts](alerts-overview.md)segítségével.
* Hozzon létre Application Insights webes teszteket és összetevőket.
* List Log Analytics munkaterület megosztott kulcsok.
* A figyelési csomagok engedélyezése vagy letiltása a Log Analytics-munkaterületen.
* Mentett keresések létrehozása és végrehajtása a Log Analytics-munkaterületen.
* Hozza létre és törölje a Log Analytics munkaterület-tárolási konfigurációját.

\*a felhasználónak külön listkeys engedélyt kell kapnia a célerőforrásra (tárfiók vagy eseményközpont névterére) a naplóprofil vagy a diagnosztikai beállítás beállításához.

> [!NOTE]
> Ez a szerepkör nem ad olvasási hozzáférést az eseményközpontba streamelt vagy egy tárfiókban tárolt naplóadatokhoz. [Az](#security-considerations-for-monitoring-data) erőforrásokhoz való hozzáférés konfigurálásával kapcsolatos további tudnivalókat alább talál.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Monitorozási engedélyek és egyéni RBAC-szerepkörök
Ha a fenti beépített szerepkörök nem felelnek meg a csapat pontos igényeinek, [létrehozhat egy egyéni RBAC-szerepkört](../../role-based-access-control/custom-roles.md) részletesebb engedélyekkel. Az alábbiakban a közös Azure Monitor RBAC-műveletek a leírásokat.

| Művelet | Leírás |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Olvasás, írás, törlés] |Műveletcsoportok olvasása/írása/törlése. |
| Microsoft.Insights/ActivityLogAlerts/[Olvasás, írás, törlés] |Tevékenységnapló-riasztások olvasása/írása/törlése. |
| Microsoft.Insights/AlertRules/[Olvasás, írás, törlés] |Olvasási/írási/törlési figyelmeztetési szabályok (klasszikus riasztásokból). |
| Microsoft.Insights/AlertRules/Incidents/Read |A riasztási szabályok hoz készült incidensek (a riasztási szabály előzményei) listázása. Ez csak a portálra vonatkozik. |
| Microsoft.Insights/AutoscaleSettings/[Olvasás, írás, törlés] |Automatikus méretezési beállítások olvasása/írása/törlése. |
| Microsoft.Insights/DiagnosticSettings/[Olvasás, írás, törlés] |Diagnosztikai beállítások olvasása/írása/törlése. |
| Microsoft.Insights/EventCategories/Read |A tevékenységnaplóban lehetséges összes kategória felsorolása. Az Azure Portal által használt. |
| Microsoft.Insights/eventtypes/digestevents/Read |Ez az engedély szükséges azoknak a felhasználóknak, akiknek a portálon keresztül hozzáférésre van szükségük a tevékenységnaplókhoz. |
| Microsoft.Insights/eventtypes/values/Read |Tevékenységnapló-események (felügyeleti események) listázása egy előfizetésben. Ez az engedély a tevékenységnaplóhoz való programozott és portálos hozzáférésre is vonatkozik. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Olvasás, írás, törlés] | A hálózati folyamatnaplók diagnosztikai beállításainak olvasása/írása/törlése. |
| Microsoft.Insights/LogDefinitions/Read |Ez az engedély szükséges azoknak a felhasználóknak, akiknek a portálon keresztül hozzáférésre van szükségük a tevékenységnaplókhoz. |
| Microsoft.Insights/LogProfiles/[Olvasás, írás, törlés] |Olvasási/írási/törlési naplóprofilok (streamelési tevékenységnapló az eseményközpontba vagy a tárfiókba). |
| Microsoft.Insights/MetricAlerts/[Olvasás, írás, törlés] |Olvasási/írási/törlési adatok közel valós idejű metrikariasztások |
| Microsoft.Insights/MetricDefinitions/Read |Metrikadefiníciók olvasása (egy erőforrás elérhető metrikatípusainak listája). |
| Microsoft.Insights/Metrics/Read |Egy erőforrás metrikáinak olvasása. |
| Microsoft.Insights/Register/Action |Regisztrálja az Azure Monitor erőforrás-szolgáltatót. |
| Microsoft.Insights/ScheduledQueryRules/[Olvasás, írás, törlés] |Olvasási/írási/törlési naplóriasztások az Azure Monitorban. |



> [!NOTE]
> Egy erőforrás riasztásainak, diagnosztikai beállításainak és metrikáinak eléréséhez a felhasználónak olvasási hozzáférése kell, hogy legyen az adott erőforrás erőforrástípusához és hatóköréhez. Hozzon létre ("write") egy diagnosztikai beállítás vagy naplóprofil, amely archiválja a tárfiókot vagy adatfolyamok eseményközpontok hoz létre a felhasználónak is listkeys engedéllyel a cél erőforrás.
> 
> 

A fenti táblázat használatával például létrehozhat egy egyéni RBAC-szerepkört egy "Tevékenységnapló-olvasóhoz", mint ez:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Biztonsági szempontok az adatok monitorozásához
Az adatok figyelése – különösen a naplófájlok – bizalmas információkat tartalmazhatnak, például IP-címeket vagy felhasználóneveket. Az Azure-ból származó adatok figyelése három alapvető formában található:

1. A tevékenységnapló, amely az Azure-előfizetés összes vezérlősík-műveletét ismerteti.
2. erőforrásnaplók, amelyek egy erőforrás által kibocsátott naplók.
3. Az erőforrások által kibocsátott metrikák.

Mindhárom adattípus tárolható egy tárfiókban, vagy streamelhető az Event Hubra, amelyek mindegyike általános célú Azure-erőforrás. Mivel ezek általános célú erőforrások, a létrehozásuk, a törlésük és a hozzáférésük egy rendszergazda számára fenntartott kiemelt fontosságú művelet. Javasoljuk, hogy a visszaélések megelőzése érdekében használja az alábbi módszereket a kapcsolódó erőforrások figyelésére:

* Egyetlen dedikált tárfiók használata az adatok figyeléséhez. Ha több tárfiókra kell elkülönítenie a figyelési adatokat, soha ne ossza meg a tárfiók használatát a figyelési és a nem figyelési adatok között, mivel ez véletlenül hozzáférést biztosíthat azoknak, akiknek csak a figyelési adatokhoz (például egy harmadik fél SIEM-hez) van szükségük. a nem nyomon követési adatokhoz való hozzáférés.
* Egyetlen, dedikált Service Bus vagy Event Hub névtér használata az összes diagnosztikai beállítás ugyanazon okból, mint fent.
* Korlátozza a hozzáférést a figyelési kapcsolatos tárfiókokhoz vagy eseményközpontokhoz azáltal, hogy külön erőforráscsoportban tartja őket, és a figyelési szerepkörök [hatókörének használatával](../../role-based-access-control/overview.md#scope) csak az adott erőforráscsoportra korlátozza a hozzáférést.
* Soha ne adjon a ListKeys engedélyt tárfiókok vagy eseményközpontok előfizetési hatókörben, ha a felhasználónak csak hozzáférésre van szüksége a figyelési adatokhoz. Ehelyett adja meg ezeket az engedélyeket a felhasználónak egy erőforrás vagy erőforráscsoport (ha van egy dedikált figyelési erőforráscsoport) hatókör.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>A figyeléssel kapcsolatos tárfiókokhoz való hozzáférés korlátozása
Ha egy felhasználónak vagy alkalmazásnak hozzáférésre van szüksége egy tárfiókban lévő figyelési adatokhoz, létre kell [hoznia egy fiók SAS-t](https://msdn.microsoft.com/library/azure/mt584140.aspx) a tárfiókon, amely szolgáltatásszintű írásvédett hozzáférést tartalmaz a blobstorage-hoz. A PowerShellben ez a következőkre néz ki:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Ezután adja meg a jogkivonatot az entitásnak, amelynek olvasnia kell az adott tárfiókból, és listázhatja és olvashatja az adott tárfiók összes blobját.

Másik lehetőségként, ha az RBAC-mal kell szabályoznia ezt az engedélyt, az adott tárfiókhoz a Microsoft.Storage/storageAccounts/listkeys/action engedélyt adhat az adott tárfiókhoz. Ez szükséges a felhasználók számára, akiknek képesnek kell lenniük arra, hogy diagnosztikai beállítást vagy naplóprofilt állítsanak be egy tárfiókarchiváláshoz. Létrehozhatja például a következő egyéni RBAC-szerepkört egy olyan felhasználóvagy alkalmazás számára, amelyet csak egy tárfiókból kell olvasnia:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> A ListKeys engedély lehetővé teszi, hogy a felhasználó felsorolja az elsődleges és másodlagos tárfiók kulcsokat. Ezek a kulcsok minden aláírt engedélyt (olvasási, írási, blobok létrehozása, blobok törlése stb.) biztosítanak a felhasználónak az adott tárfiók összes aláírt szolgáltatásában (blob, várólista, tábla, fájl). Javasoljuk, hogy lehetőség szerint használjon a fent leírt fiókSAS-t.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>A figyeléssel kapcsolatos eseményközpontokhoz való hozzáférés korlátozása
Hasonló minta követhető az eseményközpontok, de először létre kell hoznia egy dedikált listen engedélyezési szabályt. Ha hozzáférést szeretne adni egy olyan alkalmazáshoz, amelynek csak a figyeléssel kapcsolatos eseményközpontokat kell meghallgatnia, tegye a következőket:

1. Hozzon létre egy megosztott hozzáférési szabályzatot az eseményközpont(ok)on, amelyek et csak listen jogcímekkel rendelkező figyelési adatok streameléséhez hoztak létre. Ezt meg lehet tenni a portálon. Például hívhatja "monitoringReadOnly." Ha lehetséges, akkor azt szeretné, hogy a kulcsot közvetlenül a fogyasztónak, és hagyja ki a következő lépést.
2. Ha a fogyasztónak képesnek kell lennie a kulcs ad hoc beírására, adja meg a felhasználónak a ListKeys műveletet az adott eseményközponthoz. Ez olyan felhasználók számára is szükséges, akiknek diagnosztikai beállítást vagy naplóprofilt kell beállítaniuk az eseményközpontokba való streameléshez. Létrehozhat például egy RBAC-szabályt:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Monitorozás biztonságos virtuális hálózaton belül

Az Azure Monitornak hozzáférésre van szüksége az Azure-erőforrásokhoz az Ön által lehetővé teszszolgáltatások biztosításához. Ha szeretné figyelni az Azure-erőforrásokat, miközben továbbra is biztosítja őket a nyilvános internethez való hozzáférés, engedélyezheti a következő beállításokat.

### <a name="secured-storage-accounts"></a>Biztonságos tárfiókok 

Figyelési adatok gyakran írt egy tárfiókba. Érdemes lehet győződjön meg arról, hogy a tárfiókba másolt adatokat nem férhetnek hozzá a jogosulatlan felhasználók. A nagyobb biztonság érdekében zárolhatja a hálózati hozzáférést, hogy csak az engedélyezett erőforrások és a megbízható Microsoft-szolgáltatások számára engedélyezze a tárfiókhoz való hozzáférést azáltal, hogy korlátozza a tárfiókot a "kiválasztott hálózatok" használatára.
![Az Azure](./media/roles-permissions-security/secured-storage-example.png) Storage Settings Dialog Azure Monitor a következő "megbízható Microsoft-szolgáltatások" egyike, ha engedélyezi a megbízható Microsoft-szolgáltatások nak a biztonságos tárhoz való hozzáférést, az Azure-figyelő hozzáférhet a biztonságos tárfiókhoz; az Azure Monitor erőforrásnaplók, tevékenységnaplók és metrikák írásának engedélyezése a tárfiókba ezek a védett feltételek mellett. Ez azt is lehetővé teszi, hogy a Log Analytics a biztonságos tárolóból származó naplók olvasására.   


További információ: [Network security and Azure Storage](../../storage/common/storage-network-security.md)

## <a name="next-steps"></a>További lépések
* [Az RBAC és az engedélyek az Erőforrás-kezelőben](../../role-based-access-control/overview.md)
* [A figyelés áttekintése az Azure-ban](../../azure-monitor/overview.md)


