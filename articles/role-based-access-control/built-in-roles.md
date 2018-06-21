---
title: Beépített szerepkörök az Azure-ban |} Microsoft Docs
description: A beépített szerepkörök az Azure szerepköralapú hozzáférés-vezérlés (RBAC) ismerteti. A műveletek, notActions, dataActions és notDataActions sorolja fel.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/06/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 861b4ca360ef3fb9bc752d79009570ee2cfc9ade
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294496"
---
# <a name="built-in-roles-in-azure"></a>Beépített szerepkörök az Azure-ban
[Szerepköralapú hozzáférés-vezérlést (RBAC)](overview.md) rendelkezik, amelyeket hozzárendelhet a felhasználók, csoportok és szolgáltatásnevekről számos beépített szerepkör-definíciók. Szerepkör-hozzárendelések, amelyek az Ön Azure-ban erőforrásokhoz való hozzáférés szabályozása. Ha a beépített szerepkörök nem felelnek meg a szervezet igényeinek, létrehozhat saját [egyéni szerepkörök](custom-roles.md).

A beépített szerepkörök mindig fejlesztik vannak. A legújabb szerepkör-definíciók használatához [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) vagy [az szerepkör-definíció lista](/cli/azure/role/definition#az-role-definition-list).

## <a name="built-in-role-descriptions"></a>Beépített szerepkör leírása
A következő táblázat a beépített szerepkörök rövid leírása. Kattintson a szerepkör nevét, a lista `actions`, `notActions`, `dataActions`, és `notDataActions` az egyes szerepkörökhöz.


| Beépített szerepkör | Leírás |
| --- | --- |
| [Tulajdonos](#owner) | Lehetővé teszi az összes funkció kezelését, beleértve az erőforrásokhoz való hozzáférést is. |
| [Közreműködő](#contributor) | Lehetővé teszi az összes funkció kezelését, kivéve az erőforrásokhoz való hozzáférést. |
| [olvasó](#reader) | Lehetővé teszi, hogy mindent megtekinthessen, de módosításokat nem hajthat végre. |
| [AcrImageSigner](#acrimagesigner) | ACR-lemezképaláíró |
| [AcrQuarantineReader](#acrquarantinereader) | ACR-karanténadatolvasó |
| [AcrQuarantineWriter](#acrquarantinewriter) | ACR-karanténadatíró |
| [API Management szolgáltatás közreműködő](#api-management-service-contributor) | Szolgáltatás és az API-k kezelhetők |
| [API Management szolgáltatást üzemeltető szerepkör](#api-management-service-operator-role) | Szolgáltatásokat kezelhet, de API-kat nem |
| [API Management szolgáltatás olvasó szerepkört](#api-management-service-reader-role) | Írásvédett hozzáférés a szolgáltatáshoz és az API-khoz |
| [Application Insights-összetevővel kapcsolatos közreműködői](#application-insights-component-contributor) | Kezelhet Application Insights-összetevőket |
| [Application Insights pillanatkép hibakereső](#application-insights-snapshot-debugger) | Engedélyt ad a felhasználónak az Application Insights Snapshot Debugger szolgáltatásainak használatára |
| [Automation-feladat operátor](#automation-job-operator) | Feladatok létrehozása és kezelése Automation-runbookok használatával. |
| [Automatizálási operátor](#automation-operator) | Az Automation-operátorok elindíthatnak, leállíthatnak, felfüggeszthetnek és folytathatnak feladatokat |
| [Automation-Runbook kezelő](#automation-runbook-operator) | Runbook-tulajdonságok olvasása – a runbook-feladatok létrehozásához. |
| [Az Azure verem regisztrációs tulajdonosa](#azure-stack-registration-owner) | Lehetővé teszi az Azure Stack-regisztrációk kezelését. |
| [Biztonsági mentési közreműködő](#backup-contributor) | Lehetővé teszi a biztonsági mentési szolgáltatás felügyeletét, de nem hozhat létre tárolókat, és nem adhat hozzáférést másoknak |
| [Biztonságimásolat-felelős](#backup-operator) | Lehetővé teszi a biztonsági mentési szolgáltatások felügyeletét, kivéve a biztonsági másolatok eltávolítását, tárolók létrehozását és a másoknak való hozzáférés megadását |
| [Biztonsági mentési olvasó](#backup-reader) | Megtekintheti a biztonsági mentési szolgáltatásokat, de nem végezhet módosításokat |
| [Számlázási olvasó](#billing-reader) | Olvasási hozzáférést biztosít a számlázási adatokat |
| [BizTalk közreműködő](#biztalk-contributor) | Lehetővé teszi a BizTalk-szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. |
| [CDN-végpont közreműködő](#cdn-endpoint-contributor) | Kezelhet CDN-végpontokat, de nem tud hozzáférést adni más felhasználóknak. |
| [CDN-végpont olvasó](#cdn-endpoint-reader) | Megtekintheti a CDN-végpontokat, de nem végezhet módosításokat. |
| [CDN-profil közreműködő](#cdn-profile-contributor) | Kezelheti a CDN-profilokat és azok végpontjait, de nem tud hozzáférést adni más felhasználóknak. |
| [CDN-profil olvasó](#cdn-profile-reader) | Megtekintheti a CDN-profilokat és azok végpontjait, de nem végezhet módosításokat. |
| [Klasszikus hálózati közreműködő](#classic-network-contributor) | Lehetővé teszi a hagyományos hálózatok kezelését, az azokhoz való hozzáférés nélkül. |
| [Hagyományos tárolási fiók közreműködői](#classic-storage-account-contributor) | Lehetővé teszi a hagyományos tárfiókok kezelését, de ezekhez nem biztosít hozzáférést. |
| [Hagyományos tárolási fiók kulcs operátor szolgáltatás szerepkör](#classic-storage-account-key-operator-service-role) | A hagyományos tárfiók kulcsának operátorai jogosultak a hagyományos tárfiókokhoz tartozó kulcsok listázására és újragenerálására |
| [Klasszikus virtuális gép közreműködő](#classic-virtual-machine-contributor) | Lehetővé teszi a hagyományos virtuális gépek kezelését, de ezekhez nem biztosít hozzáférést, és nem teszi lehetővé a virtuális gépekhez hozzárendelt virtuális hálózatok és tárfiókok elérését sem. |
| [ClearDB MySQL DB Contributor](#cleardb-mysql-db-contributor) | Lehetővé teszi a ClearDB MySQL-adatbázisok kezelését, az azokhoz való hozzáférés nélkül. |
| [A cosmos DB fiók olvasó szerepkört](#cosmos-db-account-reader-role) | Azure Cosmos DB fiókadatokhoz el tud olvasni. Lásd: [DocumentDB-fiók közreműködői](#documentdb-account-contributor) Azure Cosmos DB kezelésére. |
| [Data Factory közreműködő](#data-factory-contributor) | Létrehozása és kezelése az adat-előállítók, valamint a gyermekszintű erőforrása rajtuk. |
| [Data Lake Analytics-fejlesztőknek](#data-lake-analytics-developer) | Lehetővé teszi a saját feladatok elküldését, figyelését és kezelését, de nem tud létrehozni vagy törölni Data Lake Analytics-fiókokat. |
| [Adatok Purger](#data-purger) | Véglegesen törölheti az elemzési adatokat |
| [DevTest Labs felhasználó](#devtest-labs-user) | Csatlakozás esetén megadható, hogy kezdő, újraindítása és leállítása a virtuális gépek az Azure DevTest Labs szolgáltatásban. |
| [DNS-zóna közreműködő](#dns-zone-contributor) | Lehetővé teszi az Azure DNS-beli DNS-zónák és rekordkészletek kezelését, de nem teszi lehetővé az azokhoz hozzáférő felhasználók felügyeletét. |
| [A DocumentDB-fiók közreműködői](#documentdb-account-contributor) | Felügyelheti az Azure Cosmos DB fiókjait. Azure Cosmos-adatbázis a DocumentDB nevén. |
| [Intelligens rendszerek fiók közreműködői](#intelligent-systems-account-contributor) | Lehetővé teszi az intelligens rendszerek fiókjainak kezelését, az azokhoz való hozzáférés nélkül. |
| [Kulcstároló közreműködő](#key-vault-contributor) | Lehetővé teszi a kulcstartók kezelését, de ezekhez nem biztosít hozzáférést. |
| [Lab Creator](#lab-creator) | Felügyelt tesztkörnyezetek az Azure Lab-fiókokban való létrehozását, kezelését és törlését teszi lehetővé. |
| [Napló Analytics közreműködő](#log-analytics-contributor) | Napló Analytics közreműködői összes figyelési adatot olvashatja és szerkesztése a figyelési beállításokat. Figyelési beállításainak szerkesztése magában foglalja a Virtuálisgép-bővítmény hozzáadása a virtuális gépek; tárfiókkulcsok tudják konfigurálni a naplók az Azure Storage; gyűjtemény olvasása létrehozásáról és konfigurálásáról az Automation-fiók; megoldásokkal; és az összes Azure-erőforrások konfigurálása az Azure diagnostics. |
| [Napló Analytics olvasó](#log-analytics-reader) | A Log Analytics-olvasó megtekintheti az összes figyelési adatot, és kereshet azokban, valamint megtekintheti a figyelési beállításokat, beleértve az összes Azure-erőforrás Azure-beli diagnosztikájának konfigurációját is. |
| [Logic App közreműködő](#logic-app-contributor) | Lehetővé teszi logikai alkalmazások kezelését, de ezekhez nem biztosít hozzáférést. |
| [Logic App operátor](#logic-app-operator) | Lehetővé teszi logikai alkalmazások olvasását, engedélyezését és letiltását. |
| [Felügyelt identitás közreműködő](#managed-identity-contributor) | A felhasználóhoz hozzárendelt identitás létrehozása, olvasása, frissítése és törlése |
| [Felügyelt identitás operátor](#managed-identity-operator) | A felhasználóhoz hozzárendelt identitás olvasása és hozzárendelése |
| [A közreműködői figyelése](#monitoring-contributor) | Összes figyelési adatot olvashatja és szerkesztheti a figyelési beállításokat. Lásd még: [Ismerkedés a szerepkörök, engedélyek és biztonsági Azure megfigyelővel](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Figyelési olvasó](#monitoring-reader) | Az összes figyelési adatokat (metrikákat, naplói, stb.) el tud olvasni. Lásd még: [Ismerkedés a szerepkörök, engedélyek és biztonsági Azure megfigyelővel](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Hálózati közreműködő](#network-contributor) | Lehetővé teszi a hálózatok kezelését, az azokhoz való hozzáférés nélkül. |
| [Új New Relic APM fiók közreműködői](#new-relic-apm-account-contributor) | Lehetővé teszi a New Relic Application Performance Management-fiókok és -alkalmazások kezelését, az azokhoz való hozzáférés nélkül. |
| [Olvasó és az adatok elérése](#reader-and-data-access) | Lehetővé teszi, hogy mindent megtekinthetnek, de nem engedélyezi törlése, vagy hozzon létre egy tárfiókhoz vagy a benne lévő erőforrás. Olvasási és írási hozzáférést tárfiókkulcsok keresztül tárfiókokban tárolt adatokat is engedélyezi. |
| [Redis gyorsítótár közreműködő](#redis-cache-contributor) | Lehetővé teszi a Redis-gyorsítótárak kezelését, az azokhoz való hozzáférés nélkül. |
| [Erőforrás házirend közreműködői (előzetes verzió)](#resource-policy-contributor-preview) | (Előnézet) Erőforrás-szabályzat létrehozására/módosítására, támogatási jegy létrehozására, valamint erőforrások/hierarchia beolvasására jogosultsággal rendelkező, EA-beli visszatöltött felhasználók. |
| [A Feladatütemező feladat gyűjtemények közreműködő](#scheduler-job-collections-contributor) | Lehetővé teszi a Scheduler-feladatgyűjtemények kezelését, az azokhoz való hozzáférés nélkül. |
| [Keresési szolgáltatás közreműködő](#search-service-contributor) | Lehetővé teszi a keresési szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. |
| [Biztonsági rendszergazda](#security-admin) | A csak a Security Center: is megtekintheti a biztonsági házirendek, biztonsági állapotok megtekintéséhez, szerkesztheti a biztonsági házirendek, a riasztások megtekintése és a javaslatok, hagyja figyelmen kívül a riasztások és javaslatok |
| [A biztonságkezelő (örökölt)](#security-manager-legacy) | Ez az örökölt szerepkör. Ehelyett használja a biztonsági rendszergazda |
| [Biztonsági olvasó](#security-reader) | A csak a Security Center: javaslatokra és riasztásokra, biztonsági házirendeket, biztonsági állapotok megtekintéséhez, de nem módosíthatnak semmit nézet megtekintéséhez |
| [Webhely-helyreállítási közreműködő](#site-recovery-contributor) | Lehetővé teszi a Site Recovery szolgáltatás felügyeletét, kivéve a tárolók létrehozását és a szerepkör-hozzárendelést |
| [Webhely-helyreállítási operátor](#site-recovery-operator) | Lehetővé teszi a feladatátvételt és a feladat-visszavételt, de nem biztosít egyéb Site Recovery-beli felügyeleti műveleteket |
| [Webhely-helyreállítási olvasó](#site-recovery-reader) | Lehetővé teszi a Site Recovery állapotának megtekintését, de nem biztosít egyéb felügyeleti műveleteket |
| [SQL DB Contributor](#sql-db-contributor) | Lehetővé teszi SQL-adatbázisok, de nem való hozzáférés kezelését. Emellett a biztonsági házirendjét vagy a szülő SQL Server nem tudja kezelni. |
| [SQL Security Manager](#sql-security-manager) | Lehetőséget nyújt az SQL-kiszolgálók és adatbázisok biztonsági házirendjeinek felügyeletére az azokhoz való hozzáférés nélkül. |
| [SQL Server közreműködő](#sql-server-contributor) | Lehetőséget nyújt az SQL-kiszolgálók és adatbázisok felügyeletére az azokhoz való hozzáférés nélkül. Az adatbázisok biztonsági házirendjeinek felügyeletét nem teszi lehetővé. |
| [Tárfiók-közreműködő](#storage-account-contributor) | Lehetővé teszi tárfiókok kezelését, de ezekhez nem biztosít hozzáférést. |
| [Tárolási fiók kulcs operátor szolgáltatás szerepkör](#storage-account-key-operator-service-role) | A tárfiók kulcsának operátorai jogosultak a tárfiókokhoz tartozó kulcsok listázására és újragenerálására |
| [Tárolási Blob adatok közreműködői (előzetes verzió)](#storage-blob-data-contributor-preview) | Azure Storage-blobtárolók és -adatok olvasási, írási és törlési hozzáférésének engedélyezése |
| [Tárolási Blob Adatolvasó (előzetes verzió)](#storage-blob-data-reader-preview) | Azure Storage-blobtárolók és -adatok olvasási hozzáférésének engedélyezése |
| [Tároló várólista adatok közreműködői (előzetes verzió)](#storage-queue-data-contributor-preview) | Azure Storage-üzenetsorok és üzenetsorbeli üzenetek olvasási, írási és törlési hozzáférésének engedélyezése |
| [Tároló várólista Adatolvasó (előzetes verzió)](#storage-queue-data-reader-preview) | Azure Storage-üzenetsorok és üzenetsorbeli üzenetek olvasási hozzáférésének engedélyezése |
| [Támogatási kérelem közreműködő](#support-request-contributor) | Támogatási kérések létrehozását és kezelését teszi lehetővé |
| [A TRAFFIC Manager közreműködő](#traffic-manager-contributor) | Lehetővé teszi Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz hozzáférő felhasználók felügyeletét. |
| [Felhasználói hozzáférés adminisztrátora](#user-access-administrator) | Lehetővé teszi a Azure-erőforrásokhoz való felhasználói hozzáférés kezelését. |
| [Virtuális gép rendszergazdai bejelentkezés](#virtual-machine-administrator-login) | – Az ezzel a szerepkörrel rendelkező felhasználók képesek bejelentkezni egy virtuális gépre a Windows rendszergazdai vagy a Linux gyökérszintű felhasználójának jogosultságaival. |
| [Virtuális gépek közreműködője](#virtual-machine-contributor) | Lehetővé teszi virtuális gépek, de nem érhető el, és nem a virtuális hálózat vagy kapcsolódó tárfiók kezelését. |
| [Virtuális gép felhasználói bejelentkezés](#virtual-machine-user-login) | Az ezzel a szerepkörrel rendelkező felhasználók képesek bejelentkezni egy virtuális gépre normál felhasználóként. |
| [Webes terv közreműködő](#web-plan-contributor) | Lehetővé teszi a webes tarifacsomagok kezelését, az azokhoz való hozzáférés nélkül. |
| [Webhely közreműködő](#website-contributor) | Lehetővé teszi a webhelyek kezelését (a webes tarifacsomagokét azonban nem), az azokhoz való hozzáférés nélkül. |


## <a name="owner"></a>Tulajdonos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az összes funkció kezelését, beleértve az erőforrásokhoz való hozzáférést is. |
> | **Azonosító** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Műveletek** |  |
> | * | Hozzon létre és kezelheti az erőforrásokat bármilyen típusú |

## <a name="contributor"></a>Közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az összes funkció kezelését, kivéve az erőforrásokhoz való hozzáférést. |
> | **Azonosító** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Műveletek** |  |
> | * | Hozzon létre és kezelheti az erőforrásokat bármilyen típusú |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Szerepkörök és szerepkör-hozzárendelések nem lehet törölni |
> | Microsoft.Authorization/*/Write | Nem hozható létre, szerepkörök és szerepkör-hozzárendelések |
> | Microsoft.Authorization/elevateAccess/Action | Felhasználói hozzáférés rendszergazdai szerepkörének megadása a hívónak a bérlői hatókörben |
> | Microsoft.Blueprint/blueprintAssignments/write |  |
> | Microsoft.Blueprint/blueprintAssignments/delete |  |

## <a name="reader"></a>Olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy mindent megtekinthessen, de módosításokat nem hajthat végre. |
> | **Azonosító** | acdd72a7-3385-48EF-bd42-f606fba81ae7 |
> | **Műveletek** |  |
> | * / olvasása | Olvassa el az erőforrásokat bármilyen típusú, kivéve a titkos kulcsok. |

## <a name="acrimagesigner"></a>ACR-lemezképaláíró
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR-lemezképaláíró |
> | **Azonosító** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |

## <a name="acrquarantinereader"></a>ACR-karanténolvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR-karanténadatolvasó |
> | **Azonosító** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="acrquarantinewriter"></a>ACR-karanténíró
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR-karanténadatíró |
> | **Azonosító** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="api-management-service-contributor"></a>API Management szolgáltatás közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Szolgáltatás és az API-k kezelhetők |
> | **Azonosító** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/* | Hozzon létre és API-kezelés szolgáltatás kezelése |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="api-management-service-operator-role"></a>Az API Management szolgáltatás operátori szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Szolgáltatásokat kezelhet, de API-kat nem |
> | **Azonosító** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/*/read | Olvasási API Management szolgáltatáspéldány |
> | Microsoft.ApiManagement/service/backup/action | A biztonsági mentési API Management szolgáltatás a felhasználó a megadott tárolóhoz megadott tárfiók |
> | Microsoft.ApiManagement/service/delete | Törli az API Management Service-példány |
> | Microsoft.ApiManagement/service/managedeployments/action | Módosítsa a Termékváltozat jegyek, hozzáadása regionális egy szolgáltatás üzemelő példányainak API Management |
> | Microsoft.ApiManagement/service/read | Olvassa el a metaadatokat az API Management szolgáltatáspéldány |
> | Microsoft.ApiManagement/service/restore/action | Állítsa vissza egy felhasználó által megadott tárfiók a megadott tároló API Management szolgáltatás |
> | Microsoft.ApiManagement/service/updatecertificate/action | Az API Management szolgáltatás SSL-tanúsítvány feltöltése |
> | Microsoft.ApiManagement/service/updatehostname/action | A telepítő, frissítésére, vagy távolítsa el az API Management szolgáltatás egyéni tartománynevek |
> | Microsoft.ApiManagement/service/write | Az API Management szolgáltatást egy új példányának létrehozása |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Felhasználói kulcsok listájának beolvasása |

## <a name="api-management-service-reader-role"></a>Az API Management szolgáltatás olvasói szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Írásvédett hozzáférés a szolgáltatáshoz és az API-khoz |
> | **Azonosító** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/*/read | Olvasási API Management szolgáltatáspéldány |
> | Microsoft.ApiManagement/service/read | Olvassa el a metaadatokat az API Management szolgáltatáspéldány |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Felhasználói kulcsok listájának beolvasása |

## <a name="application-insights-component-contributor"></a>Application Insights-összetevők közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelhet Application Insights-összetevőket |
> | **Azonosító** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.Insights/components/* | Hozzon létre és elemzések összetevők kezeléséhez |
> | Microsoft.Insights/webtests/* | Létrehozása és kezelése a webes tesztjeinek használatát |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyt ad a felhasználónak az Application Insights Snapshot Debugger szolgáltatásainak használatára |
> | **Azonosító** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="automation-job-operator"></a>Az Automation-feladat operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Feladatok létrehozása és kezelése Automation-runbookok használatával. |
> | **Azonosító** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Automation/automationAccounts/jobs/read | Egy Azure Automation-feladat beolvasása |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Egy Azure Automation-feladat folytatása |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Egy Azure Automation-feladat leállítása |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Beolvassa a hibrid forgatókönyv-feldolgozó erőforrások |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Egy Azure Automation-feladatstream beolvasása |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Egy Azure Automation-feladat felfüggesztése |
> | Microsoft.Automation/automationAccounts/jobs/write | Egy Azure Automation-feladat létrehozása |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="automation-operator"></a>Automation-operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az Automation-operátorok elindíthatnak, leállíthatnak, felfüggeszthetnek és folytathatnak feladatokat |
> | **Azonosító** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Beolvassa a hibrid forgatókönyv-feldolgozó erőforrások |
> | Microsoft.Automation/automationAccounts/jobs/read | Egy Azure Automation-feladat beolvasása |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Egy Azure Automation-feladat folytatása |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Egy Azure Automation-feladat leállítása |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Egy Azure Automation-feladatstream beolvasása |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Egy Azure Automation-feladat felfüggesztése |
> | Microsoft.Automation/automationAccounts/jobs/write | Egy Azure Automation-feladat létrehozása |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Egy Azure Automation-feladatütemezés beolvasása |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Egy Azure Automation-feladatütemezés létrehozása |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Lekérdezi a munkaterület az automation-fiókhoz csatolva. |
> | Microsoft.Automation/automationAccounts/read | Egy Azure Automation-fiók beolvasása |
> | Microsoft.Automation/automationAccounts/runbooks/read | Egy Azure Automation-runbook beolvasása |
> | Microsoft.Automation/automationAccounts/schedules/read | Egy Azure Automation szolgáltatásbeli ütemezési eszköz beolvasása |
> | Microsoft.Automation/automationAccounts/schedules/write | Létrehozza vagy frissíti az Azure Automation szolgáltatásbeli ütemezési eszköz |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Egy feladat kimenetének beolvasása |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="automation-runbook-operator"></a>Az Automation-runbook operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Runbook-tulajdonságok olvasása – a runbook-feladatok létrehozásához. |
> | **Azonosító** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Automation/automationAccounts/runbooks/read | Egy Azure Automation-runbook beolvasása |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="azure-stack-registration-owner"></a>Azure Stack-regisztráció tulajdonosa
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az Azure Stack-regisztrációk kezelését. |
> | **Azonosító** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Műveletek** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Lekéri a kiterjesztett egy Azure verem piactér megoldás részletei |
> | Microsoft.AzureStack/registrations/products/read | Egy Azure verem piactér termék tulajdonságainak beolvasása |
> | Microsoft.AzureStack/registrations/read | Egy Azure verem regisztrációs tulajdonságait olvassa be |

## <a name="backup-contributor"></a>Biztonsági mentési közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a biztonsági mentési szolgáltatás felügyeletét, de nem hozhat létre tárolókat, és nem adhat hozzáférést másoknak |
> | **Azonosító** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | A biztonsági mentési felügyeleti művelet eredménye kezelése |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Hozzon létre, és a Recovery Services-tároló biztonsági mentési hálók belül biztonsági mentési tárolók kezelése |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozásához és kezeléséhez |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportálási feladat |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Létrehozásához és kezeléséhez az biztonsági mentési felügyeletével kapcsolatos metaadatok |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Létrehozásához és kezeléséhez a biztonságimásolat-felügyeleti műveletek eredményeit |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Biztonsági szabályzatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Cikkek, amely biztonsági másolat készíthető létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Hozzon létre és kezelheti a biztonsági mentésben szereplő elemek |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Hozzon létre és cikkek biztonsági mentési tárolók kezelése |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Tanúsítványok kapcsolódik a biztonsági mentés a Recovery Services-tároló létrehozásához és felügyeletéhez |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Létrehozásához és kezeléséhez kapcsolódó tároló kiterjesztett adatai |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/* | Kezelheti a felderítési műveletet beolvasása tárolókat, újonnan létrehozott |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Hozzon létre és regisztrált felhasználók kezelése |
> | Microsoft.RecoveryServices/Vaults/usages/* | Létrehozásához és kezeléséhez a Recovery Services-tároló használata |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services beolvasása védett elemek és a védett kiszolgálók összesítések. |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | A riasztások lekéri a Recovery services-tároló. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Az exportálási feladat művelet eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="backup-operator"></a>Biztonsági mentési operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a biztonsági mentési szolgáltatások felügyeletét, kivéve a biztonsági másolatok eltávolítását, tárolók létrehozását és a másoknak való hozzáférés megadását |
> | **Azonosító** | 00c29273-979b-4161-815C-10b084fb9324 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotának beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Beolvassa a védelmi tárolón végrehajtott művelet eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Biztonsági másolat készítése egy védett elemről. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Beolvassa a védett elemeken végrehajtott művelet eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem kapcsolatobjektum-adatait adja vissza |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Védett elemek helyreállítási pontjainak beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Védett elemek helyreállítási pontjainak visszaállítása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Biztonsági mentési védett típusú elem létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Minden regisztrált tárolókat ad vissza |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozásához és kezeléséhez |
> | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | A feladat megszakítása |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | A feladatművelet eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Minden feladat objektumot ad vissza |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportálási feladat |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | A Recovery Services-tároló biztonságimásolat-kezelési metaadatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Létrehozásához és kezeléséhez a biztonságimásolat-felügyeleti műveletek eredményeit |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A szabályzatművelet eredményeinek beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Visszaadja az összes védelmi házirend |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Cikkek, amely biztonsági másolat készíthető létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | A védhető elemek listáját adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | A védett elemek listájának beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tárolókat ad vissza |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services beolvasása védett elemek és a védett kiszolgálók összesítések. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/* | Kezelheti a felderítési műveletet beolvasása tárolókat, újonnan létrehozott |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Művelet alkalmazható Get művelet eredményeit a műveleti állapotának és az aszinkron módon elküldött művelet eredménye beolvasása |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Az beszerzése tárolókat művelet alkalmazható a tárolók regisztrált erőforrás lekérése. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A szolgáltatás tárolójának regisztrálása műveletet szolgáltatásban való regisztráláshoz tárolója helyreállítási használható. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Védett elem rendelkezés azonnali elem helyreállítása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Védett elem azonnali elem helyreállítása visszavonása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | A riasztások lekéri a Recovery services-tároló. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Az exportálási feladat művelet eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
> | Microsoft.RecoveryServices/Vaults/certificates/write | A frissítés erőforrás tanúsítvány művelet frissíti az erőforrás/tárolói hitelesítő adatainak tanúsítványa. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="backup-reader"></a>Biztonsági mentési olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a biztonsági mentési szolgáltatásokat, de nem végezhet módosításokat |
> | **Azonosító** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotának beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Beolvassa a védelmi tárolón végrehajtott művelet eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Beolvassa a védett elemeken végrehajtott művelet eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem kapcsolatobjektum-adatait adja vissza |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Minden regisztrált tárolókat ad vissza |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | A feladatművelet eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Minden feladat objektumot ad vissza |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportálási feladat |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | A Recovery Services-tároló biztonságimásolat-kezelési metaadatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | A Recovery Services-tárolóval kapcsolatos biztonsági mentés eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A szabályzatművelet eredményeinek beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Visszaadja az összes védelmi házirend |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | A védett elemek listájának beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tárolókat ad vissza |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services beolvasása védett elemek és a védett kiszolgálók összesítések. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Művelet alkalmazható Get művelet eredményeit a műveleti állapotának és az aszinkron módon elküldött művelet eredménye beolvasása |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Az beszerzése tárolókat művelet alkalmazható a tárolók regisztrált erőforrás lekérése. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | A riasztások lekéri a Recovery services-tároló. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Védett elemek helyreállítási pontjainak beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Az exportálási feladat művelet eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |

## <a name="billing-reader"></a>Számlázási olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvasási hozzáférést biztosít a számlázási adatokat |
> | **Azonosító** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Billing/*/read | Olvassa el a számlázási adatokat |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználók listáját a felügyeleti csoportok. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="biztalk-contributor"></a>BizTalk közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a BizTalk-szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.BizTalkServices/BizTalk/* | Létrehozásához és kezeléséhez BizTalk szolgáltatások |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="cdn-endpoint-contributor"></a>A CDN-végpont közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelhet CDN-végpontokat, de nem tud hozzáférést adni más felhasználóknak. |
> | **Azonosító** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="cdn-endpoint-reader"></a>A CDN-végpont olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a CDN-végpontokat, de nem végezhet módosításokat. |
> | **Azonosító** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="cdn-profile-contributor"></a>A CDN-profil közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a CDN-profilokat és azok végpontjait, de nem tud hozzáférést adni más felhasználóknak. |
> | **Azonosító** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="cdn-profile-reader"></a>A CDN-profil olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a CDN-profilokat és azok végpontjait, de nem végezhet módosításokat. |
> | **Azonosító** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="classic-network-contributor"></a>Virtuális hálózatok hagyományos közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a hagyományos hálózatok kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.ClassicNetwork/* | Hozzon létre és klasszikus hálózatok kezelése |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="classic-storage-account-contributor"></a>Hagyományos tárfiók-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a hagyományos tárfiókok kezelését, de ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.ClassicStorage/storageAccounts/* | Storage-fiókok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="classic-storage-account-key-operator-service-role"></a>A hagyományos tárfiók kulcsának operátora – szolgáltatási szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A hagyományos tárfiók kulcsának operátorai jogosultak a hagyományos tárfiókokhoz tartozó kulcsok listázására és újragenerálására |
> | **Azonosító** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Műveletek** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Tárfiókok elérési kulcsainak listázása. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Tárfiók meglévő elérési kulcsainak újragenerálása. |

## <a name="classic-virtual-machine-contributor"></a>Virtuális gépek hagyományos közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a hagyományos virtuális gépek kezelését, de ezekhez nem biztosít hozzáférést, és nem teszi lehetővé a virtuális gépekhez hozzárendelt virtuális hálózatok és tárfiókok elérését sem. |
> | **Azonosító** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.ClassicCompute/domainNames/* | Hozzon létre, és a hagyományos számítási tartománynevek kezelése |
> | Microsoft.ClassicCompute/virtualMachines/* | Virtuális gépek létrehozására és kezelésére |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Egy fenntartott IP-cím hivatkozása |
> | Microsoft.ClassicNetwork/reservedIps/read | A fenntartott IP-címek beolvasása |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Csatlakozás a virtuális hálózathoz. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Virtuális hálózat beolvasása. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | A tárfióklemez beolvasása. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | A tárfióklemezkép beolvasása. |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Tárfiókok elérési kulcsainak listázása. |
> | Microsoft.ClassicStorage/storageAccounts/read | Tárfiók visszaadása az adott fiókhoz. |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL-adatbázisok közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a ClearDB MySQL-adatbázisok kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 9106cda0-8a86-4E81-b686-29a22c54effe |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | successbricks.cleardb/Databases/* | Hozzon létre és kezelheti a ClearDB MySQL-adatbázisok |

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB-fiók olvasói szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Cosmos DB fiókadatokhoz el tud olvasni. Lásd: [DocumentDB-fiók közreműködői](#documentdb-account-contributor) Azure Cosmos DB kezelésére. |
> | **Azonosító** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepkörökhöz és szerepkör-hozzárendelések elolvashatják a minden felhasználó számára megadott engedélyek |
> | Microsoft.DocumentDB/*/read | Olvassa el a gyűjtemény |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Olvassa be az adatbázis csak olvasható kulcsait. |
> | Microsoft.Insights/MetricDefinitions/read | A metrikadefiníciók olvasása |
> | Microsoft.Insights/Metrics/read | Metrikák olvasása |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="data-factory-contributor"></a>Data Factory közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Létrehozása és kezelése az adat-előállítók, valamint a gyermekszintű erőforrása rajtuk. |
> | **Azonosító** | 673868aa-7521-48A0-acc6-0f60742d39f5 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
> | Microsoft.DataFactory/dataFactories/* | Létrehozása és kezelése az adat-előállítók és gyermekerőforrásait rajtuk. |
> | Microsoft.DataFactory/factories/* | Létrehozása és kezelése az adat-előállítók és gyermekerőforrásait rajtuk. |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics-fejlesztő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a saját feladatok elküldését, figyelését és kezelését, de nem tud létrehozni vagy törölni Data Lake Analytics-fiókokat. |
> | **Azonosító** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | DataLakeAnalytics fiók törlése. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Más felhasználók által benyújtott szakítsa engedélyeket. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Fiók létrehozása vagy frissítése egy DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Fiók létrehozása vagy frissítése egy csatolt DataLakeStore DataLakeAnalytics fiók. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Megszünteti egy DataLakeStore fiók DataLakeAnalytics-fiókból. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Hozzon létre, vagy egy kapcsolódó tárfiók DataLakeAnalytics fiók módosítása. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | A Storage-fiók egy DataLakeAnalytics fiókból választható. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Hozzon létre, vagy egy tűzfalszabály módosítása. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Tűzfalszabály törlése. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Hozzon létre, vagy egy számítási házirend frissítése. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Számítás-házirend törlése. |

## <a name="data-purger"></a>Adattörlő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Véglegesen törölheti az elemzési adatokat |
> | **Azonosító** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Műveletek** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Adatok végleges törlése az Application Insightsból |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Megadott adatok törléséhez a munkaterület |

## <a name="devtest-labs-user"></a>DevTest Labs-felhasználó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Csatlakozás esetén megadható, hogy kezdő, újraindítása és leállítása a virtuális gépek az Azure DevTest Labs szolgáltatásban. |
> | **Azonosító** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
> | Microsoft.Compute/availabilitySets/read | Egy adott rendelkezésre állási csoport tulajdonságainak beolvasása |
> | Microsoft.Compute/virtualMachines/*/read | A virtuális gép (VM-méretek futási állapotának, Virtuálisgép-bővítmények, stb.) tulajdonságainak olvasása |
> | Microsoft.Compute/virtualMachines/deallocate/action | Kikapcsolja a virtuális gépet és felszabadítja a számítási erőforrásokat |
> | Microsoft.Compute/virtualMachines/read | Egy virtuális gép tulajdonságait olvassa be |
> | Microsoft.Compute/virtualMachines/restart/action | Újraindítja a virtuális gépet |
> | Microsoft.Compute/virtualMachines/start/action | Elindítja a virtuális gépet |
> | Microsoft.DevTestLab/*/read | Labor tulajdonságainak olvasása |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Virtuális gépek létrehozása egy tesztkörnyezetben. |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Jogcím egy véletlenszerű claimable virtuális gép a tesztkörnyezetben. |
> | Microsoft.DevTestLab/labs/formulas/delete | Képletek törlése. |
> | Microsoft.DevTestLab/labs/formulas/read | Olvassa el a formulákat. |
> | Microsoft.DevTestLab/labs/formulas/write | Hozzáadása vagy módosítása formulákat. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Labor házirend kiértékelése. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Saját tulajdonba vétele meglévő virtuális gépből |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | A load balancer háttércímkészletének illesztése |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Terheléselosztó bejövő forgalmat kezelő nat-szabályának illesztése |
> | Microsoft.Network/networkInterfaces/*/read | Egy adott hálózati csatoló (például, terheléselosztók, amely a hálózati adapter egy része) tulajdonságainak olvasása |
> | Microsoft.Network/networkInterfaces/join/action | A virtuális gép illesztése hálózati illesztőhöz |
> | Microsoft.Network/networkInterfaces/read | Hálózati illesztő definíciójának beolvasása.  |
> | Microsoft.Network/networkInterfaces/write | Egy adott hálózati csatoló létrehozza vagy frissíti a meglévő hálózati illesztő.  |
> | Microsoft.Network/publicIPAddresses/*/read | A nyilvános IP-cím tulajdonságainak olvasása |
> | Microsoft.Network/publicIPAddresses/join/action | A nyilvános IP-cím illesztése |
> | Microsoft.Network/publicIPAddresses/read | A nyilvános IP-cím definíciójának beolvasása |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Egy virtuális hálózathoz csatlakozik |
> | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
> | Microsoft.Resources/deployments/read | Beolvassa vagy listázza az üzemelő példányokat. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Listázza az elérhető méreteket, melyekre a virtuális gép frissíthető |

## <a name="dns-zone-contributor"></a>A DNS-zóna közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az Azure DNS-beli DNS-zónák és rekordkészletek kezelését, de nem teszi lehetővé az azokhoz hozzáférő felhasználók felügyeletét. |
> | **Azonosító** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.Network/dnsZones/* | DNS-zónák és rekordok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="documentdb-account-contributor"></a>DocumentDB-fiókközreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felügyelheti az Azure Cosmos DB fiókjait. Azure Cosmos-adatbázis a DocumentDB nevén. |
> | **Azonosító** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
> | Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB fiókok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="intelligent-systems-account-contributor"></a>Intelligens rendszerek fiókközreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az intelligens rendszerek fiókjainak kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.IntelligentSystems/accounts/* | Intelligens rendszerek fiókok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="key-vault-contributor"></a>Key Vault-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a kulcstartók kezelését, de ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Helyreállíthatóan törölt kulcstartó végleges törlése |
> | Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Tesztkörnyezet létrehozója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felügyelt tesztkörnyezetek az Azure Lab-fiókokban való létrehozását, kezelését és törlését teszi lehetővé. |
> | **Azonosító** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Labor létrehozása a tesztkörnyezeti fiók. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="log-analytics-contributor"></a>Log Analytics közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Napló Analytics közreműködői összes figyelési adatot olvashatja és szerkesztése a figyelési beállításokat. Figyelési beállításainak szerkesztése magában foglalja a Virtuálisgép-bővítmény hozzáadása a virtuális gépek; tárfiókkulcsok tudják konfigurálni a naplók az Azure Storage; gyűjtemény olvasása létrehozásáról és konfigurálásáról az Automation-fiók; megoldásokkal; és az összes Azure-erőforrások konfigurálása az Azure diagnostics. |
> | **Azonosító** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Műveletek** |  |
> | * / olvasása | Olvassa el az erőforrásokat bármilyen típusú, kivéve a titkos kulcsok. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Tárfiókok elérési kulcsainak listázása. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Hoz létre, a frissítéseket, vagy az Analysis Server diagnosztikai beállításának beolvasása |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="log-analytics-reader"></a>Log Analytics olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A Log Analytics-olvasó megtekintheti az összes figyelési adatot, és kereshet azokban, valamint megtekintheti a figyelési beállításokat, beleértve az összes Azure-erőforrás Azure-beli diagnosztikájának konfigurációját is. |
> | **Azonosító** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Műveletek** |  |
> | * / olvasása | Olvassa el az erőforrásokat bármilyen típusú, kivéve a titkos kulcsok. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | A keresés új motor használatával. |
> | Microsoft.OperationalInsights/workspaces/search/action | Keresési lekérdezés végrehajtása |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Lekéri a megosztott kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó. |

## <a name="logic-app-contributor"></a>A logikai alkalmazás közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi logikai alkalmazások kezelését, de ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Tárfiókok elérési kulcsainak listázása. |
> | Microsoft.ClassicStorage/storageAccounts/read | Tárfiók visszaadása az adott fiókhoz. |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Hoz létre, a frissítéseket, vagy az Analysis Server diagnosztikai beállításának beolvasása |
> | Microsoft.Insights/logdefinitions/* | Ez az engedély szükség a felhasználók számára, akiknek tevékenységi naplóit a portálon hozzáférésre van szükségük. Tevékenységnapló napló kategóriák listája. |
> | Microsoft.Insights/metricDefinitions/* | Olvassa el a metrikai meghatározásainak (erőforrás elérhető metrika típusok listája). |
> | Microsoft.Logic/* | Kezeli a Logic Apps-erőforrásokat. |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
> | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | Microsoft.Web/connectionGateways/* | Hozzon létre és kezeli a kapcsolat átjáró. |
> | Microsoft.Web/connections/* | Hozzon létre és kezeli a kapcsolatot. |
> | Microsoft.Web/customApis/* | Hozza létre, és egy egyéni API felügyeli. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Az egy App Service-csomag tulajdonságainak beolvasása |
> | Microsoft.Web/sites/functions/listSecrets/action | Lista titkok Web Apps funkciók. |

## <a name="logic-app-operator"></a>Logikai alkalmazás operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi logikai alkalmazások olvasását, engedélyezését és letiltását. |
> | **Azonosító** | 515c2055-d9d4-4321-B1B9-bd0c9a0f79fe |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/*/read | Olvasási Insights riasztási szabályok |
> | Microsoft.Insights/diagnosticSettings/*/read | A Logic Apps diagnosztikai beállításainak beolvasása |
> | Microsoft.Insights/metricDefinitions/*/read | A Logic Apps elérhető metrikai meghatározások beolvasása. |
> | Microsoft.Logic/*/read | Beolvassa a Logic Apps-erőforrásokat. |
> | Microsoft.Logic/workflows/disable/action | Letiltja a munkafolyamatot. |
> | Microsoft.Logic/workflows/enable/action | Engedélyezi a munkafolyamatot. |
> | Microsoft.Logic/workflows/validate/action | Érvényesíti a munkafolyamatot. |
> | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | Microsoft.Web/connectionGateways/*/read | Olvasása kapcsolat átjárók. |
> | Microsoft.Web/connections/*/read | Olvassa el a kapcsolatokat. |
> | Microsoft.Web/customApis/*/read | Olvassa el az egyéni API. |
> | Microsoft.Web/serverFarms/read | Az egy App Service-csomag tulajdonságainak beolvasása |

## <a name="managed-identity-contributor"></a>Felügyelt identitások közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A felhasználóhoz hozzárendelt identitás létrehozása, olvasása, frissítése és törlése |
> | **Azonosító** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Műveletek** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="managed-identity-operator"></a>Felügyelt identitások üzemeltetője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A felhasználóhoz hozzárendelt identitás olvasása és hozzárendelése |
> | **Azonosító** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Műveletek** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="monitoring-contributor"></a>Közreműködő figyelése
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Összes figyelési adatot olvashatja és szerkesztheti a figyelési beállításokat. Lásd még: [Ismerkedés a szerepkörök, engedélyek és biztonsági Azure megfigyelővel](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **Azonosító** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Műveletek** |  |
> | * / olvasása | Olvassa el az erőforrásokat bármilyen típusú, kivéve a titkos kulcsok. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/AlertRules/* | Olvasási, írási és törlési riasztási szabályok. |
> | Microsoft.Insights/components/* | Olvasási, írási és törlési Application Insights összetevőinek. |
> | Microsoft.Insights/DiagnosticSettings/* | Olvasási, írási és törlési diagnosztikai beállítások. |
> | Microsoft.Insights/eventtypes/* | Tevékenységnapló események (felügyeleti események) egy előfizetésben listában. Ezzel az engedéllyel csak a műveletnapló programozott és a portál eléréséhez alkalmazható. |
> | Microsoft.Insights/LogDefinitions/* | Ez az engedély szükség a felhasználók számára, akiknek tevékenységi naplóit a portálon hozzáférésre van szükségük. Tevékenységnapló napló kategóriák listája. |
> | Microsoft.Insights/MetricDefinitions/* | Olvassa el a metrikai meghatározásainak (erőforrás elérhető metrika típusok listája). |
> | Microsoft.Insights/Metrics/* | Egy erőforrás olvasni. |
> | Microsoft.Insights/Register/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | Microsoft.Insights/webtests/* | Olvasási, írási és törlési Application Insights webalkalmazás-tesztek. |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Olvasási, írási és törlési Naplóelemzési megoldás csomagokat. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Olvasási, írási és törlési Naplóelemzési mentett kereséseket. |
> | Microsoft.OperationalInsights/workspaces/search/action | Keresési lekérdezés végrehajtása |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Lekéri a megosztott kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Olvasási, írási és törlési Naplóelemzési insight tárolókonfigurációkkal. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Olvasó figyelése
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az összes figyelési adatokat (metrikákat, naplói, stb.) el tud olvasni. Lásd még: [Ismerkedés a szerepkörök, engedélyek és biztonsági Azure megfigyelővel](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **Azonosító** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Műveletek** |  |
> | * / olvasása | Olvassa el az erőforrásokat bármilyen típusú, kivéve a titkos kulcsok. |
> | Microsoft.OperationalInsights/workspaces/search/action | Keresési lekérdezés végrehajtása |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="network-contributor"></a>Hálózati közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a hálózatok kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.Network/* | Hozzon létre és hálózatok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="new-relic-apm-account-contributor"></a>New Relic APM-fiókközreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a New Relic Application Performance Management-fiókok és -alkalmazások kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | NewRelic.APM/accounts/* |  |

## <a name="reader-and-data-access"></a>Olvasó és adatelérés
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy mindent megtekinthetnek, de nem engedélyezi törlése, vagy hozzon létre egy tárfiókhoz vagy a benne lévő erőforrás. Olvasási és írási hozzáférést tárfiókkulcsok keresztül tárfiókokban tárolt adatokat is engedélyezi. |
> | **Azonosító** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
> | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |

## <a name="redis-cache-contributor"></a>Redis gyorsítótárak közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Redis-gyorsítótárak kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
> | Microsoft.Cache/redis/* | Létrehozásához és kezeléséhez a Redis gyorsítótár |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="resource-policy-contributor-preview"></a>Erőforrás-szabályzati közreműködő (előnézet)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | (Előnézet) Erőforrás-szabályzat létrehozására/módosítására, támogatási jegy létrehozására, valamint erőforrások/hierarchia beolvasására jogosultsággal rendelkező, EA-beli visszatöltött felhasználók. |
> | **Azonosító** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Műveletek** |  |
> | * / olvasása | Olvassa el az erőforrásokat bármilyen típusú, kivéve a titkos kulcsok. |
> | Microsoft.Authorization/policyassignments/* | Hozzon létre és kezelheti a házirend-hozzárendelések |
> | Microsoft.Authorization/policydefinitions/* | Létrehozásához és kezeléséhez a házirend-definíciók száma |
> | Microsoft.Authorization/policysetdefinitions/* | Létrehozásához és kezeléséhez házirend beállítása |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="scheduler-job-collections-contributor"></a>Scheduler szolgáltatás feladatgyűjteményeinek közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Scheduler-feladatgyűjtemények kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Scheduler/jobcollections/* | Feladat gyűjtemények létrehozásához és kezeléséhez |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="search-service-contributor"></a>Search szolgáltatás közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a keresési szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Search/searchServices/* | Hozzon létre és keresési szolgáltatások kezelése |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="security-admin"></a>Biztonsági rendszergazda
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A csak a Security Center: is megtekintheti a biztonsági házirendek, biztonsági állapotok megtekintéséhez, szerkesztheti a biztonsági házirendek, a riasztások megtekintése és a javaslatok, hagyja figyelmen kívül a riasztások és javaslatok |
> | **Azonosító** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Authorization/policyAssignments/* | Hozzon létre és kezelheti a házirend-hozzárendelések |
> | Microsoft.Authorization/policyDefinitions/* | Létrehozásához és kezeléséhez a házirend-definíciók száma |
> | Microsoft.Authorization/policySetDefinitions/* | Létrehozásához és kezeléséhez házirend beállítása |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.operationalInsights/workspaces/*/read | Log Analytics-adatok megtekintése |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Security/*/read | Olvasási biztonsági összetevőinek és házirendek |
> | Microsoft.Security/locations/alerts/dismiss/action | A biztonsági riasztások elvetése |
> | Microsoft.Security/locations/alerts/activate/action | Aktiválja a biztonsági riasztások |
> | Microsoft.Security/locations/tasks/dismiss/action | Biztonsági ajánlás olyan környezetekben elvetése |
> | Microsoft.Security/locations/tasks/activate/action | Biztonsági ajánlás olyan környezetekben aktiválása |
> | Microsoft.Security/policies/write | A biztonsági házirend frissítése |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="security-manager-legacy"></a>A biztonságkezelő (örökölt)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Ez az örökölt szerepkör. Ehelyett használja a biztonsági rendszergazda |
> | **Azonosító** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.ClassicCompute/*/read | Olvassa el a konfigurációs adatok klasszikus virtuális gépek |
> | Microsoft.ClassicCompute/virtualMachines/*/write | A klasszikus virtuális gépek konfigurációs írása |
> | Microsoft.ClassicNetwork/*/read | Olvassa el hagyományos hálózati konfigurációs adatait |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Security/* | Biztonsági összetevők és házirendek létrehozása és kezelése |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="security-reader"></a>Biztonsági olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A csak a Security Center: javaslatokra és riasztásokra, biztonsági házirendeket, biztonsági állapotok megtekintéséhez, de nem módosíthatnak semmit nézet megtekintéséhez |
> | **Azonosító** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Műveletek** |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.operationalInsights/workspaces/*/read | Log Analytics-adatok megtekintése |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Security/*/read | Olvasási biztonsági összetevőinek és házirendek |

## <a name="site-recovery-contributor"></a>Site Recovery-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Site Recovery szolgáltatás felügyeletét, kivéve a tárolók létrehozását és a szerepkör-hozzárendelést |
> | **Azonosító** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp egy szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/Vaults/certificates/write | A frissítés erőforrás tanúsítvány művelet frissíti az erőforrás/tárolói hitelesítő adatainak tanúsítványa. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Létrehozásához és kezeléséhez kapcsolódó tároló kiterjesztett adatai |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Hozzon létre és regisztrált felhasználók kezelése |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Replikációs riasztási beállításainak létrehozása vagy frissítése |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Bármely események olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Hozzon létre és kezelheti a replikációs hálók |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Fájlreplikációs feladatok létrehozásához és kezeléséhez |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Replikációs szabályzatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Hozzon létre és helyreállítási tervek kezelése |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Létrehozásához és kezeléséhez tárolási konfigurációt a Recovery Services-tároló |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services-tároló információi token értéket ad vissza. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló Token művelet alkalmazható tároló szintű háttérbeli műveletek tároló jogkivonatának beszerzéséhez. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | A Recovery services-tároló a riasztások olvasása |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="site-recovery-operator"></a>Site Recovery-operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a feladatátvételt és a feladat-visszavételt, de nem biztosít egyéb Site Recovery-beli felügyeleti műveleteket |
> | **Azonosító** | 494ae006-db33-4328-BF46-533a6560a3ca |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp egy szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Művelet alkalmazható Get művelet eredményeit a műveleti állapotának és az aszinkron módon elküldött művelet eredménye beolvasása |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Az beszerzése tárolókat művelet alkalmazható a tárolók regisztrált erőforrás lekérése. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Olvassa el a riasztások beállításai |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Bármely események olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Konzisztencia-ellenőrzést futtat a hálón |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | A hálók olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Átjáró újbóli társítása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Háló tanúsítványának megújítása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Olvassa el a hálózatok |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Olvassa el a hálózatok leképezését |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Olvassa el a védelmi tárolókkal |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Bármely védhető elemek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Helyreállítási pont alkalmazása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Feladatátvétel véglegesítésének |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Tervezett feladatátvétel |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Minden védett elemek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Olvassa el a replikációs helyreállítási pontot |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Javítás replikáció |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Állítsa a védett elem |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Feladatátvételi teszt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Teszt feladatátadás kitakarítását |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Feladatátvétel |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Frissítse a mobilitási szolgáltatás |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Olvassa el a védelmi tároló leképezések |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Olvassa el az összes helyreállítási szolgáltatók |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Frissítse a szolgáltatót |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | A Tárhelybesorolások olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Olvassa el a tárolási besorolás leképezések |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Olvassa el a megfelelő feladat |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Fájlreplikációs feladatok létrehozásához és kezeléséhez |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Olvassa el az összes házirend |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Feladatátvétel véglegesítésének helyreállítási terv |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Tervezett feladatátvétel helyreállítási terv |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | A helyreállítási tervek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Állítsa a helyreállítási terv |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Teszt feladatátvételi helyreállítási terv |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Teszt feladatátadás kitakarítását helyreállítási terv |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Feladatátvételi helyreállítási terv |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | A Recovery services-tároló a riasztások olvasása |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services-tároló információi token értéket ad vissza. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló Token művelet alkalmazható tároló szintű háttérbeli műveletek tároló jogkivonatának beszerzéséhez. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="site-recovery-reader"></a>Site Recovery-olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Site Recovery állapotának megtekintését, de nem biztosít egyéb felügyeleti műveleteket |
> | **Azonosító** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | A riasztások lekéri a Recovery services-tároló. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Művelet alkalmazható Get művelet eredményeit a műveleti állapotának és az aszinkron módon elküldött művelet eredménye beolvasása |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Az beszerzése tárolókat művelet alkalmazható a tárolók regisztrált erőforrás lekérése. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Olvassa el a riasztások beállításai |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Bármely események olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | A hálók olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Olvassa el a hálózatok |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Olvassa el a hálózatok leképezését |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Olvassa el a védelmi tárolókkal |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Bármely védhető elemek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Minden védett elemek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Olvassa el a replikációs helyreállítási pontot |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Olvassa el a védelmi tároló leképezések |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Olvassa el az összes helyreállítási szolgáltatók |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | A Tárhelybesorolások olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Olvassa el a tárolási besorolás leképezések |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Olvassa el a megfelelő feladat |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Olvassa el a megfelelő feladat |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Olvassa el az összes házirend |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | A helyreállítási tervek olvasása |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services-tároló információi token értéket ad vissza. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló Token művelet alkalmazható tároló szintű háttérbeli műveletek tároló jogkivonatának beszerzéséhez. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="sql-db-contributor"></a>SQL-adatbázisok közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi SQL-adatbázisok, de nem való hozzáférés kezelését. Emellett a biztonsági házirendjét vagy a szülő SQL Server nem tudja kezelni. |
> | **Azonosító** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és hozzárendelések szerepkör |
> | Microsoft.Insights/alertRules/* | Hozzon létre és riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Létrehozásához és kezeléséhez az SQL-adatbázisok |
> | Microsoft.Sql/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgálóhoz tartozó tulajdonságok listáját adja vissza. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | **NotActions** |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Naplózási házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Naplózási beállítások nem szerkeszthetők. |
> | Microsoft.Sql/servers/databases/auditRecords/read | A blob naplózási rekordok beolvasása |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Szabályzatok nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Adatmaszkolási házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Biztonsági riasztások házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Biztonsági metrikák nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>SQL-biztonságkezelő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetőséget nyújt az SQL-kiszolgálók és adatbázisok biztonsági házirendjeinek felügyeletére az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el a Microsoft engedélyezési |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Alhálózathoz csatlakozik az erőforrás, például a tárfiók, vagy az SQL-adatbázis. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL server naplózási szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/auditingSettings/* | Létrehozásához és kezeléséhez az SQL server a naplózási beállítás |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL server adatbázis naplózási szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Hozzon létre és SQL server-adatbázis naplózási beállításainak kezelése |
> | Microsoft.Sql/servers/databases/auditRecords/read | Olvasási auditálási rekordok |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | SQL server adatbázis kapcsolati szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Hozzon létre és SQL server adatbázis adatmaszkolási házirendek kezelése |
> | Microsoft.Sql/servers/databases/read | Adatbázisok vagy lekérdezi a megadott adatbázis tulajdonságainak megadása listáját adja vissza. |
> | Microsoft.Sql/servers/databases/schemas/read | Adatbázis sémák listájának beolvasása |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Egy tábla oszlopainak listájának beolvasása |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Egy adatbázis táblák listáját beolvasása |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL server adatbázis biztonsági riasztási szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Létrehozásához és kezeléséhez az SQL server-adatbázis biztonsági metrikák |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgálóhoz tartozó tulajdonságok listáját adja vissza. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | SQL server biztonsági riasztás szabályzatok létrehozása és kezelése |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="sql-server-contributor"></a>SQL Server közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetőséget nyújt az SQL-kiszolgálók és adatbázisok felügyeletére az azokhoz való hozzáférés nélkül. Az adatbázisok biztonsági házirendjeinek felügyeletét nem teszi lehetővé. |
> | **Azonosító** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Hozzon létre és SQL-kiszolgálók kezelése |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | **NotActions** |  |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL server naplózási házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/auditingSettings/* | SQL server naplózási beállítások nem szerkeszthetők. |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL server adatbázis naplózási házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Nem lehet SQL server-adatbázis naplózási beállításainak szerkesztése |
> | Microsoft.Sql/servers/databases/auditRecords/read | Nem olvasható az auditálási rekordok |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | SQL server adatbázis kapcsolati házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL server adatbázis adatmaszkolási házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL server adatbázis biztonsági riasztási házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/securityMetrics/* | SQL server-adatbázis biztonsági metrikák nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | SQL server biztonsági riasztás házirendek nem szerkeszthető. |

## <a name="storage-account-contributor"></a>Tárfiók-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi tárfiókok kezelését, de ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el az összes engedélyezési |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Diagnosztikai beállítások kezelése |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Alhálózathoz csatlakozik az erőforrás, például a tárfiók, vagy az SQL-adatbázis. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/* | Storage-fiókok létrehozása és kezelése |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="storage-account-key-operator-service-role"></a>A tárfiók kulcsának operátora – szolgáltatási szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A tárfiók kulcsának operátorai jogosultak a tárfiókokhoz tartozó kulcsok listázására és újragenerálására |
> | **Azonosító** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | A megadott tárfiók hozzáférési kulcsainak újragenerálása. |

## <a name="storage-blob-data-contributor-preview"></a>Storage-blobadatok közreműködője (minta)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Storage-blobtárolók és -adatok olvasási, írási és törlési hozzáférésének engedélyezése |
> | **Azonosító** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | A tároló törlésének eredményét adja vissza |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Egy tárolót vagy tárolók listáját adja vissza |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | A put vagy lease blobtároló-művelet eredményének visszaadása |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Egy blob törlésének eredményét adja vissza |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blobot vagy blobok listáját adja vissza |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Blob írásának eredményét adja vissza |

## <a name="storage-blob-data-reader-preview"></a>Storage-blobadatok olvasója (minta)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Storage-blobtárolók és -adatok olvasási hozzáférésének engedélyezése |
> | **Azonosító** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Egy tárolót vagy tárolók listáját adja vissza |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blobot vagy blobok listáját adja vissza |

## <a name="storage-queue-data-contributor-preview"></a>Storage-üzenetsorbeli adatok közreműködője (minta)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Storage-üzenetsorok és üzenetsorbeli üzenetek olvasási, írási és törlési hozzáférésének engedélyezése |
> | **Azonosító** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Sor törlésének eredményét adja vissza |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | A sort vagy a sorok listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | A sor írásának eredményét adja vissza |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Üzenet törlésének eredményét adja vissza |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Egy üzenetet ad vissza |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Üzenet írásának eredményét adja vissza |

## <a name="storage-queue-data-reader-preview"></a>Storage-üzenetsorbeli adatok olvasója (minta)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Storage-üzenetsorok és üzenetsorbeli üzenetek olvasási hozzáférésének engedélyezése |
> | **Azonosító** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | A sort vagy a sorok listáját adja vissza. |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Egy üzenetet ad vissza |

## <a name="support-request-contributor"></a>Támogatáskérési közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Támogatási kérések létrehozását és kezelését teszi lehetővé |
> | **Azonosító** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="traffic-manager-contributor"></a>Traffic Manager-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz hozzáférő felhasználók felügyeletét. |
> | **Azonosító** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="user-access-administrator"></a>Felhasználói hozzáférés rendszergazdája
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Azure-erőforrásokhoz való felhasználói hozzáférés kezelését. |
> | **Azonosító** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Műveletek** |  |
> | * / olvasása | Olvassa el az erőforrásokat bármilyen típusú, kivéve a titkos kulcsok. |
> | Microsoft.Authorization/* | Engedélyezési kezelése |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="virtual-machine-administrator-login"></a>Virtuális gépre való rendszergazdai bejelentkezés
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | – Az ezzel a szerepkörrel rendelkező felhasználók képesek bejelentkezni egy virtuális gépre a Windows rendszergazdai vagy a Linux gyökérszintű felhasználójának jogosultságaival. |
> | **Azonosító** | 1c0163c0-47E6-4577-8991-ea5c82e286e4 |
> | **Műveletek** |  |
> | Microsoft.Network/publicIPAddresses/read | A nyilvános IP-cím definíciójának beolvasása |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.Network/loadBalancers/read | Terheléselosztó definíciójának beolvasása |
> | Microsoft.Network/networkInterfaces/read | Hálózati illesztő definíciójának beolvasása.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Bejelentkezés egy virtuális gépre a Windows rendszergazdai vagy a Linux gyökérszintű felhasználójának jogosultságaival |

## <a name="virtual-machine-contributor"></a>Virtuális gépek közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi virtuális gépek, de nem érhető el, és nem a virtuális hálózat vagy kapcsolódó tárfiók kezelését. |
> | **Azonosító** | 9980e02c-c2be-4D73-94e8-173b1dc7cf3c |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Compute/availabilitySets/* | Hozzon létre és számítási rendelkezésre állási csoportok kezelése |
> | Microsoft.Compute/locations/* | Hozzon létre és számítási helyek kezelése |
> | Microsoft.Compute/virtualMachines/* | Virtuális gépek létrehozására és kezelésére |
> | Microsoft.Compute/virtualMachineScaleSets/* | Hozzon létre és virtuálisgép-méretezési csoportok kezelése |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Egy alkalmazás Alkalmazásátjáró háttércímkészletének illesztése |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | A load balancer háttércímkészletének illesztése |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Művelettel illeszthető egy terheléselosztó bejövő forgalmat kezelő nat-készlete |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Terheléselosztó bejövő forgalmat kezelő nat-szabályának illesztése |
> | Microsoft.Network/loadBalancers/probes/join/action | Lehetővé teszi, hogy a terheléselosztó mintavételt használatával. Például, az engedély healthProbe tulajdonság a Virtuálisgép-méretezési készlet is hivatkozhatnak a mintavételi. |
> | Microsoft.Network/loadBalancers/read | Terheléselosztó definíciójának beolvasása |
> | Microsoft.Network/locations/* | Hozzon létre és kezelheti a hálózati helyek |
> | Microsoft.Network/networkInterfaces/* | Hozzon létre és kezelheti a hálózati adapterek |
> | Microsoft.Network/networkSecurityGroups/join/action | Hálózati biztonsági csoport illesztése |
> | Microsoft.Network/networkSecurityGroups/read | Hálózati biztonsági csoport definíciójának beolvasása |
> | Microsoft.Network/publicIPAddresses/join/action | A nyilvános IP-cím illesztése |
> | Microsoft.Network/publicIPAddresses/read | A nyilvános IP-cím definíciójának beolvasása |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Egy virtuális hálózathoz csatlakozik |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem kapcsolatobjektum-adatait adja vissza |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Biztonsági mentési védett típusú elem létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | A biztonságimásolat-készítő védelmi leképezés létrehozásához |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Visszaadja az összes védelmi házirend |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Védelmi házirend létrehozása |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/write | A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
> | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |

## <a name="virtual-machine-user-login"></a>Virtuális gépre való felhasználói bejelentkezés
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az ezzel a szerepkörrel rendelkező felhasználók képesek bejelentkezni egy virtuális gépre normál felhasználóként. |
> | **Azonosító** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Műveletek** |  |
> | Microsoft.Network/publicIPAddresses/read | A nyilvános IP-cím definíciójának beolvasása |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.Network/loadBalancers/read | Terheléselosztó definíciójának beolvasása |
> | Microsoft.Network/networkInterfaces/read | Hálózati illesztő definíciójának beolvasása.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Bejelentkezés egy virtuális gépre normál felhasználóként |

## <a name="web-plan-contributor"></a>Webes tarifacsomagok közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a webes tarifacsomagok kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | Microsoft.Web/serverFarms/* | Hozzon létre, és a kiszolgálófarmok kezelése |

## <a name="website-contributor"></a>Webhelyek közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a webhelyek kezelését (a webes tarifacsomagokét azonban nem), az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Hozzon létre és elemzések riasztási szabályok kezelése |
> | Microsoft.Insights/components/* | Hozzon létre és elemzések összetevők kezeléséhez |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Hozzon létre és erőforrás-csoport központi telepítések felügyeletéhez szükséges |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre és támogatási jegyek kezelése |
> | Microsoft.Web/certificates/* | Webhely tanúsítványok létrehozásához és felügyeletéhez |
> | Microsoft.Web/listSitesAssignedToHostName/read | Lekérése az állomásnév rendelt helyek nevét. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Az egy App Service-csomag tulajdonságainak beolvasása |
> | Microsoft.Web/sites/* | Létrehozása és kezelése (webhely létrehozása is írási engedélyeket igényel a társított App Service-csomag) webhelyek |

## <a name="next-steps"></a>További lépések

- [Egyéni szerepkörök](custom-roles.md)
- [Az Azure portál használatával szerepkör-hozzárendelések kezelése](role-assignments-portal.md)
- [Engedélyek az Azure Security Centerben](../security-center/security-center-permissions.md)
