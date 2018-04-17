---
title: Az Azure erőforrás-kezelő szolgáltató műveletek |} Microsoft Docs
description: A Microsoft Azure Resource Manager erőforrás-szolgáltató elérhető műveleteinek részletei
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.openlocfilehash: 80724a24fe7cf2565334a5212a0877589eb1eecf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Az Azure Resource Manager erőforrás-szolgáltató üzemeltetése

Ez a dokumentum minden Microsoft Azure Resource Manager erőforrás-szolgáltató elérhető műveleteinek listázása. Ezek segítségével az egyéni szerepkörök az Azure-erőforrások részletes szerepköralapú hozzáférés-vezérlést (RBAC) engedélyeket biztosítanak. Ne feledje, ez nem az átfogó listáját, és műveletek előfordulhat, hogy kell hozzáadni vagy eltávolítani, mert egyes szolgáltatók frissül. Művelet karakterláncok kövesse formátuma `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Az átfogó és aktuális listáját használja a `Get-AzureRmProviderOperation` (a PowerShell) vagy `az provider operation list` (az Azure CLI v2) az Azure erőforrás-szolgáltatók műveletek.

## <a name="microsoftaad"></a>Microsoft.AAD

| Művelet | Leírás |
|---|---|
|/domainServices/delete|Deletes Domain Services.|
|/domainServices/read|Reads Domain Services.|
|/domainServices/write|Write Domain Services|
|/Locations/operationresults/Read|Egy aszinkron művelet állapotát olvassa.|
|Műveletek/olvasása|A művelet, mert a honosított rövid leírás megjelenjen-e a felhasználó.|

## <a name="microsoftaadiam"></a>microsoft.aadiam

| Művelet | Leírás |
|---|---|
|/diagnosticsettings/delete|A diagnosztikai beállítás törlése|
|/diagnosticsettings/Read|A diagnosztikai beállítás beolvasásakor.|
|/ diagnosticsettings/írása|A diagnosztikai beállításának írása|
|/diagnosticsettingscategories/Read|A diagnosztikai beállításának kategóriák olvasása|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/tenants/providers/Microsoft.Insights/logDefinitions/read|A bérlők számára a naplók beolvasása|

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Művelet | Leírás |
|---|---|
|/ configuration/művelet|Frissítések bérlő konfigurációjához.|
|/Configuration/Read|Beolvassa a bérlő konfigurációjához.|
|/ configuration/írása|Létrehoz egy bérlői konfigurációt.|
|/services/action|Frissíti a szolgáltatáspéldány a bérlőben.|
|/Services/Alerts/Read|Beolvassa a riasztásokat, a szolgáltatás számára.|
|/Services/Alerts/Read|Beolvassa a riasztásokat, a szolgáltatás számára.|
|/services/delete|Törli a szolgáltatáspéldány a bérlőben.|
|/Services/Read|A szolgáltatáspéldány a bérlő beolvasása.|
|/services/servicemembers/action|A szolgáltatás egy tag szolgáltatáspéldány hoz létre.|
|/Services/servicemembers/Alerts/Read|A szolgáltatás tagjához olvassa be a riasztásokat.|
|/services/servicemembers/delete|Törli a tag példánya a szolgáltatásban.|
|/Services/servicemembers/Read|Olvassa be a szolgáltatáspéldány tag a szolgáltatásban.|
|/ services/írása|Hozza létre a szolgáltatáspéldány, a bérlő.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Művelet | Leírás |
|---|---|
|/configurations/Read|Konfigurációk beolvasása|
|/ konfigurációk/írása|Létrehozza vagy frissítések konfigurációja|
|/ generateRecommendations/művelet|Ajánlatok generálása|
|/generateRecommendations/Read|Lekérdezi készítése javaslatok állapota|
|/Operations/Read|Lekérdezi a műveleteket, a Microsoft Advisor|
|/recommendations/Read|Javaslatok beolvasása|
|/recommendations/suppressions/DELETE|Tiltási törlése|
|/recommendations/suppressions/Read|Lekérdezi a suppressions|
|/recommendations/suppressions/Write|Suppressions hoz létre, frissítések|
|/ regisztrációs/művelet|A Microsoft Advisor előfizetésének regisztrálása|
|/suppressions/delete|Tiltási törlése|
|/suppressions/Read|Lekérdezi a suppressions|
|/ suppressions/írása|Suppressions hoz létre, frissítések|
|/ unregister/művelet|A Microsoft Advisor az előfizetés regisztrációjának törlése|

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

| Művelet | Leírás |
|---|---|
|/Alerts/Read|A riasztások lekérése a bemeneti szűrőket.|
|/Alerts/Resolve/Action|Módosítsa a riasztás állapota "Resolve"|
|/alertsSummary/Read|A riasztások összefoglaló adatait beolvasása|
|Műveletek/olvasása|A megadott műveletek beolvasása|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Művelet | Leírás |
|---|---|
|/Locations/checkNameAvailability/Action|Ellenőrzi, hogy a megadott Analysis Server neve érvényes, és nincs használatban.|
|/Locations/operationresults/Read|A megadott művelet eredményének adatainak beolvasása.|
|/Locations/operationstatuses/Read|A megadott műveleti állapotának adatainak beolvasása.|
|/Operations/Read|A műveletek adatainak beolvasása|
|/ regisztrációs/művelet|Regisztrálja az Analysis Services erőforrás-szolgáltató.|
|/servers/delete|Az elemzési kiszolgáló törlése.|
|/servers/listGatewayStatus/action|Az átjáró a kiszolgálóhoz társított állapotát listázza.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Az Analysis Server diagnosztikai beállításának beolvasása.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti az Analysis Server diagnosztikai beállításának|
|/servers/providers/Microsoft.Insights/logDefinitions/read|A kiszolgálók naplók beolvasása.|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Az elemzési kiszolgáló elérhető metrikai meghatározások beolvasása|
|/Servers/Read|A megadott elemzési kiszolgáló adatainak beolvasása.|
|/servers/resume/action|Az elemzési kiszolgáló folytatja.|
|/Servers/skus/Read|A kiszolgáló elérhető SKU információinak lekérése|
|/servers/suspend/action|Felfüggeszti az elemzési kiszolgáló.|
|/ kiszolgálók/írása|Létrehozza vagy frissíti a megadott Analysis-kiszolgálóhoz.|
|/skus/Read|Az SKU adatainak beolvasása|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Művelet | Leírás |
|---|---|
|/checkNameAvailability/Read|Ellenőrzi, hogy a megadott szolgáltatásnév érhető el|
|/Operations/Read|Minden API-műveleteket elérhető Microsoft.ApiManagement erőforrás olvasása|
|/ regisztrációs/művelet|Előfizetés regisztrálása a Microsoft.ApiManagement erőforrás-szolgáltató|
|/Reports/Read|Összesítve időszakokat, földrajzi régiót, fejlesztők, termékek, API-k, műveletek, előfizetés és byRequest jelentések lekérése.|
|/service/apis/delete|Távolítsa el a meglévő API|
|/service/apis/diagnostics/delete|Távolítsa el a meglévő diagnosztikai|
|/service/apis/diagnostics/loggers/delete|Távolítsa el a naplózó diagnosztikai beállítással leképezése|
|/service/apis/diagnostics/loggers/read|Meglévő diagnosztikai figyelő szoftverek listájának beolvasása|
|/service/apis/diagnostics/loggers/write|Térkép naplózó diagnosztikai beállítások|
|/service/apis/diagnostics/read|Diagnosztika vagy a diagnosztika Get részleteinek listáját|
|/service/apis/diagnostics/write|Új diagnosztikai hozzáadásakor vagy módosításakor a meglévő diagnosztikai részletek|
|/service/apis/operations/delete|Távolítsa el a meglévő API-művelet|
|/service/apis/operations/policies/delete|API-művelet házirendek házirend-konfiguráció eltávolítása|
|/Service/APIs/Operations/Policies/Read|API-művelet vagy Get házirend konfigurációs adatait az API-művelet házirendek lekéréséhez|
|/Service/APIs/Operations/Policies/Write|Állítsa be a házirend konfigurációs adatait az API-művelet|
|/service/apis/operations/policy/delete|Távolítsa el a házirend-konfigurációt a művelet|
|/Service/APIs/Operations/Policy/Read|Ezzel a házirend konfigurációs adatokat művelet|
|/Service/APIs/Operations/Policy/Write|Adja meg a művelet házirend konfigurációs részleteit|
|/Service/APIs/Operations/Read|A meglévő API műveletek listájának vagy részletes API művelet|
|/service/apis/operations/tags/delete|Meglévő címke meglévő műveletet a társítás törlése|
|/Service/APIs/Operations/tags/Read|A művelet vagy a kód első részletek társított címkék beolvasása|
|/service/apis/operations/tags/write|Meglévő címke társítása meglévő művelet|
|/service/apis/operations/write|Új API-művelet létrehozni vagy frissíteni a meglévő API-művelet|
|/service/apis/operationsByTags/read|Művelet/címke társítások listájának beolvasása|
|/service/apis/policies/delete|Távolítsa el a házirend-konfigurációt a API-házirendek|
|/Service/APIs/Policies/Read|Házirendek lekérése az API-t vagy a Get házirend konfigurációs adatait az API-hoz|
|/service/apis/policies/write|Állítsa be a házirend-konfigurációs adatait az API-hoz|
|/service/apis/policy/delete|Távolítsa el a házirend-konfigurációs API|
|/Service/APIs/Policy/Read|Ezzel a házirend konfigurációs adatokat az API-hoz|
|/service/apis/policy/write|Állítsa be a házirend-konfigurációs adatait az API-hoz|
|/Service/APIs/Products/Read|Az API-t részét képező összes termék beszerzése|
|/service/apis/read|Szerezze be az összes regisztrált API-k vagy Get részleteit API listát|
|/service/apis/releases/delete|Eltávolítja az összes kiadásaiban az API-t vagy API eltávolítása kiadás|
|/Service/APIs/releases/Read|Lekérni kiadások API reelase API-t vagy a Get részleteit|
|/service/apis/releases/write|Új API-verziót létrehozása vagy meglévő API-kiadás frissítése|
|/service/apis/revisions/delete|Eltávolítja az összes változatának az API-k|
|/service/apis/revisions/read|Az API-k tartozó változatok beolvasása|
|/service/apis/schemas/delete|Eltávolítja a meglévő séma|
|/service/apis/schemas/document/read|A dokumentum a séma leíró lekérése|
|/service/apis/schemas/document/write|A dokumentum leíró a séma frissítése|
|/service/apis/schemas/read|Lekérdezi a sémák az API által használt vagy a sémák lekérdezi a megadott API-hoz.|
|/service/apis/schemas/write|Beállítja a az API által használt sémák|
|/service/apis/tagDescriptions/delete|Távolítsa el az API-t címke leírása|
|/service/apis/tagDescriptions/read|Címkék leírások beszerzése API-t vagy az beszerzése címke leírás hatókörében API-hatóköre|
|/service/apis/tagDescriptions/write|Hatókör leírása létrehozása/módosítása címke API|
|/service/apis/tags/delete|Meglévő API/címke társításának megszüntetése|
|/Service/APIs/tags/Read|Minden API/címke társítás API/címke társítás API-t vagy a Get részleteinek beolvasása|
|/service/apis/tags/write|Új API/címke társítás hozzáadása|
|/service/apis/write|Hozzon létre új API-JÁVAL vagy a meglévő API részleteinek frissítése|
|/service/apisByTags/read|Szerezze be az API/címke társítások listát|
|/service/api-version-sets/delete|Távolítsa el a meglévő VersionSet|
|/service/api-version-sets/read|Itt olvashatók verzió csoport entitásokat vagy egy VersionSet lekérdezi részleteit listája|
|/service/api-version-sets/versions/read|Verzió entitások listájának beolvasása|
|/service/api-version-sets/write|Új VersionSet létrehozása vagy meglévő VersionSet részleteinek frissítése|
|/service/applynetworkconfigurationupdates/action|Frissíti a Microsoft.ApiManagement erőforrásaihoz, a virtuális hálózatot válasszon frissített hálózati beállításokat.|
|/service/authorizationServers/delete|Távolítsa el a meglévő engedélyezési kiszolgáló|
|/service/authorizationServers/read|Engedélyezési kiszolgálók listája vagy az engedélyezési kiszolgáló adatait az beszerzése|
|/service/authorizationServers/write|Hozzon létre egy új hitelesítési kiszolgáló vagy egy meglévő engedélyezési kiszolgáló frissítés részletei|
|/Service/backends/DELETE|Távolítsa el a létező háttérrendszerek|
|/Service/backends/Read|Háttérkiszolgálókon listáját, vagy a részletek a háttér|
|/Service/backends/Reconnect/Action|Újracsatlakozás kérelem létrehozása|
|/Service/backends/Write|Adja hozzá egy új háttér vagy a meglévő háttér részleteinek frissítése|
|/Service/Backup/Action|A biztonsági mentési API Management szolgáltatás a felhasználó a megadott tárolóhoz megadott tárfiók|
|/service/certificates/delete|Távolítsa el a meglévő tanúsítvány|
|/Service/Certificates/Read|A tanúsítványok listájának vagy a tanúsítvány adatainak lekérése|
|/Service/Certificates/Write|Új tanúsítvány felvétele|
|/service/delete|Törli az API Management Service-példány|
|/service/diagnostics/delete|Távolítsa el a meglévő diagnosztikai|
|/service/diagnostics/loggers/delete|Távolítsa el a naplózó diagnosztikai beállítással leképezése|
|/service/diagnostics/loggers/read|Meglévő diagnosztikai figyelő szoftverek listájának beolvasása|
|/service/diagnostics/loggers/write|Térkép naplózó diagnosztikai beállítások|
|/service/diagnostics/read|Diagnosztika vagy a diagnosztika Get részleteinek listáját|
|/service/diagnostics/write|Új diagnosztikai hozzáadásakor vagy módosításakor a meglévő diagnosztikai részletek|
|/service/getssotoken/action|Lekérdezi SSO token használható API Management szolgáltatás örökölt portáljára rendszergazdaként történő bejelentkezéshez|
|/service/groups/delete|Távolítsa el a meglévő csoporthoz|
|/Service/groups/Read|Csoportok vagy egy csoport lekérdezi részleteinek listáját|
|/service/groups/users/delete|Távolítsa el a meglévő felhasználói meglévő csoportból|
|/Service/groups/Users/Read|Csoport felhasználók listájának beolvasása|
|/Service/groups/Users/Write|Meglévő felhasználó hozzáadása meglévő csoporthoz|
|/Service/groups/Write|Új csoport létrehozása vagy meglévő csoport részleteinek frissítése|
|/service/identityProviders/delete|Távolítsa el a meglévő identitásszolgáltató|
|/service/identityProviders/read|Az identitás-szolgáltatóktól vagy identitásszolgáltató Get részleteinek listáját beszerzése|
|/service/identityProviders/write|Hozzon létre egy új identitásszolgáltató vagy a frissítés részleteit egy meglévő identitásszolgáltató|
|/service/locations/networkstatus/read|Lekérdezi a hálózati hozzáférési állapota az erőforrások, amelyen a szolgáltatás függ a helyen.|
|/service/loggers/delete|Távolítsa el a meglévő naplózó|
|/Service/loggers/Read|Figyelő szoftverek listája vagy az naplózó részleteinek beolvasása|
|/service/loggers/write|Adja hozzá az új naplózó vagy meglévő naplózó részleteinek frissítése|
|/service/managedeployments/action|Módosítsa a Termékváltozat jegyek, hozzáadása regionális egy szolgáltatás üzemelő példányainak API Management|
|/service/networkstatus/read|Lekérdezi a hálózati hozzáférési állapota az erőforrások, amelyen a szolgáltatás függ.|
|/Service/Notifications/Action|Értesítést küld egy adott felhasználó|
|/Service/Notifications/Read|Lekérdezi az összes API Management publisher értesítések vagy beszerzése API Management publisher értesítési részletei|
|/service/notifications/recipientEmails/delete|Eltávolítja a meglévő E-mail értesítést társított|
|/service/notifications/recipientEmails/read|Társított API Management Publisher értesítési e-mailek címzettjeinek beolvasása|
|/service/notifications/recipientEmails/write|Hozzon létre új E-mail címzettet az értesítés|
|/service/notifications/recipientUsers/delete|Az értesítési címzettek társított felhasználó eltávolítása|
|/service/notifications/recipientUsers/read|Az értesítés társított címzett felhasználók|
|/service/notifications/recipientUsers/write|Felhasználó hozzáadása az értesítési címzettek|
|/Service/Notifications/Write|Hozzon létre vagy az Update API Management publisher értesítés|
|/service/openidConnectProviders/delete|Távolítsa el a meglévő OpenID Connect szolgáltató|
|/service/openidConnectProviders/read|Szerezze be az OpenID Connect-szolgáltatókkal vagy az OpenID Connect szolgáltató Get részleteit listát|
|/service/openidConnectProviders/write|Hozzon létre egy új OpenID Connect szolgáltató vagy a frissítés adatait egy meglévő OpenID Connect-szolgáltató|
|/Service/operationresults/Read|Hosszú ideig futó művelet aktuális állapotának beolvasása|
|/service/policies/delete|Távolítsa el a házirend-konfigurációt a bérlő házirendek|
|/Service/Policies/Read|Házirendek bérlő bérlői vagy Get házirend konfigurációs részletek lekérése|
|/Service/Policies/Write|Állítsa be a házirend-konfiguráció részletei bérlő számára|
|/Service/policySnippets/Read|Az összes házirend kódtöredékek beolvasása|
|/Service/portalsettings/Read|Bejelentkezési beállítások beszerezni a portál vagy a Get jelentkezzen be a portál beállításait, vagy a portál delegálási beállítások beolvasása|
|/Service/portalsettings/Write|Regisztráció beállítások vagy frissítés regisztráció beállítások vagy frissítés bejelentkezés beállítások vagy frissítés bejelentkezés beállítások vagy frissítés delegálási beállításokat vagy frissítés delegálási beállítások frissítése|
|/service/products/apis/delete|Távolítsa el a meglévő API a meglévő termék|
|/Service/Products/APIs/Read|Szerezze be az API-k meglévő termékhez hozzáadott listát|
|/service/products/apis/write|Meglévő API hozzáadása meglévő termék|
|/service/products/delete|Távolítsa el a meglévő termék|
|/service/products/groups/delete|Meglévő termék társítás meglévő fejlesztői csoport törlése|
|/Service/Products/groups/Read|Termék társított fejlesztői csoportok listájának beolvasása|
|/Service/Products/groups/Write|Meglévő fejlesztői csoport társítása meglévő termék|
|/service/products/policies/delete|Távolítsa el a házirend-konfigurációt a termék házirendek|
|/Service/Products/Policies/Read|A termék vagy a Get házirend konfigurációs részletek termék házirendek lekérése|
|/Service/Products/Policies/Write|Állítsa be a házirend-konfiguráció részletei termékhez|
|/service/products/policy/delete|Távolítsa el a házirend-konfigurációt a meglévő termék|
|/Service/Products/Policy/Read|Házirend-konfigurációt meglévő termék beszerzése|
|/Service/Products/Policy/Write|A termék meglévő házirend-konfiguráció|
|/Service/Products/Read|Termékek listáját, vagy a részletek a termék|
|/service/products/subscriptions/read|Termék előfizetések listájának beolvasása|
|/service/products/tags/delete|Meglévő címke meglévő termékkel társítás törlése|
|/Service/Products/tags/Read|A termék vagy az beszerzése címke részletek társított címkék beolvasása|
|/Service/Products/tags/Write|Meglévő címke társítása meglévő termék|
|/Service/Products/Write|Új termék létrehozása vagy meglévő termékadatok frissítése|
|/service/properties/delete|Eltávolítja a meglévő tulajdonság|
|/Service/Properties/Read|Beolvassa az összes tulajdonság listáját, és lekérdezi a megadott tulajdonság részleteit|
|/service/properties/write|Új tulajdonság létrehozása vagy frissítése a megadott tulajdonság értéke|
|/service/providers/Microsoft.Insights/diagnosticSettings/read|Az API Management szolgáltatás diagnosztikai beállításának beolvasása.|
|/service/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti az API-kezelés szolgáltatás a diagnosztikai beállítása|
|/service/providers/Microsoft.Insights/logDefinitions/read|Az API Management szolgáltatás naplók beolvasása.|
|/service/providers/Microsoft.Insights/metricDefinitions/read|Az API Management szolgáltatáshoz elérhető metrikai meghatározások beolvasása|
|/Service/Quotas/Periods/Read|Kvóta számlálóérték beolvasása időszak|
|/Service/Quotas/Periods/Write|Kvóta számláló aktuális érték|
|/Service/Quotas/Read|Értékek lekérése kvóta|
|/service/quotas/write|Kvóta számláló aktuális érték|
|/service/read|Olvassa el a metaadatokat az API Management szolgáltatáspéldány|
|/Service/Reports/Read|Összesítve időszakra vagy a földrajzi régió vagy fejlesztők összesítve Get jelentés összesítve Get jelentés jelentés lekérése. vagy a jelentés megtekintése a termékek alapján összesíti. vagy a műveletek vagy Get jelentés-előfizetés összesítve összesítve API-k vagy Get jelentés összesítve jelentés megtekintése. vagy a jelentés adatainak kérelmek|
|/service/restore/action|Állítsa vissza egy felhasználó által megadott tárfiók a megadott tároló API Management szolgáltatás|
|/service/subscriptions/delete|Előfizetés törlése. Ez a művelet segítségével előfizetés törlése|
|/service/subscriptions/read|A termék előfizetések listáját, vagy a részletek a termék előfizetés|
|/service/subscriptions/regeneratePrimaryKey/action|Előfizetés elsődleges kulcs újragenerálása|
|/service/subscriptions/regenerateSecondaryKey/action|Előfizetés másodlagos kulcs újragenerálása|
|/service/subscriptions/write|Egy meglévő felhasználó számára a meglévő termék előfizetés, vagy frissítse a meglévő előfizetés részletei. Ez a művelet használható előfizetés megújításához|
|/Service/tagResources/Read|Társított erőforrásokkal rendelkező címkék listájának beolvasása|
|/service/tags/delete|Meglévő címke eltávolítása|
|/Service/tags/Read|Címkék listáját és a részletek a címke|
|/Service/tags/Write|Új címke hozzáadása vagy meglévő címke részleteinek frissítése|
|/service/templates/delete|Alapértelmezett API Management e-mail sablon alaphelyzetbe állítása|
|/service/templates/read|Lekérdezi az összes e-mail-sablonokkal vagy lekérdezi az API Management e-mail sablon részletei|
|/service/templates/write|Létrehozni vagy frissíteni az API Management e-mail sablon vagy frissíti az API Management e-mail sablon|
|/service/tenant/delete|A bérlőhöz tartozó házirend-konfiguráció eltávolítása|
|/Service/tenant/Deploy/Action|A megadott git fiókirodai módosítások alkalmazása a konfigurációs adatbázis telepítési feladat fut.|
|/service/tenant/operationResults/read|A művelet eredménye listája vagy az beszerzése az adott művelet eredménye|
|/Service/tenant/Read|Házirend-konfiguráció beolvasása a bérlő vagy a Get-bérlő hozzáférés adatai|
|/service/tenant/regeneratePrimaryKey/action|Elsődleges elérési kulcs újragenerálása|
|/service/tenant/regenerateSecondaryKey/action|Másodlagos elérési kulcs újragenerálása|
|/service/tenant/save/action|A tárházban található a megadott ágra konfigurációs pillanatképet hoz létre a véglegesítési|
|/service/tenant/syncState/read|Az utolsó git szinkronizálás állapotának beolvasása|
|/service/tenant/validate/action|Ellenőrzi a megadott git fiókirodai módosítások|
|/service/tenant/write|A bérlő vagy az Update bérlői hozzáférés adatai házirend-konfiguráció|
|/service/updatecertificate/action|Az API Management szolgáltatás SSL-tanúsítvány feltöltése|
|/service/updatehostname/action|A telepítő, frissítésére, vagy távolítsa el az API Management szolgáltatás egyéni tartománynevek|
|/service/users/action|Új felhasználó regisztrálása|
|/service/users/applications/attachments/delete|Eltávolít egy melléklet|
|/service/users/applications/attachments/read|Lekérdezi az alkalmazás mellékletek vagy lekérdezi melléklet|
|/service/users/applications/attachments/write|Melléklet alkalmazás hozzáadása|
|/service/users/applications/delete|Eltávolítja a meglévő alkalmazáshoz|
|/Service/Users/Applications/Read|Minden felhasználó az alkalmazások és az API Management lekérdezi az alkalmazás részleteinek listájának|
|/service/users/applications/write|Az API Management és a frissítéseket az alkalmazás részleteinek alkalmazás regisztrálása|
|/service/users/delete|Felhasználói fiók eltávolítása|
|/service/users/generateSsoUrl/action|Egyszeri bejelentkezési URL-címet létrehozni. Az URL-cím használható felügyeleti portál eléréséhez|
|/Service/Users/groups/Read|Felhasználói csoportok listájának beolvasása|
|/Service/Users/keys/Read|Felhasználói kulcsok listájának beolvasása|
|/Service/Users/Read|A regisztrált felhasználók listáját, vagy felhasználói fiók adatainak lekérése|
|/service/users/subscriptions/read|Felhasználói előfizetések listájának beolvasása|
|/service/users/token/action|Hozzáférés a jogkivonatokhoz-token beszerzése a felhasználó számára|
|/Service/Users/Write|Új felhasználó vagy egy meglévő felhasználó frissítés fiókadatok regisztrálása|
|/service/write|Az API Management szolgáltatást egy új példányának létrehozása|
|/ unregister/művelet|Előfizetés regisztrációjának Microsoft.ApiManagement erőforrás-szolgáltató|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Művelet | Leírás |
|---|---|
|/checkAccess/action|Ellenőrzi, hogy a hívó jogosult-e egy adott művelet végrehajtására|
|/classicAdministrators/delete|Az előfizetés rendszergazdájának eltávolítása.|
|/classicAdministrators/Read|Az előfizetés rendszergazdáinak beolvasása.|
|/ classicAdministrators/írása|Az előfizetés rendszergazdájának beállítása vagy módosítása.|
|/elevateAccess/action|Felhasználói hozzáférés rendszergazdai szerepkörének megadása a hívónak a bérlői hatókörben|
|/Locks/DELETE|A megadott hatókör zárolásainak törlése.|
|/Locks/Read|A megadott hatókör zárolásainak beolvasása.|
|/ zárolások/írása|A megadott hatókör zárolásainak beállítása.|
|/permissions/Read|A hívó adott hatókörben érvényes engedélyeinek listázása.|
|/policyAssignments/DELETE|Szabályzat-hozzárendelés törlése a megadott hatókörben.|
|/policyAssignments/Read|Szabályzat-hozzárendelés adatainak lekérése.|
|/ policyAssignments/írása|Szabályzat-hozzárendelés létrehozása a megadott hatókörben.|
|/policyDefinitions/DELETE|Szabályzat-definíció törlése.|
|/policyDefinitions/Read|Szabályzat-definíció adatainak lekérése.|
|/ policyDefinitions/írása|Egyéni szabályzat-definíció létrehozása.|
|/policySetDefinitions/delete|Szabályzatkészlet-definíció törlése.|
|/policySetDefinitions/read|Egy szabályzatkészlet-definíció adatainak beolvasása.|
|/policySetDefinitions/write|Egyéni szabályzatkészlet-definíció létrehozása.|
|/providerOperations/Read|Az összes, szerepkör-definíciókban használható erőforrás-szolgáltató műveleteinek beolvasása.|
|/roleAssignments/DELETE|Szerepkör-hozzárendelés törlése a megadott hatókörből.|
|/roleAssignments/Read|Információk beolvasása a szerepkör-hozzárendelésről.|
|/ roleAssignments/írása|Szerepkör-hozzárendelés létrehozása a megadott hatókörben.|
|/roleDefinitions/DELETE|A megadott egyéni szerepkör-definíció törlése.|
|/roleDefinitions/read|Szerepkör-definíció adatainak beolvasása.|
|/roleDefinitions/write|Egy egyéni szerepkör-definíció létrehozása vagy módosítása a megadott engedélyekkel és hozzárendelhető hatókörökkel.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Művelet | Leírás |
|---|---|
|/automationAccounts/agentRegistrationInformation/read|Olvassa el a regisztrációs adatokat egy Azure Automation DSC|
|/automationAccounts/agentRegistrationInformation/regenerateKey/action|Azure Automation DSC kulcsok újragenerálása kérelem írási műveletek|
|/automationAccounts/certificates/delete|Egy Azure Automation szolgáltatásbeli tanúsítványeszköz törlése|
|/automationAccounts/Certificates/Read|Egy Azure Automation szolgáltatásbeli tanúsítványeszköz beolvasása|
|/automationAccounts/certificates/write|Létrehozza vagy frissíti az Azure Automation szolgáltatásbeli tanúsítványeszköz|
|/automationAccounts/compilationjobs/read|Egy Azure Automation DSC-fordítási beolvasása|
|/automationAccounts/compilationjobs/write|Egy Azure Automation DSC-fordítási írási műveletek|
|/automationAccounts/configurations/delete|Egy Azure Automation DSC tartalmának törlése|
|/automationAccounts/configurations/getCount/action|A szám egy Azure Automation DSC tartalmának beolvasása|
|/automationAccounts/configurations/read|Egy Azure Automation DSC tartalmának beolvasása|
|/automationAccounts/configurations/write|Egy Azure Automation DSC tartalmának írása|
|/automationAccounts/connections/delete|Azure Automation szolgáltatásbeli kapcsolódási eszköz törlése|
|/automationAccounts/connections/read|Azure Automation szolgáltatásbeli kapcsolódási eszköz beolvasása|
|/automationAccounts/connections/write|Létrehozza vagy frissíti az Azure Automation szolgáltatásbeli kapcsolódási eszköz|
|/automationAccounts/connectionTypes/delete|Egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz törlése|
|/automationAccounts/connectionTypes/read|Egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz beolvasása|
|/automationAccounts/connectionTypes/write|Egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz létrehozása|
|/automationAccounts/credentials/delete|Egy Azure Automation szolgáltatásbeli hitelesítőadat-eszköz törlése|
|/automationAccounts/credentials/read|Egy Azure Automation szolgáltatásbeli hitelesítőadat-eszköz beolvasása|
|/automationAccounts/credentials/write|Létrehozza vagy frissíti egy Azure Automation szolgáltatásbeli hitelesítőadat-eszköz|
|/automationAccounts/delete|Egy Azure Automation-fiók törlése|
|/automationAccounts/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/automationAccounts/diagnosticSettings/write|Az erőforrás diagnosztikai beállításának|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Hibrid forgatókönyv-feldolgozó erőforrások törlése|
|/automationAccounts/hybridRunbookWorkerGroups/read|Beolvassa a hibrid forgatókönyv-feldolgozó erőforrások|
|/automationAccounts/jobs/output/action|Egy feladat kimenetének beolvasása|
|/automationAccounts/jobs/output/action|Egy feladat kimenetének beolvasása|
|/automationAccounts/jobs/read|Egy Azure Automation-feladat beolvasása|
|/automationAccounts/jobs/read|Egy Azure Automation-feladat beolvasása|
|/automationAccounts/jobs/resume/action|Egy Azure Automation-feladat folytatása|
|/automationAccounts/jobs/resume/action|Egy Azure Automation-feladat folytatása|
|/automationAccounts/jobs/runbookContent/action|A feladat végrehajtásának időpontjában az Azure Automation-runbook tartalmának beolvasása|
|/automationAccounts/jobs/runbookContent/action|A feladat végrehajtásának időpontjában az Azure Automation-runbook tartalmának beolvasása|
|/automationAccounts/jobs/stop/action|Egy Azure Automation-feladat leállítása|
|/automationAccounts/jobs/stop/action|Egy Azure Automation-feladat leállítása|
|/automationAccounts/jobs/streams/read|Egy Azure Automation-feladatstream beolvasása|
|/automationAccounts/jobs/streams/read|Egy Azure Automation-feladatstream beolvasása|
|/automationAccounts/jobs/suspend/action|Egy Azure Automation-feladat felfüggesztése|
|/automationAccounts/jobs/suspend/action|Egy Azure Automation-feladat felfüggesztése|
|/automationAccounts/jobs/write|Egy Azure Automation-feladat létrehozása|
|/automationAccounts/jobs/write|Egy Azure Automation-feladat létrehozása|
|/automationAccounts/jobSchedules/delete|Egy Azure Automation-feladatütemezés törlése|
|/automationAccounts/jobSchedules/read|Egy Azure Automation-feladatütemezés beolvasása|
|/automationAccounts/jobSchedules/write|Egy Azure Automation-feladatütemezés létrehozása|
|/automationAccounts/linkedWorkspace/read|Lekérdezi a munkaterület az automation-fiókhoz csatolva.|
|/automationAccounts/logDefinitions/read|Az automatizálási fiók naplók beolvasása.|
|/automationAccounts/modules/activities/read|Lekérdezi az Azure Automation-tevékenységek|
|/automationAccounts/modules/delete|Egy Azure Automation-modul törlése|
|/automationAccounts/modules/read|Egy Azure Automation-modul beolvasása|
|/automationAccounts/Modules/Write|Létrehozza vagy frissíti az Azure Automation-modul|
|/automationAccounts/nodeConfigurations/delete|Egy Azure Automation DSC-csomópont-konfiguráció törlése|
|/automationAccounts/nodeConfigurations/read|Egy Azure Automation DSC-csomópont-konfiguráció beolvasása|
|/automationAccounts/nodeConfigurations/readContent/action|Egy Azure Automation DSC-csomópont konfigurációjának tartalma beolvasása|
|/automationAccounts/nodeConfigurations/write|Ír egy Azure Automation DSC-csomópont-konfiguráció|
|/automationAccounts/nodes/delete|Azure Automation DSC-csomópontok törlése|
|/automationAccounts/nodes/read|Azure Automation DSC-csomópontok beolvasása|
|/automationAccounts/nodes/reports/read|Azure Automation DSC-jelentés contentss beolvasása|
|/automationAccounts/nodes/reports/read|Azure Automation DSC-jelentéseket beolvasása|
|/automationAccounts/objectDataTypes/fields/read|Azure Automation szolgáltatásbeli TypeFields beolvasása|
|/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read|Automatizálási metrikai meghatározásainak beolvasása|
|/automationAccounts/read|Egy Azure Automation-fiók beolvasása|
|/automationAccounts/runbooks/delete|Egy Azure Automation-runbook törlése|
|/automationAccounts/runbooks/draft/publish/Action|Egy Azure Automation-runbookvázlat közzététele|
|/automationAccounts/runbooks/draft/Read|Egy Azure Automation-runbookvázlat beolvasása|
|/automationAccounts/runbooks/draft/readContent/action|Egy Azure Automation-runbookvázlat tartalmának beolvasása|
|/automationAccounts/runbooks/draft/testJob/read|Egy Azure Automation runbookvázlat-tesztelési feladat beolvasása|
|/automationAccounts/runbooks/draft/testJob/resume/action|Egy Azure Automation runbookvázlat-tesztelési feladat folytatása|
|/automationAccounts/runbooks/draft/testJob/stop/action|Egy Azure Automation runbookvázlat-tesztelési feladat leállítása|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Egy Azure Automation runbookvázlat-tesztelési feladat felfüggesztése|
|/automationAccounts/runbooks/draft/testJob/write|Egy Azure Automation runbookvázlat-tesztelési feladat létrehozása|
|/automationAccounts/runbooks/draft/undoEdit/action|Egy Azure Automation-runbookvázlat szerkesztésének visszavonása|
|/automationAccounts/runbooks/draft/writeContent/action|Egy Azure Automation-runbookvázlat tartalmának létrehozása|
|/automationAccounts/runbooks/read|Egy Azure Automation-runbook beolvasása|
|/automationAccounts/runbooks/readContent/action|Egy Azure Automation-runbook tartalmának beolvasása|
|/automationAccounts/runbooks/write|Létrehozza vagy frissíti az Azure Automation-runbook|
|/automationAccounts/schedules/delete|Egy Azure Automation szolgáltatásbeli ütemezéseszköz törlése|
|/automationAccounts/schedules/read|Egy Azure Automation szolgáltatásbeli ütemezési eszköz beolvasása|
|/automationAccounts/schedules/Write|Létrehozza vagy frissíti az Azure Automation szolgáltatásbeli ütemezési eszköz|
|/automationAccounts/statistics/read|Azure Automation szolgáltatásbeli statisztika beolvasása|
|/automationAccounts/usages/read|Lekérdezi az Azure Automation-használat|
|/automationAccounts/variables/delete|Egy Azure Automation szolgáltatásbeli változóeszköz törlése|
|/automationAccounts/variables/read|Egy Azure Automation szolgáltatásbeli változóeszköz beolvasása|
|/automationAccounts/variables/Write|Létrehozza vagy frissíti egy Azure Automation szolgáltatásbeli változóeszköz|
|/automationAccounts/watchers/streams/read|Egy Azure Automation-megfigyelő feladatstream beolvasása|
|/automationAccounts/webhooks/delete|Egy Azure Automation-webhook törlése |
|/automationAccounts/webhooks/generateUri/action|URI generálása Azure Automation-webhook|
|/automationAccounts/webhooks/read|Egy Azure Automation-webhook beolvasása|
|/automationAccounts/webhooks/write|Létrehozza vagy frissíti az Azure Automation-webhook|
|/automationAccounts/write|Létrehozza vagy frissíti az Azure Automation-fiók|
|/automationAccounts/write|Létrehozza vagy frissíti az Azure Automation-fiók|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

| Művelet | Leírás |
|---|---|
|/b2cDirectories/delete|B2C-címtárerőforrás törlése|
|/b2cDirectories/Read|B2C-címtárerőforrás megtekintése|
|/ b2cDirectories/írása|B2C-címtárerőforrás létrehozása vagy frissítése|
|/Operations/Read|A Microsoft.AzureActiveDirectory erőforrás-szolgáltatóhoz választható összes API-művelet olvasása|
|/ regisztrációs/művelet|A Microsoft.AzureActiveDirectory erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása|

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

| Művelet | Leírás |
|---|---|
|Műveletek/olvasása|Egy erőforrás-szolgáltató művelet tulajdonságait olvassa be|
|/ regisztrációs/művelet|Az előfizetés regisztrálása a Microsoft.AzureStack erőforrás-szolgáltató|
|/registrations/customerSubscriptions/delete|Egy Azure verem ügyfél-előfizetést törlése|
|/registrations/customerSubscriptions/read|Az Azure-verem felhasználói előfizetés tulajdonságainak beolvasása|
|/registrations/customerSubscriptions/write|Létrehozza vagy frissíti az Azure-verem felhasználói előfizetéssel|
|/registrations/delete|Egy Azure verem regisztráció törlése|
|/registrations/getActivationKey/action|A legújabb Azure verem használható aktiválási kulcs beolvasása|
|/registrations/products/listDetails/action|Lekéri a kiterjesztett egy Azure verem piactér megoldás részletei|
|/Registrations/Products/Read|Egy Azure verem piactér termék tulajdonságainak beolvasása|
|/Registrations/Read|Egy Azure verem regisztrációs tulajdonságait olvassa be|
|/ regisztrációk/írása|Létrehozza vagy frissíti az Azure-verem regisztrációs|

## <a name="microsoftbatch"></a>Microsoft.Batch

| Művelet | Leírás |
|---|---|
|/batchAccounts/Applications/DELETE|Az alkalmazás törlése|
|/batchAccounts/Applications/Read|Megjeleníti azokat alkalmazásokat, vagy az alkalmazás tulajdonságainak beolvasása|
|/batchAccounts/applications/versions/activate/action|Az alkalmazáscsomag aktiválása|
|/batchAccounts/applications/versions/delete|Az alkalmazáscsomag törlése|
|/batchAccounts/Applications/versions/Read|Alkalmazáscsomag tulajdonságait olvassa be|
|/batchAccounts/Applications/versions/Write|Létrehoz egy új alkalmazás-csomagot, vagy frissíti a meglévő alkalmazáscsomag|
|/batchAccounts/Applications/Write|Létrehoz egy új alkalmazást, vagy egy meglévő alkalmazás frissítése|
|/batchAccounts/certificateOperationResults/read|Batch-fiók tanúsítvány egy hosszú ideig futó művelet eredményének beolvasása|
|/batchAccounts/Certificates/cancelDelete/Action|A tanúsítvány a Batch-fiók törlésére tett sikertelen kísérlet megszakítása|
|/batchAccounts/Certificates/DELETE|Töröl egy tanúsítványt a Batch-fiókból|
|/batchAccounts/Certificates/Read|A Batch-fiók-tanúsítványokat sorolja fel, vagy a tanúsítvány tulajdonságainak beolvasása|
|/batchAccounts/Certificates/Write|Létrehoz egy új tanúsítványt a Batch-fiók, vagy egy meglévő tanúsítvány frissítése|
|/batchAccounts/DELETE|Batch-fiók törlése|
|/batchAccounts/listkeys/Action|Listák hívóbetűk Batch-fiókhoz|
|/batchAccounts/operationResults/read|A Batch-fiók hosszú ideig futó művelet eredményének beolvasása|
|/batchAccounts/poolOperationResults/read|Batch-fiók készlet egy hosszú ideig futó művelet eredményének beolvasása|
|/batchAccounts/Pools/DELETE|Egy készlet törlése Batch-fiók|
|/batchAccounts/pools/disableAutoscale/action|A Batch-fiók készlet automatikus skálázás letiltása|
|/batchAccounts/Pools/Read|A Batch-fiók készletek sorolja fel, vagy a készlet tulajdonságait olvassa be|
|/batchAccounts/Pools/stopResize/Action|Leállítja egy folyamatban lévő műveletet a Batch-fiók készlet átméretezése|
|/batchAccounts/Pools/upgradeOs/Action|Frissíti az operációs rendszert, a Batch-fiók címkészlet|
|/batchAccounts/Pools/Write|Új készlet létrehozása a Batch-fiók, vagy egy meglévő készlet frissítése|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/batchAccounts/providers/Microsoft.Insights/logDefinitions/read|A Batch szolgáltatás naplók beolvasása.|
|/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read|A Batch szolgáltatás elérhető metrikai meghatározások beolvasása|
|/batchAccounts/Read|Batch-fiókok listája, vagy a Batch-fiók tulajdonságait olvassa be|
|/batchAccounts/regeneratekeys/Action|A Batch-fiókhoz hívóbetűk újragenerálása|
|/batchAccounts/syncAutoStorageKeys/action|Szinkronizálja a Batch-fiók beállítása automatikus tárfiók hozzáférési kulcsainak listázása|
|/ batchAccounts/írása|Új Batch-fiók létrehozása vagy frissítése egy meglévő Batch-fiók|
|/Locations/checkNameAvailability/Action|Ellenőrzi, hogy a fiók neve érvényes, és nincs használatban.|
|/Locations/Quotas/Read|A megadott Azure-régiót kötegelt a megadott előfizetés kvótájának beolvasása|
|/ regisztrációs/művelet|A kötegelt erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi, hogy a Batch-fiókok létrehozása|
|/ unregister/művelet|A Batch-fiókok megelőzése kötegelt erőforrás-szolgáltató az előfizetés regisztrációjának törlése|

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

| Művelet | Leírás |
|---|---|
|/Clusters/DELETE|A kötegelt AI fürt törlése|
|/Clusters/Read|Felsorolja a kötegelt AI fürtök vagy egy kötegelt AI fürt tulajdonságait olvassa be|
|/clusters/remoteLoginInformation/action|Távoli-bejelentkezési adatok kötegelt AI fürt listája|
|/ fürtök/írása|Egy új kötegelt AI fürtöt hoz létre, vagy kötegelt AI meglévő fürt frissítése|
|/fileservers/delete|A kötegelt AI fájlkiszolgáló törlése|
|/fileservers/read|Kötegelt AI fileservers sorolja fel, vagy egy kötegelt AI fájlkiszolgálóhoz tulajdonságait olvassa be|
|/fileservers/resume/action|A kötegelt AI fájlkiszolgáló folytatása|
|/fileservers/suspend/action|A kötegelt AI fájlkiszolgáló felfüggesztése|
|/ fileservers/írása|Egy új kötegelt AI fájlkiszolgálóhoz létrehoz vagy frissít egy meglévő kötegelt AI fájlkiszolgáló|
|/jobs/delete|A kötegelt AI feladatok törlése|
|/Jobs/Read|Felsorolja a kötegelt AI feladatokat, vagy egy kötegelt AI feladat tulajdonságait olvassa be|
|/jobs/remoteLoginInformation/action|Távoli-bejelentkezési adatok kötegelt AI feladat listája|
|/Jobs/Terminate/Action|A kötegelt AI feladat leállítása|
|/ feladatok/írása|Létrehoz egy új kötegelt AI feladatot, vagy egy meglévő kötegelt AI feladat frissítése|
|/ regisztrációs/művelet|A kötegelt AI erőforrás-szolgáltató előfizetésének regisztrálása és létrehozását lehetővé tevő kötegelt AI erőforrások|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Művelet | Leírás |
|---|---|
|/billingPeriods/Read|Megjeleníti az elérhető elszámolási időszakok|
|/Invoices/Read|Rendelkezésre álló listák számlák|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Művelet | Leírás |
|---|---|
|/mapApis/Delete|Törlési művelet|
|/mapApis/listSecrets/action|A titkos kulcsok listázása|
|/mapApis/listSingleSignOnToken/action|Az erőforráshoz tartozó egyszeri bejelentkezési engedélyezési jogkivonat beolvasása|
|/ mapApis/Olvasás|Olvasási művelet|
|/mapApis/regenerateKey/action|A kulcs újragenerálása|
|/ mapApis/írása|Írási művelet|
|Műveletek/olvasása|A művelet leírását.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Művelet | Leírás |
|---|---|
|/ checknameavailability/művelet|Annak ellenőrzése, hogy a név rendelkezésre áll-e egy új Redis Cache-hez való használatra|
|/Operations/Read|A műveletek adott "Microsoft.Cache" szolgáltató által támogatott sorolja fel.|
|/redis/delete|Teljes Redis gyorsítótár törlése|
|/redis/export/Action|Redis-adatok exportálása előre megadott tárolási blobokba meghatározott formátumban|
|/redis/firewallRules/delete|Egy Redis Cache IP-tűzfalszabályainak törlése|
|/redis/firewallRules/read|Egy Redis Cache IP-tűzfalszabályainak beolvasása|
|/redis/firewallRules/write|Egy Redis Cache IP-tűzfalszabályainak szerkesztése|
|/redis/forceReboot/action|Egy gyorsítótárpéldány kényszerített újraindítása, mely adatvesztést okozhat.|
|/redis/import/Action|Meghatározott formátumú adatok importálása a Redis szolgáltatásba több blobból|
|/redis/linkedservers/delete|Egy Redis gyorsítótárhoz csatolt kiszolgáló törlése|
|/redis/linkedservers/read|Egy Redis gyorsítótárhoz hozzárendelt csatolt kiszolgálók beolvasása.|
|/redis/linkedservers/write|Csatolt kiszolgáló hozzáadása egy Redis gyorsítótárhoz|
|/redis/listKeys/action|Redis gyorsítótár elérésikulcs-értékeinek megtekintése a felügyeleti portálon|
|/redis/listUpgradeNotifications/Read|A gyorsítótár bérlője legutóbbi csomagváltási értesítéseinek listázása.|
|/redis/Locations/operationresults/Read|Lekérdezi a long eredményét, amelynek a "Hely" fejlécet korábban vissza az ügyfélnek művelet futtatása|
|/redis/metricDefinitions/read|Egy Redis gyorsítótárhoz elérhető metrikai meghatározások beolvasása|
|/redis/patchSchedules/delete|Egy Redis Cache javítási ütemezésének törlése|
|/redis/patchSchedules/read|Egy Redis Cache javítási ütemezésének beolvasása|
|/redis/patchSchedules/write|Egy Redis Cache javítási ütemezésének módosítása|
|/redis/Read|Redis gyorsítótár beállításainak és konfigurációjának megtekintése a felügyeleti portálon|
|/redis/regenerateKey/Action|Redis gyorsítótár elérésikulcs-értékeinek módosítása a felügyeleti portálon|
|/redis/Start/Action|Gyorsítótárpéldány elindítása.|
|/redis/STOP/Action|Gyorsítótárpéldány leállítása.|
|/ redis/írása|Redis gyorsítótár beállításainak és konfigurációjának módosítása a felügyeleti portálon|
|/ regisztrációs/művelet|A Microsoft.Cache erőforrás-szolgáltató regisztrálása egy előfizetéshez|
|/ unregister/művelet|A Microsoft.Cache erőforrás-szolgáltató egy előfizetéshez tartozó regisztrációjának törlése|

## <a name="microsoftcapacity"></a>Microsoft.Capacity

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|A kapacitás erőforrás-szolgáltató regisztrálja, és lehetővé teszi a kapacitás erőforrások létrehozását.|
|/reservationorders/action|Bármely foglalásának frissítése|
|/reservationorders/delete|A foglalás törlése|
|/reservationorders/Read|Olvassa el az összes foglalások|
|/reservationorders/reservations/action|Bármely foglalásának frissítése|
|/reservationorders/reservations/delete|A foglalás törlése|
|/reservationorders/reservations/Read|Olvassa el az összes foglalások|
|/reservationorders/reservations/revisions/Read|Olvassa el az összes foglalások|
|/reservationorders/reservations/Write|A foglalás létrehozása|
|/ reservationorders/írása|A foglalás létrehozása|

## <a name="microsoftcdn"></a>Microsoft.Cdn

| Művelet | Leírás |
|---|---|
|/ CheckNameAvailability/művelet||
|/CheckResourceUsage/action||
|/edgenodes/DELETE||
|/edgenodes/Read||
|/ edgenodes/írása||
|/operationresults/DELETE||
|/operationresults/profileresults/CheckResourceUsage/action||
|/operationresults/profileresults/delete||
|/operationresults/profileresults/endpointresults/CheckResourceUsage/action||
|/operationresults/profileresults/endpointresults/customdomainresults/delete||
|/operationresults/profileresults/endpointresults/customdomainresults/ DisableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/ EnableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/read||
|/operationresults/profileresults/endpointresults/customdomainresults/write||
|/operationresults/profileresults/endpointresults/delete||
|/operationresults/profileresults/endpointresults/Load/action||
|/operationresults/profileresults/endpointresults/originresults/delete||
|/operationresults/profileresults/endpointresults/originresults/read||
|/operationresults/profileresults/endpointresults/originresults/write||
|/operationresults/profileresults/endpointresults/PURGE/Action||
|/operationresults/profileresults/endpointresults/Read||
|/operationresults/profileresults/endpointresults/Start/Action||
|/operationresults/profileresults/endpointresults/STOP/Action||
|/operationresults/profileresults/endpointresults/ValidateCustomDomain/action||
|/operationresults/profileresults/endpointresults/Write||
|/operationresults/profileresults/GenerateSsoUri/action||
|/operationresults/profileresults/GetSupportedOptimizationTypes/action||
|/operationresults/profileresults/Read||
|/operationresults/profileresults/Write||
|/operationresults/Read||
|/ operationresults/írása||
|/Operations/Read||
|/profiles/CheckResourceUsage/action||
|/profiles/delete||
|/profiles/endpoints/CheckResourceUsage/action||
|/profiles/endpoints/customdomains/delete||
|/profiles/endpoints/customdomains/DisableCustomHttps/action||
|/profiles/endpoints/customdomains/EnableCustomHttps/action||
|/profiles/endpoints/customdomains/read||
|/profiles/endpoints/customdomains/write||
|/profiles/endpoints/delete||
|/Profiles/endpoints/Load/Action||
|/profiles/endpoints/origins/delete||
|/Profiles/endpoints/origins/Read||
|/Profiles/endpoints/origins/Write||
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti az erőforrás diagnosztikai beállításait|
|/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read|Microsoft.Cdn naplók beolvasása.|
|/Profiles/endpoints/PURGE/Action||
|/Profiles/endpoints/Read||
|/Profiles/endpoints/Start/Action||
|/Profiles/endpoints/STOP/Action||
|/profiles/endpoints/ValidateCustomDomain/action||
|/Profiles/endpoints/Write||
|/profiles/GenerateSsoUri/action||
|/profiles/GetSupportedOptimizationTypes/action||
|/Profiles/Read||
|/ profilok/írása||
|/ regisztrációs/művelet|Az előfizetés regisztrálása a CDN erőforrás-szolgáltatónál, és a CDN-profilok létrehozásának engedélyezése.|
|/ValidateProbe/action||

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Művelet | Leírás |
|---|---|
|/certificateOrders/Certificates/DELETE|Meglévő tanúsítvány törlése|
|/certificateOrders/Certificates/Read|A tanúsítványok listájának lekérdezése|
|/certificateOrders/Certificates/Write|Adja hozzá az új tanúsítványt, vagy egy meglévő frissítése|
|/ certificateOrders/törlése|Egy meglévő AppServiceCertificate törlése|
|/certificateOrders/Operations/Read|Lista összes műveleteket az app service-tanúsítvány regisztrálása|
|/ certificateOrders/Olvasás|A CertificateOrders listájának lekérdezése|
|/certificateOrders/reissue/Action|Hajtsa végre újból egy meglévő certificateorder|
|/certificateOrders/renew/Action|Egy meglévő certificateorder megújítása|
|/certificateOrders/resendEmail/Action|Tanúsítvány e-mail újraküldése|
|/certificateOrders/resendRequestEmails/Action|Küldje el újra a kérelmet e-mailt egy másik e-mail címet|
|/certificateOrders/resendRequestEmails/Action|App Service kiállított tanúsítvány hely lezárása beolvasása|
|/certificateOrders/retrieveCertificateActions/Action|A tanúsítvány műveletek listájának beolvasása|
|/certificateOrders/retrieveEmailHistory/Action|Tanúsítvány e-mail előzmények beolvasása|
|/certificateOrders/verifyDomainOwnership/Action|Tartomány tulajdonjogának ellenőrzése|
|/ certificateOrders/írása|Adja hozzá egy új certificateOrder vagy egy meglévő frissítése|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Az alkalmazás egyszerű kiépítés egyszerű szolgáltatásnév|
|/ regisztrációs/művelet|Az előfizetés a Microsoft Certificates erőforrás-szolgáltató regisztrálása|
|/validateCertificateRegistrationInformation/Action|Ellenőrizze a beszerzési tanúsítványobjektum lefolytatása nélkül|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Művelet | Leírás |
|---|---|
|/Capabilities/Read|Képességek megjelenítése|
|/checkDomainNameAvailability/action|Az adott tartománynév elérhetőségének ellenőrzése.|
|/domainNames/active/write|Az aktív tartománynév beállítása.|
|/domainNames/availabilitySets/read|Az erőforrás rendelkezésre állási csoportjának megjelenítése.|
|/domainNames/capabilities/read|A tartománynév-képességek megjelenítése|
|/domainNames/delete|Erőforrások tartománynevének eltávolítása.|
|/domainNames/extensions/delete|A tartománynév-kiterjesztések eltávolítása.|
|/domainNames/extensions/operationStatuses/read|A tartománynév-kiterjesztések műveleti állapotának beolvasása.|
|/domainNames/extensions/read|A tartománynév-kiterjesztéseket adja vissza.|
|/domainNames/extensions/write|Tartománynév-kiterjesztések hozzáadása.|
|/domainNames/internalLoadBalancers/delete|Új belső terheléselosztás eltávolítása.|
|/domainNames/internalLoadBalancers/operationStatuses/read|A tartománynevek belső terheléselosztói műveleti állapotának beolvasása.|
|/domainNames/internalLoadBalancers/read|A belső terheléselosztók beolvasása.|
|/domainNames/internalLoadBalancers/write|Új belső terheléselosztás létrehozása.|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|A tartománynevek elosztott terhelésű végpontcsoportjai műveleti állapotának beolvasása.|
|/domainNames/loadBalancedEndpointSets/read|Az elosztott terhelésű végpontcsoportok megjelenítése|
|/domainNames/read|Erőforrások tartománynevének visszaadása.|
|/domainNames/serviceCertificates/delete|Az alkalmazott szolgáltatási tanúsítványok törlése.|
|/domainNames/serviceCertificates/operationStatuses/read|A tartománynevek szolgáltatási tanúsítványai műveleti állapotának beolvasása.|
|/domainNames/serviceCertificates/read|Az alkalmazott szolgáltatási tanúsítványok visszaadása.|
|/domainNames/serviceCertificates/write|Alkalmazott szolgáltatási tanúsítványok hozzáadása vagy módosítása.|
|/domainNames/slots/delete|A megadott üzembe helyezési pont törlése.|
|/domainNames/slots/operationStatuses/read|A tartománynevek üzembe helyezési pontjai műveleti állapotának beolvasása.|
|/domainNames/slots/read|Üzembe helyezési pontok megjelenítése.|
|/domainNames/slots/roles/extensionReferences/delete|Eltávolítja az üzembe helyezési ponti szerepkör kiterjesztéshivatkozását.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|A tartománynevek üzembe helyezési ponti szerepkörei kiterjesztéshivatkozásai műveleti állapotának beolvasása.|
|/domainNames/slots/roles/extensionReferences/read|Az üzembe helyezési ponti szerepkör kiterjesztéshivatkozását adja vissza.|
|/domainNames/slots/roles/extensionReferences/write|Az üzembe helyezési ponti szerepkör kiterjesztéshivatkozásának hozzáadása vagy módosítása.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read|Diagnosztikai beállítások beolvasása.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write|Diagnosztikai beállítások hozzáadása vagy módosítása.|
|/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read|A metrikai meghatározások beolvasása.|
|/domainNames/slots/roles/read|Üzembe helyezési pont szerepkörének beolvasása.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|A tartománynevek üzembe helyezési ponti szerepkörei szerepkörpéldányai műveleti állapotának beolvasása.|
|/domainNames/slots/roles/roleInstances/read|Szerepkörpéldány beolvasása.|
|/domainNames/slots/roles/roleInstances/rebuild/action|A szerepkörpéldány újraépítése.|
|/domainNames/slots/roles/roleInstances/reimage/action|Szerepkörpéldány rendszerképének alaphelyzetbe állítása.|
|/domainNames/slots/roles/roleInstances/restart/action|A szerepkörpéldányok újraindítása.|
|/domainNames/slots/start/action|Üzembe helyezési pont indítása.|
|/domainNames/slots/state/start/write|Az üzembe helyezési pont állapotának leállítottra állítása.|
|/domainNames/slots/state/stop/write|Az üzembe helyezési pont állapotának elindítottra állítása.|
|/domainNames/slots/stop/action|Üzembe helyezési pont felfüggesztése.|
|/domainNames/slots/upgradeDomain/write|Verzióváltási tartomány feldolgozása.|
|/domainNames/slots/write|Telepítés létrehozása vagy frissítése.|
|/domainNames/swap/action|Előkészítési pont cseréje üzemelési pontra.|
|/domainNames/write|Erőforrások tartománynevének hozzáadása vagy módosítása.|
|/moveSubscriptionResources/action|Minden hagyományos erőforrás áthelyezése egy másik előfizetésbe.|
|/operatingSystemFamilies/read|Ez a művelet a Microsoft Azure-ban elérhető vendég operációsrendszer-családokat, valamint az egyes családokon belül elérhető operációsrendszer-verziókat listázza.|
|/operatingSystems/read|Ez a művelet a Microsoft Azure-ban jelenleg elérhető vendég operációsrendszer-verziókat listázza.|
|/Quotas/Read|Az előfizetés kvótájának beolvasása.|
|/ regisztrációs/művelet|Regisztrálás a Classic Compute szolgáltatónál|
|/resourceTypes/skus/read|Lekérdezi a támogatott erőforrástípusok termékváltozat-listáját.|
|/validateSubscriptionMoveAvailability/action|Annak ellenőrzése, hogy áthelyezhető-e az előfizetés hagyományos áthelyezéssel.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|A virtuális géphez társított hálózati biztonsági csoport törlése.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|A virtuális gépek társított hálózati biztonsági csoportjai műveleti állapotának beolvasása.|
|/virtualMachines/associatedNetworkSecurityGroups/read|A virtuális géphez társított hálózati biztonsági csoport lekérése.|
|/virtualMachines/associatedNetworkSecurityGroups/write|A virtuális géphez társított hálózati biztonsági csoport hozzáadása.|
|/virtualMachines/asyncOperations/Read|Lehetséges aszinkron műveletek beolvasása|
|/virtualMachines/attachDisk/Action|Adatlemez csatlakoztatása a virtuális géphez.|
|/virtualMachines/delete|Virtuális gépek eltávolítása.|
|/virtualMachines/detachDisk/action|Adatlemez leválasztása a virtuális gépről.|
|/virtualMachines/Disks/Read|Adatlemezek listájának beolvasása|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|Virtuális gép RDP-fájljának letöltése.|
|/virtualMachines/extensions/operationStatuses/read|A virtuálisgép-bővítmények műveleti állapotának beolvasása.|
|/virtualMachines/Extensions/Read|Virtuálisgép-bővítmény beolvasása.|
|/virtualMachines/Extensions/Write|Virtuálisgép-bővítmény hozzáadása.|
|/virtualMachines/Metrics/Read|Metrika beolvasása.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete|A hálózati interfészhez társított hálózati biztonsági csoport törlése.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/ operationStatuses/read|A virtuális gépek társított hálózati biztonsági csoportjai műveleti állapotának beolvasása.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read|A hálózati interfészhez társított hálózati biztonsági csoport lekérése.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write|A hálózati interfészhez társított hálózati biztonsági csoport hozzáadása.|
|/virtualMachines/operationStatuses/read|A virtuális gépek műveleti állapotának beolvasása.|
|/virtualMachines/performMaintenance/Action|A művelet elvégzi a virtuális gép karbantartását.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Diagnosztikai beállítások beolvasása.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Diagnosztikai beállítások hozzáadása vagy módosítása.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|A metrikai meghatározások beolvasása.|
|/virtualMachines/Read|Virtuális gépek listájának beolvasása.|
|/virtualMachines/redeploy/Action|A virtuális gép ismételt üzembe helyezése.|
|/virtualMachines/restart/Action|Virtuális gépek újraindítása.|
|/virtualMachines/shutdown/action|Virtuális gép rendszerének leállítása.|
|/virtualMachines/Start/Action|Virtuális gép elindítása.|
|/virtualMachines/STOP/Action|Virtuális gép leállítása.|
|/ virtuális gépek vannak/írása|Virtuális gépek hozzáadása vagy módosítása.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Művelet | Leírás |
|---|---|
|/gatewaySupportedDevices/read|Támogatott eszközök listájának beolvasása.|
|/networkSecurityGroups/delete|A hálózati biztonsági csoport törlése.|
|/networkSecurityGroups/operationStatuses/read|A hálózati biztonsági csoport műveleti állapotának beolvasása.|
|/networkSecurityGroups/Read|A hálózati biztonsági csoport lekérése.|
|/networkSecurityGroups/securityRules/delete|A biztonsági szabály törlése.|
|/networkSecurityGroups/securityRules/operationStatuses/read|A hálózati biztonsági csoport biztonsági szabályai műveleti állapotának beolvasása.|
|/networkSecurityGroups/securityRules/read|A biztonsági szabály beolvasása.|
|/networkSecurityGroups/securityRules/write|Egy biztonsági szabály hozzáadása vagy frissítése.|
|/networkSecurityGroups/write|Új hálózati biztonsági csoport hozzáadása.|
|/Quotas/Read|Az előfizetés kvótájának beolvasása.|
|/ regisztrációs/művelet|Regisztrálás a Classic Network szolgáltatónál|
|/reservedIps/delete|Fenntartott IP-cím törlése.|
|/reservedIps/join/action|Csatlakozás egy fenntartott IP-címhez|
|/reservedIps/link/action|Egy fenntartott IP-cím hivatkozása|
|/reservedIps/operationStatuses/read|A fenntartott IP-címek műveleti állapotának beolvasása.|
|/reservedIps/read|A fenntartott IP-címek beolvasása|
|/reservedIps/write|Új fenntartott IP-cím felvétele|
|/virtualNetworks/capabilities/read|Képességek megjelenítése|
|/virtualNetworks/checkIPAddressAvailability/action|Az adott IP-cím virtuális hálózaton belüli elérhetőségét ellenőrzi.|
|/virtualNetworks/delete|A virtuális hálózat törlése.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|Az ügyféltanúsítvány visszavonásának megszüntetése.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|Visszavont ügyféltanúsítványok beolvasása.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|Ügyféltanúsítvány visszavonása.|
|/virtualNetworks/gateways/clientRootCertificates/delete|A virtuális hálózati átjáró ügyféltanúsítványának törlése.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|Tanúsítvány letöltése ujjlenyomat útján.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|A virtuális hálózati átjáró tanúsítványcsomagjának listázása.|
|/virtualNetworks/gateways/clientRootCertificates/read|Ügyfél főtanúsítványainak megkeresése.|
|/virtualNetworks/gateways/clientRootCertificates/write|Ügyfél új főtanúsítványának feltöltése.|
|/virtualNetworks/gateways/connections/connect/action|Webhelyek közötti átjárókapcsolat létesítése.|
|/virtualNetworks/gateways/connections/disconnect/action|Webhelyek közötti átjárókapcsolat leválasztása.|
|/virtualNetworks/gateways/connections/read|Kapcsolatok listájának beolvasása.|
|/virtualNetworks/gateways/connections/test/action|Webhelyek közötti átjárókapcsolat tesztelése.|
|/virtualNetworks/gateways/delete|Virtuális hálózati átjáró törlése.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|Eszközkonfigurációs parancsfájl letöltése.|
|/virtualNetworks/gateways/downloadDiagnostics/action|Átjáródiagnosztika letöltése.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Kör szolgáltatáskulcsának beolvasása.|
|/virtualNetworks/gateways/listPackage/action|A virtuális hálózati átjárócsomag listázása.|
|/virtualNetworks/gateways/operationStatuses/read|A virtuális hálózati átjárók műveleti állapotának beolvasása.|
|/virtualNetworks/gateways/packages/read|A virtuális hálózati átjárócsomag beolvasása.|
|/virtualNetworks/gateways/read|Virtuális hálózati átjárók beolvasása.|
|/virtualNetworks/gateways/startDiagnostics/action|Virtuális hálózati átjáró diagnosztikájának indítása.|
|/virtualNetworks/gateways/stopDiagnostics/action|Virtuális hálózati átjáró diagnosztikájának leállítása.|
|/virtualNetworks/gateways/write|Virtuális hálózati átjáró hozzáadása.|
|/virtualNetworks/join/action|Csatlakozás a virtuális hálózathoz.|
|/virtualNetworks/operationStatuses/read|A virtuális hálózatok műveleti állapotának beolvasása.|
|/virtualNetworks/peer/action|Társviszony-létesítés két virtuális hálózat között.|
|/virtualNetworks/read|Virtuális hálózat beolvasása.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete|Az alhálózathoz társított hálózati biztonsági csoport törlése.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read|A virtuális hálózat alhálózatához társított hálózati biztonsági csoport műveleti állapotának beolvasása.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/read|Az alhálózathoz társított hálózati biztonsági csoport lekérése.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/write|Az alhálózathoz társított hálózati biztonsági csoport hozzáadása.|
|/virtualNetworks/write|Új virtuális hálózat hozzáadása.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Művelet | Leírás |
|---|---|
|/Capabilities/Read|Képességek megjelenítése|
|/checkStorageAccountAvailability/action|A tárfiók elérhetőségének ellenőrzése.|
|/Disks/Read|A tárfióklemez beolvasása.|
|/Images/Read|A lemezkép visszaadása.|
|/osImages/Read|A operációs rendszerkép beolvasása.|
|/publicImages/Read|A nyilvános virtuális gép lemezképének beolvasása.|
|/Quotas/Read|Az előfizetés kvótájának beolvasása.|
|/ regisztrációs/művelet|Regisztrálás egy hagyományos tárolóba.|
|/storageAccounts/delete|Tárfiók törlése.|
|/storageAccounts/disks/delete|A megadott tárfióklemez törlése.|
|/storageAccounts/disks/operationStatuses/read|Az erőforrás műveleti állapotának beolvasása.|
|/storageAccounts/Disks/Read|A tárfióklemez beolvasása.|
|/storageAccounts/disks/write|Új tárfióklemez felvétele.|
|/storageAccounts/images/delete|A megadott tárfióklemezkép törlése.|
|/storageAccounts/Images/Read|A tárfióklemezkép beolvasása.|
|/storageAccounts/listKeys/action|Tárfiókok elérési kulcsainak listázása.|
|/storageAccounts/operationStatuses/read|Az erőforrás műveleti állapotának beolvasása.|
|/storageAccounts/osImages/delete|A megadott tárfiók operációsrendszer-lemezképének törlése.|
|/storageAccounts/osImages/Read|A tárfiók operációsrendszer-lemezképének visszaadása.|
|/storageAccounts/Read|Tárfiók visszaadása az adott fiókhoz.|
|/storageAccounts/regenerateKey/action|Tárfiók meglévő elérési kulcsainak újragenerálása.|
|/storageAccounts/services/diagnosticSettings/read|Diagnosztikai beállítások beolvasása.|
|/storageAccounts/services/diagnosticSettings/write|Diagnosztikai beállítások hozzáadása vagy módosítása.|
|/storageAccounts/services/metricDefinitions/read|A metrikai meghatározások beolvasása.|
|/storageAccounts/Services/Metrics/Read|Metrika beolvasása.|
|/storageAccounts/services/read|Elérhető szolgáltatások beolvasása.|
|/storageAccounts/write|Új tárfiók hozzáadása.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Művelet | Leírás |
|---|---|
|/accounts/DELETE|API-fiókok törlése|
|/accounts/listKeys/Action|Kulcsok listázása|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításának beolvasása.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|A Cognitive Serviceshez elérhető metrikák beolvasása.|
|/accounts/Read|API-fiókok beolvasása.|
|/accounts/regenerateKey/Action|Kulcs újragenerálása|
|/accounts/skus/Read|Az egy adott meglévő erőforráshoz rendelkezésre álló termékváltozatok beolvasása.|
|/accounts/usages/Read|Egy meglévő erőforrás kvótahasználatának beolvasása.|
|/ fiókok/írása|API-fiókok írása.|
|/Locations/checkSkuAvailability/Action|Egy előfizetéshez elérhető termékváltozatok beolvasása.|
|Műveletek/olvasása|Elérhető műveletek listázása|
|/ regisztrációs/művelet|Az előfizetés kognitív szolgáltatások regisztrálása|
|/skus/Read|Elérhető termékváltozatok olvas kognitív szolgáltatások számára.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Művelet | Leírás |
|---|---|
|RateCard/olvasása|Értéket ad vissza adatokat, Erőforrás-kijelző metaadatok és az adott előfizetéshez tartozó díjszabás kínálnak.|
|/UsageAggregates/read|Lekéri a Microsoft Azure használat előfizetés. Az eredmény összesítések tartalmaz a kapcsolódó információk, egy adott időtartományt a használati adatok, az előfizetés és az erőforrás.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Művelet | Leírás |
|---|---|
|/availabilitySets/delete|Törli a rendelkezésre állási csoportot|
|/availabilitySets/Read|Egy adott rendelkezésre állási csoport tulajdonságainak beolvasása|
|/availabilitySets/vmSizes/read|A virtuális gépek a rendelkezésre állási csoportban való létrehozásához vagy frissítéséhez használható méretek listázása|
|/availabilitySets/write|Létrehoz egy új vagy frissít egy meglévő rendelkezésre állási csoportot|
|/disks/beginGetAccess/action|Lemez SAS URI-jának beolvasása blobhozzáféréshez|
|/disks/delete|A lemez törlése|
|/disks/endGetAccess/action|A lemez SAS URI-jának visszavonása|
|/Disks/Read|A lemez tulajdonságainak beolvasása|
|/ lemezek írása|Új lemez létrehozása vagy meglévő lemez frissítése|
|/Images/DELETE|A lemezkép törlése|
|/Images/Read|A lemezkép tulajdonságainak beolvasása|
|/ képek/írása|Új lemezkép létrehozása vagy meglévő frissítése|
|/Locations/capsOperations/Read|Egy aszinkron Caps művelet állapotát olvassa be|
|/Locations/diskOperations/Read|Lemez aszinkron művelet állapotát olvassa be|
|/Locations/Operations/Read|Egy aszinkron művelet állapotát olvassa be|
|/Locations/Publishers/artifacttypes/offers/Read|A Platform képet nyújtanak tulajdonságainak beolvasása|
|/Locations/Publishers/artifacttypes/offers/skus/Read|A Platform kép Sku tulajdonságainak beolvasása|
|/locations/publishers/artifacttypes/offers/skus/versions/read|A Platform lemezkép verziója tulajdonságainak beolvasása|
|/Locations/Publishers/artifacttypes/Types/Read|A VMExtension típus tulajdonságainak beolvasása|
|/Locations/Publishers/artifacttypes/Types/versions/Read|A VMExtension verzió tulajdonságainak beolvasása|
|/Locations/Publishers/Read|A közzétevő tulajdonságainak beolvasása|
|/Locations/runCommands/Read|A helyen elérhető futtatási parancsok listázása|
|/Locations/usages/Read|Az előfizetés adott helyen lévő számítási erőforrásaihoz tartozó szolgáltatások korlátait és az aktuális felhasználási mennyiségeket olvassa be|
|/Locations/vmSizes/Read|Listázza az adott helyen elérhető virtuálisgép-méreteket|
|/Operations/Read|A Microsoft.Compute erőforrás-szolgáltató elérhető műveleteinek listázása|
|/ regisztrációs/művelet|Az előfizetés regisztrálása a Microsoft.Compute erőforrás-szolgáltatónál|
|/restorePointCollections/delete|A visszaállításipont-gyűjtemény és az abban található visszaállítási pontok törlése|
|/restorePointCollections/read|Egy visszaállításipont-gyűjtemény tulajdonságainak beolvasása|
|/restorePointCollections/restorePoints/delete|A visszaállítási pont törlése|
|/restorePointCollections/restorePoints/read|Egy visszaállítási pont tulajdonságainak beolvasása|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Egy visszaállítási pont tulajdonságainak beolvasása a blob SAS URI azonosítóival|
|/restorePointCollections/restorePoints/write|Új visszaállítási pont létrehozása|
|/restorePointCollections/write|Egy új visszaállításipont-gyűjtemény létrehozása, vagy egy meglévő módosítása|
|/sharedVMImages/delete|Törli a SharedVMImage|
|/sharedVMImages/read|Egy SharedVMImage tulajdonságainak beolvasása|
|/sharedVMImages/versions/delete|Egy SharedVMImageVersion törlése|
|/sharedVMImages/versions/read|Egy SharedVMImageVersion tulajdonságainak beolvasása|
|/sharedVMImages/versions/replicate/action|Egy SharedVMImageVersion célrégiók replikálása|
|/sharedVMImages/versions/write|Hozzon létre egy új SharedVMImageVersion vagy egy meglévő frissítése|
|/sharedVMImages/write|Létrehoz egy új SharedVMImage vagy frissít egy meglévő|
|/skus/Read|Az előfizetéshez tartozó Microsoft.Compute SKU elérhető listájának beolvasása|
|/snapshots/beginGetAccess/action|A SAS URI-jának a pillanatkép beolvasása blobhozzáféréshez|
|/snapshots/delete|A pillanatkép törlése|
|/snapshots/endGetAccess/action|A pillanatkép SAS URI visszavonása|
|/snapshots/read|Pillanatkép tulajdonságainak beolvasása|
|/snapshots/write|Új pillanatkép létrehozása vagy meglévő pillanatkép frissítése|
|/virtualMachines/Capture/Action|Rögzíti a virtuális gépet úgy, hogy másolatot készít a virtuális merevlemezekről és létrehoz egy sablont, mellyel hasonló virtuális gépek hozhatók létre|
|/virtualMachines/convertToManagedDisks/action|A virtuális gép blobalapú lemezeinek konvertálása felügyelt lemezekké|
|/virtualMachines/deallocate/Action|Kikapcsolja a virtuális gépet és felszabadítja a számítási erőforrásokat|
|/virtualMachines/delete|Törli a virtuális gépet|
|/virtualMachines/extensions/delete|Törli a virtuálisgép-bővítményt|
|/virtualMachines/Extensions/Read|Egy virtuálisgép-bővítmény tulajdonságait olvassa be|
|/virtualMachines/Extensions/Write|Létrehoz egy új vagy frissít egy meglévő virtuálisgép-bővítményt|
|/virtualMachines/generalize/action|Általánosítottra állítja a virtuális gép állapotát és előkészíti a virtuális gépet a rögzítéshez|
|/virtualMachines/instanceView/read|A virtuális gép és az erőforrásai részletes futási állapotát olvassa be|
|/virtualMachines/performMaintenance/Action|Karbantartási művelet végrehajtása a virtuális gépen.|
|/virtualMachines/powerOff/action|Kikapcsolja a virtuális gép. Vegye figyelembe, hogy a virtuális gép továbbra is fizetnie kell ezért.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Egy virtuális gép metrikadefinícióinak beolvasása|
|/virtualMachines/Read|Egy virtuális gép tulajdonságait olvassa be|
|/virtualMachines/redeploy/Action|A virtuális gép újbóli üzembe helyezése|
|/virtualMachines/reimage/action|Különbséglemezt használó virtuális gép rendszerképét állítja alaphelyzetbe.|
|/virtualMachines/restart/Action|Újraindítja a virtuális gépet|
|/virtualMachines/runCommand/action|Egy előre definiált parancsfájl végrehajtása a virtuális gépen|
|/virtualMachines/Start/Action|Elindítja a virtuális gépet|
|/virtualMachines/vmSizes/read|Listázza az elérhető méreteket, melyekre a virtuális gép frissíthető|
|/ virtuális gépek vannak/írása|Létrehoz egy új virtuális gépet vagy frissít egy meglévő virtuális gépet|
|/virtualMachineScaleSets/deallocate/action|A virtuálisgép-méretezési csoport példányaihoz hozzárendelt számítási erőforrások kikapcsolása és felszabadítása |
|/virtualMachineScaleSets/delete|A virtuálisgép-méretezési csoport törlése|
|/virtualMachineScaleSets/delete/action|A virtuálisgép-méretezési csoport példányainak törlése|
|/virtualMachineScaleSets/extensions/delete|A virtuálisgép-méretezési csoport bővítményének törlése|
|/virtualMachineScaleSets/extensions/read|Egy virtuálisgép-méretezési csoport bővítménye tulajdonságainak beolvasása|
|/virtualMachineScaleSets/extensions/write|Egy virtuálisgép-méretezési csoport egy új bővítményének létrehozása, vagy egy meglévő frissítése|
|/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action|A virtuálisgép-méretezési Beragadt egy folyamatban lévő frissítés befejezéséhez a service fabric platform frissítés tartományai manuálisan ismerteti|
|/virtualMachineScaleSets/instanceView/read|A virtuálisgép-méretezési csoport példánynézetének beolvasása|
|/virtualMachineScaleSets/manualUpgrade/Action|A példányok manuális frissítése a virtuálisgép-méretezési csoport legújabb modelljére|
|/virtualMachineScaleSets/networkInterfaces/read|Egy virtuálisgép-méretezési csoportban az összes hálózati adapterek tulajdonságainak beolvasása|
|/virtualMachineScaleSets/osUpgradeHistory/read|Lekéri a az operációs rendszer frissítési előzmények egy virtuálisgép-méretezési csoportban|
|/virtualMachineScaleSets/performMaintenance/action|Tervezett karbantartást végez a virtuálisgép-méretezési csoport példányait|
|/virtualMachineScaleSets/powerOff/action|A virtuálisgép-méretezési csoport példányainak kikapcsolása|
|/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read|Egy virtuálisgép-méretezési csoport metrikadefinícióinak beolvasása|
|/virtualMachineScaleSets/publicIPAddresses/read|Egy virtuálisgép-méretezési csoportban az összes nyilvános IP-címek tulajdonságainak beolvasása|
|/virtualMachineScaleSets/read|Virtuálisgép-méretezési csoport tulajdonságainak beolvasása|
|/virtualMachineScaleSets/redeploy/action|Telepítse újra a virtuálisgép-méretezési csoport példányait|
|/virtualMachineScaleSets/reimage/action|Ezzel a művelettel alaphelyzetbe állíthatja a virtuálisgép-méretezési csoport példányainak rendszerképét|
|/virtualMachineScaleSets/restart/action|Virtuálisgép-méretezési csoport példányainak újraindítása|
|/virtualMachineScaleSets/rollingUpgrades/cancel/action|Ezzel a művelettel megszakítja a virtuálisgép-méretezési csoport működés közbeni frissítését|
|/virtualMachineScaleSets/rollingUpgrades/read|Virtuálisgép-méretezési csoport legutóbbi működés közbeni frissítési állapotának beolvasása|
|/virtualMachineScaleSets/scale/action|Annak ellenőrzése, hogy van-e olyan meglévő virtuálisgép-méretezési csoport, mely horizontálisan le- vagy felskálázható a megadott példányszámra|
|/virtualMachineScaleSets/skus/read|Egy meglévő virtuálisgép-méretezési csoport érvényes termékváltozatainak listázása|
|/virtualMachineScaleSets/start/action|Virtuálisgép-méretezési csoport példányainak elindítása|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|Kikapcsol egy virtuális gépet egy virtuálisgép-méretezési csoportban, és felszabadítja az ahhoz tartozó számítási erőforrásokat.|
|/virtualMachineScaleSets/virtualMachines/delete|Töröl egy virtuális gépet egy virtuálisgép-méretezési csoportban.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|Egy virtuálisgép-méretezési csoport egy virtuális gépének példányait tartalmazó nézetet adja meg.|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ ipConfigurations/publicIPAddresses/read|Nyilvános IP-cím létre virtuálisgép-méretezési csoport tulajdonságainak beolvasása. Virtuálisgép-méretezési csoport hozhat létre, legfeljebb egy nyilvános IP-cím ipconfiguration (magánhálózati IP-címe)|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read|Egy hálózati adapter virtuálisgép-méretezési készlet használatával létrehozott összes vagy egy IP-konfiguráció tulajdonságainak beolvasása. IP-konfigurációk meghatároznia magánhálózati IP-címek|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/read|Vagy az összes hálózati kapcsolat a virtuálisgép-méretezési készlet használatával létrehozott virtuális gép tulajdonságainak beolvasása|
|/virtualMachineScaleSets/virtualMachines/performMaintenance/action|Tervezett karbantartást végez a virtuálisgép-méretezési csoportban lévő virtuálisgép-példányt|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|Kikapcsol egy virtuálisgép-példányt egy virtuálisgép-méretezési csoportban.|
|/virtualMachineScaleSets/virtualMachines/providers/ Microsoft.Insights/metricDefinitions/read|Egy méretezési csoportbeli virtuális gép metrikadefinícióinak beolvasása|
|/virtualMachineScaleSets/virtualMachines/read|Egy virtuálisgép-méretezési csoport egy virtuális gépének tulajdonságait adja meg|
|/virtualMachineScaleSets/virtualMachines/redeploy/action|A virtuálisgép-méretezési csoportban lévő virtuálisgép-példányt redeploys|
|/virtualMachineScaleSets/virtualMachines/reimage/action|A művelet alaphelyzetbe állítja a virtuálisgép-méretezési csoportban lévő virtuálisgép-példány rendszerképét.|
|/virtualMachineScaleSets/virtualMachines/restart/action|Újraindít egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példányt.|
|/virtualMachineScaleSets/virtualMachines/start/action|Elindít egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példányt.|
|/virtualMachineScaleSets/virtualMachines/write|Egy virtuálisgép-méretezési csoportban lévő virtuális gép tulajdonságainak frissítése|
|/virtualMachineScaleSets/write|Új virtuálisgép-méretezési csoport létrehozása vagy egy meglévő frissítése|

## <a name="microsoftconsumption"></a>Microsoft.Consumption

| Művelet | Leírás |
|---|---|
|/balances/Read|A számlázott időszak egy felügyeleti csoport összegzése kihasználtsága a listában.|
|/budgets/Read|Az előfizetés vagy a felügyeleti csoport költségvetések listában.|
|/ költségvetések/írása|Hoz, frissítése és törlése a költségvetések előfizetés vagy a felügyeleti csoport.|
|/marketplaces/Read|Egy hatókör EA és WebDirect előfizetésekhez piactér erőforrás látogatottsági adatait listázza.|
|/Operations/Read|Minden támogatott műveleteket Microsoft.Consumption erőforrás-szolgáltató a listában.|
|/pricesheets/Read|Az előfizetés vagy egy felügyeleti csoport Pricesheets adatainak listában.|
|/reservationDetails/read|A szalagfelhasználási adatok foglalás sorrend, illetve a felügyeleti csoportok által fenntartott példányok felsorolása. A részletek adata nap szintenként példányonként.|
|/reservationSummaries/read|Foglalási sorrend, illetve a felügyeleti csoportok által fenntartott példányok összefoglaló kihasználtsága a listában. Az összefoglaló adatokat vagy a napi vagy havi szinten.|
|/reservationTransactions/read|A tranzakció előzmények felügyeleti csoportok által fenntartott példányok felsorolása.|
|/terms/Read|Listázza az előfizetés vagy a felügyeleti csoport feltételeit.|
|/usageDetails/read|A használat részleteiről EA és WebDirect előfizetésekhez hatókör listában.|

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

| Művelet | Leírás |
|---|---|
|/containerGroups/containers/Logs/Read|Egy adott tároló naplóinak beolvasása.|
|/containerGroups/delete|Egy adott tárolócsoport törlése.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read|A tároló csoport diagnosztikai beállításának beolvasása.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti a a tároló csoport diagnosztikai beállításai.|
|/containerGroups/providers/Microsoft.Insights/metricDefinitions/read|A tárolócsoport az elérhető metrikai meghatározások beolvasása.|
|/containerGroups/Read|Az összes tárolócsoport beolvasása.|
|/ containerGroups/írása|Egy adott tárolócsoport létrehozása vagy frissítése.|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Művelet | Leírás |
|---|---|
|/checkNameAvailability/Read|Ellenőrzi, hogy a tároló neve használható.|
|/Locations/operationResults/Read|Lekérdezi egy aszinkron művelet eredménye|
|/Operations/Read|Felsorolja az összes Azure tároló beállításjegyzék REST API műveletek|
|/ regisztrációs/művelet|A tároló beállításjegyzék erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a tároló nyilvántartó létrehozását.|
|/registries/delete|Egy tároló beállításjegyzék törli.|
|/registries/eventGridFilters/delete|Egy esemény rács szűrő töröl egy tároló beállításjegyzék.|
|/registries/eventGridFilters/read|A megadott rács szűrő tulajdonságait olvassa be, vagy a megadott tároló beállításjegyzék összes esemény rács szűrők listája.|
|/registries/eventGridFilters/write|Létrehozza vagy frissíti a tároló beállításjegyzék rács esemény szűrőjének a megadott paraméterekkel.|
|/registries/listCredentials/action|Megjeleníti a megadott tároló beállításjegyzék bejelentkezési hitelesítő adataihoz.|
|/registries/listUsages/Read|Megjeleníti a megadott tároló beállításjegyzék a a kvóta módjait.|
|/registries/operationStatuses/Read|A beállításjegyzék aszinkron műveleti állapotának beolvasása|
|/registries/Read|A megadott tároló beállításjegyzék tulajdonságait olvassa be, vagy a megadott erőforráscsoport vagy előfizetés összes tároló nyilvántartó sorolja fel.|
|/registries/regenerateCredential/action|A bejelentkezési hitelesítő adatait a megadott tároló beállításjegyzék egyik újragenerálása.|
|/registries/replications/delete|Egy tároló beállításjegyzék törli a replikációt.|
|/registries/replications/operationStatuses/Read|A replikációs aszinkron műveleti állapotának beolvasása|
|/registries/replications/Read|A megadott replikációs tulajdonságait olvassa be, vagy a megadott tároló rendszerleíró összes replikációk listáját.|
|/registries/replications/Write|Létrehozza vagy frissíti a tároló beállításjegyzékbeli replikálása a megadott paraméterekkel.|
|/registries/webhooks/delete|A webhook törlése egy tároló beállításjegyzékből.|
|/registries/webhooks/getCallbackConfig/action|A konfigurációs szolgáltatás URI és az egyéni fejlécet lekérdezi a webhook.|
|/registries/webhooks/listEvents/action|A megadott webhook megőrző legutóbbi események listája.|
|/registries/webhooks/operationStatuses/read|A webhook aszinkron műveleti állapotának beolvasása|
|/registries/webhooks/ping/action|A webhook küldendő egy ping esemény váltja ki.|
|/registries/webhooks/read|A megadott webhook tulajdonságait olvassa be, vagy a megadott tároló rendszerleíró összes webhookok listáját.|
|/registries/webhooks/write|Létrehozza vagy frissíti a webhook a tároló beállításjegyzékbeli a megadott paraméterekkel.|
|/ nyilvántartó/írása|Létrehozza vagy frissíti a tároló beállításjegyzékbeli a megadott paraméterekkel.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Művelet | Leírás |
|---|---|
|/containerServices/delete|A megadott Tárolószolgáltatás törlése|
|/containerServices/read|A megadott Tárolószolgáltatás beolvasása|
|/containerServices/write|Tárolása vagy frissítése a megadott Tárolószolgáltatás|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Művelet | Leírás |
|---|---|
|/Applications/DELETE|Törlési művelet|
|/Applications/listSecrets/Action|Titkos kulcsok listázása|
|/applications/listSingleSignOnToken/action|Olvassa el a jogkivonatok az egyszeri bejelentkezés|
|/Applications/Read|Olvasási művelet|
|/ applications/írása|Írási művelet|
|/ applications/írása|Írási művelet|
|/ listCommunicationPreference/művelet|Lista kommunikációs beállításokat|
|/Operations/Read|olvasási műveletek|
|/updateCommunicationPreference/action|Update kommunikációs beállítás|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Művelet | Leírás |
|---|---|
|/hubs/adobemetadata/action|Létrehozása vagy frissítése bármely Azure felhasználói Insights Adobe metaadatok|
|/hubs/adobemetadata/read|Minden Azure felhasználói Insights Adobe metaadatot olvasása|
|/hubs/authorizationPolicies/delete|Bármely Azure felhasználói Insights megosztott hozzáférési aláírást házirend törlése|
|/hubs/authorizationPolicies/Read|Azure felhasználói elemzéseket megosztott hozzáférési aláírást házirend olvasása|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Azure felhasználói Insights megosztott hozzáférési aláírást házirend elsődleges kulcs újragenerálása|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Azure felhasználói Insights megosztott hozzáférési aláírást házirend másodlagos kulcs újragenerálása|
|/hubs/authorizationPolicies/Write|Hozzon létre vagy bármely Azure felhasználói Insights megosztott hozzáférési aláírást házirend frissítése|
|/hubs/Connectors/Activate/Action|Bármely Azure felhasználói Insights-összekötő aktiválása|
|/hubs/Connectors/Activate/Action|Bármely Azure felhasználói Insights-összekötő aktiválása|
|/hubs/connectors/delete|Bármely Azure felhasználói Insights-összekötő törlése|
|/hubs/connectors/getruntimestatus/action|Bármely Azure felhasználói Insights-összekötő futási állapotának beolvasása|
|/hubs/connectors/mappings/activate/action|Bármely Azure felhasználói Insights összekötő hozzárendelés aktiválásához|
|/hubs/connectors/mappings/delete|Bármely Azure felhasználói Insights összekötő leképezés törlése|
|/hubs/Connectors/Mappings/Operations/Read|Bármely Azure felhasználói Insights összekötő leképezési műveleti eredmény olvasása|
|/hubs/Connectors/Mappings/Read|Olvassa el a bármely Azure felhasználói Insights összekötő leképezése|
|/hubs/Connectors/Mappings/Write|Hozzon létre vagy frissítési bármely Azure felhasználói Insights összekötő leképezés|
|/hubs/Connectors/Operations/Read|Bármely Azure felhasználói Insights-összekötő műveleti eredmény olvasása|
|/hubs/Connectors/Read|Olvassa el a bármely Azure felhasználói Insights-összekötő|
|/hubs/connectors/saveauthinfo/action|Létrehozása vagy frissítése bármely Azure felhasználói Insights összekötő hitelesítési átjárókapcsolat adatai|
|/hubs/connectors/update/action|Bármely Azure felhasználói Insights-összekötő frissítése|
|/hubs/Connectors/Write|Létrehozása vagy frissítése bármely Azure felhasználói Insights-összekötő|
|/hubs/crmmetadata/action|Létrehozni vagy frissíteni az Azure felhasználói Insights Crm-metaadatok|
|/hubs/crmmetadata/Read|Olvassa el az Azure felhasználói Insights Crm-metaadatok|
|/hubs/DELETE|Bármely Azure felhasználói Insights Hub törlése|
|/hubs/gdpr/delete|Bármely Azure felhasználói Insights Gdpr törlése|
|/hubs/GDPR/Read|Bármely Azure felhasználói Insights Gdpr olvasása|
|/hubs/GDPR/Write|Hozzon létre vagy bármely Azure felhasználói Insights Gdpr frissítése|
|/hubs/getbillingcredits/read|Az Azure felhasználói Insights Hub számlázási jóváírásokat kap|
|/hubs/getbillinghistory/read|Az Azure felhasználói Insights Hub számlázási előzmények beolvasása|
|/hubs/images/delete|Bármely Azure felhasználói Insights lemezkép törlése|
|/hubs/Images/Read|Olvassa el az Azure felhasználói Insights képet|
|/hubs/Images/Write|Létrehozni vagy frissíteni az Azure felhasználói Insights képet|
|/hubs/interactions/delete|Bármely azure Insights ügyfélkapcsolati törlése|
|/hubs/interactions/operations/read|Bármely Azure Insights kapcsolat az ügyféllel műveleti eredmény olvasása|
|/hubs/interactions/Read|Olvassa el az Azure felhasználói Insights beavatkozás|
|/hubs/interactions/suggestrelationshiplinks/action|Bármely Azure Insights ügyfélkapcsolati javasolni kapcsolat hivatkozások|
|/hubs/interactions/Write|Létrehozása vagy frissítése Insights Azure felhasználói beavatkozás|
|/hubs/kpi/delete|Bármely Azure felhasználói Insights fő teljesítménymutató törlése|
|/hubs/KPI/Operations/Read|Bármely Azure felhasználói Insights a fő teljesítménymutatók műveleti eredmény olvasása|
|/hubs/KPI/read|Bármely Azure felhasználói Insights fő teljesítménymutató olvasása|
|/hubs/kpi/reprocess/action|Bármely Azure felhasználói Insights fő teljesítménymutatók újból feldolgozza|
|/hubs/KPI/Write|Létrehozása vagy frissítése bármely Azure felhasználói Insights fő teljesítménymutató|
|/hubs/links/delete|Bármely Azure felhasználói Insights hivatkozások törlése|
|/hubs/Links/Operations/Read|Bármely Azure felhasználói Insights hivatkozások műveleti eredmény olvasása|
|/hubs/Links/Read|Olvassa el az Azure felhasználói Insights hivatkozások|
|/hubs/Links/Write|Hozzon létre vagy a frissítés az Azure-ügyfél|
|/hubs/msemetadata/Action|Létrehozása vagy frissítése bármely Azure felhasználói Insights Mse metaadatok|
|/hubs/msemetadata/read|Minden Azure felhasználói Insights Mse metaadatot olvasása|
|/hubs/operationresults/Read|Az Azure felhasználói Insights Hub művelet eredmények|
|/hubs/predictions/delete|Bármely Azure felhasználói Insights előrejelzéseket törlése|
|/hubs/predictions/Operations/Read|Bármely Azure felhasználói Insights előrejelzéseket műveleti eredmény olvasása|
|/hubs/predictions/Read|Bármely Azure felhasználói Insights előrejelzéseket olvasása|
|/hubs/predictions/write|Létrehozása vagy frissítése bármely Azure felhasználói előrejelzés|
|/hubs/predictivematchpolicies/delete|Bármely Azure felhasználói Insights prediktív egyezés házirendek törlése|
|/hubs/predictivematchpolicies/Operations/Read|Bármely Azure felhasználói Insights prediktív egyezés házirendek műveleti eredmény olvasása|
|/hubs/predictivematchpolicies/Read|Minden Azure felhasználói Insights prediktív egyezés házirendet olvasása|
|/hubs/predictivematchpolicies/Write|Bármely Azure felhasználói Insights prediktív egyezés irányelveinek létrehozása vagy frissítése|
|/hubs/profiles/delete|Bármely Azure felhasználói Insights profil törlése|
|/hubs/Profiles/Operations/Read|Bármely Azure felhasználói Insights profil műveleti eredmény olvasása|
|/hubs/Profiles/Read|Bármely Azure felhasználói Insights profil olvasása|
|/hubs/Profiles/Write|Minden Azure felhasználói Insights profil írása|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|Naplók erőforrás beolvasása|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Az erőforrás elérhető metrikai meghatározások beolvasása|
|/hubs/Read|Bármely Azure felhasználói Insights Hub olvasása|
|/hubs/relationshiplinks/delete|Bármely Azure felhasználói Insights kapcsolat hivatkozások törlése|
|/hubs/relationshiplinks/Operations/Read|Bármely Azure felhasználói Insights kapcsolat hivatkozások műveleti eredmény olvasása|
|/hubs/relationshiplinks/Read|Olvassa el az Azure felhasználói Insights kapcsolat hivatkozások|
|/hubs/relationshiplinks/Write|Hozzon létre vagy a frissítés az Azure felhasználói Insights kapcsolat|
|/hubs/relationships/delete|Minden Azure felhasználói Insights kapcsolat törlése|
|/hubs/Relationships/Operations/Read|Bármely Azure Insights ügyfélkapcsolatok műveleti eredmény olvasása|
|/hubs/Relationships/Read|Bármely Azure Insights ügyfélkapcsolatok olvasása|
|/hubs/Relationships/Write|Létrehozása vagy frissítése bármely Azure Insights ügyfélkapcsolatok|
|/hubs/roleAssignments/DELETE|Bármely Azure felhasználói Insights Rbac-hozzárendelés törlése|
|/hubs/roleAssignments/Operations/Read|Bármely Azure felhasználói Insights Rbac-hozzárendelés műveleti eredmény olvasása|
|/hubs/roleAssignments/Read|Olvassa el a bármely Azure felhasználói Insights Rbac-hozzárendelés|
|/hubs/roleAssignments/Write|Hozzon létre, vagy bármely Azure felhasználói Insights Rbac-hozzárendelés módosítása|
|/hubs/Roles/Read|Olvassa el a bármely Azure felhasználói Insights Rbac-szerepkörök|
|/hubs/salesforcemetadata/action|Létrehozása vagy frissítése bármely Azure felhasználói Insights SalesForce metaadatok|
|/hubs/salesforcemetadata/read|Minden Azure felhasználói Insights SalesForce metaadatot olvasása|
|/hubs/segments/DELETE|Bármely Azure Insights ügyfélszegmensek törlése|
|/hubs/segments/Dynamic/Action|Felügyeleti bármely Azure felhasználói Insight dinamikus szegmensek|
|/hubs/segments/Read|Bármely Azure Insights ügyfélszegmensek olvasása|
|/hubs/segments/static/Action|Felügyeleti bármely Azure felhasználói Insight statikus szegmensek|
|/hubs/segments/Write|Bármely Azure felhasználói Insights szegmensek létrehozása és módosítása|
|/hubs/sqlconnectionstrings/delete|Bármely Azure felhasználói Insights SqlConnectionStrings törlése|
|/hubs/sqlconnectionstrings/read|Bármely Azure felhasználói Insights SqlConnectionStrings olvasása|
|/hubs/sqlconnectionstrings/write|Hozzon létre vagy bármely Azure felhasználói Insights SqlConnectionStrings frissítése|
|/hubs/suggesttypeschema/Action|Hozzon létre típus séma felajánlja a mintaadatokat|
|/hubs/tenantmanagement/Read|Bármely Azure felhasználói Insights hub beállításainak kezelése|
|/hubs/views/delete|Bármely Azure felhasználói Insights alkalmazás nézet törlése|
|/hubs/Views/Read|Olvassa el a bármely Azure felhasználói Insights alkalmazás megtekintése|
|/hubs/Views/Write|Hozzon létre vagy bármely Azure felhasználói Insights alkalmazás nézet frissítése|
|/hubs/widgettypes/Read|Olvassa el a bármely Azure felhasználói Insights App Widget típusa|
|/ hubok/írása|Hozzon létre vagy bármely Azure felhasználói Insights központ frissítése|
|/Operations/Read|Olvassa el az Azure felhasználói Insights Api Metadatas|
|/ regisztrációs/művelet|Az ügyfél Insights erőforrás-szolgáltató előfizetésének regisztrálása és létrehozását lehetővé tevő ügyfél Insights-erőforrások|
|/ unregister/művelet|Az ügyfél Insights erőforrás-szolgáltató az előfizetés regisztrációjának törlése|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Művelet | Leírás |
|---|---|
|/catalogs/DELETE|Törli a katalógusban.|
|/catalogs/Read|Tulajdonságok beolvasása katalógus vagy a katalógusban az előfizetéshez vagy erőforráscsoporthoz.|
|/ katalógusok/írása|Katalógus létrehozza vagy frissíti, a címkéket és a katalógus tulajdonságait.|
|/ checkNameAvailability/művelet|Bérlő katalógus neve rendelkezésre állását ellenőrzi.|
|/Operations/Read|Microsoft.DataCatalog erőforrás-szolgáltató elérhető műveleteinek listázása.|
|/ regisztrációs/művelet|Az előfizetés regisztrálása a Microsoft.DataCatalog erőforrás-szolgáltató.|
|/ unregister/művelet|Előfizetés Microsoft.DataCatalog erőforrás-szolgáltató regisztrációjának törlése.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Művelet | Leírás |
|---|---|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/datafactories/providers/Microsoft.Insights/metricDefinitions/read|A datafactories elérhető metrikai meghatározások beolvasása|
|/factories/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/factories/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/factories/providers/Microsoft.Insights/logDefinitions/read|Előállítók naplók beolvasása.|
|/factories/providers/Microsoft.Insights/metricDefinitions/read|Az előállítók elérhető metrikai meghatározások beolvasása|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Művelet | Leírás |
|---|---|
|/accounts/computePolicies/delete|Számítás-házirend törlése.|
|/accounts/computePolicies/Read|A számítási házirenddel kapcsolatos információk.|
|/accounts/computePolicies/Write|Hozzon létre, vagy egy számítási házirend frissítése.|
|/accounts/dataLakeStoreAccounts/delete|Megszünteti egy DataLakeStore fiók DataLakeAnalytics-fiókból.|
|/accounts/dataLakeStoreAccounts/read|A csatolt DataLakeStore fiók DataLakeAnalytics fiók adatainak beolvasása.|
|/accounts/dataLakeStoreAccounts/write|Fiók létrehozása vagy frissítése egy csatolt DataLakeStore DataLakeAnalytics fiók.|
|/accounts/DELETE|DataLakeAnalytics fiók törlése.|
|/accounts/firewallRules/delete|Tűzfalszabály törlése.|
|/accounts/firewallRules/read|Egy tűzfalszabály adatainak beolvasása.|
|/accounts/firewallRules/write|Hozzon létre, vagy egy tűzfalszabály módosítása.|
|/accounts/operationResults/Read|Első DataLakeAnalytics fiók művelet eredménye.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|A DataLakeAnalytics fiók diagnosztikai beállításainak beolvasása.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Hozzon létre, vagy frissítse a diagnosztikai beállításokat a DataLakeAnalytics fiók.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|A naplók lekérése az DataLakeAnalytics fiók.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Az elérhető mérőszámok lekérni a DataLakeAnalytics fiókhoz.|
|/accounts/Read|Meglévő DataLakeAnalytics fiók adatainak beolvasása.|
|/accounts/storageAccounts/Containers/listSasTokens/action|A tároló DataLakeAnalytics fiók csatolt tárfiók SAS-tokenje listában.|
|/accounts/storageAccounts/Containers/read|A csatolt tárfiók DataLakeAnalytics fiók tárolók beolvasása.|
|/accounts/storageAccounts/delete|A Storage-fiók egy DataLakeAnalytics fiókból választható.|
|/accounts/storageAccounts/Read|A csatolt tárfiók DataLakeAnalytics fiók adatainak beolvasása.|
|/accounts/storageAccounts/write|Hozzon létre, vagy egy kapcsolódó tárfiók DataLakeAnalytics fiók módosítása.|
|/accounts/TakeOwnerShip/Action|Más felhasználók által benyújtott szakítsa engedélyeket.|
|/ fiókok/írása|Fiók létrehozása vagy frissítése egy DataLakeAnalytics.|
|/Locations/Capability/Read|Előfizetés funkció adatainak DataLakeAnalytics használatával kapcsolatban.|
|/Locations/checkNameAvailability/Action|Egy DataLakeAnalytics fióknevet elérhetőségének ellenőrzése.|
|/Locations/operationResults/Read|Első DataLakeAnalytics fiók művelet eredménye.|
|/Operations/Read|A DataLakeAnalytics elérhető műveleteinek beolvasása.|
|/ regisztrációs/művelet|Előfizetés a DataLakeAnalytics regisztrálja.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Művelet | Leírás |
|---|---|
|/accounts/DELETE|DataLakeStore fiók törlése.|
|/accounts/enableKeyVault/Action|Engedélyezze a KeyVault DataLakeStore fiók.|
|/accounts/firewallRules/delete|Tűzfalszabály törlése.|
|/accounts/firewallRules/read|Egy tűzfalszabály adatainak beolvasása.|
|/accounts/firewallRules/write|Hozzon létre, vagy egy tűzfalszabály módosítása.|
|/accounts/operationResults/Read|Első DataLakeStore fiók művelet eredménye.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|A DataLakeStore fiók diagnosztikai beállításainak beolvasása.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Hozzon létre, vagy frissítse a diagnosztikai beállításokat a DataLakeStore fiók.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|A naplók lekérése az DataLakeStore fiók.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Az elérhető mérőszámok lekérni a DataLakeStore fiókhoz.|
|/accounts/Read|Meglévő DataLakeStore fiók adatainak beolvasása.|
|/accounts/SUPERUSER/Action|Adja meg a Data Lake Store Microsoft.Authorization/roleAssignments/write megadását felügyelő.|
|/accounts/trustedIdProviders/delete|Egy megbízható identitásszolgáltató törlése.|
|/accounts/trustedIdProviders/read|Egy megbízható identitásszolgáltató adatainak beolvasása.|
|/accounts/trustedIdProviders/write|Hozzon létre, vagy egy megbízható identitásszolgáltató frissítése.|
|/ fiókok/írása|Fiók létrehozása vagy frissítése egy DataLakeStore.|
|/Locations/Capability/Read|Előfizetés funkció adatainak DataLakeStore használatával kapcsolatban.|
|/Locations/checkNameAvailability/Action|Egy DataLakeStore fióknevet elérhetőségének ellenőrzése.|
|/Locations/operationResults/Read|Első DataLakeStore fiók művelet eredménye.|
|/Operations/Read|A DataLakeStore elérhető műveleteinek beolvasása.|
|/ regisztrációs/művelet|Előfizetés a DataLakeStore regisztrálja.|

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

| Művelet | Leírás |
|---|---|
|/Locations/performanceTiers/Read|A teljesítmény rétegek rendelkezésre álló listát ad vissza.|
|/performanceTiers/Read|A teljesítmény rétegek rendelkezésre álló listát ad vissza.|
|/servers/delete|Egy meglévő kiszolgáló törlése.|
|/servers/firewallRules/delete|Meglévő tűzfalszabály törlése.|
|/servers/firewallRules/read|Tűzfal listájának kiszolgáló szabályok vagy a megadott tűzfalszabály tulajdonságainak beolvasása vissza.|
|/servers/firewallRules/write|A megadott paraméterek vagy a meglévő szabály módosítása egy olyan tűzfalszabályt hoz létre.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Lekérdezi az erőforráshoz tartozó disagnostic beállításai|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Térjen vissza a típusú ítélt adatbázisokhoz|
|/Servers/Read|A kiszolgálók vagy lekérdezi a megadott kiszolgálóhoz tartozó tulajdonságok listáját adja vissza.|
|/servers/recoverableServers/read|A helyreállítható MySQL kiszolgáló adatait adja vissza|
|/servers/virtualNetworkRules/delete|Egy meglévő virtuális hálózat szabály törlése|
|/servers/virtualNetworkRules/read|Térjen vissza a virtuális hálózat szabályok vagy a megadott virtuális hálózati szabály tulajdonságainak beolvasása.|
|/servers/virtualNetworkRules/write|A virtuális hálózati szabályt hoz létre a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott virtuális hálózati szabály címkék frissítése.|
|/ kiszolgálók/írása|Kiszolgáló létrehozása a megadott paraméterekkel, vagy frissítse a Tulajdonságok vagy a címkék a megadott kiszolgáló.|

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

| Művelet | Leírás |
|---|---|
|/Locations/performanceTiers/Read|A teljesítmény rétegek rendelkezésre álló listát ad vissza.|
|/performanceTiers/Read|A teljesítmény rétegek rendelkezésre álló listát ad vissza.|
|/servers/delete|Egy meglévő kiszolgáló törlése.|
|/servers/firewallRules/delete|Meglévő tűzfalszabály törlése.|
|/servers/firewallRules/read|Tűzfal listájának kiszolgáló szabályok vagy a megadott tűzfalszabály tulajdonságainak beolvasása vissza.|
|/servers/firewallRules/write|A megadott paraméterek vagy a meglévő szabály módosítása egy olyan tűzfalszabályt hoz létre.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Lekérdezi az erőforráshoz tartozó disagnostic beállításai|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Térjen vissza az adatbázisok elérhető naplók típusait|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Térjen vissza a típusú ítélt adatbázisokhoz|
|/Servers/Read|A kiszolgálók vagy lekérdezi a megadott kiszolgálóhoz tartozó tulajdonságok listáját adja vissza.|
|/servers/recoverableServers/read|A helyreállítható PostgreSQL-kiszolgáló adatait adja vissza|
|/servers/virtualNetworkRules/delete|Egy meglévő virtuális hálózat szabály törlése|
|/servers/virtualNetworkRules/read|Térjen vissza a virtuális hálózat szabályok vagy a megadott virtuális hálózati szabály tulajdonságainak beolvasása.|
|/servers/virtualNetworkRules/write|A virtuális hálózati szabályt hoz létre a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott virtuális hálózati szabály címkék frissítése.|
|/ kiszolgálók/írása|Kiszolgáló létrehozása a megadott paraméterekkel, vagy frissítse a Tulajdonságok vagy a címkék a megadott kiszolgáló.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Művelet | Leírás |
|---|---|
|/checkNameAvailability/Action|Ellenőrizze a rendelkezésre álló Ha IOT hubbal név|
|/checkProvisioningServiceNameAvailability/Action|Ellenőrizze a rendelkezésre álló Ha IOT hubbal név|
|/ElasticPools/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/ElasticPools/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/elasticPools/iotHubTenants/Delete|Az IOT hubbal bérlő erőforrás törlése|
|/ElasticPools/IotHubTenants/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/ElasticPools/IotHubTenants/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete|Az EventHub felhasználói csoport törlése|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read|Az EventHub fogyasztói csoportot beolvasása|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write|Az EventHub felhasználói csoport létrehozása|
|/elasticPools/iotHubTenants/exportDevices/Action|Eszközök exportálása|
|/elasticPools/iotHubTenants/getStats/Read|Az IOT hubbal bérlői statisztikák erőforrás beolvasása|
|/elasticPools/iotHubTenants/importDevices/Action|Eszközök importálása|
|/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action|Az IOT hubbal bérlői kulcs beolvasása|
|/elasticPools/iotHubTenants/jobs/Read|Ezzel a feladat a megadott IOT hubbal beküldött adatokat|
|/elasticPools/iotHubTenants/listKeys/Action|Lekérdezi a bérlőkulcsok IOT hubbal|
|/ElasticPools/IotHubTenants/logDefinitions/read|Lekérdezi az IOT hubbal szolgáltatáshoz elérhető naplófájl-definíciói|
|/ElasticPools/IotHubTenants/metricDefinitions/read|Az IOT hubbal szolgáltatáshoz elérhető metrikai meghatározások beolvasása|
|/elasticPools/iotHubTenants/quotaMetrics/Read|Kvóta metrikák beolvasása|
|/elasticPools/iotHubTenants/Read|Az IOT hubbal bérlő erőforrás beolvasása|
|/elasticPools/iotHubTenants/routing/routes/$testall/Action|A tesztüzenet szemben az összes meglévő útvonal|
|/elasticPools/iotHubTenants/routing/routes/$testnew/Action|A tesztüzenet szemben a megadott útvonal tesztelése|
|/elasticPools/iotHubTenants/routingEndpointsHealth/Read|Az IOT hubbal az összes útválasztási végpontok állapotának beolvasása|
|/elasticPools/iotHubTenants/Write|Létrehozni vagy frissíteni az IOT hubbal bérlő erőforrás|
|/ElasticPools/metricDefinitions/read|Az IOT hubbal szolgáltatáshoz elérhető metrikai meghatározások beolvasása|
|/iotHubs/certificates/generateVerificationCode/Action|Ellenőrző kód készítése|
|/iotHubs/certificates/verify/Action|Ellenőrizze az erőforrás-tanúsítvány|
|/iotHubs/Delete|IOT hubbal erőforrás törlése|
|/IotHubs/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/IotHubs/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/iotHubs/eventGridFilters/Delete|Az esemény rács szűrő törlése|
|/iotHubs/eventGridFilters/Read|Lekérdezi a esemény rács szűrő|
|/iotHubs/eventGridFilters/Write|Új vagy meglévő esemény rács szűrő frissítése|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|Az EventHub felhasználói csoport törlése|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Az EventHub fogyasztói csoportot beolvasása|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Az EventHub felhasználói csoport létrehozása|
|/iotHubs/exportDevices/Action|Eszközök exportálása|
|/iotHubs/importDevices/Action|Eszközök importálása|
|/iotHubs/iotHubKeys/listkeys/Action|A megadott névvel az IOT hubbal kulcs beszerzése|
|/iotHubs/iotHubStats/Read|Megtekintheti a statisztikákat IOT hubbal|
|/iotHubs/Jobs/Read|Ezzel a feladat a megadott IOT hubbal beküldött adatokat|
|/iotHubs/listkeys/Action|Minden IOT hubbal kulcs beszerzése|
|/IotHubs/logDefinitions/read|Lekérdezi az IOT hubbal szolgáltatáshoz elérhető naplófájl-definíciói|
|/IotHubs/metricDefinitions/read|Az IOT hubbal szolgáltatáshoz elérhető metrikai meghatározások beolvasása|
|/iotHubs/quotaMetrics/Read|Kvóta metrikák beolvasása|
|/ iotHubs/Olvasás|Lekérdezi az IOT hubbal (oka) t|
|/iotHubs/routing/$testall/Action|A tesztüzenet szemben az összes meglévő útvonal|
|/iotHubs/routing/$testnew/Action|A tesztüzenet szemben a megadott útvonal tesztelése|
|/iotHubs/routingEndpointsHealth/Read|Az IOT hubbal az összes útválasztási végpontok állapotának beolvasása|
|/iotHubs/skus/Read|Érvényes IOT hubbal termékváltozatok beolvasása|
|/ iotHubs/írása|Létrehozni vagy frissíteni az IOT hubbal erőforrás|
|/ műveletek/Olvasás|Minden ResourceProvider műveletek beolvasása|
|/provisioningServices/certificates/generateVerificationCode/Action|Ellenőrző kód készítése|
|/provisioningServices/certificates/verify/Action|Ellenőrizze az erőforrás-tanúsítvány|
|/provisioningServices/Delete|IotDps erőforrás törlése|
|/provisioningServices/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/provisioningServices/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/provisioningServices/listkeys/Action|Minden IotDps kulcs beszerzése|
|/provisioningServices/logDefinitions/read|Lekérdezi a létesítési szolgáltatás elérhető naplófájl-definíciói|
|/provisioningServices/metricDefinitions/read|A létesítési szolgáltatás elérhető metrikai meghatározások beolvasása|
|/provisioningServices/ProvisioningServiceKeys/listkeys/Action|Kulcsnév IotDps kulcsok beszerzése|
|/ provisioningServices/Olvasás|IotDps erőforráskészlet|
|/provisioningServices/skus/Read|Érvényes IotDps termékváltozatok beolvasása|
|/provisioningServices/Write|IotDps erőforrás létrehozása|
|/ regisztrációs/művelet|Az IOT hubbal erőforrás-szolgáltató előfizetésének regisztrálása és létrehozását lehetővé tevő erőforrások IOT hubbal|
|/ regisztrációs/művelet|Az IOT hubbal erőforrás-szolgáltató előfizetésének regisztrálása és létrehozását lehetővé tevő erőforrások IOT hubbal|
|/ módjait/Olvasás|Ezzel előfizetés használati adatokat a szolgáltató.|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Művelet | Leírás |
|---|---|
|/labCenters/delete|Labor erőforrások törlése.|
|/labCenters/read|Olvassa el a labor adatközpontokban.|
|/labCenters/write|Hozzáadása vagy módosítása a labor adatközpontokban.|
|/labs/artifactSources/armTemplates/read|Olvassa el az azure resource manager-sablonok.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Állít elő, az adott összetevő egy ARM-sablon, feltölti a szükséges fájlok egy tárfiókot, és érvényesíti a generált összetevő.|
|/Labs/artifactSources/artifacts/Read|Olvassa el az összetevők.|
|/labs/artifactSources/delete|Összetevő források törlése.|
|/Labs/artifactSources/Read|Olvassa el a összetevő források.|
|/Labs/artifactSources/Write|Hozzáadása vagy módosítása összetevő források.|
|/labs/ClaimAnyVm/action|Jogcím egy véletlenszerű claimable virtuális gép a tesztkörnyezetben.|
|/labs/costs/read|Olvassa el a költségeket.|
|/Labs/Costs/Write|Adja hozzá, és módosíthatja az.|
|/labs/CreateEnvironment/action|Virtuális gépek létrehozása egy tesztkörnyezetben.|
|/labs/customImages/delete|Törölje az egyéni lemezképet.|
|/labs/customImages/read|Olvassa el az egyéni lemezképek.|
|/labs/customImages/write|Hozzáadása vagy módosítása az egyéni lemezképek.|
|/labs/delete|Labs törlése.|
|/labs/ExportResourceUsage/action|A tesztlabor-erőforrások használata exportálja a storage-fiók|
|/Labs/Formulas/DELETE|Képletek törlése.|
|/Labs/Formulas/Read|Olvassa el a formulákat.|
|/Labs/Formulas/Write|Hozzáadása vagy módosítása formulákat.|
|/Labs/galleryImages/Read|Olvassa el a gyűjtemény lemezképei.|
|/labs/GenerateUploadUri/action|URI generálása az egyéni lemezképek feltöltése a labor.|
|/labs/ImportVirtualMachine/action|A virtuális gép egy másik, amikor importálja.|
|/labs/ListVhds/action|Listázza az elérhető egyéni lemezkép létrehozásához szükséges lemezképeket.|
|/labs/notificationChannels/delete|Notificationchannels törlése.|
|/labs/notificationChannels/Notify/action|Értesítést küld a megadott csatornán.|
|/Labs/notificationChannels/Read|Olvassa el a notificationchannels.|
|/labs/notificationChannels/write|Hozzáadása vagy módosítása notificationchannels.|
|/labs/policySets/EvaluatePolicies/action|Labor házirend kiértékelése.|
|/labs/policySets/policies/delete|Törölje a házirendeket.|
|/labs/policySets/policies/read|Olvassa el a házirendeket.|
|/labs/policySets/policies/write|Adja hozzá vagy módosíthat házirendeket.|
|/Labs/Read|Olvassa el a labs.|
|/labs/schedules/delete|Törölni az ütemezéseket.|
|/labs/schedules/Execute/action|Ütemezés hajtható végre.|
|/labs/schedules/ListApplicable/action|Felsorolja az összes megfelelő ütemezést|
|/labs/schedules/read|Olvassa el az ütemezések.|
|/labs/schedules/write|Hozzáadása vagy módosítása ütemezések.|
|/labs/serviceRunners/delete|Szolgáltatás indák törlése.|
|/labs/serviceRunners/read|Olvasási szolgáltatás indák.|
|/labs/serviceRunners/write|Adja hozzá, vagy módosítsa a szolgáltatás indák.|
|/labs/users/delete|Törölje a felhasználói profilokat.|
|/labs/users/disks/Attach/action|Csatolja, és a címbérlet, a lemez a virtuális gép létrehozása.|
|/labs/users/disks/delete|Törölje a lemezt.|
|/labs/users/disks/Detach/action|Válassza le, és a címbérlet, a lemez a virtuális géphez csatlakoztatott break.|
|/labs/users/disks/read|Lemezek olvasása.|
|/Labs/Users/Disks/Write|Hozzáadása vagy módosítása a lemezeket.|
|/labs/users/environments/delete|Törölje a környezetben.|
|/Labs/Users/Environments/Read|Olvassa el a környezetben.|
|/Labs/Users/Environments/Write|Hozzáadása vagy módosítása a környezetben.|
|/Labs/Users/Read|Olvassa a felhasználói profilokat.|
|/labs/users/secrets/delete|Törölje a titkos kulcsok.|
|/Labs/Users/secrets/Read|Olvassa el a titkos kulcsok.|
|/Labs/Users/secrets/Write|Hozzáadása vagy módosítása a titkos kulcsok.|
|/labs/users/serviceFabrics/delete|Törölje a szolgáltatás hálóhoz.|
|/labs/users/serviceFabrics/ListApplicableSchedules/action|Felsorolja az összes megfelelő ütemezést|
|/labs/users/serviceFabrics/read|Olvasási szolgáltatás hálóhoz.|
|/labs/users/serviceFabrics/schedules/delete|Törölni az ütemezéseket.|
|/labs/users/serviceFabrics/schedules/Execute/action|Ütemezés hajtható végre.|
|/labs/users/serviceFabrics/schedules/read|Olvassa el az ütemezések.|
|/labs/users/serviceFabrics/schedules/write|Hozzáadása vagy módosítása ütemezések.|
|/labs/users/serviceFabrics/Start/action|Indítsa el a service fabric.|
|/labs/users/serviceFabrics/Stop/action|Állítsa le a service fabric|
|/labs/users/serviceFabrics/write|Adja hozzá, vagy módosítsa a szolgáltatás hálóhoz.|
|/Labs/Users/Write|Hozzáadása vagy módosítása a felhasználói profilok.|
|/labs/virtualMachines/AddDataDisk/action|Új vagy meglévő adatlemezt csatolni a virtuális géphez.|
|/Labs/virtualMachines/ApplyArtifacts/Action|Az összetevők vonatkozik a virtuális gép.|
|/labs/virtualMachines/Claim/action|Saját tulajdonba vétele meglévő virtuális gépből|
|/labs/virtualMachines/delete|Törölje a virtuális gépeket.|
|/labs/virtualMachines/DetachDataDisk/action|Válassza le a megadott lemezt a virtuális gép.|
|/labs/virtualMachines/ListApplicableSchedules/action|Felsorolja az összes megfelelő ütemezést|
|/Labs/virtualMachines/Read|Olvassa el a virtuális gépek.|
|/Labs/virtualMachines/restart/Action|A virtuális gép újraindításához.|
|/labs/virtualMachines/schedules/delete|Törölni az ütemezéseket.|
|/labs/virtualMachines/schedules/Execute/action|Ütemezés hajtható végre.|
|/labs/virtualMachines/schedules/read|Olvassa el az ütemezések.|
|/labs/virtualMachines/schedules/write|Hozzáadása vagy módosítása ütemezések.|
|/Labs/virtualMachines/Start/Action|Indítsa el a virtuális gépet.|
|/Labs/virtualMachines/STOP/Action|A virtuális gép leállítása|
|/labs/virtualMachines/TransferDisks/action|Saját kezűleg adatlemezek virtuális gép tulajdonjogának átruházása|
|/labs/virtualMachines/UnClaim/action|Egy meglévő virtuális gép tulajdonjogának kiadás|
|/Labs/virtualMachines/Write|Virtuális gépek hozzáadása vagy módosítása.|
|/labs/virtualNetworks/delete|Törölje a virtuális hálózatok.|
|/labs/virtualNetworks/read|Olvassa el a virtuális hálózatok.|
|/labs/virtualNetworks/write|Adja hozzá, vagy módosítsa a virtuális hálózatok.|
|/ labs/írása|Hozzáadása vagy módosítása labs.|
|/Locations/Operations/Read|Az olvasási műveletek.|
|/ regisztrációs/művelet|Az előfizetés regisztrálása|
|/schedules/DELETE|Törölni az ütemezéseket.|
|/schedules/Execute/action|Ütemezés hajtható végre.|
|/schedules/Read|Olvassa el az ütemezések.|
|/schedules/Retarget/Action|Frissíti a ütemezés célerőforrás azonosítója.|
|/ ütemezések/írása|Hozzáadása vagy módosítása ütemezések.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Művelet | Leírás |
|---|---|
|/databaseAccountNames/read|A név foglaltságának ellenőrzése.|
|/databaseAccounts/changeResourceGroup/action|Módosítsa az erőforráscsoport egy olyan adatbázis-fiók|
|/databaseAccounts/Databases/Collections/metricDefinitions/Read|A gyűjtemény metrikai meghatározásainak beolvasása.|
|/databaseAccounts/Databases/Collections/Metrics/Read|A gyűjtemény metrikák beolvasása.|
|/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read|Olvassa el az adatbázis fiók partíció szintű metrikáit|
|/databaseAccounts/Databases/Collections/Partitions/Metrics/Read|Olvassa el az adatbázis fiók partíció szintű metrikák|
|/databaseAccounts/Databases/Collections/Partitions/usages/Read|Olvassa el az adatbázis-fiók partíció szintű módjait|
|/databaseAccounts/Databases/Collections/usages/Read|Beolvassa a gyűjtemény is érvényesek.|
|/databaseAccounts/Databases/metricDefinitions/Read|Az adatbázis metrikai meghatározásainak beolvasása|
|/databaseAccounts/Databases/Metrics/Read|Az adatbázis metrikák beolvasása.|
|/databaseAccounts/Databases/usages/Read|Olvassa be az adatbázis is érvényesek.|
|/databaseAccounts/DELETE|Az adatbázis-fiók törlése.|
|/databaseAccounts/failoverPriorityChange/Action|Az adatbázisfiók régiók feladatátvételi prioritás módosítása. Ez használatos kézi feladatátvételi művelet végrehajtásához|
|/databaseAccounts/listConnectionStrings/action|A kapcsolati karakterláncok beolvasása az adatbázisfiók|
|/databaseAccounts/listKeys/Action|Egy adatbázis-fiók listázása|
|/databaseAccounts/metricDefinitions/read|Beolvassa az adatbázist fiók metrikák definíciókat.|
|/databaseAccounts/Metrics/Read|Az adatbázis-fiók metrikák beolvasása.|
|/databaseAccounts/operationResults/read|Olvassa el az aszinkron művelet állapotát|
|/databaseAccounts/percentile/metrics/read|Olvassa el a késés metrikák|
|/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read|Késés olvasni a megadott forrás- és célparaméterei régió|
|/databaseAccounts/percentile/targetRegion/metrics/read|Késés olvasni a célcsoportok régió|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read|A rendelkezésre álló napló catageries az adatbázis-fiók beolvasása|
|/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read|Az adatbázis fiók elérhető metrikai meghatározások beolvasása|
|/databaseAccounts/Read|Az adatbázisfiók beolvasása.|
|/databaseAccounts/readonlykeys/action|Olvassa be az adatbázis csak olvasható kulcsait.|
|/databaseAccounts/readonlykeys/read|Olvassa be az adatbázis csak olvasható kulcsait.|
|/databaseAccounts/regenerateKey/Action|Az adatbázisfiók kulcsokkal elforgatása|
|/databaseAccounts/Region/Databases/Collections/Metrics/Read|A regionális gyűjtemény metrikák beolvasása.|
|/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read|Olvassa el a regionális adatbázis fiók partíció szintű metrikáit|
|/databaseAccounts/Region/Databases/Collections/Partitions/Metrics/Read|Olvassa el a regionális adatbázis fiók partíció szintű metrikák|
|/databaseAccounts/Region/Databases/Collections/Partitions/Read|Olvassa el az adatbázis fiók partíciók egy gyűjteményen belül|
|/databaseAccounts/Region/Metrics/Read|A régió és az adatbázis fiók metrikák beolvasása.|
|/databaseAccounts/usages/Read|Beolvassa az adatbázist fiók módjait.|
|/ databaseAccounts/írása|Egy adatbázis-fiók frissítéséhez.|
|/locations/deleteVirtualNetworkOrSubnets/action|Microsoft.DocumentDB értesíti, hogy VirtualNetwork vagy az alhálózat törlése folyamatban van|
|/operationResults/Read|Olvassa el az aszinkron művelet állapotát|
|/Operations/Read|A Microsoft documentdb elérhető olvasási műveletek |
|/ regisztrációs/művelet| Az előfizetés a Microsoft a DocumentDB erőforrás-szolgáltató regisztrálása|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Művelet | Leírás |
|---|---|
|/checkDomainAvailability/Action|Ellenőrizze, hogy a tartomány megvásárolható|
|/domains/Delete|Törölje a meglévő tartományban.|
|/domains/domainownershipidentifiers/Delete|Törölje a tulajdonjog azonosítója|
|/Domains/domainownershipidentifiers/Read|Lista tulajdonjoga azonosítói|
|/Domains/domainownershipidentifiers/Read|Tulajdonosi azonosító lekérése|
|/Domains/domainownershipidentifiers/Write|Létrehozása vagy frissítése azonosítója|
|/domains/operationresults/Read|A tartomány művelet|
|/domains/operations/Read|Minden műveleteket az app service tartományregisztrációs felsorolása|
|/ tartományok/Olvasás|A tartományok listájának lekérdezése|
|/ tartományok/Olvasás|Get domain|
|/domains/renew/Action|Újítsa meg egy meglévő tartományhoz.|
|/domains/Write|Új tartomány hozzáadása vagy egy meglévő frissítése|
|/generateSsoRequest/Action|Létre a tartomány vezérlőközpont bejelentkezni.|
|/listDomainRecommendations/Action|A lista tartomány meg ajánlásainkat kulcsszavak beolvasása|
|/ regisztrációs/művelet|Az előfizetés a Microsoft Domains erőforrás-szolgáltató regisztrálása|
|/topLevelDomains/listAgreements/Action|A szerződés művelet|
|/topLevelDomains/Read|A toplevel tartományok beolvasása|
|/topLevelDomains/Read|A toplevel lekérése|
|/validateDomainRegistrationInformation/Action|Ellenőrizze a tartomány beszerzési objektum lefolytatása nélkül|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Művelet | Leírás |
|---|---|
|/lcsprojects/clouddeployments/Read|Megjeleníti a Microsoft Dynamics AX 2012 R3 értékelési központi telepítések a felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektben|
|/lcsprojects/clouddeployments/write|A felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektben létrehozása a Microsoft Dynamics AX 2012 R3 értékelési telepítést. Központi telepítések is kezelhető az Azure felügyeleti portálon|
|/lcsprojects/Connectors/Read|Olvassa el a Microsoft Dynamics életciklus szolgáltatások projekthez tartozó összekötők|
|/lcsprojects/Connectors/Write|Létrehozása és frissítése a Microsoft Dynamics életciklus szolgáltatások projekthez tartozó összekötők|
|/lcsprojects/delete|A felhasználóhoz tartozó Microsoft Dynamics életciklus-Services-projektek törlése|
|/lcsprojects/Read|A felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektek megjelenítéséhez|
|/lcsprojects/write|Hozzon létre, és a felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektek frissítése. Csak a nevet és leírást tulajdonságait is lehet frissíteni. Az egyes előfizetésekhez és helyekhez társított a projekt létrehozása után nem frissíthető|

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

| Művelet | Leírás |
|---|---|
|/eventSubscriptions/delete|Egy eventSubscription törlése|
|/eventSubscriptions/getFullUrl/action|Az esemény-előfizetés teljes URL-cím lekérése|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read|Az esemény előfizetések diagnosztikai beállításának beolvasása.|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti a diagnosztikai esemény-előfizetések beállítása|
|/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read|A eventSubscriptions elérhető metrikai meghatározások beolvasása|
|/eventSubscriptions/read|Olvassa el a eventSubscription|
|/eventSubscriptions/write|Hozható létre vagy frissíthető egy eventSubscription|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read|Témakörök diagnosztikai beállításának beolvasása.|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti a diagnosztikai beállításának kapcsolatos témakörök|
|/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read|Elérhető metrikai meghatározások beolvasása kapcsolatos témakörök|
|/ regisztrációs/művelet|Regisztrálja a eventSubscription a EventGrid erőforrás-szolgáltató, és lehetővé teszi, hogy a rács esemény előfizetések létrehozása.|
|/topics/delete|Egy témakör törlése|
|/topics/listKeys/Action|A témakör a listában kulcsok|
|/topics/providers/Microsoft.Insights/diagnosticSettings/read|Témakörök diagnosztikai beállításának beolvasása.|
|/topics/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti a diagnosztikai beállításának kapcsolatos témakörök|
|/topics/providers/Microsoft.Insights/metricDefinitions/read|Elérhető metrikai meghatározások beolvasása kapcsolatos témakörök|
|/topics/Read|Olvassa el a témakör|
|/topics/regenerateKey/Action|A témakör kulcs újragenerálása|
|/ témakörök/írása|Létrehozni vagy frissíteni a témakör|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Művelet | Leírás |
|---|---|
|/ checkNameAvailability/művelet|A névtér adott előfizetésben való elérhetőségének ellenőrzése.|
|/ checkNamespaceAvailability/művelet|A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja helyette a CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/action|Frissítések Namespace engedélyezési szabályt. Ez az API depricated. A Namespace engedélyezési szabály sorkészletének frissítéséhez használja a PUT hívás... Ez a művelet nem támogatott az API verzió 2017-04-01.|
|/namespaces/authorizationRules/delete|Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető. |
|/namespaces/authorizationRules/listkeys/action|A névtérhez tartozó kapcsolati karakterlánc beolvasása|
|/namespaces/authorizationRules/read|A névtér-engedélyezési szabályok leírásai listájának beolvasása.|
|/namespaces/authorizationRules/regenerateKeys/action|Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása|
|/namespaces/authorizationRules/write|A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|/ névterek/törlése|Egy névtérerőforrás törlése|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Lekérdezi az engedélyezési szabályok kulcsok a vész-helyreállítási elsődleges névtér|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Katasztrófa utáni helyreállítás elsődleges Namespace meg az engedélyezési szabályok lekérése|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|A névtéralias rendelkezésre állását ellenőrzi megadott előfizetéshez.|
|/namespaces/disasterRecoveryConfigs/delete|A névtér társított vész-helyreállítási konfiguráció törlése. Ezt a műveletet csak az elsődleges névtér keresztül kell meghívni.|
|/namespaces/disasterRecoveryConfigs/failover/action|GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson.|
|/namespaces/disasterRecoveryConfigs/read|A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása.|
|/namespaces/disasterRecoveryConfigs/write|A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése.|
|/namespaces/eventhubs/authorizationRules/action|Az EventHub frissítési művelete. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése.|
|/namespaces/eventhubs/authorizationRules/delete|A művelet törli az EventHub-engedélyezési szabályok|
|/namespaces/eventhubs/authorizationRules/listkeys/action|Az EventHub kapcsolati karakterláncot az beszerzése|
|/namespaces/eventhubs/authorizationRules/read| Az EventHub-az engedélyezési szabályok listájának lekérdezése|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása|
|/namespaces/eventhubs/authorizationRules/write|Az EventHub-engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni.|
|/namespaces/eventHubs/consumergroups/Delete|A művelet ConsumerGroup erőforrás törlése|
|/namespaces/eventHubs/consumergroups/read|Listájának ConsumerGroup erőforrás leírása|
|/namespaces/eventHubs/consumergroups/write|Hozzon létre vagy frissítés ConsumerGroup tulajdonságai.|
|/namespaces/eventhubs/Delete|A művelet az EventHub erőforrás törlése|
|/namespaces/eventhubs/read|Az EventHub erőforrás leírások listáját|
|/Namespaces/eventhubs/Write|Hozzon létre vagy frissítés EventHub tulajdonságai.|
|/namespaces/messagingPlan/read|Lekérdezi az névtér az üzenetküldési megtervezése. Ez az API elavult. A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek... Ez a művelet nem támogatott az API verzió 2017-04-01.|
|/namespaces/messagingPlan/write|Frissíti az üzenetkezelési tervezése névtér. Ez az API elavult. A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek... Ez a művelet nem támogatott az API verzió 2017-04-01.|
|/Namespaces/operationresults/Read|Névtérművelet állapotának beolvasása|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Listájának Namespace naplók erőforrás leírása|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Namespace metrikák erőforrás leírása listájának beolvasása|
|/Namespaces/Read|A névtérerőforrások leírásai listájának beolvasása|
|/ névterek/írása|Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a kapacitás, a Namespace nem frissíthető tulajdonságait.|
|/Operations/Read|Műveletek beolvasása|
|/ regisztrációs/művelet|Az EventHub erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása és az EventHub-erőforrások létrehozásának engedélyezése|
|/sku/read|Termékváltozat erőforrás leírása listájának beolvasása|
|/SKU/Regions/Read|Listájának SkuRegions erőforrás leírása|
|/ unregister/művelet|Az EventHub erőforrás-szolgáltató regisztrálása|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Művelet | Leírás |
|---|---|
|/features/Read|Beolvassa az előfizetéshez tartozó szolgáltatásokat.|
|/Providers/features/Read|Beolvassa az előfizetés szolgáltatásait az adott erőforrás-szolgáltatón.|
|/Providers/features/register/Action|Regisztrálja az előfizetéshez tartozó szolgáltatást az adott erőforrás-szolgáltatón.|
|/Providers/features/unregister/Action|Az előfizetéshez tartozó szolgáltatás regisztrációjának törlése az adott erőforrás-szolgáltatón.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Művelet | Leírás |
|---|---|
|/Clusters/changerdpsetting/Action|HDInsight-fürt RDP beállításának módosítása|
|/Clusters/configurations/Action|HDInsight-fürt konfigurációjának frissítése|
|/Clusters/configurations/Read|HDInsight-fürt konfigurációjának beolvasása|
|/Clusters/DELETE|A HDInsight-fürt törlése|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/read|A HDInsight-fürt erőforrás diagnosztikai beállításának beolvasása.|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti a HDInsight-fürt erőforrás diagnosztikai beállításának|
|/clusters/providers/Microsoft.Insights/metricDefinitions/read|HDInsight-fürthöz elérhető metrikai meghatározások beolvasása|
|/Clusters/Read|Részletes információkat szolgáltatva HDInsight-fürt|
|/Clusters/Roles/Resize/Action|A HDInsight-fürt méretezése|
|/ fürtök/írása|Létrehozni vagy frissíteni a HDInsight-fürt|
|/Locations/Capabilities/Read|Az előfizetési lehetőségek elérése|
|/Locations/checkNameAvailability/Read|Név foglaltságának ellenőrzése|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Művelet | Leírás |
|---|---|
|/jobs/delete|Egy meglévő feladat törlése.|
|/jobs/listBitLockerKeys/action|Lekérdezi a megadott feladathoz tartozó BitLocker-kulcsok.|
|/Jobs/Read|Tulajdonságait olvassa be a megadott feladathoz tartozó, illetve a feladatok beolvasása.|
|/ feladatok/írása|Létrehoz egy feladatot a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott feladathoz tartozó címkék frissítése.|
|/Locations/Read|A megadott hely tulajdonságainak beolvasása, vagy a helyek listáját adja vissza.|
|/ regisztrációs/művelet|Az importálási/exportálási erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi, hogy az importálási/exportálási feladatok létrehozása.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Művelet | Leírás |
|---|---|
|/ActionGroups/Delete|Műveletcsoport törlése|
|ActionGroups/olvasása|Műveletcsoport olvasása|
|/ActionGroups/Write|Műveletcsoport írása|
|/ActivityLogAlerts/Activated/Action|A rendszer elindította a tevékenységnapló-riasztást|
|/ActivityLogAlerts/Delete|Tevékenységnapló-riasztás törlése|
|ActivityLogAlerts/olvasása|Tevékenységnapló-riasztás olvasása|
|/ActivityLogAlerts/Write|Tevékenységnapló-riasztás olvasása|
|/AlertRules/Activated/Action|Riasztási szabály aktiválva|
|/AlertRules/Delete|Riasztási szabály konfigurációjának törlése|
|AlertRules/incidensek/olvasása|Riasztási szabály incidenskonfigurációjának beolvasása|
|/AlertRules/Read|Riasztási szabály konfigurációjának beolvasása|
|/AlertRules/Resolved/Action|Riasztási szabály feloldva|
|/ AlertRules/Halmozódni/művelet|A riiasztási szabály elindítva|
|/AlertRules/Write|Riasztási szabály konfigurációjának írása|
|/AutoscaleSettings/Delete|Automatikus skálázási beállítás konfigurációjának törlése|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|A metrikadefiníciók olvasása|
|/AutoscaleSettings/Read|Automatikus skálázási beállítás konfigurációjának beolvasása|
|/AutoscaleSettings/Scaledown/Action|Automatikus vertikális leskálázási művelet|
|/AutoscaleSettings/Scaleup/Action|Automatikus vertikális felskálázási művelet|
|/AutoscaleSettings/Write|Automatikus skálázási beállítás konfigurációjának írása|
|/Components/AnalyticsItems/Delete|Az Application Insights analytics elem törlése|
|/Components/AnalyticsItems/Read|Az Application Insights analytics elem olvasása|
|/Components/AnalyticsItems/Write|Az Application Insights analytics elem írása|
|/Components/AnalyticsTables/Action|Application Insights analytics tábla művelet|
|/Components/AnalyticsTables/Delete|Az Application Insights törlése analytics tábla séma|
|/Components/AnalyticsTables/Read|Az Application Insights olvasása analytics tábla séma|
|/Components/AnalyticsTables/Write|Az Application Insights írás analytics tábla séma|
|/Components/Annotations/Delete|Az Application Insights Megjegyzés törlése|
|Összetevő/jegyzetek/olvasása|Az Application Insights megjegyzés olvasása|
|/ Összetevőinek/jegyzetek/írása|Az Application Insights Megjegyzés írása|
|Összetevő/Api/olvasása|Az Application Insights összetevő adatok API olvasása|
|/Components/ApiKeys/Action|Application Insights API-kulcs létrehozása|
|/Components/ApiKeys/Delete|Application Insights API-kulcs törlése|
|Összetevő/ApiKeys/olvasása|Application Insights API-kulcs olvasása|
|/Components/BillingPlanForComponent/Read|Az Application Insights-összetevő egy számlázási csomagot olvasása|
|/Components/CurrentBillingFeatures/Read|Az Application Insights-összetevő jelenlegi számlázási funkcióit olvasása|
|/Components/CurrentBillingFeatures/Write|Az Application Insights-összetevő jelenlegi számlázási funkcióit írása|
|/Components/DefaultWorkItemConfig/Read|Az Application Insights alapértelmezett ALM integrációs konfigurációjának olvasása|
|/ Összetevőinek/törlés|Application Insights-összetevő konfigurációjának törlése|
|/Components/ExportConfiguration/Action|Az Application Insights exportálja a beállításokat a művelet|
|/Components/ExportConfiguration/Delete|Törlése az Application Insights beállításainak exportálása|
|Összetevő/ExportConfiguration/olvasása|Olvasási Application Insights beállításainak exportálása|
|/Components/ExportConfiguration/Write|Írás az Application Insights beállításainak exportálása|
|/Components/ExtendQueries/Read|Olvasási Application Insights összetevő kiterjesztett lekérdezés eredményei|
|/Components/Favorites/Delete|Az Application Insights-kedvenc törlése|
|Összetevő/Kedvencek/olvasása|Az Application Insights-kedvenc olvasása|
|/ Összetevőinek/Kedvencek/írása|Az Application Insights-kedvenc írása|
|Összetevő/FeatureCapabilities/olvasása|Olvasási Application Insights összetevő funkciói|
|Összetevő/GetAvailableBillingFeatures/olvasása|Olvasási Application Insights összetevő számlázási szolgáltatásai|
|Összetevő/GetToken/olvasása|Az Application Insights összetevő jogkivonatának beolvasása|
|/Components/ListMigrationDate/Action|Get hátsó előfizetés áttelepítési dátuma|
|/Components/ListMigrationDate/Read|Get hátsó előfizetés áttelepítési dátuma|
|Összetevő/MetricDefinitions/olvasása|Az Application Insights összetevő metrikai meghatározásainak olvasása|
|Összetevő/metrikák/olvasása|Olvasási Application Insights összetevő metrikák|
|/Components/MigrateToNewpricingModel/Action|Új árképzési modellt előfizetés áttelepítése|
|/ Összetevőinek/áthelyezés/művelet|Az Application Insights összetevő áthelyezése egy másik erőforráscsoportba vagy előfizetésbe|
|/Components/MyAnalyticsItems/Delete|Az Application Insights személyes analytics elem törlése|
|/Components/MyAnalyticsItems/Read|Az Application Insights személyes analytics elem olvasása|
|/Components/MyAnalyticsItems/Write|Az Application Insights személyes analytics elem írása|
|Összetevő/MyFavorites Kedvencek/olvasása|Személyes Application Insights-kedvenc olvasása|
|Összetevő/PricingPlans/olvasása|Az Application Insights összetevő terv árképzési olvasása|
|/Components/PricingPlans/Write|Az Application Insights összetevő terv árképzési írása|
|/Components/ProactiveDetectionConfigs/Read|Olvasási Application Insights proaktív észlelésének konfigurálása|
|/Components/ProactiveDetectionConfigs/Write|Írás az Application Insights proaktív észlelésének konfigurálása|
|/Components/providers/Microsoft.Insights/MetricDefinitions/Read|A metrikadefiníciók olvasása|
|Összetevő/QuotaStatus/olvasása|Az Application Insights összetevő kvóta állapotának olvasása közben|
|Összetevő/olvasása|Application Insights-összetevő konfigurációjának olvasása|
|/Components/RollbackToLegacyPricingModel/Action|Visszaállítás előfizetés fizetési modell örökölt|
|Összetevő/SyntheticMonitorLocations/olvasása|Olvasási Application Insights webtesztben helyek|
|/Components/WorkItemConfigs/Delete|Az Application Insights ALM integrációs konfiguráció törlése|
|/Components/WorkItemConfigs/Read|Az Application Insights ALM integrációs konfigurációjának olvasása|
|/Components/WorkItemConfigs/Write|Az Application Insights ALM integrálásának konfigurációját írása|
|/ Összetevőinek/írása|Application Insights-összetevő konfigurációjának írása|
|/DiagnosticSettings/Delete|Diagnosztikai beállítások konfigurációjának törlése|
|DiagnosticSettings/olvasása|Diagnosztikai beállítások konfigurációjának olvasása|
|/DiagnosticSettings/Write|Diagnosztikai beállítások konfigurációjának írása|
|EventCategories/olvasása|Eseménykategória olvasása|
|/eventtypes/digestevents/Read|A felügyeleti eseménytípus kivonatának olvasása|
|/eventtypes/Values/Read|A felügyeleti eseménytípus értékeinek olvasása|
|/ExtendedDiagnosticSettings/Delete|Bővített diagnosztikai beállítások konfigurációjának törlése|
|/ExtendedDiagnosticSettings/Read|Bővített diagnosztikai beállítások konfigurációjának olvasása|
|/ExtendedDiagnosticSettings/Write|Bővített diagnosztikai beállítások konfigurációjának írása|
|LogDefinitions/olvasása|A naplódefiníciók olvasása|
|/LogProfiles/Delete|Naplóprofilok konfigurációjának törlése|
|LogProfiles/olvasása|Naplóprofilok olvasása|
|/LogProfiles/Write|Naplóprofil-konfigurációba való írás|
|/MetricAlerts/Delete|Metrikariasztás törlése|
|MetricAlerts/olvasása|Metrikariasztás olvasása|
|/MetricAlerts/Write|Metrikariasztás írása|
|/MetricDefinitions/Microsoft.Insights/Read|A metrikadefiníciók olvasása|
|/MetricDefinitions/providers/Microsoft.Insights/Read|A metrikadefiníciók olvasása|
|MetricDefinitions/olvasása|A metrikadefiníciók olvasása|
|Metrikák/szolgáltatók/metrikák/olvasása|Metrikák olvasása|
|Metrikák/olvasása|Metrikák olvasása|
|Metrikák/írása|Metrikák írása|
|Műveletek/olvasása|Olvasási műveletek|
|/Register/Action|A Microsoft Insights-szolgáltató regisztrálása|
|/ Bérlők/regisztrációs/művelet|A Microsoft Insights-szolgáltató inicializálása|
|/Unregister/Action|A Microsoft Insights-szolgáltató regisztrálása|
|/Webtests/Delete|Webteszt-konfiguráció törlése|
|/Webtests/GetToken/Read|A webtesztben jogkivonatának beolvasása|
|/Webtests/MetricDefinitions/Read|A webtesztben metrikai meghatározásainak olvasása|
|Webtests/metrikák/olvasása|A webtesztben metrikákat olvasása|
|Webtests/olvasása|Webteszt-konfiguráció olvasása|
|/ Webtests/írása|Webteszt-konfiguráció írása|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Művelet | Leírás |
|---|---|
|/checkNameAvailability/Read|Annak ellenőrzése, hogy érvényes-e a kulcstartónév, és nincs-e használatban|
|/deletedVaults/read|Helyreállíthatóan törölt kulcstartók tulajdonságainak megtekintése|
|/hsmPools/delete|HSM-készlet törlése|
|/hsmPools/joinVault/action|Kulcstartó HSM-készlethez való csatlakoztatása|
|/hsmPools/Read|A HSM-készlet tulajdonságainak megtekintése|
|/hsmPools/write|Új HSM-készlet létrehozása vagy egy meglévő HSM-készlet tulajdonságainak frissítése|
|/locations/deletedVaults/purge/action|Helyreállíthatóan törölt kulcstartó végleges törlése|
|/locations/deletedVaults/read|Helyreállíthatóan törölt kulcstartó tulajdonságainak megtekintése|
|/locations/deleteVirtualNetworkOrSubnets/action|A Microsoft.KeyVault értesítése arról, hogy virtuális hálózat vagy alhálózat törlése folyamatban van|
|/Locations/operationResults/Read|Hosszú futtatású művelet eredményének ellenőrzése|
|/Operations/Read|A Microsoft.KeyVault erőforrás-szolgáltató elérhető műveleteinek listázása|
|/ regisztrációs/művelet|Az előfizetés regisztrálása|
|/ unregister/művelet|Előfizetés regisztrációjának törlése|
|/vaults/accessPolicies/write|Egyesítéssel vagy cserével frissíthet egy meglévő hozzáférési szabályzatot, vagy létrehozhat egy új hozzáférési szabályzatot a tárolóban.|
|/vaults/DELETE|Kulcstároló törlése|
|/vaults/Deploy/Action|A kulcstartóban található titkos kódokhoz való hozzáférés engedélyezése Azure-erőforrások üzembe helyezésekor|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/vaults/providers/Microsoft.Insights/logDefinitions/read|Beolvassa a kulcstartóhoz elérhető naplókat|
|/vaults/providers/Microsoft.Insights/metricDefinitions/read|Beolvassa a kulcstartóhoz elérhető metrikákat|
|/vaults/Read|A kulcstartó tulajdonságainak megtekintése|
|/vaults/secrets/read|A titkos kód tulajdonságainak megtekintése, kivéve a kód értékét|
|/vaults/secrets/Write|Új titkos kód létrehozása, vagy meglévő titkos kód értékének módosítása|
|/ tárolók/írása|Új kulcstartó létrehozása, vagy meglévő kulcstartó tulajdonságainak módosítása|

## <a name="microsoftlabservices"></a>Microsoft.LabServices

| Művelet | Leírás |
|---|---|
|/labAccounts/CreateLab/Action|Labor létrehozása a tesztkörnyezeti fiók.|
|/labAccounts/DELETE|Tesztkörnyezet törlését.|
|/labAccounts/Labs/DELETE|Labs törlése.|
|/labAccounts/labs/environmentSettings/delete|Törölje a környezeti beállítás.|
|/labAccounts/labs/environmentSettings/environments/delete|Törölje a környezetben.|
|/labAccounts/labs/environmentSettings/environments/read|Olvassa el a környezetben.|
|/labAccounts/labs/environmentSettings/environments/write|Hozzáadása vagy módosítása a környezetben.|
|/labAccounts/labs/environmentSettings/Publish/action|Rendelkezések/deprovisions szükséges erőforrások beállítása környezet aktuális állapotát, a laborkörnyezet/beállítás alapján.|
|/labAccounts/labs/environmentSettings/read|Olvassa el a környezeti beállítás.|
|/labAccounts/labs/environmentSettings/write|Hozzáadása vagy módosítása a környezeti beállítás.|
|/labAccounts/Labs/Read|Olvassa el a labs.|
|/labAccounts/labs/users/delete|Felhasználók törlése.|
|/labAccounts/labs/users/read|Olvassa el a felhasználók.|
|/labAccounts/labs/users/write|Hozzáadása vagy módosítása a felhasználók.|
|/labAccounts/Labs/Write|Hozzáadása vagy módosítása labs.|
|/labAccounts/Read|Olvassa el a labor fiókok.|
|/ labAccounts/írása|Hozzáadása vagy módosítása a labor fiókok.|
|/Locations/Operations/Read|Az olvasási műveletek.|
|/ regisztrációs/művelet|Az előfizetés regisztrálása|

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

| Művelet | Leírás |
|---|---|
|/accounts/DELETE|Egy hely alapú törlése szolgáltatásfiók.|
|/accounts/listKeys/Action|Hely alapú Services-fiók listázása|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|A hely alapú szolgáltatások fiókok elérhető metrikai meghatározások beolvasása|
|/accounts/Read|Egy alapú helyét szolgáltatásfiók.|
|/accounts/regenerateKey/Action|Hozzon létre új hely alapú Services-fiók elsődleges vagy másodlagos kulcsot|
|/ fiókok/írása|Fiók létrehozása vagy frissítése egy hely alapú szolgáltatások.|
|/ regisztrációs/művelet|Regisztrálja a szolgáltatót|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Művelet | Leírás |
|---|---|
|/integrationAccounts/agreements/delete|A szerződés integrációs-fiók törlése.|
|/integrationAccounts/agreements/listContentCallbackUrl/action|Lekérdezi a visszahívási URL-címet az integráció fiók megállapodás tartalom.|
|/integrationAccounts/agreements/Read|A szerződés integrációs fiók beolvasása.|
|/integrationAccounts/agreements/Write|Létrehozza vagy frissíti az integráció fiók létrejött.|
|/integrationAccounts/assemblies/delete|A szerelvény integrációs-fiók törlése.|
|/integrationAccounts/assemblies/listContentCallbackUrl/action|A visszahívási URL-cím lekérése az integráció fiók szerelvény tartalom.|
|/integrationAccounts/assemblies/Read|A szerelvény integrációs fiók beolvasása.|
|/integrationAccounts/assemblies/Write|Létrehozza vagy frissíti a szerelvény integrációs fiók.|
|/integrationAccounts/batchConfigurations/delete|A kötegelt konfiguráció integrációs-fiók törlése.|
|/integrationAccounts/batchConfigurations/read|A kötegelt konfigurációs integrációs fiók beolvasása.|
|/integrationAccounts/batchConfigurations/write|Létrehozza vagy frissíti a kötegelt konfigurációs integrációs fiók.|
|/integrationAccounts/Certificates/DELETE|A tanúsítvány integrációs fiók törlése.|
|/integrationAccounts/Certificates/Read|A tanúsítvány integrációs fiók beolvasása.|
|/integrationAccounts/Certificates/Write|Létrehozza vagy frissíti az integráció fiók-tanúsítványt.|
|/integrationAccounts/delete|Az integráció fiók törlése.|
|/integrationAccounts/listCallbackUrl/action|A visszahívási URL-cím lekérése integrációs fiók.|
|/integrationAccounts/listKeyVaultKeys/action|A kulcsok beolvasása a key vault.|
|/integrationAccounts/logTrackingEvents/Action|A nyomkövetési eseményeket naplózza az integráció fiók.|
|/integrationAccounts/maps/delete|A térkép integrációs-fiók törlése.|
|/integrationAccounts/maps/listContentCallbackUrl/action|A visszahívási URL-címet lekéri integrációs fiók térkép tartalma.|
|/integrationAccounts/maps/read|A térkép integrációs fiók beolvasása.|
|/integrationAccounts/maps/write|Létrehozza vagy frissíti a térkép integrációs fiók.|
|/integrationAccounts/partners/delete|A partner integrációs-fiók törlése.|
|/integrationAccounts/partners/listContentCallbackUrl/action|A visszahívási URL-cím lekérése az integráció fiók partner tartalom.|
|/integrationAccounts/partners/Read|Beolvassa a megoldással integrációs fiók.|
|/integrationAccounts/partners/Write|Létrehozza vagy frissíti a partner integrációs fiók.|
|/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read|Az integrációs fiók naplódefinícióinak olvasása.|
|/integrationAccounts/Read|Az integráció fiók beolvasása.|
|/integrationAccounts/regenerateAccessKey/action|Újra létrehozza a hívóbetű titkos kulcsait.|
|/integrationAccounts/schemas/DELETE|A séma integrációs-fiók törlése.|
|/integrationAccounts/schemas/listContentCallbackUrl/action|Lekérdezi a visszahívási URL-címet az integráció fiók séma tartalom.|
|/integrationAccounts/schemas/Read|A séma integrációs fiók beolvasása.|
|/integrationAccounts/schemas/Write|Létrehozza vagy frissíti a séma integrációs fiók.|
|/integrationAccounts/sessions/delete|A munkamenet integrációs-fiók törlése.|
|/integrationAccounts/SESSIONS/Read|A kötegelt konfigurációs integrációs fiók beolvasása.|
|/integrationAccounts/SESSIONS/Write|Létrehozza vagy frissíti a munkamenet integrációs fiók.|
|/ integrationAccounts/írása|Létrehozza vagy frissíti az integráció fiók.|
|/Locations/workflows/Validate/Action|Érvényesíti a munkafolyamatot.|
|/Operations/Read|A művelet lekérdezi.|
|/ regisztrációs/művelet|Regisztrálja az adott előfizetéshez tartozó Microsoft.Logic erőforrás-szolgáltató.|
|/workflows/accessKeys/delete|Törli a hívóbetűt.|
|/workflows/accessKeys/List/Action|Listázza a hívóbetű titkos kulcsait.|
|/workflows/accessKeys/Read|Beolvassa a hívóbetűt.|
|/workflows/accessKeys/regenerate/action|Újra létrehozza a hívóbetű titkos kulcsait.|
|/workflows/accessKeys/Write|Létrehozza vagy frissíti a hívóbetűt.|
|/workflows/DELETE|Törli a munkafolyamatot.|
|/workflows/disable/Action|Letiltja a munkafolyamatot.|
|/workflows/enable/Action|Engedélyezi a munkafolyamatot.|
|/workflows/listCallbackUrl/Action|Egy munkafolyamat visszahívási URL-címének beolvasása.|
|/workflows/listSwagger/action|A munkafolyamat Swagger-definícióinak beolvasása.|
|/workflows/MOVE/Action|A munkafolyamatot a meglévő előfizetés-azonosítójáról, erőforráscsoportjából és/vagy nevéről egy másik előfizetés-azonosítóra, erőforráscsoportba és/vagy névre helyezi át.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/read|Beolvassa a munkafolyamat diagnosztikai beállításait.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti a munkafolyamat diagnosztikai beállításait.|
|/workflows/providers/Microsoft.Insights/logDefinitions/read|Beolvassa a munkafolyamat naplómeghatározásait.|
|/workflows/providers/Microsoft.Insights/metricDefinitions/read|Beolvassa a munkafolyamat metrikai meghatározásait.|
|/workflows/Read|Beolvassa a munkafolyamatot.|
|/workflows/regenerateAccessKey/action|Újra létrehozza a hívóbetű titkos kulcsait.|
|/workflows/Run/Action|Elindítja egy munkafolyamat futtatását.|
|/workflows/runs/actions/listExpressionTraces/action|A munkafolyamat-futtatási műveleti kifejezés követési adatainak beolvasása.|
|/workflows/runs/actions/Read|Beolvassa a munkafolyamat-futtatási műveletet.|
|/workflows/runs/actions/repetitions/listExpressionTraces/action|Lekérdezi a munkafolyamat művelet megismétlését kifejezés nyomkövetési adatokat.|
|/workflows/runs/actions/repetitions/Read|Olvassa be a munkafolyamat művelet megismétlését.|
|/workflows/runs/actions/scoperepetitions/Read|A munkafolyamat műveleti hatókör ismétlődési beolvasása.|
|/workflows/runs/Cancel/Action|Megszakítja egy munkafolyamat futtatását.|
|/workflows/runs/Operations/Read|Beolvassa a munkafolyamat-futtatás műveleti állapotát.|
|/ munkafolyamatok/fut/olvasása|Beolvassa a munkafolyamat-futtatást.|
|/workflows/suspend/Action|A munkafolyamat felfüggesztése.|
|/workflows/triggers/histories/Read|Beolvassa a triggerelőzményeket.|
|/workflows/triggers/histories/resubmit/Action|A munkafolyamat-trigger újraküldése.|
|/workflows/triggers/listCallbackUrl/action|Egy trigger visszahívási URL-címének beolvasása.|
|/workflows/triggers/Read|Beolvassa a triggert.|
|/workflows/triggers/reset/action|Alaphelyzetbe állítja az eseményindító.|
|/workflows/triggers/run/action|Végrehajtja a triggert.|
|/workflows/triggers/setState/action|Az eseményindító állapotának beállítása.|
|/workflows/Validate/Action|Érvényesíti a munkafolyamatot.|
|/workflows/versions/Read|Beolvassa a munkafolyamat verzióját.|
|/workflows/versions/triggers/listCallbackUrl/action|Egy trigger visszahívási URL-címének beolvasása.|
|/ munkafolyamatok/írása|Létrehozza vagy frissíti a munkafolyamatot.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Művelet | Leírás |
|---|---|
|/commitmentPlans/commitmentAssociations/move/action|Helyezze át a gépi tanulási kötelezettségvállalás terv társítása|
|/commitmentPlans/commitmentAssociations/read|Olvassa el a gépi tanulási kötelezettségvállalás terv társítása|
|/commitmentPlans/delete|Gépi tanulás kötelezettségvállalás terv törlése|
|/commitmentPlans/JOIN/Action|Csatlakozás a gépi tanulási kötelezettségvállalás terv|
|/commitmentPlans/Read|Olvassa el a gépi tanulási kötelezettségvállalás terv|
|/ commitmentPlans/írása|Hozzon létre vagy bármely Machine Learning előfizetési csomag frissítése|
|/Locations/operationresults/Read|A Machine Learning művelet eredményének beolvasása|
|/Locations/operationsstatus/Read|A Machine Learning futó művelet állapotának beolvasása|
|/Operations/Read|Gépi tanulási műveletek beolvasása|
|/ regisztrációs/művelet|A machine learning web service erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a webszolgáltatások létrehozását.|
|/skus/Read|Gépi tanulási kötelezettségvállalás terv termékváltozatok beolvasása|
|/webServices/action|A támogatott régiók regionális webes szolgáltatás tulajdonságok létrehozása|
|/webServices/delete|A Machine Learning webszolgáltatás törlése|
|/webServices/read|Olvassa el a Machine Learning webszolgáltatás|
|/webServices/write|Létrehozni vagy frissíteni a Machine Learning webszolgáltatás|
|/Workspaces/delete|Gépi tanulás munkaterület törlése|
|/ Munkaterületek/listworkspacekeys/művelet|A Machine Learning-munkaterület listában kulcsok|
|A munkaterületek között/olvasása|Olvassa el a gépi tanulási munkaterület|
|/Workspaces/resyncstoragekeys/action|A Machine Learning-munkaterület beállítása tárfiók kulcsait újraszinkronizálásra|
|A munkaterületek között/írása|Létrehozni vagy frissíteni a gépi tanulási munkaterület|

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

| Művelet | Leírás |
|---|---|
|/operationalizationClusters/checkUpdate/action|Ha frissítések érhetők el a operationalization fürt-szolgáltatások ellenőrzése|
|/operationalizationClusters/delete|Bármely üzemeltetési fiók törlése|
|/operationalizationClusters/listKeys/action|Operationalization-fürthöz tartozó kulcsainak listázása|
|/operationalizationClusters/Read|Bármely olyan üzemeltetési fiók olvasása|
|/operationalizationClusters/updateSystem/action|A rendszerszolgáltatások operationalization fürt frissítése|
|/ operationalizationClusters/írása|Bármely üzemeltetési fiók létrehozása vagy frissítése|
|/ regisztrációs/művelet|Az erőforrás-szolgáltató az előfizetés-azonosító regisztrálja, és lehetővé teszi a machine learning-számítási erőforrásokat létrehozását|

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

| Művelet | Leírás |
|---|---|
|/accounts/DELETE|Bármely üzemeltetési fiók törlése|
|/accounts/Read|Bármely olyan üzemeltetési fiók olvasása|
|/ fiókok/írása|Bármely üzemeltetési fiók létrehozása vagy frissítése|
|/ regisztrációs/művelet|Az erőforrás-szolgáltató az előfizetés-azonosító regisztrálja, és lehetővé teszi egy üzemeltetési fiók létrehozását|

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

| Művelet | Leírás |
|---|---|
|/userAssignedIdentities/assign/action|Szerepalapú művelet egy meglévő felhasználó hozzárendelése egy erőforráshoz hozzárendelt identitás|
|/userAssignedIdentities/delete|Egy meglévő felhasználó lehet hozzárendelve identitás törlése|
|/userAssignedIdentities/read|Lekérdezi egy meglévő felhasználó lehet hozzárendelve identitás|
|/userAssignedIdentities/write|Egy új felhasználó lehet hozzárendelve identitás létrehoz vagy frissít egy meglévő felhasználó lehet hozzárendelve identitás társított címkék|

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

| Művelet | Leírás |
|---|---|
|/labAccounts/CreateLab/Action|Labor létrehozása a tesztkörnyezeti fiók.|
|/labAccounts/DELETE|Tesztkörnyezet törlését.|
|/labAccounts/Labs/DELETE|Labs törlése.|
|/labAccounts/labs/environmentSettings/delete|Törölje a környezeti beállítás.|
|/labAccounts/labs/environmentSettings/environments/delete|Törölje a környezetben.|
|/labAccounts/labs/environmentSettings/environments/read|Olvassa el a környezetben.|
|/labAccounts/labs/environmentSettings/environments/write|Hozzáadása vagy módosítása a környezetben.|
|/labAccounts/labs/environmentSettings/read|Olvassa el a környezeti beállítás.|
|/labAccounts/labs/environmentSettings/write|Hozzáadása vagy módosítása a környezeti beállítás.|
|/labAccounts/labs/labVms/delete|Törölje a labor virtuális gépeket.|
|/labAccounts/labs/labVms/read|Olvassa el a labor virtuális gépeken.|
|/labAccounts/labs/labVms/write|Hozzáadása vagy módosítása a labor virtuális gépeken.|
|/labAccounts/Labs/Read|Olvassa el a labs.|
|/labAccounts/Labs/Write|Hozzáadása vagy módosítása labs.|
|/labAccounts/Read|Olvassa el a labor fiókok.|
|/ labAccounts/írása|Hozzáadása vagy módosítása a labor fiókok.|
|/Locations/Operations/Read|Az olvasási műveletek.|
|/ regisztrációs/művelet|Az előfizetés regisztrálása|

## <a name="microsoftmanagement"></a>Microsoft.Management

| Művelet | Leírás |
|---|---|
|/ checkNameAvailability/művelet|Ellenőrzi, hogy a megadott felügyeleti csoport neve érvényes és egyedi.|
|/getEntities/action|Összes entitás (felügyeleti csoportok, előfizetések stb.) a hitelesített felhasználók listázása.|
|/managementGroups/delete|Felügyeleti csoport törlése.|
|/managementGroups/Read|A hitelesített felhasználók listáját a felügyeleti csoportok.|
|/managementGroups/subscriptions/delete|Előfizetés a felügyeleti csoportból való társítja.|
|/managementGroups/subscriptions/write|Meglévő felügyeleti csoporttal előfizetés társult.|
|/ managementGroups/írása|Hozzon létre, vagy egy felügyeleti csoport frissítése.|

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

| Művelet | Leírás |
|---|---|
|/ClassicDevServices/delete|Klasszikus fejlesztői szolgáltatás erőforrás törlési műveletet végez.|
|/ClassicDevServices/listSecrets/action|Lekérdezi a klasszikus fejlesztői erőforrás felügyeleti kulcsai.|
|/ClassicDevServices/listSingleSignOnToken/action|Az egyszeri bejelentkezési URL-cím lekérése egy klasszikus fejlesztői szolgáltatáshoz.|
|/ClassicDevServices/read|Klasszikus fejlesztői szolgáltatás a GET műveletet végez.|
|/ClassicDevServices/regenerateKey/action|A klasszikus fejlesztői erőforrás felügyeleti kulcsai állít elő.|
|Műveletek/olvasása|Olvassa el a műveleteket, az összes erőforrástípus.|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Művelet | Leírás |
|---|---|
|/agreements/offers/plans/Cancel/Action|Szakítsa meg a szerződés egy adott piactér elem|
|/agreements/offers/plans/Read|Térjen vissza az adott piactér elem szerződés|
|/agreements/offers/plans/Sign/Action|Egy adott piactér elem létrejött|
|/agreements/Read|Térjen vissza a minden szerződés megadott előfizetés|
|/offertypes/publishers/offers/plans/agreements/read|Egy adott piactér virtuális gép elem szerződés beolvasása|
|/offertypes/publishers/offers/plans/agreements/write|Bejelentkezés, vagy szakítsa meg a szerződés egy adott piactér virtuális gép elem|

## <a name="microsoftmedia"></a>Microsoft.Media

| Művelet | Leírás |
|---|---|
|/ checknameavailability/művelet|Ellenőrzi, hogy a Media Services-fiók nevének érhető el|
|/mediaservices/delete|A Media Services-fiók törlése|
|/mediaservices/listKeys/action|A Media Services-fiókhoz tartozó ACS kulcsainak listázása|
|/mediaservices/Read|Olvassa el a Media Services-fiók|
|/mediaservices/regenerateKey/action|Egy Media Services, ACS kulcs újragenerálása|
|/mediaservices/syncStorageKeys/action|A tárolási kulcsokat csatolt Azure Storage-fiók szinkronizálása|
|/ mediaservices/írása|Bármely Media Services-fiók létrehozása vagy frissítése|
|/Operations/Read|Olvassa el a Media Services-fiók|
|/ regisztrációs/művelet|A Media Services erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a Media Services-fiókok létrehozását|

## <a name="microsoftmigrate"></a>Microsoft.Migrate

| Művelet | Leírás |
|---|---|
|Műveletek/olvasása|Az elérhető műveletek beolvasása|

## <a name="microsoftnetwork"></a>Microsoft.Network

| Művelet | Leírás |
|---|---|
|/applicationGatewayAvailableSslOptions/predefinedPolicies/read|Alkalmazás átjáró Ssl előre definiált házirend|
|/applicationGatewayAvailableSslOptions/read|Alkalmazás átjáró elérhető Ssl-beállítások|
|/applicationGatewayAvailableWafRuleSets/read|Rendelkezésre álló Waf beállítására Alkalmazásátjáró beolvasása|
|/applicationGateways/backendAddressPools/join/action|Egy alkalmazás Alkalmazásátjáró háttércímkészletének illesztése|
|/applicationGateways/backendhealth/action|Az átjáró háttér állapotának beolvasása|
|/applicationGateways/delete|Alkalmazásátjáró törlése|
|/applicationGateways/effectiveNetworkSecurityGroups/action|Alkalmazásátjáró konfigurált útválasztási táblázatot beolvasása|
|/applicationGateways/effectiveRouteTable/action|Alkalmazásátjáró konfigurált útválasztási táblázatot beolvasása|
|/applicationGateways/providers/Microsoft.Insights/logDefinitions/read|Az Alkalmazásátjáró eseményeinek beolvasása|
|/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read|Az Alkalmazásátjáró elérhető metrikai meghatározások beolvasása|
|/applicationGateways/read|Alkalmazásátjáró beolvasása|
|/applicationGateways/setSecurityCenterConfiguration/action|Konfigurációs készletek alkalmazás átjáró biztonsági központ|
|/applicationGateways/start/action|Alkalmazásátjáró kezdődik|
|/applicationGateways/stop/action|Leállítja az Alkalmazásátjáró|
|/applicationGateways/write|Alkalmazásátjáró létrehozása vagy meglévő Alkalmazásátjáró frissítése|
|/applicationSecurityGroups/delete|Az alkalmazás biztonsági csoport törlése|
|/applicationSecurityGroups/joinIpConfiguration/action|Biztonsági csoportok csatlakoztatja IP-konfigurációt.|
|/applicationSecurityGroups/joinNetworkSecurityRule/action|Csatlakozik a szabály az alkalmazás biztonsági csoportokat.|
|/applicationSecurityGroups/Read|Lekérdezi egy alkalmazás biztonsági csoport.|
|/applicationSecurityGroups/write|Egy alkalmazás biztonsági csoportot hoz létre, vagy frissíti a meglévő alkalmazás biztonsági csoporthoz.|
|/bgpServiceCommunities/read|Bgp-szolgáltatás Közösségek beolvasása|
|/checkTrafficManagerNameAvailability/action|A Traffic Manager relatív DNS-név elérhetőségének ellenőrzése.|
|/Connections/DELETE|Deletes VirtualNetworkGatewayConnection|
|/Connections/Read|Gets VirtualNetworkGatewayConnection|
|/Connections/sharedkey/Action|ConnectionType(hypernet/routebased) SharedKey beolvasása|
|/Connections/sharedKey/Read|Gets VirtualNetworkGatewayConnection SharedKey|
|/connections/sharedKey/write|Létrehoz vagy frissít egy meglévő ConnectionType(hypernet/routebased) SharedKey|
|/connections/vpndeviceconfigurationscript/read|Lekérdezi a ConnectionType(hypernet/routebased) VPN-eszköz konfigurációja|
|/ kapcsolatok/írása|Létrehoz vagy frissít egy meglévő ConnectionType(hypernet/routebased)|
|/ddosProtectionPlans/ddosProtectionPlanProxies/delete|DDoS védelem terv Proxy törlése|
|/ddosProtectionPlans/ddosProtectionPlanProxies/read|DDoS védelem megtervezése Proxy definíciójának beolvasása|
|/ddosProtectionPlans/ddosProtectionPlanProxies/write|Létrehoz egy DDoS védelem megtervezése Proxy vagy frissítések- és meglévő DDoS védelem megtervezése|
|/ddosProtectionPlans/delete|DDoS védelem terv törlése|
|/ddosProtectionPlans/join/action|DDoS védelmi tervet illesztése|
|/ddosProtectionPlans/read|Lekérdezi a DDoS védelem terv|
|/ddosProtectionPlans/write|DDoS védelem terv létrehozása vagy frissítése. a DDoS védelem megtervezése |
|/dnsoperationresults/Read|A DNS-művelet eredményének beolvasása|
|/dnsoperationstatuses/read|A DNS-művelet állapotának beolvasása |
|/dnszones/A/delete|Eltávolítható az adott nevű és írja be a "A" a DNS-zónából.|
|/dnszones/A/read|A "A" típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza.|
|/dnszones/A/write|Hozzon létre vagy frissíthető a DNS-zónában "A" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját.|
|/dnszones/AAAA/delete|Eltávolítható az adott nevű és írja be a "AAAA" a DNS-zónából.|
|/dnszones/AAAA/read|"AAAA", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza.|
|/dnszones/AAAA/write|Hozzon létre vagy frissíthető a DNS-zónában "AAAA" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját.|
|/dnszones/all/read|Lekérdezi a DNS-rekordhalmazok típusok között|
|/dnszones/CAA/delete|Eltávolítható az adott nevű és írja be a "CAA" a DNS-zónából.|
|/dnszones/CAA/read|"CAA", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordkészlet tartalmazza a TTL-t, a címkék és az etag.|
|/dnszones/CAA/write|Hozzon létre vagy frissíthető a DNS-zónában "CAA" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját.|
|/dnszones/CNAME/delete|Eltávolítható az adott nevű és írja be a "CNAME" a DNS-zónából.|
|/dnszones/CNAME/read|"CNAME", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordkészlet tartalmazza a TTL-t, a címkék és az etag.|
|/dnszones/CNAME/write|Hozzon létre vagy frissíthető a DNS-zónában "CNAME" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját.|
|/dnszones/delete|Törli a DNS-zóna JSON formátumban. A zóna tulajdonságai tartalmaznak címkéket, etag, numberOfRecordSets és maxnumberofrecordsets tulajdonságokat.|
|/dnszones/MX/delete|Eltávolítható az adott nevű és írja be az "MX" a DNS-zónából.|
|/dnszones/MX/read|"MX", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza.|
|/dnszones/MX/write|Hozzon létre vagy frissíthető a DNS-zónában "MX" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját.|
|/dnszones/NS/delete|Törli a DNS-rekordhalmaz NS típusú|
|/dnszones/NS/read|Gets DNS record set of type NS|
|/dnszones/NS/write|Létrehozza vagy frissíti a DNS NS típusú rekordhalmaz|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/read|A DNS-zóna diagnosztikai beállításainak beolvasása|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti a DNS-zóna diagnosztikai beállítások|
|/dnszones/providers/Microsoft.Insights/metricDefinitions/read|A DNS-zóna metrikai meghatározásainak beolvasása|
|/dnszones/PTR/delete|Eltávolítható az adott nevű és írja be a "PTR" a DNS-zónából.|
|/dnszones/PTR/read|"PTR", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza.|
|/dnszones/PTR/write|Hozzon létre vagy frissíthető a DNS-zónában "PTR" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját.|
|/dnszones/read|A DNS-zóna JSON formátumban beolvasása. A zóna tulajdonságai tartalmaznak címkéket, etag, numberOfRecordSets és maxnumberofrecordsets tulajdonságokat. Vegye figyelembe, hogy ez a parancs nem kéri le a rekordkészleteket, a zóna tartalmaz.|
|/dnszones/recordsets/read|Lekérdezi a DNS-rekordhalmazok típusok között|
|/dnszones/SOA/read|Gets DNS record set of type SOA|
|/dnszones/SOA/write|Létrehozza vagy frissíti a DNS-rekordhalmaz SOA típusú|
|/dnszones/SRV/delete|Eltávolítható az adott nevű és írja be a "SRV" a DNS-zónából.|
|/dnszones/SRV/read|"SRV", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza.|
|/dnszones/SRV/write|SRV típusú rekordot csoport létrehozása vagy frissítése|
|/dnszones/TXT/delete|Eltávolítható az adott nevű és írja be a "TXT" a DNS-zónából.|
|/dnszones/TXT/read|"TXT", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza.|
|/dnszones/TXT/write|Hozzon létre vagy frissíthető a DNS-zónában "TXT" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját.|
|/ dnszones/írása|Hozzon létre, vagy frissítse a DNS-zónák erőforráscsoporton belül.  A címke van megadva a DNS-zóna erőforrás frissítésére szolgáló. Vegye figyelembe, hogy ez a parancs nem használható létrehozására vagy frissítésére a zónán belül rekordhalmazok.|
|/expressRouteCircuits/authorizations/delete|Törli az Express route-körnek engedélyezési|
|/expressRouteCircuits/authorizations/read|Lekérdezi az Express route-körnek engedély|
|/expressRouteCircuits/authorizations/write|Létrehozza vagy frissíti a meglévő Express route-körnek engedély|
|/expressRouteCircuits/delete|Az Express route-körnek törlése|
|/expressRouteCircuits/peerings/arpTables/action|Az Express route-körnek társviszony-létesítés ArpTable beolvasása|
|/expressRouteCircuits/peerings/connections/delete|Az Express route-körnek kapcsolat törlése|
|/expressRouteCircuits/peerings/Connections/Read|Lekérdezi az Express route-körnek kapcsolat|
|/expressRouteCircuits/peerings/Connections/Write|Létrehozza vagy frissíti az Express route-körnek meglévő kapcsolat erőforrás|
|/expressRouteCircuits/peerings/delete|Az Express route-körnek Társviszony törlése|
|/expressRouteCircuits/peerings/Read|Lekérdezi az Express route-körnek társviszony-létesítés|
|/expressRouteCircuits/peerings/routeTables/Action|Az Express route-körnek társviszony-létesítés Migrálták beolvasása|
|/expressRouteCircuits/peerings/routeTablesSummary/action|Az Express route-körnek társviszony-létesítés Migrálták összegzését beolvasása|
|/expressRouteCircuits/peerings/stats/Read|Lekérdezi az Express route-körnek társviszony-létesítés statisztika|
|/expressRouteCircuits/peerings/write|Létrehoz vagy frissít egy meglévő Express route-körnek Társviszony|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read|Az ExpressRoute-Kapcsolatcsoportok diagnosztikai beállításainak beolvasása|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti az ExpressRoute-Kapcsolatcsoportok tartozó diagnosztikai beállítások|
|/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read|Az események lekérése ExpressRoute-Kapcsolatcsoportok|
|/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read|Az ExpressRoute-Kapcsolatcsoportok metrikai meghatározásainak beolvasása|
|/expressRouteCircuits/read|Az Express route-körnek beolvasása|
|/expressRouteCircuits/stats/read|Az Express route-körnek Stat beolvasása|
|/expressRouteCircuits/write|Létrehoz vagy frissít egy meglévő Express route-körnek|
|/expressRouteCrossConnections/delete|Törölje az Express Route telephelyek közötti kapcsolat|
|/expressRouteCrossConnections/join/action|Az Express Route illesztések telephelyek közötti kapcsolat|
|/expressRouteCrossConnections/peerings/arpTables/action|Egy Expressroute közötti kapcsolat társviszony-létesítés Arp-táblázat beolvasása|
|/expressRouteCrossConnections/peerings/delete|Egy Expressroute közötti kapcsolat Társviszony törlése|
|/expressRouteCrossConnections/peerings/read|Egy Expressroute közötti kapcsolat társviszony-létesítés beolvasása|
|/expressRouteCrossConnections/peerings/routeTables/action|Egy Expressroute közötti kapcsolat társviszony-létesítés útvonaltábla beolvasása|
|/expressRouteCrossConnections/peerings/routeTableSummary/action|Egy Expressroute közötti kapcsolat társviszony-létesítés útvonal tábla összegzés beolvasása|
|/expressRouteCrossConnections/peerings/stats/read|Egy Expressroute közötti kapcsolat társviszony-létesítés statisztika beolvasása|
|/expressRouteCrossConnections/peerings/write|Az Express Route telephelyek közötti kapcsolat társviszony-létesítés létrehozása vagy frissítése. egy meglévő Express Route telephelyek közötti kapcsolat társviszony-létesítés|
|/expressRouteCrossConnections/read|Expressroute beolvasása telephelyek közötti kapcsolat|
|/expressRouteCrossConnections/write|Hozzon létre vagy frissíthető Expressroute telephelyek közötti kapcsolat|
|/expressRouteServiceProviders/read|Lekérdezi az Express Route-szolgáltatók|
|/loadBalancers/backendAddressPools/join/action|A load balancer háttércímkészletének illesztése|
|/loadBalancers/backendAddressPools/read|A load balancer háttércímkészlet definíciójának beolvasása|
|/loadBalancers/delete|Törli a terheléselosztó|
|/loadBalancers/frontendIPConfigurations/read|A terheléselosztó előtérbeli IP konfiguráció definíciójának beolvasása|
|/loadBalancers/inboundNatPools/join/action|Művelettel illeszthető egy terheléselosztó bejövő forgalmat kezelő nat-készlete|
|/loadBalancers/inboundNatPools/read|Terheléselosztó bejövő forgalmat kezelő nat-készlet definíciójának beolvasása|
|/loadBalancers/inboundNatRules/delete|Terheléselosztó bejövő forgalmat kezelő nat-szabályának törlése|
|/loadBalancers/inboundNatRules/join/action|Terheléselosztó bejövő forgalmat kezelő nat-szabályának illesztése|
|/loadBalancers/inboundNatRules/read|Terheléselosztó bejövő forgalmat kezelő nat-szabályát leíró definíció beolvasása|
|/loadBalancers/inboundNatRules/write|Terheléselosztó bejövő forgalmat kezelő nat-szabályának létrehozása vagy frissítése egy meglévő terheléselosztó bejövő nat-szabálya|
|/loadBalancers/loadBalancingRules/read|Terheléselosztó terheléselosztási terheléselosztási szabály definíciójának beolvasása|
|/loadBalancers/networkInterfaces/read|A terheléselosztó minden hálózati interfészen hivatkozások beolvasása|
|/loadBalancers/outboundNatRules/read|Terheléselosztó kimenő forgalmat kezelő nat szabály definíciójának beolvasása|
|/loadBalancers/probes/join/action|Lehetővé teszi, hogy a terheléselosztó mintavételt használatával. Például, az engedély healthProbe tulajdonság a Virtuálisgép-méretezési készlet is hivatkozhatnak a mintavételi.|
|/loadBalancers/probes/read|Terheléselosztói mintavétel beolvasása|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read|A Load Balancer diagnosztikai beállításainak beolvasása|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti a terheléselosztási diagnosztikai beállításokat|
|/loadBalancers/providers/Microsoft.Insights/logDefinitions/read|Terheléselosztó eseményeinek beolvasása|
|/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read|A terheléselosztó elérhető metrikai meghatározások beolvasása|
|/loadBalancers/Read|Terheléselosztó definíciójának beolvasása|
|/loadBalancers/virtualMachines/read|A terheléselosztó virtuális gépek hivatkozások beolvasása|
|/loadBalancers/write|Terheléselosztó létrehozása vagy meglévő terheléselosztó frissítése|
|/localnetworkgateways/DELETE|LocalNetworkGateway törlése|
|/localnetworkgateways/Read|Lekérdezi a LocalNetworkGateway|
|/ localnetworkgateways/írása|Létrehoz vagy frissít egy meglévő virtuális|
|/locations/checkDnsNameAvailability/read|Ellenőrzi, hogy DNS-címke érhető el a megadott helyen|
|/Locations/operationResults/Read|Aszinkrón POST vagy DELETE művelet eredményének beolvasása|
|/Locations/Operations/Read|Egy aszinkron művelet állapotát jelző művelet erőforrás beolvasása|
|/Locations/usages/Read|A szoftverhasználati mérési adatok erőforrások lekérése|
|/locations/virtualNetworkAvailableEndpointServices/read|Virtuális hálózati végpont elérhető szolgáltatások listájának lekérése|
|/networkInterfaces/DELETE|Hálózati kapcsolat törlése|
|/networkInterfaces/diagnosticIdentity/read|Lekérdezi az erőforrás diagnosztikai identitás|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|Hálózati biztonsági csoportok konfigurált a hálózati kapcsolat, a VM-lekérdezés|
|/networkInterfaces/effectiveRouteTable/action|A virtuális gép hálózati illesztőjén konfigurált útválasztási táblázatot beolvasása|
|/networkInterfaces/ipconfigurations/read|A hálózati illesztő ip-konfiguráció definíciójának beolvasása |
|/networkInterfaces/JOIN/Action|A virtuális gép illesztése hálózati illesztőhöz|
|/networkInterfaces/loadBalancers/read|A hálózati illesztő részét képező terheléselosztók beolvasása|
|/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read|Elérhető lekérdezi a hálózati adapter|
|/networkInterfaces/Read|Hálózati illesztő definíciójának beolvasása. |
|/ hálózati illesztők/írása|Egy adott hálózati csatoló létrehozza vagy frissíti a meglévő hálózati illesztő. |
|/networkSecurityGroups/defaultSecurityRules/read|Alapértelmezett biztonsági szabály definíciójának beolvasása|
|/networkSecurityGroups/delete|A hálózati biztonsági csoport törlése|
|/networkSecurityGroups/join/action|Hálózati biztonsági csoport illesztése|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read|A hálózati biztonsági csoport diagnosztikai beállításainak beolvasása|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti a hálózati biztonsági csoport diagnosztikai beállításainak, ezt a műveletet az adatelemzési erőforrás-szolgáltató hozhatók.|
|/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read|Hálózati biztonsági csoport eseményeinek beolvasása|
|/networkSecurityGroups/Read|Hálózati biztonsági csoport definíciójának beolvasása|
|/networkSecurityGroups/securityRules/delete|A biztonsági szabály törlése|
|/networkSecurityGroups/securityRules/read|Biztonsági szabály definíciójának beolvasása|
|/networkSecurityGroups/securityRules/write|A szabály létrehozása vagy meglévő biztonsági szabály frissítése|
|/networkSecurityGroups/write|Hálózati biztonsági csoport létrehozása vagy meglévő hálózati biztonsági csoport frissítése|
|/networkWatchers/availableProvidersList/action|Adja vissza az összes rendelkezésre álló internet szolgáltatók a megadott Azure-régió.|
|/networkWatchers/azureReachabilityReport/action|Adja vissza a relatív késés pontszám internetes szolgáltatók megadott helyről az Azure-régiók.|
|/networkWatchers/configureFlowLog/action|Konfigurálja a cél erőforráson folyamat naplózását.|
|/networkWatchers/connectionMonitors/delete|Figyelő kapcsolat törlése|
|/networkWatchers/connectionMonitors/Providers/Microsoft.Insights/ diagnosticSettings olvasása|Figyelő kapcsolat diagnosztikai beállításainak beolvasása|
|/networkWatchers/connectionMonitors/Providers/Microsoft.Insights/ diagnosticSettings írása|Létrehozza vagy frissíti a kapcsolat figyelő diagnosztikai beállításai|
|/networkWatchers/connectionMonitors/Providers/Microsoft.Insights/ metricDefinitions olvasása|Figyelő kapcsolat elérhető metrikai meghatározások beolvasása|
|/networkWatchers/connectionMonitors/Query/Action|A lekérdezés megadott végpontok közötti kapcsolat ellenőrzése|
|/networkWatchers/connectionMonitors/Read|Részletek a kapcsolat figyelője|
|/networkWatchers/connectionMonitors/Start/Action|Indítsa el a megadott végpontok közötti kapcsolat ellenőrzése|
|/networkWatchers/connectionMonitors/STOP/Action|Állítsa le vagy felfüggesztése megadott végpontok közötti kapcsolat ellenőrzése|
|/networkWatchers/connectionMonitors/Write|Egy kapcsolat figyelő létrehozása|
|/networkWatchers/connectivityCheck/Action|Ellenőrzi a lehetőségét a közvetlen TCP-kapcsolatot a virtuális gép egy adott végpont, többek között a másik virtuális gép vagy egy tetszőleges távoli kiszolgálón.|
|/networkWatchers/delete|Törli a hálózati figyelőt|
|/networkWatchers/ipFlowVerify/action|Adja vissza, hogy a csomag engedélyezett vagy megtagadott vagy onnan az adott célhelyre.|
|/networkWatchers/lenses/delete|A fókuszok téma törlése|
|/networkWatchers/lenses/Query/Action|A megadott végpont a hálózati forgalom figyelése lekérdezés|
|/networkWatchers/lenses/Read|Részletek a fókuszt|
|/networkWatchers/lenses/Start/Action|Indítsa el a megadott végpont a hálózati forgalom figyelése|
|/networkWatchers/lenses/STOP/Action|A megadott végpont a hálózati forgalom figyelése STOP/felfüggesztése|
|/networkWatchers/lenses/write|Létrehoz egy fókuszban|
|/networkWatchers/nextHop/Action|A megadott cél és a cél IP-cím térjen vissza a következő ugrás típusa, és ezután az IP-cím legyen.|
|/networkWatchers/packetCaptures/delete|A csomagrögzítéssel törlése|
|/networkWatchers/packetCaptures/queryStatus/action|Lekérdezi a tulajdonságok és a csomag rögzítési erőforrás állapotával kapcsolatos adatokat.|
|/networkWatchers/packetCaptures/read|A csomag rögzítési definíciójának beolvasása|
|/networkWatchers/packetCaptures/stop/action|A futó csomag-rögzítési munkamenet leállítása.|
|/networkWatchers/packetCaptures/write|A csomagrögzítéssel létrehozása|
|/networkWatchers/queryFlowLogStatus/action|Erőforrás-naplózás folyamat állapotának beolvasása.|
|/networkWatchers/queryTroubleshootResult/action|Lekérdezi a korábban futtatott vagy jelenleg hibaelhárítási eredménye hibaelhárítási művelet futtatása.|
|/networkWatchers/Read|A hálózati figyelő definíciójának beolvasása|
|/networkWatchers/securityGroupView/action|A konfigurált és hatékony hálózati biztonsági csoport szabálya a virtuális gép megtekintése.|
|/networkWatchers/topology/Action|Lekérdezi a hálózati szintű áttekintés a erőforrásokat, és azok erőforráscsoportban.|
|/networkWatchers/troubleshoot/Action|Egy hálózati erőforráshoz az Azure-ban végzett hibaelhárítás indítása.|
|/ networkWatchers/írása|A hálózati figyelőt létrehoz vagy frissít egy meglévő hálózati figyelőt|
|/Operations/Read|Rendelkezésre álló műveletek beolvasása|
|/publicIPAddresses/delete|Törli a nyilvános IP-cím.|
|/publicIPAddresses/join/action|A nyilvános IP-cím illesztése|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read|A nyilvános IP-cím diagnosztikai beállításainak beolvasása|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write|A nyilvános IP-cím diagnosztikai beállításainak létrehozása vagy frissítése|
|/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read|A naplófájl-definíciói nyilvános IP-cím beszerzése|
|/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read|A metrikák definíciók nyilvános IP-cím beszerzése|
|/publicIPAddresses/read|A nyilvános IP-cím definíciójának beolvasása|
|/publicIPAddresses/write|A nyilvános IP-cím létrehozza vagy frissíti a meglévő nyilvános IP-címnek. |
|/ regisztrációs/művelet|Az előfizetés regisztrálása|
|/routeFilters/DELETE|Egy útvonal szűrődefinícióban törlése|
|/routeFilters/JOIN/Action|Útvonal szűrő illesztése|
|/routeFilters/Read|Útvonal szűrő definíciójának beolvasása|
|/routeFilters/routeFilterRules/delete|Törli a útvonal szűrő szabályát leíró definíció beolvasása|
|/routeFilters/routeFilterRules/read|Útvonal szűrési szabály definíciójának beolvasása|
|/routeFilters/routeFilterRules/write|Egy útvonal-szűrő szabályt hoz létre, vagy meglévő útvonal szűrési szabály frissítése|
|/ routeFilters/írása|Útvonal szűrő létrehoz vagy frissít egy meglévő rotue szűrő|
|/routeTables/DELETE|Útvonaltábla-definíció törlése|
|/routeTables/JOIN/Action|Egy útválasztási táblázatot illesztése|
|/routeTables/Read|Útvonaltábla-definíció beolvasása|
|/routeTables/routes/delete|Útvonal-definíció törlése|
|/routeTables/routes/Read|Útvonal definíciójának beolvasása|
|/routeTables/routes/Write|Új útvonal létrehozása vagy meglévő útvonal frissítése|
|/ routeTables/írása|Útvonaltábla létrehozása vagy meglévő útvonaltábla frissítése|
|/securegateways/applicationRuleCollections/delete|Az alkalmazás-szabálygyűjtemény egy biztonságos átjáró törlése|
|/securegateways/applicationRuleCollections/read|Az alkalmazás-szabálygyűjtemény egy adott biztonságos átjáró beolvasása|
|/securegateways/applicationRuleCollections/write|Létrehozza vagy frissíti az alkalmazás-szabálygyűjtemény egy biztonságos átjáró|
|/securegateways/delete|Biztonságos átjáró törlése|
|/securegateways/networkRuleCollections/delete|Egy hálózati szabálygyűjteményhez biztonságos átjáró törlése|
|/securegateways/networkRuleCollections/read|Egy adott biztonságos átjáró egy hálózati szabálygyűjteményhez beolvasása|
|/securegateways/networkRuleCollections/write|Létrehozza vagy frissíti egy hálózati szabálygyűjteményhez biztonságos átjáró|
|/securegateways/read|Biztonságos átjáró beolvasása|
|/securegateways/write|Létrehozza vagy frissíti egy biztonságos átjáró|
|/serviceEndpointPolicies/delete|A szolgáltatási végpont házirend törlése|
|/serviceEndpointPolicies/join/action|A szolgáltatási végpont házirend illesztése|
|/serviceEndpointPolicies/joinSubnet/action|A szolgáltatási végpont házirendek alhálózat illesztése|
|/serviceEndpointPolicies/read|Lekérdezi a szolgáltatási végpont házirend leírása|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete|A szolgáltatási végpont házirend-definíció törlése|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Read|Lekérdezi a szolgáltatási végpont házirend Definition visszafejtés|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write|Szolgáltatási végpont házirend-definíció létrehozása vagy egy meglévő szolgáltatási végpont házirend-definíció frissítése|
|/serviceEndpointPolicies/write|A szolgáltatási végpont házirend létrehozza vagy frissíti a meglévő szolgáltatási végpont házirend|
|/trafficManagerGeographicHierarchies/read|Lekérdezi a régiókban, amelyek együtt a földrajzi forgalom-útválasztási módszert tartalmazó Traffic Manager Geographic hierarchia|
|/trafficManagerProfiles/azureEndpoints/delete|Azure-végpont törlése egy meglévő Traffic Manager-profil. A TRAFFIC Manager útválasztási forgalmat a törölt Azure-beli végpont le fog állni.|
|/trafficManagerProfiles/azureEndpoints/read|Lekérdezi egy Azure-végpontot, amely a Traffic Manager-profil, beleértve az adott Azure-beli végpont tulajdonságait.|
|/trafficManagerProfiles/azureEndpoints/write|Új Azure-végpont hozzáadása a meglévő Traffic Manager-profilt, vagy egy meglévő Azure-beli végpont, hogy a Traffic Manager-profil tulajdonságainak frissítéséhez.|
|/trafficManagerProfiles/delete|A Traffic Manager-profil törlése. A Traffic Manager-profil társított összes beállítást elvész, és a profil már nem használható a forgalom irányításához.|
|/trafficManagerProfiles/externalEndpoints/delete|Külső végpont törlése egy meglévő Traffic Manager-profil. A TRAFFIC Manager útválasztási forgalmat a törölt külső végpont le fog állni.|
|/trafficManagerProfiles/externalEndpoints/read|Lekérdezi a külső végpont, amely a Traffic Manager-profil, beleértve a külső végpont tulajdonságait.|
|/trafficManagerProfiles/externalEndpoints/write|Új külső végpont hozzáadása a meglévő Traffic Manager-profilt, vagy egy meglévő külső végpont, hogy a Traffic Manager-profil tulajdonságainak frissítéséhez.|
|/trafficManagerProfiles/heatMaps/read|A Traffic Manager Hőtérkép lekérdezi a megadott Traffic Manager-profil helyét és a forrás IP-címekként számát és a késés adatait tartalmazó.|
|/trafficManagerProfiles/nestedEndpoints/delete|A beágyazott végpont törlése egy meglévő Traffic Manager-profil. A TRAFFIC Manager útválasztási forgalmat a törölt beágyazott végpont le fog állni.|
|/trafficManagerProfiles/nestedEndpoints/read|Lekérdezi egy beágyazott végpontot, amely a Traffic Manager-profil, beleértve, hogy a beágyazott végpont tulajdonságait.|
|/trafficManagerProfiles/nestedEndpoints/write|Adja hozzá új beágyazott végpont egy már meglévő Traffic Manager-profilt, vagy egy meglévő beágyazott végpont a Traffic Manager-profil tulajdonságainak frissítéséhez.|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read|A Traffic Manager diagnosztikai beállításainak beolvasása|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti a Traffic Manager diagnosztikai beállítások, a művelet nem hozhatók insights erőforrás-szolgáltató.|
|/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read|A Traffic Manager eseményeinek beolvasása|
|/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read|Elérhető metrikai meghatározások beolvasása a Traffic Manager.|
|/trafficManagerProfiles/read|A Traffic Manager-profil konfigurációjának beolvasása. Ez magában foglalja a DNS-beállítások, forgalom útválasztásához tartozó beállításokat, végpontmonitoring beállításai, és továbbítja a Traffic Manager-profil végpontok listáját.|
|/trafficManagerProfiles/write|Traffic Manager-profil létrehozása, vagy egy meglévő Traffic Manager-profil konfigurációjának módosítása. Ez magában foglalja a engedélyezésére, vagy egy profil letiltása folyamatban van, és módosítja a DNS-beállítások, a forgalom útválasztásához tartozó beállításokat vagy a végpontmonitoring beállításai. A Traffic Manager-profil által kezelendő végpontok hozzáadott, eltávolított, engedélyezhető vagy le van tiltva.|
|/trafficManagerUserMetricsKeys/delete|A valós idejű felhasználói metrikák gyűjtemény használt előfizetés szintű kulcs törlése.|
|/trafficManagerUserMetricsKeys/read|A valós idejű felhasználói metrikák gyűjtemény használt előfizetés szintű kulcs lekérése.|
|/trafficManagerUserMetricsKeys/write|Létrehoz egy új előfizetés-szintű kulcs valós idejű felhasználói metrikák gyűjtemény használható.|
|/ unregister/művelet|Az előfizetés regisztrációjának törlése|
|/virtualHubs/delete|Egy virtuális központi törlése|
|/virtualHubs/hubVirtualNetworkConnections/delete|Törli a HubVirtualNetworkConnection|
|/virtualHubs/hubVirtualNetworkConnections/read|Egy HubVirtualNetworkConnection beolvasása|
|/virtualHubs/hubVirtualNetworkConnections/write|Hozható létre vagy frissíthető egy HubVirtualNetworkConnection|
|/virtualHubs/read|Egy virtuális központi beolvasása|
|/virtualHubs/write|Létrehozni vagy frissíteni a virtuális Hub|
|/virtualnetworkgateways/connections/read|Get VirtualNetworkGatewayConnection|
|/virtualNetworkGateways/delete|Törli az átjárónak|
|/virtualnetworkgateways/generatevpnclientpackage/action|Az átjárónak VpnClient csomagot|
|/virtualnetworkgateways/generatevpnprofile/action|Pedig VpnProfile csomag létrehozása|
|/virtualnetworkgateways/getadvertisedroutes/action|Lekérdezi pedig hirdetett útvonalakat|
|/virtualnetworkgateways/getbgppeerstatus/action|Lekérdezi a pedig a bgp társ állapota|
|/virtualnetworkgateways/getlearnedroutes/action|Megtudta, pedig útvonalak beolvasása|
|/virtualnetworkgateways/getvpnclientipsecparameters/action|Vpnclient Ipsec paramétereinek lekérése pedig P2S-ügyfél.|
|/virtualnetworkgateways/getvpnprofilepackageurl/action|Lekérdezi egy előre generált VPN-ügyfélcsomag profil URL-címe|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read|A virtuális hálózati átjáró diagnosztikai beállításainak beolvasása|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write|Létrehozza vagy frissíti a virtuális hálózati átjáró diagnosztikai beállítások, a művelet nem hozhatók insights erőforrás-szolgáltató.|
|/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read|Virtuális hálózati átjáró eseményeinek beolvasása|
|/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read|A virtuális hálózati átjáró elérhető metrikai meghatározások beolvasása|
|/virtualNetworkGateways/read|Lekérdezi a pedig|
|/virtualnetworkgateways/reset/action|Alaphelyzetbe állítja a pedig|
|/virtualnetworkgateways/setvpnclientipsecparameters/action|Vpnclient Ipsec paramétert meg az átjárónak P2S-ügyfél.|
|/virtualnetworkgateways/supportedvpndevices/action|Listák támogatott VPN-eszközök|
|/virtualNetworkGateways/write|Létrehozza vagy frissíti az átjárónak|
|/virtualNetworks/checkIpAddressAvailability/read|Ellenőrizze, hogy a megadott virtuális hálózati érhető el-e IP-cím|
|/virtualNetworks/customViews/get/action|A virtuális hálózati egyéni nézet tartalmának lekérdezése|
|/virtualNetworks/customViews/read|Virtuális hálózat egyéni nézet definíciójának lekérdezése|
|/virtualNetworks/delete|Virtuális hálózat törlése|
|/virtualNetworks/peer/action|A virtuális hálózat egy másik virtuális hálózathoz állomásokhoz|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read|A virtuális hálózat diagnosztikai beállításainak beolvasása|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write|A virtuális hálózat diagnosztikai beállításainak létrehozása vagy frissítése|
|/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read|A naplófájl-definíciói virtuális hálózat beolvasása|
|/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read|A virtuális hálózat metrikai meghatározásainak beolvasása|
|/virtualNetworks/read|A virtuális hálózat definíciójának beolvasása|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete|Törli a virtuális hálózati társviszony-létesítési proxy|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/read|A virtuális hálózati társviszony-létesítés proxydefiníció beolvasása|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/write|A virtuális hálózati társviszony-létesítési proxy létrehoz vagy frissít egy meglévő virtuális hálózati társviszony-létesítési proxy|
|/virtualNetworks/subnets/delete|Virtuális hálózati alhálózat törlése|
|/virtualNetworks/subnets/join/action|Egy virtuális hálózathoz csatlakozik|
|/virtualNetworks/subnets/joinViaServiceEndpoint/action|Alhálózathoz csatlakozik az erőforrás, például a tárfiók, vagy az SQL-adatbázis.|
|/virtualNetworks/subnets/read|Virtuális hálózati alhálózat definíciójának beolvasása|
|/virtualNetworks/subnets/resourceNavigationLinks/delete|Egy erőforrás-navigációs hivatkozás törlése|
|/virtualNetworks/subnets/resourceNavigationLinks/read|Az erőforrás-navigációs hivatkozás definíciójának beolvasása|
|/virtualNetworks/subnets/resourceNavigationLinks/write|Egy erőforrás-navigációs hivatkozás létrehoz vagy frissít egy meglévő erőforrás-navigációs hivatkozás|
|/virtualNetworks/subnets/virtualMachines/read|Virtuális hálózati alhálózat virtuális gépeire mutató hivatkozások beolvasása|
|/virtualNetworks/subnets/write|Virtuális hálózati alhálózat létrehozása vagy egy meglévő virtuális hálózati alhálózat frissítése|
|/virtualNetworks/taggedTrafficConsumers/delete|Címkézett forgalom ügyféllel törlése|
|/virtualNetworks/taggedTrafficConsumers/read|A forgalom fogyasztói címkézett definíciójának beolvasása|
|/virtualNetworks/taggedTrafficConsumers/validate/action|Ellenőrzi az címkézett forgalom ügyféllel|
|/virtualNetworks/taggedTrafficConsumers/write|Címkézett forgalom ügyféllel létrehoz vagy frissít egy meglévő címkézett forgalom fogyasztói|
|/virtualNetworks/usages/read|Az IP-módjait minden, a virtuális hálózati alhálózat beolvasása|
|/virtualNetworks/virtualMachines/read|Virtuális hálózat virtuális gépeire mutató hivatkozások beolvasása|
|/virtualNetworks/virtualNetworkPeerings/delete|Törli a virtuális hálózati társviszony-létesítés|
|/virtualNetworks/virtualNetworkPeerings/read|Virtuális hálózati társviszony-létesítési definíciójának beolvasása|
|/virtualNetworks/virtualNetworkPeerings/write|Virtuális hálózati társviszony-létesítés létrehozása vagy frissítése. egy meglévő virtuális hálózati társviszony|
|/virtualNetworks/write|Létrehoz egy virtuális hálózatot, vagy meglévő virtuális hálózat frissítése|
|/virtualNetworkTaps/delete|Koppintson a virtuális hálózat törlése|
|/virtualNetworkTaps/join/action|A virtuális hálózati tap illesztése|
|/virtualNetworkTaps/read|Virtuális hálózati Tap beolvasása|
|/virtualNetworkTaps/write|Létrehozni vagy frissíteni a virtuális hálózati Tap|
|/virtualwans/delete|Törli a virtuális Wan|
|/virtualwans/Read|Get virtuális a Wan|
|/virtualWans/virtualHubProxies/delete|Egy virtuális központi proxy törlése|
|/virtualWans/virtualHubProxies/read|Egy virtuális központi proxy definíciójának beolvasása|
|/virtualWans/virtualHubProxies/write|Egy virtuális központi proxy létrehoz vagy frissít egy virtuális központi proxy|
|/virtualwans/virtualHubs/read|Egy virtuális Wan tartozó összes virtuális hubok lekérdezi.|
|/virtualwans/vpnconfiguration/read|Lekérdezi a VPN-konfiguráció|
|/virtualWans/vpnSiteProxies/delete|Törli a Vpn-hely proxy|
|/virtualWans/vpnSiteProxies/read|Vpn-hely proxy definíciójának beolvasása|
|/virtualWans/vpnSiteProxies/write|A Vpn-hely proxy létrehozása vagy frissítése egy Vpn-hely proxy|
|/virtualwans/vpnSites/read|Lekéri az összes társított virtuális Wan VPN-hely.|
|/ virtualwans/írása|Létrehozni vagy frissíteni a virtuális Wan|
|/vpnGateways/read|Lekérdezi a A(z).|
|/vpnGateways/vpnConnections/read|Lekérdezi a VPN-kapcsolatot.|
|/vpnGateways/vpnConnections/write|A VPN-kapcsolat helyezi.|
|/vpnGateways/write|A A(z) helyezi.|
|/vpnsites/delete|Törli a Vpn-hely erőforrást.|
|/vpnsites/Read|A Vpn-hely erőforrás lekérése.|
|/ vpnsites/írása|Létrehozza vagy frissíti a Vpn-hely erőforrás.|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Művelet | Leírás |
|---|---|
|/ CheckNamespaceAvailability/művelet|Adott névtér-erőforrásnév elérhetőségének ellenőrzése a Notification Hub szolgáltatásban.|
|/Namespaces/authorizationRules/action|A névtér-engedélyezési szabályok leírásai listájának beolvasása.|
|/Namespaces/authorizationRules/delete|Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető. |
|/Namespaces/authorizationRules/listkeys/action|A névtérhez tartozó kapcsolati karakterlánc beolvasása|
|/Namespaces/authorizationRules/read|A névtér-engedélyezési szabályok leírásai listájának beolvasása.|
|/Namespaces/authorizationRules/regenerateKeys/action|Egy névtér-engedélyezési szabály elsődleges vagy másodlagos kulcsának újragenerálása; meg kell adni az újragenerálni kívánt kulcsot|
|/Namespaces/authorizationRules/write|A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|/Namespaces/CheckNotificationHubAvailability/action|Adott értesítésiközpont-név elérhetőségének ellenőrzése egy névtérben.|
|Vagy névterek/törlése|Egy névtérerőforrás törlése|
|/Namespaces/NotificationHubs/authorizationRules/action|Az értesítésiközpont-engedélyezési szabályok listájának beolvasása|
|/Namespaces/NotificationHubs/authorizationRules/delete|Értesítésiközpont-engedélyezési szabályok törlése|
|/Namespaces/NotificationHubs/authorizationRules/listkeys/action|Az értesítési központhoz tartozó kapcsolati karakterlánc beolvasása|
|/Namespaces/NotificationHubs/authorizationRules/read|Az értesítésiközpont-engedélyezési szabályok listájának beolvasása|
|/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action|Egy értesítésiközpont-engedélyezési szabály elsődleges vagy másodlagos kulcsának újragenerálása; meg kell adni az újragenerálni kívánt kulcsot|
|/Namespaces/NotificationHubs/authorizationRules/write|Értesítési központ engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|/Namespaces/NotificationHubs/debugSend/action|Leküldéses tesztértesítés küldése.|
|/Namespaces/NotificationHubs/Delete|Értesítésiközpont-erőforrás törlése|
|/Namespaces/NotificationHubs/metricDefinitions/read|Namespace metrikák erőforrás leírása listájának beolvasása|
|/Namespaces/NotificationHubs/pnsCredentials/action|Minden értesítési központ PNS hitelesítő adatainak lekérése. Ez magában foglalja a wns-ből, a MPNS, a APNS, a GCM és a Baidu hitelesítő adatokat|
|/Namespaces/NotificationHubs/read|Az értesítésiközpont-erőforrások leírásai listájának beolvasása|
|/Namespaces/NotificationHubs/write|Hozzon létre egy értesítési központot, és a tulajdonságok frissítése. A Tulajdonságok főként PNS hitelesítő adatok közé tartozik. Az engedélyezési szabályok és a TTL-t|
|Névterek/olvasása|A névtérerőforrások leírásai listájának beolvasása|
|Névterek/írása|Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a kapacitás, a Namespace nem frissíthető tulajdonságait.|
|/ regisztrációs/művelet|A Notification Hubs-erőforrásszolgáltatóra való előfizetés regisztrálása, valamint a névterek és a Notification Hubs-példányok létrehozásának lehetővé tétele|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Művelet | Leírás |
|---|---|
|/linkTargets/Read|Felsorolja a meglévő fiókokat, amelyek nem kapcsolódnak az Azure-előfizetéssel. Az Azure-előfizetés összekapcsolása egy munkaterület, egy felhasználói azonosítót, ez a művelet az ügyfél-azonosító tulajdonsággal, a munkaterület létrehozása művelet által visszaadott használja.|
|/ regisztrációs/művelet|Egy erőforrás-szolgáltató előfizetés regisztrálásához.|
|/Workspaces/Analytics/Query/Action|A keresés új motor használatával.|
|/Workspaces/Analytics/Query/Schema/Read|Keresési séma V2 beolvasása.|
|/Workspaces/API/Query/Action|A keresés új motor használatával.|
|/Workspaces/API/Query/Schema/Read|Keresési séma V2 beolvasása.|
|/workspaces/configurationScopes/delete|A konfigurációs hatókörben törlése|
|/workspaces/configurationScopes/read|A konfigurációs hatókörben beolvasása|
|/workspaces/configurationScopes/write|Konfigurációs hatókör megadása|
|/workspaces/datasources/delete|Törölje az adott munkaterületen adatforrásokhoz.|
|/Workspaces/Datasources/Read|Adatforrások beolvasása az adott munkaterületen.|
|/Workspaces/Datasources/Write|Az adott munkaterületen Adatforrás létrehozása/frissítése.|
|/Workspaces/DELETE|A munkaterület törlése. Ha a munkaterületet lett csatolva egy meglévő munkaterület a létrehozás időpontjában volt csatolva a munkaterület nem törlődik.|
|/Workspaces/generateregistrationcertificate/Action|A munkaterület regisztrációs tanúsítványt hoz létre. Ezzel a tanúsítvánnyal a munkaterületen a Microsoft System Center Operation Manager csatlakozni.|
|/workspaces/intelligencepacks/disable/action|Egy intelligence Pack csomagot egy adott munkaterület letiltja.|
|/workspaces/intelligencepacks/enable/action|Lehetővé teszi, hogy egy adott munkaterület egy intelligence Pack csomagot.|
|/workspaces/intelligencepacks/read|Megjeleníti egy adott worksapce esetében az eszközintelligencia-csomagokat, és is megjeleníti, hogy a csomag engedélyezett vagy letiltott adott munkaterület.|
|/workspaces/linkedServices/delete|A szolgáltatás kapcsolódó törlése megadott munkaterületen.|
|/workspaces/linkedServices/read|A társított szolgáltatások munkaterület megadott.|
|/workspaces/linkedServices/write|Létrehozása/frissítése kapcsolódó szolgáltatások az adott munkaterületen.|
|/Workspaces/listKeys/Action|Lekéri a listában kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó.|
|/Workspaces/listKeys/Read|Lekéri a listában kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó.|
|/Workspaces/managementGroups/Read|System Center Operations Manager felügyeleti csoport csatlakoztatva a munkaterület neve és metaadatok beolvasása.|
|/workspaces/metricDefinitions/read|A metrika definíciók lekérése a munkaterületen|
|/workspaces/notificationSettings/delete|A felhasználói értesítés beállításait a munkaterület törlése.|
|/workspaces/notificationSettings/read|A felhasználói értesítés beállításait a munkaterület beolvasása.|
|/workspaces/notificationSettings/write|Állítsa be a felhasználói értesítés beállításait a munkaterületen.|
|/Workspaces/PURGE/Action|Megadott adatok törléséhez a munkaterület|
|/Workspaces/Read|Lekérdezi a meglévő-munkaterülettel|
|/workspaces/savedSearches/delete|Törli a mentett keresési lekérdezés|
|/workspaces/savedSearches/read|Lekérdezi a mentett keresési lekérdezés|
|/workspaces/savedSearches/write|A mentett keresési lekérdezést hoz létre|
|/Workspaces/Schema/Read|A keresési séma lekérése a munkaterületen.  Keresési séma kitett mezőket és a típusukat tartalmazza.|
|/Workspaces/Search/Action|Keresési lekérdezés végrehajtása|
|/workspaces/sharedKeys/action|Lekéri a megosztott kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó.|
|/workspaces/sharedKeys/read|Lekéri a megosztott kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó.|
|/workspaces/storageinsightconfigs/delete|A tárolási konfiguráció törlése. Ez a jövőben nem Microsoft Operational Insights adatok beolvasása a tárfiókból.|
|/workspaces/storageinsightconfigs/read|Lekérdezi a tárolási konfiguráció.|
|/workspaces/storageinsightconfigs/write|Létrehoz egy új tárolási konfigurációt. Ezek a beállítások segítségével olvasnak be adatokat egy hely a meglévő tárfiókot.|
|/Workspaces/usages/Read|Lekérdezi a használati adatok egy munkaterület, beleértve a munkaterület által beolvasott adatok mennyiségét.|
|/ munkaterületek/írása|Új munkaterület vagy egy meglévő munkaterület mutató hivatkozások azáltal, hogy az ügyfél-azonosítója a meglévő munkaterületről hozható létre.|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Művelet | Leírás |
|---|---|
|/managementAssociations/delete|Törölje a meglévő felügyeleti társítása|
|/managementAssociations/read|Meglévő felügyeleti társítása beolvasása|
|/ managementAssociations/írása|Hozzon létre egy új felügyeleti társítása|
|/managementConfigurations/delete|Törölje a meglévő felügyeleti Configuratin|
|/managementConfigurations/Read|Meglévő felügyeleti konfiguráció beolvasása|
|/ managementConfigurations/írása|Hozzon létre egy új felügyeleti konfiguráció|
|/ regisztrációs/művelet|Egy erőforrás-szolgáltató előfizetés regisztrálásához.|
|/solutions/delete|Törölje a meglévő OMS-megoldás|
|/Solutions/Read|Kilépés az OMS-megoldás beszerzése|
|/ megoldások írása|Új OMS-megoldás létrehozása|

## <a name="microsoftportal"></a>Microsoft.Portal

| Művelet | Leírás |
|---|---|
|/dashboards/delete|Az irányítópult eltávolítása az előfizetésből.|
|/dashboards/Read|Az előfizetéshez tartozó irányítópultok beolvasása.|
|/ irányítópultok/írása|Irányítópult felvétele vagy átalakítása előfizetéssé.|

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

| Művelet | Leírás |
|---|---|
|/capacities/checkNameAvailability/action|Ellenőrzi, hogy a Power BI dedikált kapacitás a megadott név érvényes, és nincs használatban.|
|/capacities/DELETE|Törli a Power BI dedikált kapacitás.|
|/capacities/providers/Microsoft.Insights/metricDefinitions/read|A Power BI dedikált kapacitás elérhető metrikai meghatározások beolvasása.|
|/capacities/Read|A megadott Power BI dedikált kapacitás adatainak beolvasása.|
|/ kapacitások/írása|Létrehozza vagy frissíti a megadott Power BI dedikált kapacitásának kihasználásához.|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Művelet | Leírás |
|---|---|
|/locations/allocatedStamp/read|A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ|
|/locations/allocateStamp/action|AllocateStamp egy szolgáltatás által használt belső művelet|
|/Locations/backupPreValidateProtection/Action||
|/Locations/backupStatus/Action|Helyreállítási szolgáltatások tárolók a biztonsági mentés állapotának ellenőrzése|
|/Locations/backupValidateFeatures/Action|Szolgáltatások ellenőrzése|
|/Operations/Read|Műveletet az erőforrás-szolgáltató műveleteinek listáját adja vissza.|
|/ regisztrációs/művelet|Regiszterekben előfizetést az adott erőforrás-szolgáltató|
|/Vaults/backupconfig/read|A helyreállításhoz adja vissza konfigurációs szolgáltatások tárolóban.|
|/Vaults/backupconfig/write|Frissítések konfigurációja helyreállítási szolgáltatások tárolóban.|
|Tárolók/backupEngines/olvasása|A tárolóban regisztrált biztonsági másolatokat kezelő kiszolgálókat adja vissza.|
|/Vaults/backupFabrics/{fabricName}/protectionContainers/{containerName}/items/read|A tárolóban lévő összes beolvasásának|
|/Vaults/backupFabrics/backupProtectionIntent/write|A biztonságimásolat-készítő védelmi leképezés létrehozásához|
|/Vaults/backupFabrics/operationResults/read|A művelet állapotának beolvasása|
|/Vaults/backupFabrics/protectableContainers/read|Az összes védhető tároló beolvasása|
|/Vaults/backupFabrics/protectionContainers/inquire/action|Hajtsa végre a lekérdezést a tárolóban futó alkalmazások és szolgáltatások|
|/Vaults/backupFabrics/protectionContainers/operationResults/read|Beolvassa a védelmi tárolón végrehajtott művelet eredményét.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action|Biztonsági másolat készítése egy védett elemről.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/delete|Törli a védett elem|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read|Beolvassa a védett elemeken végrehajtott művelet eredményét.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read|A védett elemeken végrehajtott művelet állapotát adja vissza.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/read|A védett elem kapcsolatobjektum-adatait adja vissza|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/provisionInstantItemRecovery/action|Védett elem rendelkezés azonnali elem helyreállítása|
|/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read|Védett elemek helyreállítási pontjainak beolvasása.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/restore/action|Védett elemek helyreállítási pontjainak visszaállítása.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/revokeInstantItemRecovery/action|Védett elem azonnali elem helyreállítása visszavonása|
|/Vaults/backupFabrics/protectionContainers/protectedItems/write|Biztonsági mentési védett típusú elem létrehozása|
|/Vaults/backupFabrics/protectionContainers/read|Minden regisztrált tárolókat ad vissza|
|/Vaults/backupFabrics/protectionContainers/write|Tárolót hoz létre regisztrált|
|/Vaults/backupFabrics/refreshContainers/action|Frissíti a tároló listáját|
|/ Tárolók/backupJobs/Mégse/művelet|A feladat megszakítása|
|/Vaults/backupJobs/operationResults/read|A feladatművelet eredményét adja vissza.|
|Tárolók/backupJobs/olvasása|Minden feladat objektumot ad vissza|
|/ Tárolók/backupJobsExport/művelet|Exportálási feladat|
|/Vaults/backupJobsExport/operationResults/read|Az exportálási feladat művelet eredményét adja vissza.|
|/Vaults/backupManagementMetaData/read|A Recovery Services-tároló biztonságimásolat-kezelési metaadatait adja vissza.|
|/Vaults/backupOperationResults/read|A Recovery Services-tárolóval kapcsolatos biztonsági mentés eredményét adja vissza.|
|Tárolók/backupOperations/olvasása|Visszaadja a biztonsági mentési művelet állapotának helyreállítási szolgáltatások tárolóban.|
|/Vaults/backupPolicies/delete|Védelmi házirend törlése|
|/Vaults/backupPolicies/operationResults/read|A szabályzatművelet eredményeinek beolvasása.|
|Tárolók/backupPolicies/műveletek/olvasása|Házirend művelet állapotának beolvasása.|
|Tárolók/backupPolicies/olvasása|Visszaadja az összes védelmi házirend|
|/Vaults/backupPolicies/write|Védelmi házirend létrehozása|
|/Vaults/backupProtectableItems/read|A védhető elemek listáját adja vissza.|
|/Vaults/backupProtectedItems/read|A védett elemek listájának beolvasása.|
|/Vaults/backupProtectionContainers/read|Az előfizetéshez tartozó összes tárolókat ad vissza|
|/Vaults/backupSecurityPIN/action|Visszaadja a PIN-kód biztonsági tároló szolgáltatásról a helyreállításhoz.|
|/Vaults/backupstorageconfig/read|Helyreállítási beolvasása-tárolási konfigurációját szolgáltatások tárolóban.|
|/Vaults/backupstorageconfig/write|Frissítések Tárkonfigurációt helyreállítási szolgáltatások tárolóban.|
|/Vaults/backupUsageSummaries/read|Recovery Services beolvasása védett elemek és a védett kiszolgálók összesítések.|
|Tárolók/tanúsítvány/írása|A frissítés erőforrás tanúsítvány művelet frissíti az erőforrás/tárolói hitelesítő adatainak tanúsítványa.|
|Vagy tárolók vagy törlése|A tároló törlése a művelet törli a megadott Azure-erőforrás "tárolóban" típusú|
|/Vaults/extendedInformation/delete|A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be|
|/Vaults/extendedInformation/read|A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be|
|/Vaults/extendedInformation/write|A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be|
|Tárolók/monitoringAlerts/olvasása|A riasztások lekéri a Recovery services-tároló.|
|Tárolók/monitoringAlerts/írása|Feloldja a riasztást.|
|Tárolók/monitoringConfigurations/olvasása|A helyreállítási szolgáltatás tároló értesítési beállításainak lekérése.|
|/Vaults/monitoringConfigurations/write|Konfigurálja az e-mail értesítések küldéséhez Recovery services-tároló.|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/read|Az Azure biztonsági mentési diagnosztika|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/write|Az Azure biztonsági mentési diagnosztika|
|/Vaults/providers/Microsoft.Insights/logDefinitions/read|Az Azure biztonsági mentési naplók|
|/Vaults/providers/Microsoft.Insights/metricDefinitions/read|Az Azure biztonsági mentési metrikák|
|Tárolók/olvasása|A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum|
|/Vaults/registeredIdentities/delete|A tároló regisztrációját művelet használható a tároló regisztrációját.|
|/Vaults/registeredIdentities/operationResults/read|Művelet alkalmazható Get művelet eredményeit a műveleti állapotának és az aszinkron módon elküldött művelet eredménye beolvasása|
|/Vaults/registeredIdentities/read|Az beszerzése tárolókat művelet alkalmazható a tárolók regisztrált erőforrás lekérése.|
|/Vaults/registeredIdentities/write|A szolgáltatás tárolójának regisztrálása műveletet szolgáltatásban való regisztráláshoz tárolója helyreállítási használható.|
|/vaults/replicationAlertSettings/read|Olvassa el a riasztások beállításai|
|/vaults/replicationAlertSettings/write|Minden riasztás beállításainak létrehozása vagy frissítése|
|/vaults/replicationEvents/read|Bármely események olvasása|
|/vaults/replicationFabrics/checkConsistency/action|Konzisztencia-ellenőrzést futtat a hálón|
|/vaults/replicationFabrics/delete|A hálók törlése|
|/vaults/replicationFabrics/deployProcessServerImage/action|Lemezkép központi telepítése|
|/vaults/replicationFabrics/read|A hálók olvasása|
|/vaults/replicationFabrics/reassociateGateway/action|Átjáró újbóli társítása|
|/vaults/replicationFabrics/remove/action|Távolítsa el a háló|
|/vaults/replicationFabrics/renewcertificate/action|Háló tanúsítványának megújítása|
|/vaults/replicationFabrics/replicationNetworks/read|Olvassa el a hálózatok|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete|Hálózati leképezések törlése|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read|Olvassa el a hálózatok leképezését|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write|Hozzon létre vagy hálózati leképezések frissítése|
|/vaults/replicationFabrics/replicationProtectionContainers/ discoverProtectableItem/action|Védhető objektum felderítése|
|/vaults/replicationFabrics/replicationProtectionContainers/read|Olvassa el a védelmi tárolókkal|
|/vaults/replicationFabrics/replicationProtectionContainers/remove/action|Távolítsa el a védelmi tároló|
|/ tárolók/replicationFabrics/replicationProtectionContainers/replicationProtectableItems olvasása|Bármely védhető elemek olvasása|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/applyRecoveryPoint/action|Helyreállítási pont alkalmazása|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/delete|Minden védett elemek törlése|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/failoverCommit/action|Feladatátvétel véglegesítésének|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/plannedFailover/action|Tervezett feladatátvétel|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/read|Minden védett elemek olvasása|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/recoveryPoints/read|Olvassa el a replikációs helyreállítási pontot|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/remove/action|Távolítsa el a védett elem|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/repairReplication/action|Javítás replikáció|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/reProtect/action|Állítsa a védett elem|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailover/action|Feladatátvétel tesztelése|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailoverCleanup/action|Teszt feladatátadás kitakarítását|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/unplannedFailover/action|Feladatátvétel|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/updateMobilityService/action|Frissítse a mobilitási szolgáltatás|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/write|Minden védett cikkek létrehozása vagy frissítése|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/delete|A védelmi tároló leképezéseket törlése|
|/ tárolók/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings olvasása|Olvassa el a védelmi tároló leképezések|
|/ tárolók/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/eltávolítás/művelet|Védelmitároló-leképezés eltávolítása|
|/ tárolók replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/írása|Létrehozni vagy frissíteni a védelmi tároló leképezések|
|/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action|Kapcsoló védelmi tároló|
|/vaults/replicationFabrics/replicationProtectionContainers/write|Létrehozni vagy frissíteni a védelmi tárolókkal|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete|Bármilyen helyreállítási szolgáltatók törlése|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/read|Olvassa el az összes helyreállítási szolgáltatók|
|/ tárolók/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/művelet|Frissítse a szolgáltatót|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action|Recovery Services-szolgáltató eltávolítása|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/write|Létrehozása vagy frissítése a helyreállítási szolgáltatók|
|/vaults/replicationFabrics/replicationStorageClassifications/read|A Tárhelybesorolások olvasása|
|/ tárolók vagy replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/törlése|A tárolási besorolás leképezéseket törlése|
|/ tárolók/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings olvasása|Olvassa el a tárolási besorolás leképezések|
|/ tárolók replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/írása|Létrehozni vagy frissíteni a tárolási besorolás leképezések|
|/vaults/replicationFabrics/replicationvCenters/delete|Feladatok törlése|
|/vaults/replicationFabrics/replicationvCenters/read|Olvassa el a megfelelő feladat|
|/vaults/replicationFabrics/replicationvCenters/write|A feladatok létrehozása vagy módosítása|
|/vaults/replicationFabrics/write|Létrehozni vagy frissíteni a háló|
|/vaults/replicationJobs/Cancel/Action|Megszakítása|
|/vaults/replicationJobs/Read|Olvassa el a megfelelő feladat|
|/vaults/replicationJobs/restart/Action|Indítsa újra a feladatot|
|/vaults/replicationJobs/Resume/Action|Feladat folytatása|
|/vaults/replicationPolicies/delete|A szabályzatoknak törlése|
|/vaults/replicationPolicies/Read|Olvassa el az összes házirend|
|/vaults/replicationPolicies/Write|Bármely irányelveinek létrehozása vagy frissítése|
|/vaults/replicationRecoveryPlans/delete|A helyreállítási terv törlése|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Feladatátvétel véglegesítésének helyreállítási terv|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Tervezett feladatátvétel helyreállítási terv|
|/vaults/replicationRecoveryPlans/read|A helyreállítási tervek olvasása|
|/vaults/replicationRecoveryPlans/reProtect/action|Állítsa a helyreállítási terv|
|/vaults/replicationRecoveryPlans/testFailover/action|Teszt feladatátvételi helyreállítási terv|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Teszt feladatátadás kitakarítását helyreállítási terv|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Feladatátvételi helyreállítási terv|
|/vaults/replicationRecoveryPlans/write|A helyreállítási tervek létrehozása vagy módosítása|
|/Vaults/tokenInfo/read|Recovery Services-tároló információi token értéket ad vissza.|
|/vaults/usages/Read|Olvassa el a tároló módjait|
|Tárolók/módjait/olvasása|Egy adott Recovery Services-tároló használati adatait adja vissza.|
|Tárolók/vaultTokens/olvasása|A tároló Token művelet alkalmazható tároló szintű háttérbeli műveletek tároló jogkivonatának beszerzéséhez.|
|Tárolók/írása|A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Művelet | Leírás |
|---|---|
|/ checkNameAvailability/művelet|A névtér adott előfizetésben való elérhetőségének ellenőrzése.|
|/ checkNamespaceAvailability/művelet|A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja helyette a CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/action|Frissítések Namespace engedélyezési szabályt. Ez az API depricated. A Namespace engedélyezési szabály sorkészletének frissítéséhez használja a PUT hívás... Ez a művelet nem támogatott az API verzió 2017-04-01.|
|/namespaces/authorizationRules/delete|Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető. |
|/namespaces/authorizationRules/listkeys/action|A névtérhez tartozó kapcsolati karakterlánc beolvasása|
|/namespaces/authorizationRules/read|A névtér-engedélyezési szabályok leírásai listájának beolvasása.|
|/namespaces/authorizationRules/regenerateKeys/action|Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása|
|/namespaces/authorizationRules/write|A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|/ névterek/törlése|Egy névtérerőforrás törlése|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Lekérdezi az engedélyezési szabályok kulcsok a vész-helyreállítási elsődleges névtér|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Katasztrófa utáni helyreállítás elsődleges Namespace meg az engedélyezési szabályok lekérése|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|A névtéralias rendelkezésre állását ellenőrzi megadott előfizetéshez.|
|/namespaces/disasterRecoveryConfigs/delete|A névtér társított vész-helyreállítási konfiguráció törlése. Ezt a műveletet csak az elsődleges névtér keresztül kell meghívni.|
|/namespaces/disasterRecoveryConfigs/failover/action|GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson.|
|/namespaces/disasterRecoveryConfigs/read|A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása.|
|/namespaces/disasterRecoveryConfigs/write|A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése.|
|/namespaces/HybridConnections/authorizationRules/action|A művelet HybridConnection frissítéséhez. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése.|
|/namespaces/HybridConnections/authorizationRules/delete|A művelet törli HybridConnection engedélyezési szabályokat|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|A kapcsolati karakterláncot HybridConnection beolvasása|
|/namespaces/HybridConnections/authorizationRules/read| A HybridConnection engedélyezési szabályok listájának lekérdezése|
|/namespaces/HybridConnections/authorizationRules/regeneratekeys/action|Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása|
|/namespaces/HybridConnections/authorizationRules/write|HybridConnection engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni.|
|/namespaces/HybridConnections/Delete|A művelet HybridConnection erőforrás törlése|
|/namespaces/HybridConnections/read|Listájának HybridConnection erőforrás leírása|
|/namespaces/HybridConnections/write|Hozzon létre vagy frissítés HybridConnection tulajdonságai.|
|/namespaces/messagingPlan/read|Lekérdezi az névtér az üzenetküldési megtervezése. Ez az API elavult. A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek... Ez a művelet nem támogatott az API verzió 2017-04-01.|
|/namespaces/messagingPlan/write|Frissíti az üzenetkezelési tervezése névtér. Ez az API elavult. A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek... Ez a művelet nem támogatott az API verzió 2017-04-01.|
|/Namespaces/operationresults/Read|Névtérművelet állapotának beolvasása|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Namespace metrikák erőforrás leírása listájának beolvasása|
|/Namespaces/Read|A névtérerőforrások leírásai listájának beolvasása|
|/namespaces/WcfRelays/authorizationRules/action|A művelet WcfRelay frissítéséhez. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése.|
|/namespaces/WcfRelays/authorizationRules/delete|A művelet törli WcfRelay engedélyezési szabályokat|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|A kapcsolati karakterláncot WcfRelay beolvasása|
|/namespaces/WcfRelays/authorizationRules/read| A WcfRelay engedélyezési szabályok listájának lekérdezése|
|/namespaces/WcfRelays/authorizationRules/regeneratekeys/action|Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása|
|/namespaces/WcfRelays/authorizationRules/write|WcfRelay engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni.|
|/namespaces/WcfRelays/Delete|A művelet WcfRelay erőforrás törlése|
|/Namespaces/WcfRelays/Read|Listájának WcfRelay erőforrás leírása|
|/Namespaces/WcfRelays/Write|Hozzon létre vagy frissítés WcfRelay tulajdonságai.|
|/ névterek/írása|Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a kapacitás, a Namespace nem frissíthető tulajdonságait.|
|/Operations/Read|Műveletek beolvasása|
|/ regisztrációs/művelet|A Relay erőforrás-szolgáltató előfizetésének regisztrálása és a Relay-erőforrások létrehozásának engedélyezése|
|/ unregister/művelet|A Relay erőforrás-szolgáltató előfizetésének regisztrálása és a Relay-erőforrások létrehozásának engedélyezése|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Művelet | Leírás |
|---|---|
|AvailabilityStatuses/current/olvasása|Beolvassa a megadott erőforrás rendelkezésre állási állapotát|
|AvailabilityStatuses/olvasása|Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát|
|/ healthevent/művelet|A megadott erőforrás állapotának változását jelöli|
|/healthevent/activated/Action|A megadott erőforrás állapotának változását jelöli|
|/healthevent/InProgress/action|A megadott erőforrás állapotának változását jelöli|
|/healthevent/Pending/Action|A megadott erőforrás állapotának változását jelöli|
|/healthevent/resolved/Action|A megadott erőforrás állapotának változását jelöli|
|/healthevent/Updated/action|A megadott erőforrás állapotának változását jelöli|
|/ regisztrációs/művelet|A Microsoft Resource Health-előfizetés regisztrálása|

## <a name="microsoftresources"></a>Microsoft.Resources

| Művelet | Leírás |
|---|---|
|/checkResourceName/action|Erőforrásnév érvényességének ellenőrzése.|
|/Deployments/Cancel/Action|Megszakítja az üzembe helyezést.|
|/Deployments/DELETE|Törli az üzemelő példányt.|
|/Deployments/Operations/Read|Beolvassa vagy listázza az üzembe helyezési műveleteket.|
|/Deployments/Read|Beolvassa vagy listázza az üzemelő példányokat.|
|/Deployments/Validate/Action|Érvényesíti az üzemelő példányt.|
|/ központi telepítések/írása|Létrehozza vagy frissíti az üzemelő példányt.|
|/Links/DELETE|Törli az erőforrás-hivatkozást.|
|/Links/Read|Beolvassa vagy listázza az erőforrás-hivatkozásokat.|
|/ hivatkozások/írása|Létrehozza vagy frissíti az erőforrás-hivatkozást.|
|/Marketplace/Purchase/Action|Erőforrás vásárlása a Piactéren.|
|/Providers/Read|A szolgáltatók listájának lekérése.|
|/resources/Read|Az erőforrások listájának beolvasása szűrők alapján.|
|/Subscriptions/Locations/Read|Beolvassa a támogatott helyek listáját.|
|/Subscriptions/operationresults/Read|Az előfizetési művelet eredményeinek lekérése.|
|/Subscriptions/Providers/Read|Beolvassa vagy listázza az erőforrás-szolgáltatókat.|
|/Subscriptions/Read|Beolvassa az előfizetések listáját.|
|/subscriptions/resourceGroups/delete|Törli az erőforráscsoportot és az ahhoz tartozó összes erőforrást.|
|/Subscriptions/resourcegroups/Deployments/Operations/Read|Beolvassa vagy listázza az üzembe helyezési műveleteket.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|Az üzembe helyezési műveletek állapotának beolvasása vagy listázása.|
|/subscriptions/resourcegroups/deployments/read|Beolvassa vagy listázza az üzemelő példányokat.|
|/subscriptions/resourcegroups/deployments/write|Létrehozza vagy frissíti az üzemelő példányt.|
|/subscriptions/resourceGroups/moveResources/action|Erőforrásokat helyez át az egyik erőforráscsoportból egy másikba.|
|/Subscriptions/resourceGroups/Read|Beolvassa vagy listázza az erőforráscsoportokat.|
|/Subscriptions/resourcegroups/Resources/Read|Az erőforráscsoporthoz tartozó erőforrások lekérése.|
|/subscriptions/resourceGroups/validateMoveResources/action|Erőforrások erőforráscsoportok közötti áthelyezésének ellenőrzése.|
|/subscriptions/resourceGroups/write|Létrehozza vagy frissíti az erőforráscsoportot.|
|/Subscriptions/Resources/Read|Beolvassa az előfizetéshez tartozó erőforrásokat.|
|/subscriptions/tagNames/delete|Törli az előfizetéscímkét.|
|/subscriptions/tagNames/read|Beolvassa vagy listázza az előfizetéscímkéket.|
|/subscriptions/tagNames/tagValues/delete|Törli az előfizetéscímke értékét.|
|/subscriptions/tagNames/tagValues/read|Beolvassa vagy listázza az előfizetéscímkék értékeit.|
|/subscriptions/tagNames/tagValues/write|Hozzáadja az előfizetéscímke értékét.|
|/subscriptions/tagNames/write|Előfizetéscímke hozzáadása.|
|/tenants/Read|Beolvassa a bérlők listáját.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Művelet | Leírás |
|---|---|
|/jobcollections/DELETE|A feladatgyűjtemény törlése.|
|/jobcollections/disable/Action|A feladatgyűjtemény letiltása.|
|/jobcollections/enable/Action|A feladatgyűjtemény engedélyezése.|
|/jobcollections/jobs/delete|A feladat törlése.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Logic App-definíció előállítása egy Scheduler-feladat alapján.|
|/jobcollections/Jobs/jobhistories/Read|A feladatelőzmények beolvasása.|
|/jobcollections/Jobs/Read|A feladat beolvasása.|
|/jobcollections/Jobs/Run/Action|A feladat futtatása.|
|/jobcollections/Jobs/Write|Feladatok létrehozása és frissítése|
|/jobcollections/Read|Feladatgyűjtemény beolvasása|
|/ feladatgyűjtemények/írása|Feladatgyűjtemények létrehozása és frissítése|

## <a name="microsoftsearch"></a>Microsoft.Search

| Művelet | Leírás |
|---|---|
|/ checkNameAvailability/művelet|A szolgáltatásnév rendelkezésre állását ellenőrzi.|
|/ regisztrációs/művelet|A keresési erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a keresési szolgáltatások létrehozását.|
|/searchServices/createQueryKey/action|A lekérdezési kulcsot hoz létre.|
|/searchServices/delete|Törli a keresési szolgáltatást.|
|/searchServices/diagnosticSettings/read|Lekérdezi a diganostic beállítást olvassa el az erőforrás|
|/searchServices/diagnosticSettings/write|Létrehozza vagy frissíti az erőforrást a diganostic beállítása|
|/searchServices/listAdminKeys/action|Az adminisztrációs kulcsok beolvasása.|
|/searchServices/logDefinitions/read|A keresési szolgáltatás naplók beolvasása.|
|/searchServices/metricDefinitions/read|A keresési szolgáltatáshoz elérhető metrikai meghatározások beolvasása|
|/searchServices/queryKey/delete|Törli a lekérdezési kulcsot.|
|/searchServices/queryKey/read|A lekérdezési kulcsok beolvasása.|
|/searchServices/Read|Olvassa be a keresési szolgáltatást.|
|/searchServices/regenerateAdminKey/action|Az adminisztrátori kulcs újragenerálása.|
|/searchServices/Start/Action|Elindítja a keresési szolgáltatást.|
|/searchServices/STOP/Action|A keresési szolgáltatás leállítása.|
|/ searchServices/írása|Létrehozza vagy frissíti a keresési szolgáltatást.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Művelet | Leírás |
|---|---|
|/Alerts/Read|Lekérdezi az összes rendelkezésre álló biztonsági riasztások|
|/applicationWhitelistings/Read|Az alkalmazás whitelistings beolvasása|
|/ applicationWhitelistings/írása|Létrehoz egy új alkalmazások engedélyezése vagy frissít egy meglévő|
|/complianceResults/Read|A megfelelőségi eredményeket az erőforrás beolvasása|
|/Locations/Alerts/Activate/Action|Aktiválja a biztonsági riasztások|
|/Locations/Alerts/dismiss/Action|A biztonsági riasztások elvetése|
|/Locations/Alerts/Read|Lekérdezi az összes rendelkezésre álló biztonsági riasztások|
|/locations/jitNetworkAccessPolicies/initiate/action|Kezdeményezi a közvetlenül az időponthoz kötött hálózati hozzáférési házirend|
|/locations/jitNetworkAccessPolicies/read|Lekérdezi a közvetlenül az időponthoz kötött hálózati hozzáférési házirendek|
|/locations/jitNetworkAccessPolicies/write|Létrehoz egy új just-in-time hálózati hozzáférési házirendet, vagy frissít egy meglévő|
|/Locations/Read|Lekérdezi a biztonsági adatok helye|
|/Locations/Tasks/Activate/Action|Biztonsági ajánlás olyan környezetekben aktiválása|
|/Locations/Tasks/dismiss/Action|Biztonsági ajánlás olyan környezetekben elvetése|
|/Locations/Tasks/Read|Lekérdezi az összes rendelkezésre álló biztonsági javaslatok|
|/Locations/Tasks/Resolve/Action|Biztonsági ajánlás olyan környezetekben feloldása|
|/Locations/Tasks/Start/Action|Indítsa el a biztonsági ajánlás olyan környezetekben|
|/Policies/Read|Lekérdezi a biztonsági házirend|
|/ házirendek/írása|A biztonsági házirend frissítése|
|/pricings/DELETE|Törli a hatókör árképzési beállításokat|
|/pricings/Read|A hatókör árképzési beállításainak beolvasása|
|/ díjcsoportokkal/írása|Frissíti a hatókör árképzési beállításait|
|/ regisztrációs/művelet|Az Azure Security Center előfizetésének regisztrálása|
|/securityContacts/delete|A biztonsági ügyfél törlése|
|/securityContacts/Read|A biztonsági ügyfél beolvasása|
|/securityContacts/write|A biztonsági ügyfél frissítése|
|/securitySolutions/delete|Olyan biztonsági megoldás törlése|
|/securitySolutions/read|Lekérdezi a biztonsági megoldások|
|/securitySolutions/write|Létrehoz egy új biztonsági megoldást, vagy frissít egy meglévő|
|/securitySolutionsReferenceData/read|Lekérdezi a biztonsági megoldások referenciaadatok|
|/securityStatuses/Read|Az Azure-erőforrások állapotát állapotok a biztonsági beolvasása|
|/securityStatusesSummaries/read|A biztonsági állapot-összefoglalók lekérdezi a hatókör|
|/Tasks/Read|Lekérdezi az összes rendelkezésre álló biztonsági javaslatok|
|/webApplicationFirewalls/delete|Webalkalmazási tűzfal törlése|
|/webApplicationFirewalls/read|Lekérdezi a webes alkalmazás tűzfalak|
|/webApplicationFirewalls/write|Létrehoz egy új webalkalmazási tűzfal vagy frissít egy meglévő|
|/workspaceSettings/connect/action|Munkaterület beállítások újracsatlakozás beállításainak módosítása|
|/workspaceSettings/delete|A munkaterület beállításainak törlése|
|/workspaceSettings/read|A munkaterület beállításainak beolvasása|
|/workspaceSettings/write|A munkaterület beállításainak frissítése|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Művelet | Leírás |
|---|---|
|/ checkNameAvailability/művelet|A névtér adott előfizetésben való elérhetőségének ellenőrzése.|
|/ checkNamespaceAvailability/művelet|A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja helyette a CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/action|Frissítések Namespace engedélyezési szabályt. Ez az API depricated. A Namespace engedélyezési szabály sorkészletének frissítéséhez használja a PUT hívás... Ez a művelet nem támogatott az API verzió 2017-04-01.|
|/namespaces/authorizationRules/delete|Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető. |
|/namespaces/authorizationRules/listkeys/action|A névtérhez tartozó kapcsolati karakterlánc beolvasása|
|/namespaces/authorizationRules/read|A névtér-engedélyezési szabályok leírásai listájának beolvasása.|
|/namespaces/authorizationRules/regenerateKeys/action|Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása|
|/namespaces/authorizationRules/write|A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni.|
|/ névterek/törlése|Egy névtérerőforrás törlése|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Lekérdezi az engedélyezési szabályok kulcsok a vész-helyreállítási elsődleges névtér|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Katasztrófa utáni helyreállítás elsődleges Namespace meg az engedélyezési szabályok lekérése|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|A névtéralias rendelkezésre állását ellenőrzi megadott előfizetéshez.|
|/namespaces/disasterRecoveryConfigs/delete|A névtér társított vész-helyreállítási konfiguráció törlése. Ezt a műveletet csak az elsődleges névtér keresztül kell meghívni.|
|/namespaces/disasterRecoveryConfigs/failover/action|GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson.|
|/namespaces/disasterRecoveryConfigs/read|A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása.|
|/namespaces/disasterRecoveryConfigs/write|A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése.|
|/namespaces/eventGridFilters/delete|Törli a névtérhez tartozó esemény rács szűrő.|
|/namespaces/eventGridFilters/read|Lekérdezi a névtérhez tartozó esemény rács szűrő.|
|/namespaces/eventGridFilters/write|Létrehozza vagy frissíti a névtérhez tartozó esemény rács szűrő.|
|/namespaces/eventhubs/read|Az EventHub erőforrás leírások listáját|
|/namespaces/messagingPlan/read|Lekérdezi az névtér az üzenetküldési megtervezése. Ez az API elavult. A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek... Ez a művelet nem támogatott az API verzió 2017-04-01.|
|/namespaces/messagingPlan/write|Frissíti az üzenetkezelési tervezése névtér. Ez az API elavult. A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek... Ez a művelet nem támogatott az API verzió 2017-04-01.|
|/Namespaces/Migrate/Action|Névtérmigrálási művelet|
|/Namespaces/operationresults/Read|Névtérművelet állapotának beolvasása|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Listájának Namespace naplók erőforrás leírása|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Namespace metrikák erőforrás leírása listájának beolvasása|
|/namespaces/queues/authorizationRules/action|Várólista frissítési művelete. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése.|
|/namespaces/queues/authorizationRules/delete|Művelet az engedélyezési szabályok várólista törlése|
|/namespaces/queues/authorizationRules/listkeys/action|A kapcsolati karakterlánc várólistára beolvasása|
|/namespaces/queues/authorizationRules/read| A várólista-engedélyezési szabályok listájának lekérdezése|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása|
|/namespaces/queues/authorizationRules/write|Várólista engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni.|
|/Namespaces/Queues/DELETE|A művelet várólista erőforrás törlése|
|/Namespaces/Queues/Read|Várólista erőforrás leírása listájának beolvasása|
|/Namespaces/Queues/Write|Hozzon létre vagy frissítés várólista-tulajdonságok.|
|/Namespaces/Read|A névtérerőforrások leírásai listájának beolvasása|
|/namespaces/topics/authorizationRules/action|A témakör frissítése műveletet. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése.|
|/namespaces/topics/authorizationRules/delete|A művelet törli a témakör az engedélyezési szabályok|
|/namespaces/topics/authorizationRules/listkeys/action|A témakör a kapcsolati karakterlánc beolvasása|
|/namespaces/topics/authorizationRules/read| A témakör az engedélyezési szabályok listájának lekérdezése|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása|
|/namespaces/topics/authorizationRules/write|A témakör az engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni.|
|/Namespaces/topics/DELETE|A művelet témakör erőforrás törlése|
|/Namespaces/topics/Read|A témakör erőforrás leírások listáját|
|/namespaces/topics/subscriptions/Delete|A művelet TopicSubscription erőforrás törlése|
|/Namespaces/topics/Subscriptions/Read|Listájának TopicSubscription erőforrás leírása|
|/namespaces/topics/subscriptions/rules/Delete|Műveletet, szabály erőforrás törlése|
|/namespaces/topics/subscriptions/rules/read|Szabály erőforrás leírása listájának beolvasása|
|/namespaces/topics/subscriptions/rules/write|Hozzon létre vagy frissítés szabály tulajdonságait.|
|/namespaces/topics/subscriptions/write|Hozzon létre vagy frissítés TopicSubscription tulajdonságai.|
|/Namespaces/topics/Write|Hozzon létre vagy frissítés témakör tulajdonságai.|
|/ névterek/írása|Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a kapacitás, a Namespace nem frissíthető tulajdonságait.|
|/Operations/Read|Műveletek beolvasása|
|/ regisztrációs/művelet|Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóra, és lehetővé teszi a ServiceBus-erőforrások létrehozását|
|/sku/read|Termékváltozat erőforrás leírása listájának beolvasása|
|/SKU/Regions/Read|Listájának SkuRegions erőforrás leírása|
|/ unregister/művelet|Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóra, és lehetővé teszi a ServiceBus-erőforrások létrehozását|

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

| Művelet | Leírás |
|---|---|
|/Clusters/Applications/DELETE|Tetszőleges alkalmazás törlése|
|/Clusters/Applications/Read|Tetszőleges alkalmazás beolvasása|
|/clusters/applications/services/delete|Tetszőleges szolgáltatás törlése|
|/Clusters/Applications/Services/Partitions/Read|Tetszőleges partíció beolvasása|
|/Clusters/Applications/Services/Partitions/Replicas/Read|Tetszőleges replika beolvasása|
|/Clusters/Applications/Services/Read|Tetszőleges szolgáltatás beolvasása|
|/Clusters/Applications/Services/statuses/Read|Bármely szolgáltatásállapot beolvasása|
|/Clusters/Applications/Services/Write|Tetszőleges szolgáltatás létrehozása vagy frissítése|
|/Clusters/Applications/Write|Tetszőleges alkalmazás létrehozása vagy frissítése|
|/clusters/applicationTypes/delete|Tetszőleges alkalmazástípus törlése|
|/Clusters/applicationTypes/Read|Tetszőleges alkalmazástípus beolvasása|
|/clusters/applicationTypes/versions/delete|Tetszőleges alkalmazástípus-verzió törlése|
|/clusters/applicationTypes/versions/read|Tetszőleges alkalmazástípus-verzió beolvasása|
|/clusters/applicationTypes/versions/write|Tetszőleges alkalmazástípus-verzió létrehozása vagy frissítése|
|/Clusters/applicationTypes/Write|Tetszőleges alkalmazástípus létrehozása vagy frissítése|
|/Clusters/DELETE|Tetszőleges fürt törlése|
|/Clusters/NODES/Read|Tetszőleges csomópont beolvasása|
|/Clusters/Read|Tetszőleges fürt beolvasása|
|/Clusters/statuses/Read|Bármely fürtállapot beolvasása|
|/ fürtök/írása|Tetszőleges fürt létrehozása vagy frissítése|
|/Locations/clusterVersions/Read|Minden fürtverzió beolvasása|
|/Locations/Environments/clusterVersions/Read|Tetszőleges fürtverzió beolvasása egy adott környezetben|
|/Locations/operationresults/Read|Tetszőleges műveleti eredmények beolvasása|
|/Locations/Operations/Read|Tetszőleges műveletek beolvasása hely szerint|
|/Operations/Read|Tetszőleges használható műveletek beolvasása|
|/ regisztrációs/művelet|Tetszőleges művelet regisztrálása|

## <a name="microsoftsolutions"></a>Microsoft.Solutions

| Művelet | Leírás |
|---|---|
|/applicationDefinitions/delete|Alkalmazásdefiníció eltávolítása.|
|/applicationDefinitions/Read|Az alkalmazásdefiníciók listájának beolvasása.|
|/applicationDefinitions/write|Alkalmazásdefiníció hozzáadása vagy módosítása.|
|/Applications/DELETE|Alkalmazás eltávolítása.|
|/Applications/Read|Az alkalmazások listájának beolvasása.|
|/ applications/írása|Létrehoz egy alkalmazást.|
|/Locations/operationStatuses/Read|Az erőforrás műveleti állapotának beolvasása.|
|/ regisztrációs/művelet|Regisztrálás megoldásokra.|

## <a name="microsoftsql"></a>Microsoft.Sql

| Művelet | Leírás |
|---|---|
|/ checkNameAvailability/művelet|Ellenőrizze, hogy a megadott kiszolgáló neve rendelkezésre álló világszerte az adott előfizetéshez.|
|/locations/auditingSettingsAzureAsyncOperation/read|A kiterjesztett kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása|
|/locations/auditingSettingsOperationResults/read|A kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása|
|/Locations/Capabilities/Read|A képességek lekérdezi az előfizetés egy adott helyen|
|/locations/databaseAzureAsyncOperation/read|Egy adatbázis-művelet állapotát olvassa be.|
|/locations/databaseOperationResults/read|Egy adatbázis-művelet állapotát olvassa be.|
|/locations/deletedServerAsyncOperation/read|Lekérdezi a folyamatban lévő műveletek törölt kiszolgálón|
|/locations/deletedServerOperationResults/read|Lekérdezi a folyamatban lévő műveletek törölt kiszolgálón|
|/locations/deletedServers/read|A törölt kiszolgálók vagy lekérdezi a megadott törölt kiszolgáló tulajdonságok listáját adja vissza.|
|/locations/deletedServers/recover/action|Törölt kiszolgáló helyreállítása|
|/locations/deleteVirtualNetworkOrSubnets/action|Egy virtuális hálózathoz vagy alhálózathoz társított virtuális hálózati szabály törlése|
|/locations/elasticPoolAzureAsyncOperation/read|Az azure aszinkron művelet, a rugalmas készlet aszinkron művelet lekérdezi|
|/locations/elasticPoolOperationResults/read|Egy rugalmas készlet művelet eredményének beolvasása.|
|/locations/extendedAuditingSettingsAzureAsyncOperation/read|A kiterjesztett kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása|
|/locations/extendedAuditingSettingsOperationResults/read|A kiterjesztett kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása|
|/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action|Felügyelt adatbázis-visszaállítási művelet befejezéséről.|
|/locations/managedTransparentDataEncryptionAzureAsyncOperation/read|Lekérdezi a folyamatban lévő műveletek felügyelt adatbázis átlátható adattitkosítás|
|/locations/managedTransparentDataEncryptionOperationResults/read|Lekérdezi a folyamatban lévő műveletek felügyelt adatbázis átlátható adattitkosítás|
|/Locations/Read|Lekérdezi a megadott előfizetéshez tartozó helyeket|
|/locations/syncAgentOperationResults/read|A szinkronizálási ügynök erőforrás művelet eredményének beolvasása|
|/locations/syncDatabaseIds/read|A szinkronizálási adatbázis-azonosító egy adott régióban, és az előfizetés beolvasása|
|/locations/syncGroupOperationResults/read|A szinkronizálás erőforrás-művelet eredményének beolvasása|
|/locations/syncMemberOperationResults/read|A szinkronizálás tag erőforrás művelet eredményének beolvasása|
|/Locations/usages/Read|Ehhez az előfizetéshez, egy helyen lekérdezi a szoftverhasználati mérési adatok gyűjteménye|
|/locations/virtualNetworkRulesAzureAsyncOperation/read|Adatait adja vissza. a megadott virtuális hálózati szabályok azure aszinkron művelet |
|/locations/virtualNetworkRulesOperationResults/read|A megadott virtuális hálózati szabályok művelet adatait adja vissza. |
|/managedInstances/administrators/delete|Törli a meglévő rendszergazda felügyelt példány.|
|/managedInstances/administrators/read|Felügyelt példány rendszergazdák listájának lekérése.|
|/managedInstances/administrators/write|Létrehozza vagy frissíti a felügyelt példány rendszergazda a megadott paraméterekkel.|
|/managedInstances/databases/delete|Törli a meglévő felügyelt adatbázis|
|/managedInstances/databases/read|Lekérdezi a meglévő felügyelt adatbázisba|
|/managedInstances/databases/securityAlertPolicies/read|Az adatbázis fenyegetés szabályzat egy adott felügyelt adatbázisnak konfigurált részleteinek beolvasása|
|/managedInstances/databases/securityAlertPolicies/write|Egy adott felügyelt adatbázisnak adatbázis threat detection szabályzatának módosítása|
|/managedInstances/databases/securityEvents/read|Beolvassa a felügyelt adatbázis biztonsági események|
|/managedInstances/databases/transparentDataEncryption/read|Egy adott felügyelt adatbázison átlátható adattitkosítási adatbázis adatait beolvasása|
|/managedInstances/databases/transparentDataEncryption/write|Az adatbázis az átlátható adattitkosítási egy adott felügyelt adatbázis|
|/managedInstances/databases/write|Létrehoz egy új adatbázist, vagy egy meglévő adatbázist frissíti.|
|/managedInstances/delete|Törli a meglévő felügyelt példány.|
|/managedInstances/metricDefinitions/read|Felügyelt példány metrikai meghatározásainak beolvasása|
|/managedInstances/metrics/read|Felügyelt példány metrikák beolvasása|
|/managedInstances/read|A figyelt példányokat vagy lekérdezi a megadott felügyelt példány tulajdonságainak listáját adja vissza.|
|/managedInstances/securityAlertPolicies/read|A felügyelt kiszolgáló fenyegetés szabályzat egy adott felügyelt kiszolgálón konfigurált részleteinek beolvasása|
|/managedInstances/securityAlertPolicies/write|A felügyelt kiszolgáló fenyegetés szabályzat egy adott felügyelt kiszolgáló módosítása|
|/managedInstances/write|Létrehoz egy felügyelt példányt a megadott paraméterekkel, vagy tulajdonságainak vagy a megadott felügyelt példány címkék frissítése.|
|/Operations/Read|Lekérdezi a használható további műveletek|
|/ regisztrációs/művelet|A Microsoft SQL-adatbázis erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a Microsoft SQL-adatbázisok létrehozását.|
|/servers/administratorOperationResults/read|Lekérdezi a folyamatban lévő műveleteket a kiszolgáló-rendszergazdák|
|/servers/administrators/delete|Törölje a kiszolgáló rendszergazdája|
|/Servers/Administrators/Read|Rendszergazda kiszolgálóadatok beolvasása|
|/servers/administrators/write|Létrehozni vagy frissíteni a kiszolgáló rendszergazdája|
|/servers/advisors/read|A kiszolgáló elérhető tanácsadók listáját adja vissza|
|/servers/advisors/recommendedActions/read|Az a kiszolgáló megadott advisor ajánlott műveletek listáját adja vissza|
|/servers/advisors/recommendedActions/write|A javasolt műveletet alkalmazza a kiszolgálón|
|/servers/advisors/write|Frissítések automatikus-hajtható végre az advisor a kiszolgáló szintjén állapotát.|
|/servers/auditingPolicies/read|Az alapértelmezett kiszolgáló táblázat naplózás a megadott kiszolgálón konfigurált házirend részleteinek beolvasása|
|/servers/auditingPolicies/write|Az alapértelmezett kiszolgáló tábla naplózását egy adott kiszolgáló módosítása|
|/servers/auditingSettings/operationResults/read|A kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása|
|/servers/auditingSettings/read|A kiszolgáló blob naplózási házirend adatait a megadott kiszolgálón konfigurált beolvasása|
|/servers/auditingSettings/write|A kiszolgáló blob naplózását egy adott kiszolgáló módosítása|
|/servers/automaticTuning/read|A kiszolgáló automatikus hangolási beállításainak adja vissza|
|/servers/automaticTuning/write|A kiszolgáló automatikus hangolási beállításainak frissíti, és adja vissza a frissített beállításokkal|
|/servers/backupLongTermRetentionVaults/delete|Egy meglévő biztonsági mentési, archiválási tárolóban törli.|
|/servers/backupLongTermRetentionVaults/read|Ez a művelet egy biztonsági mentési hosszú távú megőrzési tárolóban eléréséhez használatos. A tárolóban regisztrált erre a kiszolgálóra vonatkozó információkat ad vissza|
|/servers/backupLongTermRetentionVaults/write|Ez a művelet egy biztonsági mentési hosszú távú megőrzési regisztrálásához használt kiszolgálóra tároló|
|/servers/communicationLinks/delete|Törli a meglévő kiszolgálói kommunikáció hivatkozás.|
|/servers/communicationLinks/read|A megadott kiszolgáló a kommunikációs kapcsolatok listáját adja vissza.|
|/servers/communicationLinks/write|Létrehozása vagy frissítése server kommunikációs kapcsolatot.|
|/servers/connectionPolicies/read|Térjen vissza a megadott kiszolgáló kiszolgálói kapcsolat házirendek listájában.|
|/servers/connectionPolicies/write|Létrehozása vagy módosítása egy kiszolgáló kapcsolatkezelési házirendet.|
|/servers/databases/advisors/read|Az adatbázis elérhető tanácsadók listáját adja vissza|
|/servers/databases/advisors/recommendedActions/read|A megadott advisort az adatbázishoz ajánlott műveletek listáját adja vissza|
|/servers/databases/advisors/recommendedActions/write|A javasolt műveletet alkalmazza az adatbázishoz|
|/servers/databases/advisors/write|Frissítés Automatikus végrehajtás az advisor adatbázis szinten állapotának.|
|/servers/databases/auditingPolicies/read|A naplózási házirend tábla, egy adott adatbázisnak konfigurált adatait beolvasása|
|/servers/databases/auditingPolicies/write|A tábla egy adott adatbázisnak a naplózási házirend módosítása|
|/servers/databases/auditingSettings/read|Egy adott adatbázisnak konfigurált blob naplózási házirend részleteinek beolvasása|
|/servers/databases/auditingSettings/write|A blob egy adott adatbázisnak a naplózási házirend módosítása|
|/servers/databases/auditRecords/read|A blob naplózási rekordok beolvasása|
|/servers/databases/automaticTuning/read|Automatikus hangolása egy adatbázis-beállításainak beolvasása|
|/servers/databases/automaticTuning/write|Egy adatbázis automatikus hangolási beállításainak frissíti, és adja vissza a frissített beállításokkal|
|/servers/databases/azureAsyncOperation/read|Egy adatbázis-művelet állapotát olvassa be.|
|/servers/databases/backupLongTermRetentionPolicies/read|Térjen vissza a megadott adatbázis biztonsági mentési archiválási házirendek listájában.|
|/servers/databases/backupLongTermRetentionPolicies/write|Hozzon létre, vagy frissítse az adatbázis biztonsági mentési, archiválási házirend.|
|/servers/databases/connectionPolicies/read|A kapcsolat házirend egy adott adatbázisnak konfigurált részleteinek beolvasása|
|/servers/databases/connectionPolicies/write|Módosítsa a kapcsolatkezelési házirendet az egy adott adatbázisnak|
|/servers/databases/dataMaskingPolicies/read|Az adatbázis adatmaszkolási házirendek listáját adja vissza.|
|/servers/databases/dataMaskingPolicies/rules/delete|Egy adott adatbázis házirendszabály adatmaszkolási törlése|
|/servers/databases/dataMaskingPolicies/rules/read|A adatmaszkolási egy adott adatbázisnak konfigurált házirendszabály részleteit beolvasása|
|/servers/databases/dataMaskingPolicies/rules/write|Adatok házirend szabály egy adott adatbázisnak a maszkolás módosítása|
|/servers/databases/dataMaskingPolicies/write|Adatmaszkolási egy adott adatbázis-házirend módosítása|
|/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read|Információkat ad vissza az elosztott lekérdezés lépés adatraktár lekérdezés kijelölt lépés azonosítója|
|/servers/databases/dataWarehouseQueries/read|Az adatraktár terjesztési lekérdezés információi a kijelölt lekérdezés Azonosítóját adja vissza|
|/servers/databases/dataWarehouseUserActivities/read|Lekéri a felhasználói tevékenység fut, és a felfüggesztett lekérdezések köztük az SQL Data Warehouse-példány|
|/servers/databases/delete|Törli a meglévő adatbázist.|
|/servers/databases/export/action|Az Azure SQL adatbázis exportálása|
|/servers/databases/extendedAuditingSettings/read|Egy adott adatbázisnak konfigurált bővített blob naplózási házirend részleteinek beolvasása|
|/servers/databases/extendedAuditingSettings/write|A bővített blob egy adott adatbázisnak a naplózási házirend módosítása|
|/Servers/Databases/Extensions/Read|Az adatbázis lekérdezi a bővítmények gyűjteménye.|
|/servers/databases/extensions/write|A megadott adatbázis bővítmény módosítása|
|/servers/databases/geoBackupPolicies/read|Georedundáns biztonsági mentési házirendek egy adott adatbázis beolvasása|
|/servers/databases/geoBackupPolicies/write|Hozzon létre vagy egy adatbázis geobackup házirend frissítése|
|/servers/databases/importExportOperationResults/read|Lekérdezi a folyamatban lévő importálási/exportálási műveleteket|
|/servers/databases/metricDefinitions/read|Térjen vissza a típusú ítélt adatbázisokhoz|
|/servers/databases/metrics/read|Térjen vissza az adatbázisok metrikák|
|/servers/databases/move/action|Az Azure SQL-adatbázis átnevezése|
|/servers/databases/operationResults/read|Egy adatbázis-művelet állapotát olvassa be.|
|/servers/databases/operations/cancel/action|Az Azure SQL Database megszakítja a függőben lévő aszinkron művelet, amely még nem fejeződött be.|
|/Servers/Databases/Operations/Read|Térjen vissza az adatbázison végrehajtott műveletek listája|
|/servers/databases/pause/action|Az Azure SQL-adatraktár adatbázisa felfüggesztése|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/servers/databases/providers/Microsoft.Insights/logDefinitions/read|Az adatbázisok naplók beolvasása.|
|/servers/databases/providers/Microsoft.Insights/metricDefinitions/read|Térjen vissza a típusú ítélt adatbázisokhoz|
|/servers/databases/queryStore/queryTexts/read|A gyűjtemény lekérdezési szövegek, amelyek megfelelnek a megadott paraméterek adja vissza.|
|/Servers/Databases/queryStore/Read|A Lekérdezéstár beállítása az adatbázis aktuális értékek beolvasása.|
|/servers/databases/queryStore/write|A Lekérdezéstár-beállítás az adatbázis frissítése|
|/Servers/Databases/Read|Adatbázisok vagy lekérdezi a megadott adatbázis tulajdonságainak megadása listáját adja vissza.|
|/servers/databases/replicationLinks/delete|Állítsa le a replikációs kapcsolat kényszerített módon, és az esetleges adatvesztés|
|/servers/databases/replicationLinks/failover/action|Feladatátvételi minden szinkronizálás után az elsődleges megváltozik, ezt az adatbázist és a replikációs relationship\u0027s az elsődleges és a távoli elsődleges és másodlagos be|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Feladatátvétel azonnal esetleges adatvesztés, ezt az adatbázist és a replikációs relationship\u0027s az elsődleges és a távoli elsődleges és másodlagos be|
|/servers/databases/replicationLinks/read|Egy adott adatbázist létrehozni a replikációs hivatkozások visszatérési részletei|
|/servers/databases/replicationLinks/unlink/action|Állítsa le a replikációs kapcsolat kényszerített módon vagy a partnerrel való szinkronizálás után|
|/servers/databases/replicationLinks/updateReplicationMode/action|A frissítési replikációs mód csatolás szinkron vagy aszinkron módban|
|/servers/databases/restorePoints/action|Új visszaállítási pont létrehozása|
|/servers/databases/restorePoints/read|Vissza visszaállítási pontok az adatbázishoz.|
|/servers/databases/resume/action|Az Azure SQL-adatraktár adatbázisa folytatása|
|/servers/databases/schemas/read|Adatbázis sémák listájának beolvasása|
|/servers/databases/schemas/tables/columns/read|Egy tábla oszlopainak listájának beolvasása|
|/servers/databases/schemas/tables/columns/sensitivityLabels/delete|A megadott oszlop tartalomcímke törlése|
|/Servers/Databases/schemas/Tables/Columns/sensitivityLabels/Read|A megadott oszlop tartalomcímke beolvasása|
|/servers/databases/schemas/tables/columns/sensitivityLabels/write|Létrehozni vagy frissíteni az adott oszlop tartalomcímke|
|/servers/databases/schemas/tables/read|Egy adatbázis táblák listáját beolvasása|
|/servers/databases/schemas/tables/recommendedIndexes/read|Egy adatbázishoz ajánlott index listájának beolvasása|
|/servers/databases/schemas/tables/recommendedIndexes/write|Alkalmazza az ajánlott index|
|/servers/databases/securityAlertPolicies/read|A fenyegetés szabályzat egy adott adatbázisnak konfigurált részleteinek beolvasása|
|/servers/databases/securityAlertPolicies/write|Egy adott adatbázisnak threat detection szabályzatának módosítása|
|/servers/databases/securityMetrics/read|Lekérdezi az adatbázis biztonsági metrikák gyűjteménye|
|/servers/databases/sensitivityLabels/read|Egy adott adatbázisnak érzékenységi címkéket listája|
|/servers/databases/serviceTierAdvisors/read|Térjen javaslat adatbázis méretezésével felfelé vagy lefelé kapcsolatos teljesítményének javítása és a költségek csökkentése a lekérdezés végrehajtási statisztika alapján|
|/servers/databases/syncGroups/cancelSync/action|Szinkronizálási csoport szinkronizálás megszakítása|
|/servers/databases/syncGroups/delete|Törli a meglévő szinkronizálási csoport.|
|/servers/databases/syncGroups/hubSchemas/read|Térjen vissza a szinkronizálási listájának hub adatbázis sémák|
|/servers/databases/syncGroups/logs/read|Térjen vissza a szinkronizálási csoport naplók listája|
|/servers/databases/syncGroups/read|Térjen vissza a szinkronizálási csoportok, vagy a megadott szinkronizálási csoport tulajdonságainak beolvasása.|
|/servers/databases/syncGroups/refreshHubSchema/action|Szinkronizáló központ adatbázis séma frissítése|
|/servers/databases/syncGroups/refreshHubSchemaOperationResults/read|A Szinkronizáló központ séma frissítési művelet eredményének beolvasása|
|/servers/databases/syncGroups/syncMembers/delete|Törli a meglévő szinkronizálási tagjára.|
|/Servers/Databases/syncGroups/syncMembers/Read|A megadott szinkronizálási tag térjen vissza a szinkronizálási tagok vagy lekérdezi a tulajdonságok listáját.|
|/servers/databases/syncGroups/syncMembers/refreshSchema/action|Szinkronizálási tag séma frissítése|
|/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read|A szinkronizálás tag séma frissítési művelet eredményének beolvasása|
|/Servers/Databases/syncGroups/syncMembers/schemas/Read|Térjen vissza a szinkronizálási listájának tag adatbázis sémák|
|/servers/databases/syncGroups/syncMembers/write|Hoz létre a szinkronizálási tagja a megadott paramétereket, vagy a megadott szinkronizálási tag tulajdonságainak frissítéséhez.|
|/servers/databases/syncGroups/triggerSync/action|Eseményindító szinkronizálási csoport szinkronizálási|
|/servers/databases/syncGroups/write|Szinkronizálási csoportot hoz létre a megadott paraméterekkel, vagy a megadott szinkronizálási csoport tulajdonságainak frissítéséhez.|
|/servers/databases/topQueries/queryText/action|A kijelölt lekérdezés Azonosítóját a Transact-SQL szöveget adja vissza|
|/servers/databases/topQueries/read|Értéket ad vissza kijelölt időszakra összesített értéket a kijelölt lekérdezésre vonatkozó futásidejű statisztikák|
|/servers/databases/topQueries/statistics/read|Értéket ad vissza kijelölt időszakra összesített értéket a kijelölt lekérdezésre vonatkozó futásidejű statisztikák|
|/servers/databases/transparentDataEncryption/operationResults/read|Lekérdezi a folyamatban lévő műveletek átlátható adattitkosítás|
|/servers/databases/transparentDataEncryption/read|Állapot és átlátható titkosítási biztonsági szolgáltatást egy adott adatbázisnak részleteinek beolvasása|
|/servers/databases/transparentDataEncryption/write|Átlátható titkosítási állapot módosítása|
|/servers/databases/upgradeDataWarehouse/action|Frissítés Azure SQL-adatraktár adatbázisa|
|/servers/databases/usages/read|Az Azure SQL Database módjait információ lekérése|
|/servers/databases/vulnerabilityAssessments/delete|Távolítsa el a megadott adatbázis biztonsági réseinek értékelése|
|/servers/databases/vulnerabilityAssessments/read|A biztonsági réseinek értékelése, egy adott adatbázisnak konfigurált részleteinek beolvasása|
|/servers/databases/vulnerabilityAssessments/rules/baselines/delete|Távolítsa el a biztonsági rés értékelése szabály alaptervet egy adott adatbázisnak|
|/servers/databases/vulnerabilityAssessments/rules/baselines/read|Egy adott adatbázisnak a biztonsági rés értékelése szabály alapterv lekérése|
|/servers/databases/vulnerabilityAssessments/rules/baselines/write|Egy adott adatbázisnak a biztonsági rés értékelése szabály alaptervet módosítása|
|/servers/databases/vulnerabilityAssessments/scans/action|A biztonsági rés adatbázis vizsgálat hajtható végre.|
|/servers/databases/vulnerabilityAssessments/scans/export/action|Egy meglévő vizsgálati eredmény átalakítása emberi olvasható formátumba. Ha már létezik semmi nem történik,|
|/servers/databases/vulnerabilityAssessments/scans/read|Az adatbázis biztonsági rés listája assessment vizsgálat rekordot ad vissza, vagy a vizsgálat rekord lekérése a megadott vizsgálat azonosítóját.|
|/servers/databases/vulnerabilityAssessments/write|A biztonsági réseinek értékelése egy adott adatbázis módosítása|
|/servers/databases/vulnerabilityAssessmentScans/action|A biztonsági rés adatbázis vizsgálat hajtható végre.|
|/servers/databases/vulnerabilityAssessmentScans/operationResults/read|Az adatbázis biztonsági rés vizsgálatának végrehajtásához hajtsa végre a művelet eredményének beolvasása|
|/servers/databases/vulnerabilityAssessmentSettings/read|A biztonsági réseinek értékelése, egy adott adatbázisnak konfigurált részleteinek beolvasása|
|/servers/databases/vulnerabilityAssessmentSettings/write|A biztonsági réseinek értékelése egy adott adatbázis módosítása|
|/servers/databases/write|Létrehoz egy adatbázist a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott adatbázis címkék frissítése.|
|/servers/delete|Egy meglévő kiszolgáló törlése.|
|/servers/disasterRecoveryConfiguration/delete|Egy meglévő vész-helyreállítási beállítások egy adott kiszolgáló törlése|
|/servers/disasterRecoveryConfiguration/failover/action|Feladatátvétel egy DisasterRecoveryConfiguration|
|/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action|Feladatátvétel egy DisasterRecoveryConfiguration kényszerítése|
|/servers/disasterRecoveryConfiguration/read|Lekérdezi a katasztrófa gyűjteménye, amely tartalmazza az ehhez a kiszolgálóhoz helyreállítási konfigurációk|
|/servers/disasterRecoveryConfiguration/write|Kiszolgáló vész helyreállítási konfigurációjának módosítása|
|/servers/elasticPoolEstimates/read|Ehhez a kiszolgálóhoz már létrehozott rugalmas készlet becslések listáját adja vissza|
|/servers/elasticPoolEstimates/write|Hoz létre új rugalmas készletbecslés megadott adatbázisok listája|
|/servers/elasticPools/advisors/read|A rugalmas készlet elérhető tanácsadók listáját adja vissza|
|/servers/elasticPools/advisors/recommendedActions/read|Javasolt művelet a megadott advisor a rugalmas készlet listáját adja vissza|
|/servers/elasticPools/advisors/recommendedActions/write|A rugalmas készlet alkalmazni a javasolt művelet|
|/servers/elasticPools/advisors/write|Frissítés Automatikus végrehajtás az advisor szinten a rugalmas készlet állapotát.|
|/servers/elasticPools/databases/read|Rugalmas készletek adatbázisok listájának lekérése|
|/servers/elasticPools/delete|A meglévő rugalmas készlet törlése|
|/Servers/elasticPools/elasticPoolActivity/Read|Tevékenységek és egy adott rugalmas adatbáziskészlet részleteinek beolvasása|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Tevékenységek és egy adott adatbázisnak, amely része a rugalmas adatbáziskészlet részleteinek beolvasása|
|/servers/elasticPools/metricDefinitions/read|Térjen vissza a rugalmas adatbáziskészletek elérhető metrikák típusú|
|/servers/elasticPools/metrics/read|Térjen vissza a rugalmas adatbáziskészletek metrikák|
|/servers/elasticPools/operations/cancel/action|Visszavonja az Azure SQL rugalmas készlet függőben lévő aszinkron művelet, amely még nem fejeződött be.|
|/servers/elasticPools/operations/read|A művelet végrehajtását, a rugalmas készlet listáját adja vissza|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read|Térjen vissza a rugalmas adatbáziskészletek elérhető metrikák típusú|
|/servers/elasticPools/read|A rugalmas készlet a megadott kiszolgálón részleteinek beolvasása|
|/servers/elasticPools/skus/read|Elérhető termékváltozatok gyűjteménye lekérése. Ez a rugalmas készlet|
|/servers/elasticPools/write|Hozzon létre egy új vagy meglévő rugalmas készlet tulajdonságainak módosítása|
|/servers/encryptionProtector/read|Kiszolgáló titkosítási protectors listáját adja vissza, vagy tulajdonságait olvassa be a megadott kiszolgálóhoz tartozó titkosítási védőt.|
|/servers/encryptionProtector/write|Az a megadott kiszolgáló titkosítási védő tulajdonságainak frissítéséhez.|
|/servers/extendedAuditingSettings/read|A kiterjesztett kiszolgáló blob a megadott kiszolgálón konfigurált naplórendet részleteinek beolvasása|
|/servers/extendedAuditingSettings/write|A kiterjesztett kiszolgáló blob naplózását egy adott kiszolgáló módosítása|
|/servers/failoverGroups/delete|Egy meglévő feladatátvevő csoport törlése.|
|/Servers/failoverGroups/Failover/Action|Tervezett feladatátvétel végrehajtása egy meglévő feladatátvevő csoportban.|
|/servers/failoverGroups/forceFailoverAllowDataLoss/action|Végrehajtja a kényszerített feladatátvételi egy meglévő feladatátvevő csoportban.|
|/Servers/failoverGroups/Read|A lista a feladatátvétel csoportokat vagy a megadott feladatátvételi csoport tulajdonságainak beolvasása adja vissza.|
|/Servers/failoverGroups/Write|Feladatátvételi csoportot hoz létre a megadott paraméterekkel, vagy frissíti a Tulajdonságok vagy a megadott feladatátvételi csoport címkék.|
|/servers/firewallRules/delete|Meglévő kiszolgáló tűzfalszabály törlése.|
|/servers/firewallRules/read|A kiszolgáló tűzfal listáját szabályok vagy tulajdonságait olvassa be a megadott kiszolgálóhoz tartozó tűzfalszabály vissza.|
|/servers/firewallRules/write|Létrehoz egy tűzfalszabály létrehozása a megadott paraméterekkel, a megadott szabály tulajdonságainak frissítéséhez, vagy minden meglévő szabályok felülírása az új kiszolgáló tűzfal-szabályok.|
|/servers/import/action|Hozzon létre egy új adatbázist a kiszolgálón, és a séma és adatainak áttelepítését egy DacPac csomag telepítése|
|/servers/importExportOperationResults/read|Lekérdezi a folyamatban lévő importálási/exportálási műveleteket|
|/servers/keys/delete|Törli a meglévő kiszolgáló-kulcsot.|
|/Servers/keys/Read|Térjen vissza a kiszolgáló a kulcsok vagy a megadott kiszolgáló kulcs tulajdonságainak beolvasása.|
|/Servers/keys/Write|Létrehoz egy kulcsot a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott kiszolgálókulcs címkék frissítése.|
|/servers/operationResults/read|Lekérdezi a folyamatban lévő kiszolgáló műveletei|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Térjen vissza a kiszolgálók esetén elérhetők metrikák típusai|
|/Servers/Read|A kiszolgálók vagy lekérdezi a megadott kiszolgálóhoz tartozó tulajdonságok listáját adja vissza.|
|/servers/recommendedElasticPools/databases/read|Egy adott kiszolgálóhoz ajánlott rugalmas adatbáziskészletek metrikák beolvasása|
|/servers/recommendedElasticPools/read|Ajánlott rugalmas adatbáziskészlet költségeinek csökkentése vagy historica erőforrás-használat alapján a teljesítmény javítása beolvasása|
|/servers/recoverableDatabases/read|Ez a művelet segítségével élő adatbázis vész-helyreállítási utolsó ismert helyes biztonsági mentési pontok-adatbázis helyreállításához. Olyan információkat ad vissza a legutóbbi helyes biztonsági másolatot, de az kapcsolatos doesn\u0027t ténylegesen állítsa vissza az adatbázist.|
|/servers/restorableDroppedDatabases/read|Az adatbázisok listája, amelyek el lettek dobva a megadott kiszolgálón még mindig belüli adatmegőrzési beolvasása.|
|/servers/securityAlertPolicies/operationResults/read|A kiszolgáló fenyegetések észlelése házirend írási művelet eredményének beolvasása|
|/servers/securityAlertPolicies/read|A megadott kiszolgálón konfigurált kiszolgáló fenyegetés észlelési házirend részleteinek beolvasása|
|/servers/securityAlertPolicies/write|A kiszolgáló fenyegetés szabályzat egy adott kiszolgáló módosítása|
|/Servers/serviceObjectives/Read|A szolgáltatási szint célkitűzései (más néven teljesítmény rétegek) a megadott kiszolgálón elérhető listájának beolvasása|
|/servers/syncAgents/delete|Törli a meglévő szinkronizálási ügynök.|
|/servers/syncAgents/generateKey/action|Szinkronizálási ügynök regisztrálás kulcs létrehozása|
|/servers/syncAgents/linkedDatabases/read|A listájának kapcsolódó ügynök adatbázisok szinkronizálása|
|/Servers/syncAgents/Read|A megadott szinkronizálási ügynök térjen vissza a szinkronizálási ügynökök vagy lekérdezi a tulajdonságok listáját.|
|/servers/syncAgents/write|A megadott paraméterek hoz létre a sync-ügynök vagy a megadott szinkronizálási ügynök tulajdonságainak frissítéséhez.|
|/servers/usages/read|Térjen vissza a kiszolgáló DTU-kvótáról és aktuális DTU consuption minden adatbázis-kiszolgálón belül|
|/servers/virtualNetworkRules/delete|Egy meglévő virtuális hálózat szabály törlése|
|/servers/virtualNetworkRules/read|Térjen vissza a virtuális hálózat szabályok vagy a megadott virtuális hálózati szabály tulajdonságainak beolvasása.|
|/servers/virtualNetworkRules/write|A virtuális hálózati szabályt hoz létre a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott virtuális hálózati szabály címkék frissítése.|
|/ kiszolgálók/írása|Kiszolgáló létrehozása a megadott paraméterekkel, vagy frissítse a Tulajdonságok vagy a címkék a megadott kiszolgáló.|
|/ unregister/művelet|Az előfizetés a Microsoft SQL-adatbázis erőforrás-szolgáltató regisztrációjának törlése, és lehetővé teszi a Microsoft SQL-adatbázisok létrehozását.|
|/virtualClusters/Read|A virtuális fürtök vagy lekérdezi a megadott virtuális fürt tulajdonságok listáját adja vissza.|
|/ virtualClusters/írása|Frissíti a virtuális fürt címkék.|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Művelet | Leírás |
|---|---|
|/checknameavailability/Read|Ellenőrzi, hogy érvényes-e a fióknév, és nincs-e használatban.|
|/locations/deleteVirtualNetworkOrSubnets/action|A Microsoft.Storage értesítése arról, hogy virtuális hálózat vagy alhálózat törlése van folyamatban|
|/Operations/Read|Az aszinkrón műveletek állapotának lekérdezése.|
|/ regisztrációs/művelet|A tárolásierőforrás-szolgáltató előfizetésének regisztrálása és a tárfiókkészítés engedélyezése.|
|/skus/Read|A Microsoft.Storage által támogatott termékváltozatok listázása.|
|/storageAccounts/blobServices/containers/clearLegalHold/action|Blobtároló visszatartásának törlése|
|/storageAccounts/blobServices/containers/delete|A tároló törlésének eredményét adja vissza|
|/storageAccounts/blobServices/containers/immutabilityPolicies/delete|Blobtároló módosíthatatlansági szabályzatának törlése|
|/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action|Blobtároló bővítésére vonatkozó módosíthatatlansági szabályzat|
|/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action|Blobtároló zárolására vonatkozó módosíthatatlansági szabályzat|
|/storageAccounts/blobServices/containers/immutabilityPolicies/read|Blobtároló beolvasására vonatkozó módosíthatatlansági szabályzat|
|/storageAccounts/blobServices/containers/immutabilityPolicies/write|Blobtárolón végrehajtott put műveletre vonatkozó módosíthatatlansági szabályzat|
|/storageAccounts/blobServices/containers/read|Egy tárolót vagy tárolók listáját adja vissza|
|/storageAccounts/blobServices/containers/setLegalHold/action|Blobtároló visszatartásának beállítása|
|/storageAccounts/blobServices/containers/write|A put vagy lease blobtároló-művelet eredményének visszaadása|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításának beolvasása.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read|A Microsoft társzolgáltatás metrikadefinícióit tartalmazó lista beolvasása.|
|/storageAccounts/blobServices/read|A Blob szolgáltatás tulajdonságait vagy a statisztikát adja vissza|
|/storageAccounts/blobServices/write|A blobszolgáltatás tulajdonságain végrehajtott Put művelet eredményének visszaadása|
|/storageAccounts/delete|Meglévő tárfiók törlése.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításának beolvasása.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read|A Microsoft társzolgáltatás metrikadefinícióit tartalmazó lista beolvasása.|
|/storageAccounts/listAccountSas/action|A fiók SAS-jogkivonatát adja vissza a megadott tárfiókhoz.|
|/storageAccounts/listkeys/action|A megadott tárfiók hozzáférési kulcsainak lekérése.|
|/storageAccounts/listServiceSas/action|A szolgáltatás SAS-jogkivonatát adja vissza a meghatározott tárfiókhoz.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításának beolvasása.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése.|
|/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read|A Microsoft társzolgáltatás metrikadefinícióit tartalmazó lista beolvasása.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read|A Microsoft társzolgáltatás metrikadefinícióit tartalmazó lista beolvasása.|
|/storageAccounts/queueServices/queues/delete|Sor törlésének eredményét adja vissza|
|/storageAccounts/queueServices/queues/read|A sort vagy a sorok listáját adja vissza.|
|/storageAccounts/queueServices/queues/write|A sor írásának eredményét adja vissza|
|/storageAccounts/queueServices/read|A Queue szolgáltatás tulajdonságait vagy a statisztikát adja vissza.|
|/storageAccounts/queueServices/write|A Queue szolgáltatás tulajdonságai beállításának eredményét adja vissza|
|/storageAccounts/Read|A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése.|
|/storageAccounts/regeneratekey/action|A megadott tárfiók hozzáférési kulcsainak újragenerálása.|
|/storageAccounts/services/diagnosticSettings/write|Tárfiók diagnosztikai beállításainak létrehozása vagy frissítése.|
|/storageAccounts/storageAccounts/queueServices/providers/ Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításának beolvasása.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításának beolvasása.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read|A Microsoft társzolgáltatás metrikadefinícióit tartalmazó lista beolvasása.|
|/storageAccounts/write|Tárfiók létrehozása a megadott paraméterekkel, a tulajdonságok vagy címkék frissítése, vagy egyéni tartomány felvétele a megadott tárfiókhoz.|
|/usages/Read|A megadott előfizetésben szereplő erőforrások korlátját és jelenlegi kihasználtsági értéket adja vissza|

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

| Művelet | Leírás |
|---|---|
|/storageSyncServices/delete|A tárolási szinkronizálási szolgáltatások törlése|
|/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read|A tárolási szinkronizálási szolgáltatások elérhető metrikai meghatározások beolvasása|
|/storageSyncServices/read|Olvassa el a tárolási szinkronizálási szolgáltatások|
|/storageSyncServices/registeredServers/delete|Bármely regisztrált kiszolgáló törlése|
|/storageSyncServices/registeredServers/read|Bármely regisztrált kiszolgáló olvasása|
|/storageSyncServices/registeredServers/write|Létrehozni vagy frissíteni a regisztrált kiszolgáló|
|/storageSyncServices/syncGroups/cloudEndpoints/delete|Felhő végpontok törlése|
|/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read|Hely api aszinkron biztonsági mentési hívások|
|/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action|Biztonsági mentés után a művelet hívása|
|/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action|Ez a művelet visszaállítás után hívható|
|/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action|Biztonsági mentés előtt. Ez a művelet hívása|
|/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action|Ez a művelet a visszaállítási előtt hívható|
|/storageSyncServices/syncGroups/cloudEndpoints/read|Olvassa el a felhő végpontok|
|/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action|Szívverés visszaállítása|
|/storageSyncServices/syncGroups/cloudEndpoints/write|Létrehozni vagy frissíteni a felhő végpontok|
|/storageSyncServices/syncGroups/delete|A szinkronizálási csoportok törlése|
|/storageSyncServices/syncGroups/read|Olvassa el a szinkronizálási csoportok|
|/storageSyncServices/syncGroups/serverEndpoints/delete|Kiszolgáló végpontok törlése|
|/storageSyncServices/syncGroups/serverEndpoints/read|Olvassa el a kiszolgáló végpontok|
|/storageSyncServices/syncGroups/serverEndpoints/recallAction/action|Ez a művelet egy visszahívás hívása|
|/storageSyncServices/syncGroups/serverEndpoints/write|Létrehozni vagy frissíteni Server végpontok|
|/storageSyncServices/syncGroups/write|A szinkronizálási csoportok létrehozása vagy frissítése|
|/storageSyncServices/write|A tárolási szinkronizálási szolgáltatások létrehozása vagy módosítása|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Művelet | Leírás |
|---|---|
|/managers/accessControlRecords/delete|A hozzáférés-vezérlési rekordokat törlése|
|/managers/accessControlRecords/read|Sorolja fel, vagy a hozzáférés-vezérlési rekordokat beolvasása|
|/managers/accessControlRecords/write|A hozzáférés-vezérlő rekordok létrehozása vagy frissítése|
|/managers/Alerts/Read|Lekérdezi a riasztásokat vagy listája|
|/managers/bandwidthSettings/delete|Törli a meglévő sávszélesség-beállítások (8000 sorozat csak)|
|/managers/bandwidthSettings/read|A sávszélesség-beállítások (csak 8000 sorozat)|
|/managers/bandwidthSettings/write|Létrehoz egy új, vagy a sávszélesség-beállítások frissítése (8000 sorozat csak)|
|Kezelői/tanúsítvány/írása|A frissítés erőforrás tanúsítvány művelet frissíti az erőforrás/tárolói hitelesítő adatainak tanúsítványa.|
|/managers/clearAlerts/Action|Törölje a jelet az Eszközkezelőben társított összes riasztást.|
|/managers/cloudApplianceConfigurations/read|A felhő készülék lista támogatott konfigurációk|
|/managers/configureDevice/action|Eszközök konfigurálása|
|/managers/DELETE|Törli az eszköz-kezelők|
|/Managers/delete|A tároló törlése a művelet törli a megadott Azure-erőforrás "tárolóban" típusú|
|/managers/Devices/alertSettings/Read|Sorolja fel, vagy a riasztási beállításainak beolvasása|
|/managers/devices/alertSettings/write|A riasztási beállításainak létrehozása vagy frissítése|
|/managers/Devices/backupPolicies/Backup/Action|Igény szerinti létrehozásához manuális biztonsági mentés készítése a házirend által védett összes kötet biztonsági mentését.|
|/managers/devices/backupPolicies/delete|Törli a meglévő biztonsági mentési házirendek (8000 sorozat csak)|
|/managers/devices/backupPolicies/read|A lista a biztonsági mentési házirendek (csak 8000 sorozat)|
|/managers/devices/backupPolicies/schedules/delete|Egy meglévő ütemezés törlése|
|/managers/devices/backupPolicies/schedules/read|Az ütemezések felsorolása|
|/managers/Devices/backupPolicies/schedules/Write|Létrehoz egy új, vagy az ütemezések frissítése|
|/managers/devices/backupPolicies/write|Létrehoz egy új, vagy frissíti a biztonsági mentési házirendek (8000 sorozat csak)|
|/managers/devices/backups/delete|A biztonságimásolat-készlet törlése|
|/managers/Devices/backups/Elements/Clone/Action|Klónozni egy fájlmegosztás vagy kötet egy biztonsági mentési elem használatával.|
|/managers/Devices/backups/Read|Lekérdezi a biztonságimásolat-készletet vagy listája|
|/managers/Devices/backups/restore/Action|Állítsa vissza az összes kötet a biztonságimásolat-készletből.|
|/managers/devices/backupScheduleGroups/delete|A biztonsági mentés ütemezése csoportok törlése|
|/managers/devices/backupScheduleGroups/read|Sorolja fel, vagy a biztonsági mentési ütemezés csoportok beolvasása|
|/managers/devices/backupScheduleGroups/write|A biztonsági mentési ütemezés csoportok létrehozása vagy frissítése|
|/managers/devices/chapSettings/delete|Törli a Chap-beállításokat|
|/managers/devices/chapSettings/read|Sorolja fel, vagy a Chap beállításainak beolvasása|
|/managers/devices/chapSettings/write|A Chap beállításainak létrehozása vagy frissítése|
|/managers/Devices/Deactivate/Action|Egy eszköz inaktiválja.|
|/managers/Devices/DELETE|Az eszközök törlése|
|/managers/Devices/download/Action|Egy eszköz letöltési frissítéseket.|
|/managers/Devices/Failover/Action|Az eszköz feladatátvétele.|
|/managers/devices/fileservers/backup/action|Egy fájlkiszolgáló biztonsági másolatok készítéséhez.|
|/managers/devices/fileservers/delete|Törli a fájlkiszolgálókon|
|/managers/Devices/fileservers/Metrics/Read|Sorolja fel, vagy a metrikák beolvasása|
|/managers/Devices/fileservers/metricsDefinitions/Read|Sorolja fel, vagy a metrikák meghatározások beolvasása|
|/managers/Devices/fileservers/Read|Lekérdezi a fájlkiszolgálók vagy listája|
|/managers/devices/fileservers/shares/delete|Törli a megosztások|
|/managers/devices/fileservers/shares/metrics/read|Sorolja fel, vagy a metrikák beolvasása|
|/managers/devices/fileservers/shares/metricsDefinitions/read|Sorolja fel, vagy a metrikák meghatározások beolvasása|
|/managers/devices/fileservers/shares/read|Lekérdezi a megosztásokat vagy listája|
|/managers/devices/fileservers/shares/write|Létrehozni vagy frissíteni a megosztásokat|
|/managers/Devices/fileservers/Write|Létrehozni vagy frissíteni a fájlkiszolgálókon|
|/managers/devices/hardwareComponentGroups/changeControllerPowerState/action|Hardver összetevőcsoportok vezérlő power állapotának módosítása|
|/managers/Devices/hardwareComponentGroups/Read|A hardver összetevő csoportokat|
|/managers/Devices/Install/Action|Frissítések telepítése egy eszközön.|
|/managers/Devices/installUpdates/Action|Telepíti a frissítéseket az eszközökön|
|/managers/devices/iscsiservers/backup/action|Az iSCSI-kiszolgálók biztonsági másolatok készítéséhez.|
|/managers/devices/iscsiservers/delete|Törli az iSCSI-kiszolgálók|
|/managers/devices/iscsiservers/disks/delete|A lemezek törlése|
|/managers/devices/iscsiservers/disks/metrics/read|Sorolja fel, vagy a metrikák beolvasása|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|Sorolja fel, vagy a metrikák meghatározások beolvasása|
|/managers/devices/iscsiservers/disks/read|Lekérdezi a lemezek vagy listája|
|/managers/devices/iscsiservers/disks/write|Létrehozni vagy frissíteni a lemezek|
|/managers/devices/iscsiservers/metrics/read|Sorolja fel, vagy a metrikák beolvasása|
|/managers/devices/iscsiservers/metricsDefinitions/read|Sorolja fel, vagy a metrikák meghatározások beolvasása|
|/managers/devices/iscsiservers/read|Sorolja fel, vagy az iSCSI kiszolgálók beolvasása|
|/managers/Devices/iscsiservers/Write|Létrehozni vagy frissíteni az iSCSI-kiszolgálók|
|/managers/Devices/Jobs/Cancel/Action|Egy futó feladat megszakítása|
|/managers/Devices/Jobs/Read|Sorolja fel, vagy a feladat beolvasása|
|/managers/devices/listFailoverSets/action|A lista a feladatátvétel beállítja egy meglévő eszközt.|
|/managers/devices/listFailoverTargets/action|Az eszközök feladatátvételi céljainak|
|/managers/Devices/Metrics/Read|Sorolja fel, vagy a metrikák beolvasása|
|/managers/Devices/metricsDefinitions/Read|Sorolja fel, vagy a metrikák meghatározások beolvasása|
|/managers/devices/migrationSourceConfigurations/confirmMigration/action|Megerősíti, hogy a sikeres áttelepítéshez és véglegesítheti.|
|/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action|Áttelepítési megerősítése állapotának beolvasása.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action|Az áttelepítési becslés feladat állapotának beolvasása.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action|Az áttelepítés állapotának beolvasása.|
|/managers/devices/migrationSourceConfigurations/import/action|Az áttelepítéshez forrás konfigurációk importálása|
|/managers/devices/migrationSourceConfigurations/startMigration/action|Forrás-konfigurációk használatával áttelepítés indítása|
|/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action|Az áttelepítési folyamat időtartamának becsléséhez feladat indítása.|
|/managers/Devices/networkSettings/Read|Sorolja fel, vagy a hálózati beállításainak beolvasása|
|/managers/Devices/networkSettings/Write|Létrehoz egy új, vagy a hálózati beállítások frissítése|
|/managers/devices/publicEncryptionKey/action|Lista nyilvános titkosítási kulcsot, az Eszközkezelőben|
|/managers/Devices/publishSupportPackage/Action|Egy eszköz a Microsoft Support hibaelhárítási támogatási csomag közzététele.|
|/managers/Devices/Read|Sorolja fel, vagy az eszköz beolvasása|
|/managers/devices/scanForUpdates/action|Egy eszköz frissítések keresése.|
|/managers/devices/securitySettings/read|A biztonsági beállítások|
|/managers/devices/securitySettings/syncRemoteManagementCertificate/action|A távoli felügyeleti tanúsítvány eszköz szinkronizálása.|
|/managers/devices/securitySettings/update/action|A biztonsági beállítások frissítése.|
|/managers/devices/securitySettings/write|Létrehoz egy új, vagy a biztonsági beállítások frissítése|
|/managers/devices/sendTestAlertEmail/action|Konfigurált küldési e-mail címzetteknek teszt figyelmeztető e-mailt küldeni.|
|/managers/devices/timeSettings/read|Sorolja fel, vagy a idő beállításainak beolvasása|
|/managers/devices/timeSettings/write|Létrehoz egy új, vagy frissíti az idő beállítása|
|/managers/Devices/updateSummary/Read|Sorolja fel, vagy a frissítés összegzés beolvasása|
|/managers/devices/volumeContainers/delete|Törli a meglévő Kötettárolók (8000 sorozat csak)|
|/managers/devices/volumeContainers/listEncryptionKeys/action|Lista titkosítási kulcsokat a Kötettárolók|
|/managers/Devices/volumeContainers/Metrics/Read|A metrikák felsorolása|
|/managers/Devices/volumeContainers/metricsDefinitions/Read|A metrikák definíciók felsorolása|
|/managers/Devices/volumeContainers/Read|Kötettárolók listázása (csak 8000 sorozat)|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|Kötettárolók a helyettesítő titkosítási kulcsok|
|/managers/Devices/volumeContainers/Volumes/DELETE|Egy meglévő kötetek törlése|
|/managers/Devices/volumeContainers/Volumes/Metrics/Read|A metrikák felsorolása|
|/managers/Devices/volumeContainers/Volumes/metricsDefinitions/Read|A metrikák definíciók felsorolása|
|/managers/Devices/volumeContainers/Volumes/Read|A kötetek felsorolása|
|/managers/Devices/volumeContainers/Volumes/Write|Létrehoz egy új vagy frissít kötetek|
|/managers/Devices/volumeContainers/Write|Létrehoz egy új vagy frissít Kötettárolók (8000 sorozat csak)|
|/managers/Devices/Write|Létrehozása vagy frissítése az eszközöket|
|/managers/encryptionSettings/read|Sorolja fel, vagy a titkosítási beállításainak beolvasása|
|/Managers/extendedInformation/delete|A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be|
|/Managers/extendedInformation/read|A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be|
|/Managers/extendedInformation/write|A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be|
|/managers/getActivationKey/action|Aktiválási kulcs lekérése az Eszközkezelőben.|
|/managers/getEncryptionKey/action|Titkosítási kulcs beszerzése az Eszközkezelőben.|
|/managers/listActivationKey/action|Az aktiválási kulcsot a StorSimple eszköz Manager lekérdezi.|
|/managers/listPrivateEncryptionKey/action|Lekérdezi a személyes titkosítási kulcs egy StorSimple az Eszközkezelőben.|
|/managers/listPublicEncryptionKey/action|Nyilvános titkosítási kulcsok egy StorSimple Device Manager listában.|
|/managers/Metrics/Read|Sorolja fel, vagy a metrikák beolvasása|
|/managers/metricsDefinitions/Read|Sorolja fel, vagy a metrikák meghatározások beolvasása|
|/managers/provisionCloudAppliance/action|Hozzon létre egy új felhőalapú készülék.|
|/managers/Read|Sorolja fel, vagy az eszköz kezelők beolvasása|
|Kezelői/olvasása|A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum|
|/managers/regenarateRegistationCertificate/action|Az eszköz kezelők a regisztrációs tanúsítvány újbóli előállítása.|
|/managers/regenerateActivationKey/action|A kezelő aktiválási kulcs újragenerálása.|
|/managers/storageAccountCredentials/delete|A Tárfiók hitelesítő adatainak törlése|
|/managers/storageAccountCredentials/listAccessKey/action|A Tárfiók hitelesítő adatainak hozzáférési listázása|
|/managers/storageAccountCredentials/read|Sorolja fel, vagy a Tárfiók hitelesítő adatainak beolvasása|
|/managers/storageAccountCredentials/write|Létrehozni vagy frissíteni a Tárfiók hitelesítő adatait|
|/managers/storageDomains/delete|A tárolási tartományok törlése|
|/managers/storageDomains/read|Sorolja fel, vagy a tárolási tartományok beolvasása|
|/managers/storageDomains/write|Hozzon létre vagy a Storage-tartományok frissítése|
|/ kezelők/írása|Létrehozni vagy frissíteni az eszköz-kezelők|
|Kezelői/írása|A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Művelet | Leírás |
|---|---|
|/Locations/Quotas/Read|Olvasási Stream Analytics előfizetési kvóta|
|/ műveletek/Olvasás|Olvasási Stream Analytics-műveletet|
|/ Regisztrációs/művelet|Regisztrálni az előfizetést a Stream Analytics erőforrás-szolgáltató|
|/streamingjobs/Delete|A Stream Analytics-feladat törlése|
|/streamingjobs/functions/Delete|Törölje a Stream Analytics-feladat függvény|
|/streamingjobs/Functions/operationresults/Read|Olvassa el a művelet eredménye a Stream Analytics-feladat függvény|
|/streamingjobs/Functions/Read|Olvasási Stream Analytics feladatcsoportot|
|/streamingjobs/functions/RetrieveDefaultDefinition/action|A Stream Analytics feladat függvény az alapértelmezett definíció beolvasása|
|/streamingjobs/Functions/test/Action|Teszt Stream Analytics feladatcsoportot|
|/streamingjobs/Functions/Write|Írás a Stream Analytics-feladat függvény|
|/streamingjobs/inputs/Delete|Stream Analytics feladat bemeneti törlése|
|/streamingjobs/Inputs/operationresults/Read|Olvassa el a művelet eredménye a Stream Analytics feladat|
|/streamingjobs/Inputs/Read|Olvasási Stream Analytics feladat bemeneti|
|/streamingjobs/Inputs/sample/Action|A minta Stream Analytics feladat bemeneti|
|/streamingjobs/Inputs/test/Action|Teszt Stream Analytics feladat bemeneti|
|/streamingjobs/Inputs/Write|Írás Analytics-feladat adatfolyam-bemenet|
|/streamingjobs/metricdefinitions/Read|Olvassa el a metrikai meghatározásainak|
|/streamingjobs/operationresults/Read|Olvassa el a művelet eredménye Stream Analytics-feladat|
|/streamingjobs/outputs/Delete|Stream Analytics-feladat kimeneti törlése|
|/streamingjobs/outputs/operationresults/Read|Olvassa el a művelet eredménye a Stream Analytics Job Output|
|/streamingjobs/outputs/Read|Olvasási Stream Analytics Feladatkiemenetét|
|/streamingjobs/outputs/test/Action|Teszt Stream Analytics feladat eredménye|
|/streamingjobs/outputs/Write|Írás Analytics-feladat kimeneti adatfolyam|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Olvassa el a diagnosztikai beállítást.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Diagnosztikai beállításának írási.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|Streamingjobs naplók beolvasása.|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|A streamingjobs elérhető metrikai meghatározások beolvasása|
|/ streamingjobs/Olvasás|Olvasási Stream Analytics-feladat|
|/streamingjobs/Start/action|A Stream Analytics-feladat indítása|
|/streamingjobs/STOP/Action|A Stream Analytics-feladat leállítása|
|/streamingjobs/transformations/Delete|Törölje a Stream Analytics feladat átalakítása|
|/streamingjobs/transformations/Read|Olvasási Stream Analytics feladat átalakítása|
|/streamingjobs/transformations/Write|Írás a Stream Analytics feladat átalakítása|
|/streamingjobs/Write|Írás a Stream Analytics-feladat|

## <a name="microsoftsubscription"></a>Microsoft.Subscription

| Művelet | Leírás |
|---|---|
|/SubscriptionDefinitions/read|A felügyeleti csoporton belül az Azure-előfizetés definíciójának lekérdezése.|
|/SubscriptionDefinitions/write|Egy Azure-előfizetés-definíció létrehozása|

## <a name="microsoftsupport"></a>Microsoft.Support

| Művelet | Leírás |
|---|---|
|/ regisztrációs/művelet|Regisztrálás a támogatási erőforrás-szolgáltatóra|
|/supportTickets/Read|Egy támogatási jegy adatainak beolvasása (beleértve az állapotot, a súlyosságot, a kapcsolattartási adatokat és a kommunikációs elemeket), vagy az előfizetésekhez tartozó támogatási jegyek listájának beolvasása.|
|/ supportTickets/írása|Létrehozza vagy frissíti egy támogatási jegy. Létrehozhat egy támogatási jegy műszaki, számlázási, kvóták és előfizetés-kezeléssel kapcsolatos problémákat. Súlyossága, kapcsolattartási adatai és a meglévő támogatási jegyek kommunikációs frissítheti.|

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

| Művelet | Leírás |
|---|---|
|/Environments/accesspolicies/DELETE|A hozzáférési házirend törlése.|
|/Environments/accesspolicies/Read|Hozzáférési házirend tulajdonságait olvassa be.|
|/Environments/accesspolicies/Write|Létrehoz egy új hozzáférési házirendet egy olyan környezetben, vagy frissíti a meglévő hozzáférési házirendek.|
|/Environments/DELETE|Törli a környezetben.|
|/environments/eventsources/delete|Törli a esemény forrását.|
|/Environments/eventsources/eventsources/Providers/Microsoft.Insights/ diagnosticSettings írása|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read|A eventsources elérhető metrikai meghatározások beolvasása|
|/Environments/eventsources/Read|Egy eseményforrás tulajdonságait olvassa be.|
|/Environments/eventsources/Write|Létrehoz egy új eseményforrás a következőhöz egy olyan környezetben, vagy egy meglévő eseményforrás frissíti.|
|/environments/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/environments/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/environments/providers/Microsoft.Insights/metricDefinitions/read|A környezetek elérhető metrikai meghatározások beolvasása|
|/Environments/Read|A környezet tulajdonságait olvassa be.|
|/Environments/referencedatasets/DELETE|Törli a referencia-adatkészlet.|
|/Environments/referencedatasets/Read|A referencia-adatkészlet tulajdonságait olvassa be.|
|/Environments/referencedatasets/Write|Létrehoz egy új referencia-adatkészlet környezet vagy frissít egy meglévő referencia-adatkészlet.|
|/Environments/status/Read|A környezet, a kapcsolódó műveleteket, például érkező állapotának állapotának beolvasása.|
|/ környezetek/írása|Létrehoz egy új környezetben, vagy frissíti a meglévő környezetben.|
|/ regisztrációs/művelet|Az idő adatsorozat Insights erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a idő adatsorozat Insights környezetek létrehozását.|

## <a name="microsoftweb"></a>microsoft.web

| Művelet | Leírás |
|---|---|
|/apimanagementaccounts/apiacls/Read|Api Management fiókok Apiacls beolvasása.|
|/apimanagementaccounts/APIs/apiacls/DELETE|Törli az Api Management fiókok API-k Apiacls.|
|/apimanagementaccounts/APIs/apiacls/Read|Api Management fiókok API-k Apiacls beolvasása.|
|/apimanagementaccounts/APIs/apiacls/Write|Frissítse az Api Management fiókok API-k Apiacls.|
|/apimanagementaccounts/APIs/connectionacls/Read|Api Management fiókok API-k Connectionacls beolvasása.|
|/apimanagementaccounts/APIs/Connections/confirmconsentcode/Action|Erősítse meg a hozzájárulási kód Api Management fiókok API-k kapcsolatok.|
|/apimanagementaccounts/apis/connections/connectionacls/delete|Törli az Api Management fiókok API-k kapcsolatok Connectionacls.|
|/apimanagementaccounts/APIs/Connections/connectionacls/Read|Api Management fiókok API-k kapcsolatok Connectionacls beolvasása.|
|/apimanagementaccounts/APIs/Connections/connectionacls/Write|Frissítse az Api Management fiókok API-k kapcsolatok Connectionacls.|
|/apimanagementaccounts/apis/connections/delete|Törli az Api Management fiókok API-k kapcsolatok.|
|/apimanagementaccounts/apis/connections/getconsentlinks/action|Hozzájárulás hivatkozások Api Management fiókok API-k kapcsolatokhoz.|
|/apimanagementaccounts/APIs/Connections/listconnectionkeys/Action|Lista kapcsolat kulcsokat Api Management fiókok API-k kapcsolatok.|
|/apimanagementaccounts/apis/connections/listsecrets/action|Lista titkok Api Management fiókok API-k kapcsolatok.|
|/apimanagementaccounts/APIs/Connections/Read|Lekérni az Api Management fiókok API-k kapcsolatokat.|
|/apimanagementaccounts/APIs/Connections/Write|Frissítse az Api Management fiókok API-k kapcsolatok.|
|/apimanagementaccounts/APIs/DELETE|Törli az Api Management fiókok API-k.|
|/apimanagementaccounts/APIs/localizeddefinitions/DELETE|Törli az Api Management fiókok API-k honosított definíciókat.|
|/apimanagementaccounts/APIs/localizeddefinitions/Read|Az Api Management beolvasása fiókok API-k honosított definíciókat.|
|/apimanagementaccounts/APIs/localizeddefinitions/Write|Frissítés Api Management fiókok API-k honosított definíciókat.|
|/apimanagementaccounts/APIs/Read|Az beszerzése Api fiókok API-val.|
|/apimanagementaccounts/APIs/Write|Frissítse az Api Management fiókok API-k.|
|/apimanagementaccounts/connectionacls/Read|Api Management fiókok Connectionacls beolvasása.|
|/availablestacks/Read|Rendelkezésre álló verem beolvasása.|
|/billingmeters/Read|Számlázási mérőszámok listájának beolvasása.|
|/ tanúsítvány/törlése|Törölje a meglévő tanúsítványt.|
|/ tanúsítvány/Olvasás|A tanúsítványok listájának lekérdezése.|
|/ tanúsítvány/írása|Adja hozzá az új tanúsítványt, vagy frissítsen egy meglévőt.|
|/checknameavailability/Read|Ellenőrizze, hogy az erőforrásnév érhető el.|
|/classicmobileservices/Read|Klasszikus mobilszolgáltatások beolvasása.|
|/connectionGateways/Delete|Egy kapcsolat átjáró törlése.|
|/connectionGateways/Join/Action|Egy kapcsolat átjáró csatlakozik.|
|/connectiongateways/liststatus/action|Állapot kapcsolat átjáróit.|
|/connectionGateways/ListStatus/Action|Megadja a kapcsolat átjáró állapotát.|
|/connectionGateways/Move/Action|Egy kapcsolat átjáró helyezi.|
|/ connectionGateways/Olvasás|A kapcsolat átjárók listájának lekérdezése.|
|/ connectionGateways/írása|Létrehozza vagy frissíti a kapcsolat átjáró.|
|/Connections/confirmconsentcode/Action|Kapcsolatok hozzájárulási kód megerősítése.|
|/ kapcsolatok/törlése|Törli a kapcsolatot.|
|/Connections/JOIN/Action|A kapcsolat csatlakozik.|
|/Connections/listconsentlinks/Action|Hozzájárulás hivatkozások kapcsolatokhoz.|
|/connections/Move/Action|Egy kapcsolatot helyezi.|
|/ kapcsolatok/Olvasás|Kapcsolatok listájának beolvasása.|
|/ kapcsolatok/írása|Létrehozza vagy frissíti a kapcsolatot.|
|/customApis/Delete|Törli az egyéni API.|
|/customApis/extractApiDefinitionFromWsdl/Action|API-definíció kiolvassa a egy WSDL.|
|/customApis/Join/Action|Egy egyéni API csatlakozik.|
|/customApis/listWsdlInterfaces/Action|Egy egyéni API felületek WSDL jeleníti meg.|
|/customApis/Move/Action|Egy egyéni API helyezi.|
|/ customApis/Olvasás|Egyéni API listájának.|
|/ customApis/írása|Létrehozza vagy frissíti egy egyéni API-t.|
|/deploymentlocations/Read|Beolvasni a központi telepítési helyét.|
|/ geoRegions/Olvasás|A földrajzi régiók között listájának lekérdezése.|
|/hostingenvironments/capacities/read|Az üzemeltetési környezetek kapacitások beolvasása.|
|/hostingEnvironments/Delete|App Service-környezet törlése|
|/hostingenvironments/Diagnostics/Read|Első üzemeltetési környezetekben diagnosztika.|
|/hostingenvironments/inboundnetworkdependenciesendpoints/read|Az összes bejövő függősége a hálózati végpont beolvasása.|
|/hostingenvironments/metricdefinitions/read|Az üzemeltetési környezetek metrikai meghatározásainak beolvasása.|
|/hostingenvironments/multirolepools/metricdefinitions/read|Az üzemeltetési környezetek többcélú készletek metrikai meghatározásainak beolvasása.|
|/hostingenvironments/multirolepools/Metrics/Read|Első üzemeltetési környezetekben többcélú készletek metrikákat.|
|/hostingEnvironments/multiRolePools/Providers/Microsoft.Insights/ metricDefinitions olvasása|Az App Service-környezet MultiRole elérhető metrikai meghatározások beolvasása|
|/hostingEnvironments/multiRolePools/Read|Egy App Service Environment-környezetben egy előtér-címkészlet tulajdonságainak beolvasása|
|/hostingenvironments/multirolepools/skus/read|Első üzemeltetési környezetekben többcélú készletek SKU.|
|/hostingenvironments/multirolepools/usages/Read|Első üzemeltetési környezetekben többcélú készletek is érvényesek.|
|/hostingEnvironments/multiRolePools/Write|Előtér-készlet létrehozása az App Service-környezetben, vagy egy meglévő frissítése|
|/hostingenvironments/Operations/Read|Első üzemeltetési környezetekben műveletek.|
|/hostingenvironments/outboundnetworkdependenciesendpoints/read|A hálózati végpont minden kimenő függőségi beolvasása.|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/ hostingEnvironments/Olvasás|Az App Service-környezetek tulajdonságainak beolvasása|
|/hostingEnvironments/reboot/Action|Indítsa újra a egy App Service-környezetben lévő összes gépen|
|/hostingenvironments/resume/action|Végezze el újra üzemeltetési környezetekben.|
|/hostingenvironments/serverfarms/read|Első üzemeltetési környezetekben App Service-csomagokról.|
|/hostingenvironments/Sites/Read|Első környezetek webalkalmazások üzemeltetéséhez.|
|/hostingenvironments/suspend/action|Felfüggesztheti üzemeltetési környezetekben.|
|/hostingenvironments/usages/read|Első üzemeltetési környezetekben is érvényesek.|
|/hostingenvironments/workerpools/metricdefinitions/read|Első üzemeltetési környezetekben Workerpools metrika definíciókat.|
|/hostingenvironments/workerpools/Metrics/Read|Első üzemeltetési környezetekben Workerpools metrikákat.|
|/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read|Az App Service-környezet WorkerPool elérhető metrikai meghatározások beolvasása|
|/hostingEnvironments/workerPools/Read|A Feldolgozókészleten egy App Service Environment-környezetben tulajdonságainak beolvasása|
|/hostingenvironments/workerpools/skus/Read|Első üzemeltetési környezetekben Workerpools SKU.|
|/hostingenvironments/workerpools/usages/read|Első üzemeltetési környezetekben Workerpools módjait.|
|/hostingEnvironments/workerPools/Write|Hozzon létre egy új Feldolgozókészletek egy App Service Environment-környezetben, vagy egy meglévő frissítése|
|/hostingEnvironments/Write|Egy új App Service Environment-környezet létrehozása vagy meglévő ütemezés frissítése|
|/ishostingenvironmentnameavailable/read|GET, ha üzemeltetési környezet neve érhető el.|
|/ishostnameavailable/read|Ellenőrizze, hogy az állomásnév érhető el.|
|/isusernameavailable/read|Ellenőrizze, hogy a felhasználónév érhető el.|
|/listSitesAssignedToHostName/Read|Lekérése az állomásnév rendelt helyek nevét.|
|/Locations/apioperations/Read|Helyek API műveleteinek beolvasása.|
|/Locations/connectiongatewayinstallations/Read|Helyek kapcsolat átjáró telepítések beolvasása.|
|/locations/extractapidefinitionfromwsdl/action|Api-definíció kinyerése WSDL helyeket.|
|/Locations/listwsdlinterfaces/Action|Lista WSDL-felületek helyeket.|
|/locations/managedapis/apioperations/read|Helyek felügyelt API-műveletek beolvasása.|
|/locations/managedapis/Join/Action|A felügyelt API csatlakozik.|
|/Locations/managedapis/Read|Helyek felügyelt API-k beolvasása.|
|/Operations/Read|Műveletek beolvasása.|
|/publishingusers/Read|Első közzététel számára.|
|/ publishingusers/írása|A frissítés közzététele a felhasználók.|
|/ javaslatok/Olvasás|Az előfizetések javaslatok listájának.|
|/ regisztrációs/művelet|Az előfizetés Microsoft.Web erőforrás-szolgáltató regisztrálása.|
|/resourcehealthmetadata/Read|Erőforrás állapota metaadatot beszerezni.|
|/serverfarms/capabilities/read|Az alkalmazásszolgáltatási csomagok lehetőségek elérése.|
|/serverfarms/Delete|Egy meglévő App Service-csomag törlése|
|/serverfarms/firstpartyapps/settings/delete|Törli az App Service csomagokban első entitás alkalmazásokra vonatkozó beállítások.|
|/serverfarms/firstpartyapps/settings/read|Az alkalmazásszolgáltatási csomagok első entitás alkalmazások beállításainak beolvasása.|
|/serverfarms/firstpartyapps/settings/write|Frissítés App Service-csomagok első entitás alkalmazásokra vonatkozó beállítások.|
|/serverfarms/hybridconnectionnamespaces/relays/delete|Az alkalmazásszolgáltatási csomagok hibrid kapcsolat névterek továbbítók törlése.|
|/serverfarms/hybridconnectionnamespaces/relays/read|Az alkalmazásszolgáltatási csomagok hibrid kapcsolat névterek továbbítók beolvasása.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|Az App Service csomagokban hibrid kapcsolat névterek továbbítók webes alkalmazások beszerzéséhez.|
|/serverfarms/hybridconnectionplanlimits/read|Az alkalmazásszolgáltatási csomagok hibrid kapcsolat terv korlátok beolvasása.|
|/serverfarms/hybridconnectionrelays/read|Az alkalmazásszolgáltatási csomagok hibrid kapcsolat továbbítók beolvasása.|
|/serverfarms/metricdefinitions/read|Az alkalmazásszolgáltatási csomagok metrikai meghatározásainak beolvasása.|
|/serverfarms/metrics/read|Az alkalmazásszolgáltatási csomagok metrikákat kaphat.|
|/serverfarms/operationresults/Read|Az alkalmazásszolgáltatási csomagok művelet eredményt.|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read|App Service-csomag elérhető metrikai meghatározások beolvasása|
|/ serverfarms/Olvasás|Az egy App Service-csomag tulajdonságainak beolvasása|
|/serverfarms/restartSites/Action|Indítsa újra az összes webes alkalmazás az App Service-csomag|
|/serverfarms/Sites/Read|Az App Service csomagokban webes alkalmazások beszerzéséhez.|
|/serverfarms/skus/read|Az alkalmazásszolgáltatási csomagok termékváltozatok beolvasása.|
|/serverfarms/usages/read|Az alkalmazásszolgáltatási csomagok módjait beolvasása.|
|/serverfarms/virtualnetworkconnections/gateways/write|Az App Service csomagokban virtuális hálózati kapcsolatok átjárók frissítése.|
|/serverfarms/virtualnetworkconnections/read|Az alkalmazásszolgáltatási csomagok virtuális hálózati kapcsolatok lekérése.|
|/serverfarms/virtualnetworkconnections/routes/delete|Az App Service csomagokban virtuális hálózati kapcsolatok útvonalak törlése.|
|/serverfarms/virtualnetworkconnections/routes/read|Az App Service csomagokban virtuális hálózati kapcsolatok útvonalak beolvasása.|
|/serverfarms/virtualnetworkconnections/routes/write|Az alkalmazásszolgáltatási csomagok virtuális hálózati kapcsolatok az útvonalak frissítése.|
|/serverfarms/workers/reboot/action|Indítsa újra az App Service csomagokban munkavállalók.|
|/serverfarms/Write|Egy új App Service-csomag létrehozása vagy meglévő pillanatkép frissítése|
|/sites/analyzecustomhostname/read|Elemezze az egyéni állomásnevet.|
|/sites/applySlotConfig/Action|Webes alkalmazás tárolóhely konfigurációt cél tárolóhelyről az aktuális webes alkalmazás|
|/Sites/Backup/Action|Hozzon létre egy új webes alkalmazás biztonsági mentése|
|/ helyek/biztonsági mentési/olvasási|Web Apps biztonsági mentés beolvasása.|
|/Sites/Backup/Write|Web Apps másolat frissítéséhez.|
|/Sites/backups/DELETE|Delete Web Apps Backups.|
|/Sites/backups/List/Action|Lista Web Apps biztonsági mentéseket.|
|/Sites/backups/Read|A webalkalmazás biztonsági mentése tulajdonságainak beolvasása|
|/Sites/backups/restore/Action|Állítsa vissza a Web Apps biztonsági mentéseket.|
|/sites/config/delete|Delete Web Apps Config.|
|/sites/config/list/Action|Webes alkalmazás biztonsági bizalmas beállítások, például a hitelesítő adatokat, az alkalmazásbeállítások és az kapcsolati karakterláncok|
|/Sites/config/Read|A webalkalmazás konfigurációs beállításainak beolvasása|
|/sites/config/Write|Webes alkalmazás konfigurációs beállításainak frissítése|
|/sites/continuouswebjobs/delete|Delete Web Apps Continuous Web Jobs.|
|/sites/continuouswebjobs/read|Webes alkalmazások folyamatos webes feladatok beolvasása.|
|/sites/continuouswebjobs/start/action|Indítsa el a webes alkalmazások folyamatos webes feladatok.|
|/sites/continuouswebjobs/stop/action|Állítsa le a webes alkalmazások folyamatos webes feladatok.|
|/ helyek/törlése|Egy már meglévő webalkalmazás törlése|
|/Sites/Deployments/DELETE|Webes alkalmazások központi telepítéseit törli.|
|/Sites/Deployments/log/Read|Webes alkalmazások központi telepítésének napló beolvasása.|
|/Sites/Deployments/Read|Webes alkalmazások központi telepítésének beolvasása.|
|/Sites/Deployments/Write|Webes alkalmazások központi telepítések frissítése.|
|/sites/diagnostics/analyses/execute/Action|Webes alkalmazások diagnosztika elemzés futtatása.|
|/Sites/Diagnostics/analyses/Read|Web Apps diagnosztika elemzés beolvasása.|
|/sites/diagnostics/aspnetcore/read|Az ASP.NET Core app a Web Apps diagnosztika beolvasása.|
|/sites/diagnostics/autoheal/read|Web Apps diagnosztika elindulásáról beolvasása.|
|/Sites/Diagnostics/Deployment/Read|A webes alkalmazások Diagnostics telepítése beolvasása.|
|/Sites/Diagnostics/Deployments/Read|Web Apps diagnosztika központi telepítések beolvasása.|
|/sites/diagnostics/detectors/execute/Action|Futtassa a Web Apps diagnosztika érzékelő.|
|/Sites/Diagnostics/detectors/Read|Web Apps diagnosztika érzékelő beolvasása.|
|/sites/diagnostics/failedrequestsperuri/read|Webes alkalmazások diagnosztika a sikertelen kérelmek / Uri beolvasása.|
|/Sites/Diagnostics/frebanalysis/Read|Web Apps diagnosztika FREB elemzés beolvasása.|
|/Sites/Diagnostics/loganalyzer/Read|Web Apps diagnosztikai naplófájl Analyzer beolvasása.|
|/Sites/Diagnostics/Read|Web Apps diagnosztika kategóriák beolvasása.|
|/sites/diagnostics/runtimeavailability/read|Web Apps diagnosztika futásidejű rendelkezésre beolvasása.|
|/sites/diagnostics/servicehealth/read|Webes alkalmazások diagnosztika szolgáltatás állapotának beolvasása.|
|/Sites/Diagnostics/sitecpuanalysis/Read|Web Apps diagnosztika hely CPU elemzés beolvasása.|
|/sites/diagnostics/sitecrashes/read|Web Apps diagnosztika hely összeomlások beolvasása.|
|/Sites/Diagnostics/sitelatency/Read|Web Apps diagnosztika hely késés beolvasása.|
|/Sites/Diagnostics/sitememoryanalysis/Read|Web Apps diagnosztika hely memória elemzés beolvasása.|
|/sites/diagnostics/siterestartsettingupdate/read|Web Apps diagnosztika hely újraindítás beállítás frissítésének letöltése.|
|/Sites/Diagnostics/siterestartuserinitiated/Read|Web Apps diagnosztika hely újraindítás felhasználó által kezdeményezett beolvasása.|
|/sites/diagnostics/siteswap/read|Web Apps diagnosztika hely Swap beolvasása.|
|/Sites/Diagnostics/THREADCOUNT/Read|Web Apps diagnosztika szálak számának beolvasása.|
|/Sites/Diagnostics/workeravailability/Read|Web Apps diagnosztika Workeravailability beolvasása.|
|/Sites/Diagnostics/workerprocessrecycle/Read|Webes alkalmazások diagnosztika munkavégző folyamat újrahasznosítást beolvasása.|
|/Sites/domainownershipidentifiers/Read|Megkapja a Web Apps tartományi tulajdonjoga azonosítókat.|
|/sites/domainownershipidentifiers/write|Frissítse a Web Apps tartományi tulajdonjoga azonosítók.|
|/Sites/Functions/Action|Webalkalmazások funkciók.|
|/sites/functions/delete|Törölje a Web Apps funkciók.|
|/Sites/Functions/listsecrets/Action|Lista titkok Web Apps funkciók.|
|/Sites/Functions/masterkey/Read|Web Apps funkciók főkulcsos beolvasása.|
|/Sites/Functions/Read|Web Apps funkciók beolvasása.|
|/Sites/Functions/token/Read|Get Web Apps funkciók jogkivonat.|
|/Sites/Functions/Write|Frissítse a Web Apps funkciók.|
|/sites/hostnamebindings/delete|Delete Web Apps Hostname Bindings.|
|/sites/hostnamebindings/read|Get Web Apps Hostname Bindings.|
|/sites/hostnamebindings/write|Update Web Apps Hostname Bindings.|
|/sites/hybridconnection/delete|Web Apps hibrid kapcsolat törlése.|
|/sites/hybridconnection/read|Web Apps a hibrid kapcsolat beolvasása.|
|/sites/hybridconnection/write|Web Apps hibrid kapcsolat frissítése.|
|/sites/hybridconnectionnamespaces/relays/delete|Törölje a Web Apps hibrid kapcsolat névterek továbbítók.|
|/sites/hybridconnectionnamespaces/relays/listkeys/action|Listában kulcsok Web Apps hibrid kapcsolat névterek továbbítók.|
|/sites/hybridconnectionnamespaces/relays/read|Web Apps hibrid kapcsolat névterek továbbítók beolvasása.|
|/sites/hybridconnectionnamespaces/relays/write|Frissítse a Web Apps hibrid kapcsolat névterek továbbítók.|
|/sites/hybridconnectionrelays/read|Web Apps hibrid kapcsolat továbbítók beolvasása.|
|/sites/instances/deployments/delete|Törölje a Web Apps példányok központi telepítéseket.|
|/sites/instances/deployments/read|Web Apps példányok központi telepítések beolvasása.|
|/sites/instances/extensions/log/read|Web Apps példányok bővítmények napló beolvasása.|
|/sites/instances/extensions/read|Web Apps példányok kiterjesztések beolvasása.|
|/sites/instances/processes/delete|Törölje a Web Apps példányok folyamatokat.|
|/sites/instances/processes/read|Web Apps példányok folyamatok beolvasása.|
|/sites/instances/read|Web Apps-példányokat beszerezni.|
|/sites/listsyncfunctiontriggerstatus/action|Lista szinkronizálási függvény eseményindító állapot webalkalmazások.|
|/Sites/metricdefinitions/Read|Web Apps metrika meghatározások beolvasása.|
|/Sites/Metrics/Read|Web Apps metrikákat kaphat.|
|/sites/metricsdefinitions/read|Web Apps metrikák meghatározások beolvasása.|
|/Sites/migratemysql/Action|MySql Web Apps alkalmazások áttelepítése.|
|/Sites/migratemysql/Read|Első Web Apps alkalmazások áttelepítése a MySql.|
|/sites/networktrace/action|Hálózati nyomkövetés webalkalmazások.|
|/Sites/newpassword/Action|Newpassword Web Apps.|
|/Sites/operationresults/Read|Web Apps művelet eredményt.|
|/Sites/Operations/Read|Web Apps műveleteinek beolvasása.|
|/Sites/perfcounters/Read|Web Apps teljesítményszámlálók beolvasása.|
|/sites/premieraddons/delete|Törölje a Web Apps Premier bővítményei.|
|/sites/premieraddons/read|Web Apps Premier bővítményei beolvasása.|
|/sites/premieraddons/write|Frissítse a Web Apps Premier bővítményei.|
|/Sites/Processes/Read|Web Apps folyamatok beolvasása.|
|/sites/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/sites/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/sites/providers/Microsoft.Insights/metricDefinitions/Read|Webalkalmazás elérhető metrikai meghatározások beolvasása|
|/Sites/publiccertificates/DELETE|Törli a Web Apps nyilvános tanúsítványokat.|
|/Sites/publiccertificates/Read|Web Apps nyilvános tanúsítványokat le.|
|/Sites/publiccertificates/Write|Web Apps nyilvános tanúsítványok frissítése.|
|/sites/publish/Action|A webes alkalmazás közzététele|
|/sites/publishxml/Action|Közzétételi profil xml egy webalkalmazást az beszerzése|
|/sites/publishxml/read|Kérhető le a webes közzétételi XML.|
|/ helyek/Olvasás|A webes alkalmazás tulajdonságainak beolvasása|
|/Sites/recommendationhistory/Read|Webes alkalmazások javaslat előzmények beolvasása.|
|/Sites/recommendations/disable/Action|Tiltsa le a Web Apps javaslatokat.|
|/Sites/recommendations/Read|A webalkalmazás javaslatok listája az beszerzése.|
|/Sites/RECOVER/Action|Recover Web Apps.|
|/sites/resetSlotConfig/Action|Alaphelyzetbe állítja a webes alkalmazás konfigurálása|
|/sites/resourcehealthmetadata/read|Web Apps erőforrás állapotának metaadatot beszerezni.|
|/sites/restart/Action|A webalkalmazás újraindítása|
|/Sites/restore/Read|Web Apps visszaállítási beolvasása.|
|/Sites/restore/Write|Állítsa vissza a webalkalmazásokat.|
|/Sites/siteextensions/DELETE|Törölje a Web Apps hely bővítmények.|
|/Sites/siteextensions/Read|Web Apps helyhez kiterjesztések beolvasása.|
|/Sites/siteextensions/Write|Web Apps hely bővítmények frissítése.|
|/sites/slots/analyzecustomhostname/read|Első webes alkalmazások üzembe helyezési ponti elemezheti az egyéni állomásnevet.|
|/sites/slots/applySlotConfig/Action|Webes alkalmazás tárolóhely konfiguráció cél tárolóhelyről az aktuális tárolóhelyre alkalmazásához.|
|/Sites/slots/Backup/Action|Hozzon létre új webalkalmazás biztonsági mentése.|
|/Sites/slots/Backup/Read|Webes alkalmazások üzembe helyezési ponti biztonsági mentés beolvasása.|
|/Sites/slots/Backup/Write|Webes alkalmazások üzembe helyezési ponti másolat frissítéséhez.|
|/Sites/slots/backups/DELETE|Törölje a webes alkalmazások üzembe helyezési ponti biztonsági mentéseket.|
|/Sites/slots/backups/List/Action|Lista webes alkalmazások üzembe helyezési ponti biztonsági mentéseket.|
|/Sites/slots/backups/Read|A webalkalmazás tárolóhelyei biztonsági másolat tulajdonságainak beolvasása|
|/Sites/slots/backups/restore/Action|Állítsa vissza a webes alkalmazások üzembe helyezési ponti biztonsági mentéseket.|
|/sites/slots/config/delete|Webes alkalmazások üzembe helyezési ponti konfiguráció törlése.|
|/sites/slots/config/list/Action|Webes alkalmazás a tárhely biztonsági bizalmas beállítások, például a hitelesítő adatokat, az alkalmazásbeállítások és az kapcsolati karakterláncok|
|/sites/slots/config/Read|Webes alkalmazás a tárhely konfigurációs beállításainak beolvasása|
|/sites/slots/config/Write|Webes alkalmazás a tárhely konfigurációs beállításainak frissítése|
|/sites/slots/continuouswebjobs/delete|Webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok törlése.|
|/sites/slots/continuouswebjobs/read|Webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok beolvasása.|
|/sites/slots/continuouswebjobs/start/action|Indítsa el a webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok.|
|/sites/slots/continuouswebjobs/stop/action|Állítsa le a webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok.|
|/Sites/slots/DELETE|Törölje a meglévő Web App tárhelyek|
|/Sites/slots/Deployments/DELETE|Törölje a webes alkalmazások üzembe helyezési ponti központi telepítések.|
|/Sites/slots/Deployments/log/Read|Webes alkalmazások üzembe helyezési ponti központi telepítések napló beolvasása.|
|/Sites/slots/Deployments/Read|Webes alkalmazások üzembe helyezési ponti központi telepítések beolvasása.|
|/Sites/slots/Deployments/Write|Webes alkalmazások üzembe helyezési ponti központi telepítések frissítése.|
|/sites/slots/diagnostics/analyses/execute/Action|Webes alkalmazások üzembe helyezési ponti diagnosztika elemzés futtatása.|
|/Sites/slots/Diagnostics/analyses/Read|Webes alkalmazások üzembe helyezési ponti diagnosztika elemzés beolvasása.|
|/sites/slots/diagnostics/aspnetcore/read|Az ASP.NET Core app a webes alkalmazások üzembe helyezési ponti diagnosztika beolvasása.|
|/sites/slots/diagnostics/autoheal/read|Webes alkalmazások üzembe helyezési ponti diagnosztika elindulásáról beolvasása.|
|/Sites/slots/Diagnostics/Deployment/Read|A webes alkalmazások üzembe helyezési ponti Diagnostics telepítése beolvasása.|
|/Sites/slots/Diagnostics/Deployments/Read|Webes alkalmazások üzembe helyezési ponti diagnosztika központi telepítések beolvasása.|
|/sites/slots/diagnostics/detectors/execute/Action|Futtassa a webes alkalmazások üzembe helyezési ponti diagnosztika érzékelő.|
|/Sites/slots/Diagnostics/detectors/Read|Webes alkalmazások üzembe helyezési ponti diagnosztika érzékelő beolvasása.|
|/Sites/slots/Diagnostics/frebanalysis/Read|Webes alkalmazások üzembe helyezési ponti diagnosztika FREB elemzés beolvasása.|
|/Sites/slots/Diagnostics/loganalyzer/Read|Webes alkalmazások üzembe helyezési ponti diagnosztikai naplófájl Analyzer beolvasása.|
|/Sites/slots/Diagnostics/Read|Webes alkalmazások üzembe helyezési ponti diagnosztika beolvasása.|
|/sites/slots/diagnostics/runtimeavailability/read|Webes alkalmazások üzembe helyezési ponti diagnosztika futásidejű rendelkezésre beolvasása.|
|/sites/slots/diagnostics/servicehealth/read|Első webes alkalmazások üzembe helyezési ponti diagnosztika szolgáltatás állapotát.|
|/Sites/slots/Diagnostics/sitecpuanalysis/Read|Webes alkalmazások üzembe helyezési ponti diagnosztika hely CPU elemzés beolvasása.|
|/Sites/slots/Diagnostics/sitecrashes/Read|Webes alkalmazások üzembe helyezési ponti diagnosztika hely összeomlások beolvasása.|
|/Sites/slots/Diagnostics/sitelatency/Read|Webes alkalmazások üzembe helyezési ponti diagnosztika hely késés beolvasása.|
|/Sites/slots/Diagnostics/sitememoryanalysis/Read|Webes alkalmazások üzembe helyezési ponti diagnosztika hely memória elemzés beolvasása.|
|/sites/slots/diagnostics/siterestartsettingupdate/read|Webes alkalmazások üzembe helyezési ponti diagnosztika hely újraindítás beállítás frissítésének letöltése.|
|/Sites/slots/Diagnostics/siterestartuserinitiated/Read|Webes alkalmazások üzembe helyezési ponti diagnosztika webhely újraindítását felhasználó által kezdeményezett beolvasása.|
|/sites/slots/diagnostics/siteswap/read|Webes alkalmazások üzembe helyezési ponti diagnosztika hely Swap beolvasása.|
|/Sites/slots/Diagnostics/THREADCOUNT/Read|Webes alkalmazások üzembe helyezési ponti diagnosztika szálak számának beolvasása.|
|/Sites/slots/Diagnostics/workeravailability/Read|Webes alkalmazások üzembe helyezési ponti diagnosztika Workeravailability beolvasása.|
|/Sites/slots/Diagnostics/workerprocessrecycle/Read|Webes alkalmazások üzembe helyezési ponti diagnosztika munkavégző folyamat újrahasznosítást beolvasása.|
|/Sites/slots/domainownershipidentifiers/Read|Megkapja a webes alkalmazások üzembe helyezési ponti tartomány tulajdonjoga azonosítókat.|
|/sites/slots/hostnamebindings/delete|Delete Web Apps Slots Hostname Bindings.|
|/sites/slots/hostnamebindings/read|Get Web Apps Slots Hostname Bindings.|
|/sites/slots/hostnamebindings/write|Update Web Apps Slots Hostname Bindings.|
|/sites/slots/hybridconnection/delete|Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat törlése.|
|/sites/slots/hybridconnection/read|Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat lekérdezése.|
|/sites/slots/hybridconnection/write|Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat frissítése.|
|/sites/slots/hybridconnectionnamespaces/relays/delete|Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat névterek továbbítók törlése.|
|/Sites/slots/hybridconnectionnamespaces/relays/Write|Frissítse a webes alkalmazások üzembe helyezési ponti hibrid kapcsolat névterek továbbítók.|
|/Sites/slots/hybridconnectionrelays/Read|Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat továbbítók beolvasása.|
|/sites/slots/instances/deployments/read|Webes alkalmazások üzembe helyezési ponti példányok központi telepítések beolvasása.|
|/sites/slots/instances/processes/delete|Törölje a webes alkalmazások üzembe helyezési ponti példányok folyamatokat.|
|/sites/slots/instances/processes/read|Webes alkalmazások üzembe helyezési ponti példányok folyamatok beolvasása.|
|/sites/slots/instances/read|Webes alkalmazások üzembe helyezési ponti-példányokat beszerezni.|
|/Sites/slots/metricdefinitions/Read|Webes alkalmazások üzembe helyezési ponti metrikai meghatározásainak beolvasása.|
|/Sites/slots/Metrics/Read|Webes alkalmazások üzembe helyezési ponti metrikákat kaphat.|
|/Sites/slots/migratemysql/Read|Első webes alkalmazások üzembe helyezési ponti MySql áttelepíteni.|
|/sites/slots/networktrace/action|Hálózati nyomkövetés Web Apps tárolóhelye.|
|/Sites/slots/newpassword/Action|ÚjJelszó Web Apps tárolóhelye.|
|/Sites/slots/operationresults/Read|Webes alkalmazások üzembe helyezési ponti művelet eredményekhez juthat.|
|/Sites/slots/Operations/Read|Webes alkalmazások üzembe helyezési ponti műveleteinek beolvasása.|
|/Sites/slots/perfcounters/Read|Webes alkalmazások üzembe helyezési ponti teljesítményszámlálók beolvasása.|
|/Sites/slots/phplogging/Read|Webes alkalmazások üzembe helyezési ponti Phplogging beolvasása.|
|/sites/slots/premieraddons/delete|Webes alkalmazások üzembe helyezési ponti Premier bővítményei törlése.|
|/sites/slots/premieraddons/read|Webes alkalmazások üzembe helyezési ponti Premier bővítményei beolvasása.|
|/sites/slots/premieraddons/write|Frissítse a webes alkalmazások üzembe helyezési ponti Premier bővítményei.|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read|Az erőforrás diagnosztikai beállításainak beolvasása|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write|Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése|
|/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read|A webalkalmazás elérhető metrikai meghatározások beolvasása|
|/Sites/slots/publiccertificates/Read|Webes alkalmazások üzembe helyezési ponti nyilvános tanúsítványokat le.|
|/Sites/slots/publiccertificates/Write|Hozzon létre, vagy a Web Apps üzembe helyezési ponti nyilvános tanúsítványok frissítése.|
|/sites/slots/publish/Action|Egy webes tárolóhelye közzététele|
|/sites/slots/publishxml/Action|Profil xml közzétételt a webalkalmazás beolvasása|
|/Sites/slots/Read|A webalkalmazás üzembe helyezési pont tulajdonságainak beolvasása|
|/sites/slots/resetSlotConfig/Action|Webes alkalmazás helyezési pont konfigurációjának visszaállítása|
|/sites/slots/resourcehealthmetadata/read|Webes alkalmazások üzembe helyezési ponti Resource Health metaadatot beszerezni.|
|/Sites/slots/restart/Action|Indítsa újra a webes alkalmazás tárhely|
|/Sites/slots/restore/Read|Webes alkalmazások üzembe helyezési ponti visszaállítási beolvasása.|
|/Sites/slots/restore/Write|Állítsa vissza a Web Apps tárolóhelye.|
|/Sites/slots/siteextensions/DELETE|Webes alkalmazások üzembe helyezési ponti helyhez kiterjesztések törlése.|
|/Sites/slots/siteextensions/Read|Webes alkalmazások üzembe helyezési ponti helyhez kiterjesztések beolvasása.|
|/Sites/slots/siteextensions/Write|Webes alkalmazások üzembe helyezési ponti hely bővítmények frissítése.|
|/Sites/slots/slotsdiffs/Action|Konfiguráció web app és az üzembe helyezési ponti közötti különbségek beolvasása|
|/sites/slots/slotsswap/Action|Felcserélni a webalkalmazás üzembe helyezési|
|/sites/slots/snapshots/read|Webes alkalmazások üzembe helyezési ponti pillanatképek beolvasása.|
|/Sites/slots/sourcecontrols/DELETE|Webes alkalmazás a tárhely forrás a konfigurációs beállítások törlése|
|/Sites/slots/sourcecontrols/Read|Webes alkalmazás a tárhely verziókezelő konfigurációs beállításainak beolvasása|
|/Sites/slots/sourcecontrols/Write|Webes alkalmazás a tárhely forrás a konfigurációs beállítások frissítése|
|/Sites/slots/Start/Action|Indítsa el a webes alkalmazás tárhely|
|/Sites/slots/STOP/Action|Állítsa le a webes alkalmazás tárhely|
|/Sites/slots/Sync/Action|Szinkronizálási Web Apps tárolóhelye.|
|/sites/slots/triggeredwebjobs/delete|Webes alkalmazások üzembe helyezési ponti indított webjobs-feladatok törlése.|
|/sites/slots/triggeredwebjobs/read|Webes alkalmazások üzembe helyezési ponti indított webjobs-feladatok beolvasása.|
|/sites/slots/triggeredwebjobs/run/action|Webes alkalmazások üzembe helyezési ponti indított webjobs-feladatok futtatásához.|
|/Sites/slots/usages/Read|Webes alkalmazások üzembe helyezési ponti módjait beolvasása.|
|/sites/slots/virtualnetworkconnections/delete|Törölje a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatokat.|
|/sites/slots/virtualnetworkconnections/gateways/write|Frissítse a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatok átjárókat.|
|/Sites/slots/virtualnetworkconnections/Read|Lekérni a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatokat.|
|/sites/slots/virtualnetworkconnections/write|Frissítse a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatokat.|
|/Sites/slots/webjobs/Read|Webes alkalmazások üzembe helyezési ponti webjobs-feladatok beolvasása.|
|/Sites/slots/Write|Webes alkalmazás új tárhely létrehozása vagy meglévő pillanatkép frissítése|
|/Sites/slotsdiffs/Action|Konfiguráció web app és az üzembe helyezési ponti közötti különbségek beolvasása|
|/sites/slotsswap/Action|Felcserélni a webalkalmazás üzembe helyezési|
|/sites/snapshots/read|Web Apps pillanatképek beolvasása.|
|/sites/sourcecontrols/Delete|Webes alkalmazás forrás a konfigurációs beállítások törlése|
|/Sites/sourcecontrols/Read|Webalkalmazás verziókövetésének konfigurációs beállításainak beolvasása|
|/Sites/sourcecontrols/Write|Webes alkalmazás forrás a konfigurációs beállítások frissítése|
|/Sites/Start/Action|A webalkalmazás elindítása|
|/Sites/STOP/Action|A webalkalmazás leállítása|
|/Sites/Sync/Action|Sync Web Apps.|
|/sites/syncfunctiontriggers/action|Szinkronizálási függvény eseményindítók webes alkalmazásokhoz.|
|/sites/triggeredwebjobs/delete|Web Apps indított webjobs-feladatok törlése.|
|/sites/triggeredwebjobs/history/read|Webes alkalmazások indított webjobs-feladatok előzményeinek lekérése.|
|/sites/triggeredwebjobs/read|Web Apps indított webjobs-feladatok beolvasása.|
|/sites/triggeredwebjobs/run/action|Web Apps indított webjobs-feladatok futtatásához.|
|/Sites/usages/Read|Web Apps módjait beolvasása.|
|/sites/virtualnetworkconnections/delete|Törölje a Web Apps virtuális hálózati kapcsolatokat.|
|/sites/virtualnetworkconnections/gateways/read|Web Apps virtuális hálózati kapcsolatok átjárók beolvasása.|
|/sites/virtualnetworkconnections/gateways/write|Frissítse a Web Apps virtuális hálózati kapcsolatok átjárókat.|
|/sites/virtualnetworkconnections/read|Lekérni a Web Apps virtuális hálózati kapcsolatokat.|
|/sites/virtualnetworkconnections/write|Frissítse a Web Apps virtuális hálózati kapcsolatokat.|
|/Sites/webjobs/Read|Web Apps webjobs-feladatok beolvasása.|
|/ helyek/írása|Új webalkalmazás létrehozása vagy meglévő pillanatkép frissítése|
|/skus/Read|SKU beolvasása.|
|/sourcecontrols/Read|Adatforrás-vezérlők beolvasása.|
|/ sourcecontrols/írása|Frissítse az adatforrás-vezérlők.|
|/ unregister/művelet|Az előfizetés Microsoft.Web erőforrás-szolgáltató regisztrációját.|
|/ ellenőrzése/művelet|Ellenőrzése.|
|/verifyhostingenvironmentvnet/action|Ellenőrizze, hogy üzemeltetési környezet Vnet.|

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

| Művelet | Leírás |
|---|---|
|/Components/Read|Olvasási műveletek erőforrások|
|/healthInstances/Read|Olvasási műveletek erőforrások|
|Műveletek/olvasása|Olvasási műveletek erőforrások|
|/workloads/DELETE|A munkaterhelés erőforrás törlése|
|/workloads/Read|A munkaterhelés erőforrás beolvasása|
|/ munkaterhelések/írása|Írja a munkaterhelés erőforrás|

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [hozzon létre egy egyéni biztonsági szerepkört](custom-roles.md).
- Tekintse át a [beépített RBAC-szerepkörök](built-in-roles.md).
- Hozzáférés-hozzárendelések kezelése [felhasználó](role-assignments-users.md) vagy [erőforrás](role-assignments-portal.md) 
- Megtudhatja, hogyan [tevékenységi naplóit rendszervizsgálati műveleteket az egyes erőforrások megtekintése](~/articles/azure-resource-manager/resource-group-audit.md)
