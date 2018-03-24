---
title: Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) beépített szerepkörök |} Microsoft Docs
description: Ez a témakör ismerteti a beépített szerepkörök szerepköralapú hozzáférés-vezérlés (RBAC). A szerepkörök folyamatosan kerülnek, ezért ellenőrizze a dokumentációt frissesség.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 0e8f1d97f51fbfedbca1619ef5e7f28a3a0570b9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Az Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök
Azure szerepköralapú hozzáférés-vezérlés (RBAC) tartalmaz a következő beépített szerepkörök, felhasználók, csoportok és szolgáltatások rendelhető. A beépített szerepkörök definíciója nem módosítható. Azonban létrehozhat [egyéni szerepkörök az Azure RBAC](role-based-access-control-custom-roles.md) a szervezet igényeinek megfelelően.

## <a name="built-in-role-descriptions"></a>Beépített szerepkör leírása
A következő táblázat a beépített szerepkörök rövid leírása. A szerepkör nevét, a részletes listájának megtekintéséhez kattintson **műveletek** és **notactions** a szerepkörhöz. A **műveletek** tulajdonság határozza meg az engedélyezett műveletek az Azure-erőforrások. A művelet karakterláncok helyettesítő karakterek is használhatók. A **notactions** tulajdonság határozza meg a műveleteket, amelyek ki lettek zárva ebből az engedélyezett műveletek.

A művelet határozza meg, milyen műveleteket hajthat végre egy adott erőforrástípusra. Példa:
- **Írási** PUT, POST, javítás és törlési műveletek végrehajtását teszi lehetővé.
- **Olvasási** GET műveletek végrehajtását teszi lehetővé.

Ez a cikk csak a különböző szerepkörök ma foglalkozik. Amikor szerepkör hozzárendelése egy felhasználóhoz, azonban korlátozhatja az engedélyezett műveletek további hatókör megadásával. Ez akkor hasznos, ha azt szeretné, hogy valaki egy webhely közreműködő, de csak az egy erőforráscsoport.

> [!NOTE]
> Az Azure szerepkör-definíciók folyamatosan fejlődik. Ez a cikk van naprakész, a lehető, de a legújabb definíciók szerepkörök az Azure PowerShell mindig található. Használja a [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) parancsmag minden aktuális szerepkörök listáját. Egy adott szerepkör használatával is mélyedjen `(get-azurermroledefinition "<role name>").actions` vagy `(get-azurermroledefinition "<role name>").notactions` alkalmazhatók. Használjon [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) műveletek adott Azure-erőforrás-szolgáltatót.


| Beépített szerepkör | Leírás |
| --- | --- |
| [Tulajdonos](#owner) | Mindent felügyelhetnek, beleértve a hozzáférést |
| [Közreműködő](#contributor) | Hozzáférés kivételével mindent felügyelhetnek |
| [Reader](#reader) | Mindent megtekinthetnek, de nem módosítható |
| [API Management szolgáltatás közreműködő](#api-management-service-contributor) | Az API Management szolgáltatásokat kezelheti |
| [API Management szolgáltatást üzemeltető szerepkör](#api-management-service-operator-role) | Az API Management szolgáltatásokat kezelheti |
| [API Management szolgáltatás olvasó szerepkört](#api-management-service-reader-role) | Az API Management szolgáltatásokat kezelheti |
| [Application Insights-összetevővel kapcsolatos közreműködői](#application-insights-component-contributor) | Kezelhet Application Insights-összetevőket |
| [Application Insights pillanatkép hibakereső](#application-insights-snapshot-debugger) | Engedélyt ad a felhasználónak az Application Insights pillanatkép-hibakeresői szolgáltatásainak használatára |
| [Automation-feladat operátor](#automation-job-operator) | Feladatok létrehozása és kezelése Automation-runbookok használatával. |
| [Automatizálási operátor](#automation-operator) | Indítás, Leállítás, felfüggesztése és folytatása feladatok tudni |
| [Automation-Runbook kezelő](#automation-runbook-operator) | Runbook-tulajdonságok olvasása – a runbook-feladatok létrehozásához. |
| [Az Azure verem regisztrációs tulajdonosa](#azure-stack-registration-owner) | Lehetővé teszi az Azure Stack-regisztrációk kezelését. |
| [Biztonsági mentési közreműködő](#backup-contributor) | Kezelheti az összes biztonságimásolat-felügyeleti műveletek, kivéve a Recovery Services-tároló létrehozása, és adjon hozzáférést a mások számára |
| [Biztonságimásolat-felelős](#backup-operator) | Kezelheti az összes biztonságimásolat-felügyeleti műveletek kivételével tárolók, mások biztonsági mentési és amely elérésének létrehozása |
| [Biztonsági mentési olvasó](#backup-reader) | A Recovery Services-tároló biztonságimásolat-felügyeleti képes figyelni |
| [Számlázási olvasó](#billing-reader) | Számlázási vonatkozó összes információt megtekinthetik |
| [BizTalk Contributor](#biztalk-contributor) | Kezelheti a BizTalk szolgáltatások |
| [CDN-végpont közreműködő](#cdn-endpoint-contributor) | Kezelhet CDN-végpontokat, de nem tud hozzáférést adni más felhasználóknak. |
| [CDN-végpont olvasó](#cdn-endpoint-reader) | Megtekintheti a CDN-végpontokat, de nem végezhet módosításokat. |
| [CDN-profil közreműködő](#cdn-profile-contributor) | Kezelheti a CDN-profilokat és azok végpontjait, de nem tud hozzáférést adni más felhasználóknak. |
| [CDN-profil olvasó](#cdn-profile-reader) | Megtekintheti a CDN-profilokat és azok végpontjait, de nem végezhet módosításokat. |
| [Klasszikus hálózati közreműködő](#classic-network-contributor) | Kezelheti a klasszikus virtuális hálózatok és foglalt IP-cím |
| [Hagyományos tárolási fiók közreműködői](#classic-storage-account-contributor) | Kezelheti a klasszikus tárfiókokba |
| [Hagyományos tárolási fiók kulcs operátor szolgáltatás szerepkör](#classic-storage-account-key-operator-service-role) | A hagyományos tárfiók kulcsának operátorai jogosultak a hagyományos tárfiókokhoz tartozó kulcsok listázására és újragenerálására |
| [Klasszikus virtuális gép közreműködő](#classic-virtual-machine-contributor) | Kezelheti a klasszikus virtuális gépeket, de nem a virtuális hálózati vagy tárolási fiókot, amelyhez csatlakoznak |
| [ClearDB MySQL DB Contributor](#cleardb-mysql-db-contributor) | Kezelheti a ClearDB MySQL-adatbázisok |
| [A cosmos DB fiók olvasó szerepkört](#cosmos-db-account-reader-role) | Azure Cosmos DB fiókadatokhoz el tud olvasni. Lásd: [DocumentDB-fiók közreműködői](#documentdb-account-contributor) Azure Cosmos DB kezelésére. |
| [Data Factory közreműködő](#data-factory-contributor) | Létrehozása és kezelése az adat-előállítók és gyermekerőforrásait rajtuk. |
| [Data Lake Analytics-fejlesztőknek](#data-lake-analytics-developer) | Lehetővé teszi a saját feladatok elküldését, figyelését és kezelését, de nem tud létrehozni vagy törölni Data Lake Analytics-fiókokat. |
| [DevTest Labs felhasználó](#devtest-labs-user) | Minden tekintheti meg és csatlakozni, a start, újraindítás és leállítás virtuális gépek |
| [DNS-zóna közreműködő](#dns-zone-contributor) | Can manage DNS zones and records. |
| [A DocumentDB-fiók közreműködői](#documentdb-account-contributor) | Felügyelheti az Azure Cosmos DB fiókjait. Azure Cosmos-adatbázis a DocumentDB nevén. |
| [Intelligens rendszerek fiók közreműködői](#intelligent-systems-account-contributor) | Intelligens rendszerek fiókok is kezelése |
| [Kulcstároló közreműködő](#key-vault-contributor) | Lehetővé teszi a kulcstartók kezelését, de ezekhez nem biztosít hozzáférést. |
| [Lab Creator](#lab-creator) | Lehetővé teszi a létrehozása, kezelése, a Azure labor fiókok a felügyelt tesztkörnyezeteket törlése. |
| [Napló Analytics közreműködő](#log-analytics-contributor) | Napló Analytics közreműködői összes figyelési adatot olvashatja és szerkesztése a figyelési beállításokat. Figyelési beállításainak szerkesztése magában foglalja a Virtuálisgép-bővítmény hozzáadása a virtuális gépek; tárfiókkulcsok tudják konfigurálni a naplók az Azure Storage; gyűjtemény olvasása létrehozásáról és konfigurálásáról az Automation-fiók; megoldásokkal; és az összes Azure-erőforrások konfigurálása az Azure diagnostics. |
| [Napló Analytics olvasó](#log-analytics-reader) | A Log Analytics-olvasó megtekintheti az összes figyelési adatot, és kereshet azokban, valamint megtekintheti a figyelési beállításokat, beleértve az összes Azure-erőforrás Azure-beli diagnosztikájának konfigurációját is. |
| [Logic App közreműködő](#logic-app-contributor) | Lehetővé teszi logikai alkalmazások kezelését, de ezekhez nem biztosít hozzáférést. |
| [Logic App operátor](#logic-app-operator) | Lehetővé teszi logikai alkalmazások olvasását, engedélyezését és letiltását. |
| [Felügyelt identitás közreműködő](#managed-identity-contributor) | Létrehozása, olvasása, frissítése és törlése a felhasználói identitás |
| [Felügyelt identitás operátor](#managed-identity-operator) | Olvassa el, és rendelje hozzá a felhasználói identitás |
| [A közreműködői figyelése](#monitoring-contributor) | Összes figyelési adatot olvashatja és szerkesztheti a figyelési beállításokat. Lásd még: [Ismerkedés a szerepkörök, engedélyek és biztonsági Azure megfigyelővel](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Figyelési olvasó](#monitoring-reader) | Az összes figyelési adatokat (metrikákat, naplói, stb.) el tud olvasni. Lásd még: [Ismerkedés a szerepkörök, engedélyek és biztonsági Azure megfigyelővel](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Hálózati közreműködő](#network-contributor) | Kezelheti az összes hálózati erőforrás |
| [Új New Relic APM fiók közreműködői](#new-relic-apm-account-contributor) | Lehetővé teszi a New Relic Application Performance Management-fiókok és -alkalmazások kezelését, az azokhoz való hozzáférés nélkül. |
| [Redis gyorsítótár közreműködő](#redis-cache-contributor) | Kezelheti a Redis gyorsítótár |
| [A Feladatütemező feladat gyűjtemények közreműködő](#scheduler-job-collections-contributor) | Kezelheti a Scheduler feladatgyűjteményei |
| [Keresési szolgáltatás közreműködő](#search-service-contributor) | Kezelheti a keresési szolgáltatások |
| [Biztonsági rendszergazda](#security-admin) | A csak a Security Center: is megtekintheti a biztonsági házirendek, biztonsági állapotok megtekintéséhez, szerkesztheti a biztonsági házirendek, a riasztások megtekintése és a javaslatok, hagyja figyelmen kívül a riasztások és javaslatok |
| [Biztonsági kezelője](#security-manager) | Kezelheti a biztonsági összetevők, a biztonsági házirendek és a virtuális gépek |
| [Biztonsági olvasó](#security-reader) | A csak a Security Center: javaslatokra és riasztásokra, biztonsági házirendeket, biztonsági állapotok megtekintéséhez, de nem módosíthatnak semmit nézet megtekintéséhez |
| [Webhely-helyreállítási közreműködő](#site-recovery-contributor) | Összes helyreállítás felügyeleti műveletek, kivéve a Recovery Services-tároló létrehozása és hozzárendelése hozzáférési jogosultsága ahhoz, hogy más felhasználók is kezelése |
| [Webhely-helyreállítási operátor](#site-recovery-operator) | Feladatátvétel és a feladat-visszavétel is, de nem más Site Recovery felügyeleti műveleteket, illetve más felhasználók számára a hozzáférés hozzárendelése |
| [Webhely-helyreállítási olvasó](#site-recovery-reader) | Recovery Services-tárolónak a Site Recovery állapot figyelheti és támogatási jegyek előléptetése |
| [SQL DB Contributor](#sql-db-contributor) | SQL-adatbázisok, de nem a biztonsági házirendek kezeléséhez |
| [SQL Security Manager](#sql-security-manager) | Kezelheti az SQL Server-kiszolgálók és adatbázisok biztonsági házirendek |
| [SQL Server közreműködő](#sql-server-contributor) | SQL Server-kiszolgálók és adatbázisok, de nem a biztonsági házirendek kezeléséhez |
| [Tárolási fiók közreműködői](#storage-account-contributor) | Igen storage-fiókok kezeléséhez, de nem érhető el. |
| [Tárolási fiók kulcs operátor szolgáltatás szerepkör](#storage-account-key-operator-service-role) | A tárfiók kulcsának operátorai jogosultak a tárfiókokhoz tartozó kulcsok listázására és újragenerálására |
| [Támogatási kérelem közreműködő](#support-request-contributor) | Hozhat létre, és az előfizetés hatókörből támogatási jegyek kezelése |
| [A TRAFFIC Manager közreműködő](#traffic-manager-contributor) | Lehetővé teszi Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz hozzáférő felhasználók felügyeletét. |
| [Felhasználói hozzáférés adminisztrátora](#user-access-administrator) | Kezelheti a felhasználói hozzáférés az Azure-erőforrások |
| [Virtuális gép rendszergazdai bejelentkezés](#virtual-machine-administrator-login) | – Ezzel a szerepkörrel rendelkező felhasználók eltávolíthatnak-jelentkezzen be egy virtuális gépet a rendszergazda Windows vagy Linux legfelső szintű felhasználói jogosultságokkal. |
| [Virtuális gép közreműködő](#virtual-machine-contributor) | Kezelheti a virtuális gépek, de nem a virtuális hálózati vagy tárolási fiókot, amelyhez csatlakoznak |
| [Virtuális gép felhasználói bejelentkezés](#virtual-machine-user-login) | Ezzel a szerepkörrel rendelkező felhasználók eltávolíthatnak-e a virtuális gépek számára felhasználói. |
| [Webes terv közreműködő](#web-plan-contributor) | Kezelheti a webes tervek |
| [Webhely közreműködő](#website-contributor) | Kezelheti a webhely, de nem a webes terveket, amelyhez csatlakoznak |

Az alábbi táblázatok bemutatják az egyes szerepkörökhöz megadott engedélyeket. Ez magában foglalhatja **műveletek**, amely adjon engedélyeket, és **NotActions**, amely korlátozza azokat.

## <a name="owner"></a>Tulajdonos
Mindent felügyelhetnek, beleértve a hozzáférést

| **Műveletek** |  |
| --- | --- |
| * | Hozzon létre és kezelheti az erőforrásokat bármilyen típusú |

## <a name="contributor"></a>Közreműködő
Hozzáférés kivételével mindent felügyelhetnek

| **Műveletek** |  |
| --- | --- |
| * | Hozzon létre és kezelheti az erőforrásokat bármilyen típusú |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete | Szerepkörök és szerepkör-hozzárendelések nem lehet törölni |
| Microsoft.Authorization/*/Write | Nem hozható létre, szerepkörök és szerepkör-hozzárendelések |
| Microsoft.Authorization/elevateAccess/Action | Felhasználói hozzáférés rendszergazdai szerepkörének megadása a hívónak a bérlői hatókörben |

## <a name="reader"></a>Olvasó
Mindent megtekinthetnek, de nem módosítható

| **Műveletek** |  |
| --- | --- |
| */read | Olvassa el az erőforrásokat bármilyen típusú, kivéve a titkos kulcsok. |

## <a name="api-management-service-contributor"></a>API Management szolgáltatás közreműködője
Az API Management szolgáltatásokat kezelheti

| **Műveletek** |  |
| --- | --- |
| Microsoft.ApiManagement/service/* | Hozzon létre és API-kezelés szolgáltatás kezelése |
| Microsoft.Authorization/*/read | Olvasási engedély |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="api-management-service-operator-role"></a>Az API Management szolgáltatás operátori szerepköre
Az API Management szolgáltatásokat kezelheti

| **Műveletek** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Olvasási API Management szolgáltatáspéldány |
| Microsoft.ApiManagement/service/backup/action | Készítsen biztonsági másolatot API Management szolgáltatást, hogy egy felhasználó által megadott tárfiók a megadott tárolóhoz |
| Microsoft.ApiManagement/service/delete | Az API Management szolgáltatáspéldány törlése |
| Microsoft.ApiManagement/service/managedeployments/action | Változás SKU jegyek; hozzáadni vagy eltávolítani a regionális egy szolgáltatás üzemelő példányainak API Management |
| Microsoft.ApiManagement/service/read | Olvassa el a metaadatokat az API Management szolgáltatáspéldány |
| Microsoft.ApiManagement/service/restore/action | Állítsa vissza egy felhasználó által megadott tárfiók a megadott tároló API Management szolgáltatás |
| Microsoft.ApiManagement/service/updatecertificate/action | Az API Management szolgáltatás SSL-tanúsítvány feltöltése |
| Microsoft.ApiManagement/service/updatehostname/action | Állítsa be, frissíteni vagy távolítsa el az API Management szolgáltatás egyéni tartománynevek |
| Microsoft.ApiManagement/service/write | Az API Management szolgáltatást egy új példányának létrehozása |
| Microsoft.Authorization/*/read | Olvasási engedély |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Felhasználói kulcsok listájának beolvasása |

## <a name="api-management-service-reader-role"></a>Az API Management szolgáltatás olvasói szerepköre
Az API Management szolgáltatásokat kezelheti

| **Műveletek** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Olvasási API Management szolgáltatáspéldány |
| Microsoft.ApiManagement/service/read | Olvassa el a metaadatokat az API Management szolgáltatáspéldány |
| Microsoft.Authorization/*/read | Olvasási engedély |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Felhasználói kulcsok listájának beolvasása |

## <a name="application-insights-component-contributor"></a>Application Insights-összetevők közreműködője
Kezelhet Application Insights-összetevőket

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.Insights/components/* | Hozzon létre és elemzések összetevők kezeléséhez |
| Microsoft.Insights/webtests/* | Létrehozása és kezelése a webes tesztjeinek használatát |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="application-insights-snapshot-debugger"></a>Az Application Insights pillanatkép-hibakeresője
Engedélyt ad a felhasználónak az Application Insights pillanatkép-hibakeresői szolgáltatásainak használatára

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Insights/components/*/read |  |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="automation-job-operator"></a>Az Automation-feladat operátora
Feladatok létrehozása és kezelése Automation-runbookok használatával.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Automation/automationAccounts/jobs/read | Egy Azure Automation-feladat beolvasása |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Egy Azure Automation-feladat folytatása |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Egy Azure Automation-feladat leállítása |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Beolvassa a hibrid forgatókönyv-feldolgozó erőforrások |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Egy Azure Automation-feladatstream beolvasása |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Egy Azure Automation-feladat felfüggesztése |
| Microsoft.Automation/automationAccounts/jobs/write | Egy Azure Automation-feladat létrehozása |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="automation-operator"></a>Automation-operátor
Indítás, Leállítás, felfüggesztése és folytatása feladatok tudni

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Automation/automationAccounts/jobs/read | Olvassa el az automatizálási fiók feladatok |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Az automation-fiók feladat folytatása |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Az automation-fiók feladat leállítása |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Automatizálási fiókot feladat adatfolyamok olvasása |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Az automation-fiók feladat felfüggesztése |
| Microsoft.Automation/automationAccounts/jobs/write | Automatizálási fiókot feladatok írása |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Olvassa el az automation-fiók feladatütemezés |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Olvassa el az automation-fiók feladatütemezés |
| Microsoft.Automation/automationAccounts/linkedWorkspace/read | Lekérdezi a munkaterület az automation-fiókhoz csatolva. |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Beolvassa a hibrid forgatókönyv-feldolgozó erőforrások |
| Microsoft.Automation/automationAccounts/read | Olvassa el az automation-fiók |
| Microsoft.Automation/automationAccounts/runbooks/read | Olvassa el az automation-forgatókönyv |
| Microsoft.Automation/automationAccounts/schedules/read | Olvassa el az automatizálási fiók ütemezése |
| Microsoft.Automation/automationAccounts/schedules/write | Automatizálási fiókot ütemezések írása |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="automation-runbook-operator"></a>Az Automation-runbook operátora
Runbook-tulajdonságok olvasása – a runbook-feladatok létrehozásához.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Automation/automationAccounts/runbooks/read | Egy Azure Automation-runbook beolvasása |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="azure-stack-registration-owner"></a>Azure Stack-regisztráció tulajdonosa
Lehetővé teszi az Azure Stack-regisztrációk kezelését.

| **Műveletek** |  |
| --- | --- |
| Microsoft.AzureStack/registrations/products/listDetails/action | Lekéri a kiterjesztett egy Azure verem piactér megoldás részletei |
| Microsoft.AzureStack/registrations/products/read | Egy Azure verem piactér termék tulajdonságainak beolvasása |
| Microsoft.AzureStack/registrations/read | Egy Azure verem regisztrációs tulajdonságait olvassa be |

## <a name="backup-contributor"></a>Biztonsági mentési közreműködő
Kezelheti az összes biztonságimásolat-felügyeleti műveletek, kivéve a Recovery Services-tároló létrehozása, és adjon hozzáférést a mások számára

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Network/virtualNetworks/read | Olvassa el a virtuális hálózatok |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | A biztonsági mentési felügyeleti művelet eredménye kezelése |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Hozzon létre, és a Recovery Services-tároló biztonsági mentési hálók belül biztonsági mentési tárolók kezelése |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozásához és kezeléséhez |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Biztonsági mentési feladatok exportálása az Excelbe |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Létrehozásához és kezeléséhez az biztonsági mentési felügyeletével kapcsolatos metaadatok |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Létrehozásához és kezeléséhez a biztonságimásolat-felügyeleti műveletek eredményeit |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | Biztonsági szabályzatok létrehozása és kezelése |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Cikkek, amely biztonsági másolat készíthető létrehozása és kezelése |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Hozzon létre és kezelheti a biztonsági mentésben szereplő elemek |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Hozzon létre és cikkek biztonsági mentési tárolók kezelése |
| Microsoft.RecoveryServices/Vaults/certificates/* | Tanúsítványok kapcsolódik a biztonsági mentés a Recovery Services-tároló létrehozásához és felügyeletéhez |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Létrehozásához és kezeléséhez kapcsolódó tároló kiterjesztett adatai |
| Microsoft.RecoveryServices/Vaults/read | Olvassa el a recovery services-tárolók |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Kezelheti a felderítési műveletet beolvasása tárolókat, újonnan létrehozott |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Hozzon létre és regisztrált felhasználók kezelése |
| Microsoft.RecoveryServices/Vaults/usages/* | Létrehozásához és kezeléséhez a Recovery Services-tároló használata |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services beolvasása védett elemek és a védett kiszolgálók összesítések. |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Storage/storageAccounts/read | Olvassa el a storage-fiókok |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | A riasztások lekéri a Recovery services-tároló. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Az exportálási feladat művelet eredményét adja vissza. |
| Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="backup-operator"></a>Biztonsági mentési operátor
Kezelheti az összes biztonságimásolat-felügyeleti műveletek kivételével tárolók, mások biztonsági mentési és amely elérésének létrehozása

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Network/virtualNetworks/read | Olvassa el a virtuális hálózatok |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Olvassa el a biztonsági mentési felügyeleti művelet eredménye |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | Olvasási művelet eredményeit az védelmi-tárolókon |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/backup/action | Igény szerinti biztonsági mentési műveletet végezni a biztonsági mentésben szereplő elemek |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | Végre művelet eredménye olvasási biztonsági másolatba mentett elem |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Olvasási biztonsági másolatba mentett elemek |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | Helyreállítási pont biztonsági másolatban szereplő elem olvasása |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/restore/action | A biztonsági mentésben szereplő elem helyreállítási pontot használ a visszaállítási művelet végrehajtása |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | Biztonsági mentési típusú elem létrehozása |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Olvassa el a biztonsági mentési elemet birtokló tárolók |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozásához és kezeléséhez |
| Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | A feladat megszakítása |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | A feladatművelet eredményét adja vissza. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Minden feladat objektumot ad vissza |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Biztonsági mentési feladatok exportálása az Excelbe |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Olvassa el a biztonsági mentési kezelésével kapcsolatos metaadatok |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Létrehozásához és kezeléséhez a biztonságimásolat-felügyeleti műveletek eredményeit |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A biztonsági mentési házirendek végrehajtott műveletek eredményeit Olvasás |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Olvassa el a biztonsági mentési házirendek |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Cikkek, amely biztonsági másolat készíthető létrehozása és kezelése |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | A védhető elemek listáját adja vissza. |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Olvasási biztonsági másolatba mentett elemek |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Olvasási biztonsági másolatba mentett tárolók okozó biztonsági másolati elemei |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services beolvasása védett elemek és a védett kiszolgálók összesítések. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Olvassa el a kapcsolódó tároló kiterjesztett adatai |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | Kiterjesztett-tároló kapcsolatos adatok |
| Microsoft.RecoveryServices/Vaults/read | Olvassa el a recovery services-tárolók |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Kezelheti a felderítési műveletet beolvasása tárolókat, újonnan létrehozott |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A tároló regisztrált elemek végre olvasási művelet eredményei |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tároló regisztrált elemek olvasása |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A tároló regisztrált cikkek írása |
| Microsoft.RecoveryServices/Vaults/usages/read | Olvassa el a Recovery Services-tároló használata |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Storage/storageAccounts/read | Olvassa el a storage-fiókok |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Védett elem rendelkezés azonnali elem helyreállítása |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Védett elem azonnali elem helyreállítása visszavonása |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | A riasztások lekéri a Recovery services-tároló. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Az exportálási feladat művelet eredményét adja vissza. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
| Microsoft.RecoveryServices/Vaults/certificates/write | A frissítés erőforrás tanúsítvány művelet frissíti az erőforrás/tárolói hitelesítő adatainak tanúsítványa. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="backup-reader"></a>Biztonsági mentési olvasó
A Recovery Services-tároló biztonságimásolat-felügyeleti képes figyelni

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Olvassa el a biztonsági mentési felügyeleti művelet eredménye |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | Olvasási művelet eredményeit az védelmi-tárolókon |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | Végre művelet eredménye olvasási biztonsági másolatba mentett elem |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Olvasási biztonsági másolatba mentett elemek |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Olvassa el a biztonsági mentési elemet birtokló tárolók |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Olvassa el a biztonsági mentési feladatok eredményei |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Olvassa el a biztonsági mentési feladatok |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Biztonsági mentési feladatok exportálása az Excelbe |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Olvassa el a biztonsági mentési kezelésével kapcsolatos metaadatok |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Olvassa el a biztonságimásolat-felügyeleti művelet |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A biztonsági mentési házirendek végrehajtott műveletek eredményeit Olvasás |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Olvassa el a biztonsági mentési házirendek |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Olvasási biztonsági másolatba mentett elemek |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Olvasási biztonsági másolatba mentett tárolók okozó biztonsági másolati elemei |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services beolvasása védett elemek és a védett kiszolgálók összesítések. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Olvassa el a kapcsolódó tároló kiterjesztett adatai |
| Microsoft.RecoveryServices/Vaults/read | Olvassa el a recovery services-tárolók |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Olvassa el a felderítési művelet való beolvasásához használt tárolókat, újonnan létrehozott eredménye |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A tároló regisztrált elemek végre olvasási művelet eredményei |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tároló regisztrált elemek olvasása |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration olvasása |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | A riasztások lekéri a Recovery services-tároló. |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | Védett elemek helyreállítási pontjainak beolvasása. |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Az exportálási feladat művelet eredményét adja vissza. |
| Microsoft.RecoveryServices/Vaults/usages/read | Olvassa el a Recovery Services-tároló használata |

## <a name="billing-reader"></a>Számlázási olvasó
Számlázási vonatkozó összes információt megtekinthetik

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Billing/*/read | Olvassa el a számlázási adatokat |
| Microsoft.Consumption/*/read |  |
| Microsoft.Commerce/*/read |  |
| Microsoft.Management/managementGroups/read | A hitelesített felhasználók listáját a felügyeleti csoportok. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="biztalk-contributor"></a>BizTalk közreműködője
Kezelheti a BizTalk szolgáltatások

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.BizTalkServices/BizTalk/* | Létrehozásához és kezeléséhez BizTalk szolgáltatások |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="cdn-endpoint-contributor"></a>A CDN-végpont közreműködője
Kezelhet CDN-végpontokat, de nem tud hozzáférést adni más felhasználóknak.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/* |  |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="cdn-endpoint-reader"></a>A CDN-végpont olvasója
Megtekintheti a CDN-végpontokat, de nem végezhet módosításokat.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/*/read |  |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="cdn-profile-contributor"></a>A CDN-profil közreműködője
Kezelheti a CDN-profilokat és azok végpontjait, de nem tud hozzáférést adni más felhasználóknak.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/* |  |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="cdn-profile-reader"></a>A CDN-profil olvasója
Megtekintheti a CDN-profilokat és azok végpontjait, de nem végezhet módosításokat.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/*/read |  |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="classic-network-contributor"></a>Virtuális hálózatok hagyományos közreműködője
Kezelheti a klasszikus virtuális hálózatok és foglalt IP-cím

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvasási engedély |
| Microsoft.ClassicNetwork/* | Hozzon létre és klasszikus hálózatok kezelése |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="classic-storage-account-contributor"></a>Hagyományos tárfiók-közreműködő
Kezelheti a klasszikus tárfiókokba

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvasási engedély |
| Microsoft.ClassicStorage/storageAccounts/* | Storage-fiókok létrehozása és kezelése |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="classic-storage-account-key-operator-service-role"></a>A hagyományos tárfiók kulcsának operátora – szolgáltatási szerepkör
A hagyományos tárfiók kulcsának operátorai jogosultak a hagyományos tárfiókokhoz tartozó kulcsok listázására és újragenerálására

| **Műveletek** |  |
| --- | --- |
| Microsoft.ClassicStorage/storageAccounts/listkeys/action | Tárfiókok elérési kulcsainak listázása. |
| Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Tárfiók meglévő elérési kulcsainak újragenerálása. |

## <a name="classic-virtual-machine-contributor"></a>Virtuális gépek hagyományos közreműködője
Kezelheti a klasszikus virtuális gépeket, de nem a virtuális hálózati vagy tárolási fiókot, amelyhez csatlakoznak

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvasási engedély |
| Microsoft.ClassicCompute/domainNames/* | Hozzon létre, és a hagyományos számítási tartománynevek kezelése |
| Microsoft.ClassicCompute/virtualMachines/* | Virtuális gépek létrehozására és kezelésére |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | Csatlakoztassa a hálózati biztonsági csoportok |
| Microsoft.ClassicNetwork/reservedIps/link/action | Hivatkozás a foglalt IP-cím |
| Microsoft.ClassicNetwork/reservedIps/read | Olvasási fenntartott IP-cím |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Csatlakozás virtuális hálózatok |
| Microsoft.ClassicNetwork/virtualNetworks/read | Olvassa el a virtuális hálózatok |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Olvassa el a tárolólemezek fiók |
| Microsoft.ClassicStorage/storageAccounts/images/read | Olvassa el a tárolási fiók lemezképek |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Tárfiókkulcsok listázása |
| Microsoft.ClassicStorage/storageAccounts/read | Olvassa el a klasszikus tárfiókokba |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL-adatbázisok közreműködője
Kezelheti a ClearDB MySQL-adatbázisok

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
| successbricks.cleardb/Databases/* | Hozzon létre és kezelheti a ClearDB MySQL-adatbázisok |

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB-fiók olvasói szerepköre
Azure Cosmos DB fiókadatokhoz el tud olvasni. Lásd: [DocumentDB-fiók közreműködői](#documentdb-account-contributor) Azure Cosmos DB kezelésére.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvasási szerepkörökhöz és szerepkör-hozzárendelések elolvashatják a minden felhasználó számára megadott engedélyek |
| Microsoft.DocumentDB/*/read | Olvassa el a gyűjtemény |
| Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Olvassa el a csak olvasható kulcsok panelen |
| Microsoft.Insights/MetricDefinitions/read | Olvassa el a metrikák definíciók |
| Microsoft.Insights/Metrics/read | Olvassa el a fiók metrikák |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="data-factory-contributor"></a>Data Factory közreműködője
Létrehozása és kezelése az adat-előállítók és gyermekerőforrásait rajtuk.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
| Microsoft.DataFactory/dataFactories/* | Létrehozása és kezelése az adat-előállítók és gyermekerőforrásait rajtuk. |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics-fejlesztő
Lehetővé teszi a saját feladatok elküldését, figyelését és kezelését, de nem tud létrehozni vagy törölni Data Lake Analytics-fiókokat.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.BigAnalytics/accounts/* |  |
| Microsoft.DataLakeAnalytics/accounts/* |  |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

| **NotActions** |  |
| --- | --- |
| Microsoft.BigAnalytics/accounts/Delete |  |
| Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
| Microsoft.BigAnalytics/accounts/Write |  |
| Microsoft.DataLakeAnalytics/accounts/Delete | DataLakeAnalytics fiók törlése. |
| Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Más felhasználók által benyújtott szakítsa engedélyeket. |
| Microsoft.DataLakeAnalytics/accounts/Write | Fiók létrehozása vagy frissítése egy DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Fiók létrehozása vagy frissítése egy csatolt DataLakeStore DataLakeAnalytics fiók. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Megszünteti egy DataLakeStore fiók DataLakeAnalytics-fiókból. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Hozzon létre, vagy egy kapcsolódó tárfiók DataLakeAnalytics fiók módosítása. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | A Storage-fiók egy DataLakeAnalytics fiókból választható. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Hozzon létre, vagy egy tűzfalszabály módosítása. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Tűzfalszabály törlése. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Hozzon létre, vagy egy számítási házirend frissítése. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Számítás-házirend törlése. |

## <a name="devtest-labs-user"></a>DevTest Labs-felhasználó
Minden tekintheti meg és csatlakozni, a start, újraindítás és leállítás virtuális gépek

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
| Microsoft.Compute/availabilitySets/read | Rendelkezésre állási készletek tulajdonságainak olvasása |
| Microsoft.Compute/virtualMachines/*/read | A virtuális gép (VM-méretek futási állapotának, Virtuálisgép-bővítmények, stb.) tulajdonságainak olvasása |
| Microsoft.Compute/virtualMachines/deallocate/action | Virtuális gép felszabadítása |
| Microsoft.Compute/virtualMachines/read | A virtuális gép tulajdonságainak olvasása |
| Microsoft.Compute/virtualMachines/restart/action | Indítsa újra a virtuális gépek |
| Microsoft.Compute/virtualMachines/start/action | Virtuális gépek elindítása |
| Microsoft.DevTestLab/*/read | Labor tulajdonságainak olvasása |
| Microsoft.DevTestLab/labs/createEnvironment/action | Egy tesztlabor környezet létrehozása |
| Microsoft.DevTestLab/labs/claimAnyVm/action | Jogcím egy véletlenszerű claimable virtuális gép a tesztkörnyezetben. |
| Microsoft.DevTestLab/labs/formulas/delete | Képletek törlése |
| Microsoft.DevTestLab/labs/formulas/read | Olvassa el a képletek |
| Microsoft.DevTestLab/labs/formulas/write | Hozzáadása vagy módosítása képletek |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Labor házirendek kiértékelése |
| Microsoft.DevTestLab/labs/virtualMachines/claim/action | Saját tulajdonba vétele meglévő virtuális gépből |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Csatlakozás egy terheléselosztó címét háttérkészletéből |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Csatlakozás a terheléselosztó bejövő NAT-szabály |
| Microsoft.Network/networkInterfaces/*/read | Egy adott hálózati csatoló (például, terheléselosztók, amely a hálózati adapter egy része) tulajdonságainak olvasása |
| Microsoft.Network/networkInterfaces/join/action | A hálózati adaptert egy virtuális gép csatlakoztatása |
| Microsoft.Network/networkInterfaces/read | Olvassa el a hálózati illesztők |
| Microsoft.Network/networkInterfaces/write | Hálózati illesztők írása |
| Microsoft.Network/publicIPAddresses/*/read | A nyilvános IP-cím tulajdonságainak olvasása |
| Microsoft.Network/publicIPAddresses/join/action | Csatlakozás egy nyilvános IP-cím |
| Microsoft.Network/publicIPAddresses/read | Olvassa el a hálózati nyilvános IP-címek |
| Microsoft.Network/virtualNetworks/subnets/join/action | Csatlakozás egy virtuális hálózathoz |
| Microsoft.Resources/deployments/operations/read | Olvassa el a telepítési műveletek |
| Microsoft.Resources/deployments/read | Olvassa el a központi telepítések |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Storage/storageAccounts/listKeys/action | Tárfiókkulcsok listázása |

| **NotActions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/vmSizes/read | Listázza az elérhető méreteket, melyekre a virtuális gép frissíthető |

## <a name="dns-zone-contributor"></a>A DNS-zóna közreműködője
Can manage DNS zones and records.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.Network/dnsZones/* | DNS-zónák és rekordok létrehozása és kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="documentdb-account-contributor"></a>DocumentDB-fiókközreműködő
Felügyelheti az Azure Cosmos DB fiókjait. Azure Cosmos-adatbázis a DocumentDB nevén.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
| Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB fiókok létrehozása és kezelése |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="intelligent-systems-account-contributor"></a>Intelligens rendszerek fiókközreműködője
Intelligens rendszerek fiókok is kezelése

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.IntelligentSystems/accounts/* | Intelligens rendszerek fiókok létrehozása és kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="key-vault-contributor"></a>Key Vault-közreműködő
Lehetővé teszi a kulcstartók kezelését, de ezekhez nem biztosít hozzáférést.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.KeyVault/* |  |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

| **NotActions** |  |
| --- | --- |
| Microsoft.KeyVault/locations/deletedVaults/purge/action | Helyreállíthatóan törölt kulcstartó végleges törlése |
| Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Tesztlabor-létrehozót
Lehetővé teszi a létrehozása, kezelése, a Azure labor fiókok a felügyelt tesztkörnyezeteket törlése.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.ManagedLab/labAccounts/createLab/action | Labor létrehozása a tesztkörnyezeti fiók. |
| Microsoft.ManagedLab/labAccounts/*/read |  |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="log-analytics-contributor"></a>Log Analytics közreműködő
Napló Analytics közreműködői összes figyelési adatot olvashatja és szerkesztése a figyelési beállításokat. Figyelési beállításainak szerkesztése magában foglalja a Virtuálisgép-bővítmény hozzáadása a virtuális gépek; tárfiókkulcsok tudják konfigurálni a naplók az Azure Storage; gyűjtemény olvasása létrehozásáról és konfigurálásáról az Automation-fiók; megoldásokkal; és az összes Azure-erőforrások konfigurálása az Azure diagnostics.

| **Műveletek** |  |
| --- | --- |
| */read | Olvassa el az erőforrásokat bármilyen típusú, kivéve a titkos kulcsok. |
| Microsoft.Automation/automationAccounts/* |  |
| Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Tárfiókok elérési kulcsainak listázása. |
| Microsoft.Compute/virtualMachines/extensions/* |  |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Insights/diagnosticSettings/* | Hoz létre, a frissítéseket, vagy az Analysis Server diagnosztikai beállításának beolvasása |
| Microsoft.OperationalInsights/* |  |
| Microsoft.OperationsManagement/* |  |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
| Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="log-analytics-reader"></a>Log Analytics olvasó
A Log Analytics-olvasó megtekintheti az összes figyelési adatot, és kereshet azokban, valamint megtekintheti a figyelési beállításokat, beleértve az összes Azure-erőforrás Azure-beli diagnosztikájának konfigurációját is.

| **Műveletek** |  |
| --- | --- |
| */read | Olvassa el az erőforrásokat bármilyen típusú, kivéve a titkos kulcsok. |
| Microsoft.OperationalInsights/workspaces/analytics/query/action | A keresés új motor használatával. |
| Microsoft.OperationalInsights/workspaces/search/action | Keresési lekérdezés végrehajtása |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

| **NotActions** |  |
| --- | --- |
| Microsoft.OperationalInsights/workspaces/sharedKeys/read | Lekéri a megosztott kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó. |

## <a name="logic-app-contributor"></a>A logikai alkalmazás közreműködője
Lehetővé teszi logikai alkalmazások kezelését, de ezekhez nem biztosít hozzáférést.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Tárfiókok elérési kulcsainak listázása. |
| Microsoft.ClassicStorage/storageAccounts/read | Tárfiók visszaadása az adott fiókhoz. |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Insights/diagnosticSettings/* | Hoz létre, a frissítéseket, vagy az Analysis Server diagnosztikai beállításának beolvasása |
| Microsoft.Insights/logdefinitions/* | Ez az engedély szükség a felhasználók számára, akiknek tevékenységi naplóit a portálon hozzáférésre van szükségük. Tevékenységnapló napló kategóriák listája. |
| Microsoft.Insights/metricDefinitions/* | Olvassa el a metrikai meghatározásainak (erőforrás elérhető metrika típusok listája). |
| Microsoft.Logic/* | Kezeli a Logic Apps-erőforrásokat. |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Storage/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
| Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
| Microsoft.Web/connectionGateways/* | Hozzon létre és kezeli a kapcsolat átjáró. |
| Microsoft.Web/connections/* | Hozzon létre és kezeli a kapcsolatot. |
| Microsoft.Web/customApis/* | Hozza létre, és egy egyéni API felügyeli. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | Az egy App Service-csomag tulajdonságainak beolvasása |
| Microsoft.Web/sites/functions/listSecrets/action | Lista titkok Web Apps funkciók. |

## <a name="logic-app-operator"></a>Logikai alkalmazás operátora
Lehetővé teszi logikai alkalmazások olvasását, engedélyezését és letiltását.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Insights/alertRules/*/read | Olvasási Insights riasztási szabályok |
| Microsoft.Insights/diagnosticSettings/*/read | A Logic Apps diagnosztikai beállításainak beolvasása |
| Microsoft.Insights/metricDefinitions/*/read | A Logic Apps elérhető metrikai meghatározások beolvasása. |
| Microsoft.Logic/*/read | Beolvassa a Logic Apps-erőforrásokat. |
| Microsoft.Logic/workflows/disable/action | Letiltja a munkafolyamatot. |
| Microsoft.Logic/workflows/enable/action | Engedélyezi a munkafolyamatot. |
| Microsoft.Logic/workflows/validate/action | Érvényesíti a munkafolyamatot. |
| Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
| Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
| Microsoft.Web/connectionGateways/*/read | Olvasása kapcsolat átjárók. |
| Microsoft.Web/connections/*/read | Olvassa el a kapcsolatokat. |
| Microsoft.Web/customApis/*/read | Olvassa el az egyéni API. |
| Microsoft.Web/serverFarms/read | Az egy App Service-csomag tulajdonságainak beolvasása |

## <a name="managed-identity-contributor"></a>Felügyelt identitás közreműködő
Létrehozása, olvasása, frissítése és törlése a felhasználói identitás

| **Műveletek** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="managed-identity-operator"></a>Felügyelt identitás operátor
Olvassa el, és rendelje hozzá a felhasználói identitás

| **Műveletek** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="monitoring-contributor"></a>Közreműködő figyelése
Összes figyelési adatot olvashatja és szerkesztheti a figyelési beállításokat. Lásd még: [Ismerkedés a szerepkörök, engedélyek és biztonsági Azure megfigyelővel](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Műveletek** |  |
| --- | --- |
| */read | Olvassa el az erőforrásokat bármilyen típusú, kivéve a titkos kulcsok. |
| Microsoft.AlertsManagement/alerts/* |  |
| Microsoft.AlertsManagement/alertsSummary/* |  |
| Microsoft.Insights/AlertRules/* | Olvasási, írási és törlési riasztási szabályok. |
| Microsoft.Insights/components/* | Olvasási, írási és törlési Application Insights összetevőinek. |
| Microsoft.Insights/DiagnosticSettings/* | Olvasási, írási és törlési diagnosztikai beállítások. |
| Microsoft.Insights/eventtypes/* | Tevékenységnapló események (felügyeleti események) egy előfizetésben listában. Ezzel az engedéllyel csak a műveletnapló programozott és a portál eléréséhez alkalmazható. |
| Microsoft.Insights/LogDefinitions/* | Ez az engedély szükség a felhasználók számára, akiknek tevékenységi naplóit a portálon hozzáférésre van szükségük. Tevékenységnapló napló kategóriák listája. |
| Microsoft.Insights/MetricDefinitions/* | Olvassa el a metrikai meghatározásainak (erőforrás elérhető metrika típusok listája). |
| Microsoft.Insights/Metrics/* | Egy erőforrás olvasni. |
| Microsoft.Insights/Register/Action | Regisztrálja a Microsoft.Insights szolgáltatót. |
| Microsoft.Insights/webtests/* | Olvasási, írási és törlési Application Insights webalkalmazás-tesztek. |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Olvasási, írási és törlési Naplóelemzési megoldás csomagokat. |
| Microsoft.OperationalInsights/workspaces/savedSearches/* | Olvasási, írási és törlési Naplóelemzési mentett kereséseket. |
| Microsoft.OperationalInsights/workspaces/search/action | Keresse meg a Naplóelemzési munkaterület. |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action | A Naplóelemzési munkaterület kulcsainak listázása. |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Olvasási, írási és törlési Naplóelemzési insight tárolókonfigurációkkal. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
| Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Olvasó figyelése
Az összes figyelési adatokat (metrikákat, naplói, stb.) el tud olvasni. Lásd még: [Ismerkedés a szerepkörök, engedélyek és biztonsági Azure megfigyelővel](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Műveletek** |  |
| --- | --- |
| */read | Olvassa el az erőforrásokat bármilyen típusú, kivéve a titkos kulcsok. |
| Microsoft.OperationalInsights/workspaces/search/action | Log Analytics-adatok keresése |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="network-contributor"></a>Hálózati közreműködő
Kezelheti az összes hálózati erőforrás

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.Network/* | Hozzon létre és hálózatok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="new-relic-apm-account-contributor"></a>New Relic APM-fiókközreműködő
Lehetővé teszi a New Relic Application Performance Management-fiókok és -alkalmazások kezelését, az azokhoz való hozzáférés nélkül.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
| NewRelic.APM/accounts/* |  |

## <a name="redis-cache-contributor"></a>Redis gyorsítótárak közreműködője
Kezelheti a Redis gyorsítótár

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
| Microsoft.Cache/redis/* | Létrehozásához és kezeléséhez a Redis gyorsítótár |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="scheduler-job-collections-contributor"></a>Scheduler szolgáltatás feladatgyűjteményeinek közreműködője
Kezelheti a Scheduler feladatgyűjteményei

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Scheduler/jobcollections/* | Feladat gyűjtemények létrehozásához és kezeléséhez |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="search-service-contributor"></a>Search szolgáltatás közreműködője
Kezelheti a keresési szolgáltatások

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Search/searchServices/* | Hozzon létre és keresési szolgáltatások kezelése |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="security-admin"></a>Biztonsági rendszergazda
A csak a Security Center: is megtekintheti a biztonsági házirendek, biztonsági állapotok megtekintéséhez, szerkesztheti a biztonsági házirendek, a riasztások megtekintése és a javaslatok, hagyja figyelmen kívül a riasztások és javaslatok

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Authorization/policyAssignments/* | Hozzon létre és kezelheti a házirend-hozzárendelések |
| Microsoft.Authorization/policyDefinitions/* | Létrehozásához és kezeléséhez a házirend-definíciók száma |
| Microsoft.Authorization/policySetDefinitions/* | Létrehozásához és kezeléséhez házirend beállítása |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.operationalInsights/workspaces/*/read | Log Analytics-adatok megtekintése |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Security/*/read | Olvasási biztonsági összetevőinek és házirendek |
| Microsoft.Security/locations/alerts/dismiss/action | A biztonsági riasztások elvetése |
| Microsoft.Security/locations/tasks/dismiss/action | Biztonsági ajánlás olyan környezetekben elvetése |
| Microsoft.Security/policies/write | A biztonsági házirend frissítése |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="security-manager"></a>Biztonságkezelő
Kezelheti a biztonsági összetevők, a biztonsági házirendek és a virtuális gépek

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.ClassicCompute/*/read | Olvassa el a konfigurációs adatok klasszikus virtuális gépek |
| Microsoft.ClassicCompute/virtualMachines/*/write | A klasszikus virtuális gépek konfigurációs írása |
| Microsoft.ClassicNetwork/*/read | Olvassa el hagyományos hálózati konfigurációs adatait |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Security/* | Biztonsági összetevők és házirendek létrehozása és kezelése |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="security-reader"></a>Biztonsági olvasó
A csak a Security Center: javaslatokra és riasztásokra, biztonsági házirendeket, biztonsági állapotok megtekintéséhez, de nem módosíthatnak semmit nézet megtekintéséhez

| **Műveletek** |  |
| --- | --- |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.operationalInsights/workspaces/*/read | Log Analytics-adatok megtekintése |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Security/*/read | Olvasási biztonsági összetevőinek és házirendek |

## <a name="site-recovery-contributor"></a>Site Recovery-közreműködő
Összes helyreállítás felügyeleti műveletek, kivéve a Recovery Services-tároló létrehozása és hozzárendelése hozzáférési jogosultsága ahhoz, hogy más felhasználók is kezelése

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.Network/virtualNetworks/read | Olvassa el a virtuális hálózatok |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp egy szolgáltatás által használt belső művelet |
| Microsoft.RecoveryServices/Vaults/certificates/write | Frissíti a tároló hitelesítő adatainak tanúsítványa |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Létrehozásához és kezeléséhez kapcsolódó tároló kiterjesztett adatai |
| Microsoft.RecoveryServices/Vaults/read | Olvasási Recovery Services-tárolók |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Kezelheti a felderítési műveletet beolvasása tárolókat, újonnan létrehozott |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Hozzon létre és regisztrált felhasználók kezelése |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Replikációs riasztási beállításainak létrehozása vagy frissítése |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Replikációs események olvasása |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | Hozzon létre és kezelheti a replikációs hálók |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Fájlreplikációs feladatok létrehozásához és kezeléséhez |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | Replikációs szabályzatok létrehozása és kezelése |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Hozzon létre és helyreállítási tervek kezelése |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Létrehozásához és kezeléséhez tárolási konfigurációt a Recovery Services-tároló |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Olvasási Recovery Services-tároló jogkivonat adatai |
| Microsoft.RecoveryServices/Vaults/usages/read | A Recovery Services-tároló használati adatainak beolvasása |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló Token művelet alkalmazható tároló szintű háttérbeli műveletek tároló jogkivonatának beszerzéséhez. |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | A Recovery services-tároló a riasztások olvasása |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration olvasása | Olvasási helyreállítási tároló értesítések konfigurálása |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Storage/storageAccounts/read | Olvassa el a storage-fiókok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="site-recovery-operator"></a>Site Recovery-operátor
Feladatátvétel és a feladat-visszavétel is, de nem más Site Recovery felügyeleti műveleteket, illetve más felhasználók számára a hozzáférés hozzárendelése

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.Network/virtualNetworks/read | Olvassa el a virtuális hálózatok |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp egy szolgáltatás által használt belső művelet |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Olvassa el a kapcsolódó tároló kiterjesztett adatai |
| Microsoft.RecoveryServices/Vaults/read | Olvasási Recovery Services-tárolók |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Kezelheti a felderítési műveletet beolvasása tárolókat, újonnan létrehozott |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Olvassa el a művelet állapotát és elküldött művelet eredménye |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Tárolók regisztrált erőforrás olvasása |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Olvassa el a replikációs riasztási beállítások |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Replikációs események olvasása |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | A hálók egységességének ellenőrzése |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Olvassa el a replikációs hálók |
| Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Társítsa a replikációs átjáró újból |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Replikációs háló tanúsítványának megújítása |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Olvassa el a replikációs háló hálózatok |
| ReplicationNetworks/replicationNetworkMappings/olvasás Microsoft.RecoveryServices/vaults/replicationFabrics/ | Olvassa el a replikációs háló hálózatleképezés |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers olvasása | Olvassa el a védelmi tároló |
| ReplicationProtectionContainers/replicationProtectableItems/olvasás Microsoft.RecoveryServices/vaults/replicationFabrics/ | Minden védhető elemek listájának beolvasása |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Helyreállítási pont alkalmazása |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Feladatátvétel véglegesítésének |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Tervezett feladatátvétel |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Minden védett elemek listájának beolvasása |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | A rendelkezésre álló helyreállítási pontok listájának beolvasása |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Javítás replikáció |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/reProtect/action | Indítsa el újra a védett elem védelmét |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/action | Indítsa el a védett elem feladatátvételi tesztje |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Teszt feladatátadás kitakarítását |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Feladatátvétel |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Frissítse a mobilitási szolgáltatás |
| ReplicationProtectionContainers/replicationProtectionContainerMappings/olvasás Microsoft.RecoveryServices/vaults/replicationFabrics/ | Olvassa el a védelmi tároló hozzárendelések |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders olvasása | Olvasási Recovery Services-szolgáltatók |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/action | Frissítse a Recovery Services-szolgáltató |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications olvasása | Olvassa el a replikációs hálók tartozó tárhelybesorolások |
| ReplicationStorageClassifications/replicationStorageClassificationMappings/olvasás Microsoft.RecoveryServices/vaults/replicationFabrics/ | Olvassa el a tárolási besorolás hozzárendelések |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Olvassa el a vCenter-információ regisztrálva |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Fájlreplikációs feladatok létrehozásához és kezeléséhez |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Olvassa el a replikációs házirendhez |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | A helyreállításhoz szükséges feladatátvételi feladatátvételi véglegesítése |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | A helyreállítási terv feladatátvételének indítása |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | A helyreállítási tervek olvasása |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Indítsa el újra a helyreállítási terv védelme |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Indítsa el a helyreállítási terv teszt feladatátvétele |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Indítsa el a helyreállítási terv feladatátvételi teszt karbantartása |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Indítsa el a helyreállítási terv nem tervezett feladatátvétel |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | A Recovery services-tároló a riasztások olvasása |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration olvasása | Olvasási helyreállítási tároló értesítések konfigurálása |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Olvassa el a tárolási konfigurációt a Recovery Services-tároló |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Olvasási Recovery Services-tároló jogkivonat adatai |
| Microsoft.RecoveryServices/Vaults/usages/read | A Recovery Services-tároló használati adatainak beolvasása |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló Token művelet alkalmazható tároló szintű háttérbeli műveletek tároló jogkivonatának beszerzéséhez. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Storage/storageAccounts/read | Olvassa el a storage-fiókok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="site-recovery-reader"></a>Site Recovery-olvasó
Recovery Services-tárolónak a Site Recovery állapot figyelheti és támogatási jegyek előléptetése

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Olvassa el a kapcsolódó tároló kiterjesztett adatai |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | A Recovery services-tároló a riasztások olvasása |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration olvasása | Olvasási helyreállítási tároló értesítések konfigurálása |
| Microsoft.RecoveryServices/Vaults/read | Olvasási Recovery Services-tárolók |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Kezelheti a felderítési műveletet beolvasása tárolókat, újonnan létrehozott |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Olvassa el a művelet állapotát és elküldött művelet eredménye |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Tárolók regisztrált erőforrás olvasása |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Olvassa el a replikációs riasztási beállítások |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Replikációs események olvasása |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Olvassa el a replikációs hálók |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Olvassa el a replikációs háló hálózatok |
| ReplicationNetworks/replicationNetworkMappings/olvasás Microsoft.RecoveryServices/vaults/replicationFabrics/ | Olvassa el a replikációs háló hálózatleképezés |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers olvasása | Olvassa el a védelmi tároló |
| ReplicationProtectionContainers/replicationProtectableItems/olvasás Microsoft.RecoveryServices/vaults/replicationFabrics/ | Minden védhető elemek listájának beolvasása |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Minden védett elemek listájának beolvasása |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | A rendelkezésre álló helyreállítási pontok listájának beolvasása |
| ReplicationProtectionContainers/replicationProtectionContainerMappings/olvasás Microsoft.RecoveryServices/vaults/replicationFabrics/ | Olvassa el a védelmi tároló hozzárendelések |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders olvasása | Olvasási Recovery Services-szolgáltatók |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications olvasása | Olvassa el a replikációs hálók tartozó tárhelybesorolások |
| ReplicationStorageClassifications/replicationStorageClassificationMappings/olvasás Microsoft.RecoveryServices/vaults/replicationFabrics/ | Olvassa el a tárolási besorolás hozzárendelések |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Olvassa el a vCenter-információ regisztrálva |
| Microsoft.RecoveryServices/vaults/replicationJobs/read | Olvassa el a replikációs feladatok állapotát |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Olvassa el a replikációs házirendhez |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | A helyreállítási tervek olvasása |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Olvassa el a tárolási konfigurációt a Recovery Services-tároló |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Olvasási Recovery Services-tároló jogkivonat adatai |
| Microsoft.RecoveryServices/Vaults/usages/read | A Recovery Services-tároló használati adatainak beolvasása |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló Token művelet alkalmazható tároló szintű háttérbeli műveletek tároló jogkivonatának beszerzéséhez. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="sql-db-contributor"></a>SQL-adatbázisok közreműködője
SQL-adatbázisok, de nem a biztonsági házirendek kezeléséhez

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
| Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/databases/* | Létrehozásához és kezeléséhez az SQL-adatbázisok |
| Microsoft.Sql/servers/read | Read SQL Servers |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Naplózási házirendek nem szerkeszthető. |
| Microsoft.Sql/servers/databases/auditingSettings/* | Naplózási beállítások nem szerkeszthetők. |
| Microsoft.Sql/servers/databases/auditRecords/read | Nem olvasható az auditálási rekordok |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Szabályzatok nem szerkeszthető. |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Adatmaszkolási házirendek nem szerkeszthető. |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Biztonsági riasztások házirendek nem szerkeszthető. |
| Microsoft.Sql/servers/databases/securityMetrics/* | Biztonsági metrikák nem szerkeszthető. |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>SQL-biztonságkezelő
Kezelheti az SQL Server-kiszolgálók és adatbázisok biztonsági házirendek

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el a Microsoft engedélyezési |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Alhálózathoz csatlakozik az erőforrás, például a tárfiók, vagy az SQL-adatbázis. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Sql/servers/auditingPolicies/* | SQL server naplózási szabályzatok létrehozása és kezelése |
| Microsoft.Sql/servers/auditingSettings/* | Létrehozásához és kezeléséhez az SQL server a naplózási beállítás |
| Microsoft.Sql/servers/databases/auditingPolicies/* | SQL server adatbázis naplózási szabályzatok létrehozása és kezelése |
| Microsoft.Sql/servers/databases/auditingSettings/* | Hozzon létre és SQL server-adatbázis naplózási beállításainak kezelése |
| Microsoft.Sql/servers/databases/auditRecords/read | Olvasási auditálási rekordok |
| Microsoft.Sql/servers/databases/connectionPolicies/* | SQL server adatbázis kapcsolati szabályzatok létrehozása és kezelése |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Hozzon létre és SQL server adatbázis adatmaszkolási házirendek kezelése |
| Microsoft.Sql/servers/databases/read | Olvassa el az SQL-adatbázisok |
| Microsoft.Sql/servers/databases/schemas/read | Olvassa el az SQL server adatbázis sémák |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Olvassa el az SQL server adatbázis táblaoszlopok |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/read | Olvassa el az SQL server adatbázistáblák |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL server adatbázis biztonsági riasztási szabályzatok létrehozása és kezelése |
| Microsoft.Sql/servers/databases/securityMetrics/* | Létrehozásához és kezeléséhez az SQL server-adatbázis biztonsági metrikák |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/firewallRules/* |  |
| Microsoft.Sql/servers/read | Read SQL Servers |
| Microsoft.Sql/servers/securityAlertPolicies/* | SQL server biztonsági riasztás szabályzatok létrehozása és kezelése |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="sql-server-contributor"></a>SQL Server közreműködője
SQL Server-kiszolgálók és adatbázisok, de nem a biztonsági házirendek kezeléséhez

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/* | Hozzon létre és SQL-kiszolgálók kezelése |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* | SQL server naplózási házirendek nem szerkeszthető. |
| Microsoft.Sql/servers/auditingSettings/* | SQL server naplózási beállítások nem szerkeszthetők. |
| Microsoft.Sql/servers/databases/auditingPolicies/* | SQL server adatbázis naplózási házirendek nem szerkeszthető. |
| Microsoft.Sql/servers/databases/auditingSettings/* | Nem lehet SQL server-adatbázis naplózási beállításainak szerkesztése |
| Microsoft.Sql/servers/databases/auditRecords/read | Nem olvasható az auditálási rekordok |
| Microsoft.Sql/servers/databases/connectionPolicies/* | SQL server adatbázis kapcsolati házirendek nem szerkeszthető. |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL server adatbázis adatmaszkolási házirendek nem szerkeszthető. |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL server adatbázis biztonsági riasztási házirendek nem szerkeszthető. |
| Microsoft.Sql/servers/databases/securityMetrics/* | SQL server-adatbázis biztonsági metrikák nem szerkeszthető. |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/securityAlertPolicies/* | SQL server biztonsági riasztás házirendek nem szerkeszthető. |

## <a name="storage-account-contributor"></a>Tárfiók-közreműködő
Igen storage-fiókok kezeléséhez, de nem érhető el.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el az összes engedélyezési |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Insights/diagnosticSettings/* | Diagnosztikai beállítások kezelése |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Alhálózathoz csatlakozik az erőforrás, például a tárfiók, vagy az SQL-adatbázis. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Storage/storageAccounts/* | Storage-fiókok létrehozása és kezelése |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="storage-account-key-operator-service-role"></a>A tárfiók kulcsának operátora – szolgáltatási szerepkör
A tárfiók kulcsának operátorai jogosultak a tárfiókokhoz tartozó kulcsok listázására és újragenerálására

| **Műveletek** |  |
| --- | --- |
| Microsoft.Storage/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
| Microsoft.Storage/storageAccounts/regeneratekey/action | A megadott tárfiók hozzáférési kulcsainak újragenerálása. |

## <a name="support-request-contributor"></a>Támogatáskérési közreműködő
Hozhat létre, és az előfizetés hatókörből támogatási jegyek kezelése

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvasási engedély |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="traffic-manager-contributor"></a>Traffic Manager-közreműködő
Lehetővé teszi Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz hozzáférő felhasználók felügyeletét.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Network/trafficManagerProfiles/* |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="user-access-administrator"></a>Felhasználói hozzáférés rendszergazdája
Kezelheti a felhasználói hozzáférés az Azure-erőforrások

| **Műveletek** |  |
| --- | --- |
| */read | Olvassa el az erőforrásokat bármilyen típusú, kivéve a titkos kulcsok. |
| Microsoft.Authorization/* | Engedélyezési kezelése |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="virtual-machine-administrator-login"></a>Virtuális gép rendszergazdai bejelentkezés
-   Ezzel a szerepkörrel rendelkező felhasználók eltávolíthatnak-jelentkezzen be a virtuális gépek a rendszergazda Windows vagy Linux legfelső szintű felhasználói jogosultságokkal.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="virtual-machine-contributor"></a>Virtuális gépek közreműködője
Kezelheti a virtuális gépek, de nem a virtuális hálózati vagy tárolási fiókot, amelyhez csatlakoznak

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvasási engedély |
| Microsoft.Compute/availabilitySets/* | Hozzon létre és számítási rendelkezésre állási csoportok kezelése |
| Microsoft.Compute/locations/* | Hozzon létre és számítási helyek kezelése |
| Microsoft.Compute/virtualMachines/* | Virtuális gépek létrehozására és kezelésére |
| Microsoft.Compute/virtualMachineScaleSets/* | Hozzon létre és virtuálisgép-méretezési csoportok kezelése |
| Microsoft.DevTestLab/schedules/* |  |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Csatlakozás hálózati alkalmazás átjáró háttércímkészletek |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Csatlakozás a load balancer háttércímkészletek |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Csatlakozás a terheléselosztó bejövő forgalmat kezelő NAT-készletek |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Csatlakozás a terheléselosztó bejövő NAT-szabályok |
| Microsoft.Network/loadBalancers/probes/join/action | Lehetővé teszi, hogy a terheléselosztó mintavételt használatával. Például, az engedély healthProbe tulajdonság a Virtuálisgép-méretezési készlet is hivatkozhatnak a mintavételi. |
| Microsoft.Network/loadBalancers/read | Olvassa el a terheléselosztók |
| Microsoft.Network/locations/* | Hozzon létre és kezelheti a hálózati helyek |
| Microsoft.Network/networkInterfaces/* | Hozzon létre és kezelheti a hálózati adapterek |
| Microsoft.Network/networkSecurityGroups/join/action | Csatlakoztassa a hálózati biztonsági csoportok |
| Microsoft.Network/networkSecurityGroups/read | Olvassa el a hálózati biztonsági csoportok |
| Microsoft.Network/publicIPAddresses/join/action | Csatlakozás hálózati nyilvános IP-címek |
| Microsoft.Network/publicIPAddresses/read | Olvassa el a hálózati nyilvános IP-címek |
| Microsoft.Network/virtualNetworks/read | Olvassa el a virtuális hálózatok |
| Microsoft.Network/virtualNetworks/subnets/join/action | Csatlakozás a virtuális alhálózatok |
| Microsoft.RecoveryServices/locations/* |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/*/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | A védett elem kapcsolatobjektum-adatait adja vissza |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | Biztonsági mentési védett típusú elem létrehozása |
| Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | A biztonságimásolat-készítő védelmi leképezés létrehozásához |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Visszaadja az összes védelmi házirend |
| Microsoft.RecoveryServices/Vaults/backupPolicies/write | Védelmi házirend létrehozása |
| Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum |
| Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
| Microsoft.RecoveryServices/Vaults/write | A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Storage/storageAccounts/listKeys/action | Tárfiókkulcsok listázása |
| Microsoft.Storage/storageAccounts/read | Olvassa el a storage-fiókok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="virtual-machine-user-login"></a>Virtuális gép felhasználói bejelentkezés
Ezzel a szerepkörrel rendelkező felhasználók eltávolíthatnak-e a virtuális gépek számára felhasználói.

| **Műveletek** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="web-plan-contributor"></a>Webes tarifacsomagok közreműködője
Kezelheti a webes tervek

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvasási engedély |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
| Microsoft.Web/serverFarms/* | Hozzon létre, és a kiszolgálófarmok kezelése |

## <a name="website-contributor"></a>Webhelyek közreműködője
Kezelheti a webhely, de nem a webes terveket, amelyhez csatlakoznak

| **Műveletek** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Olvasási engedély |
| Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
| Microsoft.Insights/components/* | Hozzon létre és elemzések összetevők kezeléséhez |
| Microsoft.ResourceHealth/availabilityStatuses/read | Az erőforrások állapotának olvasása |
| Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
| Microsoft.Resources/subscriptions/resourceGroups/read | Olvassa el az erőforráscsoport-sablonok |
| Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
| Microsoft.Web/certificates/* | Webhely tanúsítványok létrehozásához és felügyeletéhez |
| Microsoft.Web/listSitesAssignedToHostName/read | Állomásnév rendelt hely olvasása |
| Microsoft.Web/serverFarms/join/action | Csatlakozás kiszolgálófarmok |
| Microsoft.Web/serverFarms/read | Olvassa el a kiszolgálófarmok |
| Microsoft.Web/sites/* | Létrehozása és kezelése (webhely létrehozása is írási engedélyeket igényel a társított App Service-csomag) webhelyek |

## <a name="see-also"></a>Lásd még
* [Szerepköralapú hozzáférés-vezérlés](role-based-access-control-configure.md): az RBAC első lépései az Azure portálon.
* [Egyéni szerepkörök az Azure RBAC](role-based-access-control-custom-roles.md): megtudhatja, hogyan hozzon létre egyéni szerepkörök az access igényeihez.
* [Access módosítási előzményeit jelentés létrehozása](role-based-access-control-access-change-history-report.md): nyomon követjük, hogy az RBAC más szerepkörök hozzárendeléséről.
* [Szerepköralapú hozzáférés-vezérlés hibaelhárítási](role-based-access-control-troubleshooting.md): kapcsolatos gyakori hibák elhárítására vonatkozó javaslatok beolvasása.
* [Engedélyek az Azure Security Centerben](../security-center/security-center-permissions.md)
