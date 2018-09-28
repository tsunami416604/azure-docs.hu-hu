---
title: Az Azure-erőforrások beépített szerepkörök |} A Microsoft Docs
description: A beépített szerepkörök, szerepkör alapú hozzáférés-vezérlés (RBAC) és az Azure-erőforrások ismerteti. A műveletek, NotActions, DataActions vagy NotDataActions sorolja fel.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 09/27/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 6fe9a106975a03fabc9d674ede694e683dc3cd94
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410272"
---
# <a name="built-in-roles-for-azure-resources"></a>Az Azure-erőforrások beépített szerepkörök
[Szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) rendelkezik, amelyeket hozzárendelhet a felhasználók, csoportok és az egyszerű szolgáltatások számos beépített szerepkör-definíciók. Szerepkör-hozzárendelések módon az Azure-erőforrások elérését Ön szabályozza. Ha a beépített szerepkörök nem felelnek meg a cég vagy intézmény igényeinek, saját [egyéni szerepköröket](custom-roles.md) is létrehozhat.

A beépített szerepkörök mindig folyamatosan fejlődik. A legújabb szerepkör-definíciókat használja [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) vagy [az role definition listájában](/cli/azure/role/definition#az-role-definition-list).

## <a name="built-in-role-descriptions"></a>Beépített szerepkör leírása
A következő táblázat a beépített szerepkörök rövid leírása. A szerepkör nevét a listájának megtekintéséhez kattintson `Actions`, `NotActions`, `DataActions`, és `NotDataActions` az egyes szerepkörökhöz.


| Beépített szerepkör | Leírás |
| --- | --- |
| [Tulajdonos](#owner) | Lehetővé teszi az összes funkció kezelését, beleértve az erőforrásokhoz való hozzáférést is. |
| [Közreműködő](#contributor) | Lehetővé teszi az összes funkció kezelését, kivéve az erőforrásokhoz való hozzáférést. |
| [Olvasó](#reader) | Lehetővé teszi, hogy mindent megtekinthessen, de módosításokat nem hajthat végre. |
| [AcrImageSigner](#acrimagesigner) | ACR-lemezképaláíró |
| [AcrQuarantineReader](#acrquarantinereader) | ACR-karanténadatolvasó |
| [AcrQuarantineWriter](#acrquarantinewriter) | ACR-karanténadatíró |
| [API Management szolgáltatás Közreműködője](#api-management-service-contributor) | Lehetővé teszi az API Management-szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. |
| [API Management szolgáltatás operátori szerepköre](#api-management-service-operator-role) | Szolgáltatásokat kezelhet, de API-kat nem |
| [API Management szolgáltatás olvasói szerepköre](#api-management-service-reader-role) | Írásvédett hozzáférés a szolgáltatáshoz és az API-khoz |
| [Application Insights-összetevők Közreműködője](#application-insights-component-contributor) | Kezelhet Application Insights-összetevőket |
| [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Engedélyt ad a felhasználónak megtekintése és letöltése az Application Insights Snapshot Debugger az gyűjtött hibakeresési pillanatképek. Vegye figyelembe, hogy ezeket az engedélyeket nem szerepelnek a [tulajdonosa](#owner) vagy [közreműködői](#contributor) szerepköröket. |
| [Automation-feladat operátora](#automation-job-operator) | Feladatok létrehozása és kezelése Automation-runbookok használatával. |
| [Automation-operátor](#automation-operator) | Az Automation-operátorok elindíthatnak, leállíthatnak, felfüggeszthetnek és folytathatnak feladatokat |
| [Automation-Runbook operátora](#automation-runbook-operator) | Runbook-tulajdonságok olvasása – a runbook-feladatok létrehozásához. |
| [Az Azure Kubernetes Service-fürt rendszergazdai szerepkör](#azure-kubernetes-service-cluster-admin-role) | Fürt rendszergazdai hitelesítő adatok a művelet listázza. |
| [Az Azure Kubernetes Service-fürt felhasználói szerepkör](#azure-kubernetes-service-cluster-user-role) | Fürt felhasználói hitelesítő adatok a művelet listázza. |
| [Az Azure Stack-regisztráció tulajdonosa](#azure-stack-registration-owner) | Lehetővé teszi az Azure Stack-regisztrációk kezelését. |
| [Biztonsági mentési közreműködő](#backup-contributor) | Lehetővé teszi a biztonsági mentési szolgáltatás felügyeletét, de nem hozhat létre tárolókat, és nem adhat hozzáférést másoknak |
| [Biztonságimásolat-felelős](#backup-operator) | Lehetővé teszi a biztonsági mentési szolgáltatások felügyeletét, kivéve a biztonsági másolatok eltávolítását, tárolók létrehozását és a másoknak való hozzáférés megadását |
| [Biztonsági mentési olvasó](#backup-reader) | Megtekintheti a biztonsági mentési szolgáltatásokat, de nem végezhet módosításokat |
| [Számlázási olvasó](#billing-reader) | Lehetővé teszi a számlázási adatok olvasását |
| [BizTalk Közreműködője](#biztalk-contributor) | Lehetővé teszi a BizTalk-szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. |
| [CDN-végpont Közreműködője](#cdn-endpoint-contributor) | Kezelhet CDN-végpontokat, de nem tud hozzáférést adni más felhasználóknak. |
| [CDN-végpont olvasója](#cdn-endpoint-reader) | Megtekintheti a CDN-végpontokat, de nem végezhet módosításokat. |
| [A CDN-profil Közreműködője](#cdn-profile-contributor) | Kezelheti a CDN-profilokat és azok végpontjait, de nem tud hozzáférést adni más felhasználóknak. |
| [A CDN-profil olvasója](#cdn-profile-reader) | Megtekintheti a CDN-profilokat és azok végpontjait, de nem végezhet módosításokat. |
| [Klasszikus hálózati közreműködő](#classic-network-contributor) | Lehetővé teszi a hagyományos hálózatok kezelését, az azokhoz való hozzáférés nélkül. |
| [Hagyományos Tárfiók-közreműködő](#classic-storage-account-contributor) | Lehetővé teszi a hagyományos tárfiókok kezelését, de ezekhez nem biztosít hozzáférést. |
| [Hagyományos tároló fiók fő operátora – szolgáltatási szerepkör](#classic-storage-account-key-operator-service-role) | A hagyományos tárfiók kulcsának operátorai jogosultak a hagyományos tárfiókokhoz tartozó kulcsok listázására és újragenerálására |
| [Virtuális gépek hagyományos Közreműködője](#classic-virtual-machine-contributor) | Lehetővé teszi a hagyományos virtuális gépek kezelését, de ezekhez nem biztosít hozzáférést, és nem teszi lehetővé a virtuális gépekhez hozzárendelt virtuális hálózatok és tárfiókok elérését sem. |
| [A cognitive Services-közreműködő](#cognitive-services-contributor) | Lehetővé teszi, hogy létrehozása, olvasása, frissítése, törlése és a Cognitive Services, kulcsok kezelése. |
| [A cognitive Services-felhasználó](#cognitive-services-user) | Lehetővé teszi, hogy Ön elolvassa és a Cognitive Services, kulcsok listázását. |
| [A cosmos DB-fiók olvasói szerepköre](#cosmos-db-account-reader-role) | Olvashatja az Azure Cosmos DB-fiókja adatait. Lásd: [DocumentDB-Fiókközreműködő](#documentdb-account-contributor) kezeléséhez az Azure Cosmos DB-fiókokhoz. |
| [A Cost Management Közreműködője](#cost-management-contributor) | Költségek megtekintheti és kezelheti a költség-konfiguráció (pl. költségvetéseket, export) |
| [A Cost Management olvasó](#cost-management-reader) | Költségadatok és a konfiguráció (pl. költségvetéseket, export) |
| [Data Box Közreműködője](#data-box-contributor) | Kezelheti a Data Box szolgáltatás található minden elemet kivételével másoknak való hozzáférés megadását teszi lehetővé. |
| [Data Box-olvasó](#data-box-reader) | Kezelheti a Data Box szolgáltatás kivéve a rendelés létrehozása vagy szerkesztése a rendelés részleteit, és a másoknak való hozzáférés megadását teszi lehetővé. |
| [Data Factory Közreműködője](#data-factory-contributor) | Lehetővé teszi az adat-előállítók kezelését, az azokhoz való hozzáférés nélkül. |
| [Data Lake Analytics-fejlesztő](#data-lake-analytics-developer) | Lehetővé teszi a saját feladatok elküldését, figyelését és kezelését, de nem tud létrehozni vagy törölni Data Lake Analytics-fiókokat. |
| [Adatok Purger](#data-purger) | Véglegesen törölheti az elemzési adatokat |
| [DevTest Labs-felhasználó](#devtest-labs-user) | Lehetővé teszi az Azure DevTest Labs virtuális gépeinek csatlakoztatását, indítását, újraindítását és leállítását. |
| [DNS-zóna Közreműködője](#dns-zone-contributor) | Lehetővé teszi az Azure DNS-beli DNS-zónák és rekordkészletek kezelését, de nem teszi lehetővé az azokhoz hozzáférő felhasználók felügyeletét. |
| [DocumentDB-Fiókközreműködő](#documentdb-account-contributor) | Kezelheti az Azure Cosmos DB-fiókokhoz. Az Azure Cosmos DB DocumentDB nevén. |
| [HDInsight Domain Services-közreműködő](#hdinsight-domain-services-contributor) | Olvashatja, létrehozása, módosítása és törlése a tartományi szolgáltatások kapcsolódó műveletek HDInsight vállalati biztonsági csomag szükséges |
| [Intelligens rendszerek Fiókközreműködője](#intelligent-systems-account-contributor) | Lehetővé teszi az intelligens rendszerek fiókjainak kezelését, az azokhoz való hozzáférés nélkül. |
| [Key Vault-közreműködő](#key-vault-contributor) | Lehetővé teszi a kulcstartók kezelését, de ezekhez nem biztosít hozzáférést. |
| [Lab Creator](#lab-creator) | Felügyelt tesztkörnyezetek az Azure Lab-fiókokban való létrehozását, kezelését és törlését teszi lehetővé. |
| [Log Analytics-közreműködő](#log-analytics-contributor) | Log Analytics-közreműködő az összes monitorozási adat olvashatja és szerkesztheti a figyelési beállításokat. Figyelési beállítások szerkesztése magában foglalja a virtuális gépek; a Virtuálisgép-bővítmény hozzáadása tudni naplógyűjtemény konfigurálása céljából az Azure Storage; tárfiók kulcsainak beolvasását létrehozásáról és konfigurálásáról az Automation-fiókok; megoldások hozzáadását; és az Azure diagnostics konfigurálása az összes Azure-erőforrást. |
| [Log Analytics olvasó](#log-analytics-reader) | A Log Analytics-olvasó megtekintheti az összes figyelési adatot, és kereshet azokban, valamint megtekintheti a figyelési beállításokat, beleértve az összes Azure-erőforrás Azure-beli diagnosztikájának konfigurációját is. |
| [A logikai alkalmazás Közreműködője](#logic-app-contributor) | Lehetővé teszi logikai alkalmazások kezelését, de ezekhez nem biztosít hozzáférést. |
| [Logikai alkalmazás operátora](#logic-app-operator) | Lehetővé teszi logikai alkalmazások olvasását, engedélyezését és letiltását. |
| [Felügyelt alkalmazás operátori szerepkör](#managed-application-operator-role) | Olvassa el, és műveleteket hajthat végre felügyelt alkalmazásokra vonatkozó erőforrásokhoz |
| [Felügyelt alkalmazások Adatolvasó](#managed-applications-reader) | Olvassa el a felügyelt alkalmazás és igény szerinti hozzáférés kérése az erőforrások teszi lehetővé. |
| [Felügyelt identitások Közreműködője](#managed-identity-contributor) | A felhasználóhoz hozzárendelt identitás létrehozása, olvasása, frissítése és törlése |
| [Felügyelt identitások üzemeltetője](#managed-identity-operator) | A felhasználóhoz hozzárendelt identitás olvasása és hozzárendelése |
| [A felügyeleti csoport Közreműködője](#management-group-contributor) | Felügyeleti csoport közreműködői szerepköre |
| [A felügyeleti csoport olvasó](#management-group-reader) | Felügyeleti csoport olvasói szerepköre |
| [Közreműködő figyelése](#monitoring-contributor) | Az összes monitorozási adat olvashatja és szerkesztheti a figyelési beállításokat. Lásd még: [szerepkörök, engedélyek és biztonság az Azure Monitor használatának első lépései](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Figyelési metrikák közzétevő](#monitoring-metrics-publisher) | Lehetővé teszi, hogy metrikákat az Azure-erőforrások közzététele |
| [Olvasó figyelése](#monitoring-reader) | Olvashatja az összes figyelési adatot (metrikákat, naplókat, stb.). Lásd még: [szerepkörök, engedélyek és biztonság az Azure Monitor használatának első lépései](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Hálózati közreműködő](#network-contributor) | Lehetővé teszi a hálózatok kezelését, az azokhoz való hozzáférés nélkül. |
| [Új Relic APM-Fiókközreműködő](#new-relic-apm-account-contributor) | Lehetővé teszi a New Relic Application Performance Management-fiókok és -alkalmazások kezelését, az azokhoz való hozzáférés nélkül. |
| [PowerApps-rendszergazdája teszt](#powerapps-administrator-test) | Lehetővé teszi az Azure DNS-beli DNS-zónák és rekordkészletek kezelését, de nem teszi lehetővé az azokhoz hozzáférő felhasználók felügyeletét. |
| [Olvasó és adatelérés](#reader-and-data-access) | Lehetővé teszi minden megtekintését, de nem teszi lehetővé, törölheti, vagy hozzon létre egy storage-fiók vagy az abban található erőforrást. Olvasási/írási hozzáférést a tárfiókok kulcsainak elérésével storage-fiókban tárolt adatokat is engedélyezi. |
| [Redis gyorsítótárak Közreműködője](#redis-cache-contributor) | Lehetővé teszi a Redis-gyorsítótárak kezelését, az azokhoz való hozzáférés nélkül. |
| [Erőforrás-szabályzati közreműködő (előzetes verzió)](#resource-policy-contributor-preview) | (Előnézet) Erőforrás-szabályzat létrehozására/módosítására, támogatási jegy létrehozására, valamint erőforrások/hierarchia beolvasására jogosultsággal rendelkező, EA-beli visszatöltött felhasználók. |
| [A Scheduler Feladatgyűjteményeinek Közreműködője](#scheduler-job-collections-contributor) | Lehetővé teszi a Scheduler-feladatgyűjtemények kezelését, az azokhoz való hozzáférés nélkül. |
| [Search szolgáltatás Közreműködője](#search-service-contributor) | Lehetővé teszi a keresési szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. |
| [Biztonsági rendszergazda](#security-admin) | A Security Center csak: megtekintheti biztonsági házirendek, biztonsági állapotot, szerkessze a biztonsági szabályzatok, riasztások megtekintése és javaslatok, riasztások és javaslatok elvetése |
| [Biztonságkezelő](#security-manager) | Lehetővé teszi a biztonsági összetevők, a biztonsági szabályzatok és a virtuális gépek kezelését |
| [Biztonsági olvasó](#security-reader) | A Security Center csak: megtekintheti a javaslatok és riasztások, a biztonsági házirendek, a biztonsági állapotot, de nem végezhet módosításokat megtekintése |
| [Site Recovery-közreműködő](#site-recovery-contributor) | Lehetővé teszi a Site Recovery szolgáltatás felügyeletét, kivéve a tárolók létrehozását és a szerepkör-hozzárendelést |
| [Site Recovery-operátor](#site-recovery-operator) | Lehetővé teszi a feladatátvételt és a feladat-visszavételt, de nem biztosít egyéb Site Recovery-beli felügyeleti műveleteket |
| [Site Recovery-olvasó](#site-recovery-reader) | Lehetővé teszi a Site Recovery állapotának megtekintését, de nem biztosít egyéb felügyeleti műveleteket |
| [SQL-Adatbázisok Közreműködője](#sql-db-contributor) | Lehetővé teszi az SQL Database-adatbázisok kezelését, ezekhez nem biztosít hozzáférést. Emellett a biztonsággal kapcsolatos házirendjeiket vagy a szülő SQL Server nem tudja kezelni. |
| [SQL Security Manager](#sql-security-manager) | Lehetőséget nyújt az SQL-kiszolgálók és adatbázisok biztonsági házirendjeinek felügyeletére az azokhoz való hozzáférés nélkül. |
| [Az SQL Server Közreműködője](#sql-server-contributor) | Lehetőséget nyújt az SQL-kiszolgálók és adatbázisok felügyeletére az azokhoz való hozzáférés nélkül. Az adatbázisok biztonsági házirendjeinek felügyeletét nem teszi lehetővé. |
| [Tárfiók-közreműködő](#storage-account-contributor) | Lehetővé teszi tárfiókok kezelését, de ezekhez nem biztosít hozzáférést. |
| [Tárolási fiók fő operátora – szolgáltatási szerepkör](#storage-account-key-operator-service-role) | A tárfiók kulcsának operátorai jogosultak a tárfiókokhoz tartozó kulcsok listázására és újragenerálására |
| [Storage-Blobadatok Közreműködője (előzetes verzió)](#storage-blob-data-contributor-preview) | Azure Storage-blobtárolók és -adatok olvasási, írási és törlési hozzáférésének engedélyezése |
| [Storage-Blobadatok olvasója (előzetes verzió)](#storage-blob-data-reader-preview) | Azure Storage-blobtárolók és -adatok olvasási hozzáférésének engedélyezése |
| [Storage-Üzenetsorbeli adatok Közreműködője (előzetes verzió)](#storage-queue-data-contributor-preview) | Azure Storage-üzenetsorok és üzenetsorbeli üzenetek olvasási, írási és törlési hozzáférésének engedélyezése |
| [Storage-Üzenetsorbeli adatok olvasója (előzetes verzió)](#storage-queue-data-reader-preview) | Azure Storage-üzenetsorok és üzenetsorbeli üzenetek olvasási hozzáférésének engedélyezése |
| [Támogatáskérési közreműködő](#support-request-contributor) | Támogatási kérések létrehozását és kezelését teszi lehetővé |
| [Traffic Manager-közreműködő](#traffic-manager-contributor) | Lehetővé teszi Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz hozzáférő felhasználók felügyeletét. |
| [Felhasználói hozzáférés rendszergazdája](#user-access-administrator) | Lehetővé teszi a Azure-erőforrásokhoz való felhasználói hozzáférés kezelését. |
| [A virtuális gépre való rendszergazdai bejelentkezés](#virtual-machine-administrator-login) | A portálon, és jelentkezzen be rendszergazdai virtuális számítógépek megtekintése |
| [Virtuális gépek közreműködője](#virtual-machine-contributor) | Lehetővé teszi a virtuális gépek kezelését, de ezekhez nem biztosít hozzáférést, és nem teszi lehetővé a virtuális gépekhez hozzárendelt virtuális hálózatok és tárfiókok elérését sem. |
| [A virtuális gépre való felhasználói bejelentkezés](#virtual-machine-user-login) | A portálon és a felhasználói bejelentkezési nézet a virtuális gépek. |
| [Webes Tarifacsomagok Közreműködője](#web-plan-contributor) | Lehetővé teszi a webes tarifacsomagok kezelését, az azokhoz való hozzáférés nélkül. |
| [Webhelyek Közreműködője](#website-contributor) | Lehetővé teszi a webhelyek kezelését (a webes tarifacsomagokét azonban nem), az azokhoz való hozzáférés nélkül. |


## <a name="owner"></a>Tulajdonos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az összes funkció kezelését, beleértve az erőforrásokhoz való hozzáférést is. |
> | **Azonosító** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Műveletek** |  |
> | * | Bármilyen típusú erőforrások létrehozása és felügyelete |

## <a name="contributor"></a>Közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az összes funkció kezelését, kivéve az erőforrásokhoz való hozzáférést. |
> | **Azonosító** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Műveletek** |  |
> | * | Bármilyen típusú erőforrások létrehozása és felügyelete |
> | **notActions** |  |
> | Microsoft.Authorization/*/Delete | Nem lehet törölni a szerepkörök és szerepkör-hozzárendelések |
> | Microsoft.Authorization/*/Write | Szerepkörök és szerepkör-hozzárendelések nem hozható létre |
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
> | * / olvasási | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |

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
> | **Leírás** | Lehetővé teszi az API Management-szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/* | Hozzon létre, és az API Management szolgáltatás kezelése |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="api-management-service-operator-role"></a>Az API Management szolgáltatás operátori szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Szolgáltatásokat kezelhet, de API-kat nem |
> | **Azonosító** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/*/read | Olvassa el API Management szolgáltatás példányai |
> | Microsoft.ApiManagement/service/backup/action | Biztonsági mentési API Management szolgáltatás az adott tárolóba, a felhasználó a megadott tárfiók |
> | Microsoft.ApiManagement/service/delete | API Management szolgáltatás-példány törlése |
> | Microsoft.ApiManagement/service/managedeployments/action | Módosítsa az SKU/egység, API Management szolgáltatás hozzáadása/eltávolítása regionálisan üzemelő példányokba |
> | Microsoft.ApiManagement/service/read | Egy API Management szolgáltatáspéldány metaadatok olvasása |
> | Microsoft.ApiManagement/service/restore/action | A megadott tároló, a felhasználó által megadott tárfiókot az API Management szolgáltatás visszaállítása |
> | Microsoft.ApiManagement/service/updatecertificate/action | Az API Management szolgáltatás SSL-tanúsítvány feltöltése |
> | Microsoft.ApiManagement/service/updatehostname/action | A telepítő, frissítenie vagy eltávolíthat egyéni tartományneveket egy API Management szolgáltatás |
> | Microsoft.ApiManagement/service/write | API Management szolgáltatás új példányának létrehozása |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Felhasználói kulcsok listájának beolvasása |

## <a name="api-management-service-reader-role"></a>Az API Management szolgáltatás olvasói szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Írásvédett hozzáférés a szolgáltatáshoz és az API-khoz |
> | **Azonosító** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/*/read | Olvassa el API Management szolgáltatás példányai |
> | Microsoft.ApiManagement/service/read | Egy API Management szolgáltatáspéldány metaadatok olvasása |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Felhasználói kulcsok listájának beolvasása |

## <a name="application-insights-component-contributor"></a>Application Insights-összetevők közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelhet Application Insights-összetevőket |
> | **Azonosító** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.Insights/components/* | Létrehozása és kezelése az Insights-összetevők |
> | Microsoft.Insights/webtests/* | Létrehozása és kezelése a webes tesztek |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyt ad a felhasználónak megtekintése és letöltése az Application Insights Snapshot Debugger az gyűjtött hibakeresési pillanatképek. Vegye figyelembe, hogy ezeket az engedélyeket nem szerepelnek a [tulajdonosa](#owner) vagy [közreműködői](#contributor) szerepköröket. |
> | **Azonosító** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="automation-job-operator"></a>Az Automation-feladat operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Feladatok létrehozása és kezelése Automation-runbookok használatával. |
> | **Azonosító** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hibrid forgatókönyv-feldolgozó erőforrások beolvasása |
> | Microsoft.Automation/automationAccounts/jobs/read | Egy Azure Automation-feladat beolvasása |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Egy Azure Automation-feladat folytatása |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Egy Azure Automation-feladat leállítása |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Egy Azure Automation-feladatstream beolvasása |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Egy Azure Automation-feladat felfüggesztése |
> | Microsoft.Automation/automationAccounts/jobs/write | Azure Automation-feladat létrehozása |
> | Microsoft.Automation/automationAccounts/jobs/output/read | A feladat kimenetének beolvasása |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="automation-operator"></a>Automation-operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az Automation-operátorok elindíthatnak, leállíthatnak, felfüggeszthetnek és folytathatnak feladatokat |
> | **Azonosító** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hibrid forgatókönyv-feldolgozó erőforrások beolvasása |
> | Microsoft.Automation/automationAccounts/jobs/read | Egy Azure Automation-feladat beolvasása |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Egy Azure Automation-feladat folytatása |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Egy Azure Automation-feladat leállítása |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Egy Azure Automation-feladatstream beolvasása |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Egy Azure Automation-feladat felfüggesztése |
> | Microsoft.Automation/automationAccounts/jobs/write | Azure Automation-feladat létrehozása |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Azure Automation-feladatütemezés beolvasása |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Létrehoz egy Azure Automation-feladatütemezés |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | A munkaterület társítva, az automation-fiók beolvasása |
> | Microsoft.Automation/automationAccounts/read | Azure Automation-fiók beolvasása |
> | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation-runbook beolvasása |
> | Microsoft.Automation/automationAccounts/schedules/read | Egy Azure Automation szolgáltatásbeli ütemezési eszköz beolvasása |
> | Microsoft.Automation/automationAccounts/schedules/write | Létrehoz vagy frissít egy Azure Automation szolgáltatásbeli ütemezési eszköz |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Automation/automationAccounts/jobs/output/read | A feladat kimenetének beolvasása |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="automation-runbook-operator"></a>Az Automation-runbook operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Runbook-tulajdonságok olvasása – a runbook-feladatok létrehozásához. |
> | **Azonosító** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation-runbook beolvasása |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Az Azure Kubernetes Service-fürt rendszergazdai szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Fürt rendszergazdai hitelesítő adatok a művelet listázza. |
> | **Azonosító** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Műveletek** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Egy felügyelt fürt a clusterAdmin hitelesítő adatainak listázása |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Az Azure Kubernetes Service-fürt felhasználói szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Fürt felhasználói hitelesítő adatok a művelet listázza. |
> | **Azonosító** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Műveletek** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Egy felügyelt fürt a clusterUser hitelesítő adatainak listázása |

## <a name="azure-stack-registration-owner"></a>Azure Stack-regisztráció tulajdonosa
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az Azure Stack-regisztrációk kezelését. |
> | **Azonosító** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Műveletek** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Kiterjesztett az Azure Stack piactéren termék részleteinek beolvasása |
> | Microsoft.AzureStack/registrations/products/read | Az Azure Stack piactéren termék tulajdonságainak beolvasása |
> | Microsoft.AzureStack/registrations/read | Az Azure Stack-regisztrációk tulajdonságainak beolvasása |

## <a name="backup-contributor"></a>Biztonsági mentési közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a biztonsági mentési szolgáltatás felügyeletét, de nem hozhat létre tárolókat, és nem adhat hozzáférést másoknak |
> | **Azonosító** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózati definíció beolvasása |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | A biztonságimásolat-kezelési művelet eredmények kezelése |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Hozzon létre, és a helyreállítási tár biztonsági mentési hálók belüli biztonsági mentési tárolók kezelése |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Feladatexportálási művelet eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Hozzon létre, és biztonsági másolatokat kezelő kapcsolatos metaadatok kezelése |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Hozzon létre, és biztonsági másolatokat kezelő operations eredményeinek kezelése |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Biztonsági mentési szabályzatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Cikkek, amely biztonsági mentésre alkalmas létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Létrehozni és kezelni a biztonsági másolati elem |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Létre és felügyelhet tárolókat a tároló biztonsági másolati elemek |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Használati összegzéseket adja vissza a védett elemek és védett kiszolgálók egy Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Biztonsági mentés a Recovery Services-tároló kapcsolódó tanúsítványok létrehozásához és felügyeletéhez |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Létrehozásához és kezeléséhez kapcsolódó tároló kiterjesztett adatok |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a riasztások a Recovery services-tárolót. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi egy Azure-erőforrásokra "tároló" típusú objektum |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | A tárolólista |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Hozzon létre és a regisztrált felhasználók kezelése |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/usages/* | Hozzon létre, és a Recovery Services-tároló használata kezelheti |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="backup-operator"></a>Biztonsági mentési operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a biztonsági mentési szolgáltatások felügyeletét, kivéve a biztonsági másolatok eltávolítását, tárolók létrehozását és a másoknak való hozzáférés megadását |
> | **Azonosító** | 00c29273-979b-4161-815C-10b084fb9324 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózati definíció beolvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotának visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Beolvassa a védelmi tárolón végrehajtott művelet eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Biztonsági másolat készítése egy védett elemről. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Beolvassa a védett elemeken végrehajtott művelet eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem objektumadatainak visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Kiépítés azonnali Elemhelyreállításának védett elem |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Védett elemek helyreállítási pontjainak beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Védett elemek helyreállítási pontjainak visszaállítása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Védett elem azonnali visszavonása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | A védett elem biztonsági másolatának létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Az összes regisztrált tároló visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | A feladat megszakítása |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | A feladatművelet eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Összes feladatobjektum visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Feladatexportálási művelet eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | A Recovery Services-tároló biztonságimásolat-kezelési metaadatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Hozzon létre, és biztonsági másolatokat kezelő operations eredményeinek kezelése |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A szabályzatművelet eredményeinek beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Összes védelmi szabályzat visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Cikkek, amely biztonsági mentésre alkalmas létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | A védhető elemek listáját adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | A védett elemek listájának beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tároló visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Használati összegzéseket adja vissza a védett elemek és védett kiszolgálók egy Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tár hitelesítőadat-tanúsítványa. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a riasztások a Recovery services-tárolót. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi egy Azure-erőforrásokra "tároló" típusú objektum |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | A tárolólista |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Művelet eredményeinek beolvasása művelettel olvashatók a műveleti állapota és az aszinkron módon elküldött művelet eredményének beolvasása |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel az erőforráshoz regisztrált tárolók beolvasása. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A szolgáltatástároló regisztrálása művelettel egy tároló regisztrálni a helyreállítási szolgáltatás használható. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="backup-reader"></a>Biztonsági mentési olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a biztonsági mentési szolgáltatásokat, de nem végezhet módosításokat |
> | **Azonosító** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotának visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Beolvassa a védelmi tárolón végrehajtott művelet eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Beolvassa a védett elemeken végrehajtott művelet eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem objektumadatainak visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Az összes regisztrált tároló visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | A feladatművelet eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Összes feladatobjektum visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | A Recovery Services-tároló biztonságimásolat-kezelési metaadatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | A Recovery Services-tárolóval kapcsolatos biztonsági mentés eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A szabályzatművelet eredményeinek beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Összes védelmi szabályzat visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | A védett elemek listájának beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tároló visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Használati összegzéseket adja vissza a védett elemek és védett kiszolgálók egy Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi egy Azure-erőforrásokra "tároló" típusú objektum |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Művelet eredményeinek beolvasása művelettel olvashatók a műveleti állapota és az aszinkron módon elküldött művelet eredményének beolvasása |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel az erőforráshoz regisztrált tárolók beolvasása. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a riasztások a Recovery services-tárolót. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Védett elemek helyreállítási pontjainak beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Feladatexportálási művelet eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |

## <a name="billing-reader"></a>Számlázás olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a számlázási adatok olvasását |
> | **Azonosító** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Billing/*/read | Olvassa el a számlázási adatokat |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportok listája. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="biztalk-contributor"></a>BizTalk közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a BizTalk-szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.BizTalkServices/BizTalk/* | A BizTalk-szolgáltatások létrehozásához és kezeléséhez |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="cdn-endpoint-contributor"></a>A CDN-végpont közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelhet CDN-végpontokat, de nem tud hozzáférést adni más felhasználóknak. |
> | **Azonosító** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="cdn-endpoint-reader"></a>A CDN-végpont olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a CDN-végpontokat, de nem végezhet módosításokat. |
> | **Azonosító** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="cdn-profile-contributor"></a>A CDN-profil közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a CDN-profilokat és azok végpontjait, de nem tud hozzáférést adni más felhasználóknak. |
> | **Azonosító** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="cdn-profile-reader"></a>A CDN-profil olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a CDN-profilokat és azok végpontjait, de nem végezhet módosításokat. |
> | **Azonosító** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="classic-network-contributor"></a>Virtuális hálózatok hagyományos közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a hagyományos hálózatok kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.ClassicNetwork/* | Hozzon létre, és a klasszikus hálózatok kezelése |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="classic-storage-account-contributor"></a>Hagyományos tárfiók-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a hagyományos tárfiókok kezelését, de ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.ClassicStorage/storageAccounts/* | Storage-fiókok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

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
> | Microsoft.ClassicCompute/domainNames/* | Hozzon létre, és a klasszikus számítási tartománynevek kezelése |
> | Microsoft.ClassicCompute/virtualMachines/* | Virtuális gépek létrehozása és kezelése |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Egy fenntartott IP-cím hivatkozása |
> | Microsoft.ClassicNetwork/reservedIps/read | A fenntartott IP-címek beolvasása |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Csatlakozás a virtuális hálózathoz. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Virtuális hálózat beolvasása. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | A tárfióklemez beolvasása. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | A tárfióklemezkép adja vissza. (Elavult. Használja a "Microsoft.ClassicStorage/storageAccounts/vmImages") |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Tárfiókok elérési kulcsainak listázása. |
> | Microsoft.ClassicStorage/storageAccounts/read | Tárfiók visszaadása az adott fiókhoz. |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="cognitive-services-contributor"></a>A cognitive Services-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy létrehozása, olvasása, frissítése, törlése és a Cognitive Services, kulcsok kezelése. |
> | **Azonosító** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Beolvassa az előfizetéshez tartozó szolgáltatásokat. |
> | Microsoft.Features/providers/features/read | Beolvassa az előfizetés szolgáltatásait az adott erőforrás-szolgáltatón. |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Hoz létre, a frissítések vagy az Analysis Server diagnosztikai beállításának beolvasása |
> | Microsoft.Insights/logDefinitions/read | A naplódefiníciók olvasása |
> | Microsoft.Insights/metricdefinitions/read | A metrikadefiníciók olvasása |
> | Microsoft.Insights/metrics/read | Metrikák olvasása |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
> | Microsoft.Resources/subscriptions/read | Beolvassa az előfizetések listáját. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="cognitive-services-user"></a>A cognitive Services-felhasználó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy Ön elolvassa és a Cognitive Services, kulcsok listázását. |
> | **Azonosító** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Műveletek** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Kulcsok listázása |
> | Microsoft.Insights/metricdefinitions/read | A metrikadefiníciók olvasása |
> | Microsoft.Insights/metrics/read | Metrikák olvasása |
> | Microsoft.Insights/alertRules/read | Klasszikus metrikaalapú riasztás olvasása |
> | Microsoft.Insights/diagnosticSettings/read | Erőforrás diagnosztikai beállításának olvasása |
> | Microsoft.Insights/logDefinitions/read | A naplódefiníciók olvasása |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
> | Microsoft.Resources/subscriptions/read | Beolvassa az előfizetések listáját. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB-fiók olvasói szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvashatja az Azure Cosmos DB-fiókja adatait. Lásd: [DocumentDB-Fiókközreműködő](#documentdb-account-contributor) kezeléséhez az Azure Cosmos DB-fiókokhoz. |
> | **Azonosító** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendeléseket, olvashatja az egyes felhasználói engedélyeket |
> | Microsoft.DocumentDB/*/read | Minden gyűjtemény olvasása |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Az adatbázis csak olvasható kulcsainak beolvasása. |
> | Microsoft.Insights/MetricDefinitions/read | A metrikadefiníciók olvasása |
> | Microsoft.Insights/Metrics/read | Metrikák olvasása |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="cost-management-contributor"></a>A Cost Management Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Költségek megtekintheti és kezelheti a költség-konfiguráció (pl. költségvetéseket, export) |
> | **Azonosító** | 434105ed-43f6-45C7-a02f-909b2ba83430 |
> | **Műveletek** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read | Felsorolja a rendelkezésre álló elszámolási időszakok |
> | Microsoft.Resources/subscriptions/read | Beolvassa az előfizetések listáját. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="cost-management-reader"></a>A Cost Management olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Költségadatok és a konfiguráció (pl. költségvetéseket, export) |
> | **Azonosító** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Műveletek** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read | Felsorolja a rendelkezésre álló elszámolási időszakok |
> | Microsoft.Resources/subscriptions/read | Beolvassa az előfizetések listáját. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="data-box-contributor"></a>Data Box Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a Data Box szolgáltatás található minden elemet kivételével másoknak való hozzáférés megadását teszi lehetővé. |
> | **Azonosító** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Databox/* |  |

## <a name="data-box-reader"></a>Data Box-olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a Data Box szolgáltatás kivéve a rendelés létrehozása vagy szerkesztése a rendelés részleteit, és a másoknak való hozzáférés megadását teszi lehetővé. |
> | **Azonosító** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | A megrendeléssel kapcsolatos titkosítatlan hitelesítő adatok listája. |
> | Microsoft.Databox/locations/availableSkus/action | Ez a metódus a rendelkezésre álló termékváltozatok listáját adja vissza. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="data-factory-contributor"></a>Data Factory közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az adat-előállítók kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 673868aa-7521-48A0-acc6-0f60742d39f5 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.DataFactory/dataFactories/* | Létrehozhat és kezelhet adat-előállítók és a bennük található gyermekerőforrásait. |
> | Microsoft.DataFactory/factories/* | Létrehozhat és kezelhet adat-előállítók és a bennük található gyermekerőforrásait. |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics-fejlesztő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a saját feladatok elküldését, figyelését és kezelését, de nem tud létrehozni vagy törölni Data Lake Analytics-fiókokat. |
> | **Azonosító** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | DataLakeAnalytics fiók törlése. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Más felhasználók által beküldött feladatok megszakítása engedélyezheti. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Hozzon létre, vagy egy DataLakeAnalytics fiók frissítéséhez. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Hozzon létre vagy nem frissíthető a DataLakeAnalytics fiók társított Data Lake Store-fiók. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Egy DataLakeAnalytics fiókot egy Data Lake Store-fiók leválasztása. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Hozzon létre vagy nem frissíthető a DataLakeAnalytics fiók társított Storage-fiók. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | A DataLakeAnalytics fiók egy Storage-fiók leválasztása. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Létrehozása vagy frissítése egy tűzfalszabályt. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Tűzfalszabály törlése. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Hozzon létre, vagy egy számítási szabályzat frissítése. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Compute-házirend törlése. |

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
> | Microsoft.OperationalInsights/workspaces/purge/action | Megadott adatok törlése a munkaterületről |

## <a name="devtest-labs-user"></a>DevTest Labs-felhasználó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az Azure DevTest Labs virtuális gépeinek csatlakoztatását, indítását, újraindítását és leállítását. |
> | **Azonosító** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.Compute/availabilitySets/read | Egy adott rendelkezésre állási csoport tulajdonságainak beolvasása |
> | Microsoft.Compute/virtualMachines/*/read | A virtuális gépek (VM-méretek, futásidejű állapot, a Virtuálisgép-bővítmények, stb.) tulajdonságainak olvasása |
> | Microsoft.Compute/virtualMachines/deallocate/action | Kikapcsolja a virtuális gépet és felszabadítja a számítási erőforrásokat |
> | Microsoft.Compute/virtualMachines/read | Egy virtuális gép tulajdonságait olvassa be |
> | Microsoft.Compute/virtualMachines/restart/action | Újraindítja a virtuális gépet |
> | Microsoft.Compute/virtualMachines/start/action | Elindítja a virtuális gépet |
> | Microsoft.DevTestLab/*/read | Labor tulajdonságainak olvasása |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Virtuális gépek létrehozása a lab-ben. |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Jogcím egy véletlenszerű igényelhető virtuális gép a tesztkörnyezetben. |
> | Microsoft.DevTestLab/labs/formulas/delete | Törölje a képletek. |
> | Microsoft.DevTestLab/labs/formulas/read | Olvassa el a képletek. |
> | Microsoft.DevTestLab/labs/formulas/write | Adjon hozzá vagy módosíthatja a képleteket. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Kiértékeli a labor házirend. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Meglévő virtuális gép tulajdonjogának átvétele |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | A load balancer háttércímkészlet csatlakozik |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Csatlakozik a terheléselosztó bejövő nat-szabály |
> | Microsoft.Network/networkInterfaces/*/read | Hálózati adapter (például az összes a terheléselosztók, amely a hálózati adapter egy része) tulajdonságainak olvasása |
> | Microsoft.Network/networkInterfaces/join/action | Csatlakoztatja a virtuális gépek hálózati adapter |
> | Microsoft.Network/networkInterfaces/read | Lekérdezi egy hálózati illesztőjének definícióját.  |
> | Microsoft.Network/networkInterfaces/write | Hálózati adapter létrehozása vagy frissítése egy meglévő hálózati adaptert.  |
> | Microsoft.Network/publicIPAddresses/*/read | Nyilvános IP-cím tulajdonságainak olvasása |
> | Microsoft.Network/publicIPAddresses/join/action | Nyilvános ip-címmel csatlakozik |
> | Microsoft.Network/publicIPAddresses/read | Lekérdezi egy nyilvános IP-cím cím definíciója. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Virtuális hálózat csatlakozik |
> | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
> | Microsoft.Resources/deployments/read | Beolvassa vagy listázza az üzemelő példányokat. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
> | **notActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Listázza az elérhető méreteket, melyekre a virtuális gép frissíthető |

## <a name="dns-zone-contributor"></a>A DNS-zóna közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az Azure DNS-beli DNS-zónák és rekordkészletek kezelését, de nem teszi lehetővé az azokhoz hozzáférő felhasználók felügyeletét. |
> | **Azonosító** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.Network/dnsZones/* | DNS-zónák és rekordok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="documentdb-account-contributor"></a>DocumentDB-fiókközreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti az Azure Cosmos DB-fiókokhoz. Az Azure Cosmos DB DocumentDB nevén. |
> | **Azonosító** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB-fiókok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="hdinsight-domain-services-contributor"></a>HDInsight Domain Services-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvashatja, létrehozása, módosítása és törlése a tartományi szolgáltatások kapcsolódó műveletek HDInsight vállalati biztonsági csomag szükséges |
> | **Azonosító** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Műveletek** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |

## <a name="intelligent-systems-account-contributor"></a>Intelligens rendszerek fiókközreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az intelligens rendszerek fiókjainak kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.IntelligentSystems/accounts/* | Intelligens rendszerek fiókok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="key-vault-contributor"></a>Key Vault-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a kulcstartók kezelését, de ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Helyreállíthatóan törölt kulcstartó végleges törlése |
> | Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Tesztkörnyezet létrehozója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felügyelt tesztkörnyezetek az Azure Lab-fiókokban való létrehozását, kezelését és törlését teszi lehetővé. |
> | **Azonosító** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Labor létrehozása a lab-fiókokban. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Tesztkörnyezetfiók konfigurált mérete kategóriákhoz tartozó régiónkénti rendelkezésre állás információk lekérése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="log-analytics-contributor"></a>Log Analytics közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Log Analytics-közreműködő az összes monitorozási adat olvashatja és szerkesztheti a figyelési beállításokat. Figyelési beállítások szerkesztése magában foglalja a virtuális gépek; a Virtuálisgép-bővítmény hozzáadása tudni naplógyűjtemény konfigurálása céljából az Azure Storage; tárfiók kulcsainak beolvasását létrehozásáról és konfigurálásáról az Automation-fiókok; megoldások hozzáadását; és az Azure diagnostics konfigurálása az összes Azure-erőforrást. |
> | **Azonosító** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Műveletek** |  |
> | * / olvasási | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Tárfiókok elérési kulcsainak listázása. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Hoz létre, a frissítések vagy az Analysis Server diagnosztikai beállításának beolvasása |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="log-analytics-reader"></a>Log Analytics olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A Log Analytics-olvasó megtekintheti az összes figyelési adatot, és kereshet azokban, valamint megtekintheti a figyelési beállításokat, beleértve az összes Azure-erőforrás Azure-beli diagnosztikájának konfigurációját is. |
> | **Azonosító** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Műveletek** |  |
> | * / olvasási | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Új motor használatával való keresés. |
> | Microsoft.OperationalInsights/workspaces/search/action | Végrehajt egy keresési lekérdezést |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Lekéri a munkaterület a megosztott kulcsok. Ezekkel a kulcsokkal a Microsoft Operational Insights-ügynökök kapcsolódni a munkaterülethez. |

## <a name="logic-app-contributor"></a>A logikai alkalmazás közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi logikai alkalmazások kezelését, de ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Tárfiókok elérési kulcsainak listázása. |
> | Microsoft.ClassicStorage/storageAccounts/read | Tárfiók visszaadása az adott fiókhoz. |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Hoz létre, a frissítések vagy az Analysis Server diagnosztikai beállításának beolvasása |
> | Microsoft.Insights/logdefinitions/* | Erre az engedélyre szükség a felhasználók számára, akik hozzáférhetnek a vizsgálati naplók a portálon keresztül. Lista naplókategóriák a tevékenységnaplóban. |
> | Microsoft.Insights/metricDefinitions/* | (Erőforrás rendelkezésre álló metrika típusok listája) metrikadefiníciók olvasása. |
> | Microsoft.Logic/* | A Logic Apps-erőforrások kezelése. |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
> | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Web/connectionGateways/* | Hozzon létre és kezeli a kapcsolódási átjáró. |
> | Microsoft.Web/connections/* | Hozzon létre és kezeli a kapcsolat. |
> | Microsoft.Web/customApis/* | Hozza létre, és a egy egyéni API-t felügyeli. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Az App Service-csomag tulajdonságainak beolvasása |
> | Microsoft.Web/sites/functions/listSecrets/action | Lista titkok Web Apps-függvényekre. |

## <a name="logic-app-operator"></a>Logikai alkalmazás operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi logikai alkalmazások olvasását, engedélyezését és letiltását. |
> | **Azonosító** | 515c2055-d9d4-4321-B1B9-bd0c9a0f79fe |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/*/read | Olvasási Insights – riasztási szabályok |
> | Microsoft.Insights/diagnosticSettings/*/read | A Logic Apps diagnosztikai beállításainak beolvasása |
> | Microsoft.Insights/metricDefinitions/*/read | A Logic Apps elérhető metrikáinak beolvasása. |
> | Microsoft.Logic/*/read | A Logic Apps-erőforrások beolvasása. |
> | Microsoft.Logic/workflows/disable/action | Letiltja a munkafolyamatot. |
> | Microsoft.Logic/workflows/enable/action | Engedélyezi a munkafolyamatot. |
> | Microsoft.Logic/workflows/validate/action | Érvényesíti a munkafolyamatot. |
> | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Web/connectionGateways/*/read | Olvassa el a kapcsolódási átjáró. |
> | Microsoft.Web/connections/*/read | Olvassa el a kapcsolatot. |
> | Microsoft.Web/customApis/*/read | Olvassa el az egyéni API-t. |
> | Microsoft.Web/serverFarms/read | Az App Service-csomag tulajdonságainak beolvasása |

## <a name="managed-application-operator-role"></a>Felügyelt alkalmazás operátori szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvassa el, és műveleteket hajthat végre felügyelt alkalmazásokra vonatkozó erőforrásokhoz |
> | **Azonosító** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Műveletek** |  |
> | Microsoft.Solutions/applications/read | Az alkalmazások listájának beolvasása. |

## <a name="managed-applications-reader"></a>Felügyelt alkalmazások Adatolvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvassa el a felügyelt alkalmazás és igény szerinti hozzáférés kérése az erőforrások teszi lehetővé. |
> | **Azonosító** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Műveletek** |  |
> | * / olvasási | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Solutions/jitRequests/* |  |

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
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="managed-identity-operator"></a>Felügyelt identitások üzemeltetője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A felhasználóhoz hozzárendelt identitás olvasása és hozzárendelése |
> | **Azonosító** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Műveletek** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="management-group-contributor"></a>Felügyeleti csoport közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felügyeleti csoport közreműködői szerepköre |
> | **Azonosító** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Műveletek** |  |
> | Microsoft.Management/managementGroups/delete | Felügyeleti csoport törlése. |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportok listája. |
> | Microsoft.Management/managementGroups/subscriptions/delete | A felügyeleti csoportból előfizetés megszüntetéséhez társítja. |
> | Microsoft.Management/managementGroups/subscriptions/write | Hozzárendeli a meglévő előfizetés a felügyeleti csoporttal. |
> | Microsoft.Management/managementGroups/write | Hozzon létre, vagy a felügyeleti csoport frissítése. |

## <a name="management-group-reader"></a>Felügyeleti csoport olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felügyeleti csoport olvasói szerepköre |
> | **Azonosító** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Műveletek** |  |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportok listája. |

## <a name="monitoring-contributor"></a>Közreműködő figyelése
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az összes monitorozási adat olvashatja és szerkesztheti a figyelési beállításokat. Lásd még: [szerepkörök, engedélyek és biztonság az Azure Monitor használatának első lépései](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **Azonosító** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Műveletek** |  |
> | * / olvasási | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/AlertRules/* | Olvasási, írási és törlési riasztási szabályok. |
> | Microsoft.Insights/components/* | Olvasási, írási és törlési Application Insights-összetevők. |
> | Microsoft.Insights/DiagnosticSettings/* | Diagnosztikai beállítások olvasása/írása/törlése. |
> | Microsoft.Insights/eventtypes/* | Tevékenységnapló eseményei (felügyeleti események) egy adott előfizetés listázása. Ezzel az engedéllyel csak a tevékenységnaplóban a szoftveres és a portál hozzáférést alkalmazható. |
> | Microsoft.Insights/LogDefinitions/* | Erre az engedélyre szükség a felhasználók számára, akik hozzáférhetnek a vizsgálati naplók a portálon keresztül. Lista naplókategóriák a tevékenységnaplóban. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | (Erőforrás rendelkezésre álló metrika típusok listája) metrikadefiníciók olvasása. |
> | Microsoft.Insights/Metrics/* | Olvassa el a erőforrás metrikáit. |
> | Microsoft.Insights/Register/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Olvasási, írási és törlési Application Insights webes teszteket. |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Olvasási, írási és törlési Log Analytics megoldás csomagok. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | A Log Analytics olvasási, írási és törlési mentett keresések. |
> | Microsoft.OperationalInsights/workspaces/search/action | Végrehajt egy keresési lekérdezést |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Lekéri a munkaterület a megosztott kulcsok. Ezekkel a kulcsokkal a Microsoft Operational Insights-ügynökök kapcsolódni a munkaterülethez. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Olvasási, írási és törlési Log Analytics storage insight konfigurációkat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft.WorkloadMonitor/workloadInsights/* |  |

## <a name="monitoring-metrics-publisher"></a>Figyelési metrikák közzétevő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy metrikákat az Azure-erőforrások közzététele |
> | **Azonosító** | 3913510d-42f4-4E42-8a64-420c390055eb |
> | **Műveletek** |  |
> | Microsoft.Insights/Register/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Metrikák írása |

## <a name="monitoring-reader"></a>Olvasó figyelése
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvashatja az összes figyelési adatot (metrikákat, naplókat, stb.). Lásd még: [szerepkörök, engedélyek és biztonság az Azure Monitor használatának első lépései](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **Azonosító** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Műveletek** |  |
> | * / olvasási | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.OperationalInsights/workspaces/search/action | Végrehajt egy keresési lekérdezést |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="network-contributor"></a>Hálózati közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a hálózatok kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.Network/* | Hozzon létre és hálózatok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="new-relic-apm-account-contributor"></a>New Relic APM-fiókközreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a New Relic Application Performance Management-fiókok és -alkalmazások kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | NewRelic.APM/accounts/* |  |

## <a name="powerapps-administrator-test"></a>PowerApps-rendszergazdája teszt
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az Azure DNS-beli DNS-zónák és rekordkészletek kezelését, de nem teszi lehetővé az azokhoz hozzáférő felhasználók felügyeletét. |
> | **Azonosító** | befefa01-2a29-4897-83a8-272ff33ce314 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Network/dnsZones/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="reader-and-data-access"></a>Olvasó és adatelérés
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi minden megtekintését, de nem teszi lehetővé, törölheti, vagy hozzon létre egy storage-fiók vagy az abban található erőforrást. Olvasási/írási hozzáférést a tárfiókok kulcsainak elérésével storage-fiókban tárolt adatokat is engedélyezi. |
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
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.Cache/redis/* | Hozzon létre, és a redis Cache gyorsítótárak kezelése |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="resource-policy-contributor-preview"></a>Erőforrás-szabályzati közreműködő (előnézet)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | (Előnézet) Erőforrás-szabályzat létrehozására/módosítására, támogatási jegy létrehozására, valamint erőforrások/hierarchia beolvasására jogosultsággal rendelkező, EA-beli visszatöltött felhasználók. |
> | **Azonosító** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Műveletek** |  |
> | * / olvasási | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.Authorization/policyassignments/* | Hozzon létre, és a szabályzat-hozzárendelések kezelése |
> | Microsoft.Authorization/policydefinitions/* | Hozzon létre és kezelhet szabályzatdefiníciókat |
> | Microsoft.Authorization/policysetdefinitions/* | Létrehozása és kezelése a szabályzat beállítása |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="scheduler-job-collections-contributor"></a>Scheduler szolgáltatás feladatgyűjteményeinek közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Scheduler-feladatgyűjtemények kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Scheduler/jobcollections/* | Feladat gyűjtemények létrehozásához és kezeléséhez |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="search-service-contributor"></a>Search szolgáltatás közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a keresési szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Search/searchServices/* | Keresési szolgáltatások létrehozásához és kezeléséhez |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="security-admin"></a>Biztonsági rendszergazda
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A Security Center csak: megtekintheti biztonsági házirendek, biztonsági állapotot, szerkessze a biztonsági szabályzatok, riasztások megtekintése és javaslatok, riasztások és javaslatok elvetése |
> | **Azonosító** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Authorization/policyAssignments/* | Hozzon létre, és a szabályzat-hozzárendelések kezelése |
> | Microsoft.Authorization/policyDefinitions/* | Hozzon létre és kezelhet szabályzatdefiníciókat |
> | Microsoft.Authorization/policySetDefinitions/* | Létrehozása és kezelése a szabályzat beállítása |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportok listája. |
> | Microsoft.operationalInsights/workspaces/*/read | A Log Analytics-adatok megtekintése |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Security/*/read | Olvasási biztonsági összetevőinek és a szabályzatok |
> | Microsoft.Security/locations/alerts/activate/action | Egy biztonsági riasztás aktiválása |
> | Microsoft.Security/locations/alerts/dismiss/action | Egy biztonsági riasztás bezárása |
> | Microsoft.Security/locations/tasks/activate/action | Biztonsági ajánlás aktiválása |
> | Microsoft.Security/locations/tasks/dismiss/action | A biztonsági javaslatok elvetése |
> | Microsoft.Security/policies/write | A biztonsági házirend frissítése |
> | Microsoft.Security/securityContacts/write | Frissíti a biztonsági kapcsolattartó |
> | Microsoft.Security/securityContacts/delete | Törli a biztonsági kapcsolattartó |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="security-manager"></a>Biztonságkezelő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a biztonsági összetevők, a biztonsági szabályzatok és a virtuális gépek kezelését |
> | **Azonosító** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.ClassicCompute/*/read | Olvassa el a konfigurációs adatokat a klasszikus virtuális gépek |
> | Microsoft.ClassicCompute/virtualMachines/*/write | A klasszikus virtuális gépek konfigurációjának írása |
> | Microsoft.ClassicNetwork/*/read | Klasszikus hálózati konfigurációs információkat olvasása |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Security/* | Biztonsági összetevők és a szabályzatok létrehozása és kezelése |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="security-reader"></a>Biztonsági olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A Security Center csak: megtekintheti a javaslatok és riasztások, a biztonsági házirendek, a biztonsági állapotot, de nem végezhet módosításokat megtekintése |
> | **Azonosító** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.operationalInsights/workspaces/*/read | A Log Analytics-adatok megtekintése |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Security/*/read | Olvasási biztonsági összetevőinek és a szabályzatok |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportok listája. |

## <a name="site-recovery-contributor"></a>Site Recovery-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Site Recovery szolgáltatás felügyeletét, kivéve a tárolók létrehozását és a szerepkör-hozzárendelést |
> | **Azonosító** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózati definíció beolvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | Az allocatestamp a szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tár hitelesítőadat-tanúsítványa. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Létrehozásához és kezeléséhez kapcsolódó tároló kiterjesztett adatok |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi egy Azure-erőforrásokra "tároló" típusú objektum |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Hozzon létre és a regisztrált felhasználók kezelése |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Hozzon létre vagy replikációs riasztási beállítások frissítése |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Minden olyan események olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Hozzon létre és kezelheti a replikációs hálók |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Fájlreplikációs feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Replikációs szabályzatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Hozzon létre, és a helyreállítási tervek kezelése |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | A Recovery Services-tároló tárolási konfigurációjának létrehozási és |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | A jogkivonat-Recovery Services-tároló adatainak visszaadása. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A Tártoken művelettel Társzintű Háttérműveletekhez beolvasni a tároló szint háttérbeli műveletek használható. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Olvassa el a riasztások a Recovery services-tároló |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="site-recovery-operator"></a>Site Recovery-operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a feladatátvételt és a feladat-visszavételt, de nem biztosít egyéb Site Recovery-beli felügyeleti műveleteket |
> | **Azonosító** | 494ae006-db33-4328-BF46-533a6560a3ca |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózati definíció beolvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | Az allocatestamp a szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi egy Azure-erőforrásokra "tároló" típusú objektum |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Művelet eredményeinek beolvasása művelettel olvashatók a műveleti állapota és az aszinkron módon elküldött művelet eredményének beolvasása |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel az erőforráshoz regisztrált tárolók beolvasása. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Minden riasztási beállításainak olvasása |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Minden olyan események olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Konzisztencia-ellenőrzést futtat a hálón |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Minden olyan hálók olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Átjáró újbóli társítása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Háló tanúsítványának megújítása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Olvassa el a hálózatokban |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Bármely Hálózatleképezések olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Olvassa el a alkalmazásvédelmi tárolókkal |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Védhető elemek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Helyreállítási pont alkalmazása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Feladatátvétel véglegesítése |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Tervezett feladatátvétel |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Minden védett elemek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Olvassa el a replikációs helyreállítási pontot |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Replikáció javítása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Védett elem ismételt védelme |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Feladatátvételi teszt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | A feladatátvételi teszt karbantartása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Feladatátvétel |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mobilitási szolgáltatás frissítése |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Olvassa el a bármely Védelmitároló-leképezések |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Olvassa el a bármely Recovery Services-szolgáltatók |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Szolgáltató frissítése |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Bármely Tárhelybesorolások olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Olvassa el a bármely Tárhelybesorolás-leképezések |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Bármely vCenters olvasása |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Fájlreplikációs feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Szabályzatok olvasása |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Feladatátvétel véglegesítésének helyreállítási terve |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Tervezett feladatátvétel helyreállítási terve |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Bármelyik helyreállítási tervek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Ismételt védelem helyreállítási terve |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Feladatátvételi teszt helyreállítási terve |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Teszt feladatátvétel karbantartásának helyreállítási terve |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Feladatátvétel helyreállítási terve |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Olvassa el a riasztások a Recovery services-tároló |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | A jogkivonat-Recovery Services-tároló adatainak visszaadása. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A Tártoken művelettel Társzintű Háttérműveletekhez beolvasni a tároló szint háttérbeli műveletek használható. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="site-recovery-reader"></a>Site Recovery-olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Site Recovery állapotának megtekintését, de nem biztosít egyéb felügyeleti műveleteket |
> | **Azonosító** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a riasztások a Recovery services-tárolót. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi egy Azure-erőforrásokra "tároló" típusú objektum |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Művelet eredményeinek beolvasása művelettel olvashatók a műveleti állapota és az aszinkron módon elküldött művelet eredményének beolvasása |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel az erőforráshoz regisztrált tárolók beolvasása. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Minden riasztási beállításainak olvasása |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Minden olyan események olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Minden olyan hálók olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Olvassa el a hálózatokban |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Bármely Hálózatleképezések olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Olvassa el a alkalmazásvédelmi tárolókkal |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Védhető elemek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Minden védett elemek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Olvassa el a replikációs helyreállítási pontot |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Olvassa el a bármely Védelmitároló-leképezések |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Olvassa el a bármely Recovery Services-szolgáltatók |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Bármely Tárhelybesorolások olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Olvassa el a bármely Tárhelybesorolás-leképezések |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Bármely vCenters olvasása |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Olvassa el a feladatok |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Szabályzatok olvasása |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Bármelyik helyreállítási tervek olvasása |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | A jogkivonat-Recovery Services-tároló adatainak visszaadása. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A Tártoken művelettel Társzintű Háttérműveletekhez beolvasni a tároló szint háttérbeli műveletek használható. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="sql-db-contributor"></a>SQL-adatbázisok közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az SQL Database-adatbázisok kezelését, ezekhez nem biztosít hozzáférést. Emellett a biztonsággal kapcsolatos házirendjeiket vagy a szülő SQL Server nem tudja kezelni. |
> | **Azonosító** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | SQL-adatbázisok létrehozása és kezelése |
> | Microsoft.Sql/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgáló tulajdonságainak listáját adja vissza. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Naplózási házirend nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Naplózási beállítások nem szerkeszthetők. |
> | Microsoft.Sql/servers/databases/auditRecords/read | A blob naplózási rekordok beolvasása |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Kapcsolat házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Adatmaszkolás házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Biztonsági riasztás házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/securityMetrics/* | A biztonság mértékét nem szerkeszthető. |
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
> | Microsoft.Authorization/*/read | Olvassa el a Microsoft-engedélyezés |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Például a storage-fiók vagy az SQL database erőforrás csatlakozik egy alhálózathoz. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Az SQL server naplózási szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/auditingSettings/* | Hozzon létre, és az SQL-kiszolgáló naplózási beállításainak kezelése |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL server adatbázis naplózási szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Hozzon létre, és az SQL server-adatbázis naplózási beállításainak kezelése |
> | Microsoft.Sql/servers/databases/auditRecords/read | Olvassa el a naplórekordok |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Az SQL server adatbázis kapcsolati szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Hozzon létre, és az SQL server adatbázis adatmaszkolási szabályzatok kezelése |
> | Microsoft.Sql/servers/databases/read | Adatbázisok vagy lekérdezi a megadott adatbázis tulajdonságainak listáját adja vissza. |
> | Microsoft.Sql/servers/databases/schemas/read | Sémák adatbázisok listájának lekérése |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Egy tábla oszlopait listájának lekérése |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Táblák adatbázisok listájának lekérése |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Az SQL server adatbázis biztonsági riasztási szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Létrehozásához és kezeléséhez az SQL server-adatbázis biztonsági metrikák |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgáló tulajdonságainak listáját adja vissza. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Az SQL server biztonsági riasztás szabályzatok létrehozása és kezelése |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="sql-server-contributor"></a>SQL Server közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetőséget nyújt az SQL-kiszolgálók és adatbázisok felügyeletére az azokhoz való hozzáférés nélkül. Az adatbázisok biztonsági házirendjeinek felügyeletét nem teszi lehetővé. |
> | **Azonosító** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Hozzon létre és SQL-kiszolgálók kezelése |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Az SQL server naplózási házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/auditingSettings/* | SQL-kiszolgáló naplózási beállításait nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Az SQL server-adatbázis naplózási házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Az SQL server-adatbázis naplózási beállításait nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/auditRecords/read | A naplórekordok nem olvasható. |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Az SQL server adatbázis kapcsolati házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Az SQL server adatbázis adatmaszkolási házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Az SQL server adatbázis biztonsági riasztási házirendek nem szerkeszthető. |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Nem lehet szerkeszteni az SQL server-adatbázis biztonsági metrikák |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Az SQL server biztonsági riasztás házirendek nem szerkeszthető. |

## <a name="storage-account-contributor"></a>Tárfiók-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi tárfiókok kezelését, de ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el az összes engedélyt |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Diagnosztikai beállítások kezelése |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Például a storage-fiók vagy az SQL database erőforrás csatlakozik egy alhálózathoz. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/* | Storage-fiókok létrehozása és kezelése |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

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
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="traffic-manager-contributor"></a>Traffic Manager-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz hozzáférő felhasználók felügyeletét. |
> | **Azonosító** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="user-access-administrator"></a>Felhasználói hozzáférés rendszergazdája
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Azure-erőforrásokhoz való felhasználói hozzáférés kezelését. |
> | **Azonosító** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Műveletek** |  |
> | * / olvasási | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.Authorization/* | Hitelesítés |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="virtual-machine-administrator-login"></a>Virtuális gépre való rendszergazdai bejelentkezés
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A portálon, és jelentkezzen be rendszergazdai virtuális számítógépek megtekintése |
> | **Azonosító** | 1c0163c0-47E6-4577-8991-ea5c82e286e4 |
> | **Műveletek** |  |
> | Microsoft.Network/publicIPAddresses/read | Lekérdezi egy nyilvános IP-cím cím definíciója. |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózati definíció beolvasása |
> | Microsoft.Network/loadBalancers/read | Load balancer definici beolvasása |
> | Microsoft.Network/networkInterfaces/read | Lekérdezi egy hálózati illesztőjének definícióját.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Bejelentkezés egy virtuális gépre a Windows rendszergazdai vagy a Linux gyökérszintű felhasználójának jogosultságaival |

## <a name="virtual-machine-contributor"></a>Virtuális gépek közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a virtuális gépek kezelését, de ezekhez nem biztosít hozzáférést, és nem teszi lehetővé a virtuális gépekhez hozzárendelt virtuális hálózatok és tárfiókok elérését sem. |
> | **Azonosító** | 9980e02c-c2be-4D73-94e8-173b1dc7cf3c |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Compute/availabilitySets/* | Hozzon létre és számítási rendelkezésre állási készletek felügyelete |
> | Microsoft.Compute/locations/* | Létrehozni és kezelni a számítási helyek |
> | Microsoft.Compute/virtualMachines/* | Virtuális gépek létrehozása és kezelése |
> | Microsoft.Compute/virtualMachineScaleSets/* | Létrehozása és kezelése a virtual machine scale sets |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Egy application gateway háttércímkészlet csatlakozik |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | A load balancer háttércímkészlet csatlakozik |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Csatlakozik a terheléselosztó bejövő nat-készlet |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Csatlakozik a terheléselosztó bejövő nat-szabály |
> | Microsoft.Network/loadBalancers/probes/join/action | Lehetővé teszi, hogy a terheléselosztó a mintavételezők használatával. Az ezen engedély healthProbe tulajdonságot a Virtuálisgép-méretezési csoport például set hivatkozhat a mintavétel. |
> | Microsoft.Network/loadBalancers/read | Load balancer definici beolvasása |
> | Microsoft.Network/locations/* | Hozzon létre és kezelheti a hálózati helyek |
> | Microsoft.Network/networkInterfaces/* | Hozzon létre, és a hálózati adapterek kezelése |
> | Microsoft.Network/networkSecurityGroups/join/action | Csatlakoztatja a hálózati biztonsági csoport |
> | Microsoft.Network/networkSecurityGroups/read | Egy hálózati biztonsági csoport definíció beolvasása |
> | Microsoft.Network/publicIPAddresses/join/action | Nyilvános ip-címmel csatlakozik |
> | Microsoft.Network/publicIPAddresses/read | Lekérdezi egy nyilvános IP-cím cím definíciója. |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózati definíció beolvasása |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Virtuális hálózat csatlakozik |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | A biztonsági másolat védelmi leképezésének létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem objektumadatainak visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | A védett elem biztonsági másolatának létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Összes védelmi szabályzat visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Egy védelmi szabályzat létrehozása |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi egy Azure-erőforrásokra "tároló" típusú objektum |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/write | A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
> | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |

## <a name="virtual-machine-user-login"></a>Virtuális gépre való felhasználói bejelentkezés
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A portálon és a felhasználói bejelentkezési nézet a virtuális gépek. |
> | **Azonosító** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Műveletek** |  |
> | Microsoft.Network/publicIPAddresses/read | Lekérdezi egy nyilvános IP-cím cím definíciója. |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózati definíció beolvasása |
> | Microsoft.Network/loadBalancers/read | Load balancer definici beolvasása |
> | Microsoft.Network/networkInterfaces/read | Lekérdezi egy hálózati illesztőjének definícióját.  |
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
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Web/serverFarms/* | Hozzon létre, és a kiszolgálófarmok kezelése |

## <a name="website-contributor"></a>Webhelyek közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a webhelyek kezelését (a webes tarifacsomagokét azonban nem), az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Insights/components/* | Létrehozása és kezelése az Insights-összetevők |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Web/certificates/* | Webhely tanúsítványok létrehozásához és felügyeletéhez |
> | Microsoft.Web/listSitesAssignedToHostName/read | Lekérése az állomásnév rendelt helyek nevét. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Az App Service-csomag tulajdonságainak beolvasása |
> | Microsoft.Web/sites/* | Létrehozása és kezelése a websites (webhelyek létrehozását is szükséges írási engedéllyel a társított App Service-csomag) |

## <a name="next-steps"></a>További lépések

- [Egyéni szerepkörök](custom-roles.md)
- [Az Azure portal használatával szerepkör-hozzárendelések kezelése](role-assignments-portal.md)
- [Engedélyek az Azure Security Centerben](../security-center/security-center-permissions.md)
