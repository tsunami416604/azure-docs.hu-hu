---
title: Ismerkedés a szerepkörök, engedélyek és biztonsági Azure megfigyelővel |} Microsoft Docs
description: Ismerje meg, hogyan használható az Azure figyelő beépített szerepköröket és engedélyeket figyelési erőforrásokhoz való hozzáférés korlátozása.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2686e53b-72f0-4312-bcd3-3dc1b4a9b912
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: johnkem
ms.openlocfilehash: 81f083b799e359f69605de22c30d3adc4480e44b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Ismerkedés a szerepkörök, engedélyek és biztonságának és az Azure-figyelő
Sok csapatok kell szigorúan szabályozzák, figyelési adatok és beállítások. Például ha vannak olyan dolgozó kizárólag figyelése (a támogatási szakértők, devops mérnökök) csoport tagjai, vagy ha egy felügyelt szolgáltató használja, érdemes lehet hozzáférést azokat csak figyelési adatok korlátozásával létrehozása, módosítása, vagy törli az erőforrást. Ez a cikk bemutatja, hogyan gyorsan figyelési beépített RBAC szerepkör alkalmazni a felhasználók az Azure-ban vagy a saját egyéni szerepkör a felhasználókat, akiknek korlátozott felügyeleti engedélyekkel. A cikk ismerteti az Azure-figyelő kapcsolódó erőforrások és hogyan korlátozhatja az adatok tartalmazzák a hozzáférést a biztonsági szempontok majd.

## <a name="built-in-monitoring-roles"></a>Beépített figyelési szerepkörök
Az Azure figyelő beépített szerepkörök útmutatók célja, továbbra is engedélyezze az beszerzése és konfigurálja a szükséges adatok infrastruktúra figyelése egy előfizetésben erőforrásokhoz való hozzáférés korlátozása. Az Azure két out-of-az-box szerepkörök biztosít: A figyelési olvasó és a figyelés közreműködő.

### <a name="monitoring-reader"></a>Olvasó figyelése
A figyelés olvasó szerepét személyek összes figyelési adatok megtekintése az előfizetés, de nem módosíthatja bármely erőforrás vagy bármely források követésével kapcsolatos beállítások szerkesztése. A szerepkör megfelelő vállalatnál, például a támogatási szolgálathoz vagy a műveletek mérnökök, akiknek kell tennie:

* Figyelési irányítópult megtekintése a portálon, és a saját személyes figyelési irányítópultot létrehozni.
* Megtekintheti a definiált riasztási szabályok [Azure riasztások](monitoring-overview-unified-alerts.md)
* Metrikák használatára vonatkozó lekérdezés a [Azure figyelő REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx), [PowerShell-parancsmagok](insights-powershell-samples.md), vagy [platformfüggetlen parancssori felület](insights-cli-samples.md).
* A lekérdezés a műveletnapló a portálon, a Azure figyelő REST API-t, a PowerShell-parancsmagok vagy a platformfüggetlen parancssori felület használatával.
* Nézet a [diagnosztikai beállítások](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) erőforrás.
* Nézet a [profilt naplózni](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) az előfizetéshez.
* Automatikus skálázási beállítások megtekintéséhez.
* Riasztási tevékenység megtekintése és a beállítások.
* Az Application Insights adatokat, és AI Analytics adatainak megtekintéséhez.
* Keresse meg a Naplóelemzési munkaterület adatokat, többek között a munkaterület vonatkozó használati adatokat.
* Log Analytics felügyeleti csoportoknak a megtekintése.
* A Naplóelemzési keresési séma beolvasása.
* Naplóelemzési eszközintelligencia csomagok felsorolása.
* Kérje le, majd a mentett keresések Naplóelemzési hajtható végre.
* A Naplóelemzési tárkonfiguráció beolvasása.

> [!NOTE]
> Ez a szerepkör nem szolgálnak olvasási hozzáférés egy eseményközpontba folyamatos átviteli vagy a storage-fiókban tárolt Teljesítménynapló-adatokat. [Lásd az alábbi](#security-considerations-for-monitoring-data) ezekhez az erőforrásokhoz való hozzáférés konfigurálásával kapcsolatos információkat.
> 
> 

### <a name="monitoring-contributor"></a>Közreműködő figyelése
A figyelési közreműködői szerepkör hozzárendelése mások is láthatják az összes figyelési adatok az előfizetés és hozzon létre vagy figyelési beállítások azonban nem lehet módosítani más erőforrásokat. Ez a szerepkör kibővíti a figyelési olvasó szerepkört, és megfelelő-e a szervezetek figyelési team vagy felügyelt szolgáltatók, akik mellett a fentiek engedélyeket is kell tennie:

* Tegye közzé a figyelési irányítópult megosztott irányítópultként.
* Állítsa be [diagnosztikai beállítások](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) egy resource.* számára
* Állítsa be a [profilt naplózni](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) egy subscription.* számára
* Riasztási szabályok tevékenység és a beállítások segítségével [Azure riasztások](monitoring-overview-unified-alerts.md).
* Az Application Insights webalkalmazás-tesztek és összetevők létrehozása.
* Lista Naplóelemzési munkaterület a megosztott kulcsok szükségesek.
* Engedélyezi vagy letiltja a Naplóelemzési eszközintelligencia csomagokat.
* Hozzon létre és törlése mentett keresések Naplóelemzési hajtható végre.
* Hozzon létre, és a Naplóelemzési tárkonfiguráció törlése.

* felhasználói külön is engedéllyel kell ListKeys a cél erőforráson (tárolási fiók vagy esemény hub névtér) beállítása egy napló profil vagy a diagnosztikai beállítás.

> [!NOTE]
> Ez a szerepkör nem szolgálnak olvasási hozzáférés egy eseményközpontba folyamatos átviteli vagy a storage-fiókban tárolt Teljesítménynapló-adatokat. [Lásd az alábbi](#security-considerations-for-monitoring-data) ezekhez az erőforrásokhoz való hozzáférés konfigurálásával kapcsolatos információkat.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Figyelési engedélyek és egyéni RBAC-szerepkörök
Ha a fenti beépített szerepkörök nem felelnek meg a csoport pontos igényeinek, akkor [hozzon létre egy egyéni RBAC szerepkör](../active-directory/role-based-access-control-custom-roles.md) részletesebb engedélyekkel. Az alábbiakban azok leírásait tartalmazza az általános Azure RBAC-figyelő műveleteket.

| Művelet | Leírás |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |A csoport olvasási, írási és törlési művelet. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Olvasási, írási és törlési tevékenység napló riasztásokat. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Olvasási, írási és törlési riasztási szabályok (a riasztások klasszikus). |
| Microsoft.Insights/AlertRules/Incidents/Read |A riasztási szabályok listázása incidensek (váltódik ki, hogy a riasztási szabály előzmények). Ez csak a portál vonatkozik. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Olvasási, írási és törlési automatikus skálázási beállításokat. |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |Olvasási, írási és törlési diagnosztikai beállítások. |
| Microsoft.Insights/EventCategories/Read |Az összes kategória lehetséges a műveletnaplóban számbavétele. Az Azure portál által használt. |
| Microsoft.Insights/eventtypes/digestevents/Read |Ez az engedély szükség a felhasználók számára, akiknek tevékenységi naplóit a portálon hozzáférésre van szükségük. |
| Microsoft.Insights/eventtypes/values/Read |Tevékenységnapló események (felügyeleti események) egy előfizetésben listában. Ezzel az engedéllyel csak a műveletnapló programozott és a portál eléréséhez alkalmazható. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | Olvasási, írási és törlési diagnosztikai beállítások hálózati folyamata naplókhoz. |
| Microsoft.Insights/LogDefinitions/Read |Ez az engedély szükség a felhasználók számára, akiknek tevékenységi naplóit a portálon hozzáférésre van szükségük. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |Olvasási, írási és törlési napló profilok (tevékenységnapló streaming event hub vagy tárolási fiókhoz). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Olvasási, írási és törlési közel valós idejű metrika riasztások |
| Microsoft.Insights/MetricDefinitions/Read |Olvassa el a metrikai meghatározásainak (erőforrás elérhető metrika típusok listája). |
| Microsoft.Insights/Metrics/Read |Egy erőforrás olvasni. |
| Microsoft.Insights/Register/Action |Az Azure-figyelő erőforrás-szolgáltató regisztrálása. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Az Application Insights riasztásainak olvasási, írási és törlési napló. |



> [!NOTE]
> Elérhető riasztásokat, a diagnosztikai beállítások és a metrikák egy erőforrást igényel, hogy a felhasználó rendelkezik-e az erőforrás típusát és a hatókör erőforrás olvasási hozzáférést. ("Write"), amely egy tárfiókhoz vagy az event hubs adatfolyamok archiválja diagnosztikai beállítás vagy a napló profil létrehozásához a felhasználó számára ListKeys engedéllyel is rendelkezik a cél erőforráson.
> 
> 

A fenti táblázatban egy "tevékenység napló olvasó" ilyen létrehozhat egy egyéni RBAC-szerepkör használata esetén például:

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

## <a name="security-considerations-for-monitoring-data"></a>Figyelési adatok biztonsági szempontjai
Figyelési adatok – különösen naplófájlok – tartalmazhatnak bizalmas adatokat, például az IP-címek vagy felhasználó neve. Három alapvető képernyőn tartalmaz figyelési adatok az Azure-ból:

1. A műveletnapló, amely minden vezérlő-vezérlősík műveleteket ismerteti az Azure-előfizetéshez.
2. Diagnosztikai naplók, amelyek az erőforrás által kibocsátott naplókat.
3. Metrika, amely erőforrások által kibocsátott.

Ezek az adattípusok három storage-fiókban tárolt, vagy Eseményközpont, amelyek mindkettő általános célú Azure-erőforrások részére. Mivel ezek az általános célú erőforrások, létrehozása, törlése és hozzájuk férni a rendszergazda számára fenntartott kiemelt művelet. Javasoljuk, hogy az alábbi eljárásokkal kapcsolatos megfigyelési erőforrások megakadályozására használja:

* A figyelési adatok dedikált tárolási fiók használata. Figyelési adatok több tárfiókot külön kell, ha ne ossza meg a storage-fiókok között figyelés használatát, és előfordulhat, hogy nem figyelési adatok, ez véletlenül adjon azok, akik csak a figyelési adatok (például egy harmadik fél SIEM) hozzáféréssel kell rendelkezniük hozzáférést nem figyelési adatokat.
* Használja a dedikált Service Bus vagy az Event Hubs-névtér összes diagnosztikai beállítások ugyanebből az okból a fenti.
* Korlátozhatja az kapcsolatos megfigyelési tárfiók vagy az event hubs egy külön erőforráscsoportot tartva és [hatókört használja](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) a figyelési szerepkörökhöz való hozzáférés csak ennek az erőforráscsoportnak korlátozására.
* Soha nem engedélyt ListKeys storage-fiókok vagy az event hubs előfizetési hatókört, ha a felhasználó csak nem figyelési adatok eléréséhez. Ehelyett biztosítják azokat az engedélyeket a felhasználó egy erőforrás vagy az erőforráscsoportot, (ha van egy dedikált figyelési erőforráscsoport) hatókör.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Figyelési kapcsolatos tárfiókok való hozzáférés korlátozása
Ha egy felhasználó vagy alkalmazás figyelését a tárfiókban lévő adatokat a hozzáférést, akkor [egy fiók SAS létre](https://msdn.microsoft.com/library/azure/mt584140.aspx) a tárfiók, amely tartalmazza a szolgáltatási szint csak olvasási hozzáféréssel a blob storage figyelési adatokat. A PowerShellben ez látható:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Majd biztosíthat a jogkivonat az entitásban, hogy kell olvasni a tárolási fiók, és azt is listában és olvassa el az összes BLOB storage-fiókhoz tartozó.

Azt is megteheti Ha szeretne vezérelni az RBAC ezzel az engedéllyel, engedélyt adhat az entitás a Microsoft.Storage/storageAccounts/listkeys/action a tárolási fiók. Erre akkor szükség a felhasználók számára a diagnosztikai beállítás megadása, vagy a storage-fiók profil archiválására jelentkezzen képesnek kell lenniük. Például létrehozhatja a következő egyéni RBAC-szerepkör egy felhasználó vagy alkalmazás, amelyet a csak olvasható egy tárfiókot:

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
> A ListKeys engedély lehetővé teszi a felhasználó számára az elsődleges és másodlagos tárfiókkulcsok listázása. Ezek a kulcsok adja meg a felhasználónak minden aláírt engedélyeket (olvasás, írás, blobok létrehozása, törlése, blobok stb.) összes aláírt szolgáltatások (blob, várólista, tábla, fájl), hogy a tárfiók. Egy fiók SAS, ha lehetséges, a fent leírt használatát javasoljuk.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Figyelési kapcsolatos az event hubs való hozzáférés korlátozása
Az event hubs gyűjtheti hasonló mintát, de először hozzon létre egy dedikált figyelési engedélyezési szabályt kell. Ha meg szeretné adni egy alkalmazás, amely csak a figyelési kapcsolatos event hubs figyelni kell a hozzáférést, tegye a következőket:

1. Az esemény hub(s) az adatfolyamként történő figyelési adatok csak a figyelési JOGCÍMEKKEL rendelkező és létrehozott egy megosztott hozzáférési házirend létrehozása Ezt megteheti a portálon. Például akkor lehet, hogy neki "monitoringReadOnly." Ha lehetséges érdemes kulcs adjon közvetlenül a fogyasztó számára, és ugorjon a következő lépéssel.
2. Ha a fogyasztó kell helyeznie a kulcs alkalmi, adja meg a felhasználónak az adott eseményközpont ListKeys művelet. Ez akkor is szükséges, a felhasználók számára a diagnosztikai beállítás megadása, vagy az event hubs adatfolyamot profil jelentkezzen képesnek kell lenniük. Például előfordulhat, hogy létre RBAC-szabályt:
   
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

## <a name="next-steps"></a>További lépések
* [További tudnivalók a Szerepalapú és engedélyeket az erőforrás-kezelőben](../active-directory/role-based-access-control-what-is.md)
* [Olvassa el az Azure-ban Figyelés áttekintése](monitoring-overview.md)

