---
title: Beépített szerepkörök az Azure-erőforrásokhoz | Microsoft Docs
description: Ismerteti a szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure-erőforrások beépített szerepköreit. Felsorolja a műveleteket, a nem Tapintatokat, a DataActions és a NotDataActions.
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
ms.date: 07/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: de068563e50da4510343572fd641aadd93157073
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868647"
---
# <a name="built-in-roles-for-azure-resources"></a>Beépített szerepkörök az Azure-erőforrásokhoz

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) számos beépített szerepkörrel rendelkezik az Azure-erőforrásokhoz, amelyeket hozzárendelhet a felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz és felügyelt identitásokhoz. A szerepkör-hozzárendelések lehetővé teszik az Azure-erőforrásokhoz való hozzáférés szabályozását. Ha a beépített szerepkörök nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját [Egyéni szerepköröket az Azure](custom-roles.md)-erőforrásokhoz.

Ez a cikk az Azure-erőforrások beépített szerepköreit sorolja fel, amelyek folyamatosan fejlődnek. A legújabb szerepkörök beszerzéséhez használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) vagy [az az role definition List](/cli/azure/role/definition#az-role-definition-list)lehetőséget. Ha a Azure Active Directory rendszergazdai szerepköreit keresi, tekintse meg a [rendszergazdai szerepkör engedélyeit Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Beépített szerepkör-leírások

A következő táblázat az egyes beépített szerepkörök rövid leírását tartalmazza. Az egyes szerepkörök listájának `Actions` `NotActions` `DataActions` megtekintéséhezkattintsonaszerepkörnevére`NotDataActions` . További információ ezekről a műveletekről, valamint a kezelési és adatsíkokra való alkalmazásáról: az [Azure-erőforrások szerepkör](role-definitions.md)-definícióinak megismerése.


| Beépített szerepkör | Leírás |
| --- | --- |
| [Tulajdonos](#owner) | Lehetővé teszi az összes funkció kezelését, beleértve az erőforrásokhoz való hozzáférést is. |
| [Közreműködő](#contributor) | Lehetővé teszi, hogy mindent kezelje, kivéve az erőforrásokhoz való hozzáférést. |
| [Olvasó](#reader) | Lehetővé teszi, hogy mindent megtekintse, de ne végezzen módosításokat. |
| [AcrDelete](#acrdelete) | ACR törlése |
| [AcrImageSigner](#acrimagesigner) | ACR-rendszerkép aláírója |
| [AcrPull](#acrpull) | ACR-lekérés |
| [AcrPush](#acrpush) | ACR leküldése |
| [AcrQuarantineReader](#acrquarantinereader) | ACR karanténba helyezési Adatolvasó |
| [AcrQuarantineWriter](#acrquarantinewriter) | az ACR karanténba helyezési adatírója |
| [API Management szolgáltatás közreműködői](#api-management-service-contributor) | Képes a szolgáltatás és az API-k kezelésére |
| [API Management Service operátori szerepkör](#api-management-service-operator-role) | Kezelheti a szolgáltatást, de nem az API-kat |
| [API Management szolgáltatás-olvasó szerepkör](#api-management-service-reader-role) | Csak olvasási hozzáférés a szolgáltatáshoz és az API-khoz |
| [Application Insights összetevő közreműködője](#application-insights-component-contributor) | Felügyelheti Application Insights összetevőket |
| [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Felhasználói jogosultságot biztosít a Application Insights Snapshot Debugger gyűjtött hibakeresési Pillanatképek megtekintéséhez és letöltéséhez. Vegye figyelembe, hogy ezek az engedélyek nem szerepelnek a [tulajdonos](#owner) vagy a [közreműködő](#contributor) szerepkörben. |
| [Automation-feladatok operátora](#automation-job-operator) | Feladatok létrehozása és kezelése Automation-Runbookok használatával. |
| [Automation-operátor](#automation-operator) | Az Automation-operátorok képesek a feladatok elindítására, leállítására, felfüggesztésére és folytatására |
| [Automation Runbook operátor](#automation-runbook-operator) | A Runbook tulajdonságainak olvasása – a Runbook feladatai létrehozásához. |
| [Avere közreműködője](#avere-contributor) | Létrehozhat és kezelhet egy avere vFXT-fürtöt. |
| [Avere operátor](#avere-operator) | A avere vFXT-fürt által használt fürt kezelése |
| [Azure Event Hubs adattulajdonos (előzetes verzió)](#azure-event-hubs-data-owner-preview) | Lehetővé teszi az Azure Event Hubs-erőforrások teljes hozzáférését. |
| [Azure Event Hubs adatfogadó (előzetes verzió)](#azure-event-hubs-data-receiver-preview) | Engedélyezi az Azure Event Hubs-erőforrások elérését. |
| [Azure Event Hubs adatfeladó (előzetes verzió)](#azure-event-hubs-data-sender-preview) | Engedélyezi az Azure Event Hubs-erőforrásokhoz való hozzáférés küldését. |
| [Azure Kubernetes Service-fürt rendszergazdai szerepköre](#azure-kubernetes-service-cluster-admin-role) | A fürt rendszergazdai hitelesítő adatainak listázása művelet. |
| [Azure Kubernetes Service-fürt felhasználói szerepköre](#azure-kubernetes-service-cluster-user-role) | Fürt felhasználói hitelesítő adatainak listázása. |
| [Azure Maps Adatolvasó (előzetes verzió)](#azure-maps-data-reader-preview) | Hozzáférést biztosít egy Azure Maps-fiókból az olvasási leképezéssel kapcsolatos adatokhoz. |
| [Azure Service Bus adattulajdonos (előzetes verzió)](#azure-service-bus-data-owner-preview) | Teljes hozzáférés engedélyezése Azure Service Bus erőforrásokhoz. |
| [Azure Service Bus adatfogadó (előzetes verzió)](#azure-service-bus-data-receiver-preview) | Engedélyezi a hozzáférést Azure Service Bus erőforrásokhoz. |
| [Azure Service Bus adatfeladó (előzetes verzió)](#azure-service-bus-data-sender-preview) | Lehetővé teszi a Azure Service Bus erőforrásokhoz való hozzáférés küldését. |
| [Azure Stack regisztrációs tulajdonos](#azure-stack-registration-owner) | Lehetővé teszi Azure Stack regisztrációk kezelését. |
| [Biztonsági mentési közreműködő](#backup-contributor) | Lehetővé teszi a Backup szolgáltatás kezelését, de nem hozhat létre tárolókat, és hozzáférést biztosíthat másoknak |
| [Biztonságimásolat-felelős](#backup-operator) | Lehetővé teszi a biztonsági mentési szolgáltatások kezelését, kivéve a biztonsági mentés eltávolítását, a tár létrehozását és a másokhoz való hozzáférést. |
| [Backup-olvasó](#backup-reader) | Megtekintheti a biztonsági mentési szolgáltatásokat, de nem végezheti el a módosításokat |
| [Számlázási olvasó](#billing-reader) | Olvasási hozzáférés engedélyezése a számlázási információkhoz |
| [BizTalk közreműködő](#biztalk-contributor) | Lehetővé teszi a BizTalk Services kezelését, de azokhoz való hozzáférés nélkül. |
| [Blockchain-hozzáférés (előzetes verzió)](#blockchain-member-node-access-preview) | Engedélyezi a hozzáférést a Blockchain-tagok csomópontjaihoz |
| [CDN-végpont közreműködői](#cdn-endpoint-contributor) | Kezelheti a CDN-végpontokat, de nem tud hozzáférést biztosítani más felhasználóknak. |
| [CDN-végpont olvasója](#cdn-endpoint-reader) | Megtekintheti a CDN-végpontokat, de nem végezheti el a módosításokat. |
| [CDN-profil közreműködői](#cdn-profile-contributor) | Kezelheti a CDN-profilokat és azok végpontjait, de nem adhat hozzáférést más felhasználóknak. |
| [CDN-profil olvasója](#cdn-profile-reader) | Megtekintheti a CDN-profilokat és azok végpontjait, de nem végezhet módosításokat. |
| [Klasszikus hálózati közreműködő](#classic-network-contributor) | Lehetővé teszi a klasszikus hálózatok kezelését, de azokhoz való hozzáférés nélkül. |
| [Klasszikus Storage-fiók közreműködői](#classic-storage-account-contributor) | Lehetővé teszi a klasszikus Storage-fiókok kezelését, de azokhoz való hozzáférés nélkül. |
| [A klasszikus Storage-fiók kulcs-kezelő szolgáltatásának szerepköre](#classic-storage-account-key-operator-service-role) | A klasszikus Storage-fiók kulcsfontosságú operátorai jogosultak a kulcsok listázására és újragenerálása a klasszikus Storage-fiókokban |
| [Klasszikus virtuális gép közreműködője](#classic-virtual-machine-contributor) | Lehetővé teszi a klasszikus virtuális gépek kezelését, de nem fér hozzájuk, nem pedig a virtuális hálózati vagy a Storage-fiókhoz, amelyhez csatlakoznak. |
| [Cognitive Services közreműködő](#cognitive-services-contributor) | Lehetővé teszi Cognitive Services kulcsainak létrehozását, olvasását, frissítését, törlését és kezelését. |
| [Cognitive Services Adatolvasó (előzetes verzió)](#cognitive-services-data-reader-preview) | Lehetővé teszi Cognitive Services-információk olvasását. |
| [Cognitive Services felhasználó](#cognitive-services-user) | Lehetővé teszi Cognitive Services kulcsainak olvasását és listázását. |
| [Cosmos DB fiók-olvasó szerepkör](#cosmos-db-account-reader-role) | Azure Cosmos DB fiókadatok olvasása. Lásd: [DocumentDB Account közreműködő](#documentdb-account-contributor) a Azure Cosmos db-fiókok kezeléséhez. |
| [Cosmos DB operátor](#cosmos-db-operator) | Lehetővé teszi Azure Cosmos DB-fiókok kezelését, de nem férnek hozzájuk. Megakadályozza a fiók kulcsai és a kapcsolati karakterláncok elérését. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Visszaküldheti a visszaállítási kérelmet egy Cosmos DB adatbázishoz vagy egy fiókhoz tartozó tárolóhoz |
| [Cost Management közreműködő](#cost-management-contributor) | Megtekintheti a költségeket és kezelheti a költségek konfigurációját (pl. költségvetés, exportálás) |
| [Cost Management olvasó](#cost-management-reader) | Megtekintheti a költségeket és a konfigurációt (például a költségvetést, az exportot) |
| [Data Box közreműködő](#data-box-contributor) | A lehetővé teszi, hogy minden Data Box szolgáltatás alatt kezelje a hozzáférést, kivéve, ha mások számára biztosít hozzáférést. |
| [Data Box olvasó](#data-box-reader) | Lehetővé teszi Data Box szolgáltatás felügyeletét, kivéve a rendelés vagy a szerkesztési sorrend részleteit, és hozzáférést biztosít másoknak. |
| [Data Factory közreműködő](#data-factory-contributor) | Az adatüzemek létrehozása és kezelése, valamint a bennük található alárendelt erőforrások. |
| [Data Lake Analytics fejlesztő](#data-lake-analytics-developer) | Lehetővé teszi a saját feladatok küldését, figyelését és kezelését, de Data Lake Analytics fiókokat nem hozhat létre vagy törölhet. |
| [Adattisztító](#data-purger) | Törölheti az elemzési adatfájlokat |
| [DevTest Labs User](#devtest-labs-user) | Lehetővé teszi a virtuális gépek a Azure DevTest Labs való összekapcsolását, indítását, újraindítását és leállítását. |
| [DNS-zóna közreműködője](#dns-zone-contributor) | Lehetővé teszi a DNS-zónák és-rekordhalmazok kezelését Azure DNSban, de nem teszi lehetővé, hogy ki férhet hozzájuk. |
| [DocumentDB-fiók közreműködői](#documentdb-account-contributor) | Felügyelheti Azure Cosmos DB fiókokat. Azure Cosmos DB korábbi nevén DocumentDB. |
| [EventGrid EventSubscription közreműködője](#eventgrid-eventsubscription-contributor) | Lehetővé teszi a EventGrid esemény-előfizetési műveletek kezelését. |
| [EventGrid EventSubscription-olvasó](#eventgrid-eventsubscription-reader) | Lehetővé teszi a EventGrid esemény-előfizetések olvasását. |
| [HDInsight-fürt operátora](#hdinsight-cluster-operator) | Lehetővé teszi a HDInsight-fürt konfigurációjának olvasását és módosítását. |
| [HDInsight tartományi szolgáltatások közreműködője](#hdinsight-domain-services-contributor) | Elolvashatja, létrehozhatja, módosíthatja és törölheti a HDInsight kapcsolatos tartományi szolgáltatásokat Enterprise Security Package |
| [Intelligens rendszer-fiók közreműködői](#intelligent-systems-account-contributor) | Lehetővé teszi az intelligens rendszerfiókok kezelését, az azokhoz való hozzáférés nélkül. |
| [Key Vault közreműködő](#key-vault-contributor) | Lehetővé teszi a kulcstartók kezelését, de azokhoz való hozzáférés nélkül. |
| [Lab Creator](#lab-creator) | Lehetővé teszi a felügyelt Labs Azure Lab-fiókokban való létrehozását, kezelését és törlését. |
| [Log Analytics közreműködő](#log-analytics-contributor) | Log Analytics közreműködő képes olvasni az összes figyelési és szerkesztési beállítást. A figyelési beállítások szerkesztése magában foglalja a virtuálisgép-bővítmény virtuális gépekhez való hozzáadását. a Storage-fiók kulcsainak beolvasása az Azure Storage-ból származó naplók gyűjtésének konfigurálásához; Automation-fiókok létrehozása és konfigurálása; megoldások hozzáadása; és az Azure Diagnostics konfigurálása az összes Azure-erőforráshoz. |
| [Log Analytics olvasó](#log-analytics-reader) | Log Analytics olvasó megtekintheti és megkeresheti az összes figyelési adatait, valamint megtekintheti a figyelési beállításokat, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson. |
| [Logikai alkalmazás közreműködői](#logic-app-contributor) | Lehetővé teszi a logikai alkalmazások kezelését, de nem változtatja meg a hozzájuk való hozzáférést. |
| [Logic app-operátor](#logic-app-operator) | Lehetővé teszi a logikai alkalmazások olvasását, engedélyezését és letiltását, de nem szerkesztheti és nem frissíti őket. |
| [Felügyelt alkalmazás operátori szerepköre](#managed-application-operator-role) | Lehetővé teszi műveletek olvasását és végrehajtását a felügyelt alkalmazás erőforrásain |
| [Felügyelt alkalmazások olvasója](#managed-applications-reader) | Lehetővé teszi az erőforrások olvasását egy felügyelt alkalmazásban, valamint a JIT-hozzáférés kérését. |
| [Felügyelt identitás közreműködői](#managed-identity-contributor) | Felhasználóhoz rendelt identitás létrehozása, olvasása, frissítése és törlése |
| [Felügyelt identitás operátora](#managed-identity-operator) | Felhasználóhoz rendelt identitás olvasása és hozzárendelése |
| [Felügyeleti csoport közreműködője](#management-group-contributor) | Felügyeleti csoport közreműködői szerepköre |
| [Felügyeleti csoport olvasója](#management-group-reader) | Felügyeleti csoport olvasójának szerepköre |
| [Közreműködő figyelése](#monitoring-contributor) | Beolvashatja az összes figyelési és a figyelési beállításokat. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Figyelési metrikák közzétevője](#monitoring-metrics-publisher) | Lehetővé teszi az Azure-erőforrásokhoz való közzétételi mérőszámok közzétételét |
| [Figyelő olvasó](#monitoring-reader) | Az összes figyelési adat (mérőszámok, naplók stb.) olvasása. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Hálózati közreműködő](#network-contributor) | Lehetővé teszi a hálózatok kezelését, az azokhoz való hozzáférés nélkül. |
| [Új ereklye APM-fiók közreműködői](#new-relic-apm-account-contributor) | Lehetővé teszi New Relic Application Performance Management-fiókok és-alkalmazások kezelését, az azokhoz való hozzáférés nélkül. |
| [Olvasó és adathozzáférés](#reader-and-data-access) | Lehetővé teszi az összes megtekintését, de nem engedélyezheti a Storage-fiók vagy a tárolt erőforrás törlését vagy létrehozását. Az olvasási/írási hozzáférést is lehetővé teszi a Storage-fiókban található összes, a Storage-fiók kulcsaihoz való hozzáférés útján. |
| [Redis Cache közreműködő](#redis-cache-contributor) | Lehetővé teszi a Redis gyorsítótárak kezelését, de azokhoz való hozzáférés nélkül. |
| [Erőforrás-házirend közreműködője (előzetes verzió)](#resource-policy-contributor-preview) | Előnézet Visszatöltötte-felhasználóktól, az erőforrás-házirend létrehozásához/módosításához, támogatási jegy létrehozásához és az erőforrások/hierarchia olvasásához szükséges jogosultságokkal. |
| [Ütemező – feladattípusok közreműködői](#scheduler-job-collections-contributor) | Lehetővé teszi a Scheduler-feladatok összegyűjtését, de nem fér hozzájuk. |
| [Search Service közreműködő](#search-service-contributor) | Lehetővé teszi a keresési szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. |
| [Biztonsági rendszergazda](#security-admin) | Csak Security Centerban: Megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, szerkesztheti a biztonsági szabályzatokat, megtekintheti a riasztásokat és a javaslatokat, |
| [Security Manager (örökölt)](#security-manager-legacy) | Ez egy örökölt szerepkör. Használja helyette a biztonsági rendszergazdát |
| [Biztonsági olvasó](#security-reader) | Csak Security Centerban: Megtekintheti a javaslatokat és riasztásokat, megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, de nem végezhet módosításokat |
| [Site Recovery közreműködő](#site-recovery-contributor) | Lehetővé teszi Site Recovery szolgáltatás felügyeletét, kivéve a tár létrehozását és a szerepkör-hozzárendelést |
| [Site Recovery operátor](#site-recovery-operator) | Lehetővé teszi a feladatátvételt és a feladat-visszavételt, de nem hajt végre más Site Recovery felügyeleti műveleteket |
| [Site Recovery olvasó](#site-recovery-reader) | Lehetővé teszi, hogy megtekintse Site Recovery állapotát, de ne végezzen más felügyeleti műveleteket |
| [Térbeli horgonyok fiók közreműködője](#spatial-anchors-account-contributor) | Lehetővé teszi a térbeli horgonyok kezelését a fiókban, de nem törli őket |
| [Térbeli horgonyok fiókjának tulajdonosa](#spatial-anchors-account-owner) | Lehetővé teszi a fiókban lévő térbeli horgonyok kezelését, beleértve azok törlését is |
| [Térbeli horgonyok fiókjának olvasója](#spatial-anchors-account-reader) | Lehetővé teszi a fiókban található térbeli horgonyok tulajdonságainak megkeresését és beolvasását |
| [SQL-adatbázis közreműködői](#sql-db-contributor) | Lehetővé teszi az SQL-adatbázisok kezelését, de azokhoz való hozzáférés nélkül. Emellett nem kezelheti a biztonsággal kapcsolatos házirendeket vagy a szülő SQL-kiszolgálókat. |
| [SQL felügyelt példány közreműködője](#sql-managed-instance-contributor) | Lehetővé teszi az SQL felügyelt példányok és a szükséges hálózati konfiguráció kezelését, de mások számára nem biztosít hozzáférést. |
| [SQL Security Manager](#sql-security-manager) | Lehetővé teszi az SQL-kiszolgálók és-adatbázisok biztonsággal kapcsolatos házirendjeinek kezelését, az azokhoz való hozzáférés nélkül. |
| [SQL Server közreműködő](#sql-server-contributor) | Lehetővé teszi az SQL-kiszolgálók és-adatbázisok kezelését, de azokhoz való hozzáférés nélkül, és nem a biztonsággal kapcsolatos házirendjeiket. |
| [Tárfiók-közreműködő](#storage-account-contributor) | Engedélyezi a Storage-fiókok kezelését. Hozzáférést biztosít a fiók kulcsaként, amely a megosztott kulcsos hitelesítésen keresztüli hozzáféréshez használható. |
| [A Storage-fiók kulcs-kezelő szolgáltatásának szerepköre](#storage-account-key-operator-service-role) | Engedélyezi a Storage-fiók hozzáférési kulcsainak listázását és újragenerálását. |
| [Storage blob adatközreműködői](#storage-blob-data-contributor) | Azure Storage-tárolók és-Blobok olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Storage blob-adattulajdonos](#storage-blob-data-owner) | Teljes hozzáférést biztosít az Azure Storage blob-tárolók és-adatkészletekhez, beleértve a POSIX hozzáférés-vezérlés hozzárendelését. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Storage blob-Adatolvasó](#storage-blob-data-reader) | Azure Storage-tárolók és-Blobok olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Tárolási várólista adatközreműködői](#storage-queue-data-contributor) | Azure Storage-várólisták és üzenetsor-üzenetek olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Tárolási üzenetsor adatüzenet-processzora](#storage-queue-data-message-processor) | Üzenet betekintése, beolvasása és törlése egy Azure Storage-várólistából. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Tárolási várólista adatüzenetének küldője](#storage-queue-data-message-sender) | Üzenetek hozzáadása egy Azure Storage-várólistához. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Storage-várólista adatolvasója](#storage-queue-data-reader) | Azure Storage-várólisták és üzenetsor-üzenetek olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Támogatási kérelem közreműködői](#support-request-contributor) | Lehetővé teszi a támogatási kérések létrehozását és kezelését |
| [Traffic Manager közreműködő](#traffic-manager-contributor) | Lehetővé teszi a Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz való hozzáférés szabályozását. |
| [Felhasználói hozzáférés rendszergazdája](#user-access-administrator) | Lehetővé teszi az Azure-erőforrásokhoz való felhasználói hozzáférés kezelését. |
| [Virtuális gép rendszergazdai bejelentkezése](#virtual-machine-administrator-login) | Virtual Machines megtekintése a Portálon és Bejelentkezés rendszergazdaként |
| [Virtuális gépek közreműködője](#virtual-machine-contributor) | Lehetővé teszi a virtuális gépek kezelését, de nem fér hozzájuk, nem pedig a virtuális hálózati vagy a Storage-fiókhoz, amelyhez csatlakoznak. |
| [Virtuális gép felhasználói bejelentkezés](#virtual-machine-user-login) | Tekintse meg Virtual Machines a portálon, és jelentkezzen be normál felhasználóként. |
| [Webes csomag közreműködői](#web-plan-contributor) | Lehetővé teszi a webhelyek webes csomagjainak kezelését, de azokhoz való hozzáférés nélkül. |
| [Webhely közreműködői](#website-contributor) | Lehetővé teszi a webhelyek (nem webes csomagok) kezelését, de nem fér hozzájuk. |


## <a name="owner"></a>Tulajdonos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az összes funkció kezelését, beleértve az erőforrásokhoz való hozzáférést is. |
> | **Azonosító** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Műveletek** |  |
> | * | Minden típusú erőforrás létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="contributor"></a>Közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy mindent kezelje, kivéve az erőforrásokhoz való hozzáférést. |
> | **Azonosító** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Műveletek** |  |
> | * | Minden típusú erőforrás létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft.Authorization/*/Delete | Szerepkörök és szerepkör-hozzárendelések törlése |
> | Microsoft.Authorization/*/Write | Szerepkörök és szerepkör-hozzárendelések létrehozása |
> | Microsoft.Authorization/elevateAccess/Action | A hívó felhasználói hozzáférés rendszergazdai hozzáférésének engedélyezése a bérlői hatókörben |
> | Microsoft. Blueprint/blueprintAssignments/Write | Tervrajzok létrehozása vagy frissítése |
> | Microsoft. Blueprint/blueprintAssignments/delete | Bármilyen tervrajz-összetevő törlése |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="reader"></a>Olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy mindent megtekintse, de ne végezzen módosításokat. |
> | **Azonosító** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR törlése |
> | **Azonosító** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Összetevő törlése egy tároló-beállításjegyzékben. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR-rendszerkép aláírója |
> | **Azonosító** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | A tartalom megbízhatóságának leküldése/lekérése egy tároló-beállításjegyzékben. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR-lekérés |
> | **Azonosító** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Lemezképek lekérése vagy beolvasása egy tároló-beállításjegyzékből. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR leküldése |
> | **Azonosító** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Lemezképek lekérése vagy beolvasása egy tároló-beállításjegyzékből. |
> | Microsoft.ContainerRegistry/registries/push/write | Képek leküldése vagy írása egy tároló-beállításjegyzékbe. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR karanténba helyezési Adatolvasó |
> | **Azonosító** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Karanténba helyezett lemezképek lekérése vagy beolvasása a Container registryből |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | az ACR karanténba helyezési adatírója |
> | **Azonosító** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Karanténba helyezett lemezképek lekérése vagy beolvasása a Container registryből |
> | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Karanténba helyezett lemezképek karantén állapotának írása/módosítása |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="api-management-service-contributor"></a>API Management szolgáltatás közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Képes a szolgáltatás és az API-k kezelésére |
> | **Azonosító** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/* | API Management szolgáltatás létrehozása és kezelése |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="api-management-service-operator-role"></a>API Management Service operátori szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a szolgáltatást, de nem az API-kat |
> | **Azonosító** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/*/read | API Management szolgáltatási példányok beolvasása |
> | Microsoft.ApiManagement/service/backup/action | API Management szolgáltatás biztonsági mentése a megadott tárolóba egy felhasználó által megadott Storage-fiókban |
> | Microsoft.ApiManagement/service/delete | API Management szolgáltatás példányának törlése |
> | Microsoft.ApiManagement/service/managedeployments/action | SKU/egységek módosítása, API Management szolgáltatás regionális központi telepítésének hozzáadása/eltávolítása |
> | Microsoft.ApiManagement/service/read | API Management szolgáltatási példány metaadatainak olvasása |
> | Microsoft.ApiManagement/service/restore/action | API Management szolgáltatás visszaállítása a megadott tárolóból egy felhasználó által megadott Storage-fiókban |
> | Microsoft.ApiManagement/service/updatecertificate/action | API Management szolgáltatás SSL-tanúsítványának feltöltése |
> | Microsoft.ApiManagement/service/updatehostname/action | API Management szolgáltatás egyéni tartománynevének beállítása, frissítése vagy eltávolítása |
> | Microsoft.ApiManagement/service/write | API Management szolgáltatás új példányának létrehozása |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Felhasználóhoz tartozó kulcsok beolvasása |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="api-management-service-reader-role"></a>API Management szolgáltatás-olvasó szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Csak olvasási hozzáférés a szolgáltatáshoz és az API-khoz |
> | **Azonosító** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/*/read | API Management szolgáltatási példányok beolvasása |
> | Microsoft.ApiManagement/service/read | API Management szolgáltatási példány metaadatainak olvasása |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Felhasználóhoz tartozó kulcsok beolvasása |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="application-insights-component-contributor"></a>Application Insights összetevő közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felügyelheti Application Insights összetevőket |
> | **Azonosító** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.Insights/components/* | Az adatáttekintési összetevők létrehozása és kezelése |
> | Microsoft.Insights/webtests/* | Webes tesztek létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felhasználói jogosultságot biztosít a Application Insights Snapshot Debugger gyűjtött hibakeresési Pillanatképek megtekintéséhez és letöltéséhez. Vegye figyelembe, hogy ezek az engedélyek nem szerepelnek a [tulajdonos](#owner) vagy a [közreműködő](#contributor) szerepkörben. |
> | **Azonosító** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="automation-job-operator"></a>Automation-feladatok operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Feladatok létrehozása és kezelése Automation-Runbookok használatával. |
> | **Azonosító** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hibrid Runbook Worker-erőforrások beolvasása |
> | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation feladatok beolvasása |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Egy Azure Automation feladatot folytat |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Leállítja egy Azure Automation feladatot |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Beolvas egy Azure Automation-feladatok streamjét |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation feladatok felfüggesztése |
> | Microsoft.Automation/automationAccounts/jobs/write | Létrehoz egy Azure Automation feladatot |
> | Microsoft.Automation/automationAccounts/jobs/output/read | A feladatok kimenetének beolvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="automation-operator"></a>Automation-operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az Automation-operátorok képesek a feladatok elindítására, leállítására, felfüggesztésére és folytatására |
> | **Azonosító** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hibrid Runbook Worker-erőforrások beolvasása |
> | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation feladatok beolvasása |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Egy Azure Automation feladatot folytat |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Leállítja egy Azure Automation feladatot |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Beolvas egy Azure Automation-feladatok streamjét |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation feladatok felfüggesztése |
> | Microsoft.Automation/automationAccounts/jobs/write | Létrehoz egy Azure Automation feladatot |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Azure Automation feladatütemezés beolvasása |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Létrehoz egy Azure Automation feladatok ütemtervét |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Az Automation-fiókhoz kapcsolódó munkaterület beolvasása |
> | Microsoft.Automation/automationAccounts/read | Azure Automation fiók beolvasása |
> | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation runbook beolvasása |
> | Microsoft.Automation/automationAccounts/schedules/read | Azure Automation ütemezett eszköz beolvasása |
> | Microsoft.Automation/automationAccounts/schedules/write | Egy Azure Automation Schedule-eszköz létrehozása vagy frissítése |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Automation/automationAccounts/jobs/output/read | A feladatok kimenetének beolvasása |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="automation-runbook-operator"></a>Automation Runbook operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A Runbook tulajdonságainak olvasása – a Runbook feladatai létrehozásához. |
> | **Azonosító** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation runbook beolvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="avere-contributor"></a>Avere közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Létrehozhat és kezelhet egy avere vFXT-fürtöt. |
> | **Azonosító** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft. Network/*/READ |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.Network/virtualNetworks/subnets/read | Virtuális hálózati alhálózat definíciójának beolvasása |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | Microsoft.Network/networkSecurityGroups/join/action | Csatlakozik egy hálózati biztonsági csoporthoz. Nem Riasztásos. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/Resources/READ | Az erőforráscsoport erőforrásainak beolvasása. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Egy blob törlésének eredményét adja vissza. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blobot vagy Blobok listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Egy blob írásának eredményét adja vissza. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="avere-operator"></a>Avere operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A avere vFXT-fürt által használt fürt kezelése |
> | **Azonosító** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Műveletek** |  |
> | Microsoft.Compute/virtualMachines/read | Virtuális gép tulajdonságainak beolvasása |
> | Microsoft.Network/networkInterfaces/read | Hálózati adapter definíciójának beolvasása.  |
> | Microsoft.Network/networkInterfaces/write | Hálózati adaptert hoz létre, vagy frissít egy meglévő hálózati adaptert.  |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.Network/virtualNetworks/subnets/read | Virtuális hálózati alhálózat definíciójának beolvasása |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Microsoft.Network/networkSecurityGroups/join/action | Csatlakozik egy hálózati biztonsági csoporthoz. Nem Riasztásos. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Egy tároló törlésének eredményét adja vissza. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Tárolók listájának visszaadása |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | A blob-tároló Put eredményét adja vissza. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Egy blob törlésének eredményét adja vissza. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blobot vagy Blobok listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Egy blob írásának eredményét adja vissza. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="azure-event-hubs-data-owner-preview"></a>Azure Event Hubs adattulajdonos (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az Azure Event Hubs-erőforrások teljes hozzáférését. |
> | **Azonosító** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Műveletek** |  |
> | Microsoft.EventHub/* |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="azure-event-hubs-data-receiver-preview"></a>Azure Event Hubs adatfogadó (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyezi az Azure Event Hubs-erőforrások elérését. |
> | **Azonosító** | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | **Műveletek** |  |
> | Microsoft. EventHub/*/eventhubs/consumergroups/READ |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="azure-event-hubs-data-sender-preview"></a>Azure Event Hubs adatfeladó (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyezi az Azure Event Hubs-erőforrásokhoz való hozzáférés küldését. |
> | **Azonosító** | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | **Műveletek** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/send/action |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service-fürt rendszergazdai szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A fürt rendszergazdai hitelesítő adatainak listázása művelet. |
> | **Azonosító** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Műveletek** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Felügyelt fürt clusterAdmin hitelesítő adatainak listázása |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes Service-fürt felhasználói szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Fürt felhasználói hitelesítő adatainak listázása. |
> | **Azonosító** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Műveletek** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Felügyelt fürt clusterUser hitelesítő adatainak listázása |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="azure-maps-data-reader-preview"></a>Azure Maps Adatolvasó (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Hozzáférést biztosít egy Azure Maps-fiókból az olvasási leképezéssel kapcsolatos adatokhoz. |
> | **Azonosító** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Műveletek** |  |
> | *none* |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft. Maps/fiókok/adatgyűjtés/olvasás | Olvasási hozzáférést biztosít a Maps-fiókhoz. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="azure-service-bus-data-owner-preview"></a>Azure Service Bus adattulajdonos (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Teljes hozzáférés engedélyezése Azure Service Bus erőforrásokhoz. |
> | **Azonosító** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Műveletek** |  |
> | Microsoft.ServiceBus/* |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="azure-service-bus-data-receiver-preview"></a>Azure Service Bus adatfogadó (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyezi a hozzáférést Azure Service Bus erőforrásokhoz. |
> | **Azonosító** | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | **Műveletek** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/receive/action |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="azure-service-bus-data-sender-preview"></a>Azure Service Bus adatfeladó (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Azure Service Bus erőforrásokhoz való hozzáférés küldését. |
> | **Azonosító** | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | **Műveletek** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/send/action |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="azure-stack-registration-owner"></a>Azure Stack regisztrációs tulajdonos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Azure Stack regisztrációk kezelését. |
> | **Azonosító** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Műveletek** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Azure Stack Marketplace-termék részletes adatainak beolvasása |
> | Microsoft.AzureStack/registrations/products/read | Egy Azure Stack Marketplace-termék tulajdonságainak beolvasása |
> | Microsoft.AzureStack/registrations/read | Egy Azure Stack regisztráció tulajdonságainak beolvasása |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="backup-contributor"></a>Biztonsági mentési közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Backup szolgáltatás kezelését, de nem hozhat létre tárolókat, és hozzáférést biztosíthat másoknak |
> | **Azonosító** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | A művelet eredményeinek kezelése a biztonsági mentési felügyeletben |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Biztonsági mentési tárolók létrehozása és kezelése Recovery Services-tár biztonsági mentési hálójában |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Frissíti a tárolók listáját |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | A biztonsági mentési felügyelettel kapcsolatos Meta-adat létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | A biztonságimásolat-kezelési műveletek eredményeinek létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Biztonsági mentési szabályzatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Olyan elemek létrehozása és kezelése, amelyekről biztonsági másolatot lehet készíteni |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Biztonsági másolatba mentett elemek létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Tartalék elemeket tároló tárolók létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | A védett elemek és a védett kiszolgálók összefoglalásait adja vissza egy Recovery Services számára. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Biztonsági mentéshez kapcsolódó tanúsítványok létrehozása és kezelése Recovery Services-tárolóban |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | A tár szolgáltatással kapcsolatos bővített adatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a Recovery Services-tároló riasztásait. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Regisztrált identitások létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/usages/* | Recovery Services-tároló használatának létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Storage/storageAccounts/read | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | A művelet érvényesítése védett elemen |
> | Microsoft.RecoveryServices/Vaults/write | A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupEngines/READ | A tárolóban regisztrált biztonsági másolatokat kezelő kiszolgálókat adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Az összes védhető tároló beolvasása |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Recovery Services-tárolók biztonsági mentési állapotának keresése |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Funkciók ellenőrzése |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | A riasztás feloldása. |
> | Microsoft. Recoveryservices szolgáltatónál/Operations/READ | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Egy adott művelet műveleti állapotának beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Az összes biztonsági mentési védelmi leképezés listázása |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="backup-operator"></a>Biztonságimásolat-felelős
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a biztonsági mentési szolgáltatások kezelését, kivéve a biztonsági mentés eltávolítását, a tár létrehozását és a másokhoz való hozzáférést. |
> | **Azonosító** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotának visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Beolvassa a védelmi tárolón végrehajtott művelet eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Biztonsági másolat készítése egy védett elemről. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Beolvassa a védett elemeken végrehajtott művelet eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett tétel objektumának részleteit adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Azonnali elemek helyreállításának kiépítése védett elemek esetén |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Védett elemek helyreállítási pontjainak beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Védett elemek helyreállítási pontjainak visszaállítása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | A védett elemek azonnali elemek helyreállításának visszavonása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Védett elemek biztonsági másolatának létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Az összes regisztrált tároló visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Frissíti a tárolók listáját |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | A biztonságimásolat-kezelési műveletek eredményeinek létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A szabályzatművelet eredményeinek beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Az összes védelmi szabályzat visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Olyan elemek létrehozása és kezelése, amelyekről biztonsági másolatot lehet készíteni |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | A védett elemek listájának beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tárolót adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | A védett elemek és a védett kiszolgálók összefoglalásait adja vissza egy Recovery Services számára. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tároló hitelesítő adatait tartalmazó tanúsítványt. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a Recovery Services-tároló riasztásait. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A szolgáltatás-tároló regisztrálása művelettel regisztrálhat egy tárolót a helyreállítási szolgáltatással. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Storage/storageAccounts/read | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | A művelet érvényesítése védett elemen |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Házirend-művelet állapotának beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Létrehoz egy regisztrált tárolót |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | A tárolóban lévő számítási feladatok lekérdezése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupEngines/READ | A tárolóban regisztrált biztonsági másolatokat kezelő kiszolgálókat adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Biztonsági másolat védelmi leképezésének létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Biztonsági másolat védelmi szándékának beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Az összes védhető tároló beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Tároló összes elemének beolvasása |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Recovery Services-tárolók biztonsági mentési állapotának keresése |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Funkciók ellenőrzése |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | A riasztás feloldása. |
> | Microsoft. Recoveryservices szolgáltatónál/Operations/READ | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Egy adott művelet műveleti állapotának beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Az összes biztonsági mentési védelmi leképezés listázása |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="backup-reader"></a>Backup-olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a biztonsági mentési szolgáltatásokat, de nem végezheti el a módosításokat |
> | **Azonosító** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotának visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Beolvassa a védelmi tárolón végrehajtott művelet eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Beolvassa a védett elemeken végrehajtott művelet eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett tétel objektumának részleteit adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Védett elemek helyreállítási pontjainak beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Az összes regisztrált tároló visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | A feladatművelet eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Az összes feladatütemezés visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | A Recovery Services-tárolóval kapcsolatos biztonsági mentés eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A szabályzatművelet eredményeinek beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Az összes védelmi szabályzat visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | A védett elemek listájának beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tárolót adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | A védett elemek és a védett kiszolgálók összefoglalásait adja vissza egy Recovery Services számára. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a Recovery Services-tároló riasztásait. |
> | Microsoft.RecoveryServices/Vaults/read | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Recovery Services tároló tárolási konfigurációját adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Recovery Services-tároló konfigurációjának visszaadása. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Házirend-művelet állapotának beolvasása. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupEngines/READ | A tárolóban regisztrált biztonsági másolatokat kezelő kiszolgálókat adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Biztonsági másolat védelmi szándékának beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Tároló összes elemének beolvasása |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Recovery Services-tárolók biztonsági mentési állapotának keresése |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | A riasztás feloldása. |
> | Microsoft. Recoveryservices szolgáltatónál/Operations/READ | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Egy adott művelet műveleti állapotának beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Az összes biztonsági mentési védelmi leképezés listázása |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="billing-reader"></a>Számlázás olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvasási hozzáférés engedélyezése a számlázási információkhoz |
> | **Azonosító** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Billing/*/read | Számlázási adatok olvasása |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="biztalk-contributor"></a>BizTalk közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a BizTalk Services kezelését, de azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.BizTalkServices/BizTalk/* | BizTalk Services-szolgáltatások létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="blockchain-member-node-access-preview"></a>Blockchain-hozzáférés (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyezi a hozzáférést a Blockchain-tagok csomópontjaihoz |
> | **Azonosító** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Műveletek** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Lekérdezi vagy felsorolja a meglévő Blockchain-tag tranzakciós csomópont (oka) t. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Csatlakozás egy Blockchain-tag tranzakciós csomóponthoz. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cdn-endpoint-contributor"></a>CDN-végpont közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a CDN-végpontokat, de nem tud hozzáférést biztosítani más felhasználóknak. |
> | **Azonosító** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cdn-endpoint-reader"></a>CDN-végpont olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a CDN-végpontokat, de nem végezheti el a módosításokat. |
> | **Azonosító** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cdn-profile-contributor"></a>CDN-profil közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a CDN-profilokat és azok végpontjait, de nem adhat hozzáférést más felhasználóknak. |
> | **Azonosító** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cdn-profile-reader"></a>CDN-profil olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a CDN-profilokat és azok végpontjait, de nem végezhet módosításokat. |
> | **Azonosító** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="classic-network-contributor"></a>Klasszikus hálózati közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a klasszikus hálózatok kezelését, de azokhoz való hozzáférés nélkül. |
> | **Azonosító** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft.ClassicNetwork/* | Klasszikus hálózatok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="classic-storage-account-contributor"></a>Klasszikus Storage-fiók közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a klasszikus Storage-fiókok kezelését, de azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft.ClassicStorage/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>A klasszikus Storage-fiók kulcs-kezelő szolgáltatásának szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A klasszikus Storage-fiók kulcsfontosságú operátorai jogosultak a kulcsok listázására és újragenerálása a klasszikus Storage-fiókokban |
> | **Azonosító** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Műveletek** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Újragenerálja a Storage-fiók meglévő hozzáférési kulcsait. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="classic-virtual-machine-contributor"></a>Klasszikus virtuális gép közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a klasszikus virtuális gépek kezelését, de nem fér hozzájuk, nem pedig a virtuális hálózati vagy a Storage-fiókhoz, amelyhez csatlakoznak. |
> | **Azonosító** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft.ClassicCompute/domainNames/* | Klasszikus számítási tartománynevek létrehozása és kezelése |
> | Microsoft.ClassicCompute/virtualMachines/* | Virtuális gépek létrehozása és kezelése |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Fenntartott IP-cím csatolása |
> | Microsoft.ClassicNetwork/reservedIps/read | A fenntartott IP-címek beolvasása |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Csatlakozik a virtuális hálózathoz. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Szerezze be a virtuális hálózatot. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | A Storage-fiók lemezét adja vissza. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | A Storage-fiók rendszerképét adja vissza. Elavult. A "Microsoft. ClassicStorage/storageAccounts/lemezképet" használata |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | Microsoft.ClassicStorage/storageAccounts/read | Adja vissza a Storage-fiókot az adott fiókkal. |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cognitive-services-contributor"></a>Cognitive Services közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Cognitive Services kulcsainak létrehozását, olvasását, frissítését, törlését és kezelését. |
> | **Azonosító** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Egy előfizetés funkcióinak beolvasása. |
> | Microsoft. features/Providers/features/READ | Egy adott erőforrás-szolgáltatóhoz tartozó előfizetés funkciójának beolvasása. |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a Analysis Server diagnosztikai beállításait. |
> | Microsoft.Insights/logDefinitions/read | Napló-definíciók olvasása |
> | Microsoft. bepillantások/metricdefinitions/olvasás | Metrikus definíciók olvasása |
> | Microsoft. bepillantások/mérőszámok/olvasás | Metrikák olvasása |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/deployments/operations/read | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek beolvasása. |
> | Microsoft. Resources/előfizetések/olvasás | Az előfizetések listájának beolvasása. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cognitive-services-data-reader-preview"></a>Cognitive Services Adatolvasó (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Cognitive Services-információk olvasását. |
> | **Azonosító** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Műveletek** |  |
> | *none* |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cognitive-services-user"></a>Cognitive Services felhasználó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Cognitive Services kulcsainak olvasását és listázását. |
> | **Azonosító** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Műveletek** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Kulcsok listázása |
> | Microsoft.Insights/alertRules/read | Klasszikus metrikai riasztás beolvasása |
> | Microsoft. bepillantások/diagnosticSettings/olvasás | Erőforrás diagnosztikai beállításának beolvasása |
> | Microsoft.Insights/logDefinitions/read | Napló-definíciók olvasása |
> | Microsoft. bepillantások/metricdefinitions/olvasás | Metrikus definíciók olvasása |
> | Microsoft. bepillantások/mérőszámok/olvasás | Metrikák olvasása |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/operations/read | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek beolvasása. |
> | Microsoft. Resources/előfizetések/olvasás | Az előfizetések listájának beolvasása. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB fiók-olvasó szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Cosmos DB fiókadatok olvasása. Lásd: [DocumentDB Account közreműködő](#documentdb-account-contributor) a Azure Cosmos db-fiókok kezeléséhez. |
> | **Azonosító** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések beolvasása, az egyes felhasználók számára megadott engedélyek olvasása |
> | Microsoft.DocumentDB/*/read | Bármely gyűjtemény beolvasása |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Az adatbázis-fiók írásvédett kulcsának beolvasása. |
> | Microsoft.Insights/MetricDefinitions/read | Metrikus definíciók olvasása |
> | Microsoft.Insights/Metrics/read | Metrikák olvasása |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cosmos-db-operator"></a>Cosmos DB operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Azure Cosmos DB-fiókok kezelését, de nem férnek hozzájuk. Megakadályozza a fiók kulcsai és a kapcsolati karakterláncok elérését. |
> | **Azonosító** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Műveletek** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
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
> | **Leírás** | Visszaküldheti a visszaállítási kérelmet egy Cosmos DB adatbázishoz vagy egy fiókhoz tartozó tárolóhoz |
> | **Azonosító** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Műveletek** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | A biztonsági mentés konfigurálására vonatkozó kérelem küldése |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Visszaállítási kérelem elküldése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cost-management-contributor"></a>Cost Management közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a költségeket és kezelheti a költségek konfigurációját (pl. költségvetés, exportálás) |
> | **Azonosító** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Műveletek** |  |
> | Microsoft. fogyasztás/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft. számlázás/billingPeriods/olvasás | Az elérhető számlázási időszakok felsorolása |
> | Microsoft. Resources/előfizetések/olvasás | Az előfizetések listájának beolvasása. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Advisor/configurations/read | Konfigurációk beolvasása |
> | Microsoft. Advisor/javaslatok/olvasás | Olvasási javaslatok |
> | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="cost-management-reader"></a>Cost Management olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a költségeket és a konfigurációt (például a költségvetést, az exportot) |
> | **Azonosító** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Műveletek** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft. számlázás/billingPeriods/olvasás | Az elérhető számlázási időszakok felsorolása |
> | Microsoft. Resources/előfizetések/olvasás | Az előfizetések listájának beolvasása. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Advisor/configurations/read | Konfigurációk beolvasása |
> | Microsoft. Advisor/javaslatok/olvasás | Olvasási javaslatok |
> | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="data-box-contributor"></a>Data Box közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A lehetővé teszi, hogy minden Data Box szolgáltatás alatt kezelje a hozzáférést, kivéve, ha mások számára biztosít hozzáférést. |
> | **Azonosító** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Databox/* |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="data-box-reader"></a>Data Box olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Data Box szolgáltatás felügyeletét, kivéve a rendelés vagy a szerkesztési sorrend részleteit, és hozzáférést biztosít másoknak. |
> | **Azonosító** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Felsorolja a rendeléshez kapcsolódó titkosítatlan hitelesítő adatokat. |
> | Microsoft.Databox/locations/availableSkus/action | Ez a metódus visszaadja az elérhető SKU-ket tartalmazó listát. |
> | Microsoft.Databox/locations/validateAddress/action | Ellenőrzi a szállítási címet, és alternatív címeket biztosít, ha vannak ilyenek. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="data-factory-contributor"></a>Data Factory közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az adatüzemek létrehozása és kezelése, valamint a bennük található alárendelt erőforrások. |
> | **Azonosító** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.DataFactory/dataFactories/* | Létrehozhatja és kezelheti az adatelőállítókat és a bennük található alárendelt erőforrásokat. |
> | Microsoft. DataFactory/gyárak/* | Létrehozhatja és kezelheti az adatelőállítókat és a bennük található alárendelt erőforrásokat. |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics fejlesztő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a saját feladatok küldését, figyelését és kezelését, de Data Lake Analytics fiókokat nem hozhat létre vagy törölhet. |
> | **Azonosító** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft. BigAnalytics/accounts/TakeOwnership/Action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | DataLakeAnalytics-fiók törlése. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Engedélyek megadása a más felhasználók által küldött feladatok megszakításához. |
> | Microsoft.DataLakeAnalytics/accounts/Write | DataLakeAnalytics-fiók létrehozása vagy frissítése. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Hozzon létre vagy frissítsen egy DataLakeAnalytics-fiók csatolt Data Lake Store-fiókját. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Data Lake Store-fiók DataLakeAnalytics-fiókból való leválasztása. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Hozzon létre vagy frissítsen egy DataLakeAnalytics-fiók csatolt Storage-fiókját. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Egy DataLakeAnalytics-fiókhoz tartozó Storage-fiók leválasztása. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Tűzfalszabály létrehozása vagy frissítése. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Tűzfalszabály törlése. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Számítási szabályzat létrehozása vagy frissítése. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Számítási szabályzat törlése. |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="data-purger"></a>Adattisztító
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Törölheti az elemzési adatfájlokat |
> | **Azonosító** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Műveletek** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft. bepillantások/összetevők/kiürítés/művelet | Adatok törlése a Application Insightsból |
> | Microsoft. OperationalInsights/munkaterületek/*/READ |  |
> | Microsoft. OperationalInsights/munkaterületek/kiürítés/művelet | Megadott adatok törlése a munkaterületről |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="devtest-labs-user"></a>DevTest Labs User
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a virtuális gépek a Azure DevTest Labs való összekapcsolását, indítását, újraindítását és leállítását. |
> | **Azonosító** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Compute/availabilitySets/read | Rendelkezésre állási csoport tulajdonságainak beolvasása |
> | Microsoft.Compute/virtualMachines/*/read | Virtuális gép tulajdonságainak beolvasása (virtuálisgép-méretek, futásidejű állapot, virtuálisgép-bővítmények stb.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Kikapcsolja a virtuális gépet, és felszabadítja a számítási erőforrásokat |
> | Microsoft.Compute/virtualMachines/read | Virtuális gép tulajdonságainak beolvasása |
> | Microsoft.Compute/virtualMachines/restart/action | Újraindítja a virtuális gépet |
> | Microsoft.Compute/virtualMachines/start/action | Elindítja a virtuális gépet |
> | Microsoft.DevTestLab/*/read | Tesztkörnyezet tulajdonságainak olvasása |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Véletlenszerű, igényelhető virtuális gép igénylése a laborban. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Virtuális gépek létrehozása tesztkörnyezetben. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Ellenőrizze, hogy az aktuális felhasználó rendelkezik-e érvényes profillal a laborban. |
> | Microsoft.DevTestLab/labs/formulas/delete | Képletek törlése. |
> | Microsoft.DevTestLab/labs/formulas/read | Képletek olvasása. |
> | Microsoft.DevTestLab/labs/formulas/write | Képletek hozzáadása vagy módosítása. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Kiértékeli a tesztkörnyezet házirendjét. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Meglévő virtuális gép tulajdonjogának átvétele |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Felsorolja a megfelelő indítási/leállítási ütemterveket, ha vannak ilyenek. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Egy olyan karakterlánc beolvasása, amely a virtuális gép RDP-fájljának tartalmát jelöli |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | A terheléselosztó háttérbeli címkészlet csatlakoztatása. Nem Riasztásos. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | A terheléselosztó bejövő NAT-szabályához csatlakozik. Nem Riasztásos. |
> | Microsoft.Network/networkInterfaces/*/read | Olvassa el egy hálózati adapter tulajdonságait (például az összes olyan terheléselosztó, amely a hálózati adapter részét képezi) |
> | Microsoft.Network/networkInterfaces/join/action | Csatlakoztat egy virtuális gépet egy hálózati adapterhez. Nem Riasztásos. |
> | Microsoft.Network/networkInterfaces/read | Hálózati adapter definíciójának beolvasása.  |
> | Microsoft.Network/networkInterfaces/write | Hálózati adaptert hoz létre, vagy frissít egy meglévő hálózati adaptert.  |
> | Microsoft.Network/publicIPAddresses/*/read | Nyilvános IP-cím tulajdonságainak beolvasása |
> | Microsoft.Network/publicIPAddresses/join/action | Csatlakozik egy nyilvános IP-címhez. Nem Riasztásos. |
> | Microsoft.Network/publicIPAddresses/read | Egy nyilvános IP-cím definíciójának beolvasása. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Microsoft.Resources/deployments/operations/read | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | Microsoft.Resources/deployments/read | Lekérdezi vagy felsorolja az üzemelő példányokat. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | **Nincs tapintat** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | A virtuális gép által frissíthető elérhető méretek listája |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="dns-zone-contributor"></a>DNS-zóna közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a DNS-zónák és-rekordhalmazok kezelését Azure DNSban, de nem teszi lehetővé, hogy ki férhet hozzájuk. |
> | **Azonosító** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.Network/dnsZones/* | DNS-zónák és-rekordok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="documentdb-account-contributor"></a>DocumentDB-fiók közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felügyelheti Azure Cosmos DB fiókokat. Azure Cosmos DB korábbi nevén DocumentDB. |
> | **Azonosító** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB fiókok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a EventGrid esemény-előfizetési műveletek kezelését. |
> | **Azonosító** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Globális esemény-előfizetések listázása a témakör típusa szerint |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Regionális esemény-előfizetések listázása |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Regionális esemény-előfizetések listázása topictype szerint |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription-olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a EventGrid esemény-előfizetések olvasását. |
> | **Azonosító** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.EventGrid/eventSubscriptions/read | EventSubscription beolvasása |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Globális esemény-előfizetések listázása a témakör típusa szerint |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Regionális esemény-előfizetések listázása |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Regionális esemény-előfizetések listázása topictype szerint |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="hdinsight-cluster-operator"></a>HDInsight-fürt operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a HDInsight-fürt konfigurációjának olvasását és módosítását. |
> | **Azonosító** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **Műveletek** |  |
> | Microsoft. HDInsight/*/READ |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | Átjáró beállításainak beolvasása a HDInsight-fürthöz |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | HDInsight-fürthöz tartozó átjáró beállításainak frissítése |
> | Microsoft. HDInsight/fürtök/konfigurációk/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Resources/deployments/operations/read | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="hdinsight-domain-services-contributor"></a>HDInsight tartományi szolgáltatások közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Elolvashatja, létrehozhatja, módosíthatja és törölheti a HDInsight kapcsolatos tartományi szolgáltatásokat Enterprise Security Package |
> | **Azonosító** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Műveletek** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="intelligent-systems-account-contributor"></a>Intelligens rendszer-fiók közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az intelligens rendszerfiókok kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.IntelligentSystems/accounts/* | Intelligens rendszerek fiókjainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="key-vault-contributor"></a>Key Vault közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a kulcstartók kezelését, de azokhoz való hozzáférés nélkül. |
> | **Azonosító** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Helyreállítható törölhető kulcstartó törlése |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="lab-creator"></a>Tesztkörnyezet létrehozója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a felügyelt Labs Azure Lab-fiókokban való létrehozását, kezelését és törlését. |
> | **Azonosító** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. LabServices/labAccounts/*/READ |  |
> | Microsoft. LabServices/labAccounts/createLab/művelet | Tesztkörnyezet létrehozása labor-fiókban. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Regionális elérhetőségi információk beolvasása a labor-fiókban konfigurált egyes méretek kategóriához |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="log-analytics-contributor"></a>Log Analytics közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Log Analytics közreműködő képes olvasni az összes figyelési és szerkesztési beállítást. A figyelési beállítások szerkesztése magában foglalja a virtuálisgép-bővítmény virtuális gépekhez való hozzáadását. a Storage-fiók kulcsainak beolvasása az Azure Storage-ból származó naplók gyűjtésének konfigurálásához; Automation-fiókok létrehozása és konfigurálása; megoldások hozzáadása; és az Azure Diagnostics konfigurálása az összes Azure-erőforráshoz. |
> | **Azonosító** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a Analysis Server diagnosztikai beállításait. |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="log-analytics-reader"></a>Log Analytics olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Log Analytics olvasó megtekintheti és megkeresheti az összes figyelési adatait, valamint megtekintheti a figyelési beállításokat, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson. |
> | **Azonosító** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Keresés az új motor használatával. |
> | Microsoft.OperationalInsights/workspaces/search/action | Keresési lekérdezés végrehajtása |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Lekéri a munkaterület megosztott kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="logic-app-contributor"></a>Logikai alkalmazás közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a logikai alkalmazások kezelését, de nem változtatja meg a hozzájuk való hozzáférést. |
> | **Azonosító** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | Microsoft.ClassicStorage/storageAccounts/read | Adja vissza a Storage-fiókot az adott fiókkal. |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a Analysis Server diagnosztikai beállításait. |
> | Microsoft.Insights/logdefinitions/* | Ez az engedély olyan felhasználók számára szükséges, akiknek a portálon keresztül kell hozzáférnie a tevékenység naplóihoz. A műveletnapló kategóriáinak listázása a tevékenység naplójában. |
> | Microsoft.Insights/metricDefinitions/* | A metrikai definíciók (az adott erőforráshoz elérhető metrikai típusok listája) olvasása. |
> | Microsoft.Logic/* | Felügyeli Logic Apps erőforrásait. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek beolvasása. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Storage/storageAccounts/listkeys/action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Microsoft.Storage/storageAccounts/read | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Web/connectionGateways/* | Hozzon létre és kezelje a kapcsolatok átjáróját. |
> | Microsoft.Web/connections/* | Hozzon létre és kezelje a kapcsolatokat. |
> | Microsoft.Web/customApis/* | Egyéni API létrehozása és kezelése. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | App Service csomag tulajdonságainak beolvasása |
> | Microsoft.Web/sites/functions/listSecrets/action | A Secrets Web Apps függvények listázása. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="logic-app-operator"></a>Logic app-operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a logikai alkalmazások olvasását, engedélyezését és letiltását, de nem szerkesztheti és nem frissíti őket. |
> | **Azonosító** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/*/read | Az információk beolvasása riasztási szabályok |
> | Microsoft.Insights/diagnosticSettings/*/read | A Logic Apps diagnosztikai beállításainak beolvasása |
> | Microsoft.Insights/metricDefinitions/*/read | A Logic Apps elérhető metrikáinak beolvasása. |
> | Microsoft.Logic/*/read | Logic Apps erőforrások beolvasása. |
> | Microsoft.Logic/workflows/disable/action | Letiltja a munkafolyamatot. |
> | Microsoft.Logic/workflows/enable/action | Engedélyezi a munkafolyamatot. |
> | Microsoft.Logic/workflows/validate/action | Ellenőrzi a munkafolyamatot. |
> | Microsoft.Resources/deployments/operations/read | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek beolvasása. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Web/connectionGateways/*/read | A kapcsolatok átjáróinak beolvasása. |
> | Microsoft.Web/connections/*/read | Olvasási kapcsolatok. |
> | Microsoft.Web/customApis/*/read | Egyéni API olvasása. |
> | Microsoft.Web/serverFarms/read | App Service csomag tulajdonságainak beolvasása |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="managed-application-operator-role"></a>Felügyelt alkalmazás operátori szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi műveletek olvasását és végrehajtását a felügyelt alkalmazás erőforrásain |
> | **Azonosító** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft.Solutions/applications/read | Az alkalmazások listájának beolvasása. |
> | Microsoft.Solutions/*/action |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="managed-applications-reader"></a>Felügyelt alkalmazások olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az erőforrások olvasását egy felügyelt alkalmazásban, valamint a JIT-hozzáférés kérését. |
> | **Azonosító** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Solutions/jitRequests/* |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="managed-identity-contributor"></a>Felügyelt identitás közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felhasználóhoz rendelt identitás létrehozása, olvasása, frissítése és törlése |
> | **Azonosító** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Műveletek** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Meglévő felhasználóhoz rendelt identitás beolvasása |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Új felhasználóhoz rendelt identitás létrehozása vagy egy meglévő felhasználóhoz rendelt identitáshoz társított címkék frissítése |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Meglévő felhasználó által hozzárendelt identitás törlése |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="managed-identity-operator"></a>Felügyelt identitás operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felhasználóhoz rendelt identitás olvasása és hozzárendelése |
> | **Azonosító** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Műveletek** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="management-group-contributor"></a>Felügyeleti csoport közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felügyeleti csoport közreműködői szerepköre |
> | **Azonosító** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Műveletek** |  |
> | Microsoft.Management/managementGroups/delete | Felügyeleti csoport törlése. |
> | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Az előfizetés társítása a felügyeleti csoportból. |
> | Microsoft. Management/managementGroups/előfizetések/írás | Meglévő előfizetés társítása a felügyeleti csoporttal. |
> | Microsoft. Management/managementGroups/írás | Hozzon létre vagy frissítsen egy felügyeleti csoportot. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="management-group-reader"></a>Felügyeleti csoport olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felügyeleti csoport olvasójának szerepköre |
> | **Azonosító** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Műveletek** |  |
> | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="monitoring-contributor"></a>Közreműködő figyelése
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Beolvashatja az összes figyelési és a figyelési beállításokat. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Azonosító** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft. bepillantások/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Riasztási szabályok olvasása/írása/törlése. |
> | Microsoft.Insights/components/* | Application Insights összetevők olvasása/írása/törlése. |
> | Microsoft.Insights/DiagnosticSettings/* | Diagnosztikai beállítások olvasása/írása/törlése. |
> | Microsoft.Insights/eventtypes/* | Az előfizetésben szereplő tevékenység-naplózási események (kezelési események) listázása. Ez az engedély mind a programozási, mind a portálhoz való hozzáférésre alkalmazható a tevékenység naplójában. |
> | Microsoft.Insights/LogDefinitions/* | Ez az engedély olyan felhasználók számára szükséges, akiknek a portálon keresztül kell hozzáférnie a tevékenység naplóihoz. A műveletnapló kategóriáinak listázása a tevékenység naplójában. |
> | Microsoft. bepillantások/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | A metrikai definíciók (az adott erőforráshoz elérhető metrikai típusok listája) olvasása. |
> | Microsoft.Insights/Metrics/* | Erőforrás metrikáinak olvasása. |
> | Microsoft.Insights/Register/Action | A Microsoft bepillantást nyújtó szolgáltató regisztrálása |
> | Microsoft. bepillantások/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Application Insights webes tesztek olvasása/írása/törlése. |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Log Analytics-megoldási csomagok olvasása/írása/törlése. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | A log Analytics-beli mentett keresések olvasása/írása/törlése. |
> | Microsoft.OperationalInsights/workspaces/search/action | Keresési lekérdezés végrehajtása |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Lekéri a munkaterület megosztott kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | A log Analytics tárolási Insight-konfigurációinak olvasása/írása/törlése. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. WorkloadMonitor/figyelők/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft. AlertsManagement/smartDetectorAlertRules/* |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="monitoring-metrics-publisher"></a>Figyelési metrikák közzétevője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az Azure-erőforrásokhoz való közzétételi mérőszámok közzétételét |
> | **Azonosító** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Műveletek** |  |
> | Microsoft.Insights/Register/Action | A Microsoft bepillantást nyújtó szolgáltató regisztrálása |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Mérőszámok írása |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="monitoring-reader"></a>Figyelő olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az összes figyelési adat (mérőszámok, naplók stb.) olvasása. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Azonosító** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft.OperationalInsights/workspaces/search/action | Keresési lekérdezés végrehajtása |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="network-contributor"></a>Hálózati közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a hálózatok kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.Network/* | Hálózatok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="new-relic-apm-account-contributor"></a>Új ereklye APM-fiók közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi New Relic Application Performance Management-fiókok és-alkalmazások kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | NewRelic.APM/accounts/* |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="reader-and-data-access"></a>Olvasó és adathozzáférés
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az összes megtekintését, de nem engedélyezheti a Storage-fiók vagy a tárolt erőforrás törlését vagy létrehozását. Az olvasási/írási hozzáférést is lehetővé teszi a Storage-fiókban található összes, a Storage-fiók kulcsaihoz való hozzáférés útján. |
> | **Azonosító** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | A megadott Storage-fiókhoz tartozó SAS-tokent adja vissza. |
> | Microsoft.Storage/storageAccounts/read | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="redis-cache-contributor"></a>Redis Cache közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Redis gyorsítótárak kezelését, de azokhoz való hozzáférés nélkül. |
> | **Azonosító** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Cache/redis/* | Redis cache-gyorsítótárak létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="resource-policy-contributor-preview"></a>Erőforrás-házirend közreműködője (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Előnézet Visszatöltötte-felhasználóktól, az erőforrás-házirend létrehozásához/módosításához, támogatási jegy létrehozásához és az erőforrások/hierarchia olvasásához szükséges jogosultságokkal. |
> | **Azonosító** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft. Authorization/policyassignments/* | Szabályzat-hozzárendelések létrehozása és kezelése |
> | Microsoft.Authorization/policydefinitions/* | Szabályzat-definíciók létrehozása és kezelése |
> | Microsoft.Authorization/policysetdefinitions/* | Szabályzat-készletek létrehozása és kezelése |
> | Microsoft. PolicyInsights/* |  |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="scheduler-job-collections-contributor"></a>Ütemező – feladattípusok közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Scheduler-feladatok összegyűjtését, de nem fér hozzájuk. |
> | **Azonosító** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Scheduler/jobcollections/* | Feladatok gyűjteményének létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="search-service-contributor"></a>Search Service közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a keresési szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Search/searchServices/* | Keresési szolgáltatások létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="security-admin"></a>Biztonsági rendszergazda
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Csak Security Centerban: Megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, szerkesztheti a biztonsági szabályzatokat, megtekintheti a riasztásokat és a javaslatokat, |
> | **Azonosító** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Authorization/policyAssignments/* | Szabályzat-hozzárendelések létrehozása és kezelése |
> | Microsoft.Authorization/policyDefinitions/* | Szabályzat-definíciók létrehozása és kezelése |
> | Microsoft.Authorization/policySetDefinitions/* | Szabályzat-készletek létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | Microsoft.operationalInsights/workspaces/*/read | Log Analytics-adatértékek megtekintése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="security-manager-legacy"></a>Security Manager (örökölt)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Ez egy örökölt szerepkör. Használja helyette a biztonsági rendszergazdát |
> | **Azonosító** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ClassicCompute/*/read | Konfigurációs információk beolvasása a klasszikus virtuális gépeken |
> | Microsoft.ClassicCompute/virtualMachines/*/write | A klasszikus virtuális gépek írási konfigurációja |
> | Microsoft.ClassicNetwork/*/read | A klasszikus hálózat konfigurációs információinak olvasása |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Security/* | Biztonsági összetevők és házirendek létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="security-reader"></a>Biztonsági olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Csak Security Centerban: Megtekintheti a javaslatokat és riasztásokat, megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, de nem végezhet módosításokat |
> | **Azonosító** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.operationalInsights/workspaces/*/read | Log Analytics-adatértékek megtekintése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Security/*/read | Biztonsági összetevők és szabályzatok olvasása |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="site-recovery-contributor"></a>Site Recovery közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Site Recovery szolgáltatás felügyeletét, kivéve a tár létrehozását és a szerepkör-hozzárendelést |
> | **Azonosító** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | A AllocateStamp a szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tároló hitelesítő adatait tartalmazó tanúsítványt. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | A tár szolgáltatással kapcsolatos bővített adatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/read | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Regisztrált identitások létrehozása és kezelése |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Replikációs riasztási beállítások létrehozása vagy frissítése |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Események beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Replikációs hálók létrehozása és kezelése |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Replikációs feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Replikációs házirendek létrehozása és kezelése |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Helyreállítási tervek létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Recovery Services-tároló tárolási konfigurációjának létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | A Recovery Services-tárolóra vonatkozó olvasási riasztások |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Storage/storageAccounts/read | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="site-recovery-operator"></a>Site Recovery operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a feladatátvételt és a feladat-visszavételt, de nem hajt végre más Site Recovery felügyeleti műveleteket |
> | **Azonosító** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | A AllocateStamp a szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/read | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Riasztási beállítások beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Események beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Konzisztencia-ellenőrzést futtat a hálón |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Bármilyen háló beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Átjáró újrahozzárendelése |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Háló tanúsítványának megújítása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Bármilyen hálózat beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Hálózati leképezések olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Védelmi tárolók beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Bármely védhető elem olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Helyreállítási pont alkalmazása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Feladatátvétel véglegesítve |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Tervezett feladatátvétel |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Védett elemek beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Replikációs helyreállítási pontok olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Replikáció javítása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Védett elemek ismételt védelme |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Feladatátvételi teszt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Feladatátvételi teszt karbantartása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Feladatátvétel |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mobilitási szolgáltatás frissítése |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | A védelmi tárolók megfeleltetésének beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services szolgáltatók beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Szolgáltató frissítése |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | A tárolási besorolások beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | A tárolási besorolási leképezések beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Bármilyen vCenter beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Replikációs feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Bármilyen szabályzat beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Feladatátvételi véglegesítés helyreállítási terve |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Tervezett feladatátvétel helyreállítási terve |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Helyreállítási tervek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Helyreállítási terv ismételt védetté |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Feladatátvételi teszt helyreállítási terve |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Feladatátvételi teszt karbantartásának helyreállítási terve |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Feladatátvétel helyreállítási terve |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | A Recovery Services-tárolóra vonatkozó olvasási riasztások |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Storage/storageAccounts/read | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="site-recovery-reader"></a>Site Recovery olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy megtekintse Site Recovery állapotát, de ne végezzen más felügyeleti műveleteket |
> | **Azonosító** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a Recovery Services-tároló riasztásait. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Riasztási beállítások beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Események beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Bármilyen háló beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Bármilyen hálózat beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Hálózati leképezések olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Védelmi tárolók beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Bármely védhető elem olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Védett elemek beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Replikációs helyreállítási pontok olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | A védelmi tárolók megfeleltetésének beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services szolgáltatók beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | A tárolási besorolások beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | A tárolási besorolási leképezések beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Bármilyen vCenter beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Bármilyen feladat beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Bármilyen szabályzat beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Helyreállítási tervek olvasása |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="spatial-anchors-account-contributor"></a>Térbeli horgonyok fiók közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a térbeli horgonyok kezelését a fiókban, de nem törli őket |
> | **Azonosító** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Műveletek** |  |
> | *none* |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Térbeli horgonyok létrehozása |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak beolvasása |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Térbeli horgonyok keresése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Térbeli horgonyok tulajdonságainak frissítése |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="spatial-anchors-account-owner"></a>Térbeli horgonyok fiókjának tulajdonosa
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a fiókban lévő térbeli horgonyok kezelését, beleértve azok törlését is |
> | **Azonosító** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Műveletek** |  |
> | *none* |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Térbeli horgonyok létrehozása |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Térbeli horgonyok törlése |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak beolvasása |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Térbeli horgonyok keresése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Térbeli horgonyok tulajdonságainak frissítése |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="spatial-anchors-account-reader"></a>Térbeli horgonyok fiókjának olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a fiókban található térbeli horgonyok tulajdonságainak megkeresését és beolvasását |
> | **Azonosító** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Műveletek** |  |
> | *none* |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak beolvasása |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Térbeli horgonyok keresése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="sql-db-contributor"></a>SQL-adatbázis közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az SQL-adatbázisok kezelését, de azokhoz való hozzáférés nélkül. Emellett nem kezelheti a biztonsággal kapcsolatos házirendeket vagy a szülő SQL-kiszolgálókat. |
> | **Azonosító** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | SQL-adatbázisok létrehozása és kezelése |
> | Microsoft.Sql/servers/read | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. bepillantások/mérőszámok/olvasás | Metrikák olvasása |
> | Microsoft. bepillantások/metricDefinitions/olvasás | Metrikus definíciók olvasása |
> | **Nincs tapintat** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Naplózási házirendek szerkesztése |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Naplózási beállítások szerkesztése |
> | Microsoft.Sql/servers/databases/auditRecords/read | Az adatbázis blob-naplózási rekordjainak beolvasása |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | A kapcsolatok házirendjeinek szerkesztése |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Adatmaszkolási házirendek szerkesztése |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Biztonsági riasztási házirendek szerkesztése |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Biztonsági mérőszámok szerkesztése |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="sql-managed-instance-contributor"></a>SQL felügyelt példány közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az SQL felügyelt példányok és a szükséges hálózati konfiguráció kezelését, de mások számára nem biztosít hozzáférést. |
> | **Azonosító** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **Műveletek** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft. Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Network/virtualNetworks/subnets/* |  |
> | Microsoft. Network/virtualNetworks/* |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. bepillantások/mérőszámok/olvasás | Metrikák olvasása |
> | Microsoft. bepillantások/metricDefinitions/olvasás | Metrikus definíciók olvasása |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="sql-security-manager"></a>SQL Security Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az SQL-kiszolgálók és-adatbázisok biztonsággal kapcsolatos házirendjeinek kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Microsoft-hitelesítés olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL Server naplózási szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/auditingSettings/* | SQL Server naplózási beállításának létrehozása és kezelése |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Az adott kiszolgálón konfigurált kiterjesztett kiszolgálói blob-naplózási szabályzat részleteinek beolvasása |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL Server-adatbázis naplózási házirendjeinek létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/auditingSettings/* | SQL Server-adatbázis naplózási beállításainak létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/auditRecords/read | Naplózási rekordok olvasása |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | SQL Server adatbázis-kapcsolatok házirendjeinek létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Az SQL Server-adatbázis adatmaszkolási házirendjeinek létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Az adott adatbázisban konfigurált kiterjesztett blob-naplózási szabályzat részleteinek beolvasása |
> | Microsoft.Sql/servers/databases/read | Az adatbázisok listájának visszaadása vagy a megadott adatbázis tulajdonságainak beolvasása. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Adatbázis-séma beszerzése. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Adatbázis oszlopának beolvasása. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Adatbázis táblázatának beolvasása. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL Server-adatbázis biztonsági riasztási házirendjeinek létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/securityMetrics/* | SQL Server-adatbázis biztonsági metrikáinak létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | SQL Server biztonsági riasztási szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="sql-server-contributor"></a>SQL Server közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az SQL-kiszolgálók és-adatbázisok kezelését, de azokhoz való hozzáférés nélkül, és nem a biztonsággal kapcsolatos házirendjeiket. |
> | **Azonosító** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | SQL-kiszolgálók létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. bepillantások/mérőszámok/olvasás | Metrikák olvasása |
> | Microsoft. bepillantások/metricDefinitions/olvasás | Metrikus definíciók olvasása |
> | **Nincs tapintat** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL Server naplózási szabályzatok szerkesztése |
> | Microsoft.Sql/servers/auditingSettings/* | Az SQL Server naplózási beállításainak szerkesztése |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL Server-adatbázis naplózási házirendjeinek szerkesztése |
> | Microsoft.Sql/servers/databases/auditingSettings/* | SQL Server-adatbázis naplózási beállításainak szerkesztése |
> | Microsoft.Sql/servers/databases/auditRecords/read | Naplózási rekordok olvasása |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | SQL Server adatbázis-kapcsolatok házirendjeinek szerkesztése |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Az SQL Server-adatbázis adatmaszkolási házirendjeinek szerkesztése |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL Server-adatbázis biztonsági riasztási házirendjeinek szerkesztése |
> | Microsoft.Sql/servers/databases/securityMetrics/* | SQL Server-adatbázis biztonsági metrikáinak szerkesztése |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | SQL Server biztonsági riasztási szabályzatok szerkesztése |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-account-contributor"></a>Storage-fiók közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyezi a Storage-fiókok kezelését. Hozzáférést biztosít a fiók kulcsaként, amely a megosztott kulcsos hitelesítésen keresztüli hozzáféréshez használható. |
> | **Azonosító** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Az összes engedély olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Diagnosztikai beállítások kezelése |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Storage/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-account-key-operator-service-role"></a>A Storage-fiók kulcs-kezelő szolgáltatásának szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyezi a Storage-fiók hozzáférési kulcsainak listázását és újragenerálását. |
> | **Azonosító** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Újragenerálja a megadott Storage-fiók hozzáférési kulcsait. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-blob-data-contributor"></a>Storage blob adatközreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Storage-tárolók és-Blobok olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Tároló törlése. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Tároló vagy tárolók listájának visszaadása. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Egy tároló metaadatainak vagy tulajdonságainak módosítása. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | BLOB törlése. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | BLOB vagy Blobok listájának visszaadása. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Írás blobba. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-blob-data-owner"></a>Storage blob-adattulajdonos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Teljes hozzáférést biztosít az Azure Storage blob-tárolók és-adatkészletekhez, beleértve a POSIX hozzáférés-vezérlés hozzárendelését. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | A tárolók teljes engedélyei. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | A Blobok teljes engedélyei. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-blob-data-reader"></a>Storage blob-Adatolvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Storage-tárolók és-Blobok olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Tároló vagy tárolók listájának visszaadása. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | BLOB vagy Blobok listájának visszaadása. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-queue-data-contributor"></a>Tárolási várólista adatközreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Storage-várólisták és üzenetsor-üzenetek olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Várólista törlése. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Várólista vagy várólisták listájának visszaadása. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Várólista metaadatainak vagy tulajdonságainak módosítása |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Egy vagy több üzenet törlése egy várólistából. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Egy vagy több üzenet betekintése vagy lekérése egy várólistából. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Üzenet hozzáadása egy várólistához. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-queue-data-message-processor"></a>Tárolási üzenetsor adatüzenet-processzora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Üzenet betekintése, beolvasása és törlése egy Azure Storage-várólistából. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Műveletek** |  |
> | *none* |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Üzenet bepillantása. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Üzenet beolvasása és törlése. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-queue-data-message-sender"></a>Tárolási várólista adatüzenetének küldője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Üzenetek hozzáadása egy Azure Storage-várólistához. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Műveletek** |  |
> | *none* |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Üzenet hozzáadása egy várólistához. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="storage-queue-data-reader"></a>Storage-várólista adatolvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Storage-várólisták és üzenetsor-üzenetek olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Várólistát vagy várólisták listáját adja vissza. |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Egy vagy több üzenet betekintése vagy lekérése egy várólistából. |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="support-request-contributor"></a>Támogatási kérelem közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a támogatási kérések létrehozását és kezelését |
> | **Azonosító** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="traffic-manager-contributor"></a>Traffic Manager közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz való hozzáférés szabályozását. |
> | **Azonosító** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="user-access-administrator"></a>Felhasználói hozzáférés rendszergazdája
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az Azure-erőforrásokhoz való felhasználói hozzáférés kezelését. |
> | **Azonosító** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft.Authorization/* | Engedélyezés kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="virtual-machine-administrator-login"></a>Virtuális gép rendszergazdai bejelentkezése
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Virtual Machines megtekintése a Portálon és Bejelentkezés rendszergazdaként |
> | **Azonosító** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Műveletek** |  |
> | Microsoft.Network/publicIPAddresses/read | Egy nyilvános IP-cím definíciójának beolvasása. |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.Network/loadBalancers/read | Terheléselosztó definíciójának beolvasása |
> | Microsoft.Network/networkInterfaces/read | Hálózati adapter definíciójának beolvasása.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Jelentkezzen be egy virtuális gépre a Windows rendszergazdai vagy a Linux root felhasználói jogosultságokkal |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="virtual-machine-contributor"></a>Virtuális gépek közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a virtuális gépek kezelését, de nem fér hozzájuk, nem pedig a virtuális hálózati vagy a Storage-fiókhoz, amelyhez csatlakoznak. |
> | **Azonosító** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft.Compute/availabilitySets/* | Számítási rendelkezésre állási csoportok létrehozása és kezelése |
> | Microsoft.Compute/locations/* | Számítási helyszínek létrehozása és kezelése |
> | Microsoft.Compute/virtualMachines/* | Virtuális gépek létrehozása és kezelése |
> | Microsoft.Compute/virtualMachineScaleSets/* | Virtuálisgép-méretezési csoportok létrehozása és kezelése |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Egy Application Gateway háttérbeli címkészlet illesztése. Nem Riasztásos. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | A terheléselosztó háttérbeli címkészlet csatlakoztatása. Nem Riasztásos. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | A terheléselosztó bejövő NAT-készletéhez csatlakozik. Nem riasztásos. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | A terheléselosztó bejövő NAT-szabályához csatlakozik. Nem Riasztásos. |
> | Microsoft.Network/loadBalancers/probes/join/action | Lehetővé teszi a terheléselosztó mintavételi funkcióinak használatát. A virtuálisgép-méretezési csoport ezen engedély healthProbe tulajdonsága például hivatkozhat a mintavételre. Nem riasztásos. |
> | Microsoft.Network/loadBalancers/read | Terheléselosztó definíciójának beolvasása |
> | Microsoft.Network/locations/* | Hálózati telephelyek létrehozása és kezelése |
> | Microsoft.Network/networkInterfaces/* | Hálózati adapterek létrehozása és kezelése |
> | Microsoft.Network/networkSecurityGroups/join/action | Csatlakozik egy hálózati biztonsági csoporthoz. Nem Riasztásos. |
> | Microsoft.Network/networkSecurityGroups/read | Hálózati biztonsági csoport definíciójának beolvasása |
> | Microsoft.Network/publicIPAddresses/join/action | Csatlakozik egy nyilvános IP-címhez. Nem Riasztásos. |
> | Microsoft.Network/publicIPAddresses/read | Egy nyilvános IP-cím definíciójának beolvasása. |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Biztonsági másolat védelmi leképezésének létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett tétel objektumának részleteit adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Védett elemek biztonsági másolatának létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Az összes védelmi szabályzat visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Védelmi szabályzat létrehozása |
> | Microsoft.RecoveryServices/Vaults/read | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/write | A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Microsoft.Storage/storageAccounts/read | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="virtual-machine-user-login"></a>Virtuális gép felhasználói bejelentkezés
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Tekintse meg Virtual Machines a portálon, és jelentkezzen be normál felhasználóként. |
> | **Azonosító** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Műveletek** |  |
> | Microsoft.Network/publicIPAddresses/read | Egy nyilvános IP-cím definíciójának beolvasása. |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.Network/loadBalancers/read | Terheléselosztó definíciójának beolvasása |
> | Microsoft.Network/networkInterfaces/read | Hálózati adapter definíciójának beolvasása.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="web-plan-contributor"></a>Webes csomag közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a webhelyek webes csomagjainak kezelését, de azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Web/serverFarms/* | Kiszolgálófarm létrehozása és kezelése |
> | Microsoft. Web/hostingEnvironments/csatlakozás/művelet | Egy App Service Environment illesztése |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="website-contributor"></a>Webhely közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a webhelyek (nem webes csomagok) kezelését, de nem fér hozzájuk. |
> | **Azonosító** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Insights/components/* | Az adatáttekintési összetevők létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Web/certificates/* | Webhely-tanúsítványok létrehozása és kezelése |
> | Microsoft.Web/listSitesAssignedToHostName/read | Az állomásnévhez rendelt helyek nevének beolvasása. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | App Service csomag tulajdonságainak beolvasása |
> | Microsoft.Web/sites/* | Webhelyek létrehozása és kezelése (a hely létrehozásához írási engedély szükséges a társított App Service csomaghoz) |
> | **Nincs tapintat** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## <a name="next-steps"></a>További lépések

- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure Portal használatával](role-assignments-portal.md)
- [Engedélyek az Azure Security Centerben](../security-center/security-center-permissions.md)
