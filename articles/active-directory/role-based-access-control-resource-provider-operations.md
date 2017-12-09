---
title: "Az Azure erőforrás-kezelő szolgáltató műveletek |} Microsoft Docs"
description: "A Microsoft Azure Resource Manager erőforrás-szolgáltató elérhető műveleteinek részletei"
services: active-directory
documentationcenter: 
author: jboeshart
manager: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: jaboes
ms.openlocfilehash: 7e25b9a77d5ba7b856c9616c8384a567b7d37bbd
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Az Azure Resource Manager erőforrás-szolgáltató üzemeltetése

Ez a dokumentum minden Microsoft Azure Resource Manager erőforrás-szolgáltató elérhető műveleteinek listázása. Ezek segítségével az egyéni szerepkörök az Azure-erőforrások részletes szerepköralapú hozzáférés-vezérlést (RBAC) engedélyeket biztosítanak. Ne feledje, ez nem az átfogó listáját, és műveletek előfordulhat, hogy kell hozzáadni vagy eltávolítani, mert egyes szolgáltatók frissül. Művelet karakterláncok kövesse formátuma `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Az átfogó és aktuális listáját használja a `Get-AzureRmProviderOperation` (a PowerShell) vagy `az provider operation list` (az Azure CLI v2) az Azure erőforrás-szolgáltatók műveletek.

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Művelet | Leírás |
|---|---|
|/ configuration/művelet|Frissítések bérlő konfigurációjához.|
|/ services/művelet|Frissíti a szolgáltatáspéldány a bérlőben.|
|/ configuration/írása|Létrehoz egy bérlői konfigurációt.|
|/Configuration/Read|Beolvassa a bérlő konfigurációjához.|
|/ services/írása|Hozza létre a szolgáltatáspéldány, a bérlő.|
|/Services/Read|A szolgáltatáspéldány a bérlő beolvasása.|
|/Services/DELETE|Törli a szolgáltatáspéldány a bérlőben.|
|/Services/servicemembers/Action|A szolgáltatás egy tag szolgáltatáspéldány hoz létre.|
|/Services/servicemembers/Read|Olvassa be a szolgáltatáspéldány tag a szolgáltatásban.|
|/Services/servicemembers/DELETE|Törli a tag példánya a szolgáltatásban.|
|/Services/servicemembers/Alerts/Read|A szolgáltatás tagjához olvassa be a riasztásokat.|
|/Services/Alerts/Read|Beolvassa a riasztásokat, a szolgáltatás számára.|
|/Services/Alerts/Read|Beolvassa a riasztásokat, a szolgáltatás számára.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Művelet | Leírás |
|---|---|
|/ generateRecommendations/művelet|Ajánlatok generálása|
|/ suppressions/művelet|Suppressions hoz létre, frissítések|
|/ regisztrációs/művelet|A Microsoft Advisor előfizetésének regisztrálása|
|/generateRecommendations/Read|Lekérdezi készítése javaslatok állapota|
|/recommendations/Read|Javaslatok beolvasása|
|/suppressions/Read|Lekérdezi a suppressions|
|/suppressions/DELETE|Tiltási törlése|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Művelet | Leírás |
|---|---|
|/Servers/Read|A megadott elemzési kiszolgáló adatainak beolvasása.|
|/ kiszolgálók/írása|Létrehozza vagy frissíti a megadott Analysis-kiszolgálóhoz.|
|/Servers/DELETE|Az elemzési kiszolgáló törlése.|
|/Servers/suspend/Action|Felfüggeszti az elemzési kiszolgáló.|
|/Servers/Resume/Action|Az elemzési kiszolgáló folytatja.|
|/ kiszolgálók/checkNameAvailability<br>/ művelet|Ellenőrzi, hogy a megadott Analysis Server neve érvényes, és nincs használatban.|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Művelet | Leírás |
|---|---|
|/ checkNameAvailability/művelet|Ellenőrzi, hogy a megadott szolgáltatásnév érhető el|
|/ regisztrációs/művelet|Előfizetés regisztrálása a Microsoft.ApiManagement erőforrás-szolgáltató|
|/ unregister/művelet|Előfizetés regisztrációjának Microsoft.ApiManagement erőforrás-szolgáltató|
|/ service/írása|Az API Management szolgáltatást egy új példányának létrehozása|
|/Service/Read|Olvassa el a metaadatokat az API Management szolgáltatáspéldány|
|/Service/DELETE|Törli az API Management Service-példány|
|/Service/updatehostname/Action|A telepítő, frissítésére, vagy távolítsa el az API Management szolgáltatás egyéni tartománynevek|
|/Service/uploadcertificate/Action|Az API Management szolgáltatás SSL-tanúsítvány feltöltése|
|/Service/Backup/Action|A biztonsági mentési API Management szolgáltatás a felhasználó a megadott tárolóhoz megadott tárfiók|
|/Service/restore/Action|Állítsa vissza egy felhasználó által megadott tárfiók a megadott tároló API Management szolgáltatás|
|/Service/managedeployments/Action|Módosítsa a Termékváltozat jegyek, hozzáadása regionális egy szolgáltatás üzemelő példányainak API Management|
|/Service/getssotoken/Action|Lekérdezi SSO token használható API Management szolgáltatás örökölt portáljára rendszergazdaként történő bejelentkezéshez|
|/Service/applynetworkconfigurationupdates/Action|Frissíti a Microsoft.ApiManagement erőforrásaihoz, a virtuális hálózatot válasszon frissített hálózati beállításokat.|
|/Service/operationresults/Read|Hosszú ideig futó művelet aktuális állapotának beolvasása|
|/Service/networkStatus/Read|Lekérdezi a hálózati erőforrások hozzáférési állapotát.|
|/Service/loggers/Read|Figyelő szoftverek listája vagy az naplózó részleteinek beolvasása|
|/Service/loggers/Write|Adja hozzá az új naplózó vagy meglévő naplózó részleteinek frissítése|
|/Service/loggers/DELETE|Távolítsa el a meglévő naplózó|
|/Service/Users/Read|A regisztrált felhasználók listáját, vagy felhasználói fiók adatainak lekérése|
|/Service/Users/Write|Új felhasználó vagy egy meglévő felhasználó frissítés fiókadatok regisztrálása|
|/Service/Users/DELETE|Felhasználói fiók eltávolítása|
|/Service/Users/generateSsoUrl/Action|Egyszeri bejelentkezési URL-címet létrehozni. Az URL-cím használható felügyeleti portál eléréséhez|
|/Service/Users/Subscriptions/Read|Felhasználói előfizetések listájának beolvasása|
|/Service/Users/keys/Read|Felhasználói kulcsok listájának beolvasása|
|/Service/Users/groups/Read|Felhasználói csoportok listájának beolvasása|
|/Service/tenant/operationResults/Read|A művelet eredménye listája vagy az beszerzése az adott művelet eredménye|
|/Service/tenant/Policy/Read|Házirend-konfigurációt a bérlő beolvasása|
|/Service/tenant/Policy/Write|A bérlőhöz tartozó házirend-konfiguráció|
|/Service/tenant/Policy/DELETE|A bérlőhöz tartozó házirend-konfiguráció eltávolítása|
|/Service/tenant/Configuration/Save/Action|A tárházban található a megadott ágra konfigurációs pillanatképet hoz létre a véglegesítési|
|/Service/tenant/Configuration/Deploy/Action|A megadott git fiókirodai módosítások alkalmazása a konfigurációs adatbázis telepítési feladat fut.|
|/Service/tenant/Configuration/Validate/Action|Ellenőrzi a megadott git fiókirodai módosítások|
|/Service/tenant/Configuration/operationResults/Read|A művelet eredménye listája vagy az beszerzése az adott művelet eredménye|
|/Service/tenant/Configuration/syncState/Read|Az utolsó git szinkronizálás állapotának beolvasása|
|/Service/tenant/Access/Read|Szerezze be a bérlő hozzáférési adatai|
|/Service/tenant/Access/Write|Bérlői hozzáférési információ részleteinek frissítése|
|/Service/tenant/Access/regeneratePrimaryKey/Action|Elsődleges elérési kulcs újragenerálása|
|/Service/tenant/Access/regenerateSecondaryKey/Action|Másodlagos elérési kulcs újragenerálása|
|/Service/identityProviders/Read|Az identitás-szolgáltatóktól vagy identitásszolgáltató Get részleteinek listáját beszerzése|
|/Service/identityProviders/Write|Hozzon létre egy új identitásszolgáltató vagy a frissítés részleteit egy meglévő identitásszolgáltató|
|/Service/identityProviders/DELETE|Távolítsa el a meglévő identitásszolgáltató|
|/Service/Subscriptions/Read|A termék előfizetések listáját, vagy a részletek a termék előfizetés|
|/Service/Subscriptions/Write|Egy meglévő felhasználó számára a meglévő termék előfizetés, vagy frissítse a meglévő előfizetés részletei. Ez a művelet használható előfizetés megújításához|
|/Service/Subscriptions/DELETE|Előfizetés törlése. Ez a művelet segítségével előfizetés törlése|
|/Service/Subscriptions/regeneratePrimaryKey/Action|Előfizetés elsődleges kulcs újragenerálása|
|/Service/Subscriptions/regenerateSecondaryKey/Action|Előfizetés másodlagos kulcs újragenerálása|
|/Service/backends/Read|Háttérkiszolgálókon listáját, vagy a részletek a háttér|
|/Service/backends/Write|Adja hozzá egy új háttér vagy a meglévő háttér részleteinek frissítése|
|/Service/backends/DELETE|Távolítsa el a létező háttérrendszerek|
|/Service/APIs/Read|Szerezze be az összes regisztrált API-k vagy Get részleteit API listát|
|/Service/APIs/Write|Hozzon létre új API-JÁVAL vagy a meglévő API részleteinek frissítése|
|/Service/APIs/DELETE|Távolítsa el a meglévő API|
|/Service/APIs/Policy/Read|Ezzel a házirend konfigurációs adatokat az API-hoz|
|/Service/APIs/Policy/Write|Állítsa be a házirend-konfigurációs adatait az API-hoz|
|/Service/APIs/Policy/DELETE|Távolítsa el a házirend-konfigurációs API|
|/Service/APIs/Operations/Read|A meglévő API műveletek listájának vagy részletes API művelet|
|/Service/APIs/Operations/Write|Új API-művelet létrehozni vagy frissíteni a meglévő API-művelet|
|/Service/APIs/Operations/DELETE|Távolítsa el a meglévő API-művelet|
|/Service/APIs/Operations/Policy/Read|Ezzel a házirend konfigurációs adatokat művelet|
|/Service/APIs/Operations/Policy/Write|Adja meg a művelet házirend konfigurációs részleteit|
|/Service/APIs/Operations/Policy/DELETE|Távolítsa el a házirend-konfigurációt a művelet|
|/Service/Products/Read|Termékek listáját, vagy a részletek a termék|
|/Service/Products/Write|Új termék létrehozása vagy meglévő termékadatok frissítése|
|/Service/Products/DELETE|Távolítsa el a meglévő termék|
|/Service/Products/Subscriptions/Read|Termék előfizetések listájának beolvasása|
|/Service/Products/APIs/Read|Szerezze be az API-k meglévő termékhez hozzáadott listát|
|/Service/Products/APIs/Write|Meglévő API hozzáadása meglévő termék|
|/Service/Products/APIs/DELETE|Távolítsa el a meglévő API a meglévő termék|
|/Service/Products/Policy/Read|Házirend-konfigurációt meglévő termék beszerzése|
|/Service/Products/Policy/Write|A termék meglévő házirend-konfiguráció|
|/Service/Products/Policy/DELETE|Távolítsa el a házirend-konfigurációt a meglévő termék|
|/Service/Products/groups/Read|Termék társított fejlesztői csoportok listájának beolvasása|
|/Service/Products/groups/Write|Meglévő fejlesztői csoport társítása meglévő termék|
|/Service/Products/groups/DELETE|Meglévő termék társítás meglévő fejlesztői csoport törlése|
|/Service/openidConnectProviders/Read|Szerezze be az OpenID Connect-szolgáltatókkal vagy az OpenID Connect szolgáltató Get részleteit listát|
|/Service/openidConnectProviders/Write|Hozzon létre egy új OpenID Connect szolgáltató vagy a frissítés adatait egy meglévő OpenID Connect-szolgáltató|
|/Service/openidConnectProviders/DELETE|Távolítsa el a meglévő OpenID Connect szolgáltató|
|/Service/Certificates/Read|A tanúsítványok listájának vagy a tanúsítvány adatainak lekérése|
|/Service/Certificates/Write|Új tanúsítvány felvétele|
|/Service/Certificates/DELETE|Távolítsa el a meglévő tanúsítvány|
|/Service/Properties/Read|Beolvassa az összes tulajdonság listáját, és lekérdezi a megadott tulajdonság részleteit|
|/Service/Properties/Write|Új tulajdonság létrehozása vagy frissítése a megadott tulajdonság értéke|
|/Service/Properties/DELETE|Eltávolítja a meglévő tulajdonság|
|/Service/groups/Read|Csoportok vagy egy csoport lekérdezi részleteinek listáját|
|/Service/groups/Write|Új csoport létrehozása vagy meglévő csoport részleteinek frissítése|
|/Service/groups/DELETE|Távolítsa el a meglévő csoporthoz|
|/Service/groups/Users/Read|Csoport felhasználók listájának beolvasása|
|/Service/groups/Users/Write|Meglévő felhasználó hozzáadása meglévő csoporthoz|
|/Service/groups/Users/DELETE|Távolítsa el a meglévő felhasználói meglévő csoportból|
|/Service/authorizationServers/Read|Engedélyezési kiszolgálók listája vagy az engedélyezési kiszolgáló adatait az beszerzése|
|/Service/authorizationServers/Write|Hozzon létre egy új hitelesítési kiszolgáló vagy egy meglévő engedélyezési kiszolgáló frissítés részletei|
|/Service/authorizationServers/DELETE|Távolítsa el a meglévő engedélyezési kiszolgáló|
|/Service/Reports/bySubscription/Read|A jelentés-előfizetés összesítve beolvasása.|
|/Service/Reports/byRequest/Read|Jelentési adatok kérelmek|
|/Service/Reports/byOperation/Read|Műveletek összesítve jelentés megtekintése|
|/Service/Reports/byGeo/Read|Földrajzi régió összesítve jelentés megtekintése|
|/Service/Reports/byUser/Read|A jelentés összesíti a fejlesztők beolvasása.|
|/Service/Reports/byTime/Read|A jelentés megtekintése időszakokra összesítve|
|/Service/Reports/byApi/Read|API-k összesítve jelentés megtekintése|
|/Service/Reports/byProduct/Read|A jelentés termékek összesítve beolvasása.|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Művelet | Leírás |
|---|---|
|/ elevateAccess/művelet|Felhasználói hozzáférés rendszergazdai szerepkörének megadása a hívónak a bérlői hatókörben|
|/classicAdministrators/Read|Az előfizetés rendszergazdáinak beolvasása.|
|/ classicAdministrators/írása|Az előfizetés rendszergazdájának beállítása vagy módosítása.|
|/classicAdministrators/DELETE|Az előfizetés rendszergazdájának eltávolítása.|
|/Locks/Read|A megadott hatókör zárolásainak beolvasása.|
|/ zárolások/írása|A megadott hatókör zárolásainak beállítása.|
|/Locks/DELETE|A megadott hatókör zárolásainak törlése.|
|/policyAssignments/Read|Szabályzat-hozzárendelés adatainak lekérése.|
|/ policyAssignments/írása|Szabályzat-hozzárendelés létrehozása a megadott hatókörben.|
|/policyAssignments/DELETE|Szabályzat-hozzárendelés törlése a megadott hatókörben.|
|/permissions/Read|A hívó adott hatókörben érvényes engedélyeinek listázása.|
|/roleDefinitions/Read|Szerepkör-definíció adatainak beolvasása.|
|/ roleDefinitions/írása|Egy egyéni szerepkör-definíció létrehozása vagy módosítása a megadott engedélyekkel és hozzárendelhető hatókörökkel.|
|/roleDefinitions/DELETE|A megadott egyéni szerepkör-definíció törlése.|
|/providerOperations/Read|Az összes, szerepkör-definíciókban használható erőforrás-szolgáltató műveleteinek beolvasása.|
|/policyDefinitions/Read|Szabályzat-definíció adatainak lekérése.|
|/ policyDefinitions/írása|Egyéni szabályzat-definíció létrehozása.|
|/policyDefinitions/DELETE|Szabályzat-definíció törlése.|
|/roleAssignments/Read|Szerepkör-hozzárendelés adatainak beolvasása.|
|/ roleAssignments/írása|Szerepkör-hozzárendelés létrehozása a megadott hatókörben.|
|/roleAssignments/DELETE|Szerepkör-hozzárendelés törlése a megadott hatókörből.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Művelet | Leírás |
|---|---|
|/automationAccounts/Read|Egy Azure Automation-fiók beolvasása|
|/ automationAccounts/írása|Létrehozza vagy frissíti az Azure Automation-fiók|
|/automationAccounts/DELETE|Egy Azure Automation-fiók törlése|
|/automationAccounts/configurations/readContent/Action|Egy Azure Automation DSC tartalmának beolvasása|
|/automationAccounts/hybridRunbookWorkerGroups/Read|Beolvassa a hibrid forgatókönyv-feldolgozó erőforrások|
|/automationAccounts/hybridRunbookWorkerGroups/DELETE|Hibrid forgatókönyv-feldolgozó erőforrások törlése|
|/automationAccounts/jobSchedules/Read|Egy Azure Automation-feladatütemezés beolvasása|
|/automationAccounts/jobSchedules/Write|Egy Azure Automation-feladatütemezés létrehozása|
|/automationAccounts/jobSchedules/DELETE|Egy Azure Automation-feladatütemezés törlése|
|/automationAccounts/connectionTypes/Read|Egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz beolvasása|
|/automationAccounts/connectionTypes/Write|Egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz létrehozása|
|/automationAccounts/connectionTypes/DELETE|Egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz törlése|
|/automationAccounts/Modules/Read|Egy Azure Automation-modul beolvasása|
|/automationAccounts/Modules/Write|Létrehozza vagy frissíti az Azure Automation-modul|
|/automationAccounts/Modules/DELETE|Egy Azure Automation-modul törlése|
|/automationAccounts/credentials/Read|Egy Azure Automation szolgáltatásbeli hitelesítőadat-eszköz beolvasása|
|/automationAccounts/credentials/Write|Létrehozza vagy frissíti egy Azure Automation szolgáltatásbeli hitelesítőadat-eszköz|
|/automationAccounts/credentials/DELETE|Egy Azure Automation szolgáltatásbeli hitelesítőadat-eszköz törlése|
|/automationAccounts/Certificates/Read|Egy Azure Automation szolgáltatásbeli tanúsítványeszköz beolvasása|
|/automationAccounts/Certificates/Write|Létrehozza vagy frissíti az Azure Automation szolgáltatásbeli tanúsítványeszköz|
|/automationAccounts/Certificates/DELETE|Egy Azure Automation szolgáltatásbeli tanúsítványeszköz törlése|
|/automationAccounts/schedules/Read|Egy Azure Automation szolgáltatásbeli ütemezési eszköz beolvasása|
|/automationAccounts/schedules/Write|Létrehozza vagy frissíti az Azure Automation szolgáltatásbeli ütemezési eszköz|
|/automationAccounts/schedules/DELETE|Egy Azure Automation szolgáltatásbeli ütemezéseszköz törlése|
|/automationAccounts/Jobs/Read|Egy Azure Automation-feladat beolvasása|
|/automationAccounts/Jobs/Write|Egy Azure Automation-feladat létrehozása|
|/automationAccounts/Jobs/STOP/Action|Egy Azure Automation-feladat leállítása|
|/automationAccounts/Jobs/suspend/Action|Egy Azure Automation-feladat felfüggesztése|
|/automationAccounts/Jobs/Resume/Action|Egy Azure Automation-feladat folytatása|
|/automationAccounts/Jobs/runbookContent/Action|A feladat végrehajtásának időpontjában az Azure Automation-runbook tartalmának beolvasása|
|/automationAccounts/Jobs/output/Action|Egy feladat kimenetének beolvasása|
|/automationAccounts/Jobs/Read|Egy Azure Automation-feladat beolvasása|
|/automationAccounts/Jobs/Write|Egy Azure Automation-feladat létrehozása|
|/automationAccounts/Jobs/STOP/Action|Egy Azure Automation-feladat leállítása|
|/automationAccounts/Jobs/suspend/Action|Egy Azure Automation-feladat felfüggesztése|
|/automationAccounts/Jobs/Resume/Action|Egy Azure Automation-feladat folytatása|
|/automationAccounts/Jobs/STREAMS/Read|Egy Azure Automation-feladatstream beolvasása|
|/automationAccounts/Connections/Read|Azure Automation szolgáltatásbeli kapcsolódási eszköz beolvasása|
|/automationAccounts/Connections/Write|Létrehozza vagy frissíti az Azure Automation szolgáltatásbeli kapcsolódási eszköz|
|/automationAccounts/Connections/DELETE|Azure Automation szolgáltatásbeli kapcsolódási eszköz törlése|
|/automationAccounts/variables/Read|Egy Azure Automation szolgáltatásbeli változóeszköz beolvasása|
|/automationAccounts/variables/Write|Létrehozza vagy frissíti egy Azure Automation szolgáltatásbeli változóeszköz|
|/automationAccounts/variables/DELETE|Egy Azure Automation szolgáltatásbeli változóeszköz törlése|
|/automationAccounts/runbooks/readContent/Action|Egy Azure Automation-runbook tartalmának beolvasása|
|/automationAccounts/runbooks/Read|Egy Azure Automation-runbook beolvasása|
|/automationAccounts/runbooks/Write|Létrehozza vagy frissíti az Azure Automation-runbook|
|/automationAccounts/runbooks/DELETE|Egy Azure Automation-runbook törlése|
|/automationAccounts/runbooks/draft/readContent/Action|Egy Azure Automation-runbookvázlat tartalmának beolvasása|
|/automationAccounts/runbooks/draft/writeContent/Action|Egy Azure Automation-runbookvázlat tartalmának létrehozása|
|/automationAccounts/runbooks/draft/Read|Egy Azure Automation-runbookvázlat beolvasása|
|/automationAccounts/runbooks/draft/publish/Action|Egy Azure Automation-runbookvázlat közzététele|
|/automationAccounts/runbooks/draft/undoEdit/Action|Egy Azure Automation-runbookvázlat szerkesztésének visszavonása|
|/automationAccounts/runbooks/draft/testJob/Read|Egy Azure Automation runbookvázlat-tesztelési feladat beolvasása|
|/automationAccounts/runbooks/draft/testJob/Write|Egy Azure Automation runbookvázlat-tesztelési feladat létrehozása|
|/automationAccounts/runbooks/draft/testJob/STOP/Action|Egy Azure Automation runbookvázlat-tesztelési feladat leállítása|
|/automationAccounts/runbooks/draft/testJob/suspend/Action|Egy Azure Automation runbookvázlat-tesztelési feladat felfüggesztése|
|/automationAccounts/runbooks/draft/testJob/Resume/Action|Egy Azure Automation runbookvázlat-tesztelési feladat folytatása|
|/automationAccounts/webhooks/Read|Egy Azure Automation-webhook beolvasása|
|/automationAccounts/webhooks/Write|Létrehozza vagy frissíti az Azure Automation-webhook|
|/automationAccounts/webhooks/DELETE|Egy Azure Automation-webhook törlése |
|/automationAccounts/webhooks/generateUri/Action|URI generálása Azure Automation-webhook|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

A szolgáltató nem egy teljes ARM-szolgáltató, és nem biztosít semmilyen ARM műveletek.

## <a name="microsoftbatch"></a>Microsoft.Batch

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|A kötegelt erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi, hogy a Batch-fiókok létrehozása|
|/ batchAccounts/írása|Új Batch-fiók létrehozása vagy frissítése egy meglévő Batch-fiók|
|/batchAccounts/Read|Batch-fiókok listája, vagy a Batch-fiók tulajdonságait olvassa be|
|/batchAccounts/DELETE|Batch-fiók törlése|
|/batchAccounts/listkeys/Action|Listák hívóbetűk Batch-fiókhoz|
|/batchAccounts/regeneratekeys/Action|A Batch-fiókhoz hívóbetűk újragenerálása|
|/batchAccounts/syncAutoStorageKeys/Action|Szinkronizálja a Batch-fiók beállítása automatikus tárfiók hozzáférési kulcsainak listázása|
|/batchAccounts/Applications/Read|Megjeleníti azokat alkalmazásokat, vagy az alkalmazás tulajdonságainak beolvasása|
|/batchAccounts/Applications/Write|Létrehoz egy új alkalmazást, vagy egy meglévő alkalmazás frissítése|
|/batchAccounts/Applications/DELETE|Az alkalmazás törlése|
|/batchAccounts/Applications/versions/Read|Alkalmazáscsomag tulajdonságait olvassa be|
|/batchAccounts/Applications/versions/Write|Létrehoz egy új alkalmazás-csomagot, vagy frissíti a meglévő alkalmazáscsomag|
|/batchAccounts/Applications/versions/Activate/Action|Az alkalmazáscsomag aktiválása|
|/batchAccounts/Applications/versions/DELETE|Az alkalmazáscsomag törlése|
|/Locations/Quotas/Read|A megadott Azure-régiót kötegelt a megadott előfizetés kvótájának beolvasása|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Művelet | Leírás |
|---|---|
|/Invoices/Read|Rendelkezésre álló listák számlák|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Művelet | Leírás |
|---|---|
|/ mapApis/Olvasás|Olvasási művelet|
|/ mapApis/írása|Írási művelet|
|/ mapApis/törlése|Törlési művelet|
|/mapApis/regenerateKey/Action|A kulcs újragenerálása|
|/mapApis/listSecrets/Action|A titkos kulcsok listázása|
|/mapApis/listSingleSignOnToken/Action|Az erőforráshoz tartozó egyszeri bejelentkezési engedélyezési jogkivonat beolvasása|
|Műveletek/olvasása|A művelet leírását.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Művelet | Leírás |
|---|---|
|/ checknameavailability/művelet|Ellenőrzi, hogy a név egy új Redis Cache használható|
|/ regisztrációs/művelet|Előfizetés az "Microsoft.Cache" erőforrás-szolgáltató regisztrálása|
|/ unregister/művelet|Előfizetés az "Microsoft.Cache" erőforrás-szolgáltató regisztrációjának törlése|
|/ redis/írása|Redis gyorsítótár beállításainak és konfigurációjának a kezelési portál módosítása|
|/redis/Read|Redis gyorsítótár beállításainak és konfigurációjának megtekintése a felügyeleti portálon|
|/redis/DELETE|A teljes Redis gyorsítótár törlése|
|/redis/listKeys/Action|Redis gyorsítótár elérésikulcs-értékeinek megtekintése a felügyeleti portálon|
|/redis/regenerateKey/Action|Redis gyorsítótár elérési kulcsainak a kezelési portál értékének módosítása|
|/redis/import/Action|Meghatározott formátumú adatok importálása a Redis szolgáltatásba több blobból|
|/redis/export/Action|Redis-adatok exportálása előre megadott tárolási blobokba meghatározott formátumban|
|/redis/forceReboot/Action|Egy gyorsítótárpéldány kényszerített újraindítása, mely adatvesztést okozhat.|
|/redis/STOP/Action|Állítsa le a gyorsítótárpéldány.|
|/redis/Start/Action|Indítsa el a gyorsítótárpéldány.|
|/redis/metricDefinitions/Read|Egy Redis gyorsítótárhoz elérhető metrikai meghatározások beolvasása|
|/redis/firewallRules/Read|Az IP-tűzfalszabályok Redis gyorsítótár beolvasása|
|/redis/firewallRules/Write|Az IP-tűzfalszabályok Redis gyorsítótár szerkesztése|
|/redis/firewallRules/DELETE|Egy Redis gyorsítótárhoz IP tűzfalszabályt törlése|
|/redis/listUpgradeNotifications/Read|A gyorsítótár bérlője legutóbbi csomagváltási értesítéseinek listázása.|
|/redis/linkedservers/Read|A redis gyorsítótár társított csatolt kiszolgálók beolvasása.|
|/redis/linkedservers/Write|A csatolt kiszolgáló hozzáadása a Redis gyorsítótár|
|/redis/linkedservers/DELETE|A csatolt kiszolgáló törlése a Redis gyorsítótár|
|/redis/patchSchedules/Read|Lekérdezi a Redis Cache javítási ütemezését|
|/redis/patchSchedules/Write|Egy Redis gyorsítótár javítási ütemezésének módosítása|
|/redis/patchSchedules/DELETE|Egy Redis gyorsítótárhoz javítás ütemezését törlése|

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Művelet | Leírás |
|---|---|
|/ provisionGlobalAppServicePrincipalInUserTenant/művelet|Az alkalmazás egyszerű kiépítés egyszerű szolgáltatásnév|
|/ validateCertificateRegistrationInformation/művelet|Ellenőrizze a beszerzési tanúsítványobjektum lefolytatása nélkül|
|/ regisztrációs/művelet|Az előfizetés a Microsoft Certificates erőforrás-szolgáltató regisztrálása|
|/ certificateOrders/írása|Adja hozzá egy új certificateOrder vagy egy meglévő frissítése|
|/ certificateOrders/törlése|Egy meglévő AppServiceCertificate törlése|
|/ certificateOrders/Olvasás|A CertificateOrders listájának lekérdezése|
|/certificateOrders/reissue/Action|Hajtsa végre újból egy meglévő certificateorder|
|/certificateOrders/renew/Action|Egy meglévő certificateorder megújítása|
|/certificateOrders/retrieveCertificateActions/Action|A tanúsítvány műveletek listájának beolvasása|
|/certificateOrders/retrieveEmailHistory/Action|Tanúsítvány e-mail előzmények beolvasása|
|/certificateOrders/resendEmail/Action|Tanúsítvány e-mail újraküldése|
|/certificateOrders/verifyDomainOwnership/Action|Ellenőrizze a tartomány tulajdonosa|
|/certificateOrders/resendRequestEmails/Action|Küldje el újra a kérelmet e-mailt egy másik e-mail címet|
|/certificateOrders/resendRequestEmails/Action|App Service kiállított tanúsítvány hely lezárása beolvasása|
|/certificateOrders/Certificates/Write|Adja hozzá az új tanúsítványt, vagy egy meglévő frissítése|
|/certificateOrders/Certificates/DELETE|Meglévő tanúsítvány törlése|
|/certificateOrders/Certificates/Read|A tanúsítványok listájának lekérdezése|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|A hagyományos számítási regisztrálása|
|/ checkDomainNameAvailability/művelet|Az adott tartománynév elérhetőségének ellenőrzése.|
|/ moveSubscriptionResources/művelet|Minden hagyományos erőforrás áthelyezése egy másik előfizetést.|
|/ validateSubscriptionMoveAvailability/művelet|Ellenőrizze az előfizetés elérhetősége a klasszikus áthelyezési művelet.|
|/operatingSystemFamilies/Read|Felsorolja a Vendég operációsrendszer-család a Microsoft Azure-ban elérhető, és minden f elérhető operációsrendszer-verziókat is felsorolja
|/Capabilities/Read|A képességek megjelenítése|
|/operatingSystems/Read|A Microsoft Azure jelenleg elérhető a Vendég-operációsrendszer-verziók listája olvasható.|
|/resourceTypes/skus/Read|A támogatott erőforrástípusai Sku listájának beolvasása.|
|/domainNames/Read|Erőforrások tartománynevének visszaadása.|
|/ domainNames/írása|Hozzáadása vagy módosítása a erőforrások tartománynevének.|
|/domainNames/DELETE|Erőforrások tartománynevének eltávolítása.|
|/domainNames/swap/Action|Előkészítési pont cseréje üzemelési pontra.|
|/domainNames/serviceCertificates/Read|Az alkalmazott szolgáltatási tanúsítványok visszaadása.|
|/domainNames/serviceCertificates/Write|Hozzáadása vagy módosítása az alkalmazott szolgáltatási tanúsítványok.|
|/domainNames/serviceCertificates/DELETE|Az alkalmazott szolgáltatási tanúsítványok törlése.|
|/domainNames/serviceCertificates/operationStatuses/Read|Tartománynevek szolgáltatási tanúsítványai a tartományhoz tartozó műveleti állapotának beolvasása.|
|/domainNames/Capabilities/Read|Megjeleníti a tartományt képességek|
|/domainNames/Extensions/Read|A tartomány kiterjesztéseket adja vissza.|
|/domainNames/Extensions/Write|A tartománynév-kiterjesztések hozzáadása.|
|/domainNames/Extensions/DELETE|A tartománynév-kiterjesztések eltávolítása.|
|/domainNames/Extensions/operationStatuses/Read|A művelet állapotának a kiterjesztések beolvasása.|
|/domainNames/Active/Write|Az aktív tartománynév beállítása.|
|/domainNames/slots/Read|Az üzembe helyezési jeleníti meg.|
|/domainNames/slots/Write|Hoz, vagy az üzemelő példány frissítése.|
|/domainNames/slots/DELETE|A megadott üzembe helyezési pont törlése.|
|/domainNames/slots/Start/Action|Elindít egy üzembe helyezési tárhelyet.|
|/domainNames/slots/STOP/Action|Felfüggeszti a telepített környezet tárolóhelye.|
|/domainNames/slots/operationStatuses/Read|Tartománynevek üzembe helyezési pontjai a tartományhoz tartozó műveleti állapotának beolvasása.|
|/domainNames/slots/Roles/Read|Az üzembe helyezési pont szerepkörének beolvasása.|
|/domainNames/slots/Roles/extensionReferences/Read|Az üzembe helyezési ponti szerepkör kiterjesztéshivatkozását adja vissza.|
|/domainNames/slots/Roles/extensionReferences/Write|Hozzáadása vagy módosítása az üzembe helyezési ponti szerepkör kiterjesztéshivatkozását.|
|/domainNames/slots/Roles/extensionReferences/DELETE|Távolítsa el az üzembe helyezési ponti szerepkör kiterjesztéshivatkozását.|
|/domainNames/slots/Roles/extensionReferences/operationStatuses/Read|A tartomány nevét üzembe helyezési ponti szerepkörei kiterjesztéshivatkozásai műveleti állapotának beolvasása.|
|/domainNames/slots/Roles/roleInstances/Read|A szerepkör példánya beolvasása.|
|/domainNames/slots/Roles/roleInstances/restart/Action|Újraindítja a szerepkörpéldányt beállítani.|
|/domainNames/slots/Roles/roleInstances/reimage/Action|A szerepkör példánya reimages.|
|/domainNames/slots/Roles/roleInstances/operationStatuses/Read|A tartomány nevét üzembe helyezési ponti szerepkörei szerepkörpéldányai műveleti állapotának beolvasása.|
|/domainNames/slots/State/Start/Write|Módosítja az üzembe helyezési pont állapotának leállt.|
|/domainNames/slots/State/STOP/Write|Módosítja az üzembe helyezési pont állapotának elindult.|
|/domainNames/slots/upgradeDomain/Write|Bejárása a tartomány frissítéséhez.|
|/domainNames/internalLoadBalancers/Read|A belső terheléselosztók beolvasása.|
|/domainNames/internalLoadBalancers/Write|Létrehoz egy új belső terheléselosztás.|
|/domainNames/internalLoadBalancers/DELETE|Új belső terheléselosztás eltávolítása.|
|/domainNames/internalLoadBalancers/operationStatuses/Read|A tartománynevek belső terheléselosztóinak műveleti állapotának beolvasása.|
|/domainNames/loadBalancedEndpointSets/Read|Megjeleníti az elosztott terhelésű végpontcsoportok megjelenítése|
|/domainNames/loadBalancedEndpointSets/operationStatuses/Read|A tartománynevek elosztott terhelésű végpontcsoportjai műveleti állapotának beolvasása.|
|/domainNames/availabilitySets/Read|A rendelkezésre állási csoportot az erőforrás megjelenítése.|
|/Quotas/Read|Az előfizetés kvótájának beolvasása.|
|/virtualMachines/Read|Virtuális gépek listájának beolvasása.|
|/ virtuális gépek vannak/írása|Adja hozzá, vagy módosítsa a virtuális gépek.|
|/virtualMachines/DELETE|Eltávolítja a virtuális gépek.|
|/virtualMachines/Start/Action|A virtuális gép elindításához.|
|/virtualMachines/redeploy/Action|A virtuális gép redeploys.|
|/virtualMachines/restart/Action|Újraindítja a virtuális gépek.|
|/virtualMachines/STOP/Action|A virtuális gép leáll.|
|/virtualMachines/shutdown/Action|Állítsa le a virtuális gépet.|
|/virtualMachines/attachDisk/Action|Adatlemez egy virtuális géphez.|
|/virtualMachines/detachDisk/Action|A virtuális gépről adatlemez.|
|/virtualMachines/downloadRemoteDesktopConnectionFile/Action|A virtuális gép RDP-fájljának letöltése.|
|/virtualMachines/hálózati illesztők /<br>associatedNetworkSecurityGroups olvasása|A hálózati interfészhez társított hálózati biztonsági csoport lekérése.|
|/virtualMachines/hálózati illesztők /<br>associatedNetworkSecurityGroups írása|A hálózati interfészhez társított hálózati biztonsági csoport hozzáadása.|
|/virtualMachines/hálózati illesztők /<br>associatedNetworkSecurityGroups vagy törlése|A hálózati interfészhez társított hálózati biztonsági csoport törlése.|
|/virtualMachines/hálózati illesztők /<br>olvasási idő associatedNetworkSecurityGroups/operationStatuses|A virtuális gépek műveleti állapotának társított hálózati biztonsági csoportok beolvasása.|
|/virtualMachines/Providers/Microsoft.Insights/metricDefinitions/Read|Lekérdezi a metrikák definíciókat.|
|/virtualMachines/Providers/Microsoft.Insights/diagnosticSettings/Read|A diagnosztikai beállításainak beolvasása.|
|/virtualMachines/Providers/Microsoft.Insights/diagnosticSettings/Write|Hozzáadása vagy módosítása a diagnosztikai beállításokat.|
|/virtualMachines/Metrics/Read|A metrikák beolvasása.|
|/virtualMachines/operationStatuses/Read|A virtuális gépek műveleti állapotának beolvasása.|
|/virtualMachines/Extensions/Read|A virtuálisgép-bővítmény beolvasása.|
|/virtualMachines/Extensions/Write|A virtuálisgép-bővítmény helyezi.|
|/virtualMachines/Extensions/operationStatuses/Read|A virtuálisgép-bővítmények műveleti állapotának beolvasása.|
|/virtualMachines/asyncOperations/Read|Lehetséges aszinkron műveletek beolvasása|
|/virtualMachines/Disks/Read|Az adatlemezek adatlemezek listájának beolvasása|
|/virtualMachines/associatedNetworkSecurityGroups/Read|A virtuális géphez társított hálózati biztonsági csoport lekérése.|
|/virtualMachines/associatedNetworkSecurityGroups/Write|A virtuális géphez társított hálózati biztonsági csoport hozzáadása.|
|/virtualMachines/associatedNetworkSecurityGroups/DELETE|A virtuális géphez társított hálózati biztonsági csoport törlése.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/Read|A virtuális gépek műveleti állapotának társított hálózati biztonsági csoportok beolvasása.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|Klasszikus hálózathoz regisztrálása|
|/gatewaySupportedDevices/Read|Támogatott eszközök listájának beolvasása.|
|/reservedIps/Read|A foglalt IP-cím beolvasása|
|/ Keskenyeknek/írása|Új fenntartott IP-cím hozzáadása|
|/reservedIps/DELETE|A foglalt IP-cím törlése.|
|/reservedIps/Link/Action|Hivatkozásra egy fenntartott IP-cím|
|/reservedIps/JOIN/Action|Csatlakozás egy fenntartott IP-cím|
|/reservedIps/operationStatuses/Read|A fenntartott IP-címek műveleti állapotának beolvasása.|
|/virtualNetworks/Read|A virtuális hálózati beolvasása.|
|/ virtualNetworks/írása|Adjon hozzá egy új virtuális hálózat.|
|/virtualNetworks/DELETE|Törli a virtuális hálózat.|
|/virtualNetworks/Peer/Action|A virtuális hálózat egy másik virtuális hálózathoz állomásokhoz.|
|/virtualNetworks/JOIN/Action|A virtuális hálózathoz csatlakozik.|
|/virtualNetworks/checkIPAddressAvailability/Action|Egy adott IP-cím virtuális hálózaton rendelkezésre állását ellenőrzi.|
|/virtualNetworks/Capabilities/Read|A képességek megjelenítése|
|/virtualNetworks/alhálózatok /<br>associatedNetworkSecurityGroups olvasása|Az alhálózathoz társított hálózati biztonsági csoport lekérése.|
|/virtualNetworks/alhálózatok /<br>associatedNetworkSecurityGroups írása|Az alhálózathoz társított hálózati biztonsági csoport hozzáadása.|
|/virtualNetworks/alhálózatok /<br>associatedNetworkSecurityGroups vagy törlése|Az alhálózathoz társított hálózati biztonsági csoport törlése.|
|/virtualNetworks/alhálózatok /<br>olvasási idő associatedNetworkSecurityGroups/operationStatuses|Az a virtuális hálózat alhálózatához társított hálózati biztonsági csoport műveleti állapotának beolvasása.|
|/virtualNetworks/operationStatuses/Read|A virtuális hálózatok műveleti állapotának beolvasása.|
|/virtualNetworks/Gateways/Read|A virtuális hálózati átjárók beolvasása.|
|/virtualNetworks/Gateways/Write|A virtuális hálózati átjáró hozzáadása.|
|/virtualNetworks/Gateways/DELETE|A virtuális hálózati átjáró törlése.|
|/virtualNetworks/Gateways/startDiagnostics/Action|A virtuális hálózati átjáró diagnosztikájának indítása.|
|/virtualNetworks/Gateways/stopDiagnostics/Action|A virtuális hálózati átjáró diagnosztikájának leállítása.|
|/virtualNetworks/Gateways/downloadDiagnostics/Action|Az átjáró diagnostics tölti le.|
|/virtualNetworks/Gateways/listCircuitServiceKey/Action|Kör szolgáltatáskulcsának beolvasása.|
|/virtualNetworks/Gateways/downloadDeviceConfigurationScript/Action|Letölti az eszközkonfigurációs parancsprogram.|
|/virtualNetworks/Gateways/listPackage/Action|A virtuális hálózati átjárócsomag sorolja fel.|
|/virtualNetworks/Gateways/operationStatuses/Read|A virtuális hálózati átjárók műveleti állapotának beolvasása.|
|/virtualNetworks/Gateways/Packages/Read|A virtuális hálózati átjárócsomag beolvasása.|
|/virtualNetworks/Gateways/Connections/Read|Kapcsolatok listájának beolvasása.|
|/virtualNetworks/Gateways/Connections/Connect/Action|A webhelyek közötti átjárókapcsolat csatlakozik.|
|/virtualNetworks/Gateways/Connections/disconnect/Action|A webhelyek közötti átjárókapcsolat leválasztása.|
|/virtualNetworks/Gateways/Connections/test/Action|A webhelyek közötti átjárókapcsolat tesztelése.|
|/virtualNetworks/Gateways/clientRevokedCertificates/Read|Visszavont ügyféltanúsítványok olvasni.|
|/virtualNetworks/Gateways/clientRevokedCertificates/Write|Ügyfél-tanúsítvány visszavonása.|
|/virtualNetworks/Gateways/clientRevokedCertificates/DELETE|Unrevokes ügyféltanúsítványt.|
|/virtualNetworks/Gateways/clientRootCertificates/Read|Az ügyfél legfelső szintű tanúsítványok keresése.|
|/virtualNetworks/Gateways/clientRootCertificates/Write|Egy ügyfél új főtanúsítványának feltöltése.|
|/virtualNetworks/Gateways/clientRootCertificates/DELETE|A virtuális hálózati átjáró ügyféltanúsítványának törlése.|
|/virtualNetworks/Gateways/clientRootCertificates/download/Action|Letöltést tanúsítvány ujjlenyomata.|
|/virtualNetworks/Gateways/clientRootCertificates/listPackage/Action|A virtuális hálózati átjárócsomag tanúsítvány sorolja fel.|
|/networkSecurityGroups/Read|A hálózati biztonsági csoport lekérése.|
|/ biztonsági csoportok/írása|Új hálózati biztonsági csoport hozzáadása.|
|/networkSecurityGroups/DELETE|A hálózati biztonsági csoport törlése.|
|/networkSecurityGroups/operationStatuses/Read|A hálózati biztonsági csoport műveleti állapotának beolvasása.|
|/networkSecurityGroups/securityRules/Read|A szabály lekérdezi.|
|/networkSecurityGroups/securityRules/Write|Hozzáadása vagy egy biztonsági szabály frissítése.|
|/networkSecurityGroups/securityRules/DELETE|A biztonsági szabály törlése.|
|/networkSecurityGroups/securityRules/operationStatuses/Read|A hálózati biztonsági csoport biztonsági szabályai műveleti állapotának beolvasása.|
|/Quotas/Read|Az előfizetés kvótájának beolvasása.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|A hagyományos tárolási regisztrálása|
|/ checkStorageAccountAvailability/művelet|A tárfiók elérhetőségének ellenőrzése.|
|/Capabilities/Read|A képességek megjelenítése|
|/publicImages/Read|Lekérdezi a nyilvános virtuálisgép-lemezkép.|
|/Images/Read|A kép adja vissza.|
|/storageAccounts/Read|A megadott fiók tárfiók visszaadása.|
|/ storageAccounts/írása|Hozzáad egy új tárfiókot.|
|/storageAccounts/DELETE|A tárfiók törlése.|
|/storageAccounts/listKeys/Action|A tárfiókok elérési kulcsainak listázása.|
|/storageAccounts/regenerateKey/Action|A tárfiók meglévő elérési kulcsainak újragenerálása.|
|/storageAccounts/operationStatuses/Read|Az erőforrás műveleti állapotának beolvasása.|
|/storageAccounts/Images/Read|A tárfióklemezkép beolvasása.|
|/storageAccounts/Images/DELETE|A megadott tárfióklemezkép törlése.|
|/storageAccounts/Disks/Read|A tárolási fiók lemez adja vissza.|
|/storageAccounts/Disks/Write|A tárolási fiók lemez hozzáadása.|
|/storageAccounts/Disks/DELETE|A megadott fiók tárolólemez törli.|
|/storageAccounts/Disks/operationStatuses/Read|Az erőforrás műveleti állapotának beolvasása.|
|/storageAccounts/osImages/Read|A tárfiók operációsrendszer-lemezképének visszaadása.|
|/storageAccounts/osImages/DELETE|A megadott tárfiók operációsrendszer-lemezképének törlése.|
|/storageAccounts/Services/Read|Az elérhető szolgáltatások beolvasása.|
|/storageAccounts/Services/metricDefinitions/Read|Lekérdezi a metrikák definíciókat.|
|/storageAccounts/Services/Metrics/Read|A metrikák beolvasása.|
|/storageAccounts/Services/diagnosticSettings/Read|A diagnosztikai beállításainak beolvasása.|
|/storageAccounts/Services/diagnosticSettings/Write|Hozzáadása vagy módosítása a diagnosztikai beállításokat.|
|/Disks/Read|A tárolási fiók lemez adja vissza.|
|/osImages/Read|Az operációs rendszer lemezképének visszaadása.|
|/Quotas/Read|Az előfizetés kvótájának beolvasása.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Művelet | Leírás |
|---|---|
|/accounts/Read|API-fiókok beolvasása.|
|/ fiókok/írása|API-fiókok írása.|
|/accounts/DELETE|API-fiókok törlése|
|/accounts/listKeys/Action|Kulcsok listázása|
|/accounts/regenerateKey/Action|Kulcs újragenerálása|
|/accounts/skus/Read|Az egy adott meglévő erőforráshoz rendelkezésre álló termékváltozatok beolvasása.|
|/accounts/usages/Read|Egy meglévő erőforrás kvótahasználatának beolvasása.|
|Műveletek/olvasása|A művelet leírását.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Művelet | Leírás |
|---|---|
|RateCard/olvasása|Értéket ad vissza adatokat, Erőforrás-kijelző metaadatok és az adott előfizetéshez tartozó díjszabás kínálnak.|
|UsageAggregates/olvasása|Lekéri a Microsoft Azure használat előfizetés. Az eredmény összesítések tartalmaz a kapcsolódó információk, egy adott időtartományt a használati adatok, az előfizetés és az erőforrás.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|Az előfizetés regisztrálása a Microsoft.Compute erőforrás-szolgáltatónál|
|/restorePointCollections/Read|A visszaállítási pont gyűjtemény tulajdonságainak beolvasása|
|/ restorePointCollections/írása|Létrehoz egy új helyreállítási pont gyűjteményt vagy frissít egy meglévő|
|/restorePointCollections/DELETE|Törli a visszaállítási pont a gyűjteményhez, és tárolt visszaállítási pontok|
|/restorePointCollections/restorePoints/Read|Visszaállítási pont tulajdonságainak beolvasása|
|/restorePointCollections/restorePoints/Write|Új helyreállítási pont létrehozása|
|/restorePointCollections/restorePoints/DELETE|Törli a visszaállítási pont|
|/restorePointCollections/restorePoints/retrieveSasUris/Action|Egy blob SAS URI-k és-visszaállítási pontot tulajdonságainak beolvasása|
|/virtualMachineScaleSets/Read|Egy virtuálisgép-méretezési csoport tulajdonságainak lekérdezése|
|/ virtualMachineScaleSets/írása|Létrehoz egy új vagy frissít egy meglévő virtuálisgép-méretezési csoportot|
|/virtualMachineScaleSets/DELETE|Törli a virtuálisgép-méretezési csoportot|
|/virtualMachineScaleSets/Start/Action|Elindítja a virtuálisgép-méretezési csoport példányait|
|/virtualMachineScaleSets/powerOff/Action|Kikapcsolja a virtuálisgép-méretezési csoport példányait|
|/virtualMachineScaleSets/restart/Action|Újraindítja a virtuálisgép-méretezési csoport példányait|
|/virtualMachineScaleSets/deallocate/Action|Kikapcsolja és felszabadítja a virtuálisgép-méretezési csoport példányaihoz tartozó számítási erőforrásokat |
|/virtualMachineScaleSets/manualUpgrade/Action|A virtuálisgép-méretezési csoport példányai verziójának manuális váltása a legfrissebb verzióra|
|/virtualMachineScaleSets/Scale/Action|A méretezési / horizontális Felskálázás példányszámot, egy meglévő virtuálisgép-méretezési beállítása|
|/virtualMachineScaleSets/instanceView/Read|Beolvassa a virtuálisgép-méretezési csoport példányait tartalmazó nézetet|
|/virtualMachineScaleSets/skus/Read|Egy meglévő virtuálisgép-méretezési csoport érvényes termékváltozatait listázza|
|/virtualMachineScaleSets/virtualMachines/Read|Egy virtuálisgép-méretezési csoport egy virtuális gépének tulajdonságait adja meg|
|/virtualMachineScaleSets/virtualMachines/DELETE|Töröl egy virtuális gépet egy virtuálisgép-méretezési csoportban.|
|/virtualMachineScaleSets/virtualMachines/Start/Action|Elindít egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példányt.|
|/virtualMachineScaleSets/virtualMachines/powerOff/Action|Kikapcsol egy virtuálisgép-példányt egy virtuálisgép-méretezési csoportban.|
|/virtualMachineScaleSets/virtualMachines/restart/Action|Újraindít egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példányt.|
|/virtualMachineScaleSets/virtualMachines/deallocate/Action|Kikapcsol egy virtuális gépet egy virtuálisgép-méretezési csoportban, és felszabadítja az ahhoz tartozó számítási erőforrásokat.|
|/virtualMachineScaleSets/virtualMachines/instanceView/Read|Egy virtuálisgép-méretezési csoport egy virtuális gépének példányait tartalmazó nézetet adja meg.|
|/Images/Read|A lemezkép tulajdonságainak beolvasása|
|/ képek/írása|Lemezképet hoz létre új vagy frissít egy meglévő|
|/Images/DELETE|A rendszerkép törlése|
|/Operations/Read|A Microsoft.Compute erőforrás-szolgáltató elérhető műveleteinek listázása|
|/Disks/Read|A lemez tulajdonságainak beolvasása|
|/ lemezek írása|Új lemez létrehozása vagy meglévő lemez frissítése|
|/Disks/DELETE|A lemez törlése|
|/Disks/beginGetAccess/Action|Lemez SAS URI-jának beolvasása blobhozzáféréshez|
|/Disks/endGetAccess/Action|A lemez SAS URI-jának visszavonása|
|/snapshots/Read|Pillanatkép tulajdonságainak beolvasása|
|/ pillanatképek/írása|Új pillanatkép létrehozása vagy meglévő pillanatkép frissítése|
|/snapshots/DELETE|A pillanatkép törlése|
|/availabilitySets/Read|Egy adott rendelkezésre állási csoport tulajdonságainak beolvasása|
|/ availabilitySets/írása|Létrehoz egy új vagy frissít egy meglévő rendelkezésre állási csoportot|
|/availabilitySets/DELETE|Törli a rendelkezésre állási csoportot|
|/availabilitySets/vmSizes/Read|A virtuális gépek a rendelkezésre állási csoportban való létrehozásához vagy frissítéséhez használható méretek listázása|
|/virtualMachines/Read|Egy virtuális gép tulajdonságait olvassa be|
|/ virtuális gépek vannak/írása|Létrehoz egy új virtuális gépet vagy frissít egy meglévő virtuális gépet|
|/virtualMachines/DELETE|Törli a virtuális gépet|
|/virtualMachines/Start/Action|Elindítja a virtuális gépet|
|/virtualMachines/powerOff/Action|Kikapcsolja a virtuális gép. Vegye figyelembe, hogy a virtuális gép továbbra is fizetnie kell ezért.|
|/virtualMachines/redeploy/Action|A virtuális gép újbóli üzembe helyezése|
|/virtualMachines/restart/Action|Újraindítja a virtuális gépet|
|/virtualMachines/deallocate/Action|Kikapcsolja a virtuális gépet és felszabadítja a számítási erőforrásokat|
|/virtualMachines/generalize/Action|Általánosítottra állítja a virtuális gép állapotát és előkészíti a virtuális gépet a rögzítéshez|
|/virtualMachines/Capture/Action|Rögzíti a virtuális gépet úgy, hogy másolatot készít a virtuális merevlemezekről és létrehoz egy sablont, mellyel hasonló virtuális gépek hozhatók létre|
|/virtualMachines/convertToManagedDisks/Action|A blobalapú lemezek, a virtuális gép átalakítása felügyelt lemezek|
|/virtualMachines/vmSizes/Read|Listázza az elérhető méreteket, melyekre a virtuális gép frissíthető|
|/virtualMachines/instanceView/Read|A virtuális gép és az erőforrásai részletes futási állapotát olvassa be|
|/virtualMachines/Extensions/Read|Egy virtuálisgép-bővítmény tulajdonságait olvassa be|
|/virtualMachines/Extensions/Write|Létrehoz egy új vagy frissít egy meglévő virtuálisgép-bővítményt|
|/virtualMachines/Extensions/DELETE|Törli a virtuálisgép-bővítményt|
|/Locations/vmSizes/Read|Listázza az adott helyen elérhető virtuálisgép-méreteket|
|/Locations/usages/Read|Az előfizetés adott helyen lévő számítási erőforrásaihoz tartozó szolgáltatások korlátait és az aktuális felhasználási mennyiségeket olvassa be|
|/Locations/Operations/Read|Egy aszinkron művelet állapotát olvassa be|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|A tároló beállításjegyzék erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a tároló nyilvántartó létrehozását.|
|/checknameavailability/Read|Ellenőrzi, ugyanez a neve érvényes, és nincs használatban.|
|/registries/Read|A tároló nyilvántartó listát ad vissza, vagy a megadott tároló beállításjegyzék tulajdonságainak beolvasása.|
|/ nyilvántartó/írása|Egy tároló beállításjegyzék létrehozása a megadott paraméterekkel, vagy tulajdonságainak vagy a megadott tároló beállításjegyzék címkék frissítése.|
|/registries/DELETE|Egy meglévő tároló beállításjegyzék törli.|
|/registries/listCredentials/Action|Megjeleníti a megadott tároló beállításjegyzék bejelentkezési hitelesítő adataihoz.|
|/registries/regenerateCredential/Action|A bejelentkezési hitelesítő adatait a megadott tároló beállításjegyzék újragenerálása.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Művelet | Leírás |
|---|---|
|/containerServices/Subscriptions/Read|A megadott tárolószolgáltatások beolvasása előfizetés alapján|
|/containerServices/resourceGroups/Read|A megadott tárolószolgáltatások beolvasása erőforráscsoport alapján|
|/containerServices/resourceGroups/ContainerServiceName/Read|A megadott tárolószolgáltatás beolvasása|
|/containerServices/resourceGroups/ContainerServiceName/Write|A megadott tárolószolgáltatás tárolása vagy frissítése|
|/containerServices/resourceGroups/ContainerServiceName/DELETE|A megadott tárolószolgáltatás törlése|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Művelet | Leírás |
|---|---|
|/ updateCommunicationPreference/művelet|Update kommunikációs beállítás|
|/ listCommunicationPreference/művelet|Lista kommunikációs beállításokat|
|/Applications/Read|Olvasási művelet|
|/ applications/írása|Írási művelet|
|/ applications/írása|Írási művelet|
|/Applications/DELETE|Törlési művelet|
|/Applications/listSecrets/Action|Titkos kulcsok listázása|
|/Applications/listSingleSignOnToken/Action|Olvassa el a jogkivonatok az egyszeri bejelentkezés|
|/Operations/Read|olvasási műveletek|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Művelet | Leírás |
|---|---|
|/hubs/Read|Bármely Azure felhasználói Insights Hub olvasása|
|/ hubok/írása|Hozzon létre vagy bármely Azure felhasználói Insights központ frissítése|
|/hubs/DELETE|Bármely Azure felhasználói Insights Hub törlése|
|/hubs/Providers/Microsoft.Insights/metricDefinitions/Read|Az erőforrás elérhető metrikai meghatározások beolvasása|
|/hubs/Providers/Microsoft.Insights/diagnosticSettings/Read|Az erőforrás diagnosztikai beállításának beolvasása.|
|/hubs/Providers/Microsoft.Insights/diagnosticSettings/Write|Létrehozza vagy frissíti az erőforrás diagnosztikai beállításának|
|/hubs/Providers/Microsoft.Insights/logDefinitions/Read|Naplók erőforrás beolvasása|
|/hubs/authorizationPolicies/Read|Azure felhasználói elemzéseket megosztott hozzáférési aláírást házirend olvasása|
|/hubs/authorizationPolicies/Write|Hozzon létre vagy bármely Azure felhasználói Insights megosztott hozzáférési aláírást házirend frissítése|
|/hubs/authorizationPolicies/DELETE|Bármely Azure felhasználói Insights megosztott hozzáférési aláírást házirend törlése|
|/hubs/authorizationPolicies/regeneratePrimaryKey/Action|Azure felhasználói Insights megosztott hozzáférési aláírást házirend elsődleges kulcs újragenerálása|
|/hubs/authorizationPolicies/regenerateSecondaryKey/Action|Azure felhasználói Insights megosztott hozzáférési aláírást házirend másodlagos kulcs újragenerálása|
|/hubs/Profiles/Read|Bármely Azure felhasználói Insights profil olvasása|
|/hubs/Profiles/Write|Minden Azure felhasználói Insights profil írása|
|/hubs/KPI/read|Bármely Azure felhasználói Insights fő teljesítménymutató olvasása|
|/hubs/KPI/Write|Létrehozása vagy frissítése bármely Azure felhasználói Insights fő teljesítménymutató|
|/hubs/KPI/DELETE|Bármely Azure felhasználói Insights fő teljesítménymutató törlése|
|/hubs/Views/Read|Olvassa el a bármely Azure felhasználói Insights alkalmazás megtekintése|
|/hubs/Views/Write|Hozzon létre vagy bármely Azure felhasználói Insights alkalmazás nézet frissítése|
|/hubs/Views/DELETE|Bármely Azure felhasználói Insights alkalmazás nézet törlése|
|/hubs/interactions/Read|Olvassa el az Azure felhasználói Insights beavatkozás|
|/hubs/interactions/Write|Létrehozása vagy frissítése Insights Azure felhasználói beavatkozás|
|/hubs/roleAssignments/Read|Olvassa el a bármely Azure felhasználói Insights Rbac-hozzárendelés|
|/hubs/roleAssignments/Write|Hozzon létre, vagy bármely Azure felhasználói Insights Rbac-hozzárendelés módosítása|
|/hubs/roleAssignments/DELETE|Bármely Azure felhasználói Insights Rbac-hozzárendelés törlése|
|/hubs/Connectors/Read|Olvassa el a bármely Azure felhasználói Insights-összekötő|
|/hubs/Connectors/Write|Létrehozása vagy frissítése bármely Azure felhasználói Insights-összekötő|
|/hubs/Connectors/DELETE|Bármely Azure felhasználói Insights-összekötő törlése|
|/hubs/Connectors/Mappings/Read|Olvassa el a bármely Azure felhasználói Insights összekötő leképezése|
|/hubs/Connectors/Mappings/Write|Hozzon létre vagy frissítési bármely Azure felhasználói Insights összekötő leképezés|
|/hubs/Connectors/Mappings/DELETE|Bármely Azure felhasználói Insights összekötő leképezés törlése|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Művelet | Leírás |
|---|---|
|/ checkNameAvailability/művelet|Bérlő katalógus neve rendelkezésre állását ellenőrzi.|
|/catalogs/Read|Tulajdonságok beolvasása katalógus vagy a katalógusban az előfizetéshez vagy erőforráscsoporthoz.|
|/ katalógusok/írása|Katalógus létrehozza vagy frissíti, a címkéket és a katalógus tulajdonságait.|
|/catalogs/DELETE|Törli a katalógusban.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Művelet | Leírás |
|---|---|
|/datafactories/Read|Olvassa be az adat-Előállítóban.|
|/ datafactories/írása|Hozzon létre vagy adat-előállító frissítése|
|/datafactories/DELETE|Törli az adat-Előállítóban.|
|/datafactories/datapipelines/Read|Olvassa be a folyamatot.|
|/datafactories/datapipelines/DELETE|Feldolgozási sor törlése.|
|/datafactories/datapipelines/pause/Action|Adatcsatorna szünetel.|
|/datafactories/datapipelines/Resume/Action|Adatcsatorna folytatása.|
|/datafactories/datapipelines/Update/Action|Frissítések folyamat.|
|/datafactories/datapipelines/Write|Létrehozni vagy frissíteni a feldolgozási sor|
|/datafactories/linkedServices/Read|Beolvassa a társított szolgáltatás.|
|/datafactories/linkedServices/DELETE|Törli a társított szolgáltatás.|
|/datafactories/linkedServices/Write|Hozzon létre vagy frissítés társított szolgáltatás|
|/datafactories/Tables/Read|Beolvassa a táblában.|
|/datafactories/Tables/DELETE|Tábla törlése.|
|/datafactories/Tables/Write|Hozható létre vagy frissíthető tábla|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Művelet | Leírás |
|---|---|
|/accounts/Read|A DataLakeAnalytics fiók adatainak beolvasása.|
|/ fiókok/írása|Fiók létrehozása vagy frissítése a DataLakeAnalytics.|
|/accounts/DELETE|A DataLakeAnalytics fiók törlése.|
|/accounts/firewallRules/Read|Egy tűzfalszabály adatainak beolvasása.|
|/accounts/firewallRules/Write|Hozzon létre, vagy egy tűzfalszabály módosítása.|
|/accounts/firewallRules/DELETE|Tűzfalszabály törlése.|
|/accounts/storageAccounts/Read|Csatolt tárfiók lekérni a DataLakeAnalytics fiókhoz.|
|/accounts/storageAccounts/Write|A Storage-fiók összekötése a DataLakeAnalytics fiók.|
|/accounts/storageAccounts/DELETE|Választható a tárfiók DataLakeAnalytics-fiókból.|
|/accounts/storageAccounts/containers/Read|A tárfiók a tárolók beolvasása|
|/accounts/storageAccounts/containers/listSasTokens/Action|A tároló SAS-tokenje listája|
|/accounts/dataLakeStoreAccounts/Read|Hozzon létre csatolt DataLakeStore-fiókot a DataLakeAnalytics fiók.|
|/accounts/dataLakeStoreAccounts/Write|Egy DataLakeStore-fiók összekötése a DataLakeAnalytics fiók.|
|/accounts/dataLakeStoreAccounts/DELETE|Megszünteti egy DataLakeStore fiók DataLakeAnalytics-fiókból.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Művelet | Leírás |
|---|---|
|/accounts/Read|Egy már DataLakeStore fiók adatainak beolvasása.|
|/ fiókok/írása|Hozzon létre egy új DataLakeStore fiókot, vagy egy már DataLakeStore fiók frissítéséhez.|
|/accounts/DELETE|Már DataLakeStore-fiók törlése.|
|/accounts/firewallRules/Read|Egy tűzfalszabály adatainak beolvasása.|
|/accounts/firewallRules/Write|Hozzon létre, vagy egy tűzfalszabály módosítása.|
|/accounts/firewallRules/DELETE|Tűzfalszabály törlése.|
|/accounts/trustedIdProviders/Read|Egy megbízható identitásszolgáltató adatainak beolvasása.|
|/accounts/trustedIdProviders/Write|Hozzon létre, vagy egy megbízható identitásszolgáltató frissítése.|
|/accounts/trustedIdProviders/DELETE|Egy megbízható identitásszolgáltató törlése.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|Az IOT hubbal erőforrás-szolgáltató előfizetésének regisztrálása és létrehozását lehetővé tevő erőforrások IOT hubbal|
|/ checkNameAvailability/művelet|Ellenőrizze a rendelkezésre álló Ha IOT hubbal név|
|/ módjait/Olvasás|Ezzel előfizetés használati adatokat a szolgáltató.|
|/ műveletek/Olvasás|Minden ResourceProvider műveletek beolvasása|
|/ iotHubs/Olvasás|Lekérdezi az IOT hubbal (oka) t|
|/ iotHubs/írása|Létrehozni vagy frissíteni az IOT hubbal erőforrás|
|/ iotHubs/törlése|IOT hubbal erőforrás törlése|
|/iotHubs/listkeys/Action|Minden IOT hubbal kulcs beszerzése|
|/iotHubs/exportDevices/Action|Eszközök exportálása|
|/iotHubs/importDevices/Action|Eszközök importálása|
|IotHubs/metricDefinitions/olvasása|Az IOT hubbal szolgáltatáshoz elérhető metrikai meghatározások beolvasása|
|/iotHubs/iotHubKeys/listkeys/Action|A megadott névvel az IOT hubbal kulcs beszerzése|
|/iotHubs/iotHubStats/Read|Megtekintheti a statisztikákat IOT hubbal|
|/iotHubs/quotaMetrics/Read|Kvóta metrikák beolvasása|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Az EventHub felhasználói csoport létrehozása|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Az EventHub fogyasztói csoportot beolvasása|
|/iotHubs/eventHubEndpoints/consumerGroups/DELETE|Az EventHub felhasználói csoport törlése|
|/iotHubs/Routing/routes/$ testall parancsot/művelet|A tesztüzenet szemben az összes meglévő útvonal|
|/iotHubs/Routing/routes/$ testnew/művelet|A tesztüzenet szemben a megadott útvonal tesztelése|
|IotHubs/diagnosticSettings/olvasása|Az erőforrás diagnosztikai beállításának beolvasása.|
|/ IotHubs/diagnosticSettings/írása|Létrehozza vagy frissíti az erőforrás diagnosztikai beállításának|
|/iotHubs/skus/Read|Érvényes IOT hubbal termékváltozatok beolvasása|
|/iotHubs/Jobs/Read|Ezzel a feladat a megadott IOT hubbal beküldött adatokat|
|/iotHubs/routingEndpointsHealth/Read|Az IOT hubbal az összes útválasztási végpontok állapotának beolvasása|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Művelet | Leírás |
|---|---|
|/ Előfizetés/regisztrációs/művelet|Az előfizetés regisztrálása|
|/Labs/DELETE|Labs törlése.|
|/Labs/Read|Olvassa el a labs.|
|/ labs/írása|Hozzáadása vagy módosítása labs.|
|/Labs/ListVhds/Action|Listázza az elérhető egyéni lemezkép létrehozásához szükséges lemezképeket.|
|/Labs/GenerateUploadUri/Action|URI generálása az egyéni lemezképek feltöltése a labor.|
|/Labs/CreateEnvironment/Action|Virtuális gépek létrehozása egy tesztkörnyezetben.|
|/Labs/ClaimAnyVm/Action|Jogcím egy véletlenszerű claimable virtuális gép a tesztkörnyezetben.|
|/Labs/ExportResourceUsage/Action|A tesztlabor-erőforrások használata exportálja a storage-fiók|
|/Labs/Users/DELETE|Törölje a felhasználói profilokat.|
|/Labs/Users/Read|Olvassa a felhasználói profilokat.|
|/Labs/Users/Write|Hozzáadása vagy módosítása a felhasználói profilok.|
|/Labs/Users/secrets/DELETE|Törölje a titkos kulcsok.|
|/Labs/Users/secrets/Read|Olvassa el a titkos kulcsok.|
|/Labs/Users/secrets/Write|Hozzáadása vagy módosítása a titkos kulcsok.|
|/Labs/Users/Environments/DELETE|Törölje a környezetben.|
|/Labs/Users/Environments/Read|Olvassa el a környezetben.|
|/Labs/Users/Environments/Write|Hozzáadása vagy módosítása a környezetben.|
|/Labs/Users/Disks/DELETE|Törölje a lemezt.|
|/Labs/Users/Disks/Read|Lemezek olvasása.|
|/Labs/Users/Disks/Write|Hozzáadása vagy módosítása a lemezeket.|
|/Labs/Users/Disks/Attach/Action|Csatolja, és a címbérlet, a lemez a virtuális gép létrehozása.|
|/Labs/Users/Disks/Detach/Action|Válassza le, és a címbérlet, a lemez a virtuális géphez csatlakoztatott break.|
|/Labs/customImages/DELETE|Törölje az egyéni lemezképet.|
|/Labs/customImages/Read|Olvassa el az egyéni lemezképek.|
|/Labs/customImages/Write|Hozzáadása vagy módosítása az egyéni lemezképek.|
|/Labs/serviceRunners/DELETE|Szolgáltatás indák törlése.|
|/Labs/serviceRunners/Read|Olvasási szolgáltatás indák.|
|/Labs/serviceRunners/Write|Adja hozzá, vagy módosítsa a szolgáltatás indák.|
|/Labs/artifactSources/DELETE|Összetevő források törlése.|
|/Labs/artifactSources/Read|Olvassa el a összetevő források.|
|/Labs/artifactSources/Write|Hozzáadása vagy módosítása összetevő források.|
|/Labs/artifactSources/artifacts/Read|Olvassa el az összetevők.|
|/Labs/artifactSources/artifacts/GenerateArmTemplate/Action|Állít elő, az adott összetevő egy ARM-sablon, feltölti a szükséges fájlok egy tárfiókot, és érvényesíti a generált összetevő.|
|/Labs/artifactSources/armTemplates/Read|Olvassa el az azure resource manager-sablonok.|
|/Labs/Costs/Read|Olvassa el a költségeket.|
|/Labs/Costs/Write|Adja hozzá, és módosíthatja az.|
|/Labs/virtualNetworks/DELETE|Törölje a virtuális hálózatok.|
|/Labs/virtualNetworks/Read|Olvassa el a virtuális hálózatok.|
|/Labs/virtualNetworks/Write|Adja hozzá, vagy módosítsa a virtuális hálózatok.|
|/Labs/Formulas/DELETE|Képletek törlése.|
|/Labs/Formulas/Read|Olvassa el a formulákat.|
|/Labs/Formulas/Write|Hozzáadása vagy módosítása formulákat.|
|/Labs/schedules/DELETE|Törölni az ütemezéseket.|
|/Labs/schedules/Read|Olvassa el az ütemezések.|
|/Labs/schedules/Write|Hozzáadása vagy módosítása ütemezések.|
|/Labs/schedules/Execute/Action|Ütemezés hajtható végre.|
|/Labs/schedules/ListApplicable/Action|Felsorolja az összes megfelelő ütemezést|
|/Labs/galleryImages/Read|Olvassa el a gyűjtemény lemezképei.|
|/Labs/policySets/EvaluatePolicies/Action|Labor házirend kiértékelése.|
|/Labs/policySets/Policies/DELETE|Törölje a házirendeket.|
|/Labs/policySets/Policies/Read|Olvassa el a házirendeket.|
|/Labs/policySets/Policies/Write|Adja hozzá vagy módosíthat házirendeket.|
|/Labs/virtualMachines/DELETE|Törölje a virtuális gépeket.|
|/Labs/virtualMachines/Read|Olvassa el a virtuális gépek.|
|/Labs/virtualMachines/Write|Adja hozzá, vagy módosítsa a virtuális gépek.|
|/Labs/virtualMachines/Start/Action|Indítsa el a virtuális gépet.|
|/Labs/virtualMachines/STOP/Action|A virtuális gép leállítása|
|/Labs/virtualMachines/ApplyArtifacts/Action|Az összetevők vonatkozik a virtuális gép.|
|/Labs/virtualMachines/AddDataDisk/Action|Új vagy meglévő adatlemezt csatolni a virtuális géphez.|
|/Labs/virtualMachines/DetachDataDisk/Action|Válassza le a megadott lemezt a virtuális gép.|
|/Labs/virtualMachines/Claim/Action|Saját tulajdonba vétele meglévő virtuális gépből|
|/Labs/virtualMachines/ListApplicableSchedules/Action|Felsorolja az összes megfelelő ütemezést|
|/Labs/virtualMachines/schedules/DELETE|Törölni az ütemezéseket.|
|/Labs/virtualMachines/schedules/Read|Olvassa el az ütemezések.|
|/Labs/virtualMachines/schedules/Write|Hozzáadása vagy módosítása ütemezések.|
|/Labs/virtualMachines/schedules/Execute/Action|Ütemezés hajtható végre.|
|/Labs/notificationChannels/DELETE|Notificationchannels törlése.|
|/Labs/notificationChannels/Read|Olvassa el a notificationchannels.|
|/Labs/notificationChannels/Write|Hozzáadása vagy módosítása notificationchannels.|
|/Labs/notificationChannels/Notify/Action|Értesítést küld a megadott csatornán.|
|/schedules/DELETE|Törölni az ütemezéseket.|
|/schedules/Read|Olvassa el az ütemezések.|
|/ ütemezések/írása|Hozzáadása vagy módosítása ütemezések.|
|/schedules/Execute/Action|Ütemezés hajtható végre.|
|/schedules/Retarget/Action|Frissíti a ütemezés célerőforrás azonosítója.|
|/Locations/Operations/Read|Az olvasási műveletek.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Művelet | Leírás |
|---|---|
|/databaseAccountNames/Read|A név foglaltságának ellenőrzése.|
|/databaseAccounts/Read|Az adatbázisfiók beolvasása.|
|/ databaseAccounts/írása|Egy adatbázis-fiók frissítéséhez.|
|/databaseAccounts/listKeys/Action|Egy adatbázis-fiók listázása|
|/databaseAccounts/regenerateKey/Action|Az adatbázisfiók kulcsokkal elforgatása|
|/databaseAccounts/listConnectionStrings/Action|A kapcsolati karakterláncok beolvasása az adatbázisfiók|
|/databaseAccounts/changeResourceGroup/Action|Módosítsa az erőforráscsoport egy olyan adatbázis-fiók|
|/databaseAccounts/failoverPriorityChange/Action|Az adatbázisfiók régiók feladatátvételi prioritás módosítása. Ez használatos kézi feladatátvételi művelet végrehajtásához|
|/databaseAccounts/DELETE|Az adatbázis-fiók törlése.|
|/databaseAccounts/metricDefinitions/Read|Beolvassa az adatbázist fiók metrikák definíciókat.|
|/databaseAccounts/Metrics/Read|Az adatbázis-fiók metrikák beolvasása.|
|/databaseAccounts/usages/Read|Beolvassa az adatbázist fiók módjait.|
|/databaseAccounts/Databases/Collections/metricDefinitions/Read|A gyűjtemény metrikai meghatározásainak beolvasása.|
|/databaseAccounts/Databases/Collections/Metrics/Read|A gyűjtemény metrikák beolvasása.|
|/databaseAccounts/Databases/Collections/usages/Read|Beolvassa a gyűjtemény is érvényesek.|
|/databaseAccounts/Databases/metricDefinitions/Read|Az adatbázis metrikai meghatározásainak beolvasása|
|/databaseAccounts/Databases/Metrics/Read|Az adatbázis metrikák beolvasása.|
|/databaseAccounts/Databases/usages/Read|Olvassa be az adatbázis is érvényesek.|
|/databaseAccounts/readonlykeys/Read|Olvassa be az adatbázis csak olvasható kulcsait.|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Művelet | Leírás |
|---|---|
|/ generateSsoRequest/művelet|Létre a tartomány vezérlőközpont bejelentkezni.|
|/ validateDomainRegistrationInformation/művelet|Ellenőrizze a tartomány beszerzési objektum lefolytatása nélkül|
|/ checkDomainAvailability/művelet|Ellenőrizze, hogy a tartomány megvásárolható|
|/ listDomainRecommendations/művelet|A lista tartomány meg ajánlásainkat kulcsszavak beolvasása|
|/ regisztrációs/művelet|Az előfizetés a Microsoft Domains erőforrás-szolgáltató regisztrálása|
|/ tartományok/Olvasás|A tartományok listájának lekérdezése|
|/ tartományok/írása|Új tartomány hozzáadása vagy egy meglévő frissítése|
|/ tartományok/törlése|Törölje a meglévő tartományban.|
|/Domains/operationresults/Read|A tartomány művelet|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Művelet | Leírás |
|---|---|
|/lcsprojects/Read|A felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektek megjelenítéséhez|
|/ lcsprojects/írása|Hozzon létre, és a felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektek frissítése. Csak a nevet és leírást tulajdonságait is lehet frissíteni. Az egyes előfizetésekhez és helyekhez társított a projekt létrehozása után nem frissíthető|
|/lcsprojects/DELETE|A felhasználóhoz tartozó Microsoft Dynamics életciklus-Services-projektek törlése|
|/lcsprojects/clouddeployments/Read|Megjeleníti a Microsoft Dynamics AX 2012 R3 értékelési központi telepítések a felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektben|
|/lcsprojects/clouddeployments/Write|A felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektben létrehozása a Microsoft Dynamics AX 2012 R3 értékelési telepítést. Központi telepítések is kezelhető az Azure felügyeleti portálon|
|/lcsprojects/Connectors/Read|Olvassa el a Microsoft Dynamics életciklus szolgáltatások projekthez tartozó összekötők|
|/lcsprojects/Connectors/Write|Létrehozása és frissítése a Microsoft Dynamics életciklus szolgáltatások projekthez tartozó összekötők|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Művelet | Leírás |
|---|---|
|/ checkNameAvailability/művelet|A névtér elérhetőségét ellenőrzi megadott előfizetéshez.|
|/ regisztrációs/művelet|Az EventHub erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása és az EventHub-erőforrások létrehozásának engedélyezése|
|/ névterek/írása|Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a Namespace állapota már nem frissíthető tulajdonságait.|
|/Namespaces/Read|A névtérerőforrások leírásai listájának beolvasása|
|/ névterek/törlése|Egy névtérerőforrás törlése|
|/Namespaces/metricDefinitions/Read|Namespace metrikák erőforrás leírása listájának beolvasása|
|/Namespaces/authorizationRules/Read|A névtér-engedélyezési szabályok leírásai listájának beolvasása.|
|/Namespaces/authorizationRules/Write|A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|/Namespaces/authorizationRules/DELETE|Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető. |
|/Namespaces/authorizationRules/listkeys/Action|A névtérhez tartozó kapcsolati karakterlánc beolvasása|
|/Namespaces/authorizationRules/regenerateKeys/Action|Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása|
|/Namespaces/eventhubs/Write|Hozzon létre vagy frissítés EventHub tulajdonságai.|
|/Namespaces/eventhubs/Read|Az EventHub erőforrás leírások listáját|
|/Namespaces/eventhubs/DELETE|A művelet az EventHub erőforrás törlése|
|/Namespaces/eventHubs/consumergroups/Write|Hozzon létre vagy frissítés ConsumerGroup tulajdonságai.|
|/Namespaces/eventHubs/consumergroups/Read|Listájának ConsumerGroup erőforrás leírása|
|/Namespaces/eventHubs/consumergroups/DELETE|A művelet ConsumerGroup erőforrás törlése|
|/Namespaces/eventhubs/authorizationRules/Read| Az EventHub-az engedélyezési szabályok listájának lekérdezése|
|/Namespaces/eventhubs/authorizationRules/Write|Az EventHub-engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|/Namespaces/eventhubs/authorizationRules/DELETE|A művelet törli az EventHub-engedélyezési szabályok|
|/Namespaces/eventhubs/authorizationRules/listkeys/Action|Az EventHub kapcsolati karakterláncot az beszerzése|
|/Namespaces/eventhubs/authorizationRules/regenerateKeys/Action|Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása|
|/Namespaces/diagnosticSettings/Read|Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása|
|/Namespaces/diagnosticSettings/Write|Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása|
|/Namespaces/logDefinitions/Read|Listájának Namespace naplók erőforrás leírása|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Művelet | Leírás |
|---|---|
|/Providers/features/Read|Lekérdezi az előfizetés szolgáltatásait az adott erőforrás-szolgáltatón.|
|/Providers/features/register/Action|Regisztrálja az előfizetéshez tartozó szolgáltatást az adott erőforrás-szolgáltatón.|
|/features/Read|Lekérdezi az előfizetéshez tartozó szolgáltatásokat.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Művelet | Leírás |
|---|---|
|/ fürtök/írása|Létrehozni vagy frissíteni a HDInsight-fürt|
|/Clusters/Read|Részletes információkat szolgáltatva HDInsight-fürt|
|/Clusters/DELETE|A HDInsight-fürt törlése|
|/Clusters/changerdpsetting/Action|HDInsight-fürt RDP beállításának módosítása|
|/Clusters/configurations/Action|HDInsight-fürt konfigurációjának frissítése|
|/Clusters/configurations/Read|HDInsight-fürt konfigurációjának beolvasása|
|/Clusters/Roles/Resize/Action|A HDInsight-fürt méretezése|
|/Locations/Capabilities/Read|Az előfizetési lehetőségek elérése|
|/Locations/checkNameAvailability/Read|Név foglaltságának ellenőrzése|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|Az importálási/exportálási erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi, hogy az importálási/exportálási feladatok létrehozása.|
|/ feladatok/írása|Létrehoz egy feladatot a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott feladathoz tartozó címkék frissítése.|
|/Jobs/Read|Tulajdonságait olvassa be a megadott feladathoz tartozó, illetve a feladatok beolvasása.|
|/Jobs/listBitLockerKeys/Action|Lekérdezi a megadott feladathoz tartozó BitLocker-kulcsok.|
|/Jobs/DELETE|Egy meglévő feladat törlése.|
|/Locations/Read|A megadott hely tulajdonságainak beolvasása, vagy a helyek listáját adja vissza.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Művelet | Leírás |
|---|---|
|/ Regisztrációs/művelet|A Microsoft Insights-szolgáltató regisztrálása|
|/ AlertRules/írása|Riasztási szabály konfigurációjának írása|
|Vagy AlertRules/törlése|Riasztási szabály konfigurációjának törlése|
|AlertRules/olvasása|Riasztási szabály konfigurációjának beolvasása|
|/ AlertRules/aktiválva/művelet|Riasztási szabály aktiválva|
|/ AlertRules/feloldva/művelet|Riasztási szabály feloldva|
|/ AlertRules/Halmozódni/művelet|A riiasztási szabály elindítva|
|AlertRules/incidensek/olvasása|Riasztási szabály incidenskonfigurációjának beolvasása|
|MetricDefinitions/olvasása|A metrikadefiníciók olvasása|
|/eventtypes/Values/Read|A felügyeleti eseménytípus értékeinek olvasása|
|/eventtypes/digestevents/Read|A felügyeleti eseménytípus kivonatának olvasása|
|Metrikák/olvasása|Metrikák olvasása|
|/ LogProfiles/írása|Egy naplózási profil konfiguráció írásakor|
|Vagy LogProfiles/törlése|Naplóbeállítások profilok törlése|
|LogProfiles/olvasása|Olvasási napló profilok|
|/ AutoscaleSettings/írása|Automatikus skálázási beállítás konfigurációjának írása|
|Vagy AutoscaleSettings/törlése|Automatikus skálázási beállítás konfigurációjának törlése|
|AutoscaleSettings/olvasása|Automatikus skálázási beállítás konfigurációjának beolvasása|
|/ AutoscaleSettings/Scaleup/művelet|Automatikus vertikális felskálázási művelet|
|/ AutoscaleSettings/Scaledown/művelet|Automatikus vertikális leskálázási művelet|
|/AutoscaleSettings/Providers/Microsoft.Insights/MetricDefinitions/Read|A metrikadefiníciók olvasása|
|/ ActivityLogAlerts/aktiválva/művelet|Elindul a napló figyelmeztetés|
|/ DiagnosticSettings/írása|Diagnosztikai beállítások konfigurációjának írása|
|Vagy DiagnosticSettings/törlése|Diagnosztikai beállítások konfigurációjának törlése|
|DiagnosticSettings/olvasása|Diagnosztikai beállítások konfigurációjának olvasása|
|LogDefinitions/olvasása|A naplódefiníciók olvasása|
|/ ExtendedDiagnosticSettings/írása|A bővített diagnosztikai beállítások konfigurációs írása|
|Vagy ExtendedDiagnosticSettings/törlése|Bővített diagnosztikai beállítások konfiguráció törlése|
|ExtendedDiagnosticSettings/olvasása|Bővített diagnosztikai beállítások beolvasásakor|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|Egy előfizetés regisztrálása|
|/checkNameAvailability/Read|Ellenőrzi, hogy a kulcstároló neve érvényes, és nincs használatban|
|/vaults/Read|A kulcstároló tulajdonságainak megtekintése|
|/ tárolók/írása|Hozzon létre egy új kulcstartó, vagy egy meglévő kulcstároló tulajdonságainak módosítása|
|/vaults/DELETE|Kulcstároló törlése|
|/vaults/Deploy/Action|Azure-erőforrások telepítésekor kulcstároló titkos kulcsainak elérésének engedélyezése|
|/vaults/secrets/Read|A titkos kulcs, de az érték nem tulajdonságainak megtekintése|
|/vaults/secrets/Write|Hozzon létre egy új titkos kulcs, vagy frissítse az értéket egy meglévő titok|
|/vaults/accessPolicies/Write|Frissíteni a meglévő hozzáférési házirendek egyesítése vagy cseréje, vagy vegyen fel egy új házirend egy tárolót.|
|/deletedVaults/Read|Az enyhe törölt kulcstárolójának tulajdonságainak megtekintése|
|/Locations/operationResults/Read|Ellenőrizze a hosszú futtatásukkor művelet eredménye|
|/Locations/deletedVaults/Read|Az enyhe törölt kulcstároló tulajdonságainak megtekintése|
|/Locations/deletedVaults/PURGE/Action|Letölthető törölt kulcstároló törlése|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Művelet | Leírás |
|---|---|
|/workflows/Read|Beolvassa a munkafolyamatot.|
|/ munkafolyamatok/írása|Létrehozza vagy frissíti a munkafolyamatot.|
|/workflows/DELETE|Törli a munkafolyamatot.|
|/workflows/Run/Action|Elindítja egy munkafolyamat futtatását.|
|/workflows/disable/Action|Letiltja a munkafolyamatot.|
|/workflows/enable/Action|Engedélyezi a munkafolyamatot.|
|/workflows/Validate/Action|Érvényesíti a munkafolyamatot.|
|/workflows/MOVE/Action|A munkafolyamatot a meglévő előfizetés-azonosítójáról, erőforráscsoportjából és/vagy nevéről egy másik előfizetés-azonosítóra, erőforráscsoportba és/vagy névre helyezi át.|
|/workflows/listSwagger/Action|A munkafolyamat lekérdezi a swagger-definíciók.|
|/workflows/regenerateAccessKey/Action|Újra létrehozza a hívóbetű titkos kulcsait.|
|/workflows/listCallbackUrl/Action|Munkafolyamat lekérdezi a visszahívási URL-CÍMÉT.|
|/workflows/versions/Read|Beolvassa a munkafolyamat verzióját.|
|/workflows/versions/triggers/listCallbackUrl/Action|A visszahívási URL-cím lekérése eseményindító.|
|/ munkafolyamatok/fut/olvasása|Beolvassa a munkafolyamat-futtatást.|
|/workflows/runs/Cancel/Action|Megszakítja egy munkafolyamat futtatását.|
|/workflows/runs/actions/Read|Beolvassa a munkafolyamat-futtatási műveletet.|
|/workflows/runs/Operations/Read|Beolvassa a munkafolyamat-futtatás műveleti állapotát.|
|/workflows/triggers/Read|Az eseményindító beolvasása.|
|/workflows/triggers/Run/Action|Az eseményindító végrehajtása.|
|/workflows/triggers/listCallbackUrl/Action|A visszahívási URL-cím lekérése eseményindító.|
|/workflows/triggers/histories/Read|Az eseményindító alábbi előzményeinek beolvasása.|
|/workflows/triggers/histories/resubmit/Action|Megismétli a munkafolyamat eseményindító.|
|/workflows/accessKeys/Read|Beolvassa a hívóbetűt.|
|/workflows/accessKeys/Write|Létrehozza vagy frissíti a hívóbetűt.|
|/workflows/accessKeys/DELETE|Törli a hívóbetűt.|
|/workflows/accessKeys/List/Action|Listázza a hívóbetű titkos kulcsait.|
|/workflows/accessKeys/regenerate/Action|Újra létrehozza a hívóbetű titkos kulcsait.|
|/Locations/workflows/Validate/Action|Érvényesíti a munkafolyamatot.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|A machine learning web service erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a webszolgáltatások létrehozását.|
|/ webServices/művelet|A támogatott régiók regionális webes szolgáltatás tulajdonságok létrehozása|
|/commitmentPlans/Read|Olvassa el a gépi tanulási kötelezettségvállalás terv|
|/ commitmentPlans/írása|Hozzon létre vagy bármely Machine Learning előfizetési csomag frissítése|
|/commitmentPlans/DELETE|Gépi tanulás kötelezettségvállalás terv törlése|
|/commitmentPlans/JOIN/Action|Csatlakozás a gépi tanulási kötelezettségvállalás terv|
|/commitmentPlans/commitmentAssociations/Read|Olvassa el a gépi tanulási kötelezettségvállalás terv társítása|
|/commitmentPlans/commitmentAssociations/MOVE/Action|Helyezze át a gépi tanulási kötelezettségvállalás terv társítása|
|A munkaterületek között/olvasása|Olvassa el a gépi tanulási munkaterület|
|A munkaterületek között/írása|Létrehozni vagy frissíteni a gépi tanulási munkaterület|
|Vagy munkaterületek/törlése|Gépi tanulás munkaterület törlése|
|/ Munkaterületek/listworkspacekeys/művelet|A Machine Learning-munkaterület listában kulcsok|
|/ Munkaterületek/resyncstoragekeys/művelet|A Machine Learning-munkaterület beállítása tárfiók kulcsait újraszinkronizálásra|
|/WebServices/Read|Olvassa el a Machine Learning webszolgáltatás|
|/ webServices/írása|Létrehozni vagy frissíteni a Machine Learning webszolgáltatás|
|/WebServices/DELETE|A Machine Learning webszolgáltatás törlése|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Művelet | Leírás |
|---|---|
|/agreements/offers/plans/Read|Térjen vissza az adott piactér elem szerződés|
|/agreements/offers/plans/Sign/Action|Egy adott piactér elem létrejött|
|/agreements/offers/plans/Cancel/Action|Szakítsa meg a szerződés egy adott piactér elem|

## <a name="microsoftmedia"></a>Microsoft.Media

| Művelet | Leírás |
|---|---|
|/mediaservices/Read||
|/ mediaservices/írása||
|/mediaservices/DELETE||
|/mediaservices/regenerateKey/Action||
|/mediaservices/listKeys/Action||
|/mediaservices/syncStorageKeys/Action||

## <a name="microsoftnetwork"></a>Microsoft.Network

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|Az előfizetés regisztrálása|
|/ unregister/művelet|Az előfizetés regisztrációjának törlése|
|/ checkTrafficManagerNameAvailability/művelet|A Traffic Manager relatív DNS-név elérhetőségének ellenőrzése.|
|/dnszones/Read|A DNS-zóna JSON formátumban beolvasása. A zóna tulajdonságai tartalmaznak címkéket, etag, numberOfRecordSets és maxnumberofrecordsets tulajdonságokat. Vegye figyelembe, hogy ez a parancs nem kéri le a rekordkészleteket, a zóna tartalmaz.|
|/ dnszones/írása|Hozzon létre, vagy frissítse a DNS-zónák erőforráscsoporton belül.  A címke van megadva a DNS-zóna erőforrás frissítésére szolgáló. Vegye figyelembe, hogy ez a parancs nem használható létrehozására vagy frissítésére a zónán belül rekordhalmazok.|
|/dnszones/DELETE|Törli a DNS-zóna JSON formátumban. A zóna tulajdonságai tartalmaznak címkéket, etag, numberOfRecordSets és maxnumberofrecordsets tulajdonságokat.|
|/dnszones/MX/Read|"MX", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza.|
|/dnszones/MX/Write|Hozzon létre vagy frissíthető a DNS-zónában "MX" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját.|
|/dnszones/MX/DELETE|Eltávolítható az adott nevű és írja be az "MX" a DNS-zónából.|
|/dnszones/NS/Read|DNS NS típusú rekordkészlet beolvasása|
|/dnszones/NS/Write|Létrehozza vagy frissíti a DNS NS típusú rekordhalmaz|
|/dnszones/NS/DELETE|Törli a DNS-rekordhalmaz NS típusú|
|/dnszones/AAAA/Read|"AAAA", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza.|
|/dnszones/AAAA/Write|Hozzon létre vagy frissíthető a DNS-zónában "AAAA" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját.|
|/dnszones/AAAA/DELETE|Eltávolítható az adott nevű és írja be a "AAAA" a DNS-zónából.|
|/dnszones/CNAME/Read|"CNAME", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordkészlet tartalmazza a TTL-t, a címkék és az etag.|
|/dnszones/CNAME/Write|Hozzon létre vagy frissíthető a DNS-zónában "CNAME" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját.|
|/dnszones/CNAME/DELETE|Eltávolítható az adott nevű és írja be a "CNAME" a DNS-zónából.|
|/dnszones/SOA/Read|DNS SOA típusú rekordkészlet beolvasása|
|/dnszones/SOA/Write|Létrehozza vagy frissíti a DNS-rekordhalmaz SOA típusú|
|/dnszones/SRV/Read|"SRV", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza.|
|/dnszones/SRV/Write|SRV típusú rekordot csoport létrehozása vagy frissítése|
|/dnszones/SRV/DELETE|Eltávolítható az adott nevű és írja be a "SRV" a DNS-zónából.|
|/dnszones/PTR/Read|"PTR", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza.|
|/dnszones/PTR/Write|Hozzon létre vagy frissíthető a DNS-zónában "PTR" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját.|
|/dnszones/PTR/DELETE|Eltávolítható az adott nevű és írja be a "PTR" a DNS-zónából.|
|/dnszones/A/Read|A "A" típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza.|
|/dnszones/A/Write|Hozzon létre vagy frissíthető a DNS-zónában "A" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját.|
|/dnszones/A/DELETE|Eltávolítható az adott nevű és írja be a "A" a DNS-zónából.|
|/dnszones/txt/Read|"TXT", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza.|
|/dnszones/txt/Write|Hozzon létre vagy frissíthető a DNS-zónában "TXT" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját.|
|/dnszones/txt/DELETE|Eltávolítható az adott nevű és írja be a "TXT" a DNS-zónából.|
|/dnszones/recordsets/Read|Lekérdezi a DNS-rekordhalmazok típusok között|
|/networkInterfaces/Read|Hálózati illesztő definíciójának beolvasása. |
|/ hálózati illesztők/írása|Egy adott hálózati csatoló létrehozza vagy frissíti a meglévő hálózati illesztő. |
|/networkInterfaces/JOIN/Action|A virtuális gép illesztése hálózati illesztőhöz|
|/networkInterfaces/DELETE|Hálózati kapcsolat törlése|
|/networkInterfaces/effectiveRouteTable/Action|A virtuális gép hálózati illesztőjén konfigurált útválasztási táblázatot beolvasása|
|/networkInterfaces/effectiveNetworkSecurityGroups/Action|Hálózati biztonsági csoportok konfigurált a hálózati kapcsolat, a VM-lekérdezés|
|/networkInterfaces/loadBalancers/Read|A hálózati illesztő részét képező terheléselosztók beolvasása|
|/networkInterfaces/ipconfigurations/Read|A hálózati illesztő ip-konfiguráció definíciójának beolvasása |
|/publicIPAddresses/Read|A nyilvános IP-cím definíciójának beolvasása|
|/ publicIPAddresses/írása|A nyilvános IP-cím létrehozza vagy frissíti a meglévő nyilvános IP-címnek. |
|/publicIPAddresses/DELETE|Törli a nyilvános IP-cím.|
|/publicIPAddresses/JOIN/Action|A nyilvános IP-cím illesztése|
|/routeFilters/Read|Útvonal szűrő definíciójának beolvasása|
|/routeFilters/JOIN/Action|Útvonal szűrő illesztése|
|/routeFilters/DELETE|Egy útvonal szűrődefinícióban törlése|
|/ routeFilters/írása|Útvonal szűrő létrehoz vagy frissít egy meglévő rotue szűrő|
|/routeFilters/Rules/Read|Útvonal szűrési szabály definíciójának beolvasása|
|/routeFilters/Rules/Write|Egy útvonal-szűrő szabályt hoz létre, vagy meglévő útvonal szűrési szabály frissítése|
|/routeFilters/Rules/DELETE|Törli a útvonal szűrő szabályát leíró definíció beolvasása|
|/networkWatchers/Read|A hálózati figyelő definíciójának beolvasása|
|/ networkWatchers/írása|A hálózati figyelőt létrehoz vagy frissít egy meglévő hálózati figyelőt|
|/networkWatchers/DELETE|Törli a hálózati figyelőt|
|/networkWatchers/configureFlowLog/Action|Konfigurálja a cél erőforráson folyamat naplózását.|
|/networkWatchers/ipFlowVerify/Action|Adja vissza, hogy a csomag engedélyezett vagy megtagadott vagy onnan az adott célhelyre.|
|/networkWatchers/nextHop/Action|A megadott cél és a cél IP-cím térjen vissza a következő ugrás típusa, és ezután az IP-cím legyen.|
|/networkWatchers/queryFlowLogStatus/Action|Erőforrás-naplózás folyamat állapotának beolvasása.|
|/networkWatchers/queryTroubleshootResult/Action|Lekérdezi a korábban futtatott vagy jelenleg hibaelhárítási eredménye hibaelhárítási művelet futtatása.|
|/networkWatchers/securityGroupView/Action|A konfigurált és hatékony hálózati biztonsági csoport szabálya a virtuális gép megtekintése.|
|/networkWatchers/topology/Action|Lekérdezi a hálózati szintű áttekintés a erőforrásokat, és azok erőforráscsoportban.|
|/networkWatchers/troubleshoot/Action|Egy hálózati erőforráshoz az Azure-ban végzett hibaelhárítás indítása.|
|/networkWatchers/packetCaptures/queryStatus/Action|Lekérdezi a tulajdonságok és a csomag rögzítési erőforrás állapotával kapcsolatos adatokat.|
|/networkWatchers/packetCaptures/STOP/Action|A futó csomag-rögzítési munkamenet leállítása.|
|/networkWatchers/packetCaptures/Read|A csomag rögzítési definíciójának beolvasása|
|/networkWatchers/packetCaptures/Write|A csomagrögzítéssel létrehozása|
|/networkWatchers/packetCaptures/DELETE|A csomagrögzítéssel törlése|
|/loadBalancers/Read|Terheléselosztó definíciójának beolvasása|
|/ loadBalancers/írása|Terheléselosztó létrehozása vagy meglévő terheléselosztó frissítése|
|/loadBalancers/DELETE|Törli a terheléselosztó|
|/loadBalancers/networkInterfaces/Read|A terheléselosztó minden hálózati interfészen hivatkozások beolvasása|
|/loadBalancers/loadBalancingRules/Read|Terheléselosztó terheléselosztási terheléselosztási szabály definíciójának beolvasása|
|/loadBalancers/backendAddressPools/Read|A load balancer háttércímkészlet definíciójának beolvasása|
|/loadBalancers/backendAddressPools/JOIN/Action|A load balancer háttércímkészletének illesztése|
|/loadBalancers/inboundNatPools/Read|Terheléselosztó bejövő forgalmat kezelő nat-készlet definíciójának beolvasása|
|/loadBalancers/inboundNatPools/JOIN/Action|Művelettel illeszthető egy terheléselosztó bejövő forgalmat kezelő nat-készlete|
|/loadBalancers/inboundNatRules/Read|Terheléselosztó bejövő forgalmat kezelő nat-szabályát leíró definíció beolvasása|
|/loadBalancers/inboundNatRules/Write|Terheléselosztó bejövő forgalmat kezelő nat-szabályának létrehozása vagy frissítése egy meglévő terheléselosztó bejövő nat-szabálya|
|/loadBalancers/inboundNatRules/DELETE|Terheléselosztó bejövő forgalmat kezelő nat-szabályának törlése|
|/loadBalancers/inboundNatRules/JOIN/Action|Terheléselosztó bejövő forgalmat kezelő nat-szabályának illesztése|
|/loadBalancers/outboundNatRules/Read|Terheléselosztó kimenő forgalmat kezelő nat szabály definíciójának beolvasása|
|/loadBalancers/probes/Read|Terheléselosztói mintavétel beolvasása|
|/loadBalancers/virtualMachines/Read|A terheléselosztó virtuális gépek hivatkozások beolvasása|
|/loadBalancers/frontendIPConfigurations/Read|A terheléselosztó előtérbeli IP konfiguráció definíciójának beolvasása|
|/trafficManagerGeographicHierarchies/Read|Lekérdezi a régiókban, amelyek együtt a földrajzi forgalom-útválasztási módszert tartalmazó Traffic Manager Geographic hierarchia|
|/bgpServiceCommunities/Read|Bgp-szolgáltatás Közösségek beolvasása|
|/applicationGatewayAvailableWafRuleSets/Read|Rendelkezésre álló Waf beállítására Alkalmazásátjáró beolvasása|
|/virtualNetworks/Read|A virtuális hálózat definíciójának beolvasása|
|/ virtualNetworks/írása|Létrehoz egy virtuális hálózatot, vagy meglévő virtuális hálózat frissítése|
|/virtualNetworks/DELETE|Virtuális hálózat törlése|
|/virtualNetworks/Peer/Action|A virtuális hálózat egy másik virtuális hálózathoz állomásokhoz|
|/virtualNetworks/virtualNetworkPeerings/Read|Virtuális hálózati társviszony-létesítési definíciójának beolvasása|
|/virtualNetworks/virtualNetworkPeerings/Write|Virtuális hálózati társviszony-létesítés létrehozása vagy frissítése. egy meglévő virtuális hálózati társviszony|
|/virtualNetworks/virtualNetworkPeerings/DELETE|Törli a virtuális hálózati társviszony-létesítés|
|/virtualNetworks/Subnets/Read|Virtuális hálózati alhálózat definíciójának beolvasása|
|/virtualNetworks/Subnets/Write|Virtuális hálózati alhálózat létrehozása vagy egy meglévő virtuális hálózati alhálózat frissítése|
|/virtualNetworks/Subnets/DELETE|Virtuális hálózati alhálózat törlése|
|/virtualNetworks/Subnets/JOIN/Action|Egy virtuális hálózathoz csatlakozik|
|/virtualNetworks/Subnets/joinViaServiceTunnel/Action|Szolgáltatás Tunneling engedélyezve alhálózathoz csatlakozik a erőforrás, például a tárfiók, vagy az SQL-adatbázis.|
|/virtualNetworks/Subnets/virtualMachines/Read|Virtuális hálózati alhálózat virtuális gépeire mutató hivatkozások beolvasása|
|/virtualNetworks/checkIpAddressAvailability/Read|Ellenőrizze, hogy a megadott virtuális hálózati érhető el-e IP-cím|
|/virtualNetworks/virtualMachines/Read|Virtuális hálózat virtuális gépeire mutató hivatkozások beolvasása|
|/expressRouteServiceProviders/Read|Lekérdezi az Express Route-szolgáltatók|
|/dnsoperationresults/Read|A DNS-művelet eredményének beolvasása|
|/localnetworkgateways/Read|Lekérdezi a LocalNetworkGateway|
|/ localnetworkgateways/írása|Létrehoz vagy frissít egy meglévő virtuális|
|/localnetworkgateways/DELETE|LocalNetworkGateway törlése|
|/trafficManagerProfiles/Read|A Traffic Manager-profil konfigurációjának beolvasása. Ez magában foglalja a DNS-beállítások, forgalom útválasztásához tartozó beállításokat, végpontmonitoring beállításai, és továbbítja a Traffic Manager-profil végpontok listáját.|
|/ trafficManagerProfiles/írása|Traffic Manager-profil létrehozása, vagy egy meglévő Traffic Manager-profil konfigurációjának módosítása. Ez magában foglalja a engedélyezésére, vagy egy profil letiltása folyamatban van, és módosítja a DNS-beállítások, a forgalom útválasztásához tartozó beállításokat vagy a végpontmonitoring beállításai. A Traffic Manager-profil által kezelendő végpontok hozzáadott, eltávolított, engedélyezhető vagy le van tiltva.|
|/trafficManagerProfiles/DELETE|A Traffic Manager-profil törlése. A Traffic Manager-profil társított összes beállítást elvész, és a profil már nem használható a forgalom irányításához.|
|/dnsoperationstatuses/Read|A DNS-művelet állapotának beolvasása |
|/Operations/Read|Rendelkezésre álló műveletek beolvasása|
|/expressRouteCircuits/Read|Az Express route-körnek beolvasása|
|/ expressRouteCircuits/írása|Létrehoz vagy frissít egy meglévő Express route-körnek|
|/expressRouteCircuits/DELETE|Az Express route-körnek törlése|
|/expressRouteCircuits/stats/Read|Az Express route-körnek Stat beolvasása|
|/expressRouteCircuits/peerings/Read|Lekérdezi az Express route-körnek társviszony-létesítés|
|/expressRouteCircuits/peerings/Write|Létrehoz vagy frissít egy meglévő Express route-körnek Társviszony|
|/expressRouteCircuits/peerings/DELETE|Az Express route-körnek Társviszony törlése|
|/expressRouteCircuits/peerings/arpTables/Action|Az Express route-körnek társviszony-létesítés ArpTable beolvasása|
|/expressRouteCircuits/peerings/routeTables/Action|Az Express route-körnek társviszony-létesítés Migrálták beolvasása|
|/expressRouteCircuits/társviszony /<br>routeTablesSummary/művelet|Az Express route-körnek társviszony-létesítés Migrálták összegzését beolvasása|
|/expressRouteCircuits/peerings/stats/Read|Lekérdezi az Express route-körnek társviszony-létesítés statisztika|
|/expressRouteCircuits/authorizations/Read|Lekérdezi az Express route-körnek engedély|
|/expressRouteCircuits/authorizations/Write|Létrehozza vagy frissíti a meglévő Express route-körnek engedély|
|/expressRouteCircuits/authorizations/DELETE|Törli az Express route-körnek engedélyezési|
|/Connections/Read|Lekérdezi a ConnectionType(hypernet/routebased)|
|/ kapcsolatok/írása|Létrehoz vagy frissít egy meglévő ConnectionType(hypernet/routebased)|
|/Connections/DELETE|Törli a ConnectionType(hypernet/routebased)|
|/Connections/sharedKey/Read|Lekérdezi a ConnectionType(hypernet/routebased) SharedKey|
|/Connections/sharedKey/Write|Létrehoz vagy frissít egy meglévő ConnectionType(hypernet/routebased) SharedKey|
|/networkSecurityGroups/Read|Hálózati biztonsági csoport definíciójának beolvasása|
|/ biztonsági csoportok/írása|Hálózati biztonsági csoport létrehozása vagy meglévő hálózati biztonsági csoport frissítése|
|/networkSecurityGroups/DELETE|A hálózati biztonsági csoport törlése|
|/networkSecurityGroups/JOIN/Action|Hálózati biztonsági csoport illesztése|
|/networkSecurityGroups/defaultSecurityRules/Read|Alapértelmezett biztonsági szabály definíciójának beolvasása|
|/networkSecurityGroups/securityRules/Read|Biztonsági szabály definíciójának beolvasása|
|/networkSecurityGroups/securityRules/Write|A szabály létrehozása vagy meglévő biztonsági szabály frissítése|
|/networkSecurityGroups/securityRules/DELETE|A biztonsági szabály törlése|
|/applicationGateways/Read|Alkalmazásátjáró beolvasása|
|/ applicationGateways/írása|Alkalmazásátjáró létrehozása vagy meglévő Alkalmazásátjáró frissítése|
|/applicationGateways/DELETE|Alkalmazásátjáró törlése|
|/applicationGateways/backendhealth/Action|Az átjáró háttér állapotának beolvasása|
|/applicationGateways/Start/Action|Alkalmazásátjáró kezdődik|
|/applicationGateways/STOP/Action|Leállítja az Alkalmazásátjáró|
|/applicationGateways/backendAddressPools/JOIN/Action|Egy alkalmazás Alkalmazásátjáró háttércímkészletének illesztése|
|/routeTables/Read|Útvonaltábla-definíció beolvasása|
|/ routeTables/írása|Útvonaltábla létrehozása vagy meglévő útvonaltábla frissítése|
|/routeTables/DELETE|Útvonaltábla-definíció törlése|
|/routeTables/JOIN/Action|Egy útválasztási táblázatot illesztése|
|/routeTables/routes/Read|Útvonal definíciójának beolvasása|
|/routeTables/routes/Write|Új útvonal létrehozása vagy meglévő útvonal frissítése|
|/routeTables/routes/DELETE|Útvonal-definíció törlése|
|/Locations/operationResults/Read|Aszinkrón POST vagy DELETE művelet eredményének beolvasása|
|/Locations/checkDnsNameAvailability/Read|Ellenőrzi, hogy DNS-címke érhető el a megadott helyen|
|/Locations/usages/Read|A szoftverhasználati mérési adatok erőforrások lekérése|
|/Locations/Operations/Read|Egy aszinkron művelet állapotát jelző művelet erőforrás beolvasása|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|A Notification Hubs-erőforrásszolgáltatóra való előfizetés regisztrálása, valamint a névterek és a Notification Hubs-példányok létrehozásának lehetővé tétele|
|/ CheckNamespaceAvailability/művelet|Adott névtér-erőforrásnév elérhetőségének ellenőrzése a Notification Hub szolgáltatásban.|
|Névterek/írása|Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a Namespace állapota már nem frissíthető tulajdonságait.|
|Névterek/olvasása|A névtérerőforrások leírásai listájának beolvasása|
|Vagy névterek/törlése|Egy névtérerőforrás törlése|
|/ Névterek/authorizationRules/művelet|A névtér-engedélyezési szabályok leírásai listájának beolvasása.|
|/ Névterek/CheckNotificationHubAvailability/művelet|Adott értesítésiközpont-név elérhetőségének ellenőrzése egy névtérben.|
|Névterek/authorizationRules/írása|A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|Névterek/authorizationRules/olvasása|A névtér-engedélyezési szabályok leírásai listájának beolvasása.|
|Vagy névterek/authorizationRules/törlése|Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető. |
|/ Névterek/authorizationRules/listkeys/művelet|A névtérhez tartozó kapcsolati karakterlánc beolvasása|
|/ Névterek/authorizationRules/regenerateKeys/művelet|Egy névtér-engedélyezési szabály elsődleges vagy másodlagos kulcsának újragenerálása; meg kell adni az újragenerálni kívánt kulcsot|
|Névterek/NotificationHubs/írása|Hozzon létre egy értesítési központot, és a tulajdonságok frissítése. A Tulajdonságok főként PNS hitelesítő adatok közé tartozik. Az engedélyezési szabályok és a TTL-t|
|Névterek/NotificationHubs/olvasása|Az értesítésiközpont-erőforrások leírásai listájának beolvasása|
|Vagy névterek/NotificationHubs/törlése|Értesítésiközpont-erőforrás törlése|
|/ Névterek/NotificationHubs/authorizationRules/művelet|Az értesítésiközpont-engedélyezési szabályok listájának beolvasása|
|/ Névterek/NotificationHubs/pnsCredentials/művelet|Minden értesítési központ PNS hitelesítő adatainak lekérése. Ez magában foglalja a wns-ből, a MPNS, a APNS, a GCM és a Baidu hitelesítő adatokat|
|/ Névterek/NotificationHubs/debugSend/művelet|Leküldéses tesztértesítés küldése.|
|Névterek/NotificationHubs/metricDefinitions/olvasása|Namespace metrikák erőforrás leírása listájának beolvasása|
|/Namespaces/NotificationHubs /<br>authorizationRules írása|Értesítési központ engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|/Namespaces/NotificationHubs /<br>authorizationRules olvasása|Az értesítésiközpont-engedélyezési szabályok listájának beolvasása|
|/Namespaces/NotificationHubs /<br>authorizationRules vagy törlése|Értesítésiközpont-engedélyezési szabályok törlése|
|/Namespaces/NotificationHubs /<br>authorizationRules/listkeys/művelet|Az értesítési központhoz tartozó kapcsolati karakterlánc beolvasása|
|/Namespaces/NotificationHubs /<br>authorizationRules/regenerateKeys/művelet|Egy értesítésiközpont-engedélyezési szabály elsődleges vagy másodlagos kulcsának újragenerálása; meg kell adni az újragenerálni kívánt kulcsot|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|Egy erőforrás-szolgáltató előfizetés regisztrálásához.|
|/linkTargets/Read|Felsorolja a meglévő fiókokat, amelyek nem kapcsolódnak az Azure-előfizetéssel. Az Azure-előfizetés összekapcsolása egy munkaterület, egy felhasználói azonosítót, ez a művelet az ügyfél-azonosító tulajdonsággal, a munkaterület létrehozása művelet által visszaadott használja.|
|/ munkaterületek/írása|Új munkaterület vagy egy meglévő munkaterület mutató hivatkozások azáltal, hogy az ügyfél-azonosítója a meglévő munkaterületről hozható létre.|
|/Workspaces/Read|Lekérdezi a meglévő-munkaterülettel|
|/Workspaces/DELETE|A munkaterület törlése. Ha a munkaterületet lett csatolva egy meglévő munkaterület a létrehozás időpontjában volt csatolva a munkaterület nem törlődik.|
|/Workspaces/generateregistrationcertificate/Action|A munkaterület regisztrációs tanúsítványt hoz létre. Ezzel a tanúsítvánnyal a munkaterületen a Microsoft System Center Operation Manager csatlakozni.|
|/Workspaces/sharedKeys/Action|Lekéri a megosztott kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó.|
|/Workspaces/Search/Action|Keresési lekérdezés végrehajtása|
|/Workspaces/Datasources/Read|Adatforrások beolvasása az adott munkaterületen.|
|/Workspaces/Datasources/Write|Az adott munkaterületen Adatforrás létrehozása/frissítése.|
|/Workspaces/Datasources/DELETE|Törölje az adott munkaterületen adatforrásokhoz.|
|/Workspaces/managementGroups/Read|System Center Operations Manager felügyeleti csoport csatlakoztatva a munkaterület neve és metaadatok beolvasása.|
|/Workspaces/Schema/Read|A keresési séma lekérése a munkaterületen.  Keresési séma kitett mezőket és a típusukat tartalmazza.|
|/Workspaces/usages/Read|Lekérdezi a használati adatok egy munkaterület, beleértve a munkaterület által beolvasott adatok mennyiségét.|
|/Workspaces/intelligencepacks/Read|Megjeleníti egy adott worksapce esetében az eszközintelligencia-csomagokat, és is megjeleníti, hogy a csomag engedélyezett vagy letiltott adott munkaterület.|
|/Workspaces/intelligencepacks/enable/Action|Lehetővé teszi, hogy egy adott munkaterület egy intelligence Pack csomagot.|
|/Workspaces/intelligencepacks/disable/Action|Egy intelligence Pack csomagot egy adott munkaterület letiltja.|
|/Workspaces/sharedKeys/Read|Lekéri a megosztott kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó.|
|/Workspaces/savedSearches/Read|Lekérdezi a mentett keresési lekérdezés|
|/Workspaces/savedSearches/Write|A mentett keresési lekérdezést hoz létre|
|/Workspaces/savedSearches/DELETE|Törli a mentett keresési lekérdezés|
|/Workspaces/storageinsightconfigs/Write|Létrehoz egy új tárolási konfigurációt. Ezek a beállítások segítségével olvasnak be adatokat egy hely a meglévő tárfiókot.|
|/Workspaces/storageinsightconfigs/Read|Lekérdezi a tárolási konfiguráció.|
|/Workspaces/storageinsightconfigs/DELETE|A tárolási konfiguráció törlése. Ez a jövőben nem Microsoft Operational Insights adatok beolvasása a tárfiókból.|
|/Workspaces/configurationScopes/Read|A konfigurációs hatókörben beolvasása|
|/Workspaces/configurationScopes/Write|Konfigurációs hatókör megadása|
|/Workspaces/configurationScopes/DELETE|A konfigurációs hatókörben törlése|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|Egy erőforrás-szolgáltató előfizetés regisztrálásához.|
|/ megoldások írása|Új OMS-megoldás létrehozása|
|/Solutions/Read|Kilépés az OMS-megoldás beszerzése|
|/Solutions/DELETE|Törölje a meglévő OMS-megoldás|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Művelet | Leírás |
|---|---|
|/ Tárolók/backupJobsExport/művelet|Exportálási feladat|
|Tárolók/írása|A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre|
|Tárolók/olvasása|A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum|
|Vagy tárolók vagy törlése|A tároló törlése a művelet törli a megadott Azure-erőforrás "tárolóban" típusú|
|Tárolók/refreshContainers/olvasása|Frissíti a tároló listáját|
|Tárolók/backupJobsExport/operationResults/olvasása|Az exportálási feladat művelet eredményét adja vissza.|
|Tárolók/backupOperationResults/olvasása|A Recovery Services-tárolóval kapcsolatos biztonsági mentés eredményét adja vissza.|
|Tárolók/monitoringAlerts/olvasása|A riasztások lekéri a Recovery services-tároló.|
|/Vaults/monitoringAlerts / {uniqueAlertId} / olvasása|Lekérdezi a riasztás részletes adatait.|
|Tárolók/backupSecurityPIN/olvasása|Visszaadja a PIN-kód biztonsági tároló szolgáltatásról a helyreállításhoz.|
|/vaults/replicationEvents/Read|Bármely események olvasása|
|Tárolók/backupProtectableItems/olvasása|A védhető elemek listáját adja vissza.|
|/vaults/replicationFabrics/Read|A hálók olvasása|
|/vaults/replicationFabrics/Write|Létrehozni vagy frissíteni a háló|
|/vaults/replicationFabrics/Remove/Action|Távolítsa el a háló|
|/vaults/replicationFabrics/checkConsistency/Action|Konzisztencia-ellenőrzést futtat a hálón|
|/vaults/replicationFabrics/DELETE|A hálók törlése|
|/vaults/replicationFabrics/renewcertificate/Action||
|/vaults/replicationFabrics/deployProcessServerImage/Action|Lemezkép központi telepítése|
|/vaults/replicationFabrics/reassociateGateway/Action|Átjáró újbóli társítása|
|/ tárolók/replicationFabrics/replicationRecoveryServicesProviders /<br>Olvasás|Olvassa el az összes helyreállítási szolgáltatók|
|/ tárolók/replicationFabrics/replicationRecoveryServicesProviders /<br>Távolítsa el a/művelet|Recovery Services-szolgáltató eltávolítása|
|/ tárolók/replicationFabrics/replicationRecoveryServicesProviders /<br>törlés|Bármilyen helyreállítási szolgáltatók törlése|
|/ tárolók/replicationFabrics/replicationRecoveryServicesProviders /<br>refreshProvider/művelet|Frissítse a szolgáltatót|
|/vaults/replicationFabrics/replicationStorageClassifications/Read|A Tárhelybesorolások olvasása|
|/ tárolók/replicationFabrics/replicationStorageClassifications /<br>replicationStorageClassificationMappings olvasása|Olvassa el a tárolási besorolás leképezések|
|/ tárolók/replicationFabrics/replicationStorageClassifications /<br>replicationStorageClassificationMappings írása|Létrehozni vagy frissíteni a tárolási besorolás leképezések|
|/ tárolók/replicationFabrics/replicationStorageClassifications /<br>replicationStorageClassificationMappings vagy törlése|A tárolási besorolás leképezéseket törlése|
|/vaults/replicationFabrics/replicationvCenters/Read|Olvassa el a megfelelő feladat|
|/vaults/replicationFabrics/replicationvCenters/Write|A feladatok létrehozása vagy módosítása|
|/vaults/replicationFabrics/replicationvCenters/DELETE|Feladatok törlése|
|/vaults/replicationFabrics/replicationNetworks/Read|Olvassa el a hálózatok|
|/ tárolók/replicationFabrics/replicationNetworks /<br>replicationNetworkMappings olvasása|Olvassa el a hálózatok leképezését|
|/ tárolók/replicationFabrics/replicationNetworks /<br>replicationNetworkMappings írása|Hozzon létre vagy hálózati leképezések frissítése|
|/ tárolók/replicationFabrics/replicationNetworks /<br>replicationNetworkMappings vagy törlése|Hálózati leképezések törlése|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>Olvasás|Olvassa el a védelmi tárolókkal|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>discoverProtectableItem/művelet|Védhető objektum felderítése|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>írási|Létrehozni vagy frissíteni a védelmi tárolókkal|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>Távolítsa el a/művelet|Távolítsa el a védelmi tároló|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>switchprotection/művelet|Kapcsoló védelmi tároló|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectableItems olvasása|Bármely védhető elemek olvasása|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectionContainerMappings olvasása|Olvassa el a védelmi tároló leképezések|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectionContainerMappings írása|Létrehozni vagy frissíteni a védelmi tároló leképezések|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>a művelet/replicationProtectionContainerMappings/eltávolítása|Védelmitároló-leképezés eltávolítása|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectionContainerMappings vagy törlése|A védelmi tároló leképezéseket törlése|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems olvasása|Minden védett elemek olvasása|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems írása|Minden védett cikkek létrehozása vagy frissítése|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems vagy törlése|Minden védett elemek törlése|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>a művelet/replicationProtectedItems/eltávolítása|Távolítsa el a védett elem|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems/plannedFailover/művelet|Tervezett feladatátvétel|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems/unplannedFailover/művelet|Feladatátvétel|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems/testFailover/művelet|Feladatátvétel tesztelése|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems/testFailoverCleanup/művelet|Teszt feladatátadás kitakarítását|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems/failoverCommit/művelet|Feladatátvétel véglegesítésének|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems/védelem-Újrabeállítási/művelet|Állítsa a védett elem|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems/updateMobilityService/művelet|Frissítse a mobilitási szolgáltatás|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems/repairReplication/művelet|Javítás replikáció|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems/applyRecoveryPoint/művelet|Helyreállítási pont alkalmazása|
|/ tárolók/replicationFabrics/replicationProtectionContainers /<br>olvasási idő replicationProtectedItems/recoveryPoints|Olvassa el a replikációs helyreállítási pontot|
|/vaults/replicationPolicies/Read|Olvassa el az összes házirend|
|/vaults/replicationPolicies/Write|Bármely irányelveinek létrehozása vagy frissítése|
|/vaults/replicationPolicies/DELETE|A szabályzatoknak törlése|
|/vaults/replicationRecoveryPlans/Read|A helyreállítási tervek olvasása|
|/vaults/replicationRecoveryPlans/Write|A helyreállítási tervek létrehozása vagy módosítása|
|/vaults/replicationRecoveryPlans/DELETE|A helyreállítási terv törlése|
|/vaults/replicationRecoveryPlans/plannedFailover/Action|Tervezett feladatátvétel helyreállítási terv|
|/vaults/replicationRecoveryPlans/unplannedFailover/Action|Feladatátvételi helyreállítási terv|
|/vaults/replicationRecoveryPlans/testFailover/Action|Teszt feladatátvételi helyreállítási terv|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/Action|Teszt feladatátadás kitakarítását helyreállítási terv|
|/vaults/replicationRecoveryPlans/failoverCommit/Action|Feladatátvétel véglegesítésének helyreállítási terv|
|/vaults/replicationRecoveryPlans/reProtect/Action|Állítsa a helyreállítási terv|
|Tárolók/extendedInformation/olvasása|A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be|
|Tárolók/extendedInformation/írása|A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be|
|Vagy tárolók/extendedInformation/törlése|A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be|
|Tárolók/backupManagementMetaData/olvasása|A Recovery Services-tároló biztonságimásolat-kezelési metaadatait adja vissza.|
|Tárolók/backupProtectionContainers/olvasása|Az előfizetéshez tartozó összes tárolókat ad vissza|
|Tárolók/backupFabrics/operationResults/olvasása|A művelet állapotának beolvasása|
|Tárolók/backupFabrics/protectionContainers/olvasása|Minden regisztrált tárolókat ad vissza|
|/ Tárolók/backupFabrics/protectionContainers /<br>operationResults olvasása|Beolvassa a védelmi tárolón végrehajtott művelet eredményét.|
|/ Tárolók/backupFabrics/protectionContainers /<br>protectedItems olvasása|A védett elem kapcsolatobjektum-adatait adja vissza|
|/ Tárolók/backupFabrics/protectionContainers /<br>protectedItems írása|Biztonsági mentési védett típusú elem létrehozása|
|/ Tárolók/backupFabrics/protectionContainers /<br>protectedItems vagy törlése|Törli a védett elem|
|/ Tárolók/backupFabrics/protectionContainers /<br>protectedItems, a biztonsági mentés/a művelet|Biztonsági másolat készítése egy védett elemről.|
|/ Tárolók/backupFabrics/protectionContainers /<br>olvasási idő protectedItems/operationResults|Beolvassa a védett elemeken végrehajtott művelet eredményét.|
|/ Tárolók/backupFabrics/protectionContainers /<br>olvasási idő protectedItems/operationStatus|A védett elemeken végrehajtott művelet állapotát adja vissza.|
|/ Tárolók/backupFabrics/protectionContainers /<br>olvasási idő protectedItems/recoveryPoints|Védett elemek helyreállítási pontjainak beolvasása.|
|/ Tárolók/backupFabrics/protectionContainers /<br>protectedItems/recoveryPoints /<br>visszaállítási/művelet|Védett elemek helyreállítási pontjainak visszaállítása.|
|/ Tárolók/backupFabrics/protectionContainers /<br>protectedItems/recoveryPoints /<br>provisionInstantItemRecovery/művelet|Védett elem rendelkezés azonnali elem helyreállítása|
|/ Tárolók/backupFabrics/protectionContainers /<br>protectedItems/recoveryPoints /<br>revokeInstantItemRecovery/művelet|Védett elem azonnali elem helyreállítása visszavonása|
|Tárolók/módjait/olvasása|Egy adott Recovery Services-tároló használati adatait adja vissza.|
|/vaults/usages/Read|Olvassa el a tároló módjait|
|Tárolók/tanúsítvány/írása|A frissítés erőforrás tanúsítvány művelet frissíti az erőforrás/tárolói hitelesítő adatainak tanúsítványa.|
|Tárolók/tokenInfo/olvasása|Recovery Services-tároló információi token értéket ad vissza.|
|/vaults/replicationAlertSettings/Read|Olvassa el a riasztások beállításai|
|/vaults/replicationAlertSettings/Write|Minden riasztás beállításainak létrehozása vagy frissítése|
|Tárolók/backupOperations/olvasása|Visszaadja a biztonsági mentési művelet állapotának helyreállítási szolgáltatások tárolóban.|
|Tárolók/storageConfig/olvasása|Helyreállítási beolvasása-tárolási konfigurációját szolgáltatások tárolóban.|
|Tárolók/storageConfig/írása|Frissítések Tárkonfigurációt helyreállítási szolgáltatások tárolóban.|
|Tárolók/backupUsageSummaries/olvasása|Recovery Services beolvasása védett elemek és a védett kiszolgálók összesítések.|
|Tárolók/backupProtectedItems/olvasása|A védett elemek listájának beolvasása.|
|Tárolók/backupconfig/vaultconfig/olvasása|A helyreállításhoz adja vissza konfigurációs szolgáltatások tárolóban.|
|Tárolók/backupconfig/vaultconfig/írása|Frissítések konfigurációja helyreállítási szolgáltatások tárolóban.|
|Tárolók/registeredIdentities/írása|A szolgáltatás tárolójának regisztrálása műveletet szolgáltatásban való regisztráláshoz tárolója helyreállítási használható.|
|Tárolók/registeredIdentities/olvasása|Az beszerzése tárolókat művelet alkalmazható a tárolók regisztrált erőforrás lekérése.|
|Vagy tárolók/registeredIdentities/törlése|A tároló regisztrációját művelet használható a tároló regisztrációját.|
|Tárolók/registeredIdentities/operationResults/olvasása|Művelet alkalmazható Get művelet eredményeit a műveleti állapotának és az aszinkron módon elküldött művelet eredménye beolvasása|
|/vaults/replicationJobs/Read|Olvassa el a megfelelő feladat|
|/vaults/replicationJobs/Cancel/Action|Megszakítása|
|/vaults/replicationJobs/restart/Action|Indítsa újra a feladatot|
|/vaults/replicationJobs/Resume/Action|Feladat folytatása|
|Tárolók/backupPolicies/olvasása|Visszaadja az összes védelmi házirend|
|Tárolók/backupPolicies/írása|Védelmi házirend létrehozása|
|Vagy tárolók/backupPolicies/törlése|Védelmi házirend törlése|
|Tárolók/backupPolicies/operationResults/olvasása|A szabályzatművelet eredményeinek beolvasása.|
|Tárolók/backupPolicies/operationStatus/olvasása|Házirend művelet állapotának beolvasása.|
|Tárolók/vaultTokens/olvasása|A tároló Token művelet alkalmazható tároló szintű háttérbeli műveletek tároló jogkivonatának beszerzéséhez.|
|Tárolók/monitoringConfigurations/notificationConfiguration/olvasása|A helyreállítási szolgáltatás tároló értesítési beállításainak lekérése.|
|Tárolók/backupJobs/olvasása|Minden feladat objektumot ad vissza|
|/ Tárolók/backupJobs/Mégse/művelet|A feladat megszakítása|
|Tárolók/backupJobs/operationResults/olvasása|A feladatművelet eredményét adja vissza.|
|/Locations/allocateStamp/Action|AllocateStamp egy szolgáltatás által használt belső művelet|
|/Locations/allocatedStamp/Read|A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Művelet | Leírás |
|---|---|
|/ checkNamespaceAvailability/művelet|A névtér elérhetőségét ellenőrzi megadott előfizetéshez.|
|/ regisztrációs/művelet|A Relay erőforrás-szolgáltató előfizetésének regisztrálása és a Relay-erőforrások létrehozásának engedélyezése|
|/ névterek/írása|Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a Namespace állapota már nem frissíthető tulajdonságait.|
|/Namespaces/Read|A névtérerőforrások leírásai listájának beolvasása|
|/ névterek/törlése|Egy névtérerőforrás törlése|
|/Namespaces/authorizationRules/Write|A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|/Namespaces/authorizationRules/DELETE|Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető. |
|/Namespaces/authorizationRules/listkeys/Action|A névtérhez tartozó kapcsolati karakterlánc beolvasása|
|/Namespaces/HybridConnections/Write|Hozzon létre vagy frissítés HybridConnection tulajdonságai.|
|/Namespaces/HybridConnections/Read|Listájának HybridConnection erőforrás leírása|
|/Namespaces/HybridConnections/DELETE|A művelet HybridConnection erőforrás törlése|
|/Namespaces/HybridConnections/authorizationRules/Write|HybridConnection engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|/Namespaces/HybridConnections/authorizationRules/DELETE|A művelet törli HybridConnection engedélyezési szabályokat|
|/Namespaces/HybridConnections/authorizationRules/listkeys/Action|A kapcsolati karakterláncot HybridConnection beolvasása|
|/Namespaces/WcfRelays/Write|Hozzon létre vagy frissítés WcfRelay tulajdonságai.|
|/Namespaces/WcfRelays/Read|Listájának WcfRelay erőforrás leírása|
|/Namespaces/WcfRelays/DELETE|A művelet WcfRelay erőforrás törlése|
|/Namespaces/WcfRelays/authorizationRules/Write|WcfRelay engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|/Namespaces/WcfRelays/authorizationRules/DELETE|A művelet törli WcfRelay engedélyezési szabályokat|
|/Namespaces/WcfRelays/authorizationRules/listkeys/Action|A kapcsolati karakterláncot WcfRelay beolvasása|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Művelet | Leírás |
|---|---|
|AvailabilityStatuses/olvasása|Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát|
|AvailabilityStatuses/current/olvasása|Beolvassa a megadott erőforrás rendelkezésre állási állapotát|

## <a name="microsoftresources"></a>Microsoft.Resources

| Művelet | Leírás |
|---|---|
|/ checkResourceName/művelet|Az erőforrásnév érvényességének ellenőrzése.|
|/Providers/Read|A szolgáltatók listájának lekérdezése.|
|/Subscriptions/Read|Beolvassa az előfizetések listáját.|
|/Subscriptions/operationresults/Read|A művelet eredménye tartalmazó előfizetés beszerzéséhez.|
|/Subscriptions/Providers/Read|Beolvassa vagy listázza az erőforrás-szolgáltatók.|
|/Subscriptions/tagNames/Read|Beolvassa vagy listázza az előfizetéscímkéket.|
|/Subscriptions/tagNames/Write|Egy előfizetés címkét ad hozzá.|
|/Subscriptions/tagNames/DELETE|Egy előfizetés címke törlése.|
|/Subscriptions/tagNames/tagValues/Read|Beolvassa vagy listázza az előfizetéscímkék értékeit.|
|/Subscriptions/tagNames/tagValues/Write|Előfizetés címke érték hozzáadása.|
|/Subscriptions/tagNames/tagValues/DELETE|Egy előfizetés címke törlése.|
|/Subscriptions/Resources/Read|Lekérdezi az előfizetés.|
|/Subscriptions/resourceGroups/Read|Beolvassa vagy listázza az erőforráscsoportokat.|
|/Subscriptions/resourceGroups/Write|Létrehozza vagy frissíti az erőforráscsoportot.|
|/Subscriptions/resourceGroups/DELETE|Törli az erőforráscsoportot és az ahhoz tartozó összes erőforrást.|
|/Subscriptions/resourceGroups/moveResources/Action|Erőforrásokat helyez át az egyik erőforráscsoportból a másikba.|
|/Subscriptions/resourceGroups/validateMoveResources/Action|Áthelyezésének ellenőrzése erőforrásokat az egyik erőforráscsoportból a másikba.|
|/Subscriptions/resourcegroups/Resources/Read|Az erőforráscsoporthoz tartozó erőforrások lekérése.|
|/Subscriptions/resourcegroups/Deployments/Read|Beolvassa vagy listázza az központi telepítéseket.|
|/Subscriptions/resourcegroups/Deployments/Write|Létrehozza vagy frissíti az üzemelő példányt.|
|/Subscriptions/resourcegroups/Deployments/operationstatuses/Read|Beolvassa vagy listázza a központi telepítési művelet állapotok.|
|/Subscriptions/resourcegroups/Deployments/Operations/Read|Beolvassa vagy listázza az üzembe helyezési műveleteket.|
|/Subscriptions/Locations/Read|Beolvassa a támogatott helyek listáját.|
|/Links/Read|Beolvassa vagy listázza az erőforrás-hivatkozások.|
|/ hivatkozások/írása|Létrehozza vagy frissíti az erőforrás-hivatkozást.|
|/Links/DELETE|Erőforrás-hivatkozást törli.|
|/tenants/Read|Beolvassa a bérlők listáját.|
|/resources/Read|A szűrők alapján erőforrások listájának lekérdezése.|
|/Deployments/Read|Beolvassa vagy listázza az központi telepítéseket.|
|/ központi telepítések/írása|Létrehozza vagy frissíti az üzemelő példányt.|
|/Deployments/DELETE|Törli a központi telepítés.|
|/Deployments/Cancel/Action|A központi telepítés megszakítása.|
|/Deployments/Validate/Action|Egy központi telepítésének ellenőrzése.|
|/Deployments/Operations/Read|Beolvassa vagy listázza az üzembe helyezési műveleteket.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Művelet | Leírás |
|---|---|
|/jobcollections/Read|Feladatgyűjtemény beolvasása|
|/ feladatgyűjtemények/írása|Feladatgyűjtemények létrehozása és frissítése|
|/jobcollections/DELETE|A feladatgyűjtemény törlése.|
|/jobcollections/enable/Action|A feladatgyűjtemény engedélyezése.|
|/jobcollections/disable/Action|A feladatgyűjtemény letiltása.|
|/jobcollections/Jobs/Read|A feladat beolvasása.|
|/jobcollections/Jobs/Write|Feladatok létrehozása és frissítése|
|/jobcollections/Jobs/DELETE|A feladat törlése.|
|/jobcollections/Jobs/Run/Action|A feladat futtatása.|
|/jobcollections/Jobs/generateLogicAppDefinition/Action|Logic App-definíció előállítása egy Scheduler-feladat alapján.|
|/jobcollections/Jobs/jobhistories/Read|A feladatelőzmények beolvasása.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|A keresési erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a keresési szolgáltatások létrehozását.|
|/ checkNameAvailability/művelet|A szolgáltatásnév rendelkezésre állását ellenőrzi.|
|/ searchServices/írása|Létrehozza vagy frissíti a keresési szolgáltatást.|
|/searchServices/Read|Olvassa be a keresési szolgáltatást.|
|/searchServices/DELETE|Törli a keresési szolgáltatást.|
|/searchServices/Start/Action|Elindítja a keresési szolgáltatást.|
|/searchServices/STOP/Action|A keresési szolgáltatás leállítása.|
|/searchServices/listAdminKeys/Action|Az adminisztrációs kulcsok beolvasása.|
|/searchServices/regenerateAdminKey/Action|Az adminisztrátori kulcs újragenerálása.|
|/searchServices/createQueryKey/Action|A lekérdezési kulcsot hoz létre.|
|/searchServices/queryKey/Read|A lekérdezési kulcsok beolvasása.|
|/searchServices/queryKey/DELETE|Törli a lekérdezési kulcsot.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Művelet | Leírás |
|---|---|
|/jitNetworkAccessPolicies/Read|Lekérdezi a közvetlenül az időponthoz kötött hálózati hozzáférési házirendek|
|/ jitNetworkAccessPolicies/írása|Létrehoz egy új just-in-time hálózati hozzáférési házirendet, vagy frissít egy meglévő|
|/jitNetworkAccessPolicies/initiate/Action|Kezdeményezi a közvetlenül az időponthoz kötött hálózati hozzáférési házirend|
|/securitySolutionsReferenceData/Read|Lekérdezi a biztonsági megoldások referenciaadatok|
|/securityStatuses/Read|Az Azure-erőforrások állapotát állapotok a biztonsági beolvasása|
|/webApplicationFirewalls/Read|Lekérdezi a webes alkalmazás tűzfalak|
|/ webApplicationFirewalls/írása|Létrehoz egy új webalkalmazási tűzfal vagy frissít egy meglévő|
|/webApplicationFirewalls/DELETE|Webalkalmazási tűzfal törlése|
|/securitySolutions/Read|Lekérdezi a biztonsági megoldások|
|/ securitySolutions/írása|Létrehoz egy új biztonsági megoldást, vagy frissít egy meglévő|
|/securitySolutions/DELETE|Olyan biztonsági megoldás törlése|
|/Tasks/Read|Lekérdezi az összes rendelkezésre álló biztonsági javaslatok|
|/Tasks/dismiss/Action|Biztonsági ajánlás olyan környezetekben elvetése|
|/Tasks/Activate/Action|Biztonsági ajánlás olyan környezetekben aktiválása|
|/Policies/Read|Lekérdezi a biztonsági házirend|
|/ házirendek/írása|A biztonsági házirend frissítése|
|/applicationWhitelistings/Read|Az alkalmazás whitelistings beolvasása|
|/ applicationWhitelistings/írása|Létrehoz egy új alkalmazások engedélyezése vagy frissít egy meglévő|

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

| Művelet | Leírás |
|---|---|
|/ subscriptions/írása|Létrehozza vagy frissíti az előfizetés|
|/ átjárók/írása|Létrehozza vagy frissíti az átjáró|
|/Gateways/DELETE|Átjáró törlése|
|/Gateways/Read|Lekérdezi egy átjáró|
|/Gateways/regenerateprofile/Action|Az átjáró profil újragenerálása|
|/Gateways/upgradetolatest/Action|Az átjárót a legújabb verzióra frissíti.|
|/ csomópontok/írása|létrehozza vagy frissíti a csomópont|
|/NODES/DELETE|A csomópont törlése|
|/NODES/Read|Lekérdezi a csomópont|
|/ munkamenetek/írása|Létrehozza vagy frissíti a munkamenet|
|/SESSIONS/Read|A munkamenet beolvasása|
|/SESSIONS/DELETE|Törli a munkamenetet|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Művelet | Leírás |
|---|---|
|/ checkNameAvailability/művelet|A névtér elérhetőségét ellenőrzi megadott előfizetéshez.|
|/ regisztrációs/művelet|Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóra, és lehetővé teszi a ServiceBus-erőforrások létrehozását|
|/ névterek/írása|Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a Namespace állapota már nem frissíthető tulajdonságait.|
|/Namespaces/Read|A névtérerőforrások leírásai listájának beolvasása|
|/ névterek/törlése|Egy névtérerőforrás törlése|
|/Namespaces/metricDefinitions/Read|Namespace metrikák erőforrás leírása listájának beolvasása|
|/Namespaces/authorizationRules/Write|A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|/Namespaces/authorizationRules/Read|A névtér-engedélyezési szabályok leírásai listájának beolvasása.|
|/Namespaces/authorizationRules/DELETE|Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető. |
|/Namespaces/authorizationRules/listkeys/Action|A névtérhez tartozó kapcsolati karakterlánc beolvasása|
|/Namespaces/authorizationRules/regenerateKeys/Action|Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása|
|/Namespaces/diagnosticSettings/Read|Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása|
|/Namespaces/diagnosticSettings/Write|Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása|
|/Namespaces/Queues/Write|Hozzon létre vagy frissítés várólista-tulajdonságok.|
|/Namespaces/Queues/Read|Várólista erőforrás leírása listájának beolvasása|
|/Namespaces/Queues/DELETE|A művelet várólista erőforrás törlése|
|/Namespaces/Queues/authorizationRules/Write|Várólista engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|/Namespaces/Queues/authorizationRules/Read| A várólista-engedélyezési szabályok listájának lekérdezése|
|/Namespaces/Queues/authorizationRules/DELETE|Művelet az engedélyezési szabályok várólista törlése|
|/Namespaces/Queues/authorizationRules/listkeys/Action|A kapcsolati karakterlánc várólistára beolvasása|
|/Namespaces/Queues/authorizationRules/regenerateKeys/Action|Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása|
|/Namespaces/logDefinitions/Read|Listájának Namespace naplók erőforrás leírása|
|/Namespaces/topics/Write|Hozzon létre vagy frissítés témakör tulajdonságai.|
|/Namespaces/topics/Read|A témakör erőforrás leírások listáját|
|/Namespaces/topics/DELETE|A művelet témakör erőforrás törlése|
|/Namespaces/topics/authorizationRules/Write|A témakör az engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|/Namespaces/topics/authorizationRules/Read| A témakör az engedélyezési szabályok listájának lekérdezése|
|/Namespaces/topics/authorizationRules/DELETE|A művelet törli a témakör az engedélyezési szabályok|
|/Namespaces/topics/authorizationRules/listkeys/Action|A témakör a kapcsolati karakterlánc beolvasása|
|/Namespaces/topics/authorizationRules/regenerateKeys/Action|Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása|
|/Namespaces/topics/Subscriptions/Write|Hozzon létre vagy frissítés TopicSubscription tulajdonságai.|
|/Namespaces/topics/Subscriptions/Read|Listájának TopicSubscription erőforrás leírása|
|/Namespaces/topics/Subscriptions/DELETE|A művelet TopicSubscription erőforrás törlése|
|/Namespaces/topics/Subscriptions/Rules/Write|Hozzon létre vagy frissítés szabály tulajdonságait.|
|/Namespaces/topics/Subscriptions/Rules/Read|Szabály erőforrás leírása listájának beolvasása|
|/Namespaces/topics/Subscriptions/Rules/DELETE|Műveletet, szabály erőforrás törlése|

## <a name="microsoftsql"></a>Microsoft.Sql

| Művelet | Leírás |
|---|---|
|/Servers/Read|Az előfizetés egy erőforráscsoportot a kiszolgálók listáját adja vissza|
|/ kiszolgálók/írása|Hozzon létre egy új kiszolgálót vagy egy erőforráscsoportban található előfizetés a meglévő kiszolgáló tulajdonságainak módosítása|
|/Servers/DELETE|A kiszolgáló és a rajta található adatbázisok és a rugalmas készletek törlése|
|/Servers/import/Action|Hozzon létre egy új adatbázist a kiszolgálón, és a séma és adatainak áttelepítését egy DacPac csomag telepítése|
|/Servers/Upgrade/Action|Új funkció érhető el a legújabb verziót a engedélyezze és adja meg a adatbázisok kiadás átalakítási térkép|
|/Servers/VulnerabilityAssessmentScans/Action|A biztonsági rés server vizsgálat végrehajtása|
|/Servers/operationResults/Read|A művelet előrehaladását úgy követheti nyomon a kiszolgáló frissítése az alacsonyabb verzióra való magasabb szolgál|
|/Servers/operationResults/DELETE|Megszakítás kiszolgáló verzió frissítése folyamatban|
|/Servers/securityAlertPolicies/Read|A megadott kiszolgálón konfigurált kiszolgáló fenyegetés észlelési házirend részleteinek beolvasása|
|/Servers/securityAlertPolicies/Write|A kiszolgáló fenyegetések észlelése egy adott kiszolgáló módosítása|
|/Servers/securityAlertPolicies/operationResults/Read|A kiszolgáló Fenyegetésészlelés házirend Set művelet eredményének beolvasása|
|/Servers/Administrators/Read|Rendszergazda kiszolgálóadatok beolvasása|
|/Servers/Administrators/Write|Létrehozni vagy frissíteni a kiszolgáló rendszergazdája|
|/Servers/Administrators/DELETE|Törölje a kiszolgáló rendszergazdája a kiszolgálóról|
|/Servers/recoverableDatabases/Read|Ez a művelet segítségével élő adatbázis vész-helyreállítási utolsó ismert helyes biztonsági mentési pontok-adatbázis helyreállításához. A legutóbbi helyes biztonsági másolatot kapcsolatos információkat ad vissza, de azt nem ténylegesen állítsa vissza az adatbázist.|
|/Servers/serviceObjectives/Read|A szolgáltatási szint célkitűzései (más néven teljesítmény rétegek) a megadott kiszolgálón elérhető listájának beolvasása|
|/Servers/firewallRules/Read|Kiszolgáló tűzfalszabály részletei beolvasása|
|/Servers/firewallRules/Write|A kiszolgálóhoz való csatlakozáshoz engedélyezett IP-címtartomány vezérlő kiszolgáló tűzfal szabályának létrehozása vagy frissítése|
|/Servers/firewallRules/DELETE|Törli a tűzfalszabályt a kiszolgálóról|
|/Servers/administratorOperationResults/Read|Kiszolgálói rendszergazda a művelet eredménye beolvasása|
|/Servers/recommendedElasticPools/Read|Ajánlott rugalmas adatbáziskészlet költségeinek csökkentése vagy historica erőforrás-használat alapján a teljesítmény javítása beolvasása|
|/Servers/recommendedElasticPools/Metrics/Read|Egy adott kiszolgálóhoz ajánlott rugalmas adatbáziskészletek metrikák beolvasása|
|/Servers/recommendedElasticPools/Databases/Read|Ajánlott rugalmas adatbáziskészlet megadott kiszolgáló be az új adatbázisok beolvasása|
|/Servers/elasticPools/Read|A megadott kiszolgálón rugalmas adatbáziskészlet részleteit beolvasása|
|/Servers/elasticPools/Write|Hozzon létre egy új vagy létező rugalmas adatbáziskészlet tulajdonságainak módosítása|
|/Servers/elasticPools/DELETE|A meglévő rugalmas készlet törlése|
|/Servers/elasticPools/operationResults/Read|Egy adott rugalmas adatbázis-készlet művelet részleteinek beolvasása|
|/Servers/elasticPools/Providers/Microsoft.Insights/<br>metricDefinitions olvasása|Térjen vissza a rugalmas adatbáziskészletek elérhető metrikák típusú|
|/Servers/elasticPools/Providers/Microsoft.Insights/<br>diagnosticSettings olvasása|Az erőforrás diagnosztikai beállításának beolvasása.|
|/Servers/elasticPools/Providers/Microsoft.Insights/<br>diagnosticSettings írása|Létrehozza vagy frissíti az erőforrás diagnosztikai beállításának|
|/Servers/elasticPools/Metrics/Read|Térjen vissza a rugalmas készlet Erőforrás kihasználtsága metrikák|
|/Servers/elasticPools/elasticPoolDatabaseActivity/Read|Tevékenységek és egy adott adatbázisnak, amely része a rugalmas adatbáziskészlet részleteinek beolvasása|
|/Servers/elasticPools/advisors/Read|A rugalmas készlet elérhető tanácsadók listáját adja vissza|
|/Servers/elasticPools/advisors/Write|Frissítés Automatikus végrehajtás az advisor szinten a rugalmas készlet állapotát.|
|/Servers/elasticPools/advisors/recommendedActions/Read|Javasolt művelet a megadott advisor a rugalmas készlet listáját adja vissza|
|/Servers/elasticPools/advisors/recommendedActions/Write|A rugalmas készlet alkalmazni a javasolt művelet|
|/Servers/elasticPools/elasticPoolActivity/Read|Tevékenységek és egy adott rugalmas adatbáziskészlet részleteinek beolvasása|
|/Servers/elasticPools/Databases/Read|Listáját és az adatbázisok rugalmas készlet a megadott kiszolgálón részét képező adatainak beolvasása|
|/Servers/auditingPolicies/Read|Az alapértelmezett kiszolgáló táblázat naplózás a megadott kiszolgálón konfigurált házirend részleteinek beolvasása|
|/Servers/auditingPolicies/Write|Az alapértelmezett kiszolgáló tábla naplózását egy adott kiszolgáló módosítása|
|/Servers/disasterRecoveryConfiguration/operationResults/Read|Katasztrófa utáni helyreállítás konfigurációs műveletet eredményt|
|/Servers/advisors/Read|A kiszolgáló elérhető tanácsadók listáját adja vissza|
|/Servers/advisors/Write|Frissítések automatikus-hajtható végre az advisor a kiszolgáló szintjén állapotát.|
|/Servers/advisors/recommendedActions/Read|Az a kiszolgáló megadott advisor ajánlott műveletek listáját adja vissza|
|/Servers/advisors/recommendedActions/Write|A javasolt műveletet alkalmazza a kiszolgálón|
|/Servers/usages/Read|Térjen vissza a kiszolgáló DTU-kvótáról és aktuális DTU consuption minden adatbázis-kiszolgálón belül|
|/Servers/elasticPoolEstimates/Read|Ehhez a kiszolgálóhoz már létrehozott rugalmas készlet becslések listáját adja vissza|
|/Servers/elasticPoolEstimates/Write|Hoz létre új rugalmas készletbecslés megadott adatbázisok listája|
|/Servers/auditingSettings/Read|A kiszolgáló blob naplózási házirend adatait a megadott kiszolgálón konfigurált beolvasása|
|/Servers/auditingSettings/Write|A kiszolgáló blob naplózását egy adott kiszolgáló módosítása|
|/Servers/auditingSettings/operationResults/Read|A kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása|
|/Servers/backupLongTermRetentionVaults/Read|Ez a művelet egy biztonsági mentési hosszú távú megőrzési tárolóban eléréséhez használatos. A tárolóban regisztrált erre a kiszolgálóra vonatkozó információkat ad vissza.|
|/Servers/backupLongTermRetentionVaults/Write|A biztonsági mentési hosszú távú megőrzési tároló regisztrálása|
|/Servers/restorableDroppedDatabases/Read|Az adatbázisok listája, amelyek el lettek dobva a megadott kiszolgálón még mindig belüli adatmegőrzési beolvasása. Ez a művelet az adatbázisok és kapcsolódó metaadatok, például a törlés napját listáját adja vissza.|
|/Servers/Databases/Read|Az előfizetés egy erőforráscsoportot a kiszolgálók listáját adja vissza|
|/Servers/Databases/Write|Hozzon létre egy új kiszolgálót vagy egy erőforráscsoportban található előfizetés a meglévő kiszolgáló tulajdonságainak módosítása|
|/Servers/Databases/DELETE|A kiszolgáló és a rajta található adatbázisok és a rugalmas készletek törlése|
|/Servers/Databases/export/Action|Hozzon létre egy új adatbázist a kiszolgálón, és a séma és adatainak áttelepítését egy DacPac csomag telepítése|
|/Servers/Databases/VulnerabilityAssessmentScans/Action|A biztonsági rés adatbázis vizsgálat hajtható végre.|
|/Servers/Databases/pause/Action|A DataWarehouse kiadás adatbázis felfüggesztése|
|/Servers/Databases/Resume/Action|A DataWarehouse kiadás adatbázis folytatása|
|/Servers/Databases/operationResults/Read|A művelet segítségével adatbázis hosszú ideig futó művelet, például a skála előrehaladását úgy követheti nyomon.|
|/Servers/Databases/replicationLinks/Read|Egy adott adatbázist létrehozni a replikációs hivatkozások visszatérési részletei|
|/Servers/Databases/replicationLinks/DELETE|Állítsa le a replikációs kapcsolat kényszerített módon, és az esetleges adatvesztés|
|/Servers/Databases/replicationLinks/unlink/Action|Állítsa le a replikációs kapcsolat kényszerített módon vagy a partnerrel való szinkronizálás után|
|/Servers/Databases/replicationLinks/Failover/Action|Feladatátvételi minden szinkronizálás után az elsődleges vált, így ezt az adatbázist az elsődleges replikációs kapcsolat, és a távoli elsődleges és másodlagos be|
|/Servers/Databases/replicationLinks/forceFailoverAllowDataLoss/Action|Feladatátvétel azonnal esetleges adatvesztés, így ezt az adatbázist az elsődleges replikációs kapcsolat, és a távoli elsődleges és másodlagos be|
|/Servers/Databases/replicationLinks/updateReplicationMode/Action|A frissítési replikációs mód csatolás szinkron vagy aszinkron módban|
|/Servers/Databases/replicationLinks/operationResults/Read|A hosszú ideig futó műveletek az adatbázis-replikációs hivatkozások állapotának beolvasása|
|/Servers/Databases/dataMaskingPolicies/Read|A adatmaszkolási konfigurálva egy adott adatbázisnak a házirend részleteinek beolvasása|
|/Servers/Databases/dataMaskingPolicies/Write|Adatmaszkolási egy adott adatbázis-házirend módosítása|
|/Servers/Databases/dataMaskingPolicies/Rules/Read|A adatmaszkolási egy adott adatbázisnak konfigurált házirendszabály részleteit beolvasása|
|/Servers/Databases/dataMaskingPolicies/Rules/Write|Adatok házirend szabály egy adott adatbázisnak a maszkolás módosítása|
|/Servers/Databases/securityAlertPolicies/Read|A fenyegetés szabályzat egy adott adatbázisnak konfigurált részleteinek beolvasása|
|/Servers/Databases/securityAlertPolicies/Write|Egy adott adatbázisnak threat detection szabályzatának módosítása|
|/Servers/Databases/Providers/Microsoft.Insights/<br>metricDefinitions olvasása|Térjen vissza a típusú ítélt adatbázisokhoz|
|/Servers/Databases/Providers/Microsoft.Insights/<br>diagnosticSettings olvasása|Az erőforrás diagnosztikai beállításának beolvasása.|
|/Servers/Databases/Providers/Microsoft.Insights/<br>diagnosticSettings írása|Létrehozza vagy frissíti az erőforrás diagnosztikai beállításának|
|/Servers/Databases/Providers/Microsoft.Insights/<br>logDefinitions olvasása|Az adatbázisok naplók beolvasása.|
|/Servers/Databases/topQueries/Read|Értéket ad vissza kijelölt időszakra összesített értéket a kijelölt lekérdezésre vonatkozó futásidejű statisztikák|
|/Servers/Databases/topQueries/queryText/Read|A kijelölt lekérdezés Azonosítóját a Transact-SQL szöveget adja vissza|
|/Servers/Databases/topQueries/statistics/Read|Értéket ad vissza kijelölt időszakra összesített értéket a kijelölt lekérdezésre vonatkozó futásidejű statisztikák|
|/Servers/Databases/connectionPolicies/Read|A kapcsolat házirend egy adott adatbázisnak konfigurált részleteinek beolvasása|
|/Servers/Databases/connectionPolicies/Write|Módosítsa a kapcsolatkezelési házirendet az egy adott adatbázisnak|
|/Servers/Databases/Metrics/Read|Térjen vissza az adatbázist Erőforrás kihasználtsága metrikák|
|/Servers/Databases/auditRecords/Read|A blob naplózási rekordok beolvasása|
|/Servers/Databases/transparentDataEncryption/Read|Állapot és átlátható titkosítási biztonsági szolgáltatást egy adott adatbázisnak részleteinek beolvasása|
|/Servers/Databases/transparentDataEncryption/Write|Engedélyezheti vagy tilthatja le egy adott adatbázisnak az átlátható adattitkosítás|
|/Servers/Databases/transparentDataEncryption/operationResults/Read|Állapot és átlátható titkosítási biztonsági szolgáltatást egy adott adatbázisnak részleteinek beolvasása|
|/Servers/Databases/auditingPolicies/Read|A naplózási házirend tábla, egy adott adatbázisnak konfigurált adatait beolvasása|
|/Servers/Databases/auditingPolicies/Write|A tábla egy adott adatbázisnak a naplózási házirend módosítása|
|/Servers/Databases/dataWarehouseQueries/Read|Az adatraktár terjesztési lekérdezés információi a kijelölt lekérdezés Azonosítóját adja vissza|
|/ kiszolgálók/adatbázisok/dataWarehouseQueries /<br>dataWarehouseQuerySteps olvasása|Információkat ad vissza az elosztott lekérdezés lépés adatraktár lekérdezés kijelölt lépés azonosítója|
|/Servers/Databases/serviceTierAdvisors/Read|Térjen javaslat adatbázis méretezésével felfelé vagy lefelé kapcsolatos teljesítményének javítása és a költségek csökkentése a lekérdezés végrehajtási statisztika alapján|
|/Servers/Databases/advisors/Read|Az adatbázis elérhető tanácsadók listáját adja vissza|
|/Servers/Databases/advisors/Write|Frissítés Automatikus végrehajtás az advisor adatbázis szinten állapotának.|
|/Servers/Databases/advisors/recommendedActions/Read|A megadott advisort az adatbázishoz ajánlott műveletek listáját adja vissza|
|/Servers/Databases/advisors/recommendedActions/Write|A javasolt műveletet alkalmazza az adatbázishoz|
|/Servers/Databases/usages/Read|Térjen vissza az adatbázis maximális méretét, amely elérhető és az adatok által elfoglalt jelenlegi mérete|
|/Servers/Databases/queryStore/Read|A Lekérdezéstár beállítása az adatbázis aktuális értékek visszaadása|
|/Servers/Databases/queryStore/Write|A Lekérdezéstár-beállítás az adatbázis frissítése|
|/Servers/Databases/auditingSettings/Read|Egy adott adatbázisnak konfigurált blob naplózási házirend részleteinek beolvasása|
|/Servers/Databases/auditingSettings/Write|A blob egy adott adatbázisnak a naplózási házirend módosítása|
|/Servers/Databases/schemas/Tables/recommendedIndexes/Read|Egy adatbázishoz ajánlott index listájának beolvasása|
|/Servers/Databases/schemas/Tables/recommendedIndexes/Write|Alkalmazza az ajánlott index|
|/Servers/Databases/schemas/Tables/Columns/Read|Egy tábla oszlopainak listájának beolvasása|
|/Servers/Databases/missingindexes/Read|Térjen vissza az adatbázis-indexek létrehozása, módosítása vagy törlése lekérdezés teljesítményének javítása érdekében kapcsolatos javaslatok|
|/Servers/Databases/missingindexes/Write|Használja az adatbázishoz ajánlott index az adott adatbázishoz|
|/Servers/Databases/importExportOperationResults/Read|Adatbázis importálása adatait adja vissza, vagy exportálási művelet a tárfiókban található DacPac|
|/Servers/importExportOperationResults/Read|A listában az adatbázis-importálási műveletek visszaadásának storage-fiók a megadott kiszolgálón|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|A tárolásierőforrás-szolgáltató előfizetésének regisztrálása és a tárfiókkészítés engedélyezése.|
|/checknameavailability/Read|Ellenőrzi, hogy érvényes-e a fióknév, és nincs-e használatban.|
|/ storageAccounts/írása|Tárfiók létrehozása a megadott paraméterekkel, a tulajdonságok vagy címkék frissítése, vagy egyéni tartomány felvétele a megadott tárfiókhoz.|
|/storageAccounts/DELETE|Meglévő tárfiók törlése.|
|/storageAccounts/listkeys/Action|A megadott tárfiók hozzáférési kulcsainak lekérése.|
|/storageAccounts/regeneratekey/Action|A megadott tárfiók hozzáférési kulcsainak újragenerálása.|
|/storageAccounts/Read|A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése.|
|/storageAccounts/listAccountSas/Action|A fiók SAS-jogkivonatát adja vissza a megadott tárfiókhoz.|
|/storageAccounts/listServiceSas/Action|Társzolgáltatás SAS-jogkivonata|
|/storageAccounts/Services/diagnosticSettings/Write|Tárolási fiók diagnosztikai beállításainak létrehozása/frissítése.|
|/skus/Read|Felsorolja a Microsoft.Storage által támogatott termékváltozat.|
|/usages/Read|A megadott előfizetésben szereplő erőforrások korlátját és jelenlegi kihasználtsági értéket adja vissza|
|/Operations/Read|Az aszinkrón műveletek állapotának lekérdezése.|
|/Locations/deleteVirtualNetworkOrSubnets/Action|A Microsoft.Storage értesítése arról, hogy virtuális hálózat vagy alhálózat törlése van folyamatban|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Művelet | Leírás |
|---|---|
|/managers/clearAlerts/Action|Törölje a jelet az Eszközkezelőben társított összes riasztást.|
|/managers/getActivationKey/Action|Aktiválási kulcs lekérése az Eszközkezelőben.|
|/managers/regenerateActivationKey/Action|A kezelő aktiválási kulcs újragenerálása.|
|/managers/regenarateRegistationCertificate/Action|Az eszköz kezelők a regisztrációs tanúsítvány újbóli előállítása.|
|/managers/getEncryptionKey/Action|Titkosítási kulcs beszerzése az Eszközkezelőben.|
|/managers/Read|Sorolja fel, vagy az eszköz kezelők beolvasása|
|/managers/DELETE|Törli az eszköz-kezelők|
|/ kezelők/írása|Létrehozni vagy frissíteni az eszköz-kezelők|
|/managers/configureDevice/Action|Eszközök konfigurálása|
|/managers/listActivationKey/Action|Az aktiválási kulcsot a StorSimple eszköz Manager lekérdezi.|
|/managers/listPublicEncryptionKey/Action|Nyilvános titkosítási kulcsok egy StorSimple Device Manager listában.|
|/managers/listPrivateEncryptionKey/Action|Lekérdezi a személyes titkosítási kulcs egy StorSimple az Eszközkezelőben.|
|/managers/provisionCloudAppliance/Action|Hozzon létre egy új felhőalapú készülék.|
|Kezelői/írása|A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre|
|Kezelői/olvasása|A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum|
|Vagy kezelők vagy törlése|A tároló törlése a művelet törli a megadott Azure-erőforrás "tárolóban" típusú|
|/managers/storageAccountCredentials/Write|Létrehozni vagy frissíteni a Tárfiók hitelesítő adatait|
|/managers/storageAccountCredentials/Read|Sorolja fel, vagy a Tárfiók hitelesítő adatainak beolvasása|
|/managers/storageAccountCredentials/DELETE|A Tárfiók hitelesítő adatainak törlése|
|/managers/storageAccountCredentials/listAccessKey/Action|A Tárfiók hitelesítő adatainak hozzáférési listázása|
|/managers/accessControlRecords/Read|Sorolja fel, vagy a hozzáférés-vezérlési rekordokat beolvasása|
|/managers/accessControlRecords/Write|A hozzáférés-vezérlő rekordok létrehozása vagy frissítése|
|/managers/accessControlRecords/DELETE|A hozzáférés-vezérlési rekordokat törlése|
|/managers/Metrics/Read|Sorolja fel, vagy a metrikák beolvasása|
|/managers/bandwidthSettings/Read|A sávszélesség-beállítások (csak 8000 sorozat)|
|/managers/bandwidthSettings/Write|Létrehoz egy új, vagy a sávszélesség-beállítások frissítése (8000 sorozat csak)|
|/managers/bandwidthSettings/DELETE|Törli a meglévő sávszélesség-beállítások (8000 sorozat csak)|
|Kezelői/extendedInformation/olvasása|A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be|
|Kezelői/extendedInformation/írása|A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be|
|Vagy kezelők/extendedInformation/törlése|A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be|
|/managers/Alerts/Read|Lekérdezi a riasztásokat vagy listája|
|/managers/storageDomains/Read|Sorolja fel, vagy a tárolási tartományok beolvasása|
|/managers/storageDomains/Write|Hozzon létre vagy a Storage-tartományok frissítése|
|/managers/storageDomains/DELETE|A tárolási tartományok törlése|
|/managers/Devices/scanForUpdates/Action|Egy eszköz frissítések keresése.|
|/managers/Devices/download/Action|Egy eszköz letöltési frissítéseket.|
|/managers/Devices/Install/Action|Frissítések telepítése egy eszközön.|
|/managers/Devices/Read|Sorolja fel, vagy az eszköz beolvasása|
|/managers/Devices/Write|Létrehozása vagy frissítése az eszközöket|
|/managers/Devices/DELETE|Az eszközök törlése|
|/managers/Devices/Deactivate/Action|Egy eszköz inaktiválja.|
|/managers/Devices/publishSupportPackage/Action|Egy eszköz a Microsoft Support hibaelhárítási támogatási csomag közzététele.|
|/managers/Devices/Failover/Action|Az eszköz feladatátvétele.|
|/managers/Devices/sendTestAlertEmail/Action|Konfigurált küldési e-mail címzetteknek teszt figyelmeztető e-mailt küldeni.|
|/managers/Devices/installUpdates/Action|Telepíti a frissítéseket az eszközökön|
|/managers/Devices/listFailoverSets/Action|A lista a feladatátvétel beállítja egy meglévő eszközt.|
|/managers/Devices/listFailoverTargets/Action|Az eszközök feladatátvételi céljainak|
|/managers/Devices/publicEncryptionKey/Action|Lista nyilvános titkosítási kulcsot, az Eszközkezelőben|
|/ kezelők/eszközök/hardwareComponentGroups /<br>Olvasás|A hardver összetevő csoportokat|
|/ kezelők/eszközök/hardwareComponentGroups /<br>changeControllerPowerState/művelet|Hardver összetevőcsoportok vezérlő power állapotának módosítása|
|/managers/Devices/Metrics/Read|Sorolja fel, vagy a metrikák beolvasása|
|/managers/Devices/chapSettings/Write|A Chap beállításainak létrehozása vagy frissítése|
|/managers/Devices/chapSettings/Read|Sorolja fel, vagy a Chap beállításainak beolvasása|
|/managers/Devices/chapSettings/DELETE|Törli a Chap-beállításokat|
|/managers/Devices/backupScheduleGroups/Read|Sorolja fel, vagy a biztonsági mentési ütemezés csoportok beolvasása|
|/managers/Devices/backupScheduleGroups/Write|A biztonsági mentési ütemezés csoportok létrehozása vagy frissítése|
|/managers/Devices/backupScheduleGroups/DELETE|A biztonsági mentés ütemezése csoportok törlése|
|/managers/Devices/updateSummary/Read|Sorolja fel, vagy a frissítés összegzés beolvasása|
|/ kezelők/eszközök/migrationSourceConfigurations /<br>Importálás/művelet|Az áttelepítéshez forrás konfigurációk importálása|
|/ kezelők/eszközök/migrationSourceConfigurations /<br>startMigrationEstimate/művelet|Az áttelepítési folyamat időtartamának becsléséhez feladat indítása.|
|/ kezelők/eszközök/migrationSourceConfigurations /<br>startMigration/művelet|Forrás-konfigurációk használatával áttelepítés indítása|
|/ kezelők/eszközök/migrationSourceConfigurations /<br>confirmMigration/művelet|Megerősíti, hogy a sikeres áttelepítéshez és véglegesítheti.|
|/ kezelők/eszközök/migrationSourceConfigurations /<br>fetchMigrationEstimate/művelet|Az áttelepítési becslés feladat állapotának beolvasása.|
|/ kezelők/eszközök/migrationSourceConfigurations /<br>fetchMigrationStatus/művelet|Az áttelepítés állapotának beolvasása.|
|/ kezelők/eszközök/migrationSourceConfigurations /<br>fetchConfirmMigrationStatus/művelet|Áttelepítési megerősítése állapotának beolvasása.|
|/managers/Devices/alertSettings/Read|Sorolja fel, vagy a riasztási beállításainak beolvasása|
|/managers/Devices/alertSettings/Write|A riasztási beállításainak létrehozása vagy frissítése|
|/managers/Devices/networkSettings/Read|Sorolja fel, vagy a hálózati beállításainak beolvasása|
|/managers/Devices/networkSettings/Write|Létrehoz egy új, vagy a hálózati beállítások frissítése|
|/managers/Devices/Jobs/Read|Sorolja fel, vagy a feladat beolvasása|
|/managers/Devices/Jobs/Cancel/Action|Egy futó feladat megszakítása|
|/managers/Devices/metricsDefinitions/Read|Sorolja fel, vagy a metrikák meghatározások beolvasása|
|/managers/Devices/volumeContainers/Write|Létrehoz egy új vagy frissít Kötettárolók (8000 sorozat csak)|
|/managers/Devices/volumeContainers/Read|Kötettárolók listázása (csak 8000 sorozat)|
|/managers/Devices/volumeContainers/DELETE|Törli a meglévő Kötettárolók (8000 sorozat csak)|
|/managers/Devices/volumeContainers/listEncryptionKeys/Action|Lista titkosítási kulcsokat a Kötettárolók|
|/managers/Devices/volumeContainers/rolloverEncryptionKey/Action|Kötettárolók a helyettesítő titkosítási kulcsok|
|/managers/Devices/volumeContainers/Metrics/Read|A metrikák felsorolása|
|/managers/Devices/volumeContainers/Volumes/Read|A kötetek felsorolása|
|/managers/Devices/volumeContainers/Volumes/Write|Létrehoz egy új vagy frissít kötetek|
|/managers/Devices/volumeContainers/Volumes/DELETE|Egy meglévő kötetek törlése|
|/managers/Devices/volumeContainers/Volumes/Metrics/Read|A metrikák felsorolása|
|/managers/Devices/volumeContainers/Volumes/metricsDefinitions/Read|A metrikák definíciók felsorolása|
|/managers/Devices/volumeContainers/metricsDefinitions/Read|A metrikák definíciók felsorolása|
|/managers/Devices/iscsiservers/Read|Sorolja fel, vagy az iSCSI kiszolgálók beolvasása|
|/managers/Devices/iscsiservers/Write|Létrehozni vagy frissíteni az iSCSI-kiszolgálók|
|/managers/Devices/iscsiservers/DELETE|Törli az iSCSI-kiszolgálók|
|/managers/Devices/iscsiservers/Backup/Action|Az iSCSI-kiszolgálók biztonsági másolatok készítéséhez.|
|/managers/Devices/iscsiservers/Metrics/Read|Sorolja fel, vagy a metrikák beolvasása|
|/managers/Devices/iscsiservers/Disks/Read|Lekérdezi a lemezek vagy listája|
|/managers/Devices/iscsiservers/Disks/Write|Létrehozni vagy frissíteni a lemezek|
|/managers/Devices/iscsiservers/Disks/DELETE|A lemezek törlése|
|/managers/Devices/iscsiservers/Disks/Metrics/Read|Sorolja fel, vagy a metrikák beolvasása|
|/managers/Devices/iscsiservers/Disks/metricsDefinitions/Read|Sorolja fel, vagy a metrikák meghatározások beolvasása|
|/managers/Devices/iscsiservers/metricsDefinitions/Read|Sorolja fel, vagy a metrikák meghatározások beolvasása|
|/managers/Devices/backups/Read|Lekérdezi a biztonságimásolat-készletet vagy listája|
|/managers/Devices/backups/DELETE|A biztonságimásolat-készlet törlése|
|/managers/Devices/backups/restore/Action|Állítsa vissza az összes kötet a biztonságimásolat-készletből.|
|/managers/Devices/backups/Elements/Clone/Action|Klónozni egy fájlmegosztás vagy kötet egy biztonsági mentési elem használatával.|
|/managers/Devices/backupPolicies/Write|Létrehoz egy új, vagy frissíti a biztonsági mentési házirendek (8000 sorozat csak)|
|/managers/Devices/backupPolicies/Read|A lista a biztonsági mentési házirendek (csak 8000 sorozat)|
|/managers/Devices/backupPolicies/DELETE|Törli a meglévő biztonsági mentési házirendek (8000 sorozat csak)|
|/managers/Devices/backupPolicies/Backup/Action|Igény szerinti létrehozásához manuális biztonsági mentés készítése a házirend által védett összes kötet biztonsági mentését.|
|/managers/Devices/backupPolicies/schedules/Write|Létrehoz egy új, vagy az ütemezések frissítése|
|/managers/Devices/backupPolicies/schedules/Read|Az ütemezések felsorolása|
|/managers/Devices/backupPolicies/schedules/DELETE|Egy meglévő ütemezés törlése|
|/managers/Devices/securitySettings/Update/Action|A biztonsági beállítások frissítése.|
|/managers/Devices/securitySettings/Read|A biztonsági beállítások|
|/ kezelők/eszközök/securitySettings /<br>syncRemoteManagementCertificate/művelet|A távoli felügyeleti tanúsítvány eszköz szinkronizálása.|
|/managers/Devices/securitySettings/Write|Létrehoz egy új, vagy a biztonsági beállítások frissítése|
|/managers/Devices/fileservers/Read|Lekérdezi a fájlkiszolgálók vagy listája|
|/managers/Devices/fileservers/Write|Létrehozni vagy frissíteni a fájlkiszolgálókon|
|/managers/Devices/fileservers/DELETE|Törli a fájlkiszolgálókon|
|/managers/Devices/fileservers/Backup/Action|Egy fájlkiszolgáló biztonsági másolatok készítéséhez.|
|/managers/Devices/fileservers/Metrics/Read|Sorolja fel, vagy a metrikák beolvasása|
|/managers/Devices/fileservers/shares/Write|Létrehozni vagy frissíteni a megosztásokat|
|/managers/Devices/fileservers/shares/Read|Lekérdezi a megosztásokat vagy listája|
|/managers/Devices/fileservers/shares/DELETE|Törli a megosztások|
|/managers/Devices/fileservers/shares/Metrics/Read|Sorolja fel, vagy a metrikák beolvasása|
|/managers/Devices/fileservers/shares/metricsDefinitions/Read|Sorolja fel, vagy a metrikák meghatározások beolvasása|
|/managers/Devices/fileservers/metricsDefinitions/Read|Sorolja fel, vagy a metrikák meghatározások beolvasása|
|/managers/Devices/timeSettings/Read|Sorolja fel, vagy a idő beállításainak beolvasása|
|/managers/Devices/timeSettings/Write|Létrehoz egy új, vagy frissíti az idő beállítása|
|Kezelői/tanúsítvány/írása|A frissítés erőforrás tanúsítvány művelet frissíti az erőforrás/tárolói hitelesítő adatainak tanúsítványa.|
|/managers/cloudApplianceConfigurations/Read|A felhő készülék lista támogatott konfigurációk|
|/managers/metricsDefinitions/Read|Sorolja fel, vagy a metrikák meghatározások beolvasása|
|/managers/encryptionSettings/Read|Sorolja fel, vagy a titkosítási beállításainak beolvasása|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Művelet | Leírás |
|---|---|
|/streamingjobs/Start/Action|A Stream Analytics-feladat indítása|
|/streamingjobs/STOP/Action|A Stream Analytics-feladat leállítása|
|/ streamingjobs/Olvasás|Olvasási Stream Analytics-feladat|
|/ streamingjobs/írása|Írás a Stream Analytics-feladat|
|/ streamingjobs/törlése|A Stream Analytics-feladat törlése|
|/streamingjobs/Providers/Microsoft.Insights/metricDefinitions/Read|A streamingjobs elérhető metrikai meghatározások beolvasása|
|/streamingjobs/Providers/Microsoft.Insights/diagnosticSettings/Read|Olvassa el a diagnosztikai beállítást.|
|/streamingjobs/Providers/Microsoft.Insights/diagnosticSettings/Write|Diagnosztikai beállításának írási.|
|/streamingjobs/Providers/Microsoft.Insights/logDefinitions/Read|Streamingjobs naplók beolvasása.|
|/streamingjobs/Transformations/Read|Olvasási Stream Analytics feladat átalakítása|
|/streamingjobs/Transformations/Write|Írás a Stream Analytics feladat átalakítása|
|/streamingjobs/Transformations/DELETE|Törölje a Stream Analytics feladat átalakítása|
|/streamingjobs/Inputs/Read|Olvasási Stream Analytics feladat bemeneti|
|/streamingjobs/Inputs/Write|Írás Analytics-feladat adatfolyam-bemenet|
|/streamingjobs/Inputs/DELETE|Stream Analytics feladat bemeneti törlése|
|/streamingjobs/outputs/Read|Olvasási Stream Analytics Feladatkiemenetét|
|/streamingjobs/outputs/Write|Írás Analytics-feladat kimeneti adatfolyam|
|/streamingjobs/outputs/DELETE|Stream Analytics-feladat kimeneti törlése|

## <a name="microsoftsupport"></a>A Microsoft.Support

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|Regisztrálás a támogatási erőforrás-szolgáltatóra|
|/supportTickets/Read|Egy támogatási jegy adatainak beolvasása (beleértve az állapotot, a súlyosságot, a kapcsolattartási adatokat és a kommunikációs elemeket), vagy az előfizetésekhez tartozó támogatási jegyek listájának beolvasása.|
|/ supportTickets/írása|Létrehozza vagy frissíti egy támogatási jegy. Létrehozhat egy támogatási jegy műszaki, számlázási, kvóták és előfizetés-kezeléssel kapcsolatos problémákat. Súlyossága, kapcsolattartási adatai és a meglévő támogatási jegyek kommunikációs frissítheti.|

## <a name="microsoftweb"></a>Microsoft.Web

| Művelet | Leírás |
|---|---|
|/ unregister/művelet|Az előfizetés Microsoft.Web erőforrás-szolgáltató regisztrációját.|
|/ ellenőrzése/művelet|Ellenőrzése.|
|/ regisztrációs/művelet|Az előfizetés Microsoft.Web erőforrás-szolgáltató regisztrálása.|
|/ hostingEnvironments/Olvasás|Az App Service-környezetek tulajdonságainak beolvasása|
|/ hostingEnvironments/írása|Egy új App Service Environment-környezet létrehozása vagy meglévő ütemezés frissítése|
|/ hostingEnvironments/törlése|App Service-környezet törlése|
|/hostingEnvironments/reboot/Action|Indítsa újra a egy App Service-környezetben lévő összes gépen|
|/hostingenvironments/Resume/Action|Végezze el újra üzemeltetési környezetekben.|
|/hostingenvironments/suspend/Action|Felfüggesztheti üzemeltetési környezetekben.|
|/hostingenvironments/metricdefinitions/Read|Az üzemeltetési környezetek metrikai meghatározásainak beolvasása.|
|/hostingEnvironments/workerPools/Read|A Feldolgozókészleten egy App Service Environment-környezetben tulajdonságainak beolvasása|
|/hostingEnvironments/workerPools/Write|Hozzon létre egy új Feldolgozókészletek egy App Service Environment-környezetben, vagy egy meglévő frissítése|
|/hostingenvironments/workerpools/metricdefinitions/Read|Első üzemeltetési környezetekben Workerpools metrika definíciókat.|
|/hostingenvironments/workerpools/Metrics/Read|Első üzemeltetési környezetekben Workerpools metrikákat.|
|/hostingenvironments/workerpools/skus/Read|Első üzemeltetési környezetekben Workerpools SKU.|
|/hostingenvironments/workerpools/usages/Read|Első üzemeltetési környezetekben Workerpools módjait.|
|/hostingenvironments/Sites/Read|Első környezetek webalkalmazások üzemeltetéséhez.|
|/hostingenvironments/serverfarms/Read|Első üzemeltetési környezetekben App Service-csomagokról.|
|/hostingenvironments/usages/Read|Első üzemeltetési környezetekben is érvényesek.|
|/hostingenvironments/capacities/Read|Az üzemeltetési környezetek kapacitások beolvasása.|
|/hostingenvironments/Operations/Read|Első üzemeltetési környezetekben műveletek.|
|/hostingEnvironments/multiRolePools/Read|Egy App Service Environment-környezetben egy előtér-címkészlet tulajdonságainak beolvasása|
|/hostingEnvironments/multiRolePools/Write|Előtér-készlet létrehozása az App Service-környezetben, vagy egy meglévő frissítése|
|/hostingenvironments/multirolepools/metricdefinitions/Read|Az üzemeltetési környezetek többcélú készletek metrikai meghatározásainak beolvasása.|
|/hostingenvironments/multirolepools/Metrics/Read|Első üzemeltetési környezetekben többcélú készletek metrikákat.|
|/hostingenvironments/multirolepools/skus/Read|Első üzemeltetési környezetekben többcélú készletek SKU.|
|/hostingenvironments/multirolepools/usages/Read|Első üzemeltetési környezetekben többcélú készletek is érvényesek.|
|/hostingenvironments/Diagnostics/Read|Első üzemeltetési környezetekben diagnosztika.|
|/publishingusers/Read|Első közzététel számára.|
|/ publishingusers/írása|A frissítés közzététele a felhasználók.|
|/checknameavailability/Read|Ellenőrizze, hogy az erőforrásnév érhető el.|
|/ geoRegions/Olvasás|A földrajzi régiók között listájának lekérdezése.|
|/ helyek/Olvasás|A webes alkalmazás tulajdonságainak beolvasása|
|/ helyek/írása|Új webalkalmazás létrehozása vagy meglévő pillanatkép frissítése|
|/ helyek/törlése|Egy már meglévő webalkalmazás törlése|
|/Sites/Backup/Action|Hozzon létre egy új webes alkalmazás biztonsági mentése|
|/Sites/publishxml/Action|Közzétételi profil xml egy webalkalmazást az beszerzése|
|/Sites/publish/Action|A webes alkalmazás közzététele|
|/Sites/restart/Action|A webalkalmazás újraindítása|
|/Sites/Start/Action|A webalkalmazás elindítása|
|/Sites/STOP/Action|A webalkalmazás leállítása|
|/Sites/slotsswap/Action|Felcserélni a webalkalmazás üzembe helyezési|
|/Sites/slotsdiffs/Action|Konfiguráció web app és az üzembe helyezési ponti közötti különbségek beolvasása|
|/Sites/applySlotConfig/Action|Webes alkalmazás tárolóhely konfigurációt cél tárolóhelyről az aktuális webes alkalmazás|
|/Sites/resetSlotConfig/Action|Alaphelyzetbe állítja a webes alkalmazás konfigurálása|
|/Sites/Functions/Action|Webalkalmazások funkciók.|
|/Sites/listsyncfunctiontriggerstatus/Action|Lista szinkronizálási függvény eseményindító állapot webalkalmazások.|
|/Sites/networktrace/Action|Hálózati nyomkövetés webalkalmazások.|
|/Sites/newpassword/Action|ÚjJelszó webalkalmazások.|
|/Sites/Sync/Action|Szinkronizálási webalkalmazások.|
|/Sites/operationresults/Read|Web Apps művelet eredményt.|
|/Sites/webjobs/Read|Web Apps webjobs-feladatok beolvasása.|
|/ helyek/biztonsági mentési/olvasási|Web Apps biztonsági mentés beolvasása.|
|/Sites/Backup/Write|Web Apps másolat frissítéséhez.|
|/Sites/metricdefinitions/Read|Web Apps metrika meghatározások beolvasása.|
|/Sites/Metrics/Read|Web Apps metrikákat kaphat.|
|/Sites/continuouswebjobs/DELETE|Webes alkalmazások folyamatos webes feladatok törlése.|
|/Sites/continuouswebjobs/Read|Webes alkalmazások folyamatos webes feladatok beolvasása.|
|/Sites/continuouswebjobs/Start/Action|Indítsa el a webes alkalmazások folyamatos webes feladatok.|
|/Sites/continuouswebjobs/STOP/Action|Állítsa le a webes alkalmazások folyamatos webes feladatok.|
|/Sites/domainownershipidentifiers/Read|Megkapja a Web Apps tartományi tulajdonjoga azonosítókat.|
|/Sites/domainownershipidentifiers/Write|Frissítse a Web Apps tartományi tulajdonjoga azonosítók.|
|/Sites/premieraddons/DELETE|Törölje a Web Apps Premier bővítményei.|
|/Sites/premieraddons/Read|Web Apps Premier bővítményei beolvasása.|
|/Sites/premieraddons/Write|Frissítse a Web Apps Premier bővítményei.|
|/Sites/triggeredwebjobs/DELETE|Web Apps indított webjobs-feladatok törlése.|
|/Sites/triggeredwebjobs/Read|Web Apps indított webjobs-feladatok beolvasása.|
|/Sites/triggeredwebjobs/Run/Action|Web Apps indított webjobs-feladatok futtatásához.|
|/Sites/hostnamebindings/DELETE|Web Apps Állomásnévkötéseket törlése.|
|/Sites/hostnamebindings/Read|Web Apps Állomásnévkötéseket beolvasása.|
|/Sites/hostnamebindings/Write|Frissítse a Web Apps Állomásnévkötéseket.|
|/Sites/virtualnetworkconnections/DELETE|Törölje a Web Apps virtuális hálózati kapcsolatokat.|
|/Sites/virtualnetworkconnections/Read|Lekérni a Web Apps virtuális hálózati kapcsolatokat.|
|/Sites/virtualnetworkconnections/Write|Frissítse a Web Apps virtuális hálózati kapcsolatokat.|
|/Sites/virtualnetworkconnections/Gateways/Read|Web Apps virtuális hálózati kapcsolatok átjárók beolvasása.|
|/Sites/virtualnetworkconnections/Gateways/Write|Frissítse a Web Apps virtuális hálózati kapcsolatok átjárókat.|
|/Sites/publishxml/Read|Kérhető le a webes közzétételi XML.|
|/Sites/hybridconnectionrelays/Read|Web Apps hibrid kapcsolat továbbítók beolvasása.|
|/Sites/perfcounters/Read|Web Apps teljesítményszámlálók beolvasása.|
|/Sites/usages/Read|Web Apps módjait beolvasása.|
|/Sites/slots/Write|Webes alkalmazás új tárhely létrehozása vagy meglévő pillanatkép frissítése|
|/Sites/slots/DELETE|Törölje a meglévő Web App tárhelyek|
|/Sites/slots/Backup/Action|Hozzon létre új webalkalmazás biztonsági mentése.|
|/Sites/slots/publishxml/Action|Profil xml közzétételt a webalkalmazás beolvasása|
|/Sites/slots/publish/Action|Egy webes tárolóhelye közzététele|
|/Sites/slots/restart/Action|Indítsa újra a webes alkalmazás tárhely|
|/Sites/slots/Start/Action|Indítsa el a webes alkalmazás tárhely|
|/Sites/slots/STOP/Action|Állítsa le a webes alkalmazás tárhely|
|/Sites/slots/slotsswap/Action|Felcserélni a webalkalmazás üzembe helyezési|
|/Sites/slots/slotsdiffs/Action|Konfiguráció web app és az üzembe helyezési ponti közötti különbségek beolvasása|
|/Sites/slots/applySlotConfig/Action|Webes alkalmazás tárolóhely konfiguráció cél tárolóhelyről az aktuális tárolóhelyre alkalmazásához.|
|/Sites/slots/resetSlotConfig/Action|Webes alkalmazás helyezési pont konfigurációjának visszaállítása|
|/Sites/slots/Read|A webalkalmazás üzembe helyezési pont tulajdonságainak beolvasása|
|/Sites/slots/newpassword/Action|ÚjJelszó Web Apps tárolóhelye.|
|/Sites/slots/Sync/Action|Szinkronizálási Web Apps tárolóhelye.|
|/Sites/slots/operationresults/Read|Webes alkalmazások üzembe helyezési ponti művelet eredményekhez juthat.|
|/Sites/slots/webjobs/Read|Webes alkalmazások üzembe helyezési ponti webjobs-feladatok beolvasása.|
|/Sites/slots/Backup/Write|Webes alkalmazások üzembe helyezési ponti másolat frissítéséhez.|
|/Sites/slots/metricdefinitions/Read|Webes alkalmazások üzembe helyezési ponti metrikai meghatározásainak beolvasása.|
|/Sites/slots/Metrics/Read|Webes alkalmazások üzembe helyezési ponti metrikákat kaphat.|
|/Sites/slots/continuouswebjobs/DELETE|Webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok törlése.|
|/Sites/slots/continuouswebjobs/Read|Webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok beolvasása.|
|/Sites/slots/continuouswebjobs/Start/Action|Indítsa el a webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok.|
|/Sites/slots/continuouswebjobs/STOP/Action|Állítsa le a webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok.|
|/Sites/slots/premieraddons/DELETE|Webes alkalmazások üzembe helyezési ponti Premier bővítményei törlése.|
|/Sites/slots/premieraddons/Read|Webes alkalmazások üzembe helyezési ponti Premier bővítményei beolvasása.|
|/Sites/slots/premieraddons/Write|Frissítse a webes alkalmazások üzembe helyezési ponti Premier bővítményei.|
|/Sites/slots/triggeredwebjobs/DELETE|Webes alkalmazások üzembe helyezési ponti indított webjobs-feladatok törlése.|
|/Sites/slots/triggeredwebjobs/Read|Webes alkalmazások üzembe helyezési ponti indított webjobs-feladatok beolvasása.|
|/Sites/slots/triggeredwebjobs/Run/Action|Webes alkalmazások üzembe helyezési ponti indított webjobs-feladatok futtatásához.|
|/Sites/slots/hostnamebindings/DELETE|Webes alkalmazások üzembe helyezési ponti Állomásnévkötéseket törlése.|
|/Sites/slots/hostnamebindings/Read|Webes alkalmazások üzembe helyezési ponti Állomásnévkötéseket beolvasása.|
|/Sites/slots/hostnamebindings/Write|Webes alkalmazások üzembe helyezési ponti Állomásnévkötéseket frissítése.|
|/Sites/slots/phplogging/Read|Webes alkalmazások üzembe helyezési ponti Phplogging beolvasása.|
|/Sites/slots/virtualnetworkconnections/DELETE|Törölje a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatokat.|
|/Sites/slots/virtualnetworkconnections/Read|Lekérni a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatokat.|
|/Sites/slots/virtualnetworkconnections/Write|Frissítse a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatokat.|
|/Sites/slots/virtualnetworkconnections/Gateways/Write|Frissítse a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatok átjárókat.|
|/Sites/slots/usages/Read|Webes alkalmazások üzembe helyezési ponti módjait beolvasása.|
|/Sites/slots/hybridconnection/DELETE|Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat törlése.|
|/Sites/slots/hybridconnection/Read|Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat lekérdezése.|
|/Sites/slots/hybridconnection/Write|Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat frissítése.|
|/Sites/slots/config/Read|Webes alkalmazás a tárhely konfigurációs beállításainak beolvasása|
|/Sites/slots/config/List/Action|Webes alkalmazás a tárhely biztonsági bizalmas beállítások, például a hitelesítő adatokat, az alkalmazásbeállítások és az kapcsolati karakterláncok|
|/Sites/slots/config/Write|Webes alkalmazás a tárhely konfigurációs beállításainak frissítése|
|/Sites/slots/config/DELETE|Webes alkalmazások üzembe helyezési ponti konfiguráció törlése.|
|/Sites/slots/Instances/Read|Webes alkalmazások üzembe helyezési ponti-példányokat beszerezni.|
|/Sites/slots/Instances/Processes/Read|Webes alkalmazások üzembe helyezési ponti példányok folyamatok beolvasása.|
|/Sites/slots/Instances/Deployments/Read|Webes alkalmazások üzembe helyezési ponti példányok központi telepítések beolvasása.|
|/Sites/slots/sourcecontrols/Read|Webes alkalmazás a tárhely verziókezelő konfigurációs beállításainak beolvasása|
|/Sites/slots/sourcecontrols/Write|Webes alkalmazás a tárhely forrás a konfigurációs beállítások frissítése|
|/Sites/slots/sourcecontrols/DELETE|Webes alkalmazás a tárhely forrás a konfigurációs beállítások törlése|
|/Sites/slots/restore/Read|Webes alkalmazások üzembe helyezési ponti visszaállítási beolvasása.|
|/Sites/slots/analyzecustomhostname/Read|Első webes alkalmazások üzembe helyezési ponti elemezheti az egyéni állomásnevet.|
|/Sites/slots/backups/Read|A webalkalmazás tárolóhelyei biztonsági másolat tulajdonságainak beolvasása|
|/Sites/slots/backups/List/Action|Lista webes alkalmazások üzembe helyezési ponti biztonsági mentéseket.|
|/Sites/slots/backups/restore/Action|Állítsa vissza a webes alkalmazások üzembe helyezési ponti biztonsági mentéseket.|
|/Sites/slots/Deployments/DELETE|Törölje a webes alkalmazások üzembe helyezési ponti központi telepítések.|
|/Sites/slots/Deployments/Read|Webes alkalmazások üzembe helyezési ponti központi telepítések beolvasása.|
|/Sites/slots/Deployments/Write|Webes alkalmazások üzembe helyezési ponti központi telepítések frissítése.|
|/Sites/slots/Deployments/log/Read|Webes alkalmazások üzembe helyezési ponti központi telepítések napló beolvasása.|
|/Sites/hybridconnection/DELETE|Web Apps hibrid kapcsolat törlése.|
|/Sites/hybridconnection/Read|Web Apps a hibrid kapcsolat beolvasása.|
|/Sites/hybridconnection/Write|Web Apps hibrid kapcsolat frissítése.|
|/Sites/recommendationhistory/Read|Webes alkalmazások javaslat előzmények beolvasása.|
|/Sites/recommendations/Read|A webalkalmazás javaslatok listája az beszerzése.|
|/Sites/recommendations/disable/Action|Tiltsa le a Web Apps javaslatokat.|
|/Sites/config/Read|A webalkalmazás konfigurációs beállításainak beolvasása|
|/Sites/config/List/Action|Webes alkalmazás biztonsági bizalmas beállítások, például a hitelesítő adatokat, az alkalmazásbeállítások és az kapcsolati karakterláncok|
|/Sites/config/Write|Webes alkalmazás konfigurációs beállításainak frissítése|
|/Sites/config/DELETE|Webkonfiguráció alkalmazások törlése.|
|/Sites/Instances/Read|Web Apps-példányokat beszerezni.|
|/Sites/Instances/Processes/DELETE|Törölje a Web Apps példányok folyamatokat.|
|/Sites/Instances/Processes/Read|Web Apps példányok folyamatok beolvasása.|
|/Sites/Instances/Deployments/Read|Web Apps példányok központi telepítések beolvasása.|
|/Sites/sourcecontrols/Read|Webalkalmazás verziókövetésének konfigurációs beállításainak beolvasása|
|/Sites/sourcecontrols/Write|Webes alkalmazás forrás a konfigurációs beállítások frissítése|
|/Sites/sourcecontrols/DELETE|Webes alkalmazás forrás a konfigurációs beállítások törlése|
|/Sites/restore/Read|Web Apps visszaállítási beolvasása.|
|/Sites/analyzecustomhostname/Read|Elemezze az egyéni állomásnevet.|
|/Sites/backups/Read|A webalkalmazás biztonsági mentése tulajdonságainak beolvasása|
|/Sites/backups/List/Action|Lista Web Apps biztonsági mentéseket.|
|/Sites/backups/restore/Action|Állítsa vissza a Web Apps biztonsági mentéseket.|
|/Sites/snapshots/Read|Web Apps pillanatképek beolvasása.|
|/Sites/Functions/DELETE|Törölje a Web Apps funkciók.|
|/Sites/Functions/listsecrets/Action|Lista titkok Web Apps funkciók.|
|/Sites/Functions/Read|Web Apps funkciók beolvasása.|
|/Sites/Functions/Write|Frissítse a Web Apps funkciók.|
|/Sites/Deployments/DELETE|Webes alkalmazások központi telepítéseit törli.|
|/Sites/Deployments/Read|Webes alkalmazások központi telepítésének beolvasása.|
|/Sites/Deployments/Write|Webes alkalmazások központi telepítések frissítése.|
|/Sites/Deployments/log/Read|Webes alkalmazások központi telepítésének napló beolvasása.|
|/Sites/Diagnostics/Read|Web Apps diagnosztika beolvasása.|
|/Sites/Diagnostics/workerprocessrecycle/Read|Webes alkalmazások diagnosztika munkavégző folyamat újrahasznosítást beolvasása.|
|/Sites/Diagnostics/workeravailability/Read|Web Apps diagnosztika Workeravailability beolvasása.|
|/Sites/Diagnostics/runtimeavailability/Read|Web Apps diagnosztika futásidejű rendelkezésre beolvasása.|
|/Sites/Diagnostics/cpuanalysis/Read|Web Apps diagnosztika Cpuanalysis beolvasása.|
|/Sites/Diagnostics/servicehealth/Read|Webes alkalmazások diagnosztika szolgáltatás állapotának beolvasása.|
|/Sites/Diagnostics/frebanalysis/Read|Web Apps diagnosztika FREB elemzés beolvasása.|
|/availablestacks/Read|Rendelkezésre álló verem beolvasása.|
|/isusernameavailable/Read|Ellenőrizze, hogy a felhasználónév érhető el.|
|/Microsoft.Web/apiManagementAccounts/<br>API-k olvasása|Szerezze be az API-k a listát.|
|/Microsoft.Web/apiManagementAccounts/<br>API-k/írása|Adja hozzá egy új API-t, vagy frissítse a meglévőt.|
|/Microsoft.Web/apiManagementAccounts/<br>API-k vagy törlése|Törölje a meglévő Api.|
|/Microsoft.Web/apiManagementAccounts/<br>Olvasási idő API-k/kapcsolatok|Kapcsolatok listájának beolvasása.|
|/Microsoft.Web/apiManagementAccounts/<br>API-k/kapcsolatok/írása|Mentse az új kapcsolat, vagy frissítsen egy meglévőt.|
|/Microsoft.Web/apiManagementAccounts/<br>API-k vagy kapcsolatok/törlése|Törölje a meglévő kapcsolat.|
|/Microsoft.Web/apiManagementAccounts/<br>API-k/kapcsolatok/connectionAcls/Olvasás|Olvassa el a ConnectionAcls|
|/Microsoft.Web/apiManagementAccounts/<br>API-k/kapcsolatok/connectionAcls/írása|Hozzáadásakor vagy módosításakor ConnectionAcl|
|/Microsoft.Web/apiManagementAccounts/<br>API-k vagy kapcsolatok/connectionAcls/törlése|ConnectionAcl törlése|
|/Microsoft.Web/apiManagementAccounts/<br>API-k/connectionAcls/Olvasás|Olvassa el a ConnectionAcls API-hoz.|
|/Microsoft.Web/apiManagementAccounts/<br>API-k/apiAcls/Olvasás|Olvassa el a ConnectionAcls|
|/Microsoft.Web/apiManagementAccounts/<br>API-k/apiAcls/írása|Létrehozni vagy frissíteni az Api ACL-EK|
|/Microsoft.Web/apiManagementAccounts/<br>API-k vagy apiAcls/törlése|Hozzáférés-vezérlési listák Api törlése|
|/ serverfarms/Olvasás|Az egy App Service-csomag tulajdonságainak beolvasása|
|/ serverfarms/írása|Egy új App Service-csomag létrehozása vagy meglévő pillanatkép frissítése|
|/ serverfarms/törlése|Egy meglévő App Service-csomag törlése|
|/serverfarms/restartSites/Action|Indítsa újra az összes webes alkalmazás az App Service-csomag|
|/serverfarms/operationresults/Read|Az alkalmazásszolgáltatási csomagok művelet eredményt.|
|/serverfarms/Capabilities/Read|Az alkalmazásszolgáltatási csomagok lehetőségek elérése.|
|/serverfarms/metricdefinitions/Read|Az alkalmazásszolgáltatási csomagok metrikai meghatározásainak beolvasása.|
|/serverfarms/Metrics/Read|Az alkalmazásszolgáltatási csomagok metrikákat kaphat.|
|/serverfarms/hybridconnectionplanlimits/Read|Az alkalmazásszolgáltatási csomagok hibrid kapcsolat terv korlátok beolvasása.|
|/serverfarms/virtualnetworkconnections/Read|Az alkalmazásszolgáltatási csomagok virtuális hálózati kapcsolatok lekérése.|
|/serverfarms/virtualnetworkconnections/routes/DELETE|Az App Service csomagokban virtuális hálózati kapcsolatok útvonalak törlése.|
|/serverfarms/virtualnetworkconnections/routes/Read|Az App Service csomagokban virtuális hálózati kapcsolatok útvonalak beolvasása.|
|/serverfarms/virtualnetworkconnections/routes/Write|Az alkalmazásszolgáltatási csomagok virtuális hálózati kapcsolatok az útvonalak frissítése.|
|/serverfarms/virtualnetworkconnections/Gateways/Write|Az App Service csomagokban virtuális hálózati kapcsolatok átjárók frissítése.|
|/serverfarms/firstpartyapps/Settings/DELETE|Törli az App Service csomagokban első entitás alkalmazásokra vonatkozó beállítások.|
|/serverfarms/firstpartyapps/Settings/Read|Az alkalmazásszolgáltatási csomagok első entitás alkalmazások beállításainak beolvasása.|
|/serverfarms/firstpartyapps/Settings/Write|Frissítés App Service-csomagok első entitás alkalmazásokra vonatkozó beállítások.|
|/serverfarms/Sites/Read|Az App Service csomagokban webes alkalmazások beszerzéséhez.|
|/serverfarms/workers/reboot/Action|Indítsa újra az App Service csomagokban munkavállalók.|
|/serverfarms/hybridconnectionrelays/Read|Az alkalmazásszolgáltatási csomagok hibrid kapcsolat továbbítók beolvasása.|
|/serverfarms/skus/Read|Az alkalmazásszolgáltatási csomagok termékváltozatok beolvasása.|
|/serverfarms/usages/Read|Az alkalmazásszolgáltatási csomagok módjait beolvasása.|
|/serverfarms/hybridconnectionnamespaces/relays/Sites/Read|Az App Service csomagokban hibrid kapcsolat névterek továbbítók webes alkalmazások beszerzéséhez.|
|/ishostnameavailable/Read|Ellenőrizze, hogy az állomásnév érhető el.|
|/ connectionGateways/Olvasás|A kapcsolat átjárók listájának lekérdezése.|
|/ connectionGateways/írása|Létrehozza vagy frissíti a kapcsolat átjáró.|
|/ connectionGateways/törlése|Egy kapcsolat átjáró törlése.|
|/connectionGateways/JOIN/Action|Egy kapcsolat átjáró csatlakozik.|
|/classicmobileservices/Read|Klasszikus mobilszolgáltatások beolvasása.|
|/skus/Read|SKU beolvasása.|
|/ tanúsítvány/Olvasás|A tanúsítványok listájának lekérdezése.|
|/ tanúsítvány/írása|Adja hozzá az új tanúsítványt, vagy frissítsen egy meglévőt.|
|/ tanúsítvány/törlése|Törölje a meglévő tanúsítványt.|
|/Operations/Read|Műveletek beolvasása.|
|/ javaslatok/Olvasás|Az előfizetések javaslatok listájának.|
|/ishostingenvironmentnameavailable/Read|GET, ha üzemeltetési környezet neve érhető el.|
|/ apiManagementAccounts/Olvasás|A ApiManagementAccounts listájának lekérdezése.|
|/ apiManagementAccounts/írása|Adja hozzá egy új ApiManagementAccount vagy egy meglévő frissítése|
|/ apiManagementAccounts/törlése|Egy meglévő ApiManagementAccount törlése|
|/apiManagementAccounts/connectionAcls/Read|A kapcsolat ACL listájának lekérdezése.|
|/apiManagementAccounts/apiAcls/Read|Olvassa el a ConnectionAcls|
|/ kapcsolatok/Olvasás|Kapcsolatok listájának beolvasása.|
|/ kapcsolatok/írása|Létrehozza vagy frissíti a kapcsolatot.|
|/ kapcsolatok/törlése|Törli a kapcsolatot.|
|/Connections/JOIN/Action|A kapcsolat csatlakozik.|
|/Connections/confirmconsentcode/Action|Kapcsolatok hozzájárulási kód megerősítése.|
|/Connections/listconsentlinks/Action|Hozzájárulás hivatkozások kapcsolatokhoz.|
|/deploymentlocations/Read|Beolvasni a központi telepítési helyét.|
|/sourcecontrols/Read|Adatforrás-vezérlők beolvasása.|
|/ sourcecontrols/írása|Frissítse az adatforrás-vezérlők.|
|/managedhostingenvironments/Read|A felügyelt beolvasása üzemeltetési környezetekben.|
|/managedhostingenvironments/Sites/Read|Első felügyelt környezetek webalkalmazások üzemeltetéséhez.|
|/managedhostingenvironments/serverfarms/Read|A felügyelt beolvasása üzemeltetési környezetekben App Service-csomagokról.|
|/Locations/managedapis/Read|Helyek felügyelt API-k beolvasása.|
|/Locations/apioperations/Read|Helyek API műveleteinek beolvasása.|
|/Locations/connectiongatewayinstallations/Read|Helyek kapcsolat átjáró telepítések beolvasása.|
|/ listSitesAssignedToHostName/Olvasás|Lekérése az állomásnév rendelt helyek nevét.|

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [hozzon létre egy egyéni biztonsági szerepkört](role-based-access-control-custom-roles.md).
- Tekintse át a [beépített RBAC-szerepkörök](role-based-access-built-in-roles.md).
- Hozzáférés-hozzárendelések kezelése [felhasználó](role-based-access-control-manage-assignments.md) vagy [erőforrás](role-based-access-control-configure.md) 
- Megtudhatja, hogyan [tevékenységi naplóit rendszervizsgálati műveleteket az egyes erőforrások megtekintése](~/articles/azure-resource-manager/resource-group-audit.md)
