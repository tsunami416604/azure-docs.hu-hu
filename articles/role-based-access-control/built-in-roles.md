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
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 427c4615fcbb036ffff56a8fc592f258fb98845e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755109"
---
# <a name="built-in-roles-for-azure-resources"></a>Az Azure-erőforrások beépített szerepkörök

[Szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) Azure-erőforrások, amelyeket hozzárendelhet a felhasználók, csoportok, az egyszerű szolgáltatások és a felügyelt identitásokból számos beépített szerepkörrel rendelkezik. Szerepkör-hozzárendelések módon az Azure-erőforrásokhoz való hozzáférést. Ha a beépített szerepkörök nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját [egyéni szerepkörök az Azure-erőforrások](custom-roles.md).

Ez a cikk az Azure-erőforrásokhoz, amelyek mindig fejlődő a beépített szerepkörök listája. A legújabb szerepkörök használja [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) vagy [az role definition listájában](/cli/azure/role/definition#az-role-definition-list). Ha a rendszergazdai szerepkörök az Azure Active Directory keres, tekintse meg [rendszergazdája szerepkör engedélyei az Azure Active Directoryban](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Beépített szerepkör leírása

Az alábbi táblázat az egyes beépített szerepkörök rövid leírását tartalmazza. A szerepkör nevét a listájának megtekintéséhez kattintson `Actions`, `NotActions`, `DataActions`, és `NotDataActions` az egyes szerepkörökhöz. Ezeket a műveleteket jelenti azt, és ezek hogyan vonatkoznak az adatok és a felügyeleti sík az kapcsolatos információkért lásd: [megismerheti az Azure-erőforrások szerepkör-definíciók](role-definitions.md).


| Beépített szerepkör | Leírás |
| --- | --- |
| [Tulajdonos](#owner) | Lehetővé teszi az összes funkció kezelését, beleértve az erőforrásokhoz való hozzáférést. |
| [Közreműködő](#contributor) | Lehetővé teszi az erőforrásokhoz való hozzáférés kivételével mindent felügyelhetnek. |
| [Olvasó](#reader) | Lehetővé teszi, hogy mindent megtekinthessen, de nem végezze el a módosításokat. |
| [AcrDelete](#acrdelete) | ACR delete |
| [AcrImageSigner](#acrimagesigner) | ACR-lemezképaláíró |
| [AcrPull](#acrpull) | ACR lekéréses |
| [AcrPush](#acrpush) | ACR leküldéses |
| [AcrQuarantineReader](#acrquarantinereader) | ACR-karanténadatolvasó |
| [AcrQuarantineWriter](#acrquarantinewriter) | ACR-karanténadatíró |
| [API Management szolgáltatás Közreműködője](#api-management-service-contributor) | Kezelheti a szolgáltatás és az API-k |
| [API Management szolgáltatás operátori szerepköre](#api-management-service-operator-role) | Kezelheti a szolgáltatást, de nem az API-k |
| [API Management szolgáltatás olvasói szerepköre](#api-management-service-reader-role) | Csak olvasási hozzáférés a szolgáltatáshoz és API-k |
| [Application Insights-összetevők Közreműködője](#application-insights-component-contributor) | Kezelheti az Application Insights-összetevők |
| [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Engedélyt ad a felhasználónak megtekintése és letöltése az Application Insights Snapshot Debugger az gyűjtött hibakeresési pillanatképek. Vegye figyelembe, hogy ezeket az engedélyeket nem szerepelnek a [tulajdonosa](#owner) vagy [közreműködői](#contributor) szerepköröket. |
| [Automation-feladat operátora](#automation-job-operator) | Létrehozása és kezelése Automation-Runbookok használatával feladatok. |
| [Automation-operátor](#automation-operator) | Automation-operátorok tudnak indítása, leállítása, felfüggesztése és folytatása a feladatok |
| [Automation-Runbook operátora](#automation-runbook-operator) | Olvassa el a Runbook-tulajdonságok –, a runbook-feladatok létrehozásához. |
| [Avere Közreműködője](#avere-contributor) | Létrehozhat és -Avere vFXT fürt kezelése. |
| [Avere operátor](#avere-operator) | A fürt kezeléséhez a Avere vFXT fürt által használt |
| [Az Azure Kubernetes Service-fürt rendszergazdai szerepkör](#azure-kubernetes-service-cluster-admin-role) | Fürt rendszergazdai hitelesítő adatok a művelet listázza. |
| [Az Azure Kubernetes Service-fürt felhasználói szerepkör](#azure-kubernetes-service-cluster-user-role) | Fürt felhasználói hitelesítő adatok a művelet listázza. |
| [Az Azure Maps olvasója (minta)](#azure-maps-data-reader-preview) | Olvassa el a hozzáférést az Azure maps-fiók kapcsolódó adatok leképezése. |
| [Az Azure Stack-regisztráció tulajdonosa](#azure-stack-registration-owner) | Lehetővé teszi az Azure Stack-regisztrációk kezelését. |
| [Biztonsági mentési közreműködő](#backup-contributor) | Lehetővé teszi a felügyelt biztonsági mentési szolgáltatás, de nem tárolók létrehozása és adhat hozzáférést másoknak |
| [Biztonságimásolat-felelős](#backup-operator) | Lehetővé teszi a biztonsági mentési szolgáltatásai, kivéve a biztonsági mentés, a tárolók létrehozását és a másoknak való hozzáférés megadását eltávolításának kezelését |
| [Biztonsági mentési olvasó](#backup-reader) | Megtekintheti a biztonsági mentési szolgáltatásai, de nem végezhet módosításokat |
| [Számlázási olvasó](#billing-reader) | Olvasási hozzáférést biztosít a számlázási adatokat |
| [BizTalk Contributor](#biztalk-contributor) | Lehetővé teszi a BizTalk-szolgáltatások kezelését, ezekhez nem biztosít hozzáférést. |
| [Blockchain-tag-csomópont elérése (előzetes verzió)](#blockchain-member-node-access-preview) | Blockchain tagcsomópontok hozzáférésének engedélyezése |
| [CDN-végpont Közreműködője](#cdn-endpoint-contributor) | Kezelheti a CDN-végpontokat, de nem tud hozzáférést adni más felhasználóknak. |
| [CDN-végpont olvasója](#cdn-endpoint-reader) | Megtekintheti a CDN-végpontokat, de nem végezhet módosításokat. |
| [A CDN-profil Közreműködője](#cdn-profile-contributor) | Kezelheti a CDN-profilokat és azok végpontjait, de nem tud hozzáférést adni más felhasználóknak. |
| [A CDN-profil olvasója](#cdn-profile-reader) | Megtekintheti a CDN-profilokat és azok végpontjait, de nem végezhet módosításokat. |
| [Klasszikus hálózati közreműködő](#classic-network-contributor) | Lehetővé teszi a hagyományos hálózatok kezelését, ezekhez nem biztosít hozzáférést. |
| [Hagyományos Tárfiók-közreműködő](#classic-storage-account-contributor) | Lehetővé teszi a klasszikus tárfiókok kezelését, ezekhez nem biztosít hozzáférést. |
| [Hagyományos tároló fiók fő operátora – szolgáltatási szerepkör](#classic-storage-account-key-operator-service-role) | Klasszikus Tárfiók kulcsának operátorai jogosultak a hagyományos Tárfiókokhoz kulcsok listázására és újragenerálására |
| [Virtuális gépek hagyományos Közreműködője](#classic-virtual-machine-contributor) | Lehetővé teszi a klasszikus virtuális gépek, de nem biztosít hozzáférést, és nem a virtuális hálózat vagy kapcsolódó tárfiók kezelését. |
| [A cognitive Services-közreműködő](#cognitive-services-contributor) | Lehetővé teszi, hogy létrehozása, olvasása, frissítése, törlése és a Cognitive Services, kulcsok kezelése. |
| [A cognitive Services-adatok olvasója (minta)](#cognitive-services-data-reader-preview) | Lehetővé teszi a Cognitive Services-adatok olvasása. |
| [A cognitive Services-felhasználó](#cognitive-services-user) | Lehetővé teszi, hogy Ön elolvassa és a Cognitive Services, kulcsok listázását. |
| [A cosmos DB-fiók olvasói szerepköre](#cosmos-db-account-reader-role) | Olvashatja az Azure Cosmos DB-fiókja adatait. Lásd: [DocumentDB-Fiókközreműködő](#documentdb-account-contributor) kezeléséhez az Azure Cosmos DB-fiókokhoz. |
| [A cosmos DB-operátor](#cosmos-db-operator) | Lehetővé teszi az Azure Cosmos DB-fiókok, de nincs hozzáférés az adatokhoz a azok kezelését. Megakadályozza, hogy a hozzáférési kulcsokat és kapcsolati karakterláncokat. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Elküldhet egy Cosmos DB-adatbázis visszaállítási kérelmében vagy egy tároló-fiókot |
| [A Cost Management Közreműködője](#cost-management-contributor) | Költségek megtekintheti és kezelheti a költség-konfiguráció (pl. költségvetéseket, export) |
| [A Cost Management olvasó](#cost-management-reader) | Költségadatok és a konfiguráció (pl. költségvetéseket, export) |
| [Data Box Közreműködője](#data-box-contributor) | Kezelheti a Data Box szolgáltatás található minden elemet kivételével másoknak való hozzáférés megadását teszi lehetővé. |
| [Data Box-olvasó](#data-box-reader) | Kezelheti a Data Box szolgáltatás kivéve a rendelés létrehozása vagy szerkesztése a rendelés részleteit, és a másoknak való hozzáférés megadását teszi lehetővé. |
| [Data Factory Közreműködője](#data-factory-contributor) | Létrehozhat és kezelhet adat-előállítók, valamint az azokhoz tartozó gyermekerőforrásait. |
| [Data Lake Analytics-fejlesztő](#data-lake-analytics-developer) | Lehetővé teszi elküldése, monitorozásához és a saját feladatok kezelése, de nem létrehozása vagy törlése a Data Lake Analytics-fiókok. |
| [Adatok Purger](#data-purger) | Elemzési adatok is kiürítheti. |
| [DevTest Labs User](#devtest-labs-user) | Lehetővé teszi, hogy csatlakozik, kezdő, újraindítását és leállítását a virtuális gépek az Azure DevTest Labs-környezetben. |
| [DNS-zóna Közreműködője](#dns-zone-contributor) | Lehetővé teszi a DNS-zónák és -rekordhalmazok az Azure DNS kezelését, de nem teszi lehetővé az azokhoz való hozzáférés szabályozásához. |
| [DocumentDB-Fiókközreműködő](#documentdb-account-contributor) | Kezelheti az Azure Cosmos DB-fiókokhoz. Az Azure Cosmos DB DocumentDB nevén. |
| [Event Hubs-adatok tulajdonosa](#event-hubs-data-owner) | Teljes hozzáférést biztosít az Azure Event Hubs-erőforrások | 
| [EventGrid EventSubscription Közreműködője](#eventgrid-eventsubscription-contributor) | Lehetővé teszi EventGrid esemény-előfizetési műveletek kezelését. |
| [EventGrid EventSubscription olvasó](#eventgrid-eventsubscription-reader) | Olvassa el az esemény-előfizetések EventGrid teszi lehetővé. |
| [HDInsight-fürt operátor](#hdinsight-cluster-operator) | Lehetővé teszi, hogy olvassa el, és a HDInsight-fürt konfigurációjának módosítását. |
| [HDInsight Domain Services-közreműködő](#hdinsight-domain-services-contributor) | Olvashatja, létrehozása, módosítása és törlése a tartományi szolgáltatások kapcsolódó műveletek HDInsight vállalati biztonsági csomag szükséges |
| [Intelligens rendszerek Fiókközreműködője](#intelligent-systems-account-contributor) | Lehetővé teszi az intelligens rendszerek fiókjainak kezelését, ezekhez nem biztosít hozzáférést. |
| [Key Vault-közreműködő](#key-vault-contributor) | Lehetővé teszi a kulcstartók kezelését, ezekhez nem biztosít hozzáférést. |
| [Lab Creator](#lab-creator) | Lehetővé teszi a létrehozását, kezelését, és törölje a felügyelt tesztkörnyezetek az Azure Lab-fiókokban. |
| [Log Analytics-közreműködő](#log-analytics-contributor) | Log Analytics-közreműködő az összes monitorozási adat olvashatja és szerkesztheti a figyelési beállításokat. Figyelési beállítások szerkesztése magában foglalja a virtuális gépek; a Virtuálisgép-bővítmény hozzáadása tudni naplógyűjtemény konfigurálása céljából az Azure Storage; tárfiók kulcsainak beolvasását létrehozásáról és konfigurálásáról az Automation-fiókok; megoldások hozzáadását; és az Azure diagnostics konfigurálása az összes Azure-erőforrást. |
| [Log Analytics olvasó](#log-analytics-reader) | Log Analytics olvasó megtekintheti és keresheti az összes figyelési adatot, valamint megtekintheti a figyelési beállításokat, beleértve az Azure diagnostics konfigurációjának megtekintését az összes Azure-erőforrások. |
| [A logikai alkalmazás Közreműködője](#logic-app-contributor) | Lehetővé teszi, hogy a logikai alkalmazás kezelését, ezekhez nem biztosít hozzáférést. |
| [Logikai alkalmazás operátora](#logic-app-operator) | Lehetővé teszi, hogy olvasását, engedélyezését és a logikai alkalmazás letiltása. |
| [Felügyelt alkalmazás operátori szerepkör](#managed-application-operator-role) | Olvassa el, és műveleteket hajthat végre felügyelt alkalmazásokra vonatkozó erőforrásokhoz |
| [Felügyelt alkalmazások Adatolvasó](#managed-applications-reader) | Olvassa el a felügyelt alkalmazás és igény szerinti hozzáférés kérése az erőforrások teszi lehetővé. |
| [Managed Identity Contributor](#managed-identity-contributor) | Create, Read, Update, and Delete User Assigned Identity |
| [Felügyelt identitások üzemeltetője](#managed-identity-operator) | Olvassa el, és rendelje hozzá a felhasználóhoz hozzárendelt identitás |
| [A felügyeleti csoport Közreműködője](#management-group-contributor) | A felügyeleti csoport közreműködői szerepkört |
| [A felügyeleti csoport olvasó](#management-group-reader) | A felügyeleti csoport olvasói szerepköre |
| [Közreműködő figyelése](#monitoring-contributor) | Az összes monitorozási adat olvashatja és szerkesztheti a figyelési beállításokat. Lásd még: [szerepkörök, engedélyek és biztonság az Azure Monitor használatának első lépései](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Figyelési metrikák közzétevő](#monitoring-metrics-publisher) | Lehetővé teszi, hogy metrikákat az Azure-erőforrások közzététele |
| [Olvasó figyelése](#monitoring-reader) | Olvashatja az összes figyelési adatot (metrikákat, naplókat, stb.). Lásd még: [szerepkörök, engedélyek és biztonság az Azure Monitor használatának első lépései](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Hálózati közreműködő](#network-contributor) | Lehetővé teszi a hálózatok kezelését, ezekhez nem biztosít hozzáférést. |
| [Új Relic APM-Fiókközreműködő](#new-relic-apm-account-contributor) | Lehetővé teszi új Relic alkalmazásteljesítmény-felügyeleti fiókok és az alkalmazások, de az azokhoz való hozzáférés kezelését. |
| [Olvasó és adatelérés](#reader-and-data-access) | Lehetővé teszi minden megtekintését, de nem teszi lehetővé, törölheti, vagy hozzon létre egy storage-fiók vagy az abban található erőforrást. Olvasási/írási hozzáférést a tárfiókok kulcsainak elérésével storage-fiókban tárolt adatokat is engedélyezi. |
| [Redis Cache Contributor](#redis-cache-contributor) | Lehetővé teszi a Redis-gyorsítótárak kezelését, ezekhez nem biztosít hozzáférést. |
| [Erőforrás-szabályzati közreműködő (előzetes verzió)](#resource-policy-contributor-preview) | (Előzetes verzió) Erőforrás-szabályzat létrehozására/módosítására jogosultságokkal EA-beli visszatöltött felhasználók hozzon létre támogatási jegyet, és erőforrások/hierarchia beolvasására. |
| [A Scheduler Feladatgyűjteményeinek Közreműködője](#scheduler-job-collections-contributor) | Lehetővé teszi a Scheduler-feladatgyűjtemények kezelését, ezekhez nem biztosít hozzáférést. |
| [Search szolgáltatás Közreműködője](#search-service-contributor) | Lehetővé teszi a keresési szolgáltatások kezelését, ezekhez nem biztosít hozzáférést. |
| [Biztonsági rendszergazda](#security-admin) | A Security Center csak: Megtekintheti biztonsági házirendek, biztonsági állapotot, szerkessze a biztonsági szabályzatok, riasztások megtekintése és javaslatok, riasztások és javaslatok elvetése |
| [Biztonságkezelő (örökölt)](#security-manager-legacy) | Ez az örökölt szerepkör. Használja helyette a biztonsági rendszergazda |
| [Biztonsági olvasó](#security-reader) | A Security Center csak: Megtekintheti a javaslatok és riasztások, a biztonsági házirendek, a biztonsági állapotot, de nem végezhet módosításokat megtekintése |
| [Service Bus adatok tulajdonosa](#service-bus-data-owner) | Az Azure Service Bus-erőforrások teljes hozzáférésének engedélyezése |
| [Site Recovery-közreműködő](#site-recovery-contributor) | Lehetővé teszi a Site Recovery szolgáltatást, kivéve a tárolók létrehozását és a szerepkör-hozzárendelés kezelését |
| [Site Recovery-operátor](#site-recovery-operator) | Lehetővé teszi a feladatátvétel és feladat-visszavétel, de nem biztosít egyéb Site Recovery felügyeleti műveleteket |
| [Site Recovery-olvasó](#site-recovery-reader) | Lehetővé teszi a Site Recovery állapotának megtekintését, de nem biztosít egyéb felügyeleti műveleteket |
| [Térbeli horgonyok közreműködő](#spatial-anchors-account-contributor) | Lehetővé teszi, hogy térbeli horgonyok kezelése a fiókban, de ne törölje őket |
| [Térbeli horgonyok fiók tulajdonosa](#spatial-anchors-account-owner) | Lehetővé teszi a fiókjában, beleértve a törlésük térbeli horgonyok kezelését |
| [Térbeli horgonyok fiók olvasó](#spatial-anchors-account-reader) | Keresse meg és a fiókban térbeli horgonyok tulajdonságainak olvasása |
| [SQL-Adatbázisok Közreműködője](#sql-db-contributor) | Lehetővé teszi az SQL Database-adatbázisok kezelését, ezekhez nem biztosít hozzáférést. Emellett a biztonsággal kapcsolatos házirendjeiket vagy a szülő SQL Server nem tudja kezelni. |
| [SQL Managed Instance Contributor](#sql-managed-instance-contributor) | Lehetővé teszi, hogy az SQL felügyelt példányok kezelése és a szükséges hálózati konfiguráció, de nem adhat hozzáférést másoknak. |
| [SQL Security Manager](#sql-security-manager) | Lehetővé teszi az SQL Server-kiszolgálók és adatbázisok, de ezekhez nem biztosít hozzáférést a biztonsági házirendek kezelését. |
| [SQL Server Contributor](#sql-server-contributor) | Lehetővé teszi, hogy SQL Server-kiszolgálók és adatbázisok kezelése, de nem érhető el, és nem a biztonsági-házirendjeinek. |
| [Tárfiók-közreműködő](#storage-account-contributor) | Lehetővé teszi tárfiókok kezelését, de ezekhez nem biztosít hozzáférést. |
| [Tárolási fiók fő operátora – szolgáltatási szerepkör](#storage-account-key-operator-service-role) | A Tárfiók kulcsának operátorai jogosultak a Tárfiókokhoz kulcsok listázására és újragenerálására |
| [Storage-Blobadatok Közreműködője](#storage-blob-data-contributor) | Lehetővé teszi, hogy olvasási, írási és törlési hozzáférésének Azure Storage-blobtárolók és -adatok |
| [Tárolási Blob adatok tulajdonosa](#storage-blob-data-owner) | Lehetővé teszi a teljes körű hozzáférést az Azure Storage-blobtárolók és adatait, beleértve a POSIX hozzáférés-vezérlés hozzárendelése. |
| [Storage-Blobadatok olvasója](#storage-blob-data-reader) | Az Azure Storage-blobtárolók és -adatok olvasási hozzáférésének engedélyezése |
| [Storage-Üzenetsorbeli adatok Közreműködője](#storage-queue-data-contributor) | Lehetővé teszi, hogy olvasási, írási és törlési hozzáférésének Azure Storage üzenetsorok és üzenetsorbeli üzenetek számára |
| [Tárolási üzenetsor üzenetet processzor](#storage-queue-data-message-processor) | Lehetővé teszi, hogy a betekintési, kap, és törlési hozzáférésének Azure Storage-üzenetsor üzenetei |
| [Tárolási üzenetsor adatok üzenet küldője](#storage-queue-data-message-sender) | Lehetővé teszi, hogy az Azure Storage-üzenetsorbeli üzenetek küldéséhez |
| [Storage-Üzenetsorbeli adatok olvasója](#storage-queue-data-reader) | Azure Storage-üzenetsorok és üzenetsorbeli üzenetek olvasási hozzáférésének engedélyezése |
| [Támogatáskérési közreműködő](#support-request-contributor) | Lehetővé teszi a támogatási kérések létrehozásához és kezeléséhez |
| [Traffic Manager-közreműködő](#traffic-manager-contributor) | Lehetővé teszi a Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz való hozzáférés szabályozásához. |
| [Felhasználói hozzáférés rendszergazdája](#user-access-administrator) | Azure-erőforrásokhoz való felhasználói hozzáférés kezelését teszi lehetővé. |
| [A virtuális gépre való rendszergazdai bejelentkezés](#virtual-machine-administrator-login) | A portálon, és jelentkezzen be rendszergazdai virtuális számítógépek megtekintése |
| [Virtuális gépek közreműködője](#virtual-machine-contributor) | Lehetővé teszi virtuális gépek, de nem biztosít hozzáférést, és nem a virtuális hálózat vagy kapcsolódó tárfiók kezelését. |
| [A virtuális gépre való felhasználói bejelentkezés](#virtual-machine-user-login) | A portálon és a felhasználói bejelentkezési nézet a virtuális gépek. |
| [Webes Tarifacsomagok Közreműködője](#web-plan-contributor) | Lehetővé teszi számukra a websites, de nem biztosít hozzáférést a webes tarifacsomagok kezelését. |
| [Webhelyek Közreműködője](#website-contributor) | Lehetővé teszi a webhelyek (a webes tarifacsomagokét) kezelését, ezekhez nem biztosít hozzáférést. |


## <a name="owner"></a>Tulajdonos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az összes funkció kezelését, beleértve az erőforrásokhoz való hozzáférést. |
> | **Azonosító** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Műveletek** |  |
> | * | Bármilyen típusú erőforrások létrehozása és felügyelete |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="contributor"></a>Közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az erőforrásokhoz való hozzáférés kivételével mindent felügyelhetnek. |
> | **Azonosító** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Műveletek** |  |
> | * | Bármilyen típusú erőforrások létrehozása és felügyelete |
> | **notActions** |  |
> | Microsoft.Authorization/*/Delete | Szerepkörök és szerepkör-hozzárendelések törlése |
> | Microsoft.Authorization/*/Write | Szerepkörök és szerepkör-hozzárendelések létrehozása |
> | Microsoft.Authorization/elevateAccess/Action | Hozzáférést biztosít a hívó felhasználói hozzáférés rendszergazdája a bérlői hatókörben |
> | Microsoft.Blueprint/blueprintAssignments/write | Létrehozás vagy frissítés tetszőleges tervezetösszetevők |
> | Microsoft.Blueprint/blueprintAssignments/delete | Tetszőleges tervezetösszetevők törlése |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="reader"></a>Olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy mindent megtekinthessen, de nem végezze el a módosításokat. |
> | **Azonosító** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Műveletek** |  |
> | */read | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR delete |
> | **Azonosító** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Törölje a tároló-beállításjegyzék összetevő. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR-lemezképaláíró |
> | **Azonosító** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | A leküldéses tartalom megbízhatósági tároló-beállításjegyzék metaadatait. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR lekéréses |
> | **Azonosító** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Kérje le, vagy kérjen lemezképeket tároló-beállításjegyzék. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR leküldéses |
> | **Azonosító** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Kérje le, vagy kérjen lemezképeket tároló-beállításjegyzék. |
> | Microsoft.ContainerRegistry/registries/push/write | Leküldéses vagy képek írási továbbíthat egy tárolóregisztrációs adatbázisba. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR-karanténadatolvasó |
> | **Azonosító** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Kérje le, vagy szerezze be a karanténba helyezett rendszerképeket container registryből |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR-karanténadatíró |
> | **Azonosító** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Kérje le, vagy szerezze be a karanténba helyezett rendszerképeket container registryből |
> | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Karanténba helyezett rendszerképeket karantén állapotának írási/módosítása |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="api-management-service-contributor"></a>API Management szolgáltatás Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a szolgáltatás és az API-k |
> | **Azonosító** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/* | Hozzon létre, és az API Management szolgáltatás kezelése |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="api-management-service-operator-role"></a>API Management szolgáltatás operátori szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a szolgáltatást, de nem az API-k |
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
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Felhasználóhoz tartozó kulcsok beolvasása |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="api-management-service-reader-role"></a>API Management Service Reader Role
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Csak olvasási hozzáférés a szolgáltatáshoz és API-k |
> | **Azonosító** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/*/read | Olvassa el API Management szolgáltatás példányai |
> | Microsoft.ApiManagement/service/read | Egy API Management szolgáltatáspéldány metaadatok olvasása |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Felhasználóhoz tartozó kulcsok beolvasása |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="application-insights-component-contributor"></a>Application Insights-összetevők Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti az Application Insights-összetevők |
> | **Azonosító** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.Insights/components/* | Létrehozása és kezelése az Insights-összetevők |
> | Microsoft.Insights/webtests/* | Létrehozása és kezelése a webes tesztek |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

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
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="automation-job-operator"></a>Automation-feladat operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Létrehozása és kezelése Automation-Runbookok használatával feladatok. |
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
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="automation-operator"></a>Automation-operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Automation-operátorok tudnak indítása, leállítása, felfüggesztése és folytatása a feladatok |
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
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Automation/automationAccounts/jobs/output/read | A feladat kimenetének beolvasása |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="automation-runbook-operator"></a>Automation-Runbook operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvassa el a Runbook-tulajdonságok –, a runbook-feladatok létrehozásához. |
> | **Azonosító** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation-runbook beolvasása |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="avere-contributor"></a>Avere Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Létrehozhat és -Avere vFXT fürt kezelése. |
> | **Azonosító** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózati definíció beolvasása |
> | Microsoft.Network/virtualNetworks/subnets/read | Lekérdezi egy virtuális hálózat alhálózati definíció |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Egy virtuális hálózathoz csatlakozik. Nem Alertable. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Például a storage-fiók vagy az SQL database erőforrás csatlakozik egy alhálózathoz. Nem alertable. |
> | Microsoft.Network/networkSecurityGroups/join/action | Csatlakoztatja a hálózati biztonsági csoport. Nem Alertable. |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Az erőforráscsoporthoz tartozó erőforrások lekérése. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Egy blob törlésének eredményét adja vissza |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Egy blobot vagy blobok listáját adja vissza |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Blob írásának eredményét adja vissza |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="avere-operator"></a>Avere operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A fürt kezeléséhez a Avere vFXT fürt által használt |
> | **Azonosító** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Műveletek** |  |
> | Microsoft.Compute/virtualMachines/read | Virtuális gép tulajdonságainak beolvasása |
> | Microsoft.Network/networkInterfaces/read | Lekérdezi egy hálózati illesztőjének definícióját.  |
> | Microsoft.Network/networkInterfaces/write | Hálózati adapter létrehozása vagy frissítése egy meglévő hálózati adaptert.  |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózati definíció beolvasása |
> | Microsoft.Network/virtualNetworks/subnets/read | Lekérdezi egy virtuális hálózat alhálózati definíció |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Egy virtuális hálózathoz csatlakozik. Nem Alertable. |
> | Microsoft.Network/networkSecurityGroups/join/action | Csatlakoztatja a hálózati biztonsági csoport. Nem Alertable. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | A tároló törlésének eredményét adja vissza |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Tárolók listájának visszaadása |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | A put blobtároló eredményét adja vissza |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Egy blob törlésének eredményét adja vissza |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Egy blobot vagy blobok listáját adja vissza |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Blob írásának eredményét adja vissza |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Az Azure Kubernetes Service-fürt rendszergazdai szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Fürt rendszergazdai hitelesítő adatok a művelet listázza. |
> | **Azonosító** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Műveletek** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Egy felügyelt fürt a clusterAdmin hitelesítő adatainak listázása |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Az Azure Kubernetes Service-fürt felhasználói szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Fürt felhasználói hitelesítő adatok a művelet listázza. |
> | **Azonosító** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Műveletek** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Egy felügyelt fürt a clusterUser hitelesítő adatainak listázása |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="azure-maps-data-reader-preview"></a>Az Azure Maps olvasója (minta)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvassa el a hozzáférést az Azure maps-fiók kapcsolódó adatok leképezése. |
> | **Azonosító** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Műveletek** |  |
> | *none* |  |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Maps/accounts/data/read | A maps-fiók adatok olvasási hozzáférést biztosít. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="azure-stack-registration-owner"></a>Az Azure Stack-regisztráció tulajdonosa
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az Azure Stack-regisztrációk kezelését. |
> | **Azonosító** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Műveletek** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Kiterjesztett az Azure Stack piactéren termék részleteinek beolvasása |
> | Microsoft.AzureStack/registrations/products/read | Az Azure Stack piactéren termék tulajdonságainak beolvasása |
> | Microsoft.AzureStack/registrations/read | Az Azure Stack-regisztrációk tulajdonságainak beolvasása |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="backup-contributor"></a>Biztonsági mentési közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a felügyelt biztonsági mentési szolgáltatás, de nem tárolók létrehozása és adhat hozzáférést másoknak |
> | **Azonosító** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózati definíció beolvasása |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | A biztonságimásolat-kezelési művelet eredmények kezelése |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Hozzon létre, és a helyreállítási tár biztonsági mentési hálók belüli biztonsági mentési tárolók kezelése |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | A tárolólista |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
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
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Hozzon létre és a regisztrált felhasználók kezelése |
> | Microsoft.RecoveryServices/Vaults/usages/* | Hozzon létre, és a Recovery Services-tároló használata kezelheti |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/read | A storage-fiókok vagy a megadott tárfiók tulajdonságainak beolvasása adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | A védett elem a művelet érvényesítése |
> | Microsoft.RecoveryServices/Vaults/write | A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Adja vissza a biztonsági mentési művelet állapotának helyreállítási tár. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | A tárolóban regisztrált biztonsági másolatokat kezelő kiszolgálókat adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Összes védhető tároló beolvasása |
> | Microsoft.RecoveryServices/locations/backupStatus/action | A helyreállítási tárak biztonsági mentési állapotának ellenőrzése |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Funkciók ellenőrzése |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | A riasztás feloldása. |
> | Microsoft.RecoveryServices/operations/read | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Műveleti állapotának beolvasása egy adott művelethez |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Az összes biztonsági mentés védelmi leképezések listázása |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="backup-operator"></a>Biztonságimásolat-felelős
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a biztonsági mentési szolgáltatásai, kivéve a biztonsági mentés, a tárolók létrehozását és a másoknak való hozzáférés megadását eltávolításának kezelését |
> | **Azonosító** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózati definíció beolvasása |
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
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | A tárolólista |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Hozzon létre, és biztonsági másolatokat kezelő operations eredményeinek kezelése |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A szabályzatművelet eredményeinek beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Összes védelmi szabályzat visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Cikkek, amely biztonsági mentésre alkalmas létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | A védett elemek listájának beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tároló visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Használati összegzéseket adja vissza a védett elemek és védett kiszolgálók egy Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tár hitelesítőadat-tanúsítványa. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a riasztások a Recovery services-tárolót. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi egy Azure-erőforrásokra "tároló" típusú objektum |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Művelet eredményeinek beolvasása művelettel olvashatók a műveleti állapota és az aszinkron módon elküldött művelet eredményének beolvasása |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel az erőforráshoz regisztrált tárolók beolvasása. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A szolgáltatástároló regisztrálása művelettel egy tároló regisztrálni a helyreállítási szolgáltatás használható. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/read | A storage-fiókok vagy a megadott tárfiók tulajdonságainak beolvasása adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | A védett elem a művelet érvényesítése |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Adja vissza a biztonsági mentési művelet állapotának helyreállítási tár. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Házirendművelet állapotának beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Egy regisztrált tároló létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Tárolóban lévő számítási feladatok lekérdezése |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | A tárolóban regisztrált biztonsági másolatokat kezelő kiszolgálókat adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | A biztonsági másolat védelmi leképezésének létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | A biztonsági másolat védelmi leképezésének beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Összes védhető tároló beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | A tároló összes elemének lekérdezése |
> | Microsoft.RecoveryServices/locations/backupStatus/action | A helyreállítási tárak biztonsági mentési állapotának ellenőrzése |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Funkciók ellenőrzése |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | A riasztás feloldása. |
> | Microsoft.RecoveryServices/operations/read | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Műveleti állapotának beolvasása egy adott művelethez |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Az összes biztonsági mentés védelmi leképezések listázása |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="backup-reader"></a>Biztonsági mentési olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a biztonsági mentési szolgáltatásai, de nem végezhet módosításokat |
> | **Azonosító** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotának visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Beolvassa a védelmi tárolón végrehajtott művelet eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Beolvassa a védett elemeken végrehajtott művelet eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem objektumadatainak visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Védett elemek helyreállítási pontjainak beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Az összes regisztrált tároló visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | A feladatművelet eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Összes feladatobjektum visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | A Recovery Services-tárolóval kapcsolatos biztonsági mentés eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A szabályzatművelet eredményeinek beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Összes védelmi szabályzat visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | A védett elemek listájának beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tároló visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Használati összegzéseket adja vissza a védett elemek és védett kiszolgálók egy Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a riasztások a Recovery services-tárolót. |
> | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi egy Azure-erőforrásokra "tároló" típusú objektum |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Művelet eredményeinek beolvasása művelettel olvashatók a műveleti állapota és az aszinkron módon elküldött művelet eredményének beolvasása |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel az erőforráshoz regisztrált tárolók beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Tárkonfigurációjának visszaadása a Recovery Services-tároló. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Konfigurációjának visszaadása Recovery Services-tároló. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Adja vissza a biztonsági mentési művelet állapotának helyreállítási tár. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Házirendművelet állapotának beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | A tárolóban regisztrált biztonsági másolatokat kezelő kiszolgálókat adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | A biztonsági másolat védelmi leképezésének beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | A tároló összes elemének lekérdezése |
> | Microsoft.RecoveryServices/locations/backupStatus/action | A helyreállítási tárak biztonsági mentési állapotának ellenőrzése |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | A riasztás feloldása. |
> | Microsoft.RecoveryServices/operations/read | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Műveleti állapotának beolvasása egy adott művelethez |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Az összes biztonsági mentés védelmi leképezések listázása |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="billing-reader"></a>Számlázás olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvasási hozzáférést biztosít a számlázási adatokat |
> | **Azonosító** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Billing/*/read | Olvassa el a számlázási adatokat |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportok listája. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="biztalk-contributor"></a>BizTalk Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a BizTalk-szolgáltatások kezelését, ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.BizTalkServices/BizTalk/* | A BizTalk-szolgáltatások létrehozásához és kezeléséhez |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="blockchain-member-node-access-preview"></a>Blockchain-tag-csomópont elérése (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Blockchain tagcsomópontok hozzáférésének engedélyezése |
> | **Azonosító** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Műveletek** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Beolvassa vagy listázza a meglévő Blockchain tag tranzakció csomópont. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | A Blockchain tag tranzakció csomópont csatlakozik. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cdn-endpoint-contributor"></a>CDN-végpont Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a CDN-végpontokat, de nem tud hozzáférést adni más felhasználóknak. |
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
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cdn-endpoint-reader"></a>CDN-végpont olvasója
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
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cdn-profile-contributor"></a>A CDN-profil Közreműködője
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
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

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
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="classic-network-contributor"></a>Klasszikus hálózati közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a hagyományos hálózatok kezelését, ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.ClassicNetwork/* | Hozzon létre, és a klasszikus hálózatok kezelése |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="classic-storage-account-contributor"></a>Hagyományos Tárfiók-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a klasszikus tárfiókok kezelését, ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.ClassicStorage/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Hagyományos tároló fiók fő operátora – szolgáltatási szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Klasszikus Tárfiók kulcsának operátorai jogosultak a hagyományos Tárfiókokhoz kulcsok listázására és újragenerálására |
> | **Azonosító** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Műveletek** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | A storage-tárfiókok elérési kulcsainak listázása. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | A tárfiók meglévő elérési kulcsainak újragenerálása. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="classic-virtual-machine-contributor"></a>Virtuális gépek hagyományos Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a klasszikus virtuális gépek, de nem biztosít hozzáférést, és nem a virtuális hálózat vagy kapcsolódó tárfiók kezelését. |
> | **Azonosító** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.ClassicCompute/domainNames/* | Hozzon létre, és a klasszikus számítási tartománynevek kezelése |
> | Microsoft.ClassicCompute/virtualMachines/* | Virtuális gépek létrehozása és kezelése |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Hivatkozás a fenntartott IP-cím |
> | Microsoft.ClassicNetwork/reservedIps/read | A fenntartott IP-címek beolvasása |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Csatlakozás a virtuális hálózathoz. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Virtuális hálózat beolvasása. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | A tárfióklemez adja vissza. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | A tárfióklemezkép adja vissza. (Elavult. Használja a "Microsoft.ClassicStorage/storageAccounts/vmImages") |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | A storage-tárfiókok elérési kulcsainak listázása. |
> | Microsoft.ClassicStorage/storageAccounts/read | Vissza a tárfiók a megadott fiók. |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cognitive-services-contributor"></a>A cognitive Services-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy létrehozása, olvasása, frissítése, törlése és a Cognitive Services, kulcsok kezelése. |
> | **Azonosító** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Lekéri az előfizetéshez tartozó szolgáltatásokat. |
> | Microsoft.Features/providers/features/read | Az előfizetés a szolgáltatás lekéri az adott erőforrás-szolgáltatón. |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Hoz létre, a frissítések vagy az Analysis Server diagnosztikai beállításának beolvasása |
> | Microsoft.Insights/logDefinitions/read | A naplódefiníciók olvasása |
> | Microsoft.Insights/metricdefinitions/read | A metrikadefiníciók olvasása |
> | Microsoft.Insights/metrics/read | Metrikák olvasása |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveletek. |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
> | Microsoft.Resources/subscriptions/read | Az előfizetések listájának beolvasása. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cognitive-services-data-reader-preview"></a>A cognitive Services-adatok olvasója (minta)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Cognitive Services-adatok olvasása. |
> | **Azonosító** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Műveletek** |  |
> | *none* |  |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cognitive-services-user"></a>A cognitive Services-felhasználó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy Ön elolvassa és a Cognitive Services, kulcsok listázását. |
> | **Azonosító** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Műveletek** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Kulcsok listázása |
> | Microsoft.Insights/alertRules/read | Klasszikus metrikariasztás olvasása |
> | Microsoft.Insights/diagnosticSettings/read | Egy erőforrás diagnosztikai beállítás beolvasása |
> | Microsoft.Insights/logDefinitions/read | A naplódefiníciók olvasása |
> | Microsoft.Insights/metricdefinitions/read | A metrikadefiníciók olvasása |
> | Microsoft.Insights/metrics/read | Metrikák olvasása |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveletek. |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
> | Microsoft.Resources/subscriptions/read | Az előfizetések listájának beolvasása. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cosmos-db-account-reader-role"></a>A cosmos DB-fiók olvasói szerepköre
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
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cosmos-db-operator"></a>A cosmos DB-operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az Azure Cosmos DB-fiókok, de nincs hozzáférés az adatokhoz a azok kezelését. Megakadályozza, hogy a hozzáférési kulcsokat és kapcsolati karakterláncokat. |
> | **Azonosító** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Műveletek** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Elküldhet egy Cosmos DB-adatbázis visszaállítási kérelmében vagy egy tároló-fiókot |
> | **Azonosító** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Műveletek** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Biztonsági mentés konfigurálása kéréséhez |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | A visszaállítási kérés beküldése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cost-management-contributor"></a>A Cost Management Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Költségek megtekintheti és kezelheti a költség-konfiguráció (pl. költségvetéseket, export) |
> | **Azonosító** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Műveletek** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read | Felsorolja a rendelkezésre álló elszámolási időszakok |
> | Microsoft.Resources/subscriptions/read | Az előfizetések listájának beolvasása. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Advisor/configurations/read | Konfiguráció beolvasása |
> | Microsoft.Advisor/recommendations/read | Javaslatok beolvasása |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

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
> | Microsoft.Resources/subscriptions/read | Az előfizetések listájának beolvasása. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Advisor/configurations/read | Konfiguráció beolvasása |
> | Microsoft.Advisor/recommendations/read | Javaslatok beolvasása |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="data-box-contributor"></a>Data Box Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a Data Box szolgáltatás található minden elemet kivételével másoknak való hozzáférés megadását teszi lehetővé. |
> | **Azonosító** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Databox/* |  |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

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
> | Microsoft.Databox/locations/availableSkus/action | Ez a módszer a rendelkezésre álló termékváltozatok listáját adja vissza. |
> | Microsoft.Databox/locations/validateAddress/action | A szállítási cím ellenőrzése és másodlagos címek biztosítása, ha van ilyen. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="data-factory-contributor"></a>Data Factory Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Létrehozhat és kezelhet adat-előállítók, valamint az azokhoz tartozó gyermekerőforrásait. |
> | **Azonosító** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.DataFactory/dataFactories/* | Létrehozhat és kezelhet adat-előállítók és a bennük található gyermekerőforrásait. |
> | Microsoft.DataFactory/factories/* | Létrehozhat és kezelhet adat-előállítók és a bennük található gyermekerőforrásait. |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics Developer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi elküldése, monitorozásához és a saját feladatok kezelése, de nem létrehozása vagy törlése a Data Lake Analytics-fiókok. |
> | **Azonosító** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
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
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="data-purger"></a>Adatok Purger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Elemzési adatok is kiürítheti. |
> | **Azonosító** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Műveletek** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Adatok végleges törlése az Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Megadott adatok törlése a munkaterületről |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="devtest-labs-user"></a>DevTest Labs User
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy csatlakozik, kezdő, újraindítását és leállítását a virtuális gépek az Azure DevTest Labs-környezetben. |
> | **Azonosító** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.Compute/availabilitySets/read | Rendelkezésre állási csoport tulajdonságainak beolvasása |
> | Microsoft.Compute/virtualMachines/*/read | A virtuális gépek (VM-méretek, futásidejű állapot, a Virtuálisgép-bővítmények, stb.) tulajdonságainak olvasása |
> | Microsoft.Compute/virtualMachines/deallocate/action | A virtuális gépet és felszabadítja a számítási erőforrások kikapcsolása |
> | Microsoft.Compute/virtualMachines/read | Virtuális gép tulajdonságainak beolvasása |
> | Microsoft.Compute/virtualMachines/restart/action | A virtuális gép újraindítása |
> | Microsoft.Compute/virtualMachines/start/action | A virtuális gép indítása |
> | Microsoft.DevTestLab/*/read | Labor tulajdonságainak olvasása |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Jogcím egy véletlenszerű igényelhető virtuális gép a tesztkörnyezetben. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Virtuális gépek létrehozása a lab-ben. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Győződjön meg arról, az aktuális felhasználónak van érvényes profilt a tesztkörnyezetben. |
> | Microsoft.DevTestLab/labs/formulas/delete | Törölje a képletek. |
> | Microsoft.DevTestLab/labs/formulas/read | Olvassa el a képletek. |
> | Microsoft.DevTestLab/labs/formulas/write | Adjon hozzá vagy módosíthatja a képleteket. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Kiértékeli a labor házirend. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Meglévő virtuális gép tulajdonjogának átvétele |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | A alkalmazni indítása és leállítása ütemezések sorolja fel, ha van ilyen. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Egy karakterlánc, amely a virtuális gép RDP-fájl tartalmának beolvasása |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | A load balancer háttércímkészlet csatlakozik. Nem Alertable. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Csatlakozik egy terheléselosztó bejövő nat-szabályt. Nem Alertable. |
> | Microsoft.Network/networkInterfaces/*/read | Hálózati adapter (például az összes a terheléselosztók, amely a hálózati adapter egy része) tulajdonságainak olvasása |
> | Microsoft.Network/networkInterfaces/join/action | Virtuális gép csatlakozik egy hálózati adapterhez. Nem Alertable. |
> | Microsoft.Network/networkInterfaces/read | Lekérdezi egy hálózati illesztőjének definícióját.  |
> | Microsoft.Network/networkInterfaces/write | Hálózati adapter létrehozása vagy frissítése egy meglévő hálózati adaptert.  |
> | Microsoft.Network/publicIPAddresses/*/read | Nyilvános IP-cím tulajdonságainak olvasása |
> | Microsoft.Network/publicIPAddresses/join/action | Nyilvános ip-címmel csatlakozik. Nem Alertable. |
> | Microsoft.Network/publicIPAddresses/read | Lekérdezi egy nyilvános IP-cím cím definíciója. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Egy virtuális hálózathoz csatlakozik. Nem Alertable. |
> | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveletek. |
> | Microsoft.Resources/deployments/read | Beolvassa vagy listázza az üzemelő példányok. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsainak. |
> | **notActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Felsorolja az elérhető méretek a virtuális gép frissíthető |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="dns-zone-contributor"></a>DNS Zone Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a DNS-zónák és -rekordhalmazok az Azure DNS kezelését, de nem teszi lehetővé az azokhoz való hozzáférés szabályozásához. |
> | **Azonosító** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.Network/dnsZones/* | DNS-zónák és rekordok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="documentdb-account-contributor"></a>DocumentDB Account Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti az Azure Cosmos DB-fiókokhoz. Az Azure Cosmos DB DocumentDB nevén. |
> | **Azonosító** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB-fiókok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="event-hubs-data-owner"></a>Event Hubs-adatok tulajdonosa

> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az Azure Event Hubs-erőforrások teljes hozzáférést tesz lehetővé. |
> | **Azonosító** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Műveletek** |  |
> | Microsoft.EventHubs/* | Lehetővé teszi, hogy a teljes felügyeleti hozzáférés az Event Hubs-névtér |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.EventHubs/* | Lehetővé teszi, hogy a teljes adathozzáférést Event Hubs-névtér |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi EventGrid esemény-előfizetési műveletek kezelését. |
> | **Azonosító** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Lista globális eseményelőfizetések témakörtípus szerint |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Regionális esemény-előfizetések listája |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Lista regionális eseményelőfizetések topictype szerint |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvassa el az esemény-előfizetések EventGrid teszi lehetővé. |
> | **Azonosító** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.EventGrid/eventSubscriptions/read | Egy eventSubscription olvasása |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Lista globális eseményelőfizetések témakörtípus szerint |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Regionális esemény-előfizetések listája |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Lista regionális eseményelőfizetések topictype szerint |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="hdinsight-cluster-operator"></a>HDInsight-fürt operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy olvassa el, és a HDInsight-fürt konfigurációjának módosítását. |
> | **Azonosító** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **Műveletek** |  |
> | Microsoft.HDInsight/*/read |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | HDInsight-fürt átjáró beállításainak lekérése |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | HDInsight-fürt átjáró-beállításainak frissítése |
> | Microsoft.HDInsight/clusters/configurations/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveletek. |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

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
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="intelligent-systems-account-contributor"></a>Intelligens rendszerek Fiókközreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az intelligens rendszerek fiókjainak kezelését, ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.IntelligentSystems/accounts/* | Intelligens rendszerek fiókok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="key-vault-contributor"></a>Key Vault-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a kulcstartók kezelését, ezekhez nem biztosít hozzáférést. |
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
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="lab-creator"></a>Tesztkörnyezet létrehozója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a létrehozását, kezelését, és törölje a felügyelt tesztkörnyezetek az Azure Lab-fiókokban. |
> | **Azonosító** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Labor létrehozása a lab-fiókokban. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Tesztkörnyezetfiók konfigurált mérete kategóriákhoz tartozó régiónkénti rendelkezésre állás információk lekérése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="log-analytics-contributor"></a>Log Analytics közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Log Analytics-közreműködő az összes monitorozási adat olvashatja és szerkesztheti a figyelési beállításokat. Figyelési beállítások szerkesztése magában foglalja a virtuális gépek; a Virtuálisgép-bővítmény hozzáadása tudni naplógyűjtemény konfigurálása céljából az Azure Storage; tárfiók kulcsainak beolvasását létrehozásáról és konfigurálásáról az Automation-fiókok; megoldások hozzáadását; és az Azure diagnostics konfigurálása az összes Azure-erőforrást. |
> | **Azonosító** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Műveletek** |  |
> | */read | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | A storage-tárfiókok elérési kulcsainak listázása. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Hoz létre, a frissítések vagy az Analysis Server diagnosztikai beállításának beolvasása |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsainak. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="log-analytics-reader"></a>Log Analytics olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Log Analytics olvasó megtekintheti és keresheti az összes figyelési adatot, valamint megtekintheti a figyelési beállításokat, beleértve az Azure diagnostics konfigurációjának megtekintését az összes Azure-erőforrások. |
> | **Azonosító** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Műveletek** |  |
> | */read | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Új motor használatával való keresés. |
> | Microsoft.OperationalInsights/workspaces/search/action | Végrehajt egy keresési lekérdezést |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Lekéri a munkaterület a megosztott kulcsok. Ezekkel a kulcsokkal a Microsoft Operational Insights-ügynökök kapcsolódni a munkaterülethez. |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="logic-app-contributor"></a>A logikai alkalmazás Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy a logikai alkalmazás kezelését, ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | A storage-tárfiókok elérési kulcsainak listázása. |
> | Microsoft.ClassicStorage/storageAccounts/read | Vissza a tárfiók a megadott fiók. |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Hoz létre, a frissítések vagy az Analysis Server diagnosztikai beállításának beolvasása |
> | Microsoft.Insights/logdefinitions/* | Erre az engedélyre szükség a felhasználók számára, akik hozzáférhetnek a vizsgálati naplók a portálon keresztül. Lista naplókategóriák a tevékenységnaplóban. |
> | Microsoft.Insights/metricDefinitions/* | (Erőforrás rendelkezésre álló metrika típusok listája) metrikadefiníciók olvasása. |
> | Microsoft.Logic/* | A Logic Apps-erőforrások kezelése. |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsainak. |
> | Microsoft.Storage/storageAccounts/read | A storage-fiókok vagy a megadott tárfiók tulajdonságainak beolvasása adja vissza. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Web/connectionGateways/* | Hozzon létre és kezeli a kapcsolódási átjáró. |
> | Microsoft.Web/connections/* | Hozzon létre és kezeli a kapcsolat. |
> | Microsoft.Web/customApis/* | Hozza létre, és a egy egyéni API-t felügyeli. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Az App Service-csomag tulajdonságainak beolvasása |
> | Microsoft.Web/sites/functions/listSecrets/action | Lista titkok Web Apps-függvényekre. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="logic-app-operator"></a>Logikai alkalmazás operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy olvasását, engedélyezését és a logikai alkalmazás letiltása. |
> | **Azonosító** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/*/read | Olvasási Insights – riasztási szabályok |
> | Microsoft.Insights/diagnosticSettings/*/read | A Logic Apps diagnosztikai beállításainak beolvasása |
> | Microsoft.Insights/metricDefinitions/*/read | A Logic Apps elérhető metrikáinak beolvasása. |
> | Microsoft.Logic/*/read | A Logic Apps-erőforrások beolvasása. |
> | Microsoft.Logic/workflows/disable/action | Letiltja a munkafolyamatot. |
> | Microsoft.Logic/workflows/enable/action | A munkafolyamat engedélyezése. |
> | Microsoft.Logic/workflows/validate/action | Érvényesíti a munkafolyamatot. |
> | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveletek. |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Web/connectionGateways/*/read | Olvassa el a kapcsolódási átjáró. |
> | Microsoft.Web/connections/*/read | Olvassa el a kapcsolatot. |
> | Microsoft.Web/customApis/*/read | Olvassa el az egyéni API-t. |
> | Microsoft.Web/serverFarms/read | Az App Service-csomag tulajdonságainak beolvasása |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="managed-application-operator-role"></a>Felügyelt alkalmazás operátori szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvassa el, és műveleteket hajthat végre felügyelt alkalmazásokra vonatkozó erőforrásokhoz |
> | **Azonosító** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Műveletek** |  |
> | */read | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.Solutions/applications/read | Alkalmazások listájának beolvasása. |
> | Microsoft.Solutions/*/action |  |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="managed-applications-reader"></a>Felügyelt alkalmazások Adatolvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvassa el a felügyelt alkalmazás és igény szerinti hozzáférés kérése az erőforrások teszi lehetővé. |
> | **Azonosító** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Műveletek** |  |
> | */read | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Solutions/jitRequests/* |  |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="managed-identity-contributor"></a>Managed Identity Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Create, Read, Update, and Delete User Assigned Identity |
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
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="managed-identity-operator"></a>Felügyelt identitások üzemeltetője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvassa el, és rendelje hozzá a felhasználóhoz hozzárendelt identitás |
> | **Azonosító** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Műveletek** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="management-group-contributor"></a>A felügyeleti csoport Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A felügyeleti csoport közreműködői szerepkört |
> | **Azonosító** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Műveletek** |  |
> | Microsoft.Management/managementGroups/delete | Felügyeleti csoport törlése. |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportok listája. |
> | Microsoft.Management/managementGroups/subscriptions/delete | A felügyeleti csoportból előfizetés megszüntetéséhez társítja. |
> | Microsoft.Management/managementGroups/subscriptions/write | Hozzárendeli a meglévő előfizetés a felügyeleti csoporttal. |
> | Microsoft.Management/managementGroups/write | Hozzon létre, vagy a felügyeleti csoport frissítése. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="management-group-reader"></a>A felügyeleti csoport olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A felügyeleti csoport olvasói szerepköre |
> | **Azonosító** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Műveletek** |  |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportok listája. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="monitoring-contributor"></a>Közreműködő figyelése
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az összes monitorozási adat olvashatja és szerkesztheti a figyelési beállításokat. Lásd még: [szerepkörök, engedélyek és biztonság az Azure Monitor használatának első lépései](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Azonosító** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Műveletek** |  |
> | */read | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
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
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Olvasási, írási és törlési log analytics megoldás csomagok. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Olvasási, írási és törlési a log analytics mentett keresések. |
> | Microsoft.OperationalInsights/workspaces/search/action | Végrehajt egy keresési lekérdezést |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Lekéri a munkaterület a megosztott kulcsok. Ezekkel a kulcsokkal a Microsoft Operational Insights-ügynökök kapcsolódni a munkaterülethez. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Olvasási, írási és törlési log analytics storage insight konfigurációkat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="monitoring-metrics-publisher"></a>Figyelési metrikák közzétevő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy metrikákat az Azure-erőforrások közzététele |
> | **Azonosító** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Műveletek** |  |
> | Microsoft.Insights/Register/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Metrikák írása |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="monitoring-reader"></a>Olvasó figyelése
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvashatja az összes figyelési adatot (metrikákat, naplókat, stb.). Lásd még: [szerepkörök, engedélyek és biztonság az Azure Monitor használatának első lépései](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Azonosító** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Műveletek** |  |
> | */read | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.OperationalInsights/workspaces/search/action | Végrehajt egy keresési lekérdezést |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="network-contributor"></a>Hálózati közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a hálózatok kezelését, ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.Network/* | Hozzon létre és hálózatok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="new-relic-apm-account-contributor"></a>Új Relic APM-Fiókközreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi új Relic alkalmazásteljesítmény-felügyeleti fiókok és az alkalmazások, de az azokhoz való hozzáférés kezelését. |
> | **Azonosító** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | NewRelic.APM/accounts/* |  |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="reader-and-data-access"></a>Olvasó és adatelérés
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi minden megtekintését, de nem teszi lehetővé, törölheti, vagy hozzon létre egy storage-fiók vagy az abban található erőforrást. Olvasási/írási hozzáférést a tárfiókok kulcsainak elérésével storage-fiókban tárolt adatokat is engedélyezi. |
> | **Azonosító** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsainak. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | A megadott tárfiók a fiók SAS-jogkivonatát adja vissza. |
> | Microsoft.Storage/storageAccounts/read | A storage-fiókok vagy a megadott tárfiók tulajdonságainak beolvasása adja vissza. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="redis-cache-contributor"></a>Redis gyorsítótárak Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Redis-gyorsítótárak kezelését, ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.Cache/redis/* | Hozzon létre, és a redis Cache gyorsítótárak kezelése |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="resource-policy-contributor-preview"></a>Erőforrás-szabályzati közreműködő (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | (Előzetes verzió) Erőforrás-szabályzat létrehozására/módosítására jogosultságokkal EA-beli visszatöltött felhasználók hozzon létre támogatási jegyet, és erőforrások/hierarchia beolvasására. |
> | **Azonosító** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Műveletek** |  |
> | */read | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.Authorization/policyassignments/* | Hozzon létre, és a szabályzat-hozzárendelések kezelése |
> | Microsoft.Authorization/policydefinitions/* | Hozzon létre és kezelhet szabályzatdefiníciókat |
> | Microsoft.Authorization/policysetdefinitions/* | Létrehozása és kezelése a szabályzat beállítása |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="scheduler-job-collections-contributor"></a>A Scheduler Feladatgyűjteményeinek Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Scheduler-feladatgyűjtemények kezelését, ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Scheduler/jobcollections/* | Feladat gyűjtemények létrehozásához és kezeléséhez |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="search-service-contributor"></a>Search szolgáltatás Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a keresési szolgáltatások kezelését, ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Search/searchServices/* | Keresési szolgáltatások létrehozásához és kezeléséhez |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="security-admin"></a>Biztonsági rendszergazda
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A Security Center csak: Megtekintheti biztonsági házirendek, biztonsági állapotot, szerkessze a biztonsági szabályzatok, riasztások megtekintése és javaslatok, riasztások és javaslatok elvetése |
> | **Azonosító** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Authorization/policyAssignments/* | Hozzon létre, és a szabályzat-hozzárendelések kezelése |
> | Microsoft.Authorization/policyDefinitions/* | Hozzon létre és kezelhet szabályzatdefiníciókat |
> | Microsoft.Authorization/policySetDefinitions/* | Létrehozása és kezelése a szabályzat beállítása |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportok listája. |
> | Microsoft.operationalInsights/workspaces/*/read | Log analytics-adatok megtekintése |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="security-manager-legacy"></a>Biztonságkezelő (örökölt)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Ez az örökölt szerepkör. Használja helyette a biztonsági rendszergazda |
> | **Azonosító** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.ClassicCompute/*/read | Olvassa el a konfigurációs adatokat a klasszikus virtuális gépek |
> | Microsoft.ClassicCompute/virtualMachines/*/write | A klasszikus virtuális gépek konfigurációjának írása |
> | Microsoft.ClassicNetwork/*/read | Klasszikus hálózati konfigurációs információkat olvasása |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Security/* | Biztonsági összetevők és a szabályzatok létrehozása és kezelése |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="security-reader"></a>Biztonsági olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A Security Center csak: Megtekintheti a javaslatok és riasztások, a biztonsági házirendek, a biztonsági állapotot, de nem végezhet módosításokat megtekintése |
> | **Azonosító** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.operationalInsights/workspaces/*/read | Log analytics-adatok megtekintése |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Security/*/read | Olvasási biztonsági összetevőinek és a szabályzatok |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportok listája. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="service-bus-data-owner"></a>Service Bus adatok tulajdonosa

> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az Azure Service Bus-erőforrások teljes hozzáférést tesz lehetővé. |
> | **Azonosító** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Műveletek** |  |
> | Microsoft.ServiceBus/* | Lehetővé teszi, hogy a teljes felügyeleti hozzáférés a Service Bus-névtér |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* | Lehetővé teszi, hogy a teljes adathozzáférést a Service Bus-névtér |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="site-recovery-contributor"></a>Site Recovery-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Site Recovery szolgáltatást, kivéve a tárolók létrehozását és a szerepkör-hozzárendelés kezelését |
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
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A Tártoken művelettel Társzintű Háttérműveletekhez beolvasni a tároló szint háttérbeli műveletek használható. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Olvassa el a riasztások a Recovery services-tároló |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/read | A storage-fiókok vagy a megadott tárfiók tulajdonságainak beolvasása adja vissza. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="site-recovery-operator"></a>Site Recovery-operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a feladatátvétel és feladat-visszavétel, de nem biztosít egyéb Site Recovery felügyeleti műveleteket |
> | **Azonosító** | 494ae006-db33-4328-bf46-533a6560a3ca |
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
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Update Mobility Service |
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
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A Tártoken művelettel Társzintű Háttérműveletekhez beolvasni a tároló szint háttérbeli műveletek használható. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/read | A storage-fiókok vagy a megadott tárfiók tulajdonságainak beolvasása adja vissza. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

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
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A Tártoken művelettel Társzintű Háttérműveletekhez beolvasni a tároló szint háttérbeli műveletek használható. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="spatial-anchors-account-contributor"></a>Térbeli horgonyok közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy térbeli horgonyok kezelése a fiókban, de ne törölje őket |
> | **Azonosító** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Műveletek** |  |
> | *none* |  |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Térbeli horgonyokat létrehozni |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Térbeli horgonyok közeli felderítése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak beolvasása |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Keresse meg a térbeli horgonyok |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Az Azure térbeli horgonyok minőségének javítása érdekében a diagnosztikai adatok beküldése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Térbeli horgonyok tulajdonságainak frissítése |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="spatial-anchors-account-owner"></a>Térbeli horgonyok fiók tulajdonosa
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a fiókjában, beleértve a törlésük térbeli horgonyok kezelését |
> | **Azonosító** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Műveletek** |  |
> | *none* |  |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Térbeli horgonyokat létrehozni |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Térbeli kapcsolatok alapjainak törlése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Térbeli horgonyok közeli felderítése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak beolvasása |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Keresse meg a térbeli horgonyok |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Az Azure térbeli horgonyok minőségének javítása érdekében a diagnosztikai adatok beküldése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Térbeli horgonyok tulajdonságainak frissítése |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="spatial-anchors-account-reader"></a>Térbeli horgonyok fiók olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Keresse meg és a fiókban térbeli horgonyok tulajdonságainak olvasása |
> | **Azonosító** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Műveletek** |  |
> | *none* |  |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Térbeli horgonyok közeli felderítése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak beolvasása |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Keresse meg a térbeli horgonyok |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Az Azure térbeli horgonyok minőségének javítása érdekében a diagnosztikai adatok beküldése |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="sql-db-contributor"></a>SQL-Adatbázisok Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az SQL Database-adatbázisok kezelését, ezekhez nem biztosít hozzáférést. Emellett a biztonsággal kapcsolatos házirendjeiket vagy a szülő SQL Server nem tudja kezelni. |
> | **Azonosító** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre, és a riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | SQL-adatbázisok létrehozása és kezelése |
> | Microsoft.Sql/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgáló tulajdonságainak listáját adja vissza. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Insights/metrics/read | Metrikák olvasása |
> | Microsoft.Insights/metricDefinitions/read | A metrikadefiníciók olvasása |
> | **notActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Naplózási házirend szerkesztése |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Naplózási beállítások szerkesztése |
> | Microsoft.Sql/servers/databases/auditRecords/read | A blob naplózási rekordok beolvasása |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Kapcsolat-házirend szerkesztése |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Adatmaszkolás házirendek szerkesztése |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Biztonsági riasztás szabályzatok szerkesztése |
> | Microsoft.Sql/servers/databases/securityMetrics/* | A biztonság mértékét szerkesztése |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="sql-managed-instance-contributor"></a>SQL Managed Instance Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy az SQL felügyelt példányok kezelése és a szükséges hálózati konfiguráció, de nem adhat hozzáférést másoknak. |
> | **Azonosító** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **Műveletek** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Network/virtualNetworks/subnets/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Insights/metrics/read | Metrikák olvasása |
> | Microsoft.Insights/metricDefinitions/read | A metrikadefiníciók olvasása |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="sql-security-manager"></a>SQL-biztonságkezelő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az SQL Server-kiszolgálók és adatbázisok, de ezekhez nem biztosít hozzáférést a biztonsági házirendek kezelését. |
> | **Azonosító** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el a Microsoft-engedélyezés |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Például a storage-fiók vagy az SQL database erőforrás csatlakozik egy alhálózathoz. Nem alertable. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Az SQL server naplózási szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/auditingSettings/* | Hozzon létre, és az SQL-kiszolgáló naplózási beállításainak kezelése |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | A naplózás a megadott kiszolgálón konfigurált szabályzatot a kiterjesztett kiszolgáló BLOB részleteinek lekérése |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL server adatbázis naplózási szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Hozzon létre, és az SQL server-adatbázis naplózási beállításainak kezelése |
> | Microsoft.Sql/servers/databases/auditRecords/read | Olvassa el a naplórekordok |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Az SQL server adatbázis kapcsolati szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Hozzon létre, és az SQL server adatbázis adatmaszkolási szabályzatok kezelése |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | A bővített blob egy adott adatbázisban a következőn: naplózási házirend részleteinek beolvasása |
> | Microsoft.Sql/servers/databases/read | Adatbázisok vagy lekérdezi a megadott adatbázis tulajdonságainak listáját adja vissza. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Egy adatbázis-séma beolvasása. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Egy adatbázis oszlop beolvasása. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Egy adatbázis-tábla beolvasása. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Az SQL server adatbázis biztonsági riasztási szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Létrehozásához és kezeléséhez az SQL server-adatbázis biztonsági metrikák |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgáló tulajdonságainak listáját adja vissza. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Az SQL server biztonsági riasztás szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="sql-server-contributor"></a>Az SQL Server Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy SQL Server-kiszolgálók és adatbázisok kezelése, de nem érhető el, és nem a biztonsági-házirendjeinek. |
> | **Azonosító** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Hozzon létre és SQL-kiszolgálók kezelése |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Insights/metrics/read | Metrikák olvasása |
> | Microsoft.Insights/metricDefinitions/read | A metrikadefiníciók olvasása |
> | **notActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Az SQL server naplózási házirendek szerkesztése |
> | Microsoft.Sql/servers/auditingSettings/* | SQL-kiszolgáló naplózási beállításainak szerkesztése |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Az SQL server-adatbázis naplózási házirendek szerkesztése |
> | Microsoft.Sql/servers/databases/auditingSettings/* | SQL server-adatbázis naplózási beállításainak szerkesztése |
> | Microsoft.Sql/servers/databases/auditRecords/read | Olvassa el a naplórekordok |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Az SQL server adatbázis kapcsolati házirendek szerkesztése |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Az SQL server adatbázis adatmaszkolási házirendek szerkesztése |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Az SQL server adatbázis biztonsági riasztási házirendek szerkesztése |
> | Microsoft.Sql/servers/databases/securityMetrics/* | SQL server-adatbázis biztonsági metrikák szerkesztése |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Az SQL server biztonsági riasztás szabályzatok szerkesztése |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-account-contributor"></a>Tárfiók-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi tárfiókok kezelését. Nem biztosít hozzáférést a tárfiókban lévő adatokat. |
> | **Azonosító** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvassa el az összes engedélyt |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Diagnosztikai beállítások kezelése |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Például a storage-fiók vagy az SQL database erőforrás csatlakozik egy alhálózathoz. Nem alertable. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Storage/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-account-key-operator-service-role"></a>Tárolási fiók fő operátora – szolgáltatási szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyek listázása és a tárfiók hozzáférési kulcsainak újragenerálása. |
> | **Azonosító** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Vissza a megadott tárfiók hozzáférési kulcsait. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Generálja újra a megadott tárfiók hozzáférési kulcsait. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-blob-data-contributor"></a>Storage-Blobadatok Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvasási, írási és Azure Storage-tárolók és blobok törlése. A megadott művelet műveletek szükségesek kapcsolatban lásd: [hívása blob és üzenetsor Adatműveletek engedélyeinek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Törli a tárolót. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Egy tárolót vagy tárolók listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Egy tároló metaadatait vagy tulajdonságainak módosítása |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Blob törlése. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Egy blobot vagy blobok listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Egy blobba írni. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-blob-data-owner"></a>Tárolási Blob adatok tulajdonosa
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az Azure Storage-blobtárolók és adatait, beleértve a POSIX hozzáférés-vezérlés hozzárendelése teljes hozzáférést biztosít. A megadott művelet műveletek szükségesek kapcsolatban lásd: [hívása blob és üzenetsor Adatműveletek engedélyeinek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Tárolók teljes körű engedélyeket. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Blobok teljes körű engedélyeket. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-blob-data-reader"></a>Storage Blob Data Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvassa el, és az Azure Storage-tárolók és blobok listázása. A megadott művelet műveletek szükségesek kapcsolatban lásd: [hívása blob és üzenetsor Adatműveletek engedélyeinek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Egy tárolót vagy tárolók listáját adja vissza. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Egy blobot vagy blobok listáját adja vissza. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-queue-data-contributor"></a>Storage-Üzenetsorbeli adatok Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvasási, írási és törlési Azure Storage üzenetsorok és üzenetsorbeli üzenetek. A megadott művelet műveletek szükségesek kapcsolatban lásd: [hívása blob és üzenetsor Adatműveletek engedélyeinek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Üzenetsor törlése. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Egy üzenetsor vagy a sorok listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Üzenetsor metaadatai vagy tulajdonságainak módosítása |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Törölni egy vagy több üzenetet egy üzenetsorból. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Belepillantás vagy lekérdezni egy vagy több üzenetet egy üzenetsorból. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Adjon meg egy üzenetet egy üzenetsorba. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-queue-data-message-processor"></a>Tárolási üzenetsor üzenetet processzor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Belepillantás, lekérése és egy üzenet törlése az Azure Storage üzenetsorába. A megadott művelet műveletek szükségesek kapcsolatban lásd: [hívása blob és üzenetsor Adatműveletek engedélyeinek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Műveletek** |  |
> | *none* |  |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Egy üzenet megtekintése. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Kérje le, és az üzenet törlése. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-queue-data-message-sender"></a>Tárolási üzenetsor adatok üzenet küldője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Üzenetek hozzáadása az Azure Storage üzenetsorába. A megadott művelet műveletek szükségesek kapcsolatban lásd: [hívása blob és üzenetsor Adatműveletek engedélyeinek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Műveletek** |  |
> | *none* |  |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Adjon meg egy üzenetet egy üzenetsorba. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-queue-data-reader"></a>Storage Queue Data Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvassa el, és az Azure Storage üzenetsorok és üzenetsorbeli üzenetek listázása. A megadott művelet műveletek szükségesek kapcsolatban lásd: [hívása blob és üzenetsor Adatműveletek engedélyeinek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Egy üzenetsor vagy a sorok listáját adja vissza. |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Belepillantás vagy lekérdezni egy vagy több üzenetet egy üzenetsorból. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="support-request-contributor"></a>Támogatáskérési közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a támogatási kérések létrehozásához és kezeléséhez |
> | **Azonosító** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="traffic-manager-contributor"></a>Traffic Manager-közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz való hozzáférés szabályozásához. |
> | **Azonosító** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási szerepköröket és szerepkör-hozzárendelések |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="user-access-administrator"></a>Felhasználói hozzáférés rendszergazdája
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure-erőforrásokhoz való felhasználói hozzáférés kezelését teszi lehetővé. |
> | **Azonosító** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Műveletek** |  |
> | */read | Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat. |
> | Microsoft.Authorization/* | Hitelesítés |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="virtual-machine-administrator-login"></a>A virtuális gépre való rendszergazdai bejelentkezés
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A portálon, és jelentkezzen be rendszergazdai virtuális számítógépek megtekintése |
> | **Azonosító** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Műveletek** |  |
> | Microsoft.Network/publicIPAddresses/read | Lekérdezi egy nyilvános IP-cím cím definíciója. |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózati definíció beolvasása |
> | Microsoft.Network/loadBalancers/read | Load balancer definici beolvasása |
> | Microsoft.Network/networkInterfaces/read | Lekérdezi egy hálózati illesztőjének definícióját.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Jelentkezzen be egy virtuális gépre normál felhasználóként |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Jelentkezzen be egy virtuális géphez a Windows rendszergazdai vagy a Linux gyökérszintű felhasználójának jogosultságaival |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="virtual-machine-contributor"></a>Virtuális gépek Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi virtuális gépek, de nem biztosít hozzáférést, és nem a virtuális hálózat vagy kapcsolódó tárfiók kezelését. |
> | **Azonosító** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Compute/availabilitySets/* | Hozzon létre és számítási rendelkezésre állási készletek felügyelete |
> | Microsoft.Compute/locations/* | Létrehozni és kezelni a számítási helyek |
> | Microsoft.Compute/virtualMachines/* | Virtuális gépek létrehozása és kezelése |
> | Microsoft.Compute/virtualMachineScaleSets/* | Létrehozása és kezelése a virtual machine scale sets |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Egy application gateway háttércímkészlet csatlakozik. Nem Alertable. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | A load balancer háttércímkészlet csatlakozik. Nem Alertable. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Csatlakozik a terheléselosztó bejövő NAT-készlet. Nem alertable. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Csatlakozik egy terheléselosztó bejövő nat-szabályt. Nem Alertable. |
> | Microsoft.Network/loadBalancers/probes/join/action | Lehetővé teszi, hogy a terheléselosztó a mintavételezők használatával. Az ezen engedély healthProbe tulajdonságot a Virtuálisgép-méretezési csoport például set hivatkozhat a mintavétel. Nem alertable. |
> | Microsoft.Network/loadBalancers/read | Load balancer definici beolvasása |
> | Microsoft.Network/locations/* | Hozzon létre és kezelheti a hálózati helyek |
> | Microsoft.Network/networkInterfaces/* | Hozzon létre, és a hálózati adapterek kezelése |
> | Microsoft.Network/networkSecurityGroups/join/action | Csatlakoztatja a hálózati biztonsági csoport. Nem Alertable. |
> | Microsoft.Network/networkSecurityGroups/read | Egy hálózati biztonsági csoport definíció beolvasása |
> | Microsoft.Network/publicIPAddresses/join/action | Nyilvános ip-címmel csatlakozik. Nem Alertable. |
> | Microsoft.Network/publicIPAddresses/read | Lekérdezi egy nyilvános IP-cím cím definíciója. |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózati definíció beolvasása |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Egy virtuális hálózathoz csatlakozik. Nem Alertable. |
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
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsainak. |
> | Microsoft.Storage/storageAccounts/read | A storage-fiókok vagy a megadott tárfiók tulajdonságainak beolvasása adja vissza. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="virtual-machine-user-login"></a>A virtuális gépre való felhasználói bejelentkezés
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
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Jelentkezzen be egy virtuális gépre normál felhasználóként |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="web-plan-contributor"></a>Webes Tarifacsomagok Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi számukra a websites, de nem biztosít hozzáférést a webes tarifacsomagok kezelését. |
> | **Azonosító** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Web/serverFarms/* | Hozzon létre, és a kiszolgálófarmok kezelése |
> | Microsoft.Web/hostingEnvironments/Join/Action | App Service-környezet csatlakozik |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="website-contributor"></a>Webhelyek Közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a webhelyek (a webes tarifacsomagokét) kezelését, ezekhez nem biztosít hozzáférést. |
> | **Azonosító** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/read | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Hozzon létre és Insights – riasztási szabályok kezelése |
> | Microsoft.Insights/components/* | Létrehozása és kezelése az Insights-összetevők |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A rendelkezésre állási állapotok beolvasása az összes erőforrás a megadott hatókörben |
> | Microsoft.Resources/deployments/* | Erőforráscsoportok üzemelő példányainak elindíthatók |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Microsoft.Support/* | Hozzon létre, és a támogatási jegyek kezelése |
> | Microsoft.Web/certificates/* | Webhely tanúsítványok létrehozásához és felügyeletéhez |
> | Microsoft.Web/listSitesAssignedToHostName/read | Lekérése az állomásnév rendelt helyek nevét. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Az App Service-csomag tulajdonságainak beolvasása |
> | Microsoft.Web/sites/* | Létrehozása és kezelése a websites (webhelyek létrehozását is szükséges írási engedéllyel a társított App Service-csomag) |
> | **notActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="next-steps"></a>További lépések

- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure Portal használatával](role-assignments-portal.md)
- [Engedélyek az Azure Security Centerben](../security-center/security-center-permissions.md)
